<properties
    pageTitle="開始使用適用於 iOS app 的 Azure 通知中樞 | Microsoft Azure"
    description="在本教學課程中，您將了解如何使用 Azure 通知中樞，將通知推播至 iOS 應用程式。"
    services="notification-hubs"
    documentationCenter="ios"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="12/15/2015"
    ms.author="wesmc"/>


# 開始使用適用於 iOS app 的通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## 概觀

本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式。 您將使用 Apple 推播通知服務 (APN)，建立可接收推播通知的空白 iOS app。 完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您 app 的裝置。

本教學課程示範使用通知中樞的簡單廣播案例。


## 開始之前

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

完成本教學課程中的程式碼，請參閱 GitHub [這裡](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted)。

## 必要條件

本教學課程需要下列各項：

+ [行動服務 iOS SDK 1.2.4 版]
+ [Xcode 7 ][install xcode]
+ 支援 iOS 8 (或更新版本) 的裝置
+ iOS Developer Program 成員資格
   > [AZURE.NOTE] 基於推播通知的組態需求，您必須在使用 iOS 系統的裝置 (iPhone 或 iPad)，而不是在 iOS 模擬器上部署和測試推播通知。

完成本教學課程是參加 iOS app 所有其他通知中樞教學課程的先決條件。
> [AZURE.NOTE] 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)。

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## 設定您的通知中樞

本節將引導您利用所建立的 **.p12** 推播憑證，建立新的通知中樞，並設定搭配 APNS 進行驗證。 如果您想要使用已經建立的通知中樞，可以跳至步驟 5。

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">
<li>
<p>按一下 <b>設定</b> 在頂端，索引標籤，然後按一下 <b>上傳</b> 中上傳的憑證指紋 Apple 通知設定] 按鈕。然後選取 <b>.p12</b> 您稍早匯出的憑證和憑證的密碼。</p>
<p>請務必選取 <b>沙箱</b> 由於這是開發模式。如果沒有其他服務使用資料庫或伺服器，請只使用 <b>生產環境</b> 如果您想要將推播通知傳送給使用者購買您的應用程式，從存放區。</p>
</li>
</ol>
& emsp; & emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-upload-cert.png)

& emsp; & emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png)



現在已將您的通知中樞設定成使用 APNS，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。

## 將您的應用程式連接到通知中樞

1. 在 Xcode 中建立新的 iOS 專案，並選取 [單一檢視應用程式]**** 範本。

    ![][8]

2. 設定新專案的選項時，請務必使用您先前在 Apple 開發人員入口網站上設定套件組合識別碼時使用的相同**產品名稱**和**組織識別碼**。

    ![][11]

3. 在 [目標]**** 下按一下您的專案名稱，然後按一下 [組建設定]**** 索引標籤並展開 [程式碼簽署身分識別]****，接著在 [偵錯]**** 下設定您的程式碼簽署身分識別。 將 [層級]**** 從 [基本]**** 切換成 [全部]****，然後將 [佈建設定檔]**** 設為您先前建立的佈建設定檔。

    如果畫面未顯示您在 Xcode 中建立的新佈建設定檔，請嘗試重新整理簽署身分識別的設定檔。 按一下功能表列上的 [Xcode]****，再依序按一下 [喜好設定]****、[帳號]**** 索引標籤、[檢視詳細資料]**** 按鈕、您的簽署身分識別，然後按一下右下角的 [重新整理] 按鈕。

    ![][9]

4. 下載的 [行動服務 iOS SDK 1.2.4 版]，並將檔案解壓縮。 在 Xcode 中，以滑鼠右鍵按一下您的專案，然後按一下 [新增檔案至]**** 選項，將 **WindowsAzureMessaging.framework** 資料夾加入至 Xcode 專案。 選取 [必要時複製項目]****，然後按一下 [新增]****。
    >[AZURE.NOTE] 通知中樞 SDK 目前不支援 Xcode 7 上的 Bitcode。 您必須在專案的 [建置選項]**** 中將 [啟用 Bitcode]**** 設定為 [否]****。

    ![][10]

