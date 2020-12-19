<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6094d-101">この演習では、Azure Active Directory 管理センターを使用AD新しい Azure AD Web アプリケーションの登録を作成します。</span><span class="sxs-lookup"><span data-stu-id="6094d-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="6094d-102">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動します。</span><span class="sxs-lookup"><span data-stu-id="6094d-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="6094d-103">**個人用アカウント** (別名: Microsoft アカウント)、または **職場/学校アカウント** を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="6094d-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="6094d-104">左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6094d-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="6094d-105">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="6094d-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="6094d-106">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6094d-106">Select **New registration**.</span></span> <span data-ttu-id="6094d-107">[ **アプリケーションの登録]** ページで、前のセクションでコピーした ngrok 転送 URL を次のように `YOUR_NGROK_URL` 設定します。</span><span class="sxs-lookup"><span data-stu-id="6094d-107">On the **Register an application** page, set the values as follows, where `YOUR_NGROK_URL` is the ngrok forwarding URL you copied in the previous section.</span></span>

    - <span data-ttu-id="6094d-108">`Teams Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="6094d-108">Set **Name** to `Teams Graph Tutorial`.</span></span>
    - <span data-ttu-id="6094d-109">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6094d-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="6094d-110">**[リダイレクト URI]** で、最初のドロップダウン リストを `Web` に設定し、それから `YOUR_NGROK_URL/authcomplete` に値を設定します。</span><span class="sxs-lookup"><span data-stu-id="6094d-110">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `YOUR_NGROK_URL/authcomplete`.</span></span>

    ![[アプリケーションを登録する] ページのスクリーンショット](./images/aad-register-an-app.png)

1. <span data-ttu-id="6094d-112">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6094d-112">Select **Register**.</span></span> <span data-ttu-id="6094d-113">**[Teams Graph チュートリアル**] ページで、アプリケーション **(クライアント) ID** の値をコピーして保存します。次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="6094d-113">On the **Teams Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)

1. <span data-ttu-id="6094d-115">**[管理]** の下の **[認証]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6094d-115">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="6094d-116">暗黙的な許可 **セクションを見つけて**、アクセス トークンと ID **トークン\*\*\*\*を有効にします**。</span><span class="sxs-lookup"><span data-stu-id="6094d-116">Locate the **Implicit grant** section and enable **Access tokens** and **ID tokens**.</span></span> <span data-ttu-id="6094d-117">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6094d-117">Select **Save**.</span></span>

1. <span data-ttu-id="6094d-118">**[管理]** で **[証明書とシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6094d-118">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="6094d-119">
            \*\*[新しいクライアント シークレット]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6094d-119">Select the **New client secret** button.</span></span> <span data-ttu-id="6094d-120">**[説明]** に値を入力して、**[有効期限]** のオプションのいずれかを選び、**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6094d-120">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

1. <span data-ttu-id="6094d-121">クライアント シークレットの値をコピーしてから、このページから移動します。</span><span class="sxs-lookup"><span data-stu-id="6094d-121">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="6094d-122">次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="6094d-122">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="6094d-123">このクライアント シークレットは今後表示されないため、今必ずコピーするようにしてください。</span><span class="sxs-lookup"><span data-stu-id="6094d-123">This client secret is never shown again, so make sure you copy it now.</span></span>

1. <span data-ttu-id="6094d-124">[管理 **] で [API のアクセス** 許可 **] を選択し**、[アクセス許可の **追加] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="6094d-124">Select **API permissions** under **Manage**, then select **Add a permission**.</span></span>

1. <span data-ttu-id="6094d-125">**[Microsoft Graph] を選択** し、[**委任されたアクセス許可] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="6094d-125">Select **Microsoft Graph**, then **Delegated permissions**.</span></span>

1. <span data-ttu-id="6094d-126">次のアクセス許可を選択し、[アクセス許可の追加 **] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="6094d-126">Select the following permissions, then select **Add permissions**.</span></span>

    - <span data-ttu-id="6094d-127">**Calendars.ReadWrite** - これにより、アプリはユーザーの予定表を読み書きできます。</span><span class="sxs-lookup"><span data-stu-id="6094d-127">**Calendars.ReadWrite** - this will allow the app to read and write to the user's calendar.</span></span>
    - <span data-ttu-id="6094d-128">**MailboxSettings.Read** - これにより、アプリはメールボックス設定からユーザーのタイム ゾーン、日付形式、時刻形式を取得できます。</span><span class="sxs-lookup"><span data-stu-id="6094d-128">**MailboxSettings.Read** - this will allow the app to get the user's time zone, date format, and time format from their mailbox settings.</span></span>

    ![構成されているアクセス許可のスクリーンショット](images/aad-configured-permissions.png)

## <a name="configure-teams-single-sign-on"></a><span data-ttu-id="6094d-130">Teams シングル サインオンを構成する</span><span class="sxs-lookup"><span data-stu-id="6094d-130">Configure Teams single sign-on</span></span>

<span data-ttu-id="6094d-131">このセクションでは、Teams でのシングル サインオンをサポートするためにアプリ [の登録を更新します](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso)。</span><span class="sxs-lookup"><span data-stu-id="6094d-131">In this section you'll update the app registration to support [single sign-on in Teams](/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso).</span></span>

1. <span data-ttu-id="6094d-132">[API **の公開] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="6094d-132">Select **Expose an API**.</span></span> <span data-ttu-id="6094d-133">[アプリケーション ID **URI]** の横にある [ **設定] リンクを選択します**。</span><span class="sxs-lookup"><span data-stu-id="6094d-133">Select the **Set** link next to **Application ID URI**.</span></span> <span data-ttu-id="6094d-134">二重スラッシュと GUID の間に ngrok 転送 URL ドメイン名 (末尾にスラッシュ "/" が付加された名前) を挿入します。</span><span class="sxs-lookup"><span data-stu-id="6094d-134">Insert your ngrok forwarding URL domain name (with a forward slash "/" appended to the end) between the double forward slashes and the GUID.</span></span> <span data-ttu-id="6094d-135">ID 全体は次のように表示されます `api://50153897dd4d.ngrok.io/ae7d8088-3422-4c8c-a351-6ded0f21d615` 。</span><span class="sxs-lookup"><span data-stu-id="6094d-135">The entire ID should look similar to: `api://50153897dd4d.ngrok.io/ae7d8088-3422-4c8c-a351-6ded0f21d615`.</span></span>

