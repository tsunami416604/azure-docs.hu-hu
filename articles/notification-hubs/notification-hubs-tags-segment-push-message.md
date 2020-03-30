---
title: Útválasztási és címkekifejezések az Azure Értesítési központokban
description: Ismerje meg, hogyan irányíthat és címkézhet kifejezéseket az Azure Értesítési központokhoz.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062315"
---
# <a name="routing-and-tag-expressions"></a>Útválasztás és címkekifejezések

## <a name="overview"></a>Áttekintés

A címkekifejezések lehetővé teszik, hogy meghatározott eszközcsoportokat, pontosabban a regisztrációkat célozza meg, amikor leküldéses értesítést küld az értesítési központokon keresztül.

## <a name="targeting-specific-registrations"></a>Adott regisztrációk célzása

Az egyetlen módja annak, hogy célzott konkrét értesítési regisztrációk társítani címkéket velük, majd a cél ezeket a címkéket. A [regisztrációkezelés](notification-hubs-push-notification-registration-management.md), a leküldéses értesítések fogadásához az alkalmazásnak regisztrálnia kell egy eszközleírót egy értesítési központban. Miután az alkalmazás létrehoz egy regisztrációt egy értesítési központban, az alkalmazás háttérrendszer leküldéses értesítéseket küldhet hozzá. Az alkalmazás háttérrendszer kiválaszthatja a regisztrációk a cél egy adott értesítést a következő módokon:

1. **Broadcast**: az értesítési központban található összes regisztráció megkapja az értesítést.
2. **Címke**: minden olyan regisztráció, amely tartalmazza a megadott címkét, megkapja az értesítést.
3. **Címkekifejezés:** minden olyan regisztráció, amelynek címkék készlete megfelel a megadott kifejezésnek, megkapja az értesítést.

## <a name="tags"></a>Címkék

A címke lehet bármilyen legfeljebb 120 karakterből álló karakterlánc, amely alfanumerikus`_`és a`@`következő`#`nem alfanumerikus karaktereket tartalmazza: ' ' ' ' ' ' ' ' ' ' ' ', '`.`' ' ' ', '`:`', ', '`-`' A következő példa egy olyan alkalmazást mutat be, amelyből bejelentési értesítéseket kaphat bizonyos zenei csoportokról. Ebben a forgatókönyvben az értesítések egyszerű módja a regisztrációk címkézése a különböző sávokat reprezentatot képező címkékkel, az alábbi ábrán:

![Címkék – áttekintés](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Az ábrán a **Beatles-szel** jelölt üzenet csak azt a táblagépet éri el, amely a **Beatles**címkével van regisztrálva.

A címkék regisztrációinak létrehozásáról a [Regisztrációkezelés című](notification-hubs-push-notification-registration-management.md)témakörben talál további információt.

Értesítéseket küldhet a címkéknek az `Microsoft.Azure.NotificationHubs.NotificationHubClient` osztály küldési értesítési módszereivel a Microsoft Azure [Értesítési központok](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK-ban. Használhatja a Node.js vagy a Leküldéses értesítések REST API-kat is.  Íme egy példa az SDK használatával.

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

A címkéket nem szabad előre kiépíteni, és több alkalmazásspecifikus fogalomra hivatkozhat. Például, a felhasználók ebben a példában alkalmazás megjegyzést zenekarok és szeretné megkapni pirítós, nem csak a megjegyzéseket a kedvenc zenekarok, hanem az összes megjegyzést a barátok, függetlenül attól, hogy a zenekar, amelyen azok kommentálják. Az alábbi ábra egy példát mutat be erre a forgatókönyvre:

![Címkék barátok](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

Ebben a példában Alice érdeklődik a Beatles frissítései iránt, Bob pedig a Wailers frissítései iránt. Bob is érdekli Charlie észrevételeit, és Charlie érdekli a Wailers. Amikor értesítést küld Charlie-nak a Beatleshez fűzött megjegyzéséhez, az Értesítési központok elküldik alice-nek és Bobnak is.

Bár a címkékben több probléma is kódolható (például vagy `band_Beatles` `follows_Charlie`), a címkék egyszerű karakterláncok, nem pedig értékekkel rendelkező tulajdonságok. A regisztráció csak egy adott címke jelenlétében vagy hiányában egyezik meg.

A címkék érdeklődési csoportoknak való küldésére vonatkozó teljes részletes oktatóanyagról a [Breaking News (Hírek)](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)témakörben található.

> [!NOTE]
> Az Azure Notification Hubs regisztrációnként legfeljebb 60 címkét támogat.

## <a name="using-tags-to-target-users"></a>Címkék használata a felhasználók megcélzására

A címkék használatának másik módja az adott felhasználóhoz társított összes eszköz azonosítása. A regisztrációt megcímkézheti egy felhasználói azonosítót tartalmazó címkével, az alábbi ábrán látható módon:

![Felhasználók címkézése](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Az ábrán a `user_Alice` címkével ellátott üzenet `user_Alice`a .

## <a name="tag-expressions"></a>Címkekifejezések

Vannak olyan esetek, amikor az értesítéseknek olyan regisztrációkat kell megcélozniuk, amelyeket nem egyetlen címke, hanem címkéket használó logikai kifejezés azonosít.

Vegyünk egy sport alkalmazás, amely küld egy emlékeztetőt, hogy mindenki bostoni egy játék között a Red Sox és a Cardinals. Ha az ügyfélalkalmazás regisztrálja a csoportok és a hely iránti érdeklődésre vonatkozó címkéket, akkor az értesítést minden olyan bostoni nak meg kell céloznia, akit érdekel a Red Sox vagy a Cardinals. Ez a feltétel a következő logikai kifejezéssel fejezhető ki:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Címkekifejezések](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

A címkekifejezések támogatják a közös `AND` `&&`logikai operátorokat, például `OR` ( )`||`( ) és `NOT` (`!`) zárójeleket is tartalmazhatnak. A csak `OR` operátorokat használó címkekifejezések 20 címkére hivatkozhatnak; kifejezés `AND` operátorokkal, `OR` de operátorok nem hivatkozhatnak 10 címkére; ellenkező esetben a címkekifejezések legfeljebb 6 címke lehet.

Íme egy példa az SDK használatával címkekifejezésekkel rendelkező értesítések küldésére:

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