5. 將新的標頭檔新增至名為 **HubInfo.h** 的專案。 這個檔案會保留通知中心的常數。 新增下列定義，然後以您的*中樞名稱*以及先前記下的 *DefaultListenSharedAccessSignature* 取代字串常值預留位置。

     #ifndef HubInfo_h
     #define HubInfo_h
    
         #define HUBNAME @"<Enter the name of your hub>"
         #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
    
     #endif /* HubInfo_h */

6. 開啟 AppDelegate.h 檔案並新增下列 import 指示詞：

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"

7. 在 AppDelegate.m 檔案中，新增下列程式碼中的 `didFinishLaunchingWithOptions` 方法會根據您的 iOS 版本。 此程式碼會向 APN 註冊裝置控制代碼：

 對於 iOS 8：

     UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                             UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
    
     [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
     [[UIApplication sharedApplication] registerForRemoteNotifications];

 iOS 8 之前的版本：

      [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

8. 在相同檔案中新增下列方法。 此程式碼會使用您在 HubInfo.h 中指定的連接資訊連接到通知中心。 然後，它可提供裝置權杖給通知中樞，讓通知中樞能夠傳送通知：

     - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
         SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                     notificationHubPath:HUBNAME];
    
         [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
             if (error != nil) {
                 NSLog(@"Error registering for notifications: %@", error);
             }
             else {
                 [self MessageBox:@"Registration Status" message:@"Registered"];
             }
         }];
     }
    
     -(void)MessageBox:(NSString *)title message:(NSString *)messageText
     {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
             cancelButtonTitle:@"OK" otherButtonTitles: nil];
         [alert show];
     }

9. 如果應用程式在作用中時收到通知，您可以在相同檔案中新增下列方法以顯示 **UIAlert**：

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. 在裝置上建置並執行 app，以確認 app 能夠順利運作。

## 傳送通知

您可以測試應用程式中接收通知，如下列畫面所示，[Azure 傳統入口網站] 透過通知中心上的 [偵錯] 索引標籤，以傳送通知。

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]



## (選擇性) 從 App 傳送通知

如果您想在 App 內傳送通知。 本節提供使用 REST 介面執行此操作的範例。

1. 在 XCode 中，開啟 Main.storyboard 並從物件程式庫加入下列 UI 元件，以允許使用者在 app 中傳送推播通知。

    - 沒有標籤文字的標籤。 這將用來回報傳送通知時發生的錯誤。 **Lines** 屬性應該設為 **0**，如此才會自動根據左右邊界和檢視頂端的限制來調整大小。
    - 具有**預留位置**文字的文字欄位會設為**輸入通知訊息**。 將欄位限制在標籤的下方，如下所示。 將檢視控制器設為輸出委派。
    - 標題為**傳送通知**的按鈕會限制在文字欄位的下方，並在水平置中的位置。

    檢視應如下所示：

    ![][32]

2. [新增輸出](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) 標籤和文字欄位已連接您的檢視，並更新您 `介面` 定義，以支援 `設定為 UITextFieldDelegate` 和 `NSXMLParserDelegate`。 新增以下所示的三個屬性宣告，以協助呼叫 REST API 及剖析回應。

 您的 ViewController.h 檔案應該類似下列內容：

     #import <UIKit/UIKit.h>
    
     @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
     {
         NSXMLParser *xmlParser;
     }
    
     // Make sure these outlets are connected to your UI by ctrl+dragging
     @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
     @property (weak, nonatomic) IBOutlet UILabel *sendResults;
    
     @property (copy, nonatomic) NSString *statusResult;
     @property (copy, nonatomic) NSString *currentElement;
    
     @end

