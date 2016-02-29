1. 在 Mac 上，開啟 **QSTodoListViewController.m** 在 Xcode 中，並新增下列方法。 變更 _facebook_ 至 _microsoftaccount_, ，_twitter_, ，_google_, ，或 _windowsazureactivedirectory_ 如果您不使用 Facebook 作為身分識別提供者。

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

2. 使用下列程式碼取代 `viewDidLoad` 中的 `[self refresh]`：

        [self loginAndGetData];

3. 按下  **執行** 以啟動應用程式，然後登入。 當您登入時，應該能夠檢視待辦事項清單並進行更新。

