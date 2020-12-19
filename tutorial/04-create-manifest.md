<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8518e-101">アプリ マニフェストは、アプリを Microsoft Teams と統合する方法を説明し、アプリをインストールするために必要です。</span><span class="sxs-lookup"><span data-stu-id="8518e-101">The app manifest describes how the app integrates with Microsoft Teams and is required to install apps.</span></span> <span data-ttu-id="8518e-102">このセクションでは、Microsoft Teams クライアントの App Studio を使用してマニフェストを生成します。</span><span class="sxs-lookup"><span data-stu-id="8518e-102">In this section you'll use App Studio in the Microsoft Teams client to generate a manifest.</span></span>

1. <span data-ttu-id="8518e-103">まだ App Studio が Teams にインストールされていない場合は、 [今すぐインストールします](/microsoftteams/platform/concepts/build-and-test/app-studio-overview)。</span><span class="sxs-lookup"><span data-stu-id="8518e-103">If you do not already have App Studio installed in Teams, [install it now](/microsoftteams/platform/concepts/build-and-test/app-studio-overview).</span></span>

1. <span data-ttu-id="8518e-104">Microsoft Teams で App Studio を起動し、マニフェスト エディター **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="8518e-104">Launch App Studio in Microsoft Teams and select the **Manifest editor**.</span></span>

1. <span data-ttu-id="8518e-105">[新 **しいアプリの作成] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="8518e-105">Select **Create a new app**.</span></span>

    ![Microsoft Teams の App Studio のマニフェスト エディターのスクリーンショット](images/app-studio-01.png)

1. <span data-ttu-id="8518e-107">[アプリ **の詳細] ページ** で、必要なフィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="8518e-107">On the **App details** page, fill in the required fields.</span></span>

    > [!NOTE]
    > <span data-ttu-id="8518e-108">[ブランド] セクションの既定のアイコンを **使用するか** 、独自のアイコンをアップロードできます。</span><span class="sxs-lookup"><span data-stu-id="8518e-108">You can use the default icons in the **Branding** section or upload your own.</span></span>

1. <span data-ttu-id="8518e-109">左側のメニューで、[機能] の下の **[タブ** ] **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="8518e-109">On the left-hand menu, select **Tabs** under **Capabilities**.</span></span>

1. <span data-ttu-id="8518e-110">[個人用 **タブの** 追加 **] で [追加] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="8518e-110">Select **Add** under **Add a personal tab**.</span></span>

    ![App Studio の [タブ] ページのスクリーンショット](images/app-studio-02.png)

1. <span data-ttu-id="8518e-112">前のセクションでコピーした転送 URL を次 `YOUR_NGROK_URL` のようにフィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="8518e-112">Fill in the fields as follows, where `YOUR_NGROK_URL` is the forwarding URL you copied in the previous section.</span></span> <span data-ttu-id="8518e-113">完了したら **、[保存** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="8518e-113">Select **Save** when done.</span></span>

    - <span data-ttu-id="8518e-114">**名前:**`Create event`</span><span class="sxs-lookup"><span data-stu-id="8518e-114">**Name:** `Create event`</span></span>
    - <span data-ttu-id="8518e-115">**エンティティ ID:**`createEventTab`</span><span class="sxs-lookup"><span data-stu-id="8518e-115">**Entity ID:** `createEventTab`</span></span>
    - <span data-ttu-id="8518e-116">**コンテンツ URL:**`YOUR_NGROK_URL/newevent`</span><span class="sxs-lookup"><span data-stu-id="8518e-116">**Content URL:** `YOUR_NGROK_URL/newevent`</span></span>

1. <span data-ttu-id="8518e-117">[個人用 **タブの** 追加 **] で [追加] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="8518e-117">Select **Add** under **Add a personal tab**.</span></span>

1. <span data-ttu-id="8518e-118">前のセクションでコピーした転送 URL を次 `YOUR_NGROK_URL` のようにフィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="8518e-118">Fill in the fields as follows, where `YOUR_NGROK_URL` is the forwarding URL you copied in the previous section.</span></span> <span data-ttu-id="8518e-119">完了したら **、[保存** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="8518e-119">Select **Save** when done.</span></span>

    - <span data-ttu-id="8518e-120">**名前:**`Graph calendar`</span><span class="sxs-lookup"><span data-stu-id="8518e-120">**Name:** `Graph calendar`</span></span>
    - <span data-ttu-id="8518e-121">**エンティティ ID:**`calendarTab`</span><span class="sxs-lookup"><span data-stu-id="8518e-121">**Entity ID:** `calendarTab`</span></span>
    - <span data-ttu-id="8518e-122">**コンテンツ URL:**`YOUR_NGROK_URL`</span><span class="sxs-lookup"><span data-stu-id="8518e-122">**Content URL:** `YOUR_NGROK_URL`</span></span>

1. <span data-ttu-id="8518e-123">左側のメニューで、[完了] の下の [ **ドメインとアクセス許可]** を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="8518e-123">On the left-hand menu, select **Domains and permissions** under **Finish**.</span></span>

1. <span data-ttu-id="8518e-124">**AAD アプリ ID をアプリ** 登録のアプリケーション ID に設定します。</span><span class="sxs-lookup"><span data-stu-id="8518e-124">Set the **AAD App ID** to the application ID from your app registration.</span></span>

1. <span data-ttu-id="8518e-125">アプリの **登録から、[シングル サインオン]** フィールドをアプリケーション ID URI に設定します。</span><span class="sxs-lookup"><span data-stu-id="8518e-125">Set the **Single-Sign-On** field to the application ID URI from your app registration.</span></span>

1. <span data-ttu-id="8518e-126">左側のメニューで、[テスト] を選択し、[完了] **の下で** 配布 **します**。</span><span class="sxs-lookup"><span data-stu-id="8518e-126">On the left-hand menu, select **Test and distribute** under **Finish**.</span></span> <span data-ttu-id="8518e-127">[ダウンロード **] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="8518e-127">Select **Download**.</span></span>

1. <span data-ttu-id="8518e-128">Manifest という名前のプロジェクトのルートに新しいディレクトリを作成 **します**。</span><span class="sxs-lookup"><span data-stu-id="8518e-128">Create a new directory in the root of the project named **Manifest**.</span></span> <span data-ttu-id="8518e-129">ダウンロードした ZIP ファイルの内容をこのディレクトリに抽出します。</span><span class="sxs-lookup"><span data-stu-id="8518e-129">Extract the contents of the downloaded ZIP file to this directory.</span></span>
