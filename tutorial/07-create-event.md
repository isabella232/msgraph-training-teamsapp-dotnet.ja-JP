<!-- markdownlint-disable MD002 MD041 -->

このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。

## <a name="create-the-new-event-tab"></a>新しいイベント タブを作成する

1. **NewEvent.cshtml** という名前の **./Pages** ディレクトリに新しいファイルを作成し、次のコードを追加します。

    :::code language="razor" source="../demo/GraphTutorial/Pages/NewEvent.cshtml":::

    これは単純なフォームを実装し、JavaScript を追加してフォーム データを Web API にポストします。

## <a name="implement-the-web-api"></a>Web API を実装する

1. プロジェクトのルートに **Models** という名前の新しいディレクトリを作成します。

1. ./Models ディレクトリに **新しいファイルを作成** し **、NewEvent.csコード** を追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

1. **./Controllers/CalendarController.cs** を開き、ファイルの上部に次の `using` ステートメントを追加します。

    ```csharp
    using GraphTutorial.Models;
    ```

1. CalendarController クラスに次 **の関数を追加** します。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="PostSnippet":::

    これにより、フォームのフィールドを含む Web API への HTTP POST が可能です。

1. すべての変更を保存し、アプリケーションを再起動します。 Microsoft Teams でアプリを更新し、[イベントの作成] **タブを選択** します。フォームに入力し、[作成] **を選択** してユーザーの予定表にイベントを追加します。

    ![[イベントの作成] タブのスクリーンショット](images/create-event.png)
