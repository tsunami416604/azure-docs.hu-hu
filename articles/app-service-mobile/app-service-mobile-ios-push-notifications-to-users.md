<properties 
    pageTitle="傳送跨平台通知給 iOS 中的特定使用者" 
    description="了解如何將推播通知傳送至特定使用者的所有裝置。"
    services="app-service\mobile,notification-hubs" 
    documentationCenter="ios" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-mobile" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-dotnet" 
    ms.devlang="objective-c" 
    ms.topic="article" 
    ms.date="11/17/2015"
    ms.author="yuaxu"/>

# 傳送跨平台通知給特定使用者

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何從您的行動後端，將通知傳送給特定使用者的所有註冊裝置。 它引進了 [範本]，可讓用戶端應用程式能夠指定裝載格式和變數預留位置在註冊的概念。 然後，這些預留位置會傳送到每個平台，而啟用跨平台通知。

> [AZURE.NOTE] 若要取得使用跨平台的用戶端推入，您必須完成本教學課程中的您想要啟用每個平台。 您只需要執行 [行動後端更新](#backend) 共用相同行動後端的用戶端一次。
 
##必要條件 

在開始本教學課程之前，您必須已為您要啟用的每個用戶端平台完成下列應用程式服務教學課程：

+ [開始使用驗證]<br/>將登入需求加入至 TodoList 範例應用程式。

+ [開始使用推播通知]<br/>設定推播通知的 TodoList 範例應用程式。

##<a name="client"></a>更新您的用戶端，以註冊處理跨平台推播所需的範本

1. 將 Apn 註冊程式碼片段中移動 **QSAppDelegate.m**的 **: Didfinishlaunchingwithoptions<** 呼叫 **loginWithProvider** 中 **QSTodoListViewController.m** 使其在驗證完成後:

        [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
            [self refresh];
            
            // register iOS8 or previous devices for notifications
            if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            }
            else {
                // Register for remote notifications
                [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                 UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            }
        }];

2. 取代您 **registerDeviceToken** 呼叫 **application: didRegisterForRemoteNotificationsWithDeviceToken** 取代下列以使用範本:

        NSDictionary *templates = @{
                               @"testNotificationTemplate": @{ @"body" : @{ @"aps" : @{ @"alert": @"$(message)" } } }
                               };
    
        // register with templates
        [client.push registerDeviceToken:deviceToken template:templates completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

    註冊推播通知之前請務必驗證使用者。 當驗證的使用者註冊推播通知之後，便會自動新增包含使用者識別碼的標記。

##<a name="backend"></a>更新您的服務後端以傳送通知給特定使用者

[AZURE.INCLUDE [app-service-mobile-push-notifications-to-users](../../includes/app-service-mobile-push-notifications-to-users.md)]

##<a name="test"></a>測試應用程式

重新發佈您的行動後端專案，並執行任何已設定的用戶端應用程式。 在插入項目時，後端將會傳送通知給使用者已登入的所有用戶端應用程式。

<!-- URLs. -->
[Get started with authentication]: app-service-mobile-ios-get-started-users.md
[Get started with push notifications]: app-service-mobile-ios-get-started-push.md
[templates]: https://msdn.microsoft.com/library/dn530748.aspx
 
