2. 以下列程式碼取代 TodoItem 類別定義：

     public class TodoItem
     {
         public string Id { get; set; }
    
         [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
         public string Text { get; set; }
    
         [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
         public bool Complete { get; set; }
     }

 **JsonPropertyAttribute** 可用來定義用戶端類型中屬性名稱與基礎資料表中資料行名稱之間的對應。
 >[AZURE.NOTE] 在通用 Windows 應用程式專案中，TodoItem 類別的定義位於共用之 DataModel 資料夾內的個別程式碼檔案中。

1. 在檔案 MainPage.cs 中，使用陳述式新增或取消註解下列項目：

        using Microsoft.WindowsAzure.MobileServices;

4. 註解化或刪除定義現有的項目集合，然後取消註解或新增下列幾行取代 _< 資料 >_ 與 `MobileServiceClient` 時將專案連線至行動服務新增到 App.xaml.cs 檔案:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();

    此程式碼會建立行動服務感知繫結集合 (項目)，和資料庫資料表 (todoTable) 的 Proxy 類別。

4. 在 **InsertTodoItem** 方法中，移除設定 **TodoItem.Id** 屬性的程式碼行，將 **async** 修正詞新增至方法，並取消註解下列程式碼行：

        await todoTable.InsertAsync(todoItem);

    此程式碼會將新的項目插入資料表中。

5. 以下列程式碼取代 **RefreshTodoItems** 方法：

        private async void RefreshTodoItems()
        {
            MobileServiceInvalidOperationException exception = null;
            try
            {
                // Query that returns all items.   
                items = await todoTable.ToCollectionAsync();             
            }
            catch (MobileServiceInvalidOperationException e)
            {
                exception = e;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Error loading items").ShowAsync();
            }
            else
            {
                ListItems.ItemsSource = items;
                this.ButtonSave.IsEnabled = true;
            }    
        }

    這會設定 `todoTable` 中項目集合的繫結，其中包含從行動服務中傳回的所有 **TodoItem** 物件。 如果您在執行查詢時發生問題，系統會引發訊息方塊來顯示錯誤。

6. 在 **UpdateCheckedTodoItem** 方法中，將 **async** 修正因子新增至方法，並取消註解下列程式碼行：

        await todoTable.UpdateAsync(item);

    This sends an item update to the mobile service.

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。




