
2. 以下列程式碼取代 TodoItem 類別定義： 

        public class TodoItem
        {
            public string Id { get; set; }
    
            [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
            public string Text { get; set; }
    
            [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
            public bool Complete { get; set; }
        }
    
     **JsonPropertyAttribute** 用來定義資料行名稱與基礎資料表中資料的用戶端類型中屬性名稱之間的對應。

    >[AZURE.NOTE] 在通用 Windows 應用程式專案中，TodoItem 類別定義不同的程式碼中的檔案共用之 DataModel 資料夾中。

1. 在檔案 MainPage.cs 中，使用陳述式新增或取消註解下列項目： 

        using Microsoft.WindowsAzure.MobileServices;


4. 註解化或刪除定義現有的項目集合，然後取消註解或新增下列幾行取代 _& lt; 資料 (& s) gt;_ 與 `MobileServiceClient` 時將專案連線至行動服務新增到 App.xaml.cs 檔案: 

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();
          
    此程式碼會建立行動服務感知繫結集合 (項目)，和資料庫資料表 (todoTable) 的 Proxy 類別。 


4. 在 **InsertTodoItem** 方法中，移除設定的程式碼行 **TodoItem.Id** 屬性中，加入 **非同步** 修飾詞新增至方法，並取消註解下列程式碼行: 

        await todoTable.InsertAsync(todoItem);


    此程式碼會將新的項目插入資料表中。 

5. 取代 **RefreshTodoItems** 方法取代下列程式碼: 

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

    此設定的繫結至集合中的項目 `todoTable`, ，其中包含的所有 **TodoItem** 從行動服務傳回的物件。 如果您在執行查詢時發生問題，系統會引發訊息方塊來顯示錯誤。 

6. 在 **UpdateCheckedTodoItem** 方法中，加入 **非同步** 修飾詞新增至方法，並取消註解下列程式碼行: 

        await todoTable.UpdateAsync(item);

    這會將項目更新傳送到行動服務。 

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。
