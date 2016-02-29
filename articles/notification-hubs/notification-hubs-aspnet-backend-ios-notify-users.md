<properties
    pageTitle="Azure 通知中樞透過 .NET 後端通知 iOS 使用者"
    description="了解如何在 Azure 中將推播通知傳送給使用者。 程式碼範例是以 Objective-C 撰寫並以 .NET API 作為後端。"
    documentationCenter="ios"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="wesmc"/>

#Azure 通知中樞透過 .NET 後端通知 iOS 使用者

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

##概觀

Azure 中的推播通知支援可讓您存取易於使用、多重平台的大規模推播基礎結構，而大幅簡化消費者和企業應用程式在行動平台上的推播通知實作。 本教學課程將示範如何使用 Azure 通知中心，來將推播通知傳送到特定裝置上的特定應用程式使用者。 ASP.NET WebAPI 後端可用來驗證用戶端並產生通知，如指引主題所顯示的 [從應用程式後端註冊](notification-hubs-registration-management.md#registration-management-from-a-backend)。

> [AZURE.NOTE] 本教學課程假設您已建立並設定通知中心中所述 [開始使用通知中樞 (iOS)](notification-hubs-ios-get-started.md)。 本教學課程也是必要條件 [安全推播 (iOS)](notification-hubs-aspnet-backend-ios-secure-push.md) 教學課程。
> 如果您使用行動服務作為後端服務，請參閱 [行動服務版本](../mobile-services-javascript-backend-ios-push-notifications-app-users.md) 本教學課程。



[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## 修改您的 iOS 應用程式

1. 開啟您在單一頁面檢視應用程式 [開始使用通知中樞 (iOS)](notification-hubs-ios-get-started.md) 教學課程。

    > [AZURE.NOTE] 在本節中，我們假設您的專案已使用空白組織名稱。 否則，您需要針對所有類別名稱預先考量您的組織名稱。

2. 在您的 Main.storyboard 中從物件程式庫新增下面的螢幕擷取畫面中顯示的元件。

    ![][1]

    + **使用者名稱**: 含有預留位置文字的 UITextField 的 *輸入使用者名稱*, 、 傳送結果標籤和左右邊界限制並位於傳送結果標籤正下方。
    + **密碼**: 含有預留位置文字的 UITextField 的 *輸入密碼*, ，下方的 [使用者名稱文字欄位和左右邊界限制並位於使用者名稱文字欄位正下方。 檢查 **Secure Text Entry** 底下屬性偵測器中選項 *傳回金鑰*。
    + **登入**: 密碼文字欄位正下方標記的 UIButton，並取消選取 **啟用** 底下屬性偵測器中，選項 *控制項內容*
    + **WNS**: 標籤與開關來啟用傳送通知 Windows 通知服務，如果已安裝的中心。 請參閱 [Windows 入門](notification-hubs-windows-store-dotnet-get-started.md) 教學課程。
    + **GCM**: 若要啟用 Google Cloud messaging 傳送通知，如果已設定集線器上的標籤與開關。 請參閱 [Android 入門](notification-hubs-android-get-started.md) 教學課程。
    + **APNS**: 啟用傳送通知給 Apple 平台通知服務的標籤與開關。
    + **Recipent Username**: 的預留位置文字的 UITextField *收件者使用者名稱標記*, 、 立即下方 GCM 標籤，以及限制左、 右邊界及下 GCM 標籤。


    Some components were added in the [Getting Started with Notification Hubs (iOS)](notification-hubs-ios-get-started.md) tutorial.

3. **Ctrl** 拖曳檢視中的元件到 ViewController.h，並新增這些新的輸出。

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
        @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

        // Used to enable the buttons on the UI
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

        // Used to enabled sending notifications across platforms
        @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

        - (IBAction)LogInAction:(id)sender;

4. 請在 ViewController.h 中，在您的匯入陳述式正下方新增以下的 `#define`。 替代 *< 輸入您後端端點 >* 要用來部署您的應用程式後端上一節中的目的地 url 的預留位置。 例如， *http://you_backend.azurewebsites.net*。

        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"

4. 在您的專案，建立新 **Cocoa Touch 類別** 名為 **RegisterClient** ASP.NET 後端所建立的介面。 建立繼承自 `NSObject` 的類別。 然後在 RegisterClient.h 中新增下列程式碼。

        @interface RegisterClient : NSObject

        @property (strong, nonatomic) NSString* authenticationHeader;

        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;

        -(instancetype) initWithEndpoint:(NSString*)Endpoint;

        @end

5. 在 RegisterClient.m 中，更新 `@interface` 區段：

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        @property (strong, nonatomic) NSURLSession* endpoint;

        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion;
        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

        @end

6. 使用以下程式碼取代 RegisterClient.m 中的 `@implementation` 區段。


        @implementation RegisterClient

        // Globals used by RegisterClient
        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

        -(instancetype) initWithEndpoint:(NSString*)Endpoint
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
                _endpoint = Endpoint;
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                    andCompletion:(void(^)(NSError*))completion
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description]
                stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                    uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
                completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                    tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                    if (error) {
                        completion(error);
                        return;
                    }

                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if (httpResponse.statusCode == 200) {
                        completion(nil);
                    } else if (httpResponse.statusCode == 410 && retry) {
                        [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                    } else {
                        NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                    @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                                options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                                encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                    registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                if (!error)
                {
                    completion(response, error);
                }
                else
                {
                    NSLog(@"Error request: %@", error);
                    completion(nil, error);
                }
            }];
            [dataTask resume];
        }

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                        objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                    _endpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data
                        encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                        [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId
                        forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        @end

    The code above implements the logic explained in the guidance article [Registering from your app backend](notification-hubs-registration-management.md#registration-management-from-a-backend) using NSURLSession to perform REST calls to your app backend, and NSUserDefaults to locally store the registrationId returned by the notification hub.

    Note that this class requires its property **authorizationHeader** to be set in order to work properly. This property is set by the **ViewController** class after the log in.

7. 在 ViewController.h 中，為 RegisterClient.h 新增 `#import` 陳述式。 然後為裝置權杖新增宣告，並參照 `@interface` 區段中的 `RegisterClient` 執行個體：

        #import "RegisterClient.h"

        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;

8. 在 ViewController.m 中，於 `@interface` 區段中新增私用方法宣告：

        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;

        @end

> [AZURE.NOTE] 下列程式碼片段不是安全驗證結構描述，您應使用的實作 **Createandsetauthenticationheaderwithusername:** 成產生的驗證權杖，以供註冊用戶端類別，例如 OAuth、 Active Directory 您特定的驗證機制。

9. 然後在 ViewController.m 的 `@implementation` 區段中新增以下程式碼，這會新增實作以設定裝置權杖與驗證標頭。

        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }

        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                        encoding:NSUTF8StringEncoding];
        }

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }

    請留意設定裝置權杖如何啟用登入按鈕。 這是因為檢視控制器會向應用程式後端註冊推播通知 (作為登入動作的一部分)。 因此，在裝置權杖已正確設定之前，我們不希望有人能夠存取登入動作。 只要登入在推播註冊之前發生，您可能就會想要將前者與後者分開。

