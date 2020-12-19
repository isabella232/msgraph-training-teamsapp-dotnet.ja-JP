<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="44553-101">この演習では、前の演習のアプリケーションを拡張して、Azure AD でのシングル サインオン認証をサポートします。</span><span class="sxs-lookup"><span data-stu-id="44553-101">In this exercise you will extend the application from the previous exercise to support single sign-on authentication with Azure AD.</span></span> <span data-ttu-id="44553-102">これは、Microsoft Graph API を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="44553-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="44553-103">この手順では [、Microsoft.Identity.Web ライブラリを構成](https://www.nuget.org/packages/Microsoft.Identity.Web/) します。</span><span class="sxs-lookup"><span data-stu-id="44553-103">In this step you will configure the [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) library.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="44553-104">アプリケーション ID とシークレットをソースに格納しないようにするには、.NET [Secret Manager](/aspnet/core/security/app-secrets) を使用してこれらの値を格納します。</span><span class="sxs-lookup"><span data-stu-id="44553-104">To avoid storing the application ID and secret in source, you will use the [.NET Secret Manager](/aspnet/core/security/app-secrets) to store these values.</span></span> <span data-ttu-id="44553-105">シークレット マネージャーは開発のみを目的とします。実稼働アプリでは、シークレットの保存に信頼できるシークレット マネージャーを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="44553-105">The Secret Manager is for development purposes only, production apps should use a trusted secret manager for storing secrets.</span></span>

1. <span data-ttu-id="44553-106">**./appsettings.jsを開き**、その内容を次の内容に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="44553-106">Open **./appsettings.json** and replace its contents with the following.</span></span>

    :::code language="json" source="../demo/GraphTutorial/appsettings.example.json" highlight="2-8":::

1. <span data-ttu-id="44553-107">**GraphTu clil.csproj** があるディレクトリで CLI を開き、Azure portal のアプリケーション ID とアプリケーション シークレットに置き換え、次のコマンドを実行します。 `YOUR_APP_ID` `YOUR_APP_SECRET`</span><span class="sxs-lookup"><span data-stu-id="44553-107">Open your CLI in the directory where **GraphTutorial.csproj** is located, and run the following commands, substituting `YOUR_APP_ID` with your application ID from the Azure portal, and `YOUR_APP_SECRET` with your application secret.</span></span>

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="44553-108">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="44553-108">Implement sign-in</span></span>

<span data-ttu-id="44553-109">まず、アプリの JavaScript コードにシングル サインオンを実装します。</span><span class="sxs-lookup"><span data-stu-id="44553-109">First, implement single sign-on in the app's JavaScript code.</span></span> <span data-ttu-id="44553-110">Microsoft Teams [JavaScript SDK](/javascript/api/overview/msteams-client) を使用してアクセス トークンを取得します。これにより、Teams クライアントで実行されている JavaScript コードは、後で実装する Web API への AJAX 呼び出しを行えます。</span><span class="sxs-lookup"><span data-stu-id="44553-110">You will use the [Microsoft Teams JavaScript SDK](/javascript/api/overview/msteams-client) to get an access token which allows the JavaScript code running in the Teams client to make AJAX calls to Web API you will implement later.</span></span>

1. <span data-ttu-id="44553-111">**./Pages/Index.cshtml** を開き、タグ内に次のコードを追加 `<script>` します。</span><span class="sxs-lookup"><span data-stu-id="44553-111">Open **./Pages/Index.cshtml** and add the following code inside the `<script>` tag.</span></span>

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

    <span data-ttu-id="44553-112">これにより `microsoftTeams.authentication.getAuthToken` 、Teams にサインインしているユーザーとしてサイレント認証を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="44553-112">This calls the `microsoftTeams.authentication.getAuthToken` to silently authenticate as the user that is signed in to Teams.</span></span> <span data-ttu-id="44553-113">ユーザーが同意する必要がない限り、通常は UI プロンプトは表示されません。</span><span class="sxs-lookup"><span data-stu-id="44553-113">There is typically not any UI prompts involved, unless the user has to consent.</span></span> <span data-ttu-id="44553-114">その後、コードはトークンをタブに表示します。</span><span class="sxs-lookup"><span data-stu-id="44553-114">The code then displays the token in the tab.</span></span>

