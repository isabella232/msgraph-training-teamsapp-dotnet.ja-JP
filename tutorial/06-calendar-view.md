<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="46e22-101">このセクションでは、Microsoft Graph をアプリケーションに組み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="46e22-101">In this section you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="46e22-102">このアプリケーションでは [、.NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 用 Microsoft Graph クライアント ライブラリを使用して Microsoft Graph を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="46e22-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

# <a name="get-a-calendar-view"></a><span data-ttu-id="46e22-103">予定表ビューを取得する</span><span class="sxs-lookup"><span data-stu-id="46e22-103">Get a calendar view</span></span>

<span data-ttu-id="46e22-104">カレンダー ビューは、2 つの時刻の間に発生する、ユーザーの予定表からのイベントのセットです。</span><span class="sxs-lookup"><span data-stu-id="46e22-104">A calendar view is a set of events from the user's calendar that occur between two points of time.</span></span> <span data-ttu-id="46e22-105">これを使用して、現在の週のユーザーのイベントを取得します。</span><span class="sxs-lookup"><span data-stu-id="46e22-105">You'll use this to get the user's events for the current week.</span></span>

1. <span data-ttu-id="46e22-106">**./Controllers/CalendarController.cs** を開き **、CalendarController** クラスに次の関数を追加します。</span><span class="sxs-lookup"><span data-stu-id="46e22-106">Open **./Controllers/CalendarController.cs** and add the following function to the **CalendarController** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetStartOfWeekSnippet":::

1. <span data-ttu-id="46e22-107">Microsoft Graph 呼び出しから返される例外を処理する次の関数を追加します。</span><span class="sxs-lookup"><span data-stu-id="46e22-107">Add the following function to handle exceptions returned from Microsoft Graph calls.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="HandleGraphExceptionSnippet":::

1. <span data-ttu-id="46e22-108">既存の `Get` 関数を、以下の関数で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="46e22-108">Replace the existing `Get` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetSnippet" highlight="2,14-57":::

    <span data-ttu-id="46e22-109">変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="46e22-109">Review the changes.</span></span> <span data-ttu-id="46e22-110">この新しいバージョンの関数:</span><span class="sxs-lookup"><span data-stu-id="46e22-110">This new version of the function:</span></span>

    - <span data-ttu-id="46e22-111">の代 `IEnumerable<Event>` わりに返します `string` 。</span><span class="sxs-lookup"><span data-stu-id="46e22-111">Returns `IEnumerable<Event>` instead of `string`.</span></span>
    - <span data-ttu-id="46e22-112">Microsoft Graph を使用してユーザーのメールボックス設定を取得します。</span><span class="sxs-lookup"><span data-stu-id="46e22-112">Gets the user's mailbox settings using Microsoft Graph.</span></span>
    - <span data-ttu-id="46e22-113">ユーザーのタイム ゾーンを使用して、現在の週の開始と終了を計算します。</span><span class="sxs-lookup"><span data-stu-id="46e22-113">Uses the user's time zone to calculate the start and end of the current week.</span></span>
    - <span data-ttu-id="46e22-114">予定表ビューを取得します</span><span class="sxs-lookup"><span data-stu-id="46e22-114">Gets a calendar view</span></span>
        - <span data-ttu-id="46e22-115">関数を使用してヘッダーを含め、返されたイベントの開始時刻と終了時刻をユーザーのタイムゾーン `.Header()` `Prefer: outlook.timezone` に変換します。</span><span class="sxs-lookup"><span data-stu-id="46e22-115">Uses the `.Header()` function to include a `Prefer: outlook.timezone` header, which causes the returned events to have their start and end times converted to the user's timezone.</span></span>
        - <span data-ttu-id="46e22-116">この関数 `.Top()` を使用して、最大で 50 のイベントを要求します。</span><span class="sxs-lookup"><span data-stu-id="46e22-116">Uses the `.Top()` function to request at most 50 events.</span></span>
        - <span data-ttu-id="46e22-117">この関数 `.Select()` を使用して、アプリで使用されるフィールドを要求します。</span><span class="sxs-lookup"><span data-stu-id="46e22-117">Uses the `.Select()` function to request just the fields used by the app.</span></span>
        - <span data-ttu-id="46e22-118">関数を `OrderBy()` 使用して、開始時刻で結果を並べ替える。</span><span class="sxs-lookup"><span data-stu-id="46e22-118">Uses the `OrderBy()` function to sort the results by the start time.</span></span>

1. <span data-ttu-id="46e22-119">変更内容を保存し、アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="46e22-119">Save your changes and restart the app.</span></span> <span data-ttu-id="46e22-120">Microsoft Teams のタブを更新します。</span><span class="sxs-lookup"><span data-stu-id="46e22-120">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="46e22-121">アプリには、イベントの JSON 一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="46e22-121">The app displays a JSON listing of the events.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="46e22-122">結果の表示</span><span class="sxs-lookup"><span data-stu-id="46e22-122">Display the results</span></span>

<span data-ttu-id="46e22-123">これで、イベントの一覧を、より使い分け的な方法で表示できます。</span><span class="sxs-lookup"><span data-stu-id="46e22-123">Now you can display the list of events in a more user friendly way.</span></span>

1. <span data-ttu-id="46e22-124">**./Pages/Index.cshtml** を開き、タグ内に次の関数を追加 `<script>` します。</span><span class="sxs-lookup"><span data-stu-id="46e22-124">Open **./Pages/Index.cshtml** and add the following functions inside the `<script>` tag.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderHelpersSnippet":::

1. <span data-ttu-id="46e22-125">既存の `renderCalendar` 関数を、以下の関数で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="46e22-125">Replace the existing `renderCalendar` function with the following.</span></span>

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderCalendarSnippet":::

1. <span data-ttu-id="46e22-126">変更内容を保存し、アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="46e22-126">Save your changes and restart the app.</span></span> <span data-ttu-id="46e22-127">Microsoft Teams のタブを更新します。</span><span class="sxs-lookup"><span data-stu-id="46e22-127">Refresh the tab in Microsoft Teams.</span></span> <span data-ttu-id="46e22-128">アプリは、ユーザーの予定表にイベントを表示します。</span><span class="sxs-lookup"><span data-stu-id="46e22-128">The app displays events on the user's calendar.</span></span>

    ![ユーザーの予定表を表示しているアプリのスクリーンショット](images/calendar-view.png)
