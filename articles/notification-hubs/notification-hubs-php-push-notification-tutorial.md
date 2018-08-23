---
title: A Notification Hubs használata PHP
description: Útmutató az Azure Notification hubs szolgáltatás használata egy PHP-háttérrendszert.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: b812d60363ffebf1f4374b6fd44dff5e67497e08
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "42059472"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Php-ből a Notification Hubs használatával
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Összes Notification Hubs szolgáltatás egy Java/PHP vagy Ruby-háttérrendszert a Notification Hub REST-felület használatával, az MSDN-témakörben ismertetett módon érheti el [Notification Hubs – REST API-k](http://msdn.microsoft.com/library/dn223264.aspx).

Ebben a témakörben bemutatjuk, hogyan lehet:

* A Notification Hubs-szolgáltatások, a PHP; REST-ügyfél létrehozása
* Kövesse a [első lépéseket ismertető oktatóanyag](notification-hubs-ios-apple-push-notification-apns-get-started.md) mobil a választott platformnak, megvalósítása a háttér-részét a PHP.

## <a name="client-interface"></a>Ügyféloldali felület
A fő ügyféloldali felület is adja meg ugyanazokat a módszereket érhetők el a [.NET Notification Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx), amely lehetővé teszi, hogy közvetlenül fordítása minden az oktatóanyagaink és példáink segítségével érhető el ezen a helyen, és százalékaránya a közösségi az interneten.

Az összes elérhető kódját megtalálja a [A PHP REST burkoló minta].

Például egy ügyfél létrehozásához:

    $hub = new NotificationHub("connection string", "hubname");    

Natív értesítés küldéséhez egy IOS-es:

    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Megvalósítás
Ha még nem tette meg, kövesse a [első lépéseket ismertető oktatóanyag] az utolsó szakaszban, hogy a háttér-beállítása.
Is, ha azt szeretné, használhatja a kódot az a [A PHP REST burkoló minta] , és lépjen közvetlenül a [az oktatóanyag elvégzéséhez](#complete-tutorial) szakaszban.

A részletekről a teljes REST-burkoló megvalósításához találhatók [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Ebben a szakaszban ismertetünk, a Notification Hubs REST-végpontokat eléréséhez szükséges fő lépések PHP végrehajtása:

1. Kapcsolati sztring elemzése
2. Az engedélyezési jogkivonat létrehozása
3. A HTTP-hívás végrehajtása

### <a name="parse-the-connection-string"></a>Kapcsolati sztring elemzése
A következő megvalósítása az ügyfelet, amelynek konstruktort, amely elemzi a kapcsolati karakterláncot a fő osztályban:

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>Biztonsági jogkivonat létrehozása
A részletekért lásd a biztonsági token létrehozása az [Itt](http://msdn.microsoft.com/library/dn495627.aspx).
A következő metódust még hozzá kell adni a **Értesítésiközpont** osztállyal hoz létre a token URI-ját az aktuális kérelem és a hitelesítő adatokat a kapcsolati karakterlánc kinyert alapján.

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>Értesítés küldése
Első ossza meg velünk adjon meg egy értesítést képviselő osztályt.

    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }

Ez az osztály egy olyan tároló, egy natív értesítés szövegét, vagy a kis-és a egy sablon értesítés tulajdonságait, és beállított egy fejlécek, formátumban (natív platform vagy sablon) és az egyes platformokra vonatkozó tulajdonságokat (például Apple lejárati tulajdonságot, és a WNS tartalmazó fejlécek).

Tekintse meg a [Notification Hubs – REST API-k dokumentációjában](http://msdn.microsoft.com/library/dn495827.aspx) és az adott értesítési platformok formázza az összes rendelkezésre álló lehetőségeket.

Ez az osztály képességekkel, is most írunk küldése értesítési módszerek belül a **Értesítésiközpont** osztály.

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

A fenti módszerek HTTP POST-kérelmet küldeni az értesítési központ, a megfelelő testület és az értesítés elküldéséhez a fejlécek /messages végpontja.

## <a name="complete-tutorial"></a>Az oktatóanyag befejezése
Az első lépéseket ismertető oktatóanyag befejezéseként most az értesítés küldését a PHP háttér.

A Notification Hubs-ügyfél inicializálása (helyettesítse be a kapcsolati karakterlánc és a hub nevét a útmutatása a [első lépéseket ismertető oktatóanyag]):

    $hub = new NotificationHub("connection string", "hubname");    

Majd adja hozzá a kód küldése a cél mobil platformtól függően.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store és a Windows Phone 8.1-es (nem Silverlight)
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0-s és 8.1 Silverlight
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

A PHP-kód futtatásával kell előállítania most egy értesítés jelenik meg a céleszközön.

## <a name="next-steps"></a>További lépések
Ebben a témakörben megmutattuk, hogyan hozhat létre egy egyszerű Java REST-ügyfél a Notification hubs szolgáltatásra. Itt a következőket teheti:

* Töltse le a teljes [A PHP REST burkoló minta], amely tartalmazza a fenti kódrészlettel.
* Többet szeretne megtudni a Notification Hubs címkézési funkciójával az [híreket használhatatlanná tévő oktatóanyagban]
* További információ az értesítések leküldése az egyéni felhasználók számára [– felhasználók értesítése az oktatóanyagban]

További információ: a [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/).

[A PHP REST burkoló minta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Első lépéseket ismertető oktatóanyag]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/

