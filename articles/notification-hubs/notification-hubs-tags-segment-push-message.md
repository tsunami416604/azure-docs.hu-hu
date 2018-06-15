---
title: Az Útválasztás és címke kifejezések
description: Ez a témakör ismerteti az Azure notification hubs használatával az Útválasztás és a címke a kifejezéseket.
services: notification-hubs
documentationcenter: .net
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: e08fca0b6b57d654f2b2ff7b935f38d8c517487b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776165"
---
# <a name="routing-and-tag-expressions"></a>Az Útválasztás és címke kifejezések
## <a name="overview"></a>Áttekintés
Címke kifejezések lehetővé teszi, hogy cél adott beállítása az eszközök, vagy pontosabban regisztráció, a Notification Hubs leküldéses értesítés küldéséhez.

## <a name="targeting-specific-registrations"></a>Célcsoport-kezelési adott regisztrációk
Az egyetlen lehetőség cél adott értesítést regisztrációk rendelje hozzá a címkéket, majd célként címkék. A bemutatott [regisztrációs felügyeleti](notification-hubs-push-notification-registration-management.md), leküldéses egy értesítési központot kezelni egy alkalmazás rendelkezik kell regisztrálni egy eszközt értesítések fogadásához. A regisztráció egy értesítési központ létrehozása után az alkalmazás háttéralkalmazása küldhet leküldéses értesítést rá.
Az alkalmazás háttéralkalmazása a regisztrációk egy adott értesítés célként válasszon az alábbi módokon:

1. **Szórási**: minden regisztrációk az értesítési központban az értesítést fogadó.
2. **Címke**: a megadott kód tartalmazó összes regisztrációját az értesítést fogadó.
3. **Címke kifejezés**: az értesítést fogadó összes regisztrációját, amelynek címkék készlete megfelelnek a megadott kifejezésnek.

## <a name="tags"></a>Címkék
Karakterlánc, legfeljebb 120, alfanumerikus tartalmazó és a következő nem alfanumerikus karakterek címke lehet: "_", "@", '#', '. ',': ","-". A következő példa bemutatja egy alkalmazás, amelyből adott zene csoportok bejelentési értesítést kaphat. Ebben a forgatókönyvben egy egyszerű útvonal értesítések módja, amelyben a különböző szalagokhoz, ahogy az alábbi képen egy címke címke regisztrációk:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

A képen látható, az üzenet címkézett **Beatles** csak a tábla a címkével ellátott regisztrált eléri **Beatles**.

Címkék regisztrációinak létrehozásával kapcsolatos további információkért lásd: [regisztrációs felügyeleti](notification-hubs-push-notification-registration-management.md).

A Küldés értesítések módszerekhez címkék értesítéseket küldhet a `Microsoft.Azure.NotificationHubs.NotificationHubClient` osztályt a [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Node.js vagy a leküldéses értesítések REST API-kat is használható.  Íme egy példa a SDK használatával.

    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Címkék nem kell lennie a előtti ponton aktívvá vált, és több alkalmazás-specifikus fogalmak hivatkozhat. Példa alkalmazás felhasználók például fűzni szalagokhoz, és a kíváncsi az ismerősei, függetlenül a sávon kívüli, amelyen megjegyzések toasts, nem csak a kedvenc sávok vonatkozó megjegyzéseket, hanem az összes. Az alábbi képen látható az ebben a forgatókönyvben egy példát:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

A képen látható Ágnes a Beatles frissítései iránt érdeklődik, és Bob olyan érdeklődik a Wailers frissítései. Bob Charlie tartozó megjegyzések érdekli, amely is Charlie van a Wailers érdeklődik. A Beatles Charlie tartozó megjegyzés értesítést elküldésekor Alice és a Bob megkapni.

Több aggályokat címkéket (például "band_Beatles" vagy "follows_Charlie") is kódolására, amíg a címke található egyszerű karakterláncok és a nem értékkel rendelkező tulajdonságok. Regisztráció csak a megléte vagy hiánya, egy adott kód egyezik.

A címkék használata az érdeklődési körök alapján történő teljes részletes oktatóanyaga, lásd: [Megtörje hírek](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

## <a name="using-tags-to-target-users"></a>A cél felhasználók számára címkék használatával
Egy másik címkék használata módja, ha egy adott felhasználó összes eszköz azonosítása. Regisztráció prioritáscímkékkel való ellátását, amely tartalmazza a felhasználói Azonosítóját, ahogy az alábbi képen egy címkét:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

A képen látható, az üzenet címkézett uid: Alice eléri az összes regisztrációk címkézett uid:Alice; ezért Ágnes eszközeiről.

## <a name="tag-expressions"></a>Címke kifejezések
Előfordulhatnak olyan esetek, egy értesítés, amelyekre a regisztrációk csoportja, amely nem egy egyetlen címke, hanem egy logikai kifejezés címkéket, amelynél tartozik.

Vegye figyelembe a sport alkalmazást, amely egy emlékeztető Boston mindenki számára a vörös Sox és Cardinals közötti játék kapcsolatos. Ha az ügyfélalkalmazás regisztrálja a csoportok és a hely érdeklődik kapcsolatos címkék, majd az értesítés megcélozni Boston, akik a vörös Sox vagy a Cardinals mindenki számára. Ez a feltétel a következő logikai kifejezést lehet megadni:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Címke kifejezések például tartalmazhat összes logikai operátorok, és (& &), vagy (|), és nem (!). Kerek zárójeleket tartalmazhatnak is tartalmazhat. Címke kifejezések korlátozódnak 20 címkék csak ORs; tartalmazó Ellenkező esetben korlátozottak 6 címkék.

Íme egy példa az értesítések küldésével címke kifejezésekkel a SDK használatával.

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
