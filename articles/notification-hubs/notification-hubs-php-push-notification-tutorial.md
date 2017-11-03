---
title: "A Notification Hubs használata PHP"
description: "Útmutató az Azure Notification Hubs használata PHP háttér-a."
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 06/07/2016
ms.author: yuaxu
ms.openlocfilehash: c27b6308ff528224a0398e0ff40537db05417bb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-notification-hubs-from-php"></a>Php-ből a Notification Hubs használatával
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Minden értesítési központok szolgáltatás elérhető egy Java/PHP/Ruby-háttér használata a Notification Hub REST interfész, az MSDN-témakörben leírtak szerint [Notification hub REST API-k](http://msdn.microsoft.com/library/dn223264.aspx).

Ebben a témakörben megmutatjuk, hogyan:

* A Notification Hubs szolgáltatások PHP; a többi ügyfél létrehozása
* Kövesse a [Get bemutató](notification-hubs-ios-apple-push-notification-apns-get-started.md) a mobil választott platformhoz valósít meg a háttér-rész PHP.

## <a name="client-interface"></a>Ügyféloldali felület
A fő ügyféloldali felületen elérhető ugyanazokat a módszereket biztosít a [.NET Notification Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx), ez lehetővé teszi az oktatóanyagok és ezen a helyen aktuálisan elérhető mintákat közvetlenül fordítása, és az interneten a Közösség által közzétett.

Az összes elérhető kód megtalálhatja a [PHP REST burkoló minta].

Ha például egy ügyfél létrehozásához:

    $hub = new NotificationHub("connection string", "hubname");    

IOS natív értesítést küldeni:

    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Megvalósítás
Ha még nem tette, kövesse a [Get bemutató] akár utolsó szakasz esetében a háttér-megvalósításához.
Is, ha azt szeretné, használhatja a kódot a [PHP REST burkoló minta] és közvetlenül a [az oktatóanyag befejezése](#complete-tutorial) szakasz.

Egy teljes REST-burkoló megvalósításához a részleteit található [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Ebben a szakaszban azt ismerteti, a PHP megvalósítása a Notification hub REST-végpontok eléréséhez szükséges fő lépésből áll:

1. Kapcsolati karakterlánc elemzése
2. Az engedélyezési jogkivonat létrehozása
3. Hajtsa végre a HTTP-hívás

### <a name="parse-the-connection-string"></a>Kapcsolati karakterlánc elemzése
Az ügyfél, amelynek konstruktort, amely elemzi a kapcsolati karakterlánc végrehajtási fő osztály itt található:

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
A részleteket a biztonsági jogkivonat-létrehozási [Itt](http://msdn.microsoft.com/library/dn495627.aspx).
A következő metódusnak fel kell venni a **NotificationHub** a jogkivonat létrehozásához osztály alapján a jelenlegi kérelem és a hitelesítő adatokat a kapcsolati karakterlánc kinyert URI.

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
Először ossza meg velünk határozza meg egy értesítési képviselő osztályt.

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

Ez az osztály egy olyan tároló, a natív értesítési törzs, vagy egy sablon értesítés esettel tulajdonságait, és a fejléc formátuma (natív platform vagy sablon) és a platform-specifikus tulajdonságok (például az Apple lejárati tulajdonság és WNS fejlécekkel együtt) tartalmazó készlettel.

Tekintse meg a [Notification hub REST API-k dokumentáció](http://msdn.microsoft.com/library/dn495827.aspx) és az összes rendelkezésre álló beállítások az adott értesítési platformok formázza az adathordozót.

Volt képes ez az osztály felvértezve, azt most írhat a send notification módszerek belül a **NotificationHub** osztály.

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

A fenti módszerek HTTP POST-kérelmet küld a /messages végpont az értesítési központ, és a megfelelő törzs és -fejléceket elküldeni az értesítést.

## <a name="complete-tutorial"></a>Az oktatóanyag befejezése
Az első lépéseket bemutató oktatóanyaghoz most az értesítés küldésével a PHP háttér-hajthatja végre.

A Notification Hubs-ügyfél inicializálása (útmutatását, helyettesítse be a kapcsolati karakterlánc és a központ nevét a [Get bemutató]):

    $hub = new NotificationHub("connection string", "hubname");    

Majd adja hozzá a küldési kódot, attól függően, hogy a célként megadott mobilplatformot.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows áruház és Windows Phone 8.1 (nem Silverlight)
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

A PHP kódja kell előállítania most egy értesítés jelenik meg a céleszközön.

## <a name="next-steps"></a>Következő lépések
Ebben a témakörben azt bemutatta, hogyan hozhat létre egy egyszerű Java REST-ügyfél a Notification Hubs. Itt a következőket teheti:

* Töltse le a teljes [PHP REST burkoló minta], amely tartalmazza a fenti kódot.
* Folytathatja az értesítési központok szolgáltatás címkézés [Megtörje hírek oktatóprogram]
* További tudnivalók az értesítések küldését az egyéni felhasználók számára [felhasználók értesítése oktatóprogram]

További információ: a [PHP fejlesztői központ](/develop/php/).

[PHP REST burkoló minta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Get bemutató]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/

