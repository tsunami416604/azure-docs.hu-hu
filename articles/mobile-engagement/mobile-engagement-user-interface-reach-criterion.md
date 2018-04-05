---
title: Az Azure Mobile Engagement felhasználói felület - a Reach feltétel
description: Azure Mobile Engagement segítségével a felhasználók egy kijelölt részét leküldéses kampányokra küldendő célcsoport-kezelési feltételek használata
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 2adf473c6acea0f128eb14e2616748ff29d5d762
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>A felhasználók egy kijelölt részét leküldéses kampányokra küldendő célcsoport-kezelési feltételek használata
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 

A célközönség által meghatározott feltételeket az "Új feltételek" gombbal célzó a egyik leghatékonyabb az Azure Mobile Engagement, hogy a megfelelő küldünk segít leküldéses értesítések, hogy az ügyfelek válaszol levélszeméttel mindenki helyett. Korlátozza a célközönséget, a szabványos feltételek alapján, és szimulálása a leküldéses értesítések annak meghatározásához, hányan a értesítést fog kapni.

**Lásd még:**

* [Leküldéses kampány új UI - a Reach - dokumentáció][Link 27]

## <a name="audience-criteria-can-include"></a>Célközönségre vonatkozó feltételeket az alábbiakból állhat:
* ** Technicals: ** célba láthatja az elemzés és a figyelő szakaszok azonos technikai információk alapján. **További információ:** [felhasználói felületének dokumentációja – Analytics][Link 15], [felhasználói felületének dokumentációja - figyelő][Link 16]
* **Hely:** alkalmazásokat, amelyek Geokerítéseket "a hely jelentéskészítési valós idejű" használata földrajzihely-kritériumokként alkalmazhatja a, amelyekre a közönség a GPS-helyről. "Lusta hely jelentéskészítési" hívás, amelyekre a mobiltelefon helyről közönség is használható ("a hely jelentéskészítési valós idejű" és "Lusta terület hely jelentéskészítési" aktiválni kell az SDK-ból). **További információ:** [SDK-dokumentáció - iOS - integráció][Link 5], [SDK-dokumentáció - Android - integráció][Link 5]
* **Reach-visszajelzés:** célba a célközönséget, a korábbi reach értesítések keresztül közlemények, szavazások és Adatleküldések reach visszajelzései visszajelzései alapján. Ez lehetővé teszi nagyobb cél a célközönséget után két vagy három kampányok elérni, mint Ön volt az első alkalommal. Is használható kiszűrését a felhasználók, akik már úgy, hogy a kampány nem küldi el a felhasználók, akik egy adott előző kampány már megkapta a hasonló tartalmú értesítést kapott. Akkor is kizárhat a felhasználók számára tartalmazza egy adott kampányt, amely a még aktív az új leküldéses értesítések fogadása. **További információ:** [UI - a Reach - dokumentáció leküldéses tartalom][Link 29]
* **A telepítéskövetési:** nyomon követheti a felhasználók az alkalmazást futtató alapulnak. **További információ:** [felhasználói felületének dokumentációja – beállítások][Link 20]
* **Felhasználói profil:** akkor is a célként megadott általános jogú felhasználói adatok alapján, és a cél az Ön által létrehozott egyéni alkalmazásadat alapján is. Ez magában foglalja a jelenleg bejelentkezett felhasználók és a felhasználók kell őket beállítani a maga az alkalmazás helyett csak hogyan azok válaszolt előző kampányokra kérték adott kérdések válaszolni. Minden alkalmazás adatához definiálva az alkalmazás megjelennek a listában.
* Szegmensek: Is alapján létrehozott szegmensek cél több feltételt tartalmazó felhasználói viselkedés alapján. A szegmenseket, az alkalmazás definiált összes jelenik meg, a listában. **További információ:** [felhasználói felületének dokumentációja – szegmensek][Link 18]
* **Alkalmazásadat:** egyéni App-Info címkék a felhasználó viselkedésének nyomon a "beállítások" hozhatók létre. **További információ:** [felhasználói felületének dokumentációja – beállítások][Link 20]

## <a name="example"></a>Példa:
Ha azt szeretné, amelyekkel közlemény csak az alárendelt készletét, amely egy alkalmazáson belüli vásárlás műveletet hajtott végre, a felhasználók a.

1. Nyissa meg az alkalmazás beállításait tartalmazó lap, válassza ki a "Alkalmazásadatok" menüt, és válassza az "Új app-info"
2. Egy új "inAppPurchase" nevű logikai alkalmazásadatok regisztrálása
3. Ellenőrizze az alkalmazás beállítása "igaz" Ha a felhasználó sikeresen elvégzi az alkalmazáson belüli vásárlás az alkalmazásadatok (használatával a sendAppInfo ("inAppPurchase",...) függvény)
4. Ha nem szeretné, ehhez az alkalmazásról, ezt megteheti a háttérrendszerből az eszköz API használatával)
5. Ezután egyszerűen hozzon létre egy olyan feltételt, korlátozza a célközönséget, a felhasználók számára, hogy "inAppPurchase" értékre a hirdetmény "true")

> [!NOTE]
> Azure Mobile Engagement összegyűjteni a felhasználók eszközéről, a leküldéses elküldése előtt, és így lelassulhat célzó eltérő app-info címkék feltétel alapján szükséges. Leküldéses értesítések összetett leküldéses konfigurációs beállítások (például a jelvényt frissítése) is késleltetheti-e. A leküldéses API "egy hibaüzenetet" kampány használata az Azure Mobile Engagement abszolút leggyorsabb ügyfélleküldéses módszer. A következő leggyorsabb eljárás csak app-info címkék használatával leküldéses feltételként a Reach-kampány (vagy Reach API vagy a felhasználói felület) azért, mert az app-info címkék tárolja a kiszolgáló oldalán. A leginkább rugalmas, de a leghosszabb ügyfélleküldéses módszer más célcsoport-kezelési feltételek alapján a leküldéses kampány részeként, mivel Azure Mobile Engagement csak a kampány küldéséhez az eszközök lekérdezésére.

![Reach-Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Feltétel beállításai vonatkoznak:
* **Technicals**     
* Belső vezérlőprogram name: belső vezérlőprogram neve
* Belső vezérlőprogram-verziója: belső vezérlőprogram-verziója
* Eszköz típusa: eszközmodell
* Eszköz gyártója: eszköz gyártója
* Alkalmazás-verzió: alkalmazás verziója
* Portvívő név: szolgáltatónként neve nincs definiálva
* Portvívő ország: szolgáltatónként ország, nincs definiálva
* Hálózati típusa: hálózati típusa
* Területi beállítás: területi beállítás
* Képernyő méretéhez: képernyő méretéhez
* **Hely**      
* Utolsó ismert terület: ország, régió, helység szerint.
* Valós idejű geokerítések: lista a Poi (név, műveletek), a körkörös POI (név, szélesség hosszúság, Radius a mérőszámok)
* **Visszajelzés elérni**     
* Bejelentés visszajelzés: közlemény, visszajelzés
* Kérdezze le visszajelzését: lekérdezési, visszajelzés
* Kérdezze le a válasz-visszajelzés: kérdezze le a válasz visszajelzést, a kérdés, a választott
* Adatok leküldéses visszajelzés: az Adatleküldés, visszajelzés
* **A telepítéskövetési**     
* Tároló: Tároló nincs definiálva
* Forrás: Forrás nincs definiálva
* **Felhasználói profil**     
* Nemét: csatlakozó vagy nő, nincs definiálva
* Dátum születése: kezelő, dátum, nincs definiálva
* Részt: IGAZ vagy hamis, nem definiált
* **Alkalmazásadatok**      
* Karakterlánc: Karakterlánc, nincs definiálva
* : Operátor, dátuma nincs definiálva
* Egész szám: operátor, számot, nincs definiálva
* Logikai érték: IGAZ vagy hamis, nincs definiálva
* **Szegmens**    
* (A legördülő listából) szegmensek neve (felhasználók megcélzása, akik nem tartozik ebbe a szegmensbe) kizárási.

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
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

