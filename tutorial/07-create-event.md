<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="1c434-101">このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="1c434-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-the-new-event-tab"></a><span data-ttu-id="1c434-102">新しいイベント タブを作成する</span><span class="sxs-lookup"><span data-stu-id="1c434-102">Create the new event tab</span></span>

1. <span data-ttu-id="1c434-103">**NewEvent.cshtml** という名前の **./Pages** ディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="1c434-103">Create a new file in the **./Pages** directory named **NewEvent.cshtml** and add the following code.</span></span>

    :::code language="razor" source="../demo/GraphTutorial/Pages/NewEvent.cshtml":::

    <span data-ttu-id="1c434-104">これは単純なフォームを実装し、JavaScript を追加してフォーム データを Web API にポストします。</span><span class="sxs-lookup"><span data-stu-id="1c434-104">This implements a simple form, and adds JavaScript to post the form data to the Web API.</span></span>

## <a name="implement-the-web-api"></a><span data-ttu-id="1c434-105">Web API を実装する</span><span class="sxs-lookup"><span data-stu-id="1c434-105">Implement the Web API</span></span>

1. <span data-ttu-id="1c434-106">プロジェクトのルートに **Models** という名前の新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="1c434-106">Create a new directory named **Models** in the root of the project.</span></span>

1. <span data-ttu-id="1c434-107">./Models ディレクトリに **新しいファイルを作成** し **、NewEvent.csコード** を追加します。</span><span class="sxs-lookup"><span data-stu-id="1c434-107">Create a new file in the **./Models** directory named **NewEvent.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

1. <span data-ttu-id="1c434-108">**./Controllers/CalendarController.cs** を開き、ファイルの上部に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="1c434-108">Open **./Controllers/CalendarController.cs** and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    ```

1. <span data-ttu-id="1c434-109">CalendarController クラスに次 **の関数を追加** します。</span><span class="sxs-lookup"><span data-stu-id="1c434-109">Add the following function to the **CalendarController** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="PostSnippet":::

    <span data-ttu-id="1c434-110">これにより、フォームのフィールドを含む Web API への HTTP POST が可能です。</span><span class="sxs-lookup"><span data-stu-id="1c434-110">This allows an HTTP POST to the Web API with the fields of the form.</span></span>

1. <span data-ttu-id="1c434-111">すべての変更を保存し、アプリケーションを再起動します。</span><span class="sxs-lookup"><span data-stu-id="1c434-111">Save all of your changes and restart the application.</span></span> <span data-ttu-id="1c434-112">Microsoft Teams でアプリを更新し、[イベントの作成] **タブを選択** します。フォームに入力し、[作成] **を選択** してユーザーの予定表にイベントを追加します。</span><span class="sxs-lookup"><span data-stu-id="1c434-112">Refresh the app in Microsoft Teams, and select the **Create event** tab. Fill out the form and select **Create** to add an event to the user's calendar.</span></span>

    ![[イベントの作成] タブのスクリーンショット](images/create-event.png)