1. <span data-ttu-id="44553-115">変更を保存し、CLI で次のコマンドを実行してアプリケーションを起動します。</span><span class="sxs-lookup"><span data-stu-id="44553-115">Save your changes and start your application by running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="44553-116">ngrok を再起動し、ngrok URL が変更されている場合は、テストする前に、次の場所で ngrok 値 **を更新してください** 。</span><span class="sxs-lookup"><span data-stu-id="44553-116">If you have restarted ngrok and your ngrok URL has changed, be sure to update the ngrok value in the following place **before** you test.</span></span>
    >
    > - <span data-ttu-id="44553-117">アプリ登録のリダイレクト URI</span><span class="sxs-lookup"><span data-stu-id="44553-117">The redirect URI in your app registration</span></span>
    > - <span data-ttu-id="44553-118">アプリ登録のアプリケーション ID URI</span><span class="sxs-lookup"><span data-stu-id="44553-118">The application ID URI in your app registration</span></span>
    > - <span data-ttu-id="44553-119">`contentUrl` in manifest.json</span><span class="sxs-lookup"><span data-stu-id="44553-119">`contentUrl` in manifest.json</span></span>
    > - <span data-ttu-id="44553-120">`validDomains` in manifest.json</span><span class="sxs-lookup"><span data-stu-id="44553-120">`validDomains` in manifest.json</span></span>
    > - <span data-ttu-id="44553-121">`resource` in manifest.json</span><span class="sxs-lookup"><span data-stu-id="44553-121">`resource` in manifest.json</span></span>

1. <span data-ttu-id="44553-122">ZIP ファイルを作成し、manifest.js **をオン、color.png、outline.png。** \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="44553-122">Create a ZIP file with **manifest.json**, **color.png**, and **outline.png**.</span></span>

1. <span data-ttu-id="44553-123">Microsoft Teams で、左側のバーで [**アプリ**]を選択し、[カスタム アプリのアップロード] を選択し、[自分または自分のチームにアップロード **] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="44553-123">In Microsoft Teams, select **Apps** in the left-hand bar, select **Upload a custom app**, then select **Upload for me or my teams**.</span></span>

    ![Microsoft Teams の [カスタム アプリのアップロード] リンクのスクリーンショット](images/upload-custom-app.png)

1. <span data-ttu-id="44553-125">前に作成した ZIP ファイルを参照し、[開く] を選択 **します**。</span><span class="sxs-lookup"><span data-stu-id="44553-125">Browse to the ZIP file you created previously and select **Open**.</span></span>

1. <span data-ttu-id="44553-126">アプリケーション情報を確認し、[追加] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="44553-126">Review the application information and select **Add**.</span></span>

1. <span data-ttu-id="44553-127">アプリケーションが Teams で開き、アクセス トークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="44553-127">The application opens in Teams and displays an access token.</span></span>