10. 在 ViewController.m 中，使用下列程式碼片段來實作動作方法的程式 **登入** 按鈕，將傳送通知訊息中使用 ASP.NET 後端的方法。

        - (IBAction)LogInAction:(id)sender {
            // create authentication header and set it in register client
            NSString* username = self.UsernameField.text;
            NSString* password = self.PasswordField.text;

            [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

            __weak ViewController* selfie = self;
            [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
                andCompletion:^(NSError* error) {
                if (!error) {
                    dispatch_async(dispatch_get_main_queue(),
                    ^{
                        selfie.SendNotificationButton.enabled = YES;
                        [self MessageBox:@"Success" message:@"Registered successfully!"];
                    });
                }
            }];
        }


        - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                    Message:(NSString*)message
        {
            NSURLSession* session = [NSURLSession
                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
                delegateQueue:nil];

            // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
            NSURL* requestURL = [NSURL URLWithString:[NSString
                stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
                usernameTag]];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];

            // Get the mock authenticationheader from the register client
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                self.registerClient.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

            // Execute the send notification REST API on the ASP.NET Backend
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                        pns, httpResponse.statusCode, error];
                    dispatch_async(dispatch_get_main_queue(),
                    ^{
                        // Append text because all 3 PNS calls may also have information to view
                        [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                    });
                    NSLog(status);
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


11. 更新的動作 **傳送通知** ] 按鈕以使用 ASP.NET 後端並傳送至交換器所啟用的任何 PNS。


        - (IBAction)SendNotificationMessage:(id)sender
        {
            //[self SendNotificationRESTAPI];
            [self SendToEnabledPlatforms];
        }


        -(void)SendToEnabledPlatforms
        {
            NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

            [self.sendResults setText:@""];

            if ([self.WNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

            if ([self.GCMSwitch isOn])
                [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

            if ([self.APNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
        }



11. 函式中 **ViewDidLoad**, ，新增下列內容來具現化 RegisterClient 執行個體並設定文字欄位的委派。

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.RecipientField.delegate = self;
        self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];

12. 現在在 **AppDelegate.m**, ，方法的所有內容都移除 **application: didRegisterForPushNotificationWithDeviceToken:** 換成下列內容以確定檢視控制器包含從 Apn 擷取的最新裝置權杖:

        // Add import to the top of the file
        #import "ViewController.h"

        - (void)application:(UIApplication *)application
                    didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            ViewController* rvc = (ViewController*) self.window.rootViewController;
            rvc.deviceToken = deviceToken;
        }

13. 最後在 **AppDelegate.m**, ，請確定您有下列方法:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

## 測試應用程式

1. 在 XCode 中，在實體 iOS 裝置上執行應用程式 (推播通知無法在模擬器中運作)。

2. 在 iOS 應用程式 UI 中，輸入使用者名稱和密碼。 這些可以是任何字串，但兩個必須是相同的字串值。 然後按一下 [ **登入**。

    ![][2]


3. 您應該會看到註冊成功的快顯通知。 按一下 [ **確定**。

    ![][3]

4. 在 **收件者使用者名稱標記* 文字欄位中，輸入從其他裝置註冊時搭配使用的使用者名稱標記。
5. 輸入通知訊息，然後按一下 [ **傳送通知**。  只有已經使用收件者使用者名稱標記註冊的裝置才會收到通知訊息。  通知訊息只會傳送給那些使用者。

    ![][4]


[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png

