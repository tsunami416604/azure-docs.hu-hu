 <properties
    pageTitle="將推播通知新增至行動服務應用程式 (Xamarin.Forms) - 行動服務"
    description="了解如何在 Xamarin.Forms 應用程式中以 Azure 行動服務使用推播通知。"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    services="mobile-services"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.workload="mobile"
    ms.date="10/05/2015"
    ms.author="wesmc"/>

# 將推播通知新增至 Xamarin.Forms 應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##概觀

本主題說明如何使用 Azure 行動服務，將推播通知傳送至 Xamarin.Forms 方案的 iOS、Android 和 Windows Phone 應用程式。 由建立行動服務開始。 然後，您將下載入門範例、利用適當的推播通知服務註冊，以及將程式碼加入至要從這些服務接收通知的方案。

完成本教學課程時，您的行動服務會在每次使用者於其中一個應用程式中新增工作時傳送推播通知。 您可以找到完整的範例: [Completed Xamarin.Forms Azure Push Notification Sample]。

本教學課程需要下列各項：

+ iOS 8 裝置 (您無法在 iOS 模擬器中測試推播通知)
+ iOS Developer Program 成員資格
+ [Xamarin.iOS Studio]
+ [Azure 行動服務元件]
+ 有效的 Google 帳戶
+ [Google Cloud Messaging Client Component]. 您在教學課程中會新增此元件。

本主題內容：

1. [建立新的行動服務](#create-service)
2. [下載並設定入門範例](#download-starter-sample)
4. [將推播通知新增至 Xamarin.Forms.iOS 應用程式](#iOS)
5. [將推播通知新增至 Xamarin.Forms.Android 應用程式](#Android)
6. [將推播通知新增至 Xamarin.Forms.Windows 應用程式](#Windows)
7. [更新 Azure 資料表插入指令碼，以傳送推播通知給所有應用程式](#all-apps)

## <a name="create-service"></a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

若要能夠在新行動服務中儲存應用程式資料，您必須先建立新的資料表。

1. 在 Azure 傳統入口網站中，按一下 [ **行動電話服務**, ，然後按一下您剛才建立的行動服務。

2. 按一下 [ **資料** 索引標籤，然後按一下 [ **+ 建立**。

    ![][123]

    這會顯示 **建立新資料表** ] 對話方塊。

3. 在 **資料表名稱** 類型 _TodoItem_, ，然後按一下核取按鈕。

    ![][124]

    這會建立新的儲存體資料表 **TodoItem** 含預設權限集，這表示應用程式的任何使用者可以存取與變更資料表中的資料。

    > [AZURE.NOTE] 在行動服務快速入門中使用相同的資料表名稱。 However, each table is created in a schema that is specific to a given mobile service. 目的是為了防止多個行動服務使用相同資料庫時產生資料衝突。

4. 按一下新 **TodoItem** 資料表，並驗證其中不含資料列。

5. 按一下 [ **資料行** ] 索引標籤上，並確認只有一個 **識別碼** 自動為您建立資料行。

    此為行動服務資料表的最低需求。

    > [AZURE.NOTE] 在您的行動服務上啟用動態結構描述時，新的資料行是在 JSON 物件傳送至行動服務透過插入或更新操作時自動建立。

現在您已準備好將新的行動服務作為應用程式的資料儲存區使用。

## <a name="download-starter-sample"></a>下載並設定入門範例
我們會將推播通知新增至現有範例。

1. 下載下列的範例: [Xamarin.Forms Azure Push Notification Starter Sample]。

2. 在 [Azure classic portal], ，按一下 [ **行動電話服務**, ，然後按一下 [行動服務。 按一下 [ **儀表板** 索引標籤並記下 **網站 URL**。 然後按一下 [ **管理金鑰** 並記下的 **應用程式金鑰**。 當您從應用程式程式碼存取行動服務時，您將會用到這些值。

3. 在  **todoazure (portable)** 專案的方案中，開啟 **Constants.cs** 檔案中，將 `ApplicationURL` 和 `ApplicationKey` 的網站 URL 和應用程式您在上一個步驟中取得金鑰。

## <a name="iOS"></a>將推播通知新增至 Xamarin.Forms.iOS 應用程式

您將使用 Apple Push Notification 服務 (APNS)，將推播通知加入至 iOS 應用程式。 您將需要有效的 Google 帳戶，而 [Google Cloud Messaging Client Component]。

>[AZURE.IMPORTANT] 基於 Apple 推播通知服務 (APNS) 需求，您必須部署，並在模擬器中測試推播通知在 iOS 功能裝置 (iPhone 或 iPad) 而不是。

APNS 使用憑證來驗證您的行動服務。 遵循這些指示建立必要的憑證，並將憑證上傳至您的行動服務。 正式的 APNS 功能文件，請參閱 [Apple Push Notification Service]。

### <a name="certificates"></a>產生憑證簽署要求檔案

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

### <a name="register"></a>針對推播通知註冊應用程式

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

    注意: 根據預設，下載的檔案開發憑證名為 <strong>aps_development.cer</strong>.

7. 按兩下下載的推播憑證 **aps_development.cer**。

    這樣會將新的憑證安裝在金鑰鏈中，如下所示：

    ![][10]

    注意: 憑證中的名稱可能會不同，但將會加 <strong>Apple Development iOS Push Notification Services:</strong>.

之後，您會使用該憑證來產生 .p12 檔案，並將該檔案上傳到行動服務以啟用 APNS 驗證。

### <a name="profile"></a>建立應用程式的佈建設定檔

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

7. 在 Xcode 中開啟 organizer 並選取 Devices 檢視，選取 **佈建設定檔** 中 **程式庫** 在左窗格中，區段，然後按一下 **重新整理** 在中間窗格底部的按鈕。

### <a name="configure-mobileServices"></a>設定行動服務傳送推播要求

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

### <a name="configure-app"></a>設定您的 Xamarin.iOS 應用程式

1. 在 Xamarin.Studio 或 Visual Studio 中開啟 **Info.plist**, ，並更新 **Bundle Identifier** 與您稍早建立的識別碼。

    ![][121]

2. 向下捲動至 **背景模式** 並檢查 **啟用背景模式** 方塊和 **遠端通知** 方塊。

    ![][122]

3. 按兩下以開啟 [方案] 面板中的專案 **專案選項**。

4.  選擇 **iOS 套件組合簽署** 下 **建置**, ，然後選取對應 **識別** 和 **佈建設定檔** 您設定此專案。

    ![][120]

    這將確保 Xamarin 專案使用新的設定檔進行程式碼簽署。 如需官方 Xamarin 裝置佈建文件，請參閱 [Xamarin Device Provisioning]。

### <a name="add-push"></a>將推播通知新增至應用程式

1. 在 Xamarin.Studio 或 Visual Studio 中，依序展開 **ToDoAzure.iOS** 專案中，開啟 **AppDelegate** 類別，然後再取代 **FinishedLaunching** 事件，以下列程式碼:

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();

            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. 在 **AppDelegate**, ，覆寫 **RegisteredForRemoteNotifications** 事件:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };

            const string template = "{\"aps\":{\"alert\":\"$(message)\"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag)
        }

7. 在 **AppDelegate**, ，覆寫 **ReceivedRemoteNotification** 事件:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

您的應用程式現在已更新為支援推播通知。

### <a name="update-scripts"></a>在 Azure 傳統入口網站中更新已註冊的插入指令碼

1. 在 Azure 傳統入口網站中，按一下 [ **資料** 標籤，然後按一下 **TodoItem** 資料表。

    ![][21]

2. 在 **todoitem**, ，按一下 [ **指令碼** 索引標籤並選取 **插入**。

    ![][22]

    這會顯示函式中發生插入時所叫用 **TodoItem** 資料表。

3. 下列程式碼，來取代 insert 函數，然後按一下 [ **儲存**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }

    如此即會註冊新的插入指令碼，該指令碼會將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。

   >[AZURE.NOTE] 此指令碼將延遲傳送通知，讓您有時間關閉應用程式以接收快顯通知。

### <a name="test"></a>在應用程式中測試推播通知

1. 按下 **執行** 按鈕以建置專案並啟動應用程式在執行 iOS 的裝置，然後按一下 [ **確定** 以接受推播通知

   >[AZURE.NOTE] 您必須明確地接受推播通知，從您的應用程式。 只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中，按一下 [ **新增** 按鈕、 新增工作標題，然後按一下 **儲存** ] 按鈕。

3. 確認已收到通知，然後按一下 [ **確定** 以關閉通知。


您已成功完成此教學課程。

## <a name="Android"></a>將推播通知新增至 Xamarin.Forms.Android 應用程式

您會使用 Google Cloud Messaging (GCM) 服務將推播通知新增至 Android 應用程式。 您將需要有效的 Google 帳戶，而 [Google Cloud Messaging Client Component]。

###<a id="register"></a>啟用 Google 雲端通訊

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###<a id="configure"></a>設定行動服務以傳送推播要求

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>更新註冊的插入指令碼以傳送通知

>[AZURE.NOTE] 下列步驟顯示如何更新 Azure 傳統入口網站的 TodoItem 資料表的插入作業註冊的指令碼。 您也可以直接在 Visual Studio 之伺服器總管的 Azure 節點中，直接存取和編輯此行動服務指令碼。

在 [Azure classic portal], ，按一下 [ **資料** 標籤，然後按一下 **TodoItem** 資料表。

   ![][21]

2. 在 **todoitem**, ，按一下 [ **指令碼** 索引標籤並選取 **插入**。

   ![][22]

    這會顯示函式中發生插入時所叫用 **TodoItem** 資料表。

3. 下列程式碼，來取代 insert 函數，然後按一下 [ **儲存**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    如此即會註冊新的插入指令碼，該指令碼會將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。

   >[AZURE.NOTE] 此指令碼將延遲傳送通知，讓您有時間關閉應用程式以接收快顯通知。


###<a id="configure-app"></a>設定用於推播通知的現有專案

1. 在 [方案] 檢視中，展開 **元件** Xamarin.Android 應用程式中的資料夾，並確定已安裝 Azure 行動服務封裝。

2. 以滑鼠右鍵按一下 **元件** 資料夾中，按一下 [  **取得更多元件...**, ，搜尋 **Google Cloud Messaging 用戶端** 元件並將它加入至專案。

1. 開啟 MainActivity.cs 專案檔案，然後將下列 using 陳述式加入至類別：

        using Gcm.Client;


4.  在 **MainActivity** 類別中，新增下列程式碼 **OnCreate** 方法呼叫後面 **Oncreate** 方法:

            try
            {
                // Check to ensure everything's setup right
                GcmClient.CheckDevice(this);
                GcmClient.CheckManifest(this);

                // Register for push notifications
                System.Diagnostics.Debug.WriteLine("Registering...");
                GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
            }
            catch (Java.Net.MalformedURLException)
            {
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

您 **MainActivity** 現在已準備好供新增推播通知。

###<a id="add-push"></a>將推播通知程式碼新增至應用程式

5. 在 ToDoAzure.Droid 專案中，建立名為 `GcmService` 的新類別。

5. 新增下列 using 陳述式 **GcmService** 類別:

        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;

6. 新增下列權限要求之間 **使用** 陳述式和 **命名空間** 宣告:

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. 在 **GcmService.cs** 專案檔案，新增下列類別:

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {

            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

    在上述程式碼中，您必須將 _`<PROJECT_NUMBER>`_ 在佈建您的應用程式在 Google 開發人員入口網站時由 Google 指派的專案編號。

8. 在 GcmService.cs 專案檔案中，新增下列程式碼定義 **GcmService** 類別:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


    請注意，此類別衍生自 **GcmServiceBase** ， **服務** 屬性必須套用至這個類別。

    >[AZURE.NOTE] **GcmServiceBase** 類別會實作 **onregistered ()**, ，**onunregistered ()**, ，**onmessage ()** 和 **onerror ()** 方法。 您必須覆寫這些方法在 **GcmService** 類別。

5. 加入下列程式碼以 **GcmService** 類別會覆寫 **OnRegistered** 事件處理常式。

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;

            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }
        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{\"data\":{\"message\":\"$(message)\"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

    此方法使用傳回的 GCM 註冊識別碼，向 Azure 註冊以取得推送通知。

10. 覆寫 **OnMessage** 方法中的 **GcmService** 為下列程式碼:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. 加入下列方法覆寫 **onunregistered ()** 和 **onerror ()**, ，所需的編譯專案。

        protected override void OnError(Context context, string errorId)
        {
              Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>在應用程式中測試推播通知

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

> [AZURE.IMPORTANT] 若要接收推播通知，您必須設定 Google 帳戶 Android 虛擬裝置上 (在模擬器中，瀏覽至 **設定** 按一下 **新增帳戶**)。 另外，確定模擬器已連線到網際網路。

1. 從 **工具**, ，按一下 [ **Open Android Emulator Manager**, ，選取您的裝置，然後按一下 **編輯**。

    ![][125]

2. 選取 **Google APIs** 中 **目標**, ，然後按一下 [ **確定**。

    ![][126]

3. 在上方工具列中，按一下 [ **執行**, ，然後選取您的應用程式。 這將啟動模擬器，並執行應用程式。

  應用程式擷取 *registrationId* 從 GCM 並向通知中心。

1. 在應用程式中加入新的工作。

2. 從螢幕頂端向下撥動將裝置的通知中心開啟，以檢視通知。

    ![][127]

## <a name="Windows"></a>將推播通知新增至 Xamarin.Forms.Windows 應用程式

本節說明如何使用 Azure 行動服務，將推播通知傳送至 Xamarin.Forms 方案中包含的 Windows Phone Silverlight 應用程式。

###<a id="update-app"></a> 更新應用程式以註冊通知

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1. 在 Visual Studio 中，開啟 App.xaml.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Phone.Notification;

3. 新增下列程式碼至 App.xaml.cs：

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                   // Register for notifications using the new channel
                    const string template =
                    "<?xml version=\"1.0\" encoding=\"utf-8\"?><wp:Notification " +
                    "xmlns:wp=\"WPNotification\"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    此程式碼會從 Windows Phone 8.x "Silverlight" 使用的 Microsoft 推播通知服務 (MPNS)，擷取適用於應用程式的 ChannelURI，然後註冊該 ChannelURI，以進行推播通知。

    >[AZURE.NOTE]在本教學課程中，行動服務會傳送快顯通知給裝置。 當您傳送磚通知時，您必須改為呼叫 **BindToShellTile** 通道上的方法。

4. 在頂端 **Application_Launching** 事件處理常式，在 App.xaml.cs 中，新增下列呼叫新 **AcquirePushChannel** 方法:

        AcquirePushChannel();

    這會確使在每次載入頁面時都會要求註冊。 在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。

5. 按下 **F5** 鍵以執行應用程式。 包含註冊金鑰的快顯對話方塊隨即顯示。

6.  在 [方案總管] 中，依序展開 **屬性**, 、 開啟 WMAppManifest.xml 檔案、 按一下 **功能** ] 索引標籤上，並確定 **ID___CAP___PUSH_NOTIFICATION** 已勾選功能。

    ![在 VS 中啟用通知](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

    如此可確定您的應用程式可以提出快顯通知。

###<a id="update-scripts"></a> 更新伺服器指令碼以傳送推播通知

最後，您必須在 TodoItem 資料表上更新對插入作業註冊的指令碼，以傳送通知。

1. 在 [Azure classic portal], ，按一下 [ **資料** 標籤，然後按一下 **TodoItem** 資料表。

    ![][21]

2. 在 **todoitem**, ，按一下 [ **指令碼** 索引標籤並選取 **插入**。

    ![][22]

    這會顯示函式中發生插入時所叫用 **TodoItem** 資料表。

3. 下列程式碼，來取代 insert 函數，然後按一下 [ **儲存**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    如此即會註冊新的插入指令碼，該指令碼會將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。

3. 按一下 [ **推播** ] 索引標籤，核取 **啟用未經驗證的推播通知**, ，然後按一下 [ **儲存**。

    這會使行動服務以未經驗證的模式連接到 MPNS，以傳送推播通知。

    >[AZURE.NOTE]本教學課程使用未經驗證的模式的 MPNS。 在此模式中，MPNS 會限制可傳送至裝置通道的通知數。 若要移除此限制，您必須產生憑證，然後按一下 [上傳]**** 並選取憑證，以上傳憑證。 如需產生憑證的詳細資訊，請參閱 [設定已驗證的 Web 服務以傳送 Windows Phone 的推播通知]。

###<a id="test"></a> 在應用程式中測試推播通知

1. 在 Visual Studio 中，按 F5 鍵以執行此應用程式。

    >[AZURE.NOTE] Windows Phone 模擬器上測試時，您可能會遇到 401 未經授權的 RegistrationAuthorizationException。 因為 Windows Phone 模擬器將其時鐘與主機電腦同步的方式，可能會在 `RegisterNativeAsync()` 呼叫期間發生這種情形。 這樣可能會產生將被拒絕的安全性權杖。 若要解決這個問題，只要在測試之前，手動設定模擬器中的時鐘即可。

5. 在應用程式，建立新的工作標題 **Hello push**, ，然後立即按一下啟動按鈕或返回按鈕，以離開應用程式。

    如此會傳送插入要求給行動服務來儲存新增的項目。 請注意，裝置會接收到顯示快顯通知 **hello push**。

    ![接收到快顯通知](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

    >[AZURE.NOTE]如果您仍在應用程式中，將不會收到通知。 若要接收快顯通知，應用程式正在作用中時，您必須處理 [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx) 事件。

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

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]:./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]:./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]:./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]:./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]:./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]:./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


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
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Forms Azure Push Notification Starter Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[Completed Xamarin.Forms Azure Push Notification Sample]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms

