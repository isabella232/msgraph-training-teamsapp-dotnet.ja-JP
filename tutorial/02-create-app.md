<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="44eef-101">Microsoft Teams タブ アプリケーションには、ユーザーを認証して Microsoft Graph を呼び出す複数のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="44eef-101">Microsoft Teams tab applications have multiple options to authenticate the user and call Microsoft Graph.</span></span> <span data-ttu-id="44eef-102">この演習では、シングル サインオンを実行してクライアントで[](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso)認証トークンを取得するタブを実装し、サーバー上の代理フローを使用[](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)して、そのトークンを交換して Microsoft Graph にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="44eef-102">In this exercise, you'll implement a tab that does [single sign-on](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso) to get an auth token on the client, then uses the [on-behalf-of flow](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) on the server to exchange that token to get access to Microsoft Graph.</span></span>

<span data-ttu-id="44eef-103">その他の方法については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="44eef-103">For other alternatives, see the following.</span></span>

- <span data-ttu-id="44eef-104">[Microsoft Graph を使用して Microsoft Teams タブをToolkit。](/graph/toolkit/get-started/build-a-microsoft-teams-tab)</span><span class="sxs-lookup"><span data-stu-id="44eef-104">[Build a Microsoft Teams tab with the Microsoft Graph Toolkit](/graph/toolkit/get-started/build-a-microsoft-teams-tab).</span></span> <span data-ttu-id="44eef-105">このサンプルは完全にクライアント側であり、Microsoft Graph Toolkitを使用して、認証を処理し、Microsoft Graph を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="44eef-105">This sample is completely client-side, and uses the Microsoft Graph Toolkit to handle authentication and making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="44eef-106">[Microsoft Teams 認証のサンプル](https://github.com/OfficeDev/microsoft-teams-sample-auth-node)。</span><span class="sxs-lookup"><span data-stu-id="44eef-106">[Microsoft Teams Authentication Sample](https://github.com/OfficeDev/microsoft-teams-sample-auth-node).</span></span> <span data-ttu-id="44eef-107">このサンプルには、さまざまな認証シナリオをカバーする複数の例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="44eef-107">This sample contains multiple examples covering different authentication scenarios.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="44eef-108">プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="44eef-108">Create the project</span></span>

<span data-ttu-id="44eef-109">まず、新しい Core ASP.NET作成します。</span><span class="sxs-lookup"><span data-stu-id="44eef-109">Start by creating an ASP.NET Core web app.</span></span>

1. <span data-ttu-id="44eef-110">プロジェクトを作成するディレクトリでコマンド ライン インターフェイス (CLI) を開きます。</span><span class="sxs-lookup"><span data-stu-id="44eef-110">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="44eef-111">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="44eef-111">Run the following command.</span></span>

    ```Shell
    dotnet new webapp -o GraphTutorial
    ```

1. <span data-ttu-id="44eef-112">プロジェクトが作成された後、現在のディレクトリを **GraphTu clil** ディレクトリに変更し、CLI で次のコマンドを実行して、プロジェクトが動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="44eef-112">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

1. <span data-ttu-id="44eef-113">ブラウザーを開き、参照します `https://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="44eef-113">Open your browser and browse to `https://localhost:5001`.</span></span> <span data-ttu-id="44eef-114">すべてが動作している場合は、既定の [コア] ページASP.NET表示されます。</span><span class="sxs-lookup"><span data-stu-id="44eef-114">If everything is working, you should see a default ASP.NET Core page.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="44eef-115">**localhost** の証明書が信頼されていないという警告を受け取った場合は、.NET Core CLI を使用して開発証明書をインストールして信頼できます。</span><span class="sxs-lookup"><span data-stu-id="44eef-115">If you receive a warning that the certificate for **localhost** is un-trusted you can use the .NET Core CLI to install and trust the development certificate.</span></span> <span data-ttu-id="44eef-116">特定 [のオペレーティング システムの手順については、「ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) での HTTPS の適用」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="44eef-116">See [Enforce HTTPS in ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) for instructions for specific operating systems.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="44eef-117">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="44eef-117">Add NuGet packages</span></span>

<span data-ttu-id="44eef-118">次に進む前に、後で使用する追加の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="44eef-118">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="44eef-119">アクセス トークンを認証および要求する[Microsoft.Identity.Web。](https://www.nuget.org/packages/Microsoft.Identity.Web/)</span><span class="sxs-lookup"><span data-stu-id="44eef-119">[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) for authenticating and requesting access tokens.</span></span>
- <span data-ttu-id="44eef-120">[Microsoft.Identity.Web.Web で](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) 構成された Microsoft Graph サポートを追加する Microsoft.Identity.Web.MicrosoftGraph。</span><span class="sxs-lookup"><span data-stu-id="44eef-120">[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) for adding Microsoft Graph support configured with Microsoft.Identity.Web.</span></span>
- <span data-ttu-id="44eef-121">[Microsoft.Graph を](https://www.nuget.org/packages/Microsoft.Graph/) 使用して、Microsoft.Identity.Web.MicrosoftGraph によってインストールされたこのパッケージのバージョンを更新します。</span><span class="sxs-lookup"><span data-stu-id="44eef-121">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) to update the version of this package installed by Microsoft.Identity.Web.MicrosoftGraph.</span></span>
- <span data-ttu-id="44eef-122">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) は、Microsoft Graph SDK との互換性のために Newtonsoft JSON コンバーターを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="44eef-122">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) to enable Newtonsoft JSON converters for compatibility with the Microsoft Graph SDK.</span></span>
- <span data-ttu-id="44eef-123">Windows タイム ゾーン識別子を IANA 識別子に変換する[TimeZoneConverter。](https://github.com/mj1856/TimeZoneConverter)</span><span class="sxs-lookup"><span data-stu-id="44eef-123">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for translating Windows time zone identifiers to IANA identifiers.</span></span>

1. <span data-ttu-id="44eef-124">CLI で次のコマンドを実行して、依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="44eef-124">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.Web.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.16.0
    dotnet add package Microsoft.AspNetCore.Mvc.NewtonsoftJson
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a><span data-ttu-id="44eef-125">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="44eef-125">Design the app</span></span>

<span data-ttu-id="44eef-126">このセクションでは、アプリケーションの基本的な UI 構造を作成します。</span><span class="sxs-lookup"><span data-stu-id="44eef-126">In this section you will create the basic UI structure of the application.</span></span>

> [!TIP]
> <span data-ttu-id="44eef-127">このチュートリアルでは、任意のテキスト エディターを使用してソース ファイルを編集できます。</span><span class="sxs-lookup"><span data-stu-id="44eef-127">You can use any text editor to edit the source files for this tutorial.</span></span> <span data-ttu-id="44eef-128">ただし [、Visual Studioコードには](https://code.visualstudio.com/) 、デバッグや Intellisense などの追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="44eef-128">However, [Visual Studio Code](https://code.visualstudio.com/) provides additional features, such as debugging and Intellisense.</span></span>

1. <span data-ttu-id="44eef-129">**./Pages/Shared/_Layout.cshtml** を開き、コンテンツ全体を次のコードに置き換え、アプリのグローバル レイアウトを更新します。</span><span class="sxs-lookup"><span data-stu-id="44eef-129">Open **./Pages/Shared/_Layout.cshtml** and replace its entire contents with the following code to update the global layout of the app.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Pages/Shared/_Layout.cshtml" id="LayoutSnippet":::

    <span data-ttu-id="44eef-130">これにより、Bootstrap が [Fluent UI](https://developer.microsoft.com/fluentui)に置き換わるので [、Microsoft Teams SDK](/javascript/api/overview/msteams-client)が追加され、レイアウトが簡略化されます。</span><span class="sxs-lookup"><span data-stu-id="44eef-130">This replaces Bootstrap with [Fluent UI](https://developer.microsoft.com/fluentui), adds the [Microsoft Teams SDK](/javascript/api/overview/msteams-client), and simplifies the layout.</span></span>

1. <span data-ttu-id="44eef-131">**./wwwroot/js/site.js** 開き、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="44eef-131">Open **./wwwroot/js/site.js** and add the following code.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/wwwroot/js/site.js" id="ThemeSupportSnippet":::

    <span data-ttu-id="44eef-132">これにより、濃色テーマとハイ コントラスト テーマの既定のテキストの色を変更する単純なテーマ変更ハンドラーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="44eef-132">This adds a simple theme change handler to change the default text color for dark and high contrast themes.</span></span>

1. <span data-ttu-id="44eef-133">**./wwwroot/css/site.css を** 開き、その内容を次の内容に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="44eef-133">Open **./wwwroot/css/site.css** and replace its contents with the following.</span></span>

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. <span data-ttu-id="44eef-134">**./Pages/Index.cshtml** を開き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="44eef-134">Open **./Pages/Index.cshtml** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="44eef-135">**./Startup.cs** 開き、メソッド `app.UseHttpsRedirection();` の行を削除 `Configure` します。</span><span class="sxs-lookup"><span data-stu-id="44eef-135">Open **./Startup.cs** and remove the `app.UseHttpsRedirection();` line in the `Configure` method.</span></span> <span data-ttu-id="44eef-136">これは、ngrok トンネリングが機能するために必要です。</span><span class="sxs-lookup"><span data-stu-id="44eef-136">This is necessary for ngrok tunneling to work.</span></span>

## <a name="run-ngrok"></a><span data-ttu-id="44eef-137">ngrok を実行する</span><span class="sxs-lookup"><span data-stu-id="44eef-137">Run ngrok</span></span>

<span data-ttu-id="44eef-138">Microsoft Teams では、アプリのローカル ホスティングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="44eef-138">Microsoft Teams does not support local hosting for apps.</span></span> <span data-ttu-id="44eef-139">アプリをホストするサーバーは、HTTPS エンドポイントを使用してクラウドから利用できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="44eef-139">The server hosting your app must be available from the cloud using HTTPS endpoints.</span></span> <span data-ttu-id="44eef-140">ローカルでデバッグを行う場合は、ngrok を使用して、ローカルでホストされるプロジェクトのパブリック URL を作成できます。</span><span class="sxs-lookup"><span data-stu-id="44eef-140">For debugging locally, you can use ngrok to create a public URL for your locally-hosted project.</span></span>

1. <span data-ttu-id="44eef-141">CLI を開き、次のコマンドを実行して ngrok を起動します。</span><span class="sxs-lookup"><span data-stu-id="44eef-141">Open your CLI and run the following command to start ngrok.</span></span>

    ```Shell
    ngrok http 5000
    ```

1. <span data-ttu-id="44eef-142">ngrok が起動したら、HTTPS 転送 URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="44eef-142">Once ngrok starts, copy the HTTPS Forwarding URL.</span></span> <span data-ttu-id="44eef-143">次のように表示されます `https://50153897dd4d.ngrok.io` 。</span><span class="sxs-lookup"><span data-stu-id="44eef-143">It should look like `https://50153897dd4d.ngrok.io`.</span></span> <span data-ttu-id="44eef-144">この値は、後の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="44eef-144">You'll need this value in later steps.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="44eef-145">ngrok の無料バージョンを使用している場合は、ngrok を再起動するごとに転送 URL が変更されます。</span><span class="sxs-lookup"><span data-stu-id="44eef-145">If you are using the free version of ngrok, the forwarding URL changes every time you restart ngrok.</span></span> <span data-ttu-id="44eef-146">同じ URL を保持するには、このチュートリアルを完了するまで ngrok を実行しておいておくのをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="44eef-146">It's recommended that you leave ngrok running until you complete this tutorial to keep the same URL.</span></span> <span data-ttu-id="44eef-147">ngrok を再起動する必要がある場合は、URL が使用されているすべての場所で URL を更新し、Microsoft Teams にアプリを再インストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="44eef-147">If you have to restart ngrok, you'll need to update your URL everywhere that it is used and reinstall the app in Microsoft Teams.</span></span>
