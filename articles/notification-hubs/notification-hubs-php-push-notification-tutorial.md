---
title: Az Azure Notification Hubs használata php-vel
description: Ismerje meg, hogyan használhatja az Azure Értesítési központokat egy PHP háttérrendszerből.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9a77a9d9c8b2d71197089f66d81e07d56c780e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263846"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Az értesítési központok használata a PHP-ből

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Az értesítési központok összes szolgáltatását java/PHP/Ruby háttérrendszerből érheti el az Értesítési központ REST-felületén leírtak szerint az MSDN [értesítési központok REST API-jai című](https://msdn.microsoft.com/library/dn223264.aspx)témakörben leírtak szerint.

Ebben a témában megmutatjuk, hogyan:

* Rest-ügyfél létrehozása a PHP értesítési központok funkcióihoz;
* Kövesse az [Első lépések oktatóanyagot](notification-hubs-ios-apple-push-notification-apns-get-started.md) a választott mobil platformhoz, és valósítsa meg a háttérrészt a PHP-ben.

## <a name="client-interface"></a>Ügyfél kapcsolata

A fő ügyfélfelület ugyanazokat a módszereket biztosítja, amelyek a [.NET Notification Hubs SDK-ban](https://msdn.microsoft.com/library/jj933431.aspx)érhetők el, amely lehetővé teszi, hogy közvetlenül lefordítsa az összes oktatóanyagot és mintát, amely jelenleg elérhető ezen az oldalon, és hozzájárult a közösség által az interneten.

Megtalálható az összes kód elérhető a [PHP REST wrapper minta].

Például egy ügyfél létrehozása:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

IOS natív értesítés küldése:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Megvalósítás

Ha még nem tette meg, kövesse az [Első lépések oktatóanyagot] az utolsó szakaszig, ahol végre kell hajtania a háttérrendszert.
Továbbá, ha azt szeretnénk, használhatja a kódot a [PHP REST burkoló mintát,] és közvetlenül a [teljes az oktatóanyag](#complete-tutorial) szakaszban.

A teljes REST-burkoló megvalósításának minden részlete megtalálható az [MSDN-en.](https://msdn.microsoft.com/library/dn530746.aspx) Ebben a szakaszban az Értesítési központok REST-végpontjaieléréséhez szükséges főbb lépések PHP-implementációját ismertetjük:

1. Kapcsolati sztring elemzése
2. Az engedélyezési jogkivonat létrehozása
3. A HTTP-hívás végrehajtása

### <a name="parse-the-connection-string"></a>Kapcsolati sztring elemzése

Itt van az ügyfél megvalósításának fő osztálya, amelynek konstruktora elemzi a kapcsolati karakterláncot:

    ```php
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
    ```

### <a name="create-a-security-token"></a>Biztonsági jogkivonat létrehozása

A [SAS biztonsági jogkivonat létrehozásáról](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token)az Azure dokumentációjában olvashat.

Adja `generateSasToken` hozzá a `NotificationHub` metódust az osztályhoz az aktuális kérelem URI-ja és a kapcsolati karakterláncból kinyert hitelesítő adatok alapján történő létrehozásához.

    ```php
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
    ```

### <a name="send-a-notification"></a>Értesítés küldése

Először is határozzunk meg egy értesítést képviselő osztályt.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Ez az osztály egy natív értesítési törzs tárolója, vagy egy sablonértesítés esetén a tulajdonságok készlete, valamint a fejlécek készlete, amely formátumot (natív platform vagy sablon) és platformspecifikus tulajdonságokat (például Apple lejárati tulajdonságot és WNS-t tartalmaz) tartalmaz. fejlécek).

Tekintse meg az [értesítési központok REST API-k dokumentációját](https://msdn.microsoft.com/library/dn495827.aspx) és az egyes értesítési platformok formátumait az összes rendelkezésre álló lehetőségről.

Felfegyverkezve ebben az osztályban, most már írni `NotificationHub` a küldési értesítési módszerek belül az osztály:

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
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
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

A fenti módszerek http postakérést küldenek az `/messages` értesítési központ végpontjára, a megfelelő törzsgel és fejlécekkel az értesítés elküldéséhez.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Az oktatóanyag befejezése

Most már befejezheti az Első lépések oktatóanyagot egy PHP háttérrendszerértesítés elküldésével.

Az Értesítési központok ügyfélalkalmazás inicializálása (a kapcsolati karakterlánc és a hub nevének helyettesítése az [Első lépések oktatóanyagban):]

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Ezután adja hozzá a küldési kódot a célmobil platformtól függően.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Áruház és Windows Phone 8.1 (nem Silverlight)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 és 8.1 Silverlight

    ```php
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
    ```

### <a name="kindle-fire"></a>Kindle Tűz

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

A PHP-kód futtatása mostantól egy értesítést eredményez a céleszközön.

## <a name="next-steps"></a>Következő lépések

Ebben a témakörben azt mutatta, hogyan hozhat létre egy egyszerű Java REST-ügyfél értesítési központok. Ebből a menüből:

* Töltse le a teljes [PHP REST wrapper mintát], amely tartalmazza a fenti kódot.
* További ismerkedés az Értesítési központok címkézési funkciójáról a [Breaking News oktatóanyag]
* További információ az értesítések leküldése az egyes felhasználóknak a [Felhasználók értesítése bemutató]

További információ: A [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[PHP REST burkolóminta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Első lépéseket ismertető oktatóanyag]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
