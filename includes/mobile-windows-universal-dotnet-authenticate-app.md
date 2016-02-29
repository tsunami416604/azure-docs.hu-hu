
1. 開啟共用專案檔案 MainPage.cs，然後將下列程式碼片段新增至 MainPage 類別：
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    此程式碼會使用 Facebook 登入來驗證使用者。 如果您使用除了 Facebook 以外的識別提供者，將值變更 **MobileServiceAuthenticationProvider** 上為您的提供者的值。

3. 註解化或刪除呼叫 **RefreshTodoItems** 方法中的現有 **OnNavigatedTo** 方法覆寫。

    如此能避免在使用者通過驗證之前載入資料。 接下來，您將加入 **登入** ] 按鈕，就會觸發驗證的應用程式。

4. 將下列程式碼片段新增至 MainPage 類別：

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. 在 Windows 市集應用程式專案中，開啟 MainPage.xaml 專案檔案並新增下列 **按鈕** 項目定義的元素前方 **儲存** 按鈕:

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. 在 Windows Phone 市集應用程式專案中，新增下列 **按鈕** 中的項目 **ContentPanel**, 之後， **文字方塊** 項目:

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. 開啟共用的 App.xaml.cs 專案檔案，並新增下列程式碼：

        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

    如果 **OnActivated** 方法已存在，只要新增 `#if...#endif` 程式碼區塊。

9. 按 F5 鍵執行 Windows 市集應用程式，請按一下 [ **登入** 按鈕，然後登入應用程式與您選擇的身分識別提供者。 

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢後端並更新資料。

10. 以滑鼠右鍵按一下 Windows Phone 市集應用程式專案中，按一下 **設定為啟始專案**, ，然後重複上述步驟來驗證，Windows Phone 市集應用程式也能正常運作。  

 
