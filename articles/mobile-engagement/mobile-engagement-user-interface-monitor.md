---
title: Az Azure Mobile Engagement felhasználói felület - figyelő
description: További tudnivalók az Azure Mobile Engagement segítségével alkalmazással kapcsolatos valós idejű adatok figyelése
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b91ad89a-b89d-4377-abb0-cc2d16a2836d
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b370a5db1acee37ce234f5ec3f004be20719cfea
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-monitor-real-time-data-about-your-application"></a>Az alkalmazással kapcsolatos valós idejű adatok figyelése
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ez a cikk ismerteti a **FIGYELŐ** lapján a **a Mobile Engagement** portálon. Használja a **a Mobile Engagement** portal felügyeletét és kezelését a mobile apps szolgáltatásban. Vegye figyelembe, hogy a portál használatának megkezdéséhez először hozzon létre egy **Azure Mobile Engagement** fiók. 

A figyelő a szakasz a felhasználói felület valós idejű elemzési információkat nyújt, és lehetővé teszi riasztások beállítása a küszöbértékek elérésekor a legtöbb pedig ugyanazok az adatok rendelkezésre álló korábban az a [ANALYTICS](mobile-engagement-user-interface-analytics.md) szakasza a felhasználói felületen. Tekintse meg a **szószedet** szakasz a [fogalmak](http://go.microsoft.com/fwlink/?LinkId=525555) definíciójának kifejezések és rövidítések elemzés és -figyelő a témakör (például a következő: aktív felhasználói, új felhasználó, a felhasználó őrzi meg, a munkamenet, a felhasználói elérési útja Graph, felhasználók térkép, követési URL-címek, trendeket, tevékenység, esemény, feladat, hiba, további információ, összeomlási és App-info).

> [!NOTE]
> Sok szakasza a **a Mobile Engagement** portál felhasználói felületének tartalmaz a **megjelenítése SÚGÓ** gombra. Nyomja le az erre a gombra kattintva szakasz környezetfüggő ismertetése.
> 
> 

## <a name="monitor---sessions-jobs-events-errors-and-crashes"></a>A figyelő - munkamenetek, feladatok, eseményeket, hibákat, és az összeomlások
Láthatja, hogy hány felhasználó jelenleg munkamenet és adott képernyők, vagy konkrét műveletek során a rendszer. Felhasználói tevékenység munkamenetek, feladatok, eseményeket, hibákat, és az összeomlások osztva tekintheti meg. Tekintse meg az aktuális információkat, és az utolsó óra, nap vagy hét adatainak megjelenítése. Minden egyes kategóriát vagy a rendezési információt adott munkamenet, feladat, esemény, hiba, és összeomlási tekintheti meg.  Élő figyelési akkor hasznos, ha van egy uptick működés közben jobb a leküldéses értesítés elküldése után esemény, például a leküldéses kampány során használandó.

![Monitor1][14]  

## <a name="troubleshooting-with-monitor---events---details"></a>Hibaelhárítás az figyelő - események - részletek
Generál egy eseményt az alkalmazás a vizsgálati eszköz, és hogy a figyelő - események - részletek az egyik legegyszerűbb módja az Eszközazonosítót található a vizsgálati eszköz, és győződjön meg arról, hogy Azure Mobile Engagement-integráció Analytics, figyelés, és Az alkalmazásból szegmensek működik. Miután az eszköz azonosítója a vizsgálati eszköz, hozzáadhatja a vizsgálati eszközök "Fiók – eszközök". Ha az esemény nem hozható létre, győződjön meg arról, hogy az SDK az Android vagy iOS/webes/Windows/Windows Phone-alkalmazás megfelelően integrált Azure Mobile Engagement.

További információkért lásd: [SDK-dokumentáció][Link 5]

![Monitor2][15]  

## <a name="troubleshooting-with-monitor---crashes---details"></a>Hibaelhárítás az figyelő - összeomlik - részletek
Összeomlási információkat annak meghatározásához, hogy az alkalmazás összeomló miért - összeomlás - figyelő adatait az alkalmazásra vonatkozó tekintheti meg. Az SDK a kibocsátási megjegyzésekben egyes verzióihoz készült Android vagy iOS/webes/Windows/Windows Phone SDK minden verziójának ismert problémái is kell meg.

További információkért lásd: [SDK-dokumentáció – kibocsátási megjegyzések][Link 5]

![Monitor3][16]

## <a name="monitor---alerts"></a>Monitor - riasztás
Riasztások, automatikusan küldjön el Önnek e-mailben vagy csevegőüzenetekkel feltételei is megadható. (Minden XMPP-kompatibilis szolgáltatás például a Google GTalk vagy az Apple iChat támogatottak.) Riasztások egy előre definiált kártevőészlelés küszöbértéket (>) nagyobb vagy kisebb, mint (<) egy bizonyos számú munkamenetek, feladatok, eseményeket, hibákat vagy összeomlik / másodpercben, perc vagy óra alapulnak. Riasztások figyelése egy adott típusú összes tevékenységet, vagy csak figyelése egy adott feladat, esemény vagy hiba. 

A minimális azonosítási időköz, amely beállítás perc alatt, győződjön meg arról, hogy a riasztás aktiválása esetén soha nem értesítést fog kapni 1-nél több / megadott időköz az egyazon riasztáshoz tartozó két értesítés közötti időtartamot is megadható.

![Monitor4][17]

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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