<span data-ttu-id="44553-128">トークンをコピーする場合は、トークンを別の[jwt.ms。](https://jwt.ms)</span><span class="sxs-lookup"><span data-stu-id="44553-128">If you copy the token, you can paste it into [jwt.ms](https://jwt.ms).</span></span> <span data-ttu-id="44553-129">対象ユーザー (クレーム) がアプリケーション ID であり、作成した API スコープ `aud` (クレーム) が唯一のスコープです `scp` `access_as_user` 。</span><span class="sxs-lookup"><span data-stu-id="44553-129">Verify that the audience (the `aud` claim) is your application ID, and the only scope (the `scp` claim) is the `access_as_user` API scope you created.</span></span> <span data-ttu-id="44553-130">つまり、このトークンは Microsoft Graph への直接アクセスを許可しないという意味です。</span><span class="sxs-lookup"><span data-stu-id="44553-130">That means that this token does not grant direct access to Microsoft Graph!</span></span> <span data-ttu-id="44553-131">代わりに、間もなく実装する Web API は、Microsoft Graph[](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)呼び出しで動作するトークンを取得するために、代理フローを使用してこのトークンを交換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="44553-131">Instead, the Web API you will implement soon will need to exchange this token using the [on-behalf-of flow](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) to get a token that will work with Microsoft Graph calls.</span></span>

## <a name="configure-authentication-in-the-aspnet-core-app"></a><span data-ttu-id="44553-132">ASP.NET Core アプリで認証を構成する</span><span class="sxs-lookup"><span data-stu-id="44553-132">Configure authentication in the ASP.NET Core app</span></span>

<span data-ttu-id="44553-133">まず、Microsoft Identity プラットフォーム サービスをアプリケーションに追加します。</span><span class="sxs-lookup"><span data-stu-id="44553-133">Start by adding the Microsoft Identity platform services to the application.</span></span>

1. <span data-ttu-id="44553-134">**./Startup.cs** ファイルを開き、ファイルの一番上に次 `using` のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="44553-134">Open the **./Startup.cs** file and add the following `using` statement to the top of the file.</span></span>

    ```csharp
    using Microsoft.Identity.Web;
    ```

1. <span data-ttu-id="44553-135">関数の行の直前に `app.UseAuthorization();` 次の行を追加 `Configure` します。</span><span class="sxs-lookup"><span data-stu-id="44553-135">Add the following line just before the `app.UseAuthorization();` line in the `Configure` function.</span></span>

    ```csharp
    app.UseAuthentication();
    ```

1. <span data-ttu-id="44553-136">関数の行の直後に `endpoints.MapRazorPages();` 次の行を追加 `Configure` します。</span><span class="sxs-lookup"><span data-stu-id="44553-136">Add the following line just after the `endpoints.MapRazorPages();` line in the `Configure` function.</span></span>

    ```csharp
    endpoints.MapControllers();
    ```

1. <span data-ttu-id="44553-137">既存の `ConfigureServices` 関数を、以下の関数で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="44553-137">Replace the existing `ConfigureServices` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="ConfigureServicesSnippet":::

    <span data-ttu-id="44553-138">このコードは、ヘッダー内の JWT ベアラー トークンに基づいて Web API への呼び出しを認証できるアプリケーションを構成 `Authorization` します。</span><span class="sxs-lookup"><span data-stu-id="44553-138">This code configures the application to allow calls to Web APIs to be authenticated based on the JWT bearer token in the `Authorization` header.</span></span> <span data-ttu-id="44553-139">また、代理フローを介してそのトークンを交換できるトークン取得サービスも追加します。</span><span class="sxs-lookup"><span data-stu-id="44553-139">It also adds the token acquisition services that can exchange that token via the on-behalf-of flow.</span></span>

## <a name="create-the-web-api-controller"></a><span data-ttu-id="44553-140">Web API コントローラーを作成する</span><span class="sxs-lookup"><span data-stu-id="44553-140">Create the Web API controller</span></span>

1. <span data-ttu-id="44553-141">Controllers という名前のプロジェクトのルートに新しいディレクトリを **作成します**。</span><span class="sxs-lookup"><span data-stu-id="44553-141">Create a new directory in the root of the project named **Controllers**.</span></span>

1. <span data-ttu-id="44553-142">**./Controllers** ディレクトリに新しいファイルを作成し、CalendarController.cs **コードを** 追加します。</span><span class="sxs-lookup"><span data-stu-id="44553-142">Create a new file in the **./Controllers** directory named **CalendarController.cs** and add the following code.</span></span>

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

    <span data-ttu-id="44553-143">これにより、Teams タブから呼び出し可能な Web API ( `GET /calendar` ) が実装されます。今のところは、Graph トークンのベアラー トークンの交換を試みるのです。</span><span class="sxs-lookup"><span data-stu-id="44553-143">This implements a Web API (`GET /calendar`) that can be called from the Teams tab. For now it simply tries to exchange the bearer token for a Graph token.</span></span> <span data-ttu-id="44553-144">ユーザーが初めてタブを読み込む場合、アプリが自分の代わりに Microsoft Graph へのアクセスを許可することはまだ同意していないので、これは失敗します。</span><span class="sxs-lookup"><span data-stu-id="44553-144">The first time a user loads the tab, this will fail because they have not yet consented to allow the app access to Microsoft Graph on their behalf.</span></span>

1. <span data-ttu-id="44553-145">**./Pages/Index.cshtml** を開き、関数を `successCallback` 次の関数に置き換える。</span><span class="sxs-lookup"><span data-stu-id="44553-145">Open **./Pages/Index.cshtml** and replace the `successCallback` function with the following.</span></span>

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

    <span data-ttu-id="44553-146">これにより、Web API が呼び出され、応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="44553-146">This will call the Web API and display the response.</span></span>

1. <span data-ttu-id="44553-147">変更内容を保存し、アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="44553-147">Save your changes and restart the app.</span></span> <span data-ttu-id="44553-148">Microsoft Teams のタブを更新します。</span><span class="sxs-lookup"><span data-stu-id="44553-148">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="44553-149">ページが表示されます `consent_required` 。</span><span class="sxs-lookup"><span data-stu-id="44553-149">The page should display `consent_required`.</span></span>

1. <span data-ttu-id="44553-150">CLI でログ出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="44553-150">Review the log output in your CLI.</span></span> <span data-ttu-id="44553-151">2 つの点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="44553-151">Notice two things.</span></span>

    - <span data-ttu-id="44553-152">次のようなエントリ `Authenticated user: MeganB@contoso.com` です。</span><span class="sxs-lookup"><span data-stu-id="44553-152">An entry like `Authenticated user: MeganB@contoso.com`.</span></span> <span data-ttu-id="44553-153">Web API は、API 要求と一緒に送信されたトークンに基づいてユーザーを認証しました。</span><span class="sxs-lookup"><span data-stu-id="44553-153">The Web API has authenticated the user based on the token sent with the API request.</span></span>
    - <span data-ttu-id="44553-154">次のような `AADSTS65001: The user or administrator has not consented to use the application with ID...` エントリです。</span><span class="sxs-lookup"><span data-stu-id="44553-154">An entry like `AADSTS65001: The user or administrator has not consented to use the application with ID...`.</span></span> <span data-ttu-id="44553-155">要求された Microsoft Graph アクセス許可スコープに対する同意を求めるメッセージがまだユーザーに表示されていないので、これは想定されています。</span><span class="sxs-lookup"><span data-stu-id="44553-155">This is expected, since the user has not yet been prompted to consent for the requested Microsoft Graph permission scopes.</span></span>

## <a name="implement-consent-prompt"></a><span data-ttu-id="44553-156">同意プロンプトを実装する</span><span class="sxs-lookup"><span data-stu-id="44553-156">Implement consent prompt</span></span>

<span data-ttu-id="44553-157">Web API はユーザーにプロンプトを表示できないので、[Teams] タブでプロンプトを実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="44553-157">Because the Web API cannot prompt the user, the Teams tab will need to implement a prompt.</span></span> <span data-ttu-id="44553-158">これは、ユーザーごとに 1 回だけ行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="44553-158">This will only need to be done once for each user.</span></span> <span data-ttu-id="44553-159">ユーザーが同意すると、アプリケーションへのアクセスを明示的に取り消していない限り、同意する必要がなされます。</span><span class="sxs-lookup"><span data-stu-id="44553-159">Once a user consents, they do not need to reconsent unless they explicitly revoke access to your application.</span></span>

1. <span data-ttu-id="44553-160">./Pages ディレクトリに **新しいファイルを作成** し **、Authenticate.cshtml.csコード** を追加します。</span><span class="sxs-lookup"><span data-stu-id="44553-160">Create a new file in the **./Pages** directory named **Authenticate.cshtml.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Pages/Authenticate.cshtml.cs" id="AuthenticateModelSnippet":::

1. <span data-ttu-id="44553-161">**Authenticate.cshtml** という名前の **./Pages** ディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="44553-161">Create a new file in the **./Pages** directory named **Authenticate.cshtml** and add the following code.</span></span>

    :::code language="razor" source="../demo/GraphTutorial/Pages/Authenticate.cshtml":::

1. <span data-ttu-id="44553-162">**AuthComplete.cshtml** という名前の **./Pages** ディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="44553-162">Create a new file in the **./Pages** directory named **AuthComplete.cshtml** and add the following code.</span></span>

    :::code language="razor" source="../demo/GraphTutorial/Pages/AuthComplete.cshtml":::

1. <span data-ttu-id="44553-163">**./Pages/Index.cshtml** を開き、タグ内に次の関数を追加 `<script>` します。</span><span class="sxs-lookup"><span data-stu-id="44553-163">Open **./Pages/Index.cshtml** and add the following functions inside the `<script>` tag.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="LoadUserCalendarSnippet":::

1. <span data-ttu-id="44553-164">タグ内に次の関数を追加 `<script>` して、Web API からの正常な結果を表示します。</span><span class="sxs-lookup"><span data-stu-id="44553-164">Add the following function inside the `<script>` tag to display a successful result from the Web API.</span></span>

    ```javascript
    function renderCalendar(events) {
      $('#tab-container').empty();

      $('<pre/>').append($('<code/>', {
        text: JSON.stringify(events, null, 2),
        style: 'word-break: break-all;'
      })).appendTo('#tab-container');
    }
    ```

1. <span data-ttu-id="44553-165">既存のコードを次 `successCallback` のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="44553-165">Replace the existing `successCallback` with the following code.</span></span>

    ```javascript
    successCallback: (token) => {
      loadUserCalendar(token, (events) => {
        renderCalendar(events);
      });
    }
    ```

1. <span data-ttu-id="44553-166">変更内容を保存し、アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="44553-166">Save your changes and restart the app.</span></span> <span data-ttu-id="44553-167">Microsoft Teams のタブを更新します。</span><span class="sxs-lookup"><span data-stu-id="44553-167">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="44553-168">タブが表示されます `{ "status": "OK" }` 。</span><span class="sxs-lookup"><span data-stu-id="44553-168">The tab should display `{ "status": "OK" }`.</span></span>

1. <span data-ttu-id="44553-169">ログ出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="44553-169">Review the log output.</span></span> <span data-ttu-id="44553-170">エントリが表示 `Access token for Graph` されます。</span><span class="sxs-lookup"><span data-stu-id="44553-170">You should see the `Access token for Graph` entry.</span></span> <span data-ttu-id="44553-171">そのトークンを解析すると、そのトークンに、このトークンで構成されている Microsoft Graph のスコープが含appsettings.js **されます**。</span><span class="sxs-lookup"><span data-stu-id="44553-171">If you parse that token, you'll notice that it contains the Microsoft Graph scopes configured in **appsettings.json**.</span></span>

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="44553-172">トークンの保存と更新</span><span class="sxs-lookup"><span data-stu-id="44553-172">Storing and refreshing tokens</span></span>

<span data-ttu-id="44553-173">この時点で、アプリケーションはアクセス トークンを持ち、API 呼び出しのヘッダー `Authorization` で送信されます。</span><span class="sxs-lookup"><span data-stu-id="44553-173">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="44553-174">これは、アプリが Microsoft Graph にユーザーの代わりにアクセスできるようにするトークンです。</span><span class="sxs-lookup"><span data-stu-id="44553-174">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="44553-175">ただし、このトークンは一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="44553-175">However, this token is short-lived.</span></span> <span data-ttu-id="44553-176">トークンは発行後 1 時間で期限切れになります。</span><span class="sxs-lookup"><span data-stu-id="44553-176">The token expires an hour after it is issued.</span></span> <span data-ttu-id="44553-177">ここで、更新トークンが役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="44553-177">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="44553-178">更新トークンを使用すると、ユーザーが再度サインインしなくても、アプリは新しいアクセス トークンを要求できます。</span><span class="sxs-lookup"><span data-stu-id="44553-178">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="44553-179">アプリは Microsoft.Identity.Web ライブラリを使用しているので、トークンストレージまたは更新ロジックを実装する必要は一切ない。</span><span class="sxs-lookup"><span data-stu-id="44553-179">Because the app is using the Microsoft.Identity.Web library, you do not have to implement any token storage or refresh logic.</span></span>

<span data-ttu-id="44553-180">アプリはメモリ内トークン キャッシュを使用しますが、アプリの再起動時にトークンを保持する必要がないアプリでは十分です。</span><span class="sxs-lookup"><span data-stu-id="44553-180">The app uses the in-memory token cache, which is sufficient for apps that do not need to persist tokens when the app restarts.</span></span> <span data-ttu-id="44553-181">実稼働アプリでは、代わりに[](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization)Microsoft.Identity.Web ライブラリの分散キャッシュ オプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="44553-181">Production apps may instead use the [distributed cache options](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) in the Microsoft.Identity.Web library.</span></span>

<span data-ttu-id="44553-182">この `GetAccessTokenForUserAsync` メソッドは、トークンの有効期限と更新を処理します。</span><span class="sxs-lookup"><span data-stu-id="44553-182">The `GetAccessTokenForUserAsync` method handles token expiration and refresh for you.</span></span> <span data-ttu-id="44553-183">最初にキャッシュされたトークンをチェックし、有効期限が切れていない場合はトークンを返します。</span><span class="sxs-lookup"><span data-stu-id="44553-183">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="44553-184">有効期限が切れている場合は、キャッシュされた更新トークンを使用して新しい更新トークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="44553-184">If it is expired, it uses the cached refresh token to obtain a new one.</span></span>

<span data-ttu-id="44553-185">コントローラー **が依存関係挿入** によって取得する GraphServiceClient は、自動的に使用する認証プロバイダーで事前 `GetAccessTokenForUserAsync` に構成されています。</span><span class="sxs-lookup"><span data-stu-id="44553-185">The **GraphServiceClient** that controllers get via dependency injection is pre-configured with an authentication provider that uses `GetAccessTokenForUserAsync` for you.</span></span>
