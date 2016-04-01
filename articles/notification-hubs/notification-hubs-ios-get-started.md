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

##概觀

本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式。 您將使用 Apple 推播通知服務 (APN)，建立可接收推播通知的空白 iOS app。 完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您 app 的裝置。

本教學課程示範使用通知中樞的簡單廣播案例。


## 開始之前

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

完成本教學課程中的程式碼可以在 GitHub 上找到 [這裡](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted)。 

##必要條件

本教學課程需要下列各項：

+ [行動服務 iOS SDK 1.2.4 版]
+ [Xcode 7][Install Xcode]
+ 支援 iOS 8 (或更新版本) 的裝置
+ iOS Developer Program 成員資格

   > [AZURE.NOTE] 基於推播通知組態需求，您必須部署和測試推播通知在 iOS 功能裝置 （iPhone 或 iPad） 而不是在 iOS 模擬器。

完成本教學課程是參加 iOS app 所有其他通知中樞教學課程的先決條件。

> [AZURE.NOTE] 若要完成本教學課程，您必須具有有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)。

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##設定您的通知中樞

本節將引導您建立新的通知中心，並使用 APNS 設定驗證 **.p12** 您所建立的推播憑證。 如果您想要使用已經建立的通知中樞，可以跳至步驟 5。

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">
<li>
<p>按一下 [ <b>設定</b> 的頂端，索引標籤，然後按一下 <b>上載</b> 中上傳的憑證指紋 Apple 通知設定] 按鈕。 然後選取 <b>.p12</b> 您稍早匯出的憑證和憑證的密碼。</p>
<p>請務必選取 <b>沙箱</b> 模式，因為這是進行開發。 只使用 <b>生產</b> 如果您想要將推播通知傳送給使用者購買您的應用程式，從存放區。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-upload-cert.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png)



現在已將您的通知中樞設定成使用 APNS，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。

##將您的應用程式連接到通知中樞

1. 在 Xcode 中，建立新的 iOS 專案，並選取 **Single View Application** 範本。

    ![][8]

2. 當設定為新專案的選項，請務必使用相同 **產品名稱** 和 **組織識別碼** 您先前設定的 Apple 開發人員入口網站的套件組合識別碼時使用。

    ![][11]