3. 開啟 HubInfo.h 並新增下列常數，它們將用於傳送通知給您的中樞。 使用實際的 *DefaultFullSharedAccessSignature* 連接字串，取代預留位置字串常值。

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. 新增下列 `#import` 陳述式，以您的 ViewController.h 檔案。

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. 在 ViewController.m 中，將下列程式碼新增至介面實作。 此程式碼將會剖析您的 *DefaultFullSharedAccessSignature* 連接字串。 中所述 [REST API 參考](http://msdn.microsoft.com/library/azure/dn495627.aspx), ，已剖析的資訊會用來產生的 SaS 權杖 **授權** 要求標頭。

     NSString *HubEndpoint;
     NSString *HubSasKeyName;
     NSString *HubSasKeyValue;
    
     -(void)ParseConnectionString
     {
         NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
         NSString *part;
    
         if ([parts count] != 3)
         {
             NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                 reason:@"Invalid full shared access connection string" userInfo:nil];
    
             @throw parseException;
         }
    
         for (part in parts)
         {
             if ([part hasPrefix:@"Endpoint"])
             {
                 HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
             }
             else if ([part hasPrefix:@"SharedAccessKeyName"])
             {
                 HubSasKeyName = [part substringFromIndex:20];
             }
             else if ([part hasPrefix:@"SharedAccessKey"])
             {
                 HubSasKeyValue = [part substringFromIndex:16];
             }
         }
     }

6. 在 ViewController.m 中，更新 `viewDidLoad` 方法來檢視載入剖析連接字串。 也將公用程式方法新增至介面實作，如下所示。

     - (void)viewDidLoad
     {
         [super viewDidLoad];
         [self ParseConnectionString];
         [_notificationMessage setDelegate:self];
     }
    
     -(NSString *)CF_URLEncodedString:(NSString *)inputString
     {
        return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
             NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
     }
    
     -(void)MessageBox:(NSString *)title message:(NSString *)messageText
     {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
             cancelButtonTitle:@"OK" otherButtonTitles: nil];
         [alert show];
     }

7. 在 ViewController.m 中，將下列程式碼加入至介面實作，以產生將在提供的 SaS 授權權杖 **授權** 標頭中所述 [REST API 參考](http://msdn.microsoft.com/library/azure/dn495627.aspx)。

     -(NSString*) generateSasToken:(NSString*)uri
     {
         NSString *targetUri;
         NSString* utf8LowercasedUri = NULL;
         NSString *signature = NULL;
         NSString *token = NULL;
    
         @try
         {
             // Add expiration
             uri = [uri lowercaseString];
             utf8LowercasedUri = [self CF_URLEncodedString:uri];
             targetUri = [utf8LowercasedUri lowercaseString];
             NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
             int expiresInMins = 60; // 1 hour
             expiresOnDate += expiresInMins * 60;
             UInt64 expires = trunc(expiresOnDate);
             NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];
    
             // Get an hmac_sha1 Mac instance and initialize with the signing key
             const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
             const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
             unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
             CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
             NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
             signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];
    
             // Construct authorization token string
             token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                 signature, expires, HubSasKeyName, targetUri];
         }
         @catch (NSException *exception)
         {
             [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
         }
         @finally
         {
             if (utf8LowercasedUri != NULL)
                 CFRelease((CFStringRef)utf8LowercasedUri);
             if (signature != NULL)
             CFRelease((CFStringRef)signature);
         }
    
         return token;
     }

8. Ctrl+從 [傳送通知]**** 按鈕拖曳至 ViewController.m，為 **Touch Down** 新增名為 **SendNotificationMessage** 的動作。 以下列程式碼更新方法，來使用 REST API 通知傳送。

     - (IBAction)SendNotificationMessage:(id)sender
     {
         self.sendResults.text = @"";
         [self SendNotificationRESTAPI];
     }
    
     - (void)SendNotificationRESTAPI
     {
         NSURLSession* session = [NSURLSession
                          sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                          delegate:nil delegateQueue:nil];
    
         // Apple Notification format of the notification message
         NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                             self.notificationMessage.text];
    
         // Construct the message's REST endpoint
         NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                             HUBNAME, API_VERSION]];
    
         // Generate the token to be used in the authorization header
         NSString* authorizationToken = [self generateSasToken:[url absoluteString]];
    
         //Create the request to add the APNs notification message to the hub
         NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
         [request setHTTPMethod:@"POST"];
         [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
    
         // Signify Apple notification format
         [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];
    
         //Authenticate the notification message POST request with the SaS token
         [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];
    
         //Add the notification message body
         [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    
         // Send the REST request
         NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
             completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
         {
             NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
             if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
             {
                 NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
             }
             if (data != NULL)
             {
                 xmlParser = [[NSXMLParser alloc] initWithData:data];
                 [xmlParser setDelegate:self];
                 [xmlParser parse];
             }
         }];
         [dataTask resume];
     }

