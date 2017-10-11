---
title: "Az Azure Mobile Engagement hibaelhárítási útmutatója - SDK"
description: "Az Azure Mobile Engagement SDK-integráció problémák elhárítása"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: de265cf1-2f88-43ef-8616-156ada5be7b6
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4d9d6165deb4bd0c65f1841aa7c457363a1f2865
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Az SDK-integráció problémák hibaelhárítási útmutató
A következőkben lehetséges problémák merülhetnek fel az, hogy hogyan integrálja az Azure Mobile Engagement az alkalmazás.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Az alkalmazás egy másik olyan területen meghibásodása felfedezett SDK
### <a name="issue"></a>Probléma
* Felhasználói felületi adatok gyűjtése sikertelen (elemzés, figyelés, Szegmentálás vagy irányítópultok).
* Hibák Push (leküldéses értesítések nem működnek a app alkalmazást, vagy mindkettőt).
* Speciális funkció hibák (követési, földrajzi hely meghatározásának vagy adott leküldéses értesítések nem működnek platform).
* Sikertelen API (API-k sikertelen gyakran hibaüzenet nélkül csendes).
* A Szolgáltatáshibák (Azure Mobile Engagement egyike sem működik, az alkalmazás).

### <a name="causes"></a>Okok
* A legtöbb során felmerülő kérdések oldható fel az Azure Mobile Engagement SDK az alkalmazás (például a felhasználói felület gyűjtemény hiba, leküldéses hiba, speciális szolgáltatás hibája, Alkalmazásprogramozási felületében, alkalmazás-összeomlásokat, vagy nyilvánvaló szolgáltatáskimaradás) hibát fog eredményül adni.  
* Ha egy adott szolgáltatással az Azure Mobile Engagement az alkalmazás előtt soha nem működött, szüksége lesz az integráció végrehajtásához. 
* Ha az Azure Mobile Engagement egy adott szolgáltatással dolgozott, illetve leállt, szükség lehet az utolsó frissítés az Azure Mobile Engagement SDK verziójával. Ne feledje, hogy nincs-e az Azure Mobile Engagement SDK minden Azure Mobile Engagement (Android, iOS, Windows és Windows Phone) által támogatott platform egy másik verziója.

#### <a name="sdk-integration"></a>SDK-integráció
* Az Azure Mobile Engagement SDK-ban (elemzés) nincs megfelelően integrálva.
* Reach SDK (az alkalmazás és kívüli alkalmazás leküldéses értesítések) nincs megfelelően integrálva.
* A tanúsítvány lejárt vagy nem megfelelő termék vs. Fejlesztői (csak iOS esetén).
* GCM vagy ADM nem megfelelően az SDK integrált (Android csak - szolgáltatás adott leküldéses értesítések).
* Nyomon követése nem megfelelően integrált SDK (telepítés tároló nyomon követése).
* Lusta helyét vagy GPS helyét nem megfelelően integrált SDK (célcsoportkezelést által földrajzi helyhez).

**Lásd még:**

* [SDK-dokumentáció - integráció útmutatók][Link 5] 
* [Hibaelhárítási útmutató - leküldéses][Link 23]

#### <a name="sdk-upgrade"></a>SDK frissítése
* Az SDK-val (gyakran az eszköz operációs rendszer újabb verzióit kapcsolatos) a korábbi verzióival kapcsolatos problémák megoldásához SDK frissítenie kell.
* Az alkalmazás összes korábbi verziójának eltávolítása az eszközről, és telepítse újra a legújabb verzióját, az alkalmazás a regisztrálja újra az Eszközazonosítót az Azure Mobile Engagement felhasználói felülete annak ellenőrzéséhez, hogy az eszköz az alkalmazás legújabb verzióját használja.

**Lásd még:**

* [SDK-dokumentáció – kibocsátási megjegyzések](http://go.microsoft.com/fwlink/?LinkId= 525554) 
* [SDK-dokumentáció - frissítési útmutatók](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>Egyéb SDK
* Az Application Manifest "AndroidManifest.xml" hibákat okozhat, Azure Mobile Engagement nem működik (csak Android esetén).
* SDK-integrációval és API-használati egy gyakori probléma, hogy megzavarják a SDK és az API-kulcs.

**Lásd még:**

* [Alapfogalmak - szószedet][Link 6]

## <a name="advanced-coding-issues"></a>Speciális problémák kódolása
### <a name="issue"></a>Probléma
* Platform adott kód, nem közvetlenül kapcsolódik az Azure Mobile Engagement az iOS, Android és Windows Phone hibákat is okozhat.

### <a name="causes"></a>Okok
* Nem megfelelően megírt platform adott kód, nem közvetlenül kapcsolódik az Azure Mobile Engagement számos speciális az Azure Mobile Engagement kódolási probléma okozza. Szüksége lesz a platformon, Azure Mobile Engagement dokumentáció (Android, iOS, webalkalmazás, Windows és Windows Phone) mellett a fejlesztői dokumentációból.
* Nem megfelelően állítja be "kategóriák", megakadályozza, hogy egy értesítésből összekapcsolása egy másik helyen belül vagy kívül az alkalmazás (csak Android esetén). 
* Nem a beállítást "UIKit.framework" "választható", a kódban iOS "Szimbólum nem található a hiba" mutat be és/vagy a régebbi iOS-eszközök (csak iOS) összeomlik.
* Lejárt tanúsítványok, vagy nem megfelelően a cert okok fejlesztői vagy a termék verzióját használja leküldéses kapcsolatos problémákat (csak iOS esetén).
* Bizonyos korlátozások is egy platform, amely az Azure Mobile Engagement vezérlésére nem képes (ilyen például a system center működéséről az alkalmazásból leküldéses értesítések Android és iOS) beépített szolgáltatásai.
* Az Azure Mobile Engagement teszi közzé a belső csomagok által használt Azure Mobile Engagement az iOS és Android hivatkozás teljes listáját. Ne feledje, hogy az Azure Mobile Engagement néhány szolgáltatása a platformon (Android, iOS, webalkalmazás, Windows és Windows Phone).

### <a name="see-also"></a>Lásd még:
* [Hibaelhárítási útmutató - leküldéses][Link 23] 
* [SDK-dokumentáció – kibocsátási megjegyzések][Link 5]
* [SDK-dokumentáció - frissítési útmutatók][Link 5]

## <a name="application-crashes"></a>Alkalmazás-összeomlásokat
### <a name="issue"></a>Probléma
* Az alkalmazás összeomlik a végfelhasználók eszközön.

### <a name="causes"></a>Okok
* Összeomlási adatok az megtekinthetők a *Analytics felhasználói felület* vagy a *Analytics API*
* Keresse meg az Eszközazonosítót a vizsgálati eszköz, és hajtsa végre a azonos műveletet, ami miatt a végfelhasználó számára az összeomlások okának azonosításához összeomlását az alkalmazás.
* Az Azure Mobile Engagement SDK ismert problémái miatt az alkalmazások összeomlási néha megoldó az SDK legújabb verziójára. Ellenőrizze, hogy a kibocsátási megjegyzések a platformra vonatkozó összeomlások vizsgálatakor.

### <a name="see-also"></a>Lásd még:
* [SDK-dokumentáció – kibocsátási megjegyzések][Link 5]
* [SDK-dokumentáció - frissítési útmutatók][Link 5]

## <a name="app-store-upload-failures"></a>Alkalmazás-áruház sikertelen feltöltés
### <a name="issue"></a>Probléma
* Az alkalmazás legújabb verzióját feltölteni az Apple, Google vagy a Windows App store kapcsolatos hibákat.

### <a name="causes"></a>Okok
* Alkalmazás tárolja néha letiltja az alkalmazások az egyes engedélyezett funkciók (például az Apple Store áruházból megakadályozza, hogy az áruház alkalmazások idfv fogja elvégezni lehetővé használatát és a GooglePlay tároló megakadályozza, hogy az alkalmazással kapcsolatos adatok alkalmazások közötti megosztásának). 
* Győződjön meg arról, hogy ellenőrizze a kibocsátási megjegyzések a platform és az SDK jelenlegi verzióját, ha olyan alkalmazást tölt fel az áruház nehézséget.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

