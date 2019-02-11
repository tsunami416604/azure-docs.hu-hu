---
title: A Notification Hubs használata pythonnal
description: Útmutató az Azure Notification hubs szolgáltatás használata egy Python-háttérrendszert.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.author: jowargo
ms.date: 01/04/2019
ms.openlocfilehash: 10bc12879ea30f75ee5af3bd9226ff1626b72ae3
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962740"
---
# <a name="how-to-use-notification-hubs-from-python"></a>A Notification Hubs a Python használata

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Egy Java/PHP vagy Python vagy Ruby-háttérrendszert a Notification Hub REST-felület használatával, az MSDN-cikkben leírtak szerint a keresztül elérhető összes Notification Hubs szolgáltatás [Notification Hubs – REST API-k](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Ez egy minta referenciaimplementációt megvalósításához a értesítést küld a Python és nem a hivatalosan támogatott értesítések Hub Python SDK-t. A minta Python 3.4 használatával hozták létre.

Ez a cikk bemutatja, hogyan való:

- Hozhat létre REST-ügyfél, a Notification Hubs-szolgáltatások, a Python.
- A Python felhasználói felületén a Notification Hub REST API-k az értesítések küldéséhez.
- Szerezze be a HTTP REST-kérelem/válasz egy memóriakép Hibakeresés és oktatási célú.

Kövesse a [első lépéseket ismertető oktatóanyag](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) mobil a választott platformnak, megvalósítása a háttér-részét a Pythonban.

> [!NOTE]
> A minta a hatókör csak korlátozott értesítések küldéséhez, és bármely regisztrációkezelés még nem csinál.

## <a name="client-interface"></a>Ügyféloldali felület

A fő ügyféloldali felület is adja meg ugyanazokat a módszereket érhetők el a [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx). Ez az interfész lehetővé teszi, hogy közvetlenül fordítása minden az oktatóanyagaink és példáink segítségével érhető el ezen a helyen, és az interneten a Közösség által biztosított.

Az összes elérhető kódját megtalálja a [Python REST burkoló minta].

Például egy ügyfél létrehozásához:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Windows bejelentési értesítés küldése:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Megvalósítás

Ha még nem tette meg, kövesse a [első lépéseket ismertető oktatóanyag] az utolsó szakaszban, hogy a háttér-beállítása.

A részletekről a teljes REST-burkoló megvalósításához találhatók [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). Ez a szakasz ismerteti a fő lépések Notification hubs szolgáltatás REST-végpontok elérése és értesítések küldéséhez szükséges Python megvalósítása

1. Kapcsolati sztring elemzése
2. Az engedélyezési jogkivonat létrehozása
3. Egy HTTP REST API használatával értesítést küldeni

### <a name="parse-the-connection-string"></a>Kapcsolati sztring elemzése

A következő megvalósítása az ügyfelet, amelynek konstruktor elemzi a kapcsolati karakterláncot a fő osztályban:

```python
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
```

### <a name="create-security-token"></a>Biztonsági jogkivonat létrehozása

A részletekért lásd a biztonsági token létrehozása az [Itt](https://msdn.microsoft.com/library/dn495627.aspx).
Adja hozzá az alábbi módszerek a `NotificationHub` osztállyal hoz létre a token URI-ját az aktuális kérelem és a hitelesítő adatokat a kapcsolati karakterlánc kinyert alapján.

```python
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
```

### <a name="send-a-notification-using-http-rest-api"></a>Egy HTTP REST API használatával értesítést küldeni

Először is, hogy használja egy értesítést képviselő osztályt határozza meg.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm', 'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Ez az osztály egy olyan tároló, a natív értesítési törzséhez vagy egy sablon értesítés formátuma (natív platform vagy sablon) és az egyes platformokra vonatkozó tulajdonságokat (például Apple lejárati tulajdonságot és a WNS-fejlécek) tartalmazó fejlécek, készletét tulajdonságait.

Tekintse meg a [Notification Hubs – REST API-k dokumentációjában](https://msdn.microsoft.com/library/dn495827.aspx) és az adott értesítési platformok formázza az összes rendelkezésre álló lehetőségeket.

Most már ez az osztály az írási küldése értesítési módszerek belül a `NotificationHub` osztály.

```python
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

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

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

def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
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
```

Ezek a metódusok HTTP POST-kérelmet küldeni az értesítési központ, a megfelelő testület és az értesítés elküldéséhez a fejlécek /messages végpontja.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Hibakeresési tulajdonság használatával a részletes naplózás engedélyezése

Engedélyezésével debug tulajdonság az értesítési központ inicializálása során írja a részletes naplózás tájékoztatást talál a HTTP-kérelem és válasz memóriakép, valamint a részletes értesítési üzenet küldése serkenti az eredményt.
A [Notification Hubs TestSend tulajdonság](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) részletes adatait az értesítés küldési serkenti az eredményt adja vissza.
Ezzel – inicializálása a következő kód használatával:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

A Notification Hub küldése kérelem HTTP URL-cím beolvasása hozzáfűzi az "test" lekérdezési karakterlánc eredményeként.

## <a name="complete-tutorial"></a>Az oktatóanyag befejezése

Most már az első lépéseket ismertető oktatóanyag befejezéseként az értesítések küldését egy Python-háttérrendszert.

A Notification Hubs-ügyfél inicializálása (helyettesítse be a kapcsolati karakterlánc és a hub nevét a útmutatása a [első lépéseket ismertető oktatóanyag]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Majd adja hozzá a kód küldése a cél mobil platformtól függően. Ez a minta is hozzáadja a magasabb szintű módszerek platformon, például a windows; send_windows_notification küldő értesítések engedélyezése (az apple) send_apple_notification stb.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store és a Windows Phone 8.1-es (nem Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0-s és 8.1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

A Python-kód futtatása egy értesítés jelenik meg a céleszközön kell előállítania.

## <a name="examples"></a>Példák

### <a name="enabling-the-debug-property"></a>Engedélyezi a `debug` tulajdonság

A hibakeresési jelző engedélyezésekor a Értesítésiközpont inicializálása során láthatja részletes HTTP-kérés és válasz memóriakép, valamint NotificationOutcome az alábbihoz hasonló ahol megismerheti, milyen HTTP-fejlécek lesznek átadva a kérésben, és milyen HTTP-válasz az értesítési központ kapott:

![][1]

Például részletes értesítési központ eredményt látja.

- Ha az üzenet sikeresen elküldte a Push Notification Service.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Ha nem voltak célok megadva leküldéses értesítésekhez található, majd valószínűleg fog a következő eredmény látható, a válaszban (amely azt jelzi, hogy voltak-e, hogy az értesítés valószínűleg, mert a regisztrációk volt néhány eltérő található regisztrációt. címkék)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Szórási Windows bejelentési értesítése

Figyelje meg, hogy a fejlécek, amelyek küldi el a rendszer, amikor a Windows-ügyfél egy szórási bejelentési értesítést küld.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Adjon meg egy címkét (vagy címkét alkotó kifejezés) értesítés küldése

Figyelje meg, hogy a címkék HTTP-fejléc, amelyeket hozzá beolvassa a HTTP-kérelem (az alábbi példában a rendszer értesítést küld csak "Sport" hasznos adattal regisztrációk)

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Több címkék megadásával értesítés küldése

Figyelje meg, hogyan címkék HTTP-fejléc változik, ha több címkét érkeznek.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Sablonalapú értesítés

Figyelje meg, hogy a formátum HTTP-fejléc változik, és a hasznos adat szervezet zajlik a HTTP-kérés törzse részeként:

**Ügyféloldali - regisztrált sablon:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Kiszolgálóoldali – a tartalom küldése:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta, hogyan hozhat létre a Notification Hubs Python REST-ügyféllel. Itt a következőket teheti:

- Töltse le a teljes [Python REST burkoló minta], amely ebben a cikkben minden kódját tartalmazza.
- Többet szeretne megtudni a Notification Hubs címkézési funkciójával az a [Kompatibilitástörő hírek oktatóanyag]
- A Notification Hubs sablonok szolgáltatása többet szeretne megtudni a [azaz hírek oktatóanyag]

<!-- URLs -->
[Python REST burkoló minta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Első lépéseket ismertető oktatóanyag]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Kompatibilitástörő hírek oktatóanyag]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Azaz hírek oktatóanyag]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
