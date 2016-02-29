<properties
    pageTitle="開始使用適用於 Chrome 應用程式的 Azure 通知中樞 | Microsoft Azure"
    description="在本教學課程中，您將了解如何使用 Azure 通知中樞，將通知推播到 Chrome 應用程式。"
    services="notification-hubs"
    documentationCenter=""
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="10/20/2015"
    ms.author="wesmc"/>

# 開始使用適用於 Chrome 應用程式的通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

本主題將說明如何使用 Azure 通知中樞將推播通知傳送至 Chrome 應用程式。

使用 Chrome 應用程式通知的主要優點之一，是通知會顯示在 Google Chrome 瀏覽器的內容中。 您不需要執行 Chrome 應用程式，也不需要在瀏覽器中開啟 (但 Chrome 瀏覽器本身必須為執行狀態)。 您也可以在 [Chrome 通知] 視窗中整合檢視您所有的通知。

>[AZURE.NOTE] 這不是一般的瀏覽器中推播通知，而且是 Chrome 應用程式專用 — 如需詳細資訊，請參閱 [Chrome 應用程式概觀]。 Chrome 應用程式以前稱為「封裝應用程式」，不同於較簡單的「代管應用程式」。 差異，請參閱 [可安裝的 Web 應用程式]。 Chrome 應用程式也可以透過 Apache Cordova 在行動裝置 (Android 和 iOS) 上執行。 若要了解更多，請參閱 [行動裝置上的 Chrome 應用程式]。

在本教學課程中，我們將建立可使用 Google Cloud Messaging (GCM) 接收推播通知的 Chrome 應用程式。 完成本教學課程後，您便能夠將推播通知廣播給已安裝此 Chrome 應用程式的所有 Chrome 使用者。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