3. 在 **目標**, ，按一下您的專案名稱，按一下 **建置設定** ] 索引標籤上，展開 [ **程式碼簽署識別**, ，然後在 **偵錯**, ，設定您的程式碼簽署身分識別。 切換 **層級** 從 **基本** 至 **所有**, ，並設定 **佈建設定檔** 您先前建立的佈建設定檔。

    如果畫面未顯示您在 Xcode 中建立的新佈建設定檔，請嘗試重新整理簽署身分識別的設定檔。 按一下 [ **Xcode** 功能表列上，按一下 [ **喜好設定**, ，按一下 [ **帳戶** 索引標籤上，按一下 [ **檢視詳細資料** 按鈕、 按一下您的簽署身分識別，然後按一下右下角的 [重新整理] 按鈕。

    ![][9]

4. 下載 [Mobile Services iOS SDK version 1.2.4] 並解壓縮檔案。 在 Xcode 中，以滑鼠右鍵按一下您的專案，然後按一下 **將檔案新增至** 選項來加入 **WindowsAzureMessaging.framework** Xcode 專案的資料夾。 選取 **複製的項目，如有需要**, ，然後按一下 [ **新增**。

    >[AZURE.NOTE] 通知中心 SDK 目前不支援 bitcode Xcode 7。  您必須設定 **啟用 Bitcode** 至 **否** 中 **建置選項** 為您的專案。

    ![][10]

5. 名為專案中加入新的標頭檔 **HubInfo.h**。 這個檔案會保留通知中心的常數。  新增下列定義，並取代字串常值預留位置取代您 *中樞名稱* 和 *DefaultListenSharedAccessSignature* 您先前記下。

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. 開啟 AppDelegate.h 檔案並新增下列 import 指示詞：

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. 根據您的 iOS 版本，在 AppDelegate.m 檔案的 `didFinishLaunchingWithOptions` 方法中新增下列程式碼。 此程式碼會向 APN 註冊裝置控制代碼：

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


9. 在相同檔案中，新增下列方法，以顯示 **UIAlert** 如果應用程式正在作用中時收到通知 ︰


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. 在裝置上建置並執行 app，以確認 app 能夠順利運作。

## 傳送通知


您可以測試應用程式中接收通知，傳送通知給以 [Azure Classic Portal] 透過通知中心，如下列畫面所示的 [偵錯] 索引標籤。

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]



## (選擇性) 從 App 傳送通知

如果您想在 App 內傳送通知。 本節提供使用 REST 介面執行此操作的範例。

1. 在 XCode 中，開啟 Main.storyboard 並從物件程式庫加入下列 UI 元件，以允許使用者在 app 中傳送推播通知。

    - 沒有標籤文字的標籤。 這將用來回報傳送通知時發生的錯誤。  **行** 屬性應該設為 **0** 大小，讓它自動將受限於右邊和左邊的界檢視頂端。
    - 文字欄位與 **預留位置** 文字設為 **輸入通知訊息**。 將欄位限制在標籤的下方，如下所示。 將檢視控制器設為輸出委派。
    - 標題按鈕 **傳送通知** 限制文字欄位正下方且在水平置中。

    檢視應如下所示：

    ![][32]


2. [新增輸出](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) 標籤和文字欄位已連接您的檢視，並更新您 `interface` 定義，以支援 `UITextFieldDelegate` 和 `NSXMLParserDelegate`。 新增以下所示的三個屬性宣告，以協助呼叫 REST API 及剖析回應。

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

3. 開啟 HubInfo.h 並新增下列常數，它們將用於傳送通知給您的中樞。 預留位置字串常值取代實際 *DefaultFullSharedAccessSignature* 連接字串。

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. 將下列 `#import` 陳述式新增至 ViewController.h 檔案。

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. 在 ViewController.m 中，將下列程式碼新增至介面實作。 此程式碼將會剖析您 *DefaultFullSharedAccessSignature* 連接字串。 中所述 [REST API 參考](http://msdn.microsoft.com/library/azure/dn495627.aspx), ，已剖析的資訊會用來產生的 SaS 權杖 **授權** 要求標頭。

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

6. 在 ViewController.m 中，於檢視載入時更新 `viewDidLoad` 方法以剖析連接字串。 也將公用程式方法新增至介面實作，如下所示。  


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


8. Ctrl + 拖曳 **傳送通知** 按鈕至 ViewController.m 以新增名為 **SendNotificationMessage** 的 **Touch Down**。 以下列程式碼更新方法，來使用 REST API 通知傳送。

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


10. 在 ViewController.m 中，加入下列委派方法，以支援使用 `NSXMLParser` 剖析回應。

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


> [AZURE.NOTE] 如果您遇到有關 bitcode 支援 Xcode7 中的建置錯誤，您應該變更 [建置設定]-> [啟用 Bitcode"(ENABLE_BITCODE) 來在 Xcode 中，[否]。 通知中樞 SDK 目前不支援 Bitcode。 

您可以找到所有可能的通知裝載在 Apple [Local and Push Notification Programming Guide]。


##測試應用程式

若要在 iOS 上測試推播通知，您必須將 app 部署至裝置。 您無法利用 iOS 模擬器傳送 Apple 推播通知。

1. 執行應用程式，並確認註冊成功，然後再按 **確定**。

    ![][33]

2. 您可以傳送測試通知從 [Azure Classic Portal]。 如果您已在 App 中加入傳送通知的程式碼，在文字欄位中觸控以輸入通知訊息。 然後按下 **傳送** 鍵盤上的按鈕或 **傳送通知** 傳送通知訊息檢視中的按鈕。

    ![][34]

3. 此通知會傳送至所有已註冊要接收通知的所有裝置。

    ![][35]

如果您有任何問題或為所有讀者改善本教學課程的建議，請在下方的 Disqus 一節為我們留下註解。


##後續步驟

在此簡單範例中，您廣播通知到您的所有 iOS 裝置。 我們建議您執行下一個步驟中，再繼續進行的學習 [Use Notification Hubs to push notifications to users] 教學課程。 該教學課程將逐步引導您使用標記，建立後端以傳送通知。 

如果您想要按興趣群組分隔使用者，您可以另外前往 [Use Notification Hubs to send breaking news]。 

一般通知中樞的詳細資訊，請參閱 [Notification Hubs Guidance]。



<!-- Images. -->

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



<!-- URLs. -->
[Mobile Services iOS SDK version 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-notify-users.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-send-breaking-news.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1