9. 在 ViewController.m 中，加入下列委派方法，以支援關閉文字欄位的鍵盤。 按住 Ctrl 並從文字欄位拖曳至介面設計工具中的檢視控制器圖示，以將檢視控制器設為輸出委派。

     //===[ Implement UITextFieldDelegate methods ]===
    
     -(BOOL)textFieldShouldReturn:(UITextField *)textField
     {
         [textField resignFirstResponder];
         return YES;
     }

10. 在 ViewController.m 中，加入下列委派方法，以支援使用剖析回應 `NSXMLParser`。

    //===[ Implement NSXMLParserDelegate methods ]===
    
    -(void)parserDidStartDocument:(NSXMLParser *)parser
    {
        self.statusResult = @"";
    }
    
    -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
        namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
        attributes:(NSDictionary *)attributeDict
    {
        NSString * element = [elementName lowercaseString];
        NSLog(@"*** New element parsed : %@ ***",element);
    
        if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
        {
            self.currentElement = element;
        }
    }
    
    -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
    {
        self.statusResult = [self.statusResult stringByAppendingString:
            [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
    }
    
    -(void)parserDidEndDocument:(NSXMLParser *)parser
    {
        // Set the status label text on the UI thread
        dispatch_async(dispatch_get_main_queue(),
        ^{
            [self.sendResults setText:self.statusResult];
        });
    }

11. 建置專案並確認一切正確無誤。


> [AZURE.NOTE] 如果您在 Xcode7 中遇到有關 Bitcode 支援的建置錯誤，您應該在 Xcode 中將 [建置設定] -> [啟用 Bitcode] (ENABLE_BITCODE) 變更為 'NO'。 通知中樞 SDK 目前不支援 Bitcode。 

您可以在 [本機和推播通知程式設計指南] Apple 找到所有可能的通知裝載。


## 測試應用程式

若要在 iOS 上測試推播通知，您必須將 app 部署至裝置。 您無法利用 iOS 模擬器傳送 Apple 推播通知。

1. 執行應用程式並確認註冊成功，然後按下 [確定]****。

    ![][33]

2. 您可以從 [Azure 傳統入口網站] 來傳送測試通知。 如果您已在 App 中加入傳送通知的程式碼，在文字欄位中觸控以輸入通知訊息。 接著，按下鍵盤上的 [傳送]**** 按鈕或檢視中的 [傳送通知]**** 按鈕，以傳送通知訊息。

    ![][34]

3. 此通知會傳送至所有已註冊要接收通知的所有裝置。

    ![][35]

如果您有任何問題或為所有讀者改善本教學課程的建議，請在下方的 Disqus 一節為我們留下註解。


## 後續步驟

在此簡單範例中，您廣播通知到您的所有 iOS 裝置。 我們建議您繼續學習中的下一個步驟的 [使用通知中心來推播通知給使用者] 教學課程。 該教學課程將逐步引導您使用標記，建立後端以傳送通知。

如果您想要按興趣群組分隔使用者，您可以另外移至 [使用通知中心傳送即時新聞]。

如需一般通知中樞的詳細資訊，請參閱 [通知中心指引]。







[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png 
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png 
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png 
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png 
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png 
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png 
[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png 
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png 
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png 
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png 
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png 
[mobile services ios sdk version 1.2.4]: http://aka.ms/kymw2g 
[mobile services ios sdk]: http://go.microsoft.com/fwLink/?LinkID=266533 
[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[my applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[live sdk for windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253 
[get started with mobile services]: /develop/mobile/tutorials/get-started-ios 
[azure classic portal]: https://manage.windowsazure.com/ 
[notification hubs guidance]: http://msdn.microsoft.com/library/jj927170.aspx 
[install xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532 
[ios provisioning portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456 
[get started with push notifications in mobile services]: ../mobile-services-javascript-backend-ios-get-started-push.md 
[use notification hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-notify-users.md 
[use notification hubs to send breaking news]: notification-hubs-ios-send-breaking-news.md 
[local and push notification programming guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1 

