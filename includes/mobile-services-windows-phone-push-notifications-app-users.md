
接下來，您必須變更推播通知註冊的方法，以確認使用者在嘗試註冊前已經驗證。 

1. 在方案總管] 中的 Visual Studio 中開啟 app.xaml.cs 專案檔案並在 **Application_Launching** 事件處理常式註解化或刪除呼叫 **AcquirePushChannel** 方法。 
 
2. 變更的存取範圍 **AcquirePushChannel** 方法從 `private` 到 `public` ，並新增 `static` 修飾詞。 

3. 開啟 MainPage.xaml.cs 專案檔案，並取代 **OnNavigatedTo** 方法覆寫，取代為下列:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }
