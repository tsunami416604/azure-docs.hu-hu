<properties
    pageTitle="將推播通知新增至行動服務應用程式 (Xamarin.iOS) - 行動服務"
    description="了解如何在 Xamarin.iOS 應用程式中以 Azure 行動服務使用推播通知。"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    services="mobile-services"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="yuaxu"/>

# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##概觀

本主題說明如何使用 Azure 行動服務傳送推播通知至 Xamarin.iOS 8 應用程式。 在本教學課程中，您新增推播通知使用 Apple 推播通知服務 (APNS) [Get started with Mobile Services] 專案。 完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程需要下列各項：

+ iOS 8 裝置 (您無法在 iOS 模擬器中測試推播通知)
+ iOS Developer Program 成員資格
+ [Xamarin.iOS Studio]
+ [Azure 行動服務元件]

>[AZURE.IMPORTANT] 基於 APNS 需求，您必須部署，並在模擬器中測試推播通知在 iOS 功能裝置 （iPhone 或 iPad） 而不是。

APNS 使用憑證來驗證您的行動服務。 遵循這些指示建立必要的憑證，並將憑證上傳至您的行動服務。 正式的 APNS 功能文件，請參閱 [Apple Push Notification Service]。

## <a name="certificates"></a>產生憑證簽署要求檔案

首先，您必須產生憑證簽署要求 (CSR) 檔案，這將由 Apple 用來產生簽署的憑證。

1. 在公用程式，執行 **Keychain Access 工具**。

