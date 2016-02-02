
1. 在 Visual Studio 中，開啟您完成**開始使用資料**教學課程時所修改的專案。

2. 按 **F5** 鍵以執行應用程式，然後在 [Insert a TodoItem]**** 中鍵入文字，並按一下 [儲存]****。

3. 重複前述步驟至少三次，以在 TodoItem 資料表中儲存三個以上的項目。

2. 在 MainPage.xaml.cs 檔案中，使用下列程式碼來取代 **RefreshTodoItems** 方法：

        private async void RefreshTodoItems()
        {
            // Define a filtered query that returns the top 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                            .Where(todoItem => todoItem.Complete == false)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }

    在資料繫結期間執行此查詢時，會傳回未標示為完成的前三個項目。

3. 按 **F5** 鍵以執行應用程式。

    請注意，TodoItem 資料表中只有前三個結果會顯示出來。

4. (選用) 使用訊息檢查軟體 (例如瀏覽器開發人員工具或 Fiddler) 檢視傳送至行動服務之要求的 URI。

    請注意， `take (3)` 方法轉譯成查詢選項 `$top = 3` 查詢 URI 中。

5. 再次以下列程式碼更新 **RefreshTodoItems** 方法：

        private async void RefreshTodoItems()
        {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                           .Where(todoItem => todoItem.Complete == false)
                           .Skip(3)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }

    This query skips the first three results and returns the next three after that. 實際上這就是第二「頁」資料，頁面大小為三個項目。
    >[AZURE.NOTE]本教學課程中使用簡化的案例傳遞硬式編碼分頁值至 <strong>採取</strong> 和 <strong>略過</strong> 方法。 在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。 您也可以呼叫  <strong>IncludeTotalCount</strong> 方法來取得可用項目的總計數和分頁資料的伺服器上。

6. (選用) 檢視傳送至行動服務之要求的 URI。

    請注意， `skip (3)` 方法轉譯成查詢選項 `$skip = 3` 查詢 URI 中。



[fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412 

