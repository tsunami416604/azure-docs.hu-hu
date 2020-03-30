---
title: Az Azure Notification Hubs iOS 13 frissítései | Microsoft dokumentumok
description: További információ az Azure Értesítési központokban végrehajtott módosítások iOS 13-ról
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228144"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Az Azure Notification Hubs frissítései az iOS 13-hoz

Az Apple a közelmúltban néhány változtatást hajtott végre a nyilvános push szolgáltatás; a változások többnyire az iOS 13 és az Xcode kiadásaihoz igazodnak. Ez a cikk ismerteti a változások hatását az Azure Értesítési központok.

## <a name="apns-push-payload-changes"></a>APNS leküldéses hasznos adat változások

### <a name="apns-push-type"></a>APNS leküldéses típus

Az Apple most megköveteli, hogy a fejlesztők az `apns-push-type` értesítéseket riasztásként vagy háttérértesítésként azonosítsák az APNS API új fejlécén keresztül. Az [Apple dokumentációja](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)szerint: "A fejléc értékének pontosan tükröznie kell az értesítés hasznos tartalmának tartalmát. Ha eltérés van, vagy ha a fejléc hiányzik a szükséges rendszereken, az APN-ek visszaküldhetnek egy hibát, késleltethetik az értesítés kézbesítését, vagy teljesen eldobhatják."

A fejlesztőknek most be kell állítaniuk ezt a fejlécet az Azure Notification Hubs-on keresztül értesítéseket küldő alkalmazásokban. Technikai korlátozás miatt az ügyfeleknek jogkivonat-alapú hitelesítést kell használniuk az APNS-hitelesítő adatokhoz, amelyek tartalmazzák ezt az attribútumot. Ha tanúsítványalapú hitelesítést használ az APNS-hitelesítő adatokhoz, át kell váltania a jogkivonat-alapú hitelesítés használatával.

A következő kódminták bemutatják, hogyan állíthatja be ezt a fejlécattribútumot az Azure Értesítési központokon keresztül küldött értesítési kérelmekben.

#### <a name="template-notifications---net-sdk"></a>Sablonértesítések – .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Natív értesítések – .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Közvetlen REST-hívások

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Az átmenet során, amikor az Azure Notification Hubs észleli a `apns-push-type` beállított értesítés észlelése, a szolgáltatás kikövetkezteti a leküldéses típust az értesítési kérelemből, és automatikusan beállítja az értéket. Ne feledje, hogy konfigurálnia kell az Azure Notification Hubs-ot, hogy tokenalapú hitelesítést használjon a szükséges fejléc beállításához; További információ: [Token-based (HTTP/2) Authentication for APNS](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>APNS-prioritás

Egy másik kisebb változás, de az egyik, hogy módosítani kell a háttéralkalmazás, `apns-priority` amely értesítéseket küld, az a követelmény, hogy a háttérértesítések a fejléc most kell állítani 5. Számos alkalmazás `apns-priority` 10-re állítja a fejlécet (jelezve az azonnali kézbesítést), vagy ne állítsa be, és kapja meg az alapértelmezett értéket (ami szintén 10).

Ha ezt az értéket 10-re állítja, az már nem engedélyezett a háttérértesítésekhez, ezért minden kéréshez be kell állítania az értéket. Az Apple nem küld háttérértesítéseket, ha ez az érték hiányzik. Példa:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK-módosítások

Az iOS-fejlesztők évekig a `description` `deviceToken` leküldéses jogkivonat-delegálásnak küldött adatok attribútumát használták a leküldéses jogkivonat kinyeréséhez, amelyet egy háttéralkalmazás az értesítések eszközre küldéséhez használ. Az Xcode 11-nél ez `description` az attribútum más formátumra változott. Az attribútumhoz használt meglévő kód mostantól meg van törve. Frissítettük az Azure Notification Hubs SDK-t, hogy megfeleljen ennek a változásnak, ezért frissítse az alkalmazások által használt SDK-t a 2.0.4-es vagy újabb [Azure Notification Hubs iOS SDK-ra.](https://github.com/Azure/azure-notificationhubs-ios)