1. <span data-ttu-id="6094d-136">[この **API で定義されているスコープ** ] セクションで、[スコープの追加 **] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="6094d-136">In the **Scopes defined by this API** section, select **Add a scope**.</span></span> <span data-ttu-id="6094d-137">フィールドに次のように入力し、[範囲の追加 **] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="6094d-137">Fill in the fields as follows and select **Add scope**.</span></span>

    - <span data-ttu-id="6094d-138">**スコープ名:**`access_as_user`</span><span class="sxs-lookup"><span data-stu-id="6094d-138">**Scope name:** `access_as_user`</span></span>
    - <span data-ttu-id="6094d-139">**同意できるユーザー: 管理者とユーザー**</span><span class="sxs-lookup"><span data-stu-id="6094d-139">**Who can consent?: Admins and users**</span></span>
    - <span data-ttu-id="6094d-140">**管理者の同意の表示名:**`Access the app as the user`</span><span class="sxs-lookup"><span data-stu-id="6094d-140">**Admin consent display name:** `Access the app as the user`</span></span>
    - <span data-ttu-id="6094d-141">**管理者の同意の説明:**`Allows Teams to call the app's web APIs as the current user.`</span><span class="sxs-lookup"><span data-stu-id="6094d-141">**Admin consent description:** `Allows Teams to call the app's web APIs as the current user.`</span></span>
    - <span data-ttu-id="6094d-142">**ユーザーの同意の表示名:**`Access the app as you`</span><span class="sxs-lookup"><span data-stu-id="6094d-142">**User consent display name:** `Access the app as you`</span></span>
    - <span data-ttu-id="6094d-143">**ユーザーの同意の説明:**`Allows Teams to call the app's web APIs as you.`</span><span class="sxs-lookup"><span data-stu-id="6094d-143">**User consent description:** `Allows Teams to call the app's web APIs as you.`</span></span>
    - <span data-ttu-id="6094d-144">**状態: 有効**</span><span class="sxs-lookup"><span data-stu-id="6094d-144">**State: Enabled**</span></span>

    ![[範囲の追加] フォームのスクリーンショット](images/aad-add-scope.png)

1. <span data-ttu-id="6094d-146">[承認済み **クライアント アプリケーション] セクションで** 、[クライアント アプリケーションの **追加] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="6094d-146">In the **Authorized client applications** section, select **Add a client application**.</span></span> <span data-ttu-id="6094d-147">次の一覧からクライアント ID を入力し、[承認済みスコープ] の下のスコープを有効にして、[アプリケーションの追加]**を選択します**。</span><span class="sxs-lookup"><span data-stu-id="6094d-147">Enter a client ID from the following list, enable the scope under **Authorized scopes**, and select **Add application**.</span></span> <span data-ttu-id="6094d-148">リスト内の各クライアント ID に対してこのプロセスを繰り返します。</span><span class="sxs-lookup"><span data-stu-id="6094d-148">Repeat this process for each of the client IDs in the list.</span></span>

    - <span data-ttu-id="6094d-149">`1fec8e78-bce4-4aaf-ab1b-5451cc387264` (Teams モバイル/デスクトップ アプリケーション)</span><span class="sxs-lookup"><span data-stu-id="6094d-149">`1fec8e78-bce4-4aaf-ab1b-5451cc387264` (Teams mobile/desktop application)</span></span>
    - <span data-ttu-id="6094d-150">`5e3ce6c0-2b1f-4285-8d4b-75ee78787346` (Teams Web アプリケーション)</span><span class="sxs-lookup"><span data-stu-id="6094d-150">`5e3ce6c0-2b1f-4285-8d4b-75ee78787346` (Teams web application)</span></span>
