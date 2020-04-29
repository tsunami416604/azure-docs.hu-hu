---
title: Útválasztási és címkézési kifejezések az Azure Notification Hubs
description: Megtudhatja, hogyan irányíthatja és címkézheti az Azure Notification Hubs kifejezéseit.
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
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062315"
---
# <a name="routing-and-tag-expressions"></a>Útválasztási és címkézési kifejezések

## <a name="overview"></a>Áttekintés

A címkézési kifejezések lehetővé teszik meghatározott eszközök vagy pontosabb regisztrációk megcélzását, amikor leküldéses értesítést küld Notification Hubs.

## <a name="targeting-specific-registrations"></a>Konkrét regisztrációk megcélzása

A konkrét értesítési regisztrációk célzásának egyetlen módja, ha címkéket társít velük, majd megcélozja ezeket a címkéket. A [regisztráció kezelése](notification-hubs-push-notification-registration-management.md)című cikkben leírtak szerint a leküldéses értesítések fogadásához az alkalmazásnak regisztrálnia kell egy eszköz-kezelőt az értesítési központban. Miután az alkalmazás létrehoz egy regisztrációs üzenetet egy értesítési Központon, az alkalmazás háttere leküldéses értesítéseket küldhet. Az alkalmazás-háttérrendszer a következő módokon választhatja ki, hogy mely regisztrációkat kívánja megcélozni egy adott értesítéssel:

1. **Szórás**: az értesítési központban minden regisztráció megkapja az értesítést.
2. **Címke**: a megadott címkét tartalmazó összes regisztráció megkapja az értesítést.
3. **Címke kifejezése**: minden olyan regisztráció, amelynek a címkéi a megadott kifejezésnek felelnek meg, megkapják az értesítést.

## <a name="tags"></a>Címkék

A címke lehet bármilyen karakterlánc, legfeljebb 120 karakter, amely alfanumerikus karaktereket és a`_`következő nem alfanumerikus karaktereket tartalmazza: "", "`@`"`#``.`, "", ",`:`" ","`-`". Az alábbi példa egy olyan alkalmazást mutat be, amelyről bejelentési értesítéseket kaphat az adott zenei csoportokról. Ebben az esetben az értesítések továbbításának egyszerű módja a különböző sávokat jelölő címkékkel rendelkező regisztrációk címkézése, az alábbi ábrán látható módon:

![Címkék – áttekintés](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Az ábrán a **beatlesrel** megcímkézett üzenet csak a **Beatles**címkével regisztrált tablettát éri el.

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

A címkéket nem szabad előre kiépíteni, és több alkalmazás-specifikus fogalomra is hivatkozhat. Például az alkalmazás felhasználói megjegyzéseket fűzhet a sávokhoz, és nem csak a kedvenc bandájuk megjegyzéseit, hanem a barátaik megjegyzéseit is szeretnék fogadni, függetlenül attól, hogy milyen sávban vannak észrevételeket. Az alábbi ábrán egy példa látható erre a forgatókönyvre:

![Címkék barátai](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

Ebben a példában Alice a Beatles frissítései iránt érdeklődik, és Bob a Wailers frissítései érdeklik. Bob a Charlie megjegyzéseit is érdekli, és Charlie-t érdekli a Wailers. Amikor értesítést küld a Charlie megjegyzéséhez a Beatlesben, Notification Hubs elküldi azt az Alice és a Bob számára is.

Míg a címkékben több szempontot is kódolhat (például `band_Beatles` vagy `follows_Charlie`), a címkék egyszerű karakterláncok, és nem az értékekkel rendelkező tulajdonságok. A regisztráció csak adott címke jelenlétére vagy hiányára vonatkozik.

Ha részletes oktatóanyagot szeretne arról, hogyan használhatók a címkék az érdeklődési csoportokba való küldéshez, tekintse meg a [legfrissebb híreket](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Az Azure Notification Hubs regisztráció esetén legfeljebb 60 címkét támogat.

## <a name="using-tags-to-target-users"></a>Címkék használata a felhasználók megcélzásához

A címkék használatának egy másik módja az adott felhasználóhoz társított összes eszköz azonosítása. A felhasználói azonosítót tartalmazó címkével címkézheti a regisztrációt, az alábbi ábrán látható módon:

![Felhasználók címkézése](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Az ábrán a címkével `user_Alice` ellátott `user_Alice`összes eszköz eléri a címkét.

## <a name="tag-expressions"></a>Címkézési kifejezések

Vannak olyan esetek, amikor az értesítéseknek olyan regisztrációk halmazát kell megcéloznia, amelyeket egyetlen címke sem azonosít, de a címkéket használó logikai kifejezéssel.

Vegyünk egy olyan sport-alkalmazást, amely egy, a Red Sox és a Cardinals közötti játékra emlékeztetőt küld a Bostonban mindenki számára. Ha az ügyfélalkalmazás a csoportok és a hely iránti érdeklődésre vonatkozó címkéket regisztrál, akkor az értesítéseket mindenki számára meg kell célozni, akik a Red Sox vagy a Cardinals érdeklik. Ezt az állapotot a következő logikai kifejezéssel lehet kifejezni:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Címkézési kifejezések](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

A címkézési `AND` kifejezések támogatják a közös logikai operátorokat, például (`&&`), `NOT` `!` `OR` (`||`) és (); Emellett zárójeleket is tartalmazhatnak. A csak a kezelőket használó kifejezések legfeljebb `OR` 20 címkét hivatkozhatnak. az operátorok kifejezése, de az `AND` operátorok nem `OR` hivatkozhatnak 10 címkére; Ellenkező esetben a címkézési kifejezések legfeljebb 6 címkével rendelkeznek.

Íme egy példa arra, hogy az SDK használatával hogyan küldhet értesítéseket címkézési kifejezésekkel:

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
