
在先前範例中，每次應用程式啟動時，都會連繫識別提供者和行動服務。 相反地，您可以快取授權權杖，並嘗試優先使用它。

* 建議在 iOS 用戶端上用來加密和儲存驗證權杖的方法是使用 iOS Keychain。 我們將使用 [SSKeychain](https://github.com/soffes/sskeychain) -iOS Keychain 的簡單包裝函式。 依照 [SSKeychain] 頁面上的指示，將該包裝函式加入至您的專案。 確認 **啟用模組** 在專案的啟用設定 **建置設定** (區段 **Apple LLVM-語言-模組**。)

* 開啟 **QSTodoListViewController.m** 並新增下列程式碼 ︰

```
        - (void) saveAuthInfo {
                [SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
        }


        - (void)loadAuthInfo {
                NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
            if (userid) {
                NSLog(@"userid: %@", userid);
                self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
                 self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

            }
        }
```

* 在 `loginAndGetData`, ，修改  `loginWithProvider:controller:animated:completion:`的 completion 區塊。 在 `[self refresh]` 前面加入下列這一行，以儲存使用者識別碼和權杖屬性：

```
                [self saveAuthInfo];
```

* 讓我們在應用程式啟動時載入使用者識別碼和權杖。 在 `viewDidLoad` 中 **QSTodoListViewController.m**, 之後, 加入這`self.todoService` 初始化。

```
                [self loadAuthInfo];
```


