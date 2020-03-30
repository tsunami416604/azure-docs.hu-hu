---
title: APNS VOIP-értesítések küldése az Azure Értesítési központtal
description: Ismerje meg, hogyan küldhet APNS VOIP-értesítéseket az Azure Értesítési központokon keresztül (hivatalosan nem támogatott).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146887"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Az APNS VOIP használata az értesítési központokon keresztül (hivatalosan nem támogatott)

Az APNS VOIP-értesítések az Azure Értesítési központokon keresztül is használhatók; ehhez a forgatókönyvhöz azonban nincs hivatalos támogatás.

## <a name="considerations"></a>Megfontolandó szempontok

Ha továbbra is úgy dönt, hogy az APNS VOIP-értesítéseket az értesítési központokon keresztül küldi el, vegye figyelembe az alábbi korlátozásokat:

- VoIP-értesítés küldéséhez `apns-topic` a fejlécet az alkalmazáscsomag-azonosító `.voip` + az utótag számára kell beállítani. Például egy kötegazonosítóval `com.microsoft.nhubsample`rendelkező mintaalkalmazás `apns-topic` esetén a fejlécet úgy kell beállítani, hogy`com.microsoft.nhubsample.voip.`

   Ez a módszer nem működik jól az Azure Notification Hubs, mert az alkalmazás csomagazonosítóját kell konfigurálni a hub APNS hitelesítő adatait, és az érték nem módosítható. Emellett az értesítési központok nem engedélyezik a `apns-topic` fejléc értékének felülbírálását futásidőben.

   VoIP-értesítések küldéséhez egy külön értesítési `.voip` központot kell konfigurálnia az alkalmazáscsomag-azonosítóval.

- A VOIP-értesítés `apns-push-type` elküldéséhez a fejlécet a parancsra kell `voip`állítani.

   Az iOS 13-ra való áttérés érdekében az Értesítési központok `apns-push-type` megkísérli kikövetkeztetni a fejléc helyes értékét. A következtetési logika szándékosan egyszerű, annak érdekében, hogy elkerüljék a szokásos értesítések megszegése. Sajnos ez a módszer problémákat okoz a VOIP-értesítésekkel kapcsolatban, mivel az Apple a VOIP-értesítéseket olyan különleges esetként kezeli, amely nem követi ugyanazokat a szabályokat, mint a szabványos értesítések.

   VoIP-értesítések küldéséhez meg kell adnia `apns-push-type` egy explicit értéket a fejléchez.

- Az Értesítési központok az APNS-rakományokat 4 KB-ra korlátozza, az Apple által dokumentáltak szerint. A VOIP értesítések esetében az Apple legfeljebb 5 KB-os hasznos terhet engedélyez. Az értesítési központok nem tesznek különbséget a szabványos és a VOIP-értesítések között; ezért minden bejelentés legfeljebb 4 KB.

   VoIP-értesítések küldéséhez nem lépheti túl a 4 KB-os hasznos adatmennyiség-korlátot.

## <a name="next-steps"></a>További lépések

További információk az alábbi hivatkozásokra kattintva érhetők el:

- [Dokumentáció `apns-topic` és `apns-push-type` fejlécek és értékek, beleértve a VOIP-értesítések különleges eseteit.](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)

- [A hasznos teher méretkorlátjának dokumentációja](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Az értesítési központok frissítései az iOS 13 rendszerhez.](push-notification-updates-ios-13.md#apns-push-type)
