---
title: Az Útválasztás és címke kifejezések
description: Ez a témakör ismerteti az Azure notification hubs használatával az Útválasztás és a címke a kifejezéseket.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: f52900d06049623a0a81e509abdc5e9ef76f95e5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451476"
---
# <a name="routing-and-tag-expressions"></a>Az Útválasztás és címke kifejezések

## <a name="overview"></a>Áttekintés

Címke kifejezések lehetővé teszik adott csoportok cél az eszközök, vagy még pontosabban regisztrációk, ha a Notification Hubs használatával leküldéses értesítések küldéséhez.

## <a name="targeting-specific-registrations"></a>Célcsoport-kezelési adott regisztrációk

A cél egyetlen módja a adott értesítési regisztrációk az, hogy címkéket társíthat, majd célként ezen címkék. Az [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md), leküldéses értesítéseket egy alkalmazáshoz tartozik az eszköz regisztrálása egy értesítési központ kezelni fogadásához. A regisztráció egy értesítési központ létrehozása után az alkalmazás háttérrendszerével küldhet leküldéses értesítéseket rá.
Az alkalmazás háttérrendszerével választhat a regisztrációk egy adott értesítés a cél a következő módon:

1. **Szórási**: az értesítési központ összes regisztrációk az értesítés fogadásához.
2. **Címke**: a megadott kód tartalmazó összes regisztrációk az értesítés fogadásához.
3. **Címkét alkotó kifejezésbe**: címkék, amelynek beállítása a megadott kifejezésnek megfelelő összes regisztrációk az értesítés fogadásához.

## <a name="tags"></a>Címkék

Címke lehet bármely karakterlánc, legfeljebb 120 karakter, amely alfanumerikus és a következő nem alfanumerikus karakterek: '_', "@", '#', '. ',': ","-". Az alábbi példa bemutatja egy alkalmazás, amelyből adott zene csoportokról bejelentési értesítéseket fogadhat. Ebben az esetben az értesítések átirányításához legegyszerűbb címke regisztrációk címkék, amelyek a különböző sávok, ahogy az alábbi képen is látható:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

A képen látható, az üzenet címkézett **Beatles** eléri a csak a címkével ellátott regisztrált a táblagépes **Beatles**.

Regisztrációs címkék létrehozásával kapcsolatos további információkért lásd: [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md).

A küldési értesítések módszereivel címkékre értesítéseket küldhet a `Microsoft.Azure.NotificationHubs.NotificationHubClient` osztály a [a Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK-t. Használhatja a Node.js vagy a leküldéses értesítések REST API-kat is.  Íme egy példa az SDK-val.

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

Címkék nem kell előre kiépített, és több alkalmazás-specifikus fogalmakat is hivatkozhat. Ez a példa az alkalmazás felhasználói például megjegyzéseket a szalagokon, és a szeretne kapni a toasts, nem csak a kedvenc sávok hozzászólásainak, hanem az összes ismerősei, függetlenül a sávon kívüli megjegyzéseket, amelyen a. A következő képen látható az ebben a forgatókönyvben egy példát mutat be:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

A képen látható Ágnes a Beatles frissítései iránt, és Bob olyan frissítéseket a Wailers iránt. Bob is Charlie a megjegyzések iránt, és Charlie van érdekelné a Wailers. A Beatles Charlie a megjegyzést a küldött értesítést, Alice és a Bob megkapni.

Miközben kódolhatja több problémáit a címkéket (például "band_Beatles" vagy "follows_Charlie"), a címkék olyan egyszerű karakterláncok és a nem értékkel rendelkező tulajdonságok. A regisztráció csak a, illetve az adott címkével hiánya egyezik.

Címkék használata a érdeklődési körök küldését teljes lépésenkénti oktatóanyagot, tekintse meg a [híreket használhatatlanná tévő](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

## <a name="using-tags-to-target-users"></a>A célként megadott felhasználók számára címkék használatával

A címkék használatának másik módja, hogy egy adott felhasználó összes eszköz azonosítása. Regisztrációk is címkézni egy címke, amely tartalmazza a felhasználói azonosító, a következő képen látható módon:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

A képen látható az üzenet címkézett uid: Alice eléri az összes regisztrációk címkézett "uid:Alice"; ezért összes Alice eszközt.

## <a name="tag-expressions"></a>Címke kifejezések

Előfordulhatnak olyan esetek, egy értesítés, amelyekre nem egyetlen címkében, hanem a címkéket egy logikai kifejezés által azonosított regisztrációk csoportja van.

Fontolja meg egy sport-alkalmazás, amely emlékeztetőt küld azoknak a piros Sox és Cardinals közötti játék kapcsolatos Bostonban mindenki számára elérhető. Ha az ügyfélalkalmazás regisztrálja a címkék kapcsolatos érdeklődés teams és a helyet, majd az értesítési kell megcélozni mindenki számára, akik a piros Sox vagy a Cardinals Bostonban. Ez az állapot a következő logikai kifejezést lehet megadni:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Címke kifejezések például tartalmazhatja az összes logikai operátorokat, és (& &), vagy (|), és nem (!). Zárójelek között is tartalmazhatnak. Címke kifejezések korlátozódnak 20 címkék csak ORs; tartalmazó Ellenkező esetben ezek korlátozva, 6 címkék.

Íme egy példa az értesítések küldéséhez az SDK-val címke kifejezésekkel.

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