2. 按一下 [ **鑰匙圈存取**, ，依序展開 **憑證助理**, ，然後按一下 [ **要求憑證從憑證授權單位...**。

    ![][5]

3. 輸入您 **使用者電子郵件地址**, ，輸入 **一般名稱** 值，並確定 **儲存至磁碟** 已選取，然後按一下 [ **繼續**。

    ![][6]

4. 輸入中的憑證簽署要求 (CSR) 檔案的名稱 **另存新檔**, 中, 選取位置 **其中**, ，然後按一下 [ **儲存**。

    ![][7]

    請記住您所選擇的位置。

接下來，您要向 Apple 註冊應用程式、啟用推播通知，以及上傳這個匯出的 CSR 以建立推播憑證。

## <a name="register"></a>針對推播通知註冊應用程式

若要從行動服務將推播通知傳送至 iOS 應用程式，您必須向 Apple 註冊您的應用程式，並註冊推播通知。

1. 如果您尚未註冊您的應用程式，瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a> Apple 開發人員中心，使用您的 Apple ID 登入按一下 **識別碼**, ，然後按一下 [ **應用程式識別碼**, ，並依 **+** 號以建立您的應用程式的應用程式識別碼。

    ![][102]

2. 輸入您的應用程式的名稱 **描述**, ，然後輸入並記住唯一 **Bundle Identifier**, ，檢查 「 應用程式服務] 區段中的 「 推播通知] 選項，然後按一下 **繼續**。 這個範例會使用識別碼 **MobileServices.Quickstart** ，但您可能不會重複使用此相同識別碼，因為應用程式識別碼在所有使用者必須是唯一的。 因此，建議您在應用程式名稱之後附加您的全名或縮寫。

    ![][103]

    這會產生應用程式識別碼，並要求您 **提交** 資訊。 按一下 [ **提交**。

    ![][104]

    一旦您按一下 **提交**, ，您會看到 **註冊完成** 畫面，如下所示。 按一下 [ **完成**。

    ![][105]

3. 找出剛才建立的應用程式識別碼，並且按一下該資料列。

    ![][106]

    按一下應用程式識別碼將顯示應用程式和應用程式識別碼的詳細資料。 按一下 [ **設定** ] 按鈕。

    ![][107]

4. 捲動到畫面底部，按一下 **建立憑證...** ] 區段下的按鈕 **Development Push SSL Certificate**。

    ![][108]

    這將顯示 [Add iOS Certificate] 助理。

    注意：本教學課程使用開發憑證。 註冊生產憑證時，將使用同一個程序。 您將憑證上傳至行動服務時，請確定設定相同的憑證類型。

5. 按一下 [ **選擇檔案**, ，瀏覽上您稍早儲存 CSR 檔案的位置，然後按一下 [ **產生**。

    ![][110]

6. 在入口網站建立憑證之後，請按一下 **下載** ] 按鈕，然後按一下 [ **完成**。

    ![][111]

    這會下載簽署憑證，並將它儲存到您電腦中的 [下載] 資料夾。

    ![][9]

    注意：根據預設，下載的檔案 (開發憑證) 的名稱會是 <strong>aps_development.cer</strong>。

7. 按兩下下載的推播憑證 **aps_development.cer**。

    這樣會將新的憑證安裝在金鑰鏈中，如下所示：

    ![][10]

    注意：憑證中的名稱可能會不同，不過字首會加上 <strong>Apple Development iOS Push Notification Services:</strong> 前置詞。

之後，您會使用該憑證來產生 .p12 檔案，並將該檔案上傳到行動服務以啟用 APNS 驗證。

## <a name="profile"></a>建立應用程式的佈建設定檔

1. 回到 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>, ，請選取 **佈建設定檔**, ，請選取 **所有**, ，然後按一下 [ **+** ] 按鈕以建立新的設定檔。 這會啟動 **Add iOS Provisiong Profile** 精靈。

    ![][112]

2. 選取 **iOS App Development** 下 **開發** 為佈建設定檔類型，然後按一下 **繼續**。

3. 接下來，選取 [從行動服務快速入門應用程式的應用程式識別碼 **應用程式識別碼** 下拉式清單中，然後按一下 [ **繼續**。

    ![][113]

4. 在 **選取憑證** 畫面中選取先前建立的憑證，然後按一下 **繼續**。

    ![][114]

5. 接下來，選取 **裝置** 進行測試，然後按一下 **繼續**。

    ![][115]

6. 最後，選取 [在設定檔的名稱 **設定檔名稱**, ，按一下 [ **產生**, ，然後按一下 **完成**。

    ![][116]

    這將建立新的佈建設定檔。

    ![][117]

## <a name="configure-mobileServices"></a>設定行動服務以傳送推播要求

在向 APNS 註冊應用程式及設定專案後，接下來您必須設定行動服務以整合 APNS。

1. 在 Keychain Access 中，以滑鼠右鍵按一下新的憑證，請按一下 **匯出**, ，將您的檔案，選取 **.p12** 格式，然後按一下 **儲存**。

    ![][28]

    記下匯出憑證的檔案名稱和位置。

2. 登入 [Azure classic portal], ，按一下 [ **行動電話服務**, ，然後按一下您的應用程式。

    ![][18]

3. 按一下 [ **推播** ] 索引標籤上，按一下 [ **上載** 下 **apple 推播通知設定**。

    ![][19]

    This displays the Upload Certificate dialog.

4. 按一下 [ **檔案**, 、 選取匯出的憑證.p12 檔案、 輸入 **密碼**, ，請確定正確 **模式** 已選取，按一下核取圖示，然後按一下 [ **儲存**。

    ![][20]

您的行動服務現已設定為與 APNS 搭配運作。

## <a name="configure-app"></a>設定您的 Xamarin.iOS 應用程式

1. 在 Xamarin.Studio 中，開啟 **Info.plist**, ，並更新 **Bundle Identifier** 與您稍早建立的識別碼。

    ![][121]

2. 向下捲動至 **背景模式** 並檢查 **啟用背景模式** 方塊和 **遠端通知** 方塊。

    ![][122]

3. 按兩下以開啟 [方案] 面板中的專案 **專案選項**。

4.  選擇 **iOS 套件組合簽署** 下 **建置**, ，然後選取對應 **識別** 和 **佈建設定檔** 您設定此專案。

    ![][120]

    這將確保 Xamarin 專案使用新的設定檔進行程式碼簽署。 如需官方 Xamarin 裝置佈建文件，請參閱 [Xamarin Device Provisioning]。

## <a name="add-push"></a>將推播通知新增至應用程式

1. 在 Xamarin.Studio 中，開啟 AppDelegate.cs 檔案並新增以下屬性：

        public string DeviceToken { get; set; }

2. 開啟 **TodoItem** 類別並新增下列屬性 ︰

        [JsonProperty(PropertyName = "deviceToken")]
        public string DeviceToken { get; set; }

3. 在 **QSTodoService**, ，覆寫現有的用戶端宣告為 ︰

        public MobileServiceClient client { get; private set; }

4. 然後新增下列方法，以便 **AppDelegate** 可以取得更新版本，才能註冊推播通知用戶端 ︰

        public MobileServiceClient GetClient {
            get{
                return client;
            }
        }

5. 在 **AppDelegate**, ，覆寫 **FinishedLaunching** 事件 ︰

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

6. 在 **AppDelegate**, ，覆寫 **RegisteredForRemoteNotifications** 事件 ︰

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            DeviceToken = deviceToken.Description;
            DeviceToken = DeviceToken.Trim ('<', '>').Replace (" ", "");

            // Get Mobile Services client
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            var push = client.GetPush ();
            push.RegisterNativeAsync (DeviceToken, tag);
        }

7. 在 **AppDelegate**, ，覆寫 **ReceivedRemoteNotification** 事件 ︰

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            Debug.WriteLine(userInfo.ToString());
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

8. 在 **QSTodoListViewController**, ，修改 **OnAdd** 動作以取得裝置權杖儲存在 **AppDelegeate**, ，然後將 **TodoItem** 所加入。

        string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

        var newItem = new TodoItem()
        {
            Text = itemText.Text,
            Complete = false,
            DeviceToken = deviceToken
        };

您的應用程式現在已更新為支援推播通知。

## <a name="update-scripts"></a>在 Azure 傳統入口網站中更新已註冊的插入指令碼

1. 在 [Azure classic portal], ，按一下 [ **資料** 標籤，然後按一下 **TodoItem** 資料表。

    ![][21]

2. 在 **todoitem**, ，按一下 [ **指令碼** 索引標籤並選取 **插入**。

    ![][22]

    這會顯示函式中發生插入時所叫用 **TodoItem** 資料表。

3. 下列程式碼，來取代 insert 函數，然後按一下 [ **儲存**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send("uniqueTag", {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    這會註冊新的 insert 指令碼，並使用 [apns object] 推播通知 （插入的文字） 傳送給插入要求中提供的裝置。

   >[AZURE.NOTE] 此指令碼將延遲傳送通知，讓您有時間關閉應用程式以接收快顯通知。

## <a name="test"></a>在應用程式中測試推播通知

1. 按下 **執行** 按鈕以建置專案並啟動應用程式在執行 iOS 的裝置，然後按一下 [ **確定** 以接受推播通知

    ![][23]

   >[AZURE.NOTE] 您必須明確地接受推播通知，從您的應用程式。 只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中，輸入有意義的文字，例如 _新的行動服務工作_ ，然後按一下加號 (**+**) 圖示。

    ![][24]

3. 確認已收到通知，然後按一下 [ **確定** 以關閉通知。

    ![][25]

4. 重複步驟 2 並立即關閉應用程式，接著確認以下快顯通知可顯示。

    ![][26]

您已成功完成此教學課程。

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[120]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure classic portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download


