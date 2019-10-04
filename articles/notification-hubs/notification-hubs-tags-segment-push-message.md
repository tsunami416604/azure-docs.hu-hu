---
title: Útválasztási és címkézési kifejezések
description: Ez a témakör az Azure Notification hubok útválasztási és címkézési kifejezéseit ismerteti.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: 66388f139b63c63e1f0f8ee8ee063e0ddd0f9da5
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213035"
---
# <a name="routing-and-tag-expressions"></a>Útválasztási és címkézési kifejezések

## <a name="overview"></a>Áttekintés

A címkézési kifejezések lehetővé teszik meghatározott eszközök vagy pontosabb regisztrációk megcélzását, amikor leküldéses értesítést küld Notification Hubs.

## <a name="targeting-specific-registrations"></a>Konkrét regisztrációk megcélzása

A konkrét értesítési regisztrációk célzásának egyetlen módja, ha címkéket társít velük, majd megcélozja ezeket a címkéket. A [regisztráció kezelése](notification-hubs-push-notification-registration-management.md)című cikkben leírtak szerint a leküldéses értesítések fogadásához az alkalmazásnak regisztrálnia kell egy eszköz-leírót az értesítési központban. Miután létrehozta a regisztrációt egy értesítési központban, az alkalmazás háttere leküldéses értesítéseket küldhet. Az alkalmazás-háttérrendszer a következő módokon választhatja ki, hogy mely regisztrációkat kívánja megcélozni egy adott értesítéssel:

1. **Szórás**: az értesítési központban minden regisztráció megkapja az értesítést.
2. **Címke**: a megadott címkét tartalmazó összes regisztráció megkapja az értesítést.
3. **Címke kifejezése**: minden olyan regisztráció, amelynek a címkéi a megadott kifejezésnek felelnek meg, megkapják az értesítést.

## <a name="tags"></a>Tags

A címke lehet bármilyen karakterlánc, legfeljebb 120 karakter, amely alfanumerikus karaktereket tartalmaz, és a következő nem alfanumerikus karaktereket tartalmazza: _, @, #,., ': ', '-'. Az alábbi példa egy olyan alkalmazást mutat be, amelyről bejelentési értesítéseket kaphat az adott zenei csoportokról. Ebben a forgatókönyvben az értesítések továbbításának egyszerű módja a különböző sávokat jelölő címkékkel rendelkező regisztrációk címkézése, ahogy az alábbi képen is látható:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Ebben a képen a címkézett **Beatles** üzenet csak a **Beatles**címkével regisztrált tablettát éri el.

A címkék regisztrációjának létrehozásával kapcsolatos további információkért lásd: [regisztráció kezelése](notification-hubs-push-notification-registration-management.md).

Értesítéseket küldhet a címkékre a `Microsoft.Azure.NotificationHubs.NotificationHubClient` [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK osztály küldési értesítések metódusának használatával. A Node. js-t vagy a leküldéses értesítések REST API-jait is használhatja.  Íme egy példa az SDK használatával.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

A címkéket nem kell előzetesen kiépíteni, és több alkalmazás-specifikus fogalomra is hivatkozhatnak. Például az alkalmazás felhasználói megjegyzéseket fűzhet a sávokhoz, és nem csak a kedvenc bandájuk megjegyzéseit, hanem a barátaik megjegyzéseit is szeretnék fogadni, függetlenül attól, hogy milyen sávban vannak észrevételeket. Az alábbi képen egy példa látható erre a forgatókönyvre:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Ebben a képen Alice érdekli a Beatles frissítései, és a Wailers frissítései érdeklik. Bob is érdekli Charlie megjegyzéseit, és Charlie a Wailers érdekli. Ha a Beatles-megjegyzésre vonatkozó értesítést küld a Charlie-nak, akkor Alice és Bob is megkapja.

Míg a címkékben több szempontot is kódolhat (például "band_Beatles" vagy "follows_Charlie"), a címkék egyszerű karakterláncok, és nem az értékekkel rendelkező tulajdonságok. A regisztráció csak adott címke jelenlétére vagy hiányára vonatkozik.

Ha részletes oktatóanyagot szeretne arról, hogyan használhatók a címkék az érdeklődési csoportokba való küldéshez, tekintse meg a [legfrissebb híreket](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Az Azure Notification Hubs regisztráció esetén legfeljebb 60 címkét támogat.

## <a name="using-tags-to-target-users"></a>Címkék használata a felhasználók megcélzásához

A címkék használatának egy másik módja egy adott felhasználó összes eszközének azonosítása. A regisztrációk egy felhasználói azonosítót tartalmazó címkével is megcímkézhető, ahogy az alábbi képen is látható:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Ebben a képen a címkézett UID üzenet jelenik meg: Alice eléri az összes "UID: Alice" címkét. Ezért az Alice összes eszköze.

## <a name="tag-expressions"></a>Címkézési kifejezések

Vannak olyan esetek, amikor az értesítéseknek olyan regisztrációkat kell megcéloznia, amelyeket nem egyetlen címke azonosít, de a címkéken logikai kifejezéssel.

Vegyünk egy olyan sport-alkalmazást, amely egy, a Red Sox és a Cardinals közötti játékra emlékeztetőt küld a Bostonban mindenki számára. Ha az ügyfélalkalmazás a csoportok és a hely iránti érdeklődésre vonatkozó címkéket regisztrál, akkor az értesítéseket mindenki számára meg kell célozni, akik a Red Sox vagy a Cardinals érdeklik. Ezt az állapotot a következő logikai kifejezéssel lehet kifejezni:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

A címkézési kifejezések minden logikai operátort tartalmazhatnak, például a (z) és (& &), vagy (| |), és nem (!). Emellett zárójeleket is tartalmazhatnak. A címkézési kifejezések legfeljebb 20 címkével rendelkeznek, ha csak Örs-et tartalmaznak. Ellenkező esetben 6 címkére van korlátozva.

Az alábbi példa egy, az SDK-val címkézett kifejezéseket használó értesítések küldését szemlélteti.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
