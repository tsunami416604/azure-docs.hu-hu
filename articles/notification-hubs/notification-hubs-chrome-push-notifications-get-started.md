---
title: Leküldéses értesítések küldése Chrome-alkalmazásokba az Azure Notification Hubs használatával | Microsoft Docs
description: Ebből az anyagból megtudhatja, hogyan küldhet leküldéses értesítéseket Chrome-alkalmazásokba az Azure Notification Hubs használatával.
services: notification-hubs
keywords: mobil leküldéses értesítések,leküldéses értesítések,leküldéses értesítés,chrome leküldéses értesítések
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 248fd094a8655af2a21035267a6b8f69f268683d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262165"
---
# <a name="tutorial-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Oktatóanyag: Leküldéses értesítések küldése Chrome-alkalmazásokba az Azure Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ez az oktatóanyag végigvezeti egy értesítési központ létrehozásán, és ismerteti, hogy hogyan küldhet leküldéses értesítéseket egy Google Chrome-mintaalkalmazásba a [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/) használatával. A Chrome-alkalmazás a Google Chrome böngésző környezetében fut, és regisztrál az értesítési központban. 

> [!NOTE]
> A Chrome-alkalmazások leküldéses értesítései nem általános böngészőn belüli értesítések, hanem a böngésző bővíthetőségi modelljére jellemzőek (a részletes információkat lásd: [Chrome-alkalmazások – áttekintés]). Az asztali böngészőn kívül a Chrome-alkalmazások (Android és iOS rendszerű) mobileszközökön is futnak az Apache Cordova segítségével. További információ: [Chrome-alkalmazások mobileszközökön].

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * [A Google Cloud Messaging engedélyezése](#register)
> * [Az értesítési központ konfigurálása](#configure-hub)
> * [A Chrome-alkalmazás csatlakoztatása az értesítési központhoz](#connect-app)
> * [Leküldéses értesítés küldése a Chrome-alkalmazásba](#send)

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a id="register"></a>A Google Cloud Messaging engedélyezése
1. Lépjen be a [Google Cloud Console](https://console.cloud.google.com/cloud-resource-manager) (Google felhőkonzol) webhelyére, és jelentkezzen be Google-fiókja hitelesítő adataival.
2. Kattintson az eszköztár **Create Project** (Projekt létrehozása) gombjára. 

    ![Create Project (Projekt létrehozása) gomb](media/notification-hubs-chrome-get-started/create-project-button.png)
1. Adjon meg egy megfelelő **projektnevet**, majd kattintson a **Create** (Létrehozás) gombra.
2. Kattintson az eszköztár az értesítések (harang) ikonjára, majd a **Create Project** (Projekt létrehozása) üzenetre. 

    ![Projekt-létrehozási értesítés](media/notification-hubs-chrome-get-started/project-creation-notification.png)
1. Jegyezze fel a létrehozott projekt **Projects** (Projektek) oldalán szereplő **Project Number** (Projektszám) értékét. Ezt a projektszámot a Chrome-alkalmazás GCM-regisztrációja során fogja használni a **GCM Sender ID** (GCM-küldőazonosító) értékeként.
   
    ![Google Cloud Console – Projektszám](media/notification-hubs-chrome-get-started/gcm-project-number.png)
3. Az irányítópulton kattintson a **Go to APIs overview** (Ugrás az API-k áttekintéséhez) gombra. 

    ![Go to API overviews (Ugrás az API-k áttekintéséhez) gomb](media/notification-hubs-chrome-get-started/go-to-apis-overview-button.png)
1. Az API & Services (API-k és szolgáltatások) lapon válassza az **Enable APIs and Services** (API-k és szolgáltatások engedélyezése) lehetőséget. 

    ![Enable APIs and Services (API-k és szolgáltatások engedélyezése)](media/notification-hubs-chrome-get-started/enable-apis-and-services.png)
1. A listában keressen a **cloud messaging** kulcsszóval. A szűrt listában válassza a **Google Cloud Messaging** elemet. 

    ![Google Cloud Messaging API](media/notification-hubs-chrome-get-started/google-cloud-messaging-api.png)
1. A **Google Cloud Messaging** lapon kattintson az **Enable** (Engedélyezés) lehetőségre.

    ![GCM engedélyezése](media/notification-hubs-chrome-get-started/enable-gcm.png)
1. Az **API & Services** (API és szolgáltatások) lapon váltson a **Credentials** (Hitelesítő adatok) lapra. Kattintson a **Create credentials** (Hitelesítő adatok létrehozása) lehetőségre, majd kattintson az **API key** (API-kulcs) gombra. 

    ![API-kulcs létrehozására szolgáló gomb](media/notification-hubs-chrome-get-started/create-api-key-button.png)
1. Az **API key created** (Létrehozott API-kulcs) párbeszédablakban kattintson a másolás gombra a kulcs vágólapra másolásához. Mentse a kulcsot egy tetszőleges helyre. Ezzel az értékkel fogja konfigurálni az értesítési központot a következő szakaszban, hogy engedélyezze a leküldéses értesítések küldését a GCM-be.

    ![API lap](media/notification-hubs-chrome-get-started/api-created-page.png)
12. Válassza ki az API-kulcsot az **API keys** (API-kulcsok) listából. Az API key (API-kulcs) lapon kattintson az **IP addresses (web servers, cron jobs, etc.)** (IP-címek (webkiszolgálók, CRON-feladatok stb.)) lehetőségre, adja meg a **0.0.0.0/0** IP-címet, majd kattintson a Save (Mentés) gombra. 

    ![IP-címek engedélyezése](media/notification-hubs-chrome-get-started/enable-ip-addresses.png)

## <a id="configure-hub"></a>Az értesítési központ létrehozása
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6. Válassza a **Google (GCM)** lehetőséget a **NOTIFICATION SETTINGS** (Értesítési beállítások) kategóriában, adja meg a GCM-projekt API-kulcsát az **API key** mezőben, majd kattintson a **Save** (Mentés) gombra.

      ![Azure Notification Hubs – Google (GCM)](media/notification-hubs-chrome-get-started/configure-gcm-api-key.png)

## <a id="connect-app"></a>A Chrome-alkalmazás csatlakoztatása az értesítési központhoz
Az értesítési központ konfigurálva lett a GCM-mel való együttműködésre, és rendelkezik a kapcsolati sztringekkel az alkalmazás regisztrálására értesítések fogadásához és leküldéses értesítések küldéséhez.

### <a name="create-a-new-chrome-app"></a>Új Chrome-alkalmazás létrehozása
Az alábbi minta a [Chrome-alkalmazások GCM-mintáján] alapul, és a Chrome-alkalmazás javasolt létrehozási módját alkalmazza. Ez a szakasz kiemeli azon lépéseket, amelyek kifejezetten az Azure Notification Hubsra vonatkoznak. 

> [!NOTE]
> Javasoljuk, hogy töltse le a jelen Chrome-alkalmazás forrását a következő helyről: [Chrome-alkalmazás – Notification Hub-minta]. 

A Chrome-alkalmazás JavaScripttel készült, és bármilyen szövegszerkesztővel létrehozható. A következő kép bemutatja, hogy hogyan néz ki a Chrome-alkalmazás:

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
   
    Figyelje meg a `permissions` elemet, amely megadja, hogy a Chrome-alkalmazás fogadhat-e leküldéses értesítéseket a GCM-től. A mintaalkalmazás a `gcm_128.png` ikonfájlt is használja, amely az eredeti GCM-mintából újrahasznosított forrásban található. Ezt bármilyen képpel helyettesítheti, amely megfelel az [ikonokra vonatkozó követelményeknek](https://developer.chrome.com/apps/manifest/icons).
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
                updateLog("Notification Hub Registration successful!");
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
   * A **splitConnectionString** és a **generateSaSToken** olyan segítők, amelyek az SaS-jogkivonatok létrehozásának JavaScript-alapú megvalósítását képviselik. Ezeket az összes REST API-hívásban használni kell. További információk: [Általánosan használt fogalmak](https://msdn.microsoft.com/library/dn495627.aspx).
   * A **sendNHRegistrationRequest** függvény HTTP REST-hívást hajt végre az Azure Notification Hubs felé.
   * A **registrationPayload** határozza meg a regisztrációs XML hasznos adatait. További információk: [Regisztrációs NH REST API létrehozása]. Frissítse a benne található regisztrációs azonosítót a GCM-től kapott értékkel.
   * A **client** az **XMLHttpRequest** azon példánya, amelyet az alkalmazás a HTTP POST kérelem végrehajtására használ. Frissítse az `Authorization` fejlécet a következővel: `sasToken`. A hívás sikeres végrehajtása regisztrálja a Chrome-alkalmazás ezen példányát az Azure Notification Hubsban.

        A projekt teljes mappastruktúrája a következő struktúrához hasonlít:    ![Google Chrome-alkalmazás – Mappastruktúra][21]

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
> A Notification Hubs használatával bármilyen háttérrendszerből küldhet leküldéses értesítéseket a nyilvános <a href="https://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-felületen</a> keresztül. A platformok közötti átjárhatóságról a [dokumentációs portálon](https://azure.microsoft.com/documentation/services/notification-hubs/) talál további példákat.
> 
> 

1. A Visual Studio **Fájl** menüjében válassza az **Új**, majd a **Projekt** elemet. A **Visual C#** területen kattintson a **Windows** és a **Konzolalkalmazás**, majd pedig az **OK** elemre.  Ez a lépés létrehoz egy új konzolalkalmazás-projektet.
2. Kattintson a **Tools** (Eszközök) menü a **NuGet Package Manager** (NuGet-csomagkezelő) elemére, majd a **Package Manager Console** (Csomagkezelői konzol) lehetőségre. Megjelenik a Csomagkezelői konzol az alsó ablakban.
3. A konzolablakban hajtsa végre a következő parancsot:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
   A rendszer automatikusan hozzáad a projekthez egy, az Azure Service Bus SDK-ra mutató hivatkozást a <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-csomaggal</a>.
4. Nyissa meg a `Program.cs` elemet, majd adja hozzá a következő `using` utasítást:
   
        using Microsoft.Azure.NotificationHubs;
5. A `Program` osztályban adja hozzá a következő metódust:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
    A `<hub name>` helyőrzőt cserélje le az értesítési központnak a [portál](https://portal.azure.com) Notification Hub lapján megjelenő nevére. Továbbá cserélje le a kapcsolati sztring helyőrzőjét „Az értesítési központ konfigurálása” szakaszban beszerzett `DefaultFullSharedAccessSignature` nevű kapcsolati sztringre.
   
    > [!NOTE]
    > A kapcsolati sztringet **Teljes**, és ne **Figyelési** hozzáféréssel használja. A **Figyelési** hozzáféréssel rendelkező kapcsolati sztringek nem biztosítanak engedélyeket a leküldéses értesítések küldéséhez.
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
Ebben az oktatóanyagban szórásos értesítéseket küldött a háttérrendszerben regisztrált ügyfelek mindegyikének. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott eszközökre, lépjen tovább a következő oktatóanyagra: 

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
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
[Notification Hubs Overview]: notification-hubs-push-notification-overview.md
[Chrome-alkalmazások – áttekintés]: https://developer.chrome.com/apps/about_apps
[Chrome-alkalmazások GCM-mintáján]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Chrome-alkalmazások mobileszközökön]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Regisztrációs NH REST API létrehozása]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js kódtárat]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
