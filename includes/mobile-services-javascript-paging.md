

1. 在 Visual Studio 中，開啟您完成教學課程時所修改的專案 **開始使用資料**。

2. 按下 **F5** 鍵以執行應用程式，然後輸入文字中的 **Insert a TodoItem** 按一下 **儲存**。

3. 重複前述步驟至少三次，以在 TodoItem 資料表中儲存三個以上的項目。 

2. 在 default.js 檔案中，將 **RefreshTodoItems** 方法取代下列程式碼 ︰

        var refreshTodoItems = function () {
            // Define a filtered query that returns the top 3 items.
            todoTable.where({ complete: false })
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

    在資料繫結期間執行此查詢時，會傳回未標示為完成的前三個項目。

3. 按下 **F5** 鍵以執行應用程式。

    請注意，TodoItem 資料表中只有前三個結果會顯示出來。 

4. （選擇性）檢視傳送至行動服務使用訊息檢查軟體，例如瀏覽器開發人員工具的要求的 URI 或 [Fiddler]。 

    請注意， **take （3)** 方法轉譯成查詢選項 **$top = 3** 查詢 URI 中。

5. 更新 **RefreshTodoItems** 方法一次以下列程式碼 ︰
            
        var refreshTodoItems = function () {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            todoTable.where({ complete: false })
                .skip(3)
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

    This query skips the first three results and returns the next three after that. 實際上這就是第二「頁」資料，頁面大小為三個項目。

    > [AZURE.NOTE] 本教學課程中使用簡化的案例傳遞硬式編碼分頁值至 **採取** 和 **略過** 方法。 在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。  您也可以呼叫  **includeTotalCount** 方法來取得可用項目的總計數和分頁資料的伺服器上。

6. (選用) 檢視傳送至行動服務之要求的 URI。 

    請注意， **skip （3)** 方法轉譯成查詢選項 **$skip = 3** 查詢 URI 中。

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412

