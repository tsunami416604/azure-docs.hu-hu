<properties
    pageTitle="使用 Active Directory Authentication Library 單一登入驗證您的應用程式 (Windows 市集) | Microsoft Azure"
    description="了解如何在 Windows 市集應用程式中驗證使用者的單一登入。"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/18/2015"
    ms.author="wesmc"/>

# 使用 Active Directory Authentication Library 單一登入驗證您的應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##概觀

在本教學課程中，您必須將驗證加入快速入門專案，以支援使用 Active Directory Authentication Library [用戶端主導登入作業](http://msdn.microsoft.com/library/azure/jj710106.aspx) 與 Azure Active Directory。 若要支援 [服務主導登入作業](http://msdn.microsoft.com/library/azure/dn283952.aspx) 與 Azure Active Directory，開始 [將驗證新增至行動服務應用程式](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md) 教學課程。

要能夠驗證使用者，您必須向 Azure Active Directory (AAD) 註冊您的應用程式。 此作業有兩個階段。 第一，您必須註冊行動服務，並公開其權限。 第二，您必須註冊 Windows 市集應用程式，並為其授與對這些權限的存取權


>[AZURE.NOTE] 本教學課程旨在協助您深入了解行動服務如何讓您執行單一登入 Azure Active Directory 驗證 Windows 市集應用程式使用 [用戶端主導登入作業](http://msdn.microsoft.com/library/azure/jj710106.aspx)。 如果這是您第一次接觸行動服務，來完成教學課程 [開始使用行動服務]。


##先決條件

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 完成 [開始使用行動服務] 教學課程。
* Microsoft Azure 行動服務 SDK NuGet 封裝
* Active Directory Authentication Library NuGet 封裝

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

##向 Azure Active Directory 註冊您的應用程式

若要向 Azure Active Directory 註冊應用程式，您必須將它與 Windows 市集產生關聯，並具有該應用程式的封裝安全性識別碼 (SID)。 封裝 SID 可透過 Azure Active Directory 中的原生應用程式設定進行註冊。


###將應用程式與新的市集應用程式名稱產生關聯

1. 在 Visual Studio 中，以滑鼠右鍵按一下用戶端應用程式專案，並按一下 [ **存放區** 和 **將應用程式與存放區**

    ![][1]

2. 登入您的開發人員中心帳戶。

3. 輸入您想要保留的應用程式，並按一下 [應用程式名稱 **保留**。

    ![][2]

4. 選取新的應用程式名稱，然後按一下 **下一步**。

5. 按一下 [ **關聯** 若要將應用程式與市集名稱產生關聯。


###擷取應用程式的封裝 SID。

現在，您必須擷取將使用原生應用程式設定進行設定的封裝 SID。

1. 登入 [Windows 開發人員中心儀表板] 並按一下 **編輯** 應用程式。

    ![][3]

2. 然後按一下 [ **服務**

    ![][4]

3. 然後按一下 [ **Live 服務網站**。

    ![][5]

4. 從頁面頂端複製您的封裝 SID。

    ![][6]

###建立原生應用程式註冊

1. 瀏覽至 **Active Directory** [Azure 傳統入口網站]，然後按一下您的目錄。

    ![][7]

2. 按一下 [ **應用程式** ] 索引標籤的頂端，然後按一下以 **新增** 應用程式。

    ![][8]

3. 按一下 [ **加入我的組織正在開發的應用程式**。

4. 在 [新增應用程式精靈] 中，輸入 **名稱** 您的應用程式，然後按一下 [  **原生用戶端應用程式** 型別。 接著，按一下以繼續。

    ![][9]

5. 在 **重新導向 URI** 方塊中，貼上的應用程式封裝 SID 您稍早複製然後按一下以完成原生應用程式註冊。

    ![][10]

6. 按一下 [ **設定** 索引標籤，原生應用程式並複製 **用戶端識別碼**。 稍後您將會需要此資訊。

    ![][11]

7. 將頁面向下的捲動 **其他應用程式的權限** 區段，並授與您稍早註冊的行動服務應用程式的完整存取權。 然後按一下 [ **儲存**

    ![][12]

已在 AAD 中設定您的行動服務接收來自應用程式的單一登入。



##將行動服務設定為需要驗證

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##將驗證程式碼新增至用戶端應用程式

1. 在 Visual Studio 中開啟您的 Windows 市集用戶端應用程式專案。

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. 在 Visual Studio 的 [方案總管] 視窗中開啟 MainPage.xaml.cs 檔案，然後使用陳述式新增下列項目。

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. 將下列程式碼新增至 MainPage 類別，以宣告 `AuthenticateAsync` 方法。

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>";
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

6. 中的程式碼 `AuthenticateAsync` 方法，取代 **授權單位-INSERT-CLIENT-ID-HERE** 替換為您佈建您的應用程式的租用戶名稱，格式應為 https://login.windows.net/tenant-name.onmicrosoft.com。 此值可從 [Azure 傳統入口網站] 複製到 Azure Active Directory 的 [網域] 索引標籤以外。

7. 在程式碼中的 `AuthenticateAsync` 方法，取代 **插入資源 URI-這裡** 與 **應用程式識別碼 URI** 行動服務。 如果您依照 [如何向 Azure Active Directory] 主題您的應用程式識別碼 URI 應類似於 https://todolist.azure-mobile.net/login/aad。

8. 在程式碼中的 `AuthenticateAsync` 方法，取代 **-用戶端-INSERT-CLIENT-ID-HERE** 您從原生用戶端應用程式中複製的用戶端識別碼。

9. 在 Visual Studio 的 [方案總管] 視窗中，開啟用戶端專案中的 Package.appxmanifest 檔案。 按一下 [ **功能** ] 索引標籤上，並啟用 **企業應用程式** 和 **私人網路 (用戶端和伺服器)**。 儲存檔案。

    ![][14]

10. 在 MainPage.cs 檔案中更新 `OnNavigatedTo` 事件處理常式，以呼叫 `AuthenticateAsync` 方法，如下所示。

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            await RefreshTodoItems();
        }


##使用驗證測試用戶端

1. 在 Visual Studio 中，執行用戶端應用程式。
2. 您會看見登入 Azure Active Directory 的提示。
3. 應用程式會驗證並傳回 todo 項目。

    ![][15]




<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-app-run.png

<!-- URLs. -->
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure classic portal]: https://manage.windowsazure.com/
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Windows Dev Center Dashboard]: http://go.microsoft.com/fwlink/p/?LinkID=266734
