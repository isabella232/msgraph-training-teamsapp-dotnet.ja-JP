<!-- markdownlint-disable MD002 MD041 -->

このセクションでは、Microsoft Graph をアプリケーションに組み込む必要があります。 このアプリケーションでは [、.NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 用 Microsoft Graph クライアント ライブラリを使用して Microsoft Graph を呼び出します。

# <a name="get-a-calendar-view"></a>予定表ビューを取得する

カレンダー ビューは、2 つの時刻の間に発生する、ユーザーの予定表からのイベントのセットです。 これを使用して、現在の週のユーザーのイベントを取得します。

1. **./Controllers/CalendarController.cs** を開き **、CalendarController** クラスに次の関数を追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetStartOfWeekSnippet":::

1. Microsoft Graph 呼び出しから返される例外を処理する次の関数を追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="HandleGraphExceptionSnippet":::

1. 既存の `Get` 関数を、以下の関数で置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetSnippet" highlight="2,14-57":::

    変更を確認します。 この新しいバージョンの関数:

    - の代 `IEnumerable<Event>` わりに返します `string` 。
    - Microsoft Graph を使用してユーザーのメールボックス設定を取得します。
    - ユーザーのタイム ゾーンを使用して、現在の週の開始と終了を計算します。
    - 予定表ビューを取得します
        - 関数を使用してヘッダーを含め、返されたイベントの開始時刻と終了時刻をユーザーのタイムゾーン `.Header()` `Prefer: outlook.timezone` に変換します。
        - この関数 `.Top()` を使用して、最大で 50 のイベントを要求します。
        - この関数 `.Select()` を使用して、アプリで使用されるフィールドを要求します。
        - 関数を `OrderBy()` 使用して、開始時刻で結果を並べ替える。

1. 変更内容を保存し、アプリを再起動します。 Microsoft Teams のタブを更新します。 アプリには、イベントの JSON 一覧が表示されます。

## <a name="display-the-results"></a>結果の表示

これで、イベントの一覧を、より使い分け的な方法で表示できます。

1. **./Pages/Index.cshtml** を開き、タグ内に次の関数を追加 `<script>` します。

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderHelpersSnippet":::

1. 既存の `renderCalendar` 関数を、以下の関数で置き換えます。

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderCalendarSnippet":::

1. 変更内容を保存し、アプリを再起動します。 Microsoft Teams のタブを更新します。 アプリは、ユーザーの予定表にイベントを表示します。

    ![ユーザーの予定表を表示しているアプリのスクリーンショット](images/calendar-view.png)
