##<a name="update-app"></a>更新應用程式以呼叫自訂 API

1. 在 Visual Studio 中，開啟 [快速入門專案中的 MainPage.xaml 檔案，找出 **按鈕** 名 `ButtonRefresh`, ，並以下列 XAML 程式碼取代 ︰ 

        <StackPanel Grid.Row="3" Grid.ColumnSpan="2" Orientation="Horizontal">
            <Button Width="225" Name="ButtonRefresh" 
                Click="ButtonRefresh_Click">Refresh</Button>
            <Button Width="225"  Name="ButtonCompleteAll" 
                Click="ButtonCompleteAll_Click">Complete All</Button>
        </StackPanel>

    這會將新按鈕新增至頁面。 

2. 開啟 MainPage.xaml.cs 程式碼檔案，並新增下列類別定義程式碼：

        public class MarkAllResult
        {
            public int Count { get; set; }
        }

    此類別是用來保留自訂 API 傳回的資料列計數值。 

3. 找出 **RefreshTodoItems** 方法中的 **MainPage** 類別，並請確定 `query` 定義，使用下列 **其中** 方法 ︰

        .Where(todoItem => todoItem.Complete == false)

    這會篩選項目，如此一來，查詢就不會傳回已完成的項目。

3. 在 **MainPage** 類別中，新增下列方法 ︰

        private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
        {
            string message;
            try
            {
                // Asynchronously call the custom API using the POST method. 
                var result = await App.MobileService
                    .InvokeApiAsync<MarkAllResult>("completeAll", 
                    System.Net.Http.HttpMethod.Post, null);
                message =  result.Count + " item(s) marked as complete.";
                RefreshTodoItems();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;                
            }
        
            MessageBox.Show(message);  
        }

    此方法會處理 **按一下** 新按鈕的事件。  **InvokeApiAsync** 方法會在用戶端將要求傳送給新的自訂 API 呼叫。 自訂 API 傳回的結果會顯示在訊息對話方塊中。

## <a name="test-app"></a>測試應用程式

1. 在 Visual Studio 中按 **F5** 鍵，以重建專案並啟動應用程式。

2. 在應用程式中，輸入某些文字 **Insert a TodoItem**, ，然後點選 [ **儲存**。

3. 重複前一個步驟，直到將數個 Todo 項目新增至清單為止。

4. 點選 **完成所有** ] 按鈕。

    ![](./media/mobile-services-windows-phone-call-custom-api/mobile-custom-api-windows-phone-completed.png)

    出現訊息方塊，指出標示為完成的項目數，並重新執行篩選查詢，以便清除清單的所有項目。

