---
title: "Leküldéses értesítések küldése Chrome-alkalmazásokba az Azure Notification Hubs használatával | Microsoft Docs"
description: "Ebből az anyagból megtudhatja, hogyan küldhet leküldéses értesítéseket Chrome-alkalmazásokba az Azure Notification Hubs használatával."
services: notification-hubs
keywords: "mobil leküldéses értesítések,leküldéses értesítések,leküldéses értesítés,chrome leküldéses értesítések"
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 33ef17f1556822c78783cc56b8ea7867eef2ec71
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Leküldéses értesítések küldése Chrome-alkalmazásokba az Azure Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan küldhet leküldéses értesítéseket az Azure Notification Hubs használatával egy Chrome-alkalmazásba, amely a Google Chrome böngészőben jelenik meg. Ebben az oktatóanyagban egy olyan Chrome-alkalmazást hoz létre, amely leküldéses értesítéseket fogad a [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/) használatával. 

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).
> 
> 

Az oktatóanyag bemutatja a leküldéses értesítések engedélyezéséhez szükséges, alapvető lépéseket:

* [A Google Cloud Messaging engedélyezése](#register)
* [Az értesítési központ konfigurálása](#configure-hub)
* [A Chrome-alkalmazás csatlakoztatása az értesítési központhoz](#connect-app)
* [Leküldéses értesítés küldése a Chrome-alkalmazásba](#send)
* [További funkciók és képességek](#next-steps)

> [!NOTE]
> A Chrome-alkalmazások leküldéses értesítései nem általános böngészőn belüli értesítések, hanem a böngésző bővíthetőségi modelljére jellemzőek (a részletes információkat lásd: [Chrome-alkalmazások – áttekintés]). Az asztali böngészőn kívül a Chrome-alkalmazások (Android és iOS rendszerű) mobileszközökön is futnak az Apache Cordova segítségével. További információ: [Chrome-alkalmazások mobileszközökön].
> 
> 

A GCM és az Azure Notification Hubs konfigurálása ugyanúgy történik, mint Androidon, mivel a [Google Cloud Messaging for Chrome] elavult, így az Android-eszközöket és a Chrome-példányokat már ugyanaz a GCM támogatja.

## <a id="register"></a>A Google Cloud Messaging engedélyezése
1. Nyissa meg a [Google Cloud Console] webhelyét, jelentkezzen be Google-fiókja hitelesítő adataival, majd kattintson a **Create Project** (Projekt létrehozása) elemre. Adjon meg egy megfelelő **projektnevet**, majd kattintson a **Create** (Létrehozás) gombra.
   
    ![Google Cloud Console – Projekt létrehozása][1]
2. Jegyezze fel a létrehozott projekt **Projects** (Projektek) oldalán szereplő **Project Number** (Projektszám) értékét. Ezt a projektszámot a Chrome-alkalmazás GCM-regisztrációja során fogja használni a **GCM Sender ID** (GCM-küldőazonosító) értékeként.
   
    ![Google Cloud Console – Projektszám][2]
3. A bal oldali ablaktáblán kattintson az **APIs & auth** (API-k és hitelesítés) elemre, majd görgessen lefelé, és kattintással engedélyezze a **Google Cloud Messaging for Android** beállítást. A **Google Cloud Messaging for Chrome** használatát nem kell külön engedélyeznie.
   
    ![Google Cloud Console – Kiszolgálókulcs][3]
4. A bal oldali ablaktáblán kattintson a **Credentials** (Hitelesítő adatok)  > **Create New Key** (Új kulcs létrehozása)  > **Server Key** (Kiszogálókulcs)  > **Create** (Létrehozás) elemre.
   
    ![Google Cloud Console – Hitelesítő adatok][4]
5. Jegyezze fel a kiszolgálói **API KEY** (API-kulcs) értékét. Ezzel az értékkel fogja konfigurálni az értesítési központot a következő szakaszban, hogy engedélyezze a leküldéses értesítések küldését a GCM-be.
   
    ![Google Cloud Console – API-kulcs][5]

## <a id="configure-hub"></a>A saját értesítési központ konfigurálása
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6.   A **Beállítások** oldalon válassza az **Értesítési szolgáltatások**, majd a **Google (GCM)** lehetőséget. Adja meg az API-kulcsot, majd mentsen.

        ![Azure Notification Hubs – Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

## <a id="connect-app"></a>A Chrome-alkalmazás csatlakoztatása az értesítési központhoz
Az értesítési központ konfigurálva lett a GCM-mel való együttműködésre, és rendelkezik a kapcsolati karakterláncokkal az alkalmazás regisztrálására értesítések fogadásához és leküldéses értesítések küldéséhez.

### <a name="create-a-new-chrome-app"></a>Új Chrome-alkalmazás létrehozása
Az alábbi minta a [Chrome-alkalmazások GCM-mintáján] alapul, és a Chrome-alkalmazás javasolt létrehozási módját alkalmazza. Ez a szakasz kiemeli azon lépéseket, amelyek kifejezetten az Azure Notification Hubsra vonatkoznak. 

> [!NOTE]
> Javasoljuk, hogy töltse le a jelen Chrome-alkalmazás forrását a következő helyről: [Chrome-alkalmazás – Notification Hub-minta]. 

A Chrome-alkalmazás JavaScripttel készült, és bármilyen szövegszerkesztővel létrehozható. A következő kép bemutatja, hogy a Chrome-alkalmazás hogy néz ki:

![Google Chrome-alkalmazás][15]

1. Hozzon létre egy `ChromePushApp` nevű mappát. A név szabadon választható – ha más nevet ad neki, akkor azt használja szükséges kódszakaszok elérési útjában is.
2. A második lépésben létrehozott mappába töltse le a [crypto-js kódtárat]. Ez a kódtármappa a következő két almappát tartalmazza: `components` és `rollups`.
3. Hozzon létre egy `manifest.json` fájlt. Az összes Chrome-alkalmazás egy olyan jegyzékfájlt használ, amely tartalmazza az alkalmazás metaadatait, és –ami még fontosabb – a felhasználói telepítés során az alkalmazásnak megadott összes engedélyt.
   
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
   
    Figyelje meg a `permissions` elemet, amely megadja, hogy a Chrome-alkalmazás fogadhat-e leküldéses értesítéseket a GCM-től. Meg kell adnia az Azure Notification Hubs URI-ját is, ahol a Chrome-alkalmazás REST-hívást hajt végre a regisztrációhoz.
    A mintaalkalmazás a `gcm_128.png` ikonfájlt is használja, amely az eredeti GCM-mintából újrahasznosított forrásban található. Ezt bármilyen képpel helyettesítheti, amely megfelel az [ikonokra vonatkozó követelményeknek](https://developer.chrome.com/apps/manifest/icons).
4. Hozzon létre egy `background.js` nevű fájlt a következő kóddal:
   
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
   
    Ez a fájl ugrik fel a Chrome-alkalmazás HTML-ablakában (**register.html**), valamint meghatározza a bejövő leküldéses értesítések kezelésére szolgáló **messageReceived** kezelőt is.
5. Hozzon létre egy `register.html` nevű fájlt – ez határozza meg a Chrome-alkalmazás felhasználói felületét. 
   
   > [!NOTE]
   > Ebben a példában a következőt használjuk: **CryptoJS v3.1.2**. Ha a kódtár másik verzióját töltötte le, helyettesítse be a megfelelő verziószámot az `src` elérési útjában.
   > 
   > 
   
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
6. Hozzon létre egy `register.js` nevű fájlt a jelen lépésben található kóddal. Ez a fájl a `register.html` szkriptjét határozza meg. A Chrome-alkalmazások nem engedélyezik a beágyazott futtatás használatát, így a felhasználói felülethez külön háttérszkriptet kell létrehoznia.
   
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
   
    A szkript a következő fő paraméterekkel rendelkezik:
   
   * A **window.onload** határozza meg két gomb kattintási eseményeit a felhasználói felületen. Az egyik gombkattintási eseménykezelő a GCM-ben regisztrál, a másik pedig a másik a GCM-regisztráció után visszaadott regisztrációs azonosítót az Azure Notification Hubs-regisztrációra használja.
   * Az **updateLog** a függvény, amely lehetővé teszi, hogy a kód adatokat naplózzon. 
   * A **registerWithGCM** az első kattintáskezelő, amely végrehajtja a `chrome.gcm.register` hívást a GCM felé az aktuális Chrome App-példány regisztrálásához.
   * A **registerCallback** visszahívási függvényt a rendszer a GCM-regisztrációs hívás visszatérésekor hívja meg.
   * A **registerWithNH** a második kattintáskezelő, amely a Notification Hubs-regisztrációt hajtja végre. Lekéri a `hubName` és (a felhasználó által megadott ) `connectionString` paramétereket, valamint létrehozza a Notification Hubs-regisztrációs REST API-hívást.
   * A **splitConnectionString** és a **generateSaSToken** olyan segítők, amelyek az SaS-jogkivonatok létrehozásának JavaScript-alapú megvalósítását képviselik. Ezeket az összes REST API-hívásban használni kell. További információk: [Általánosan használt fogalmak](http://msdn.microsoft.com/library/dn495627.aspx).
   * A **sendNHRegistrationRequest** függvény HTTP REST-hívást hajt végre az Azure Notification Hubs felé.
   * A **registrationPayload** határozza meg a regisztrációs XML hasznos adatait. További információk: [Regisztrációs NH REST API létrehozása]. Frissítenie kell a benne található regisztrációs azonosítót a GCM-től kapott értékkel.
   * A **client** az **XMLHttpRequest** azon példánya, amelyet az alkalmazás a HTTP POST kérelem végrehajtására használ. Frissítse az `Authorization` fejlécet a következővel: `sasToken`. A hívás sikeres végrehajtása regisztrálja a Chrome-alkalmazás ezen példányát az Azure Notification Hubsban.

A projekt teljes mappastruktúrája a következőhöz hasonlít: ![Google Chrome-alkalmazás – Mappastruktúra][21]

### <a name="set-up-and-test-your-chrome-app"></a>A Chrome-alkalmazás beállítása és tesztelése
1. Nyissa meg a Chrome böngészőt. Nyissa meg a **Chrome-bővítményeket**, majd engedélyezze a **Fejlesztői módot**.
   
    ![Google Chrome – Fejlesztői mód engedélyezése][16]
2. Kattintson a **Kicsomagolt bővítmények betöltése** elemre, és keresse meg azt a mappát, amelyben a fájlokat létrehozta. Igény szerint a **Chrome-alkalmazások és bővítmények fejlesztői eszközét** is használhatja. Ez az eszköz maga is egy Chrome-alkalmazás, amely a Chrome webáruházból telepíthető, és speciális hibakeresési képességeket biztosít a Chrome-alkalmazások fejlesztéséhez.
   
    ![Google Chrome – Kicsomagolatlan bővítmény betöltése][17]
3. Ha a Chrome-alkalmazás létrehozása hibaüzenet nélkül megtörtént, akkor megjelenik az alkalmazás.
   
    ![Google Chrome – A Chrome-alkalmazás megjelenítése][18]
4. Adja meg a **Google Cloud Console**-ból korábban beszerzett **Projektszámot** küldőazonosítóként, és kattintson a **Register with GCM** (Regisztráció a GCM-ben) elemre. A következő üzenetet kell látnia: **Registration with GCM succeeded** (Sikeres GCM-regisztráció).
   
    ![Google Chrome – A Chrome-alkalmazás testreszabása][19]
5. Adja meg a **Notification Hub Name** (Notification Hub neve) és a **DefaultListenSharedAccessSignature** értéket, amelyeket korábban szerzett be a portálról, majd kattintson a **Register with Azure Notification Hub** (Regisztráció az Azure Notification Hub használatával) lehetőségre. A következő üzenetet kell látnia: **Notification Hub Registration successful!** (Sikeres Notification Hub-regisztráció), valamint a regisztrációs válasz részletes adatait, amely tartalmazza az Azure Notification Hubs-regisztráció azonosítóját.
   
    ![Google Chrome – A Notification Hub-adatok megadása][20]  

## <a name="send"></a>Értesítés küldése a Chrome-alkalmazásba
Tesztelési célból küldjön leküldéses Chrome-értesítéseket egy .NET-konzolalkalmazás használatával. 

> [!NOTE]
> A Notification Hubs használatával bármilyen háttérrendszerből küldhet leküldéses értesítéseket a nyilvános <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-felületen</a> keresztül. A platformok közötti átjárhatóságról a [dokumentációs portálon](https://azure.microsoft.com/documentation/services/notification-hubs/) talál további példákat.
> 
> 

1. A Visual Studio **Fájl** menüjében válassza az **Új**, majd a **Projekt** elemet. A **Visual C#** területen kattintson a **Windows** és a **Konzolalkalmazás**, majd pedig az **OK** elemre.  Ez a lépés létrehoz egy új konzolalkalmazás-projektet.
2. Az **Eszközök** menüben kattintson a **NuGet-csomagkezelő**, majd a **Csomagkezelői konzol** elemre. Megjelenik a Csomagkezelői konzol az alsó ablakban.
3. A konzolablakban hajtsa végre a következő parancsot:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
       This adds a reference to the Azure Service Bus SDK with the <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>.
4. Nyissa meg a `Program.cs` elemet, majd adja hozzá a következő `using` utasítást:
   
        using Microsoft.Azure.NotificationHubs;
5. A `Program` osztályban adja hozzá a következő metódust:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
       Make sure to replace the `<hub name>` placeholder with the name of the notification hub that appears in the [portal](https://portal.azure.com) in your Notification Hub blade. Also, replace the connection string placeholder with the connection string called `DefaultFullSharedAccessSignature` that you obtained in the notification hub configuration section.
   
   > [!NOTE]
   > A kapcsolati karakterláncot **Teljes**, és ne **Figyelési** hozzáféréssel használja. A **Figyelési** hozzáféréssel rendelkező kapcsolati karakterláncok nem biztosítanak engedélyeket a leküldéses értesítések küldéséhez.
   > 
   > 
6. Adja hozzá a következő hívásokat a `Main` metódushoz:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Győződjön meg arról, hogy a Chrome böngésző fut, majd futtassa a konzolalkalmazást.
8. Az alábbi felugró értesítés jelenik meg az asztalon.
   
    ![Google Chrome – Értesítés][13]
9. Ha a Chrome fut, a tálca Chrome-értesítések ablakában az összes értesítést láthatja (Windowson).
   
    ![Google Chrome – Értesítések listája](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> A Chrome-alkalmazást nem kell futtatnia vagy megnyitnia a böngészőben (de a Chrome böngészőnek futnia kell). A Chrome-értesítések ablakban az összes értesítés egyesített nézetét is megtekintheti.
> 
> 

## <a name="next-steps"></a>Következő lépések
További tudnivalók a Notification Hubsról: [Notification Hubs – áttekintés].

Ha adott felhasználóknak szeretne értesítést küldeni, tekintse meg az [Azure Notification Hubs – Felhasználók értesítése] oktatóanyagot. 

Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el az [Azure Notification Hubs – Legfrissebb hírek] témakört.

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
[Chrome-alkalmazás – Notification Hub-minta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Google Cloud Console]: http://cloud.google.com/console
[Notification Hubs – áttekintés]: notification-hubs-push-notification-overview.md
[Chrome-alkalmazások – áttekintés]: https://developer.chrome.com/apps/about_apps
[Chrome-alkalmazások GCM-mintáján]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Chrome-alkalmazások mobileszközökön]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Regisztrációs NH REST API létrehozása]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js kódtárat]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs – Felhasználók értesítése]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs – Legfrissebb hírek]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
