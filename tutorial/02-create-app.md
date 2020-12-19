<!-- markdownlint-disable MD002 MD041 -->

Microsoft Teams タブ アプリケーションには、ユーザーを認証して Microsoft Graph を呼び出す複数のオプションがあります。 この演習では、シングル サインオンを実行してクライアントで[](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso)認証トークンを取得するタブを実装し、サーバー上の代理フローを使用[](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)して、そのトークンを交換して Microsoft Graph にアクセスします。

その他の方法については、以下を参照してください。

- [Microsoft Graph を使用して Microsoft Teams タブをToolkit。](/graph/toolkit/get-started/build-a-microsoft-teams-tab) このサンプルは完全にクライアント側であり、Microsoft Graph Toolkitを使用して、認証を処理し、Microsoft Graph を呼び出します。
- [Microsoft Teams 認証のサンプル](https://github.com/OfficeDev/microsoft-teams-sample-auth-node)。 このサンプルには、さまざまな認証シナリオをカバーする複数の例が含まれています。

## <a name="create-the-project"></a>プロジェクトを作成する

まず、新しい Core ASP.NET作成します。

1. プロジェクトを作成するディレクトリでコマンド ライン インターフェイス (CLI) を開きます。 次のコマンドを実行します。

    ```Shell
    dotnet new webapp -o GraphTutorial
    ```

1. プロジェクトが作成された後、現在のディレクトリを **GraphTu clil** ディレクトリに変更し、CLI で次のコマンドを実行して、プロジェクトが動作を確認します。

    ```Shell
    dotnet run
    ```

1. ブラウザーを開き、参照します `https://localhost:5001` 。 すべてが動作している場合は、既定の [コア] ページASP.NET表示されます。

> [!IMPORTANT]
> **localhost** の証明書が信頼されていないという警告を受け取った場合は、.NET Core CLI を使用して開発証明書をインストールして信頼できます。 特定 [のオペレーティング システムの手順については、「ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) での HTTPS の適用」を参照してください。

## <a name="add-nuget-packages"></a>NuGet パッケージを追加する

次に進む前に、後で使用する追加の NuGet パッケージをインストールします。

- アクセス トークンを認証および要求する[Microsoft.Identity.Web。](https://www.nuget.org/packages/Microsoft.Identity.Web/)
- [Microsoft.Identity.Web.Web で](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) 構成された Microsoft Graph サポートを追加する Microsoft.Identity.Web.MicrosoftGraph。
- [Microsoft.Graph を](https://www.nuget.org/packages/Microsoft.Graph/) 使用して、Microsoft.Identity.Web.MicrosoftGraph によってインストールされたこのパッケージのバージョンを更新します。
- [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) は、Microsoft Graph SDK との互換性のために Newtonsoft JSON コンバーターを有効にしています。
- Windows タイム ゾーン識別子を IANA 識別子に変換する[TimeZoneConverter。](https://github.com/mj1856/TimeZoneConverter)

1. CLI で次のコマンドを実行して、依存関係をインストールします。

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.Web.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.16.0
    dotnet add package Microsoft.AspNetCore.Mvc.NewtonsoftJson
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、アプリケーションの基本的な UI 構造を作成します。

> [!TIP]
> このチュートリアルでは、任意のテキスト エディターを使用してソース ファイルを編集できます。 ただし [、Visual Studioコードには](https://code.visualstudio.com/) 、デバッグや Intellisense などの追加機能があります。

1. **./Pages/Shared/_Layout.cshtml** を開き、コンテンツ全体を次のコードに置き換え、アプリのグローバル レイアウトを更新します。

    :::code language="cshtml" source="../demo/GraphTutorial/Pages/Shared/_Layout.cshtml" id="LayoutSnippet":::

    これにより、Bootstrap が [Fluent UI](https://developer.microsoft.com/fluentui)に置き換わるので [、Microsoft Teams SDK](/javascript/api/overview/msteams-client)が追加され、レイアウトが簡略化されます。

1. **./wwwroot/js/site.js** 開き、次のコードを追加します。

    :::code language="javascript" source="../demo/GraphTutorial/wwwroot/js/site.js" id="ThemeSupportSnippet":::

    これにより、濃色テーマとハイ コントラスト テーマの既定のテキストの色を変更する単純なテーマ変更ハンドラーが追加されます。

1. **./wwwroot/css/site.css を** 開き、その内容を次の内容に置き換えてください。

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. **./Pages/Index.cshtml** を開き、その内容を次のコードに置き換えます。

    ```cshtml
    @page
    @model IndexModel
    @{
      ViewData["Title"] = "Home page";
    }

    <div id="tab-container">
      <h1 class="ms-fontSize-24 ms-fontWeight-semibold">Loading...</h1>
    </div>

    @section Scripts
    {
      <script>
      </script>
    }
    ```

1. **./Startup.cs** 開き、メソッド `app.UseHttpsRedirection();` の行を削除 `Configure` します。 これは、ngrok トンネリングが機能するために必要です。

## <a name="run-ngrok"></a>ngrok を実行する

Microsoft Teams では、アプリのローカル ホスティングはサポートされていません。 アプリをホストするサーバーは、HTTPS エンドポイントを使用してクラウドから利用できる必要があります。 ローカルでデバッグを行う場合は、ngrok を使用して、ローカルでホストされるプロジェクトのパブリック URL を作成できます。

1. CLI を開き、次のコマンドを実行して ngrok を起動します。

    ```Shell
    ngrok http 5000
    ```

1. ngrok が起動したら、HTTPS 転送 URL をコピーします。 次のように表示されます `https://50153897dd4d.ngrok.io` 。 この値は、後の手順で必要になります。

> [!IMPORTANT]
> ngrok の無料バージョンを使用している場合は、ngrok を再起動するごとに転送 URL が変更されます。 同じ URL を保持するには、このチュートリアルを完了するまで ngrok を実行しておいておくのをお勧めします。 ngrok を再起動する必要がある場合は、URL が使用されているすべての場所で URL を更新し、Microsoft Teams にアプリを再インストールする必要があります。