* [啟用 Google 雲端通訊](#register)
* [設定您的通知中樞](#configure-hub)
* [將您的 Chrome 應用程式連接到通知中樞](#connect-app)
* [傳送通知給您的 Chrome 應用程式](#send)
* [後續步驟](#next-steps)

本教學課程示範使用通知中樞的簡單廣播案例。 設定 GCM 和 Azure 通知中樞等同於 Android，因為 [Google Cloud Messaging for Chrome] 已被取代，相同的 GCM 現在可支援 Android 裝置和 Chrome 執行個體的設定。

請確實依照教學課程的「後續步驟」一節操作，以了解如何使用通知中樞來處理特定使用者和裝置群組。

>[AZURE.NOTE] 若要完成本教學課程，您必須具有有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F)。

##<a id="register"></a>啟用 Google 雲端通訊

1. 瀏覽至 [Google Cloud Console] 網站，使用您的 Google 帳戶認證登入，然後按一下 **建立專案** ] 按鈕。 提供適當 **專案名稱**, ，然後按一下 [ **建立** ] 按鈕。

    ![][1]

2. 請記下的 **專案編號** 上 **專案** 您剛才建立的專案頁面。 您會將它做為 **GCM 寄件者識別碼** Chrome 應用程式，以向 GCM 註冊。

    ![][2]

3. 在左窗格中，按一下 [ **APIs & auth**, ，然後向下捲動並按一下切換開關以啟用 **Google Cloud Messaging for Android**。 您不需要啟用 **Google Cloud Messaging for Chrome**。

    ![][3]

4. 在左窗格中，按一下 [ **認證** > **建立新的金鑰** > **伺服器金鑰** > **建立**。

    ![][4]

5. 請記下的伺服器 **API 金鑰**。 您後續將會在通知中樞裡設定此金鑰，讓它能夠將推播通知傳送至 GCM。

    ![][5]

##<a id="configure-hub"></a>設定您的通知中樞

1. 登入 [Azure 傳統入口網站]，然後再按一下 **+ 新增** 畫面的左下方。

2. 按一下 [ **應用程式服務** > **服務匯流排** > **通知中樞** > **快速建立**。 輸入您的通知中心的名稱，選取您所需的區域，然後按一下 **建立新的通知中樞**。

    ![][6]

4. 移至您剛剛建立的通知中樞。 按一下包含您的通知中心的命名空間 (通常是 ***的通知中心名稱*ns**)。

    ![][7]

5. 按一下 [ **通知中心** 頂端的索引標籤。

    ![][8]

6. 按一下 [ **設定** 頂端的索引標籤。

    ![][9]

7. 在 **設定** 索引標籤上，向下捲動至 **google 雲端訊息設定**, ，輸入 **API 金鑰** 值，您在上一節中取得，然後按一下 **儲存**。

    ![][10]

8. 選取 **儀表板** 的頂端，索引標籤，然後按一下 **連接資訊** 底部。

    ![][11]

9. 請注意 **DefaultListenSharedAccessSignature** (這您必須向通知中心註冊 Chrome 應用程式上) 和 **DefaultFullSharedAccessSignature** (這必須為傳送通知)。

    ![][12]

現在通知中樞已設定成使用 GCM，而且您已具備必要的連接字串可做進一步設定。

##<a id="connect-app"></a>將您的 Chrome 應用程式連接到通知中樞

###建立新的 Chrome 應用程式

下列範例根據 [Chrome 應用程式 GCM 範例]，並使用建議的方式建立 Chrome 應用程式。 在下列各節中，我們將加強說明 Azure 通知中樞的相關步驟。 我們建議您下載此 Chrome 應用程式，從 [Chrome 應用程式通知中樞範例] 的原始碼。

Chrome 應用程式是透過 JavaScript 建立的，您可以使用任何慣用的文字編輯器加以建立。 此 Chrome 應用程式如下所示。

    ![][15]

2. 建立資料夾，並將它命名 **ChromePushApp** 或任何您想要的項目。

3. 下載 **crypto-js 程式庫** [crypto-js 程式庫] 從這個資料夾中。 此程式庫資料夾將包含兩個子資料夾: **元件** 和 **彙總套件**。

4. 建立 **manifest.json** 檔案。 所有的 Chrome 應用程式都會由資訊清單檔案備份，此檔案會說明應用程式中繼資料，特別是應用程式的可用權限。

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    請注意 **權限** 元素，其指定此 Chrome 應用程式能夠從 GCM 接收推播通知。 它也必須指定 Azure 通知中樞 URI，其中 Chrome 應用程式將呼叫 REST 以進行註冊。
    此程序會使用圖示檔案 gcm_128.png，您會在原始碼中發現此檔案重複使用於原始的 GCM 範例。 您可以使用任何想要的影像。

5. 建立檔案，稱為 **為 background.js** 為下列程式碼:

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    這是快顯 Chrome 應用程式視窗 HTML 檔案 (**register.html**) 也會定義處理常式 **messageReceived** 來處理內送的推播通知。

6. 建立檔案，稱為 **register.html** 定義 Chrome 應用程式的 UI。 請注意，這個範例會使用 *CryptoJS v3.1.2*。 如果您下載任何其他版本，請修正指令碼 src 路徑。

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

7. 建立檔案，稱為 **register.js** 下列程式碼。 此檔案會指定後面的指令碼 **register.html**。 Chrome 應用程式並不允許內嵌執行，因此您必須為 UI 建立個別的備份指令碼。

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    上述指令碼有下列的輸出：
    - *window.onload* 在 UI 上定義的兩個按鈕的按鈕 click 事件。 其中一個使用 GCM，註冊，另一個使用 GCM 來註冊 Azure 通知中樞的註冊後，會傳回的註冊識別碼。
    - *updateLog* 是定義簡單的記錄函式的函式。
    - *registerWithGCM* 是第一個按鈕 click 處理常式，讓 **進行 chrome.gcm.register** GCM 註冊此 Chrome 應用程式執行個體來呼叫。
    - *registerCallback* 是在上述 GCM 註冊呼叫傳回時，會呼叫回呼函式。
    - *registerWithNH* 是第二個按鈕 click 處理常式，會向通知中心。 它會取得 **hubName** 和 **connectionString** (其中使用者已指定) 並製作通知中樞註冊 REST API 呼叫。
    - *splitConnectionString* 和 *generateSaSToken* 都是建立 SaS 權杖，必須在所有的 REST API 呼叫中傳送的 JavaScript 實作。 如需詳細資訊，請參閱 [一般概念](http://msdn.microsoft.com/library/dn495627.aspx)。
    - *sendNHRegistrationRequest* 是進行 HTTP REST 呼叫的函式。
    - *registrationPayload* 定義註冊 XML 裝載。 如需詳細資訊，請參閱 [建立註冊 NH REST API]。 我們會以接收自 GCM 更新其中的註冊識別碼。
    - *用戶端* 的執行個體 **XMLHttpRequest** 我們用來發出 HTTP POST 要求。 請注意，我們更新 **授權** 標頭 **sasToken**。 成功完成此呼叫後，即會向 Azure 通知中樞註冊此 Chrome 應用程式執行個體。


您應該會在結尾處看見下列資料夾檢視：
    ![][21]

###設定和測試 Chrome 應用程式

1. 開啟 Chrome 瀏覽器。 開啟 **Chrome 延伸模組** 並啟用 **開發人員模式下**。

    ![][16]

2. 按一下 [ **載入解壓縮的延伸模組** 並瀏覽至您用來建立檔案的資料夾。 您可以選擇性地使用 **Chrome 應用程式與延伸模組開發人員工具**。 此工具本身為 Chrome 應用程式 (從 Chrome 線上應用程式商店進行安裝)，且提供 Chrome 應用程式開發進階偵錯功能。

    ![][17]

3. 如果該 Chrome 應用程式在建立時未發生任何錯誤，則您將會看見該應用程式顯示。

    ![][18]

4. 輸入 **專案編號** 你稍早從 **Google Cloud Console** 為寄件者識別碼，然後按一下 **向 GCM 註冊**。 您必須查看郵件 **Registration with GCM 成功。**

    ![][19]

5. 輸入您 **的通知中心名稱** 和 **DefaultListenSharedAccessSignature** 您從入口網站更早版本，並按一下 [取得 **Azure 通知中心註冊**。 您必須查看郵件 **成功的通知中心註冊!** 和註冊回應，其中包含 Azure 通知中樞註冊識別碼的詳細資料

    ![][20]  

##<a name="send"></a>傳送通知給您的 Chrome 應用程式

在本教學課程中，您將使用 .NET 主控台應用程式來傳送通知。 不過，您可以透過任何後端使用通知中心傳送通知 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 介面，從任何後端使用通知中心傳送通知</a>.

如需如何從整合通知中心之 Azure 行動服務後端傳送通知的範例，請參閱 「 開始使用行動服務中的推播通知 」 ([.NET 後端](../mobile-services-javascript-backend-android-get-started-push.md) | [JavaScript 後端](../mobile-services-javascript-backend-android-get-started-push.md))。  
如需如何使用 REST Api 傳送通知的範例，請參閱 < 如何從 Java/PHP/Python 使用通知中樞 」 ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md))。

1. 在 Visual Studio 中，從 **檔案** 功能表上，選取 **新增** 然後 **專案**。 在 **Visual C#**, ，按一下 [ **Windows** 和 **主控台應用程式**, ，然後按一下 [ **確定**。  這會建立新的主控台應用程式專案。

2. 從 **工具** ] 功能表上，按一下 [ **程式庫套件管理員** 然後 **Package Manager Console**。 這會顯示 [Package Manager Console]。

3. 在主控台視窗中，執行下列命令：

        Install-Package Microsoft.Azure.NotificationHubs

    這會使用 <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝，來新增 Azure 服務匯流排 SDK 的參考</a>.

4. 開啟檔案 **Program.cs** ，並新增下列 `using` 陳述式:

        using Microsoft.Azure.NotificationHubs;

5. 在 **程式** 類別中，新增下列方法:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    請務必取代 *中樞名稱* 預留位置會出現在入口網站上的通知中心名稱 **通知中心** ] 索引標籤。 此外，將連接字串預留位置取代連接字串呼叫 **DefaultFullSharedAccessSignature** 您取得的一節中 「 設定您的通知中心 」。

    >[AZURE.NOTE] 請確定您使用的連接字串具有 **完整** 存取權，而非 **接聽** 存取。  **接聽** 存取字串不具備傳送通知的權限。

5. 新增以下幾行，在 **Main** 方法:

         SendNotificationAsync();
         Console.ReadLine();

6. 請確定您已開啟 Chrome 瀏覽器。 為此並不需要開啟 Chrome 應用程式。 您應該會在桌面上看見下列通知快顯視窗。

    ![][13]

7. 當 Chrome 執行時，您也可以使用工作列 (在 Windows 上) 上的 [Chrome 通知] 視窗來查看所有的通知。

    ![][14]

## <a name="next-steps"> </a>後續步驟

在此簡單範例中，您已將通知廣播到 Chrome 應用程式。
進一步了解通知中心，在 [通知中樞概觀]。
若要鎖定特定使用者，請參閱本教學課程 [Azure 通知中樞通知使用者]。 如果您想要按興趣群組分隔使用者，您可以參閱 [Azure 通知中樞即時新聞]。

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome App Notification Hub Sample]: http://google.com
[Google Cloud Console]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notification Hubs Overview]: http://msdn.microsoft.com/library/jj927170.aspx
[Chrome Apps Overview]: https://developer.chrome.com/apps/about_apps
[Chrome App GCM Sample]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Chrome Apps on Mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Create Registration NH REST API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js library]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-store-dotnet-send-breaking-news.md

