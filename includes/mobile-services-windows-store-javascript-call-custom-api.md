
##<a name="update-app"></a>更新應用程式以呼叫自訂 API

1. 在 Visual Studio 中，開啟 [快速入門專案的 default.html 檔案，找出 **按鈕** 名 `buttonRefresh`, ，並緊接其後新增下列新元素: 

        <button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>

    這會將新按鈕新增至頁面。 

2. 開啟 default.js 程式碼檔案中的 `js` 專案資料夾中，找出 **refreshTodoItems** 函式，並確定此函數包含下列程式碼:

        todoTable.where({ complete: false })
           .read()
           .done(function (results) {
               todoItems = new WinJS.Binding.List(results);
               listItems.winControl.itemDataSource = todoItems.dataSource;
           });            

    這會篩選項目，如此一來，查詢就不會傳回已完成的項目。

3. 之後 **refreshTodoItems** 函式中，新增下列程式碼:

        var completeAllTodoItems = function () {
            var okCommand = new Windows.UI.Popups.UICommand("OK");
        
            // Asynchronously call the custom API using the POST method. 
            mobileService.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.commands.append(okCommand);
                dialog.showAsync().done(function () {
                    refreshTodoItems();
                });
            }, function (error) {
                var dialog = new Windows.UI.Popups
                    .MessageDialog(error.message);
                dialog.commands.append(okCommand);
                dialog.showAsync().done();
            });
        };

        buttonCompleteAll.addEventListener("click", function () {
            completeAllTodoItems();
        });

    此方法會處理 **按一下** 新按鈕的事件。  **InvokeApiAsync** 方法會在用戶端，可將 POST 要求傳送給新的自訂 API 呼叫。 如有任何錯誤，自訂 API 傳回的結果會顯示在訊息對話方塊中。

## <a name="test-app"></a>測試應用程式

1. 在 Visual Studio 中按 **F5** 鍵，以重建專案並啟動應用程式。

2. 在應用程式中，輸入某些文字 **Insert a TodoItem**, ，然後按一下 [ **儲存**。

3. 重複前一個步驟，直到將數個 Todo 項目新增至清單為止。

4. 按一下 [ **完成所有** ] 按鈕。

    ![](./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png)

    出現訊息對話方塊，指出標示為完成的項目數，並重新執行篩選查詢，以便清除清單的所有項目。
