<!-- markdownlint-disable MD002 MD041 -->

この演習では、前の演習のアプリケーションを拡張して、Azure AD でのシングル サインオン認証をサポートします。 これは、Microsoft Graph API を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。 この手順では [、Microsoft.Identity.Web ライブラリを構成](https://www.nuget.org/packages/Microsoft.Identity.Web/) します。

> [!IMPORTANT]
> アプリケーション ID とシークレットをソースに格納しないようにするには、.NET [Secret Manager](/aspnet/core/security/app-secrets) を使用してこれらの値を格納します。 シークレット マネージャーは開発のみを目的とします。実稼働アプリでは、シークレットの保存に信頼できるシークレット マネージャーを使用する必要があります。

1. **./appsettings.jsを開き**、その内容を次の内容に置き換えてください。

    :::code language="json" source="../demo/GraphTutorial/appsettings.example.json" highlight="2-8":::

1. **GraphTu clil.csproj** があるディレクトリで CLI を開き、Azure portal のアプリケーション ID とアプリケーション シークレットに置き換え、次のコマンドを実行します。 `YOUR_APP_ID` `YOUR_APP_SECRET`

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a>サインインの実装

まず、アプリの JavaScript コードにシングル サインオンを実装します。 Microsoft Teams [JavaScript SDK](/javascript/api/overview/msteams-client) を使用してアクセス トークンを取得します。これにより、Teams クライアントで実行されている JavaScript コードは、後で実装する Web API への AJAX 呼び出しを行えます。

1. **./Pages/Index.cshtml** を開き、タグ内に次のコードを追加 `<script>` します。

    ```javascript
    (function () {
      if (microsoftTeams) {
        microsoftTeams.initialize();

        microsoftTeams.authentication.getAuthToken({
          successCallback: (token) => {
            // TEMPORARY: Display the access token for debugging
            $('#tab-container').empty();

            $('<code/>', {
              text: token,
              style: 'word-break: break-all;'
            }).appendTo('#tab-container');
          },
          failureCallback: (error) => {
            renderError(error);
          }
        });
      }
    })();

    function renderError(error) {
      $('#tab-container').empty();

      $('<h1/>', {
        text: 'Error'
      }).appendTo('#tab-container');

      $('<code/>', {
        text: JSON.stringify(error, Object.getOwnPropertyNames(error)),
        style: 'word-break: break-all;'
      }).appendTo('#tab-container');
    }
    ```

    これにより `microsoftTeams.authentication.getAuthToken` 、Teams にサインインしているユーザーとしてサイレント認証を呼び出します。 ユーザーが同意する必要がない限り、通常は UI プロンプトは表示されません。 その後、コードはトークンをタブに表示します。

1. 変更を保存し、CLI で次のコマンドを実行してアプリケーションを起動します。

    ```Shell
    dotnet run
    ```

    > [!IMPORTANT]
    > ngrok を再起動し、ngrok URL が変更されている場合は、テストする前に、次の場所で ngrok 値 **を更新してください** 。
    >
    > - アプリ登録のリダイレクト URI
    > - アプリ登録のアプリケーション ID URI
    > - `contentUrl` in manifest.json
    > - `validDomains` in manifest.json
    > - `resource` in manifest.json

1. ZIP ファイルを作成し、manifest.js **をオン、color.png、outline.png。** ****

1. Microsoft Teams で、左側のバーで [**アプリ**]を選択し、[カスタム アプリのアップロード] を選択し、[自分または自分のチームにアップロード **] を選択します**。

    ![Microsoft Teams の [カスタム アプリのアップロード] リンクのスクリーンショット](images/upload-custom-app.png)

1. 前に作成した ZIP ファイルを参照し、[開く] を選択 **します**。

1. アプリケーション情報を確認し、[追加] を **選択します**。

1. アプリケーションが Teams で開き、アクセス トークンが表示されます。

トークンをコピーする場合は、トークンを別の[jwt.ms。](https://jwt.ms) 対象ユーザー (クレーム) がアプリケーション ID であり、作成した API スコープ `aud` (クレーム) が唯一のスコープです `scp` `access_as_user` 。 つまり、このトークンは Microsoft Graph への直接アクセスを許可しないという意味です。 代わりに、間もなく実装する Web API は、Microsoft Graph[](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)呼び出しで動作するトークンを取得するために、代理フローを使用してこのトークンを交換する必要があります。

## <a name="configure-authentication-in-the-aspnet-core-app"></a>ASP.NET Core アプリで認証を構成する

まず、Microsoft Identity プラットフォーム サービスをアプリケーションに追加します。

1. **./Startup.cs** ファイルを開き、ファイルの一番上に次 `using` のステートメントを追加します。

    ```csharp
    using Microsoft.Identity.Web;
    ```

1. 関数の行の直前に `app.UseAuthorization();` 次の行を追加 `Configure` します。

    ```csharp
    app.UseAuthentication();
    ```

1. 関数の行の直後に `endpoints.MapRazorPages();` 次の行を追加 `Configure` します。

    ```csharp
    endpoints.MapControllers();
    ```

1. 既存の `ConfigureServices` 関数を、以下の関数で置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="ConfigureServicesSnippet":::

    このコードは、ヘッダー内の JWT ベアラー トークンに基づいて Web API への呼び出しを認証できるアプリケーションを構成 `Authorization` します。 また、代理フローを介してそのトークンを交換できるトークン取得サービスも追加します。

## <a name="create-the-web-api-controller"></a>Web API コントローラーを作成する

1. Controllers という名前のプロジェクトのルートに新しいディレクトリを **作成します**。

1. **./Controllers** ディレクトリに新しいファイルを作成し、CalendarController.cs **コードを** 追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using Microsoft.Identity.Web.Resource;
    using Microsoft.Graph;
    using TimeZoneConverter;

    namespace GraphTutorial.Controllers
    {
        [ApiController]
        [Route("[controller]")]
        [Authorize]
        public class CalendarController : ControllerBase
        {
            private static readonly string[] apiScopes = new[] { "access_as_user" };

            private readonly GraphServiceClient _graphClient;
            private readonly ITokenAcquisition _tokenAcquisition;
            private readonly ILogger<CalendarController> _logger;

            public CalendarController(ITokenAcquisition tokenAcquisition, GraphServiceClient graphClient, ILogger<CalendarController> logger)
            {
                _tokenAcquisition = tokenAcquisition;
                _graphClient = graphClient;
                _logger = logger;
            }

            [HttpGet]
            public async Task<string> Get()
            {
                // This verifies that the access_as_user scope is
                // present in the bearer token, throws if not
                HttpContext.VerifyUserHasAnyAcceptedScope(apiScopes);

                // To verify that the identity libraries have authenticated
                // based on the token, log the user's name
                _logger.LogInformation($"Authenticated user: {User.GetDisplayName()}");

                try
                {
                    // TEMPORARY
                    // Get a Graph token via OBO flow
                    var token = await _tokenAcquisition
                        .GetAccessTokenForUserAsync(new[]{
                            "User.Read",
                            "MailboxSettings.Read",
                            "Calendars.ReadWrite" });

                    // Log the token
                    _logger.LogInformation($"Access token for Graph: {token}");
                    return "{ \"status\": \"OK\" }";
                }
                catch (MicrosoftIdentityWebChallengeUserException ex)
                {
                    _logger.LogError(ex, "Consent required");
                    // This exception indicates consent is required.
                    // Return a 403 with "consent_required" in the body
                    // to signal to the tab it needs to prompt for consent
                    HttpContext.Response.ContentType = "text/plain";
                    HttpContext.Response.StatusCode = (int)HttpStatusCode.Forbidden;
                    await HttpContext.Response.WriteAsync("consent_required");
                    return null;
                }
                catch (Exception ex)
                {
                    _logger.LogError(ex, "Error occurred");
                    return null;
                }
            }
        }
    }
    ```

    これにより、Teams タブから呼び出し可能な Web API ( `GET /calendar` ) が実装されます。今のところは、Graph トークンのベアラー トークンの交換を試みるのです。 ユーザーが初めてタブを読み込む場合、アプリが自分の代わりに Microsoft Graph へのアクセスを許可することはまだ同意していないので、これは失敗します。

1. **./Pages/Index.cshtml** を開き、関数を `successCallback` 次の関数に置き換える。

    ```javascript
    successCallback: (token) => {
      // TEMPORARY: Call the Web API
      fetch('/calendar', {
        headers: {
          'Authorization': `Bearer ${token}`
        }
      }).then(response => {
        response.text()
          .then(body => {
            $('#tab-container').empty();
            $('<code/>', {
              text: body
            }).appendTo('#tab-container');
          });
      }).catch(error => {
        console.error(error);
        renderError(error);
      });
    }
    ```

    これにより、Web API が呼び出され、応答が表示されます。

1. 変更内容を保存し、アプリを再起動します。 Microsoft Teams のタブを更新します。 ページが表示されます `consent_required` 。

1. CLI でログ出力を確認します。 2 つの点に注意してください。

    - 次のようなエントリ `Authenticated user: MeganB@contoso.com` です。 Web API は、API 要求と一緒に送信されたトークンに基づいてユーザーを認証しました。
    - 次のような `AADSTS65001: The user or administrator has not consented to use the application with ID...` エントリです。 要求された Microsoft Graph アクセス許可スコープに対する同意を求めるメッセージがまだユーザーに表示されていないので、これは想定されています。

## <a name="implement-consent-prompt"></a>同意プロンプトを実装する

Web API はユーザーにプロンプトを表示できないので、[Teams] タブでプロンプトを実装する必要があります。 これは、ユーザーごとに 1 回だけ行う必要があります。 ユーザーが同意すると、アプリケーションへのアクセスを明示的に取り消していない限り、同意する必要がなされます。

1. ./Pages ディレクトリに **新しいファイルを作成** し **、Authenticate.cshtml.csコード** を追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Pages/Authenticate.cshtml.cs" id="AuthenticateModelSnippet":::

1. **Authenticate.cshtml** という名前の **./Pages** ディレクトリに新しいファイルを作成し、次のコードを追加します。

    :::code language="razor" source="../demo/GraphTutorial/Pages/Authenticate.cshtml":::

1. **AuthComplete.cshtml** という名前の **./Pages** ディレクトリに新しいファイルを作成し、次のコードを追加します。

    :::code language="razor" source="../demo/GraphTutorial/Pages/AuthComplete.cshtml":::

1. **./Pages/Index.cshtml** を開き、タグ内に次の関数を追加 `<script>` します。

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="LoadUserCalendarSnippet":::

1. タグ内に次の関数を追加 `<script>` して、Web API からの正常な結果を表示します。

    ```javascript
    function renderCalendar(events) {
      $('#tab-container').empty();

      $('<pre/>').append($('<code/>', {
        text: JSON.stringify(events, null, 2),
        style: 'word-break: break-all;'
      })).appendTo('#tab-container');
    }
    ```

1. 既存のコードを次 `successCallback` のコードに置き換えます。

    ```javascript
    successCallback: (token) => {
      loadUserCalendar(token, (events) => {
        renderCalendar(events);
      });
    }
    ```

1. 変更内容を保存し、アプリを再起動します。 Microsoft Teams のタブを更新します。 タブが表示されます `{ "status": "OK" }` 。

1. ログ出力を確認します。 エントリが表示 `Access token for Graph` されます。 そのトークンを解析すると、そのトークンに、このトークンで構成されている Microsoft Graph のスコープが含appsettings.js **されます**。

## <a name="storing-and-refreshing-tokens"></a>トークンの保存と更新

この時点で、アプリケーションはアクセス トークンを持ち、API 呼び出しのヘッダー `Authorization` で送信されます。 これは、アプリが Microsoft Graph にユーザーの代わりにアクセスできるようにするトークンです。

ただし、このトークンは一時的なものです。 トークンは発行後 1 時間で期限切れになります。 ここで、更新トークンが役に立ちます。 更新トークンを使用すると、ユーザーが再度サインインしなくても、アプリは新しいアクセス トークンを要求できます。

アプリは Microsoft.Identity.Web ライブラリを使用しているので、トークンストレージまたは更新ロジックを実装する必要は一切ない。

アプリはメモリ内トークン キャッシュを使用しますが、アプリの再起動時にトークンを保持する必要がないアプリでは十分です。 実稼働アプリでは、代わりに[](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization)Microsoft.Identity.Web ライブラリの分散キャッシュ オプションを使用できます。

この `GetAccessTokenForUserAsync` メソッドは、トークンの有効期限と更新を処理します。 最初にキャッシュされたトークンをチェックし、有効期限が切れていない場合はトークンを返します。 有効期限が切れている場合は、キャッシュされた更新トークンを使用して新しい更新トークンを取得します。

コントローラー **が依存関係挿入** によって取得する GraphServiceClient は、自動的に使用する認証プロバイダーで事前 `GetAccessTokenForUserAsync` に構成されています。
