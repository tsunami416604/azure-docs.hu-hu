---
title: A Notification Hubs használata Python
description: Ismerje meg, hogy a Python háttér-Azure Notification Hubs használatával.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 32953bacb8fdb135d5f3e0e9324218d2a71b0818
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777018"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Notification Hubs Python használatával
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

A Java/PHP/Python vagy Ruby-háttér használata a Notification Hub REST interfész, az MSDN-témakörben leírtak szerint minden értesítési központok szolgáltatás érhető el [Notification hub REST API-k](http://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Ez a minta hivatkozás megvalósítása megvalósításához a értesítést küld a Python és nem a hivatalosan támogatott értesítések Hub Python SDK.
> 
> Ez a minta a Python 3.4 leírva.
> 
> 

Ez a cikk azt ismerteti, hogyan számára:

* A Notification Hubs-szolgáltatások a Python egy REST-ügyfél felépítéséhez.
* A Python felületén a Notification Hub REST API-k értesítések küldéséhez. 
* Adja a HTTP REST-kérelem/válasz lekérése hibakeresés/oktatási célra. 

Kövesse a [Get bemutató](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) választás a mobil platform megvalósításához a háttér-rész a Python.

> [!NOTE]
> A minta a hatókör csak korlátozott mértékben értesítések küldéséhez, és minden olyan regisztrációs felügyeleti nem.
> 
> 

## <a name="client-interface"></a>Ügyféloldali felület
A fő ügyféloldali felületen elérhető ugyanazokat a módszereket biztosít a [.NET Notification Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx). Ez az interfész lehetővé teszi az oktatóanyagok és ezen a helyen aktuálisan elérhető mintákat közvetlenül fordítását, és az interneten a Közösség által közzétett.

Az összes elérhető kód megtalálhatja a [Python REST burkoló minta].

Ha például egy ügyfél létrehozásához:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Windows bejelentési értesítés küldése:

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

## <a name="implementation"></a>Megvalósítás
Ha még nem tette, hajtsa végre a [Get bemutató] akár utolsó szakasz esetében a háttér-megvalósításához.

Egy teljes REST-burkoló megvalósításához a részleteit található [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Ez a szakasz ismerteti a fő Notification hub REST-végpontok elérését, és értesítések küldéséhez szükséges lépéseket a Python végrehajtása

1. Kapcsolati karakterlánc elemzése
2. Az engedélyezési jogkivonat létrehozása
3. A HTTP REST API használatával értesítést küldeni

### <a name="parse-the-connection-string"></a>Kapcsolati karakterlánc elemzése
Az ügyfél, amelynek konstruktor elemzi a kapcsolati karakterlánc végrehajtási fő osztály itt található:

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"

        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug

            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")

            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Biztonsági jogkivonat létrehozása
A részleteket a biztonsági jogkivonat-létrehozási [Itt](http://msdn.microsoft.com/library/dn495627.aspx).
Adja hozzá a következő módszerek a **NotificationHub** a jogkivonat létrehozásához osztály alapján a jelenlegi kérelem és a hitelesítő adatokat a kapcsolati karakterlánc kinyert URI.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>A HTTP REST API használatával értesítést küldeni
Először is, hogy használja értesítést képviselő osztályt határozza meg.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")

            self.format = notification_format
            self.payload = payload

            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Ez az osztály egy olyan tároló, a natív értesítési törzs vagy egy sablon értesítés, fejlécek, olyan készlete, (natív platform vagy sablon) és a platform-specifikus tulajdonságok (például az Apple lejárati tulajdonság és WNS fejlécekkel együtt) tartalmazó tulajdonságait.

Tekintse meg a [Notification hub REST API-k dokumentáció](http://msdn.microsoft.com/library/dn495827.aspx) és az összes rendelkezésre álló beállítások az adott értesítési platformok formázza az adathordozót.

Most már ez az osztály írásával küldése értesítési módszerek belül a **NotificationHub** osztály.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Ezek a módszerek HTTP POST-kérelmet küld az értesítési központot, és a megfelelő törzs és -fejléceket elküldeni az értesítést /messages végpontja.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Hibakeresési tulajdonság használatával a részletes naplózás engedélyezése
A HTTP-kérelem részletes naplózás információt ír engedélyezése debug tulajdonság az értesítési központ inicializálása során, és válasz memóriakép, valamint részletes értesítési üzenetet küldeni eredménye. A [Notification Hubs TestSend tulajdonság](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) az értesítés küldési kimenetelét részletes információt ad vissza. Használja az-inicializálása a következő kódot:

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

A Notification Hub kérés küldése HTTP URL-cím beolvasása kiegészül a "test" lekérdezési karakterlánc eredményeképpen. 

## <a name="complete-tutorial"></a>Az oktatóanyag befejezése
Az első lépéseket bemutató oktatóanyaghoz most az értesítés küldésével a Python háttér-hajthatja végre.

A Notification Hubs-ügyfél inicializálása (útmutatását, helyettesítse be a kapcsolati karakterlánc és a központ nevét a [Get bemutató]):

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

Majd adja hozzá a küldési kódot, attól függően, hogy a célként megadott mobilplatformot. Ez a minta is hozzáad a magasabb szintű módszerek a platformhoz, például a windows; send_windows_notification alapján küldő értesítések engedélyezése (az apple) send_apple_notification stb. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows áruház és Windows Phone 8.1 (nem Silverlight)
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0-s és 8.1 Silverlight
    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS
    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

A Python kódja egy értesítés jelenik meg a céleszközön kell előállítania.

## <a name="examples"></a>Példák:
### <a name="enabling-debug-property"></a>Debug tulajdonság engedélyezése
Ha engedélyezi a hibakeresési jelző a NotificationHub inicializálása közben, megjelenik az részletes HTTP-kérelem és válasz memóriakép, valamint NotificationOutcome a következő ahol megismerheti, milyen HTTP-fejlécek át lettek adva, a kérelem, és milyen HTTP-válasz az értesítési központ érkezett: ![][1]

Megjelenik az értesítési központ eredménye például részletes. 

* Ha az üzenet sikeresen elküldte a leküldéses értesítési szolgáltatáshoz. 
  
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
* Ha nem voltak célok megadva a leküldéses értesítésekhez található, majd valószínűleg kívánja a következő kimenet jelenik meg, a válaszban (amely azt jelzi, hogy történtek-e az értesítés valószínűleg biztosítanak, mert a regisztrációk volt néhány eltérő található regisztrációt. címkék)
  
        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Szórási Windows bejelentési értesítés
Figyelje meg a fejlécek beolvasása kiküldött, ha a Windows-ügyfélhez egy szórási bejelentési értesítést küld. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Egy tag (vagy egy címke kifejezés) megadása értesítés küldése
Figyelje meg, a címkék HTTP-fejlécet, amely lekérdezi a HTTP-kérelem fel (az alábbi példában a rendszer értesítést küld csak a "Sport" tartalom regisztrációk)

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Több címkék megadásával értesítés küldése
Figyelje meg, hogyan változik a címkék HTTP-fejléc, több címke elküldésekor. 

    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Értesítési sablon
Figyelje meg, hogy a formátum HTTP-fejléc módosítja és a tartalom törzsében van küldi el a HTTP-kérelem törzse:

**Ügyféloldali - regisztrált sablon**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";

**Kiszolgálóoldali – a tartalom küldése**

        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]

## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta, hogyan Python többi ügyfelet a Notification Hubs létrehozni. Itt a következőket teheti:

* Töltse le a teljes [Python REST burkoló minta], amely tartalmazza az ebben a cikkben a kódot.
* Folytathatja az címkézés funkciót a Notification Hubs a [Megtörje hírek oktatóanyag]
* A Notification Hubs sablonok funkcióival kapcsolatos folytathatja a [azaz híreket az oktatóanyag]

<!-- URLs -->
[Python REST burkoló minta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Get bemutató]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Megtörje hírek oktatóanyag]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[azaz híreket az oktatóanyag]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png

