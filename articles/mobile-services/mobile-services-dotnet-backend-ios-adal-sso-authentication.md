<properties
    pageTitle="使用 Active Directory Authentication Library 單一登入驗證您的應用程式 (iOS) | Microsoft Azure"
    description="了解如何在 iOS 應用程式中驗證使用者的單一登入。"
    documentationCenter="ios"
    authors="mattchenderson"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="mahender"/>

# 使用 Active Directory Authentication Library 單一登入驗證您的應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##概觀

在本教學課程中，您會使用 Active Directory Authentication Library 將驗證新增至快速入門專案。

要能夠驗證使用者，您必須向 Azure Active Directory (AAD) 註冊您的應用程式。 此作業有兩個階段。 第一，您必須註冊行動服務，並公開其權限。 第二，您必須註冊 iOS 應用程式，並為其授與對這些權限的存取權


>[AZURE.NOTE] 本教學課程旨在協助您深入了解行動服務如何讓您執行單一登入 Azure Active Directory 驗證 iOS 應用程式。 如果這是您第一次接觸行動服務，來完成教學課程 [開始使用行動服務]。


##先決條件


本教學課程需要下列各項：

* XCode 4.5 和 iOS 6.0 (或更新版本)
* 完成 [開始使用行動服務] 教學課程。
* 完成 [註冊您的應用程式使用 Azure Active Directory 帳戶登入]
* Microsoft Azure 行動服務 SDK
* [Active Directory Authentication Library for iOS]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##將行動服務設定為需要驗證

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##將驗證程式碼新增至用戶端應用程式

1. 下載 [Active Directory Authentication Library for iOS]，並將它包含在您的專案。 請務必同時從 ADAL 來源新增腳本。

2. 在 QSTodoListViewController 中，使用下列程式碼納入 ADAL：

        #import "ADALiOS/ADAuthenticationContext.h"

2. 然後，新增下列方法：

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }


6. 中的程式碼 `loginAndGetData` 方法，取代 **授權單位-INSERT-CLIENT-ID-HERE** 替換為您佈建您的應用程式的租用戶名稱，格式應為 https://login.windows.net/tenant-name.onmicrosoft.com。 此值可從 [Azure 傳統入口網站] 複製到 Azure Active Directory 的 [網域] 索引標籤以外。

7. 在程式碼中的 `loginAndGetData` 方法，取代 **插入資源 URI-這裡** 與 **應用程式識別碼 URI** 行動服務。 如果您依照 [如何向 Azure Active Directory] 主題您的應用程式識別碼 URI 應類似於 https://todolist.azure-mobile.net/login/aad。

8. 在程式碼中的 `loginAndGetData` 方法，取代 **-用戶端-INSERT-CLIENT-ID-HERE** 您從原生用戶端應用程式中複製的用戶端識別碼。

9. 在程式碼中的 `loginAndGetData` 方法，取代 **插入重新導向 URI-這裡** 與登入/完成您的行動服務的端點。 此項目應與 https://todolist.azure-mobile.net/login/done 類似。


3. 在 QSTodoListViewController 中，使用以下內容取代 `[self refresh]`，以修改 `ViewDidLoad`：

        [self loginAndGetData];

##使用驗證測試用戶端

1. 從 [產品] 功能表中按一下 [執行]，以啟動應用程式
2. 您會看見登入 Azure Active Directory 的提示。
3. 應用程式會驗證並傳回 todo 項目。

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Register your apps to use an Azure Active Directory Account login]:mobile-services-how-to-register-active-directory-authentication.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure classic portal]: https://manage.windowsazure.com/
[Active Directory Authentication Library for iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios

