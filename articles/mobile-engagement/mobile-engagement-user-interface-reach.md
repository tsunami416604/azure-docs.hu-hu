---
title: Az Azure Mobile Engagement felhasználói felület - a Reach
description: Megtudhatja, hogyan érheti el az alkalmazás a felhasználók az Azure Mobile Engagement leküldéses értesítéseket
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d999b83df7d9d467f08ce8ec72468c738e8acfa5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Hogyan érheti el a felhasználókkal leküldéses értesítések
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ez a cikk ismerteti a **ELÉRNI** lapján a **a Mobile Engagement** portálon. Használja a **a Mobile Engagement** portal felügyeletét és kezelését a mobile apps szolgáltatásban. Vegye figyelembe, hogy a portál használatának megkezdéséhez először hozzon létre egy **Azure Mobile Engagement** fiók. További információkért lásd: [Azure Mobile Engagement-fiók létrehozása](mobile-engagement-create.md).

A felhasználói felület Reach szakasza a leküldéses kampány felügyeleti eszköz, ahol létrehozása/szerkesztése/aktiválása/Befejezés/figyelő, a következőket teheti, és megtekintheti a statisztikákat a leküldéses értesítési kampányt, és a Reach API (és néhány eleme az alacsony szintű Push API) keresztül is elérhető szolgáltatások . Ne feledje, hogy függetlenül attól, hogy az API-k vagy a felhasználói felület, szüksége lesz integrálni Azure Mobile Engagement és a használata előtt az SDK-val az alkalmazásba, az egyes platformokon Reach elérését a kampányok.

> [!NOTE]
> Sok szakasza a **a Mobile Engagement** portál felhasználói felületének tartalmaz a **megjelenítése SÚGÓ** gombra. Nyomja le az erre a gombra kattintva szakasz környezetfüggő ismertetése.
> 
> 

## <a name="four-types-of-push-notifications"></a>Leküldéses értesítések négy típusa
1. Közlemények - lehetővé teszi, hirdetések küldéséhez felhasználók számára, amelyek átirányítja őket egy másik hely belül az alkalmazás vagy egy weblapot, vagy kívül az app store el őket. 
2. Szavazások - lehetővé teszi információ gyűjtését a végfelhasználók által kérdések kéri a felhasználót.
3. Adatleküldések - teszik bináris vagy base64 adatok fájl küldése. Található adatokat küld az alkalmazás aktuális élmény a felhasználók az alkalmazás módosításához. Az alkalmazás kell tudni feldolgozni az adatokat található adatokat.

## <a name="campaign-details"></a>Kampány adatai
Szerkesztése, klónozzon, törlése, és aktiválja a kampányt, amely rendelkezik még nincs aktiválva a nevek fölé vagy kattintva nyissa meg ezeket. Már aktiválták a nevek fölé kampányok klónozhat, vagy a megnyitásukhoz gombra. Azonban a kampány nem módosítható, miután aktiválva lett.

![Reach1][18]

## <a name="reach-feedback"></a>Visszajelzés elérni
Kattintson a **statisztika** a Reach-kampány a részletek megtekintéséhez. A **egyszerű** nézet nyújt arról, mi történt egy kampány aktiválása után oszlop oszlopdiagramon formájában vizuális megjelenítése. A **speciális** nézet tartalmazza a leküldéses kampány részletesebb adatait. Ezek az adatok nem lesz elérhető, ha egy teszt kampány azaz küld egy vizsgálati eszköz leküldéses. Itt látható, hogyan kell értelmezni ezeket az adatokat:

1. **Leküldött** -azt határozza meg az eszközökre leküldött üzenetek száma. Ez a szám a leküldéses kampány létrehozásához a megadott célközönség függ. Ha nem adja meg a célközönség, majd a leküldéses fog el lehet küldeni a regisztrált eszközöket. Minden más leküldéses szolgáltatások, például a Microsoft nem leküldéses értesítéseket közvetlenül az eszközök számára, de ehelyett azok leküldése az adott platform adott leküldéses értesítéseket kezelő szolgáltatása (PNS - APNS/GCM/WNS), hogy ezek az értesítések által biztosított eszközök. 
2. **I** -azt határozza meg sikeresen érhetők el a pns-sel, hogy az eszköz és a nyugtázott üzenetek száma, mint a Mobile Engagement SDK által fogadott. 
   
   *A kézbesített okait száma kevesebb, mint a megnyomott száma:*
   
   1. Ha a felhasználó rendelkezik eltávolítja az alkalmazást az eszközről, de a pns-sel kapcsolatos nem ismeri a leküldéses nem küldeni a pns-sel időpontjában az üzenetet a program eltávolítja.
   2. Ha az eszköz regisztrációját az alkalmazást, de a eszközöktől hosszabb ideig offline állapotban volt, a pns-sel meghiúsul kézbesíteni az üzenetet az eszközt. 
   3. Ha az üzenet beolvasása juttatni az eszközre, de az alkalmazásban a Mobile Engagement SDK nem ismeri fel az üzenet tartalmát, majd esik az üzenet. Ez akkor fordulhat elő, ha az alkalmazásban az értesítés testreszabási hoz létre egy kivételt, amely azt a catch az SDK-t, és dobja el az üzenetet. Ez akkor is előfordulhat, ha az alkalmazást az eszközön a Mobile Engagement SDK, amely nem érti a platformnak a leküldéses üzenet újabb verziójában a verzióját használja, de ez csak akkor, ha az alkalmazás a t az értesítés elküldése után frissítették. He service platform. A **speciális** lapon megtudhatja, hogy hány üzenetek el lettek dobva. 
   4. Az iOS-eszközökön üzenetek néha nem kézbesítve vagy az eszköz az alacsony töltöttségű telepre vonatkozó vagy ha az alkalmazás nem használ jelentős részét, távoli értesítések feldolgozása közben. Ez az iOS-eszközök korlátozása.   
3. **Megjelenített** -azt határozza meg, amelyek sikeresen jelennek meg az értesítési központba leküldéses/out-az-app rendszerértesítőként vagy belül a mobilalkalmazás az alkalmazásbeli értesítések formájában az eszközön az alkalmazás felhasználói üzenetek száma.  A **speciális** lapon megtudhatja, hány volt rendszer értesítéseket és alkalmazáson belüli értesítések. 
   
   *A megjelenő okait száma kevesebb, mint a kézbesített száma (megjelenítendő várakozási)*
   
   1. Az értesítési kampány záródátumot volt, majd esetén előfordulhat, hogy az értesítési lett kézbesítve, de a idő kapott megnyitásához, és megjeleníti azt az alkalmazás felhasználó számára, amikor azt már elévült, a rendszer soha nem jelenik meg.   
   2. Esetén az értesítés az alkalmazáson belüli értesítések a notification csak akkor látható, ha az alkalmazás felhasználó megnyitja az alkalmazást. Azokban az esetekben, ahol az alkalmazás felhasználói nem nyitotta meg az alkalmazást az SDK jelentést, hogy az értesítés-i, de még nem jelenik meg az alkalmazás már meg van nyitva. 
   3. Ha az értesítést az alkalmazáson belüli értesítést, és akkor is megjelenik az értesítés, szállított, de még nem kézbesíteni, amikor a felhasználó egy adott tevékenység képernyőn megjelenítendő konfigurált megnyitja az alkalmazás az adott képernyőn. 
4. **Felhasználói kapcsolati** -azt határozza meg, amely az alkalmazás felhasználói kezelni rendelkezik üzenetek száma és az üzeneteket, amelyek műveletet kiváltó, illetve amelyekből kiléptek. 
   
   * *Az alkalmazás felhasználói a következő módokon reagálhat egy értesítést az alábbi módon:*
     
     1. Ha az értesítési rendszer/out-az-app értesítést vagy egy csak értesítést küldött, majd az alkalmazás felhasználó kattint az értesítés az alkalmazáson belüli értesítés.
     2. Ha az értesítést az alkalmazáson belüli értesítést a szöveges vagy webes-nézetet, vagy majd lekérdezi az alkalmazás felhasználó kattint az akciógombra kattinthat, az értesítés.
     3. Ha az értesítést az alkalmazáson belüli értesítést a web-nézetet majd az alkalmazás felhasználó a hivatkozásra kattint a webes nézet [Android csak] egy URL-cím
   * *Az alkalmazás felhasználói a következő módokon léphet egy értesítést az alábbi módon:*
     
     1. Közvetlenül az értesítés Bezárás gombjára kattintva. 
     2. Lehúzni számítógépnél, vagy törölje az értesítést. 
     3. Alkalmazáson belüli értesítések szöveges vagy webes tartalom és szavazások esetén általában jelennek meg az alkalmazás felhasználói egy kétlépéses folyamat. Akkor jelenik meg értesítés először, és ha azok kattintson rá, akkor jelenik meg a következő szöveges/webes/lekérdezési tartalmat. Az alkalmazás felhasználói a következő módokon léphet egy értesítést az alábbi lépéseket, és a Speciális nézetben részleteit ez rögzíti. 
5. **Műveletet kiváltó** -azt határozza meg, amely az alkalmazás felhasználó explicit módon műveletet kiváltó üzenetek száma. Ez az a a legérdekesebb, ez alapján hány alkalmazás felhasználók által az értesítési leküldött üzenet volt interested. 

> [!NOTE]
> IOS & Windows nyissa meg a platformok, ha a felhasználó rendelkezik-e az alkalmazás és a kampány lett egy "Bármikor" kampány, akkor lehetséges, hogy mindkét kívül és alkalmazáson belüli értesítések jelennek meg egyszerre. Emiatt a magasabb, mint a kézbesített megjelenített számát. Ha a felhasználó kommunikál esetleg műveletek az értesítés, akkor még a felhasználó kapcsolati/Actioned száma nagyobb, mint a kézbesített. 
> 
> 

![Reach2][19]

## <a name="see-also"></a>Lásd még
* [Alapfogalmak][Link 6]
* [Hibaelhárítási útmutató szolgáltatás][Link 24]

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

