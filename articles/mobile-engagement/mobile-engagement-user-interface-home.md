---
title: "Az Azure Mobile Engagement felhasználói felület – kezdőlap"
description: "A létező alkalmazást és az Azure Mobile Engagement projektek kezelése"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0f15cb975f57f6f5cab12d5118ff50a6fab14388
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-your-existing-application-and-projects"></a>A meglévő alkalmazás és a projektek kezelése
Ez a cikk ismerteti a **Home** oldalán a **a Mobile Engagement** portálon. Használja a **a Mobile Engagement** portal felügyeletét és kezelését a mobile apps szolgáltatásban. Vegye figyelembe, hogy a portál használatának megkezdéséhez először hozzon létre egy **Azure Mobile Engagement** fiók. 

Ahhoz, hogy a kezdőlapra, kattintson a **otthoni** a bal felső részén a lapot. Az alkalmazásokat, amelyek a kiválasztott gyűjtemény részei listáját tartalmazza. Ezen az oldalon csak láthatja az alkalmazások gyors áttekintést.

A kezdőlap is tartalmaz, amelyek bármely alkalmazás, amely a fiók összes projektet. Fontos megjegyezni, hogy bárki hozzáférhet a felhasználói felület kezdőlapján hozzon létre egy fiókot, engedélyt kell biztosítania ahhoz, hogy azokat más felhasználók hozzáférhessenek az egyéni alkalmazások **projektek**.

Az összehasonlítási táblázat a kijelölt alkalmazások is megtekintheti. Vagy válassza a kijelölt alkalmazások az összehasonlítási táblázat megtekintéséhez a projektben.

![Home1][0]

## <a name="my-applications"></a>Saját alkalmazások
Az alkalmazások gyors áttekintést melyik részletes menüszalag megtekintéséhez nyissa meg a kívánt alkalmazás kiválasztását teszi lehetővé. Kattintson a nevére, az alkalmazás az alkalmazás nemrég megtekintett menüszalag helyeket, vagy kattintson a fogaskerék ikonra kattintva közvetlenül a "Beállítások" lapon, az alkalmazás. Keresés, szűrheti vagy rendezheti az alkalmazások táblák adatait. Húzza az is, és dobja el az oszlopfejlécek sorrendjének módosításához.

Többek között az alkalmazások áttekintését tartalmazza:

* **Új felhasználók trend**: alakulása az elmúlt két hétben új felhasználók.
* **Aktív felhasználók**: az elmúlt 30 napban aktív felhasználók száma.
* **Aktív felhasználók trend**: alakulása az elmúlt két hétben aktív felhasználók.
* **Munkamenetek**: A munkamenet a felhasználó által végrehajtott alkalmazás használatát, abból az időből, amikor a felhasználó elindítja a befejezéséig tart a felhasználói leáll.
* **Munkamenet trendek**: alakulása az elmúlt két hétben munkamenetek.

Után az alkalmazás kattint, megkezdheti a figyelés és a felhasználói felületen az alkalmazások kezelését. Példa:    

* [Az alkalmazással kapcsolatos valós idejű adatok figyelése](mobile-engagement-user-interface-monitor.md)
* [Az alkalmazás előzményadatainak elemzése](mobile-engagement-user-interface-analytics.md)
* [Felhasználók szegmenseinek létrehozása és kezelése a használati minták azonosításához](mobile-engagement-user-interface-segments.md)
* [Kapcsolatfelvétel a felhasználókkal leküldéses értesítések segítségével](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>Saját projektek
Projektek segítségével az alkalmazások csoport és az engedélyek más felhasználók hozzáférhessenek az alkalmazásokhoz. E-mail cím megadásával más felhasználóknak adjon engedélyeket. A **új projekt** gomb lehetővé teszi, hogy hozzon létre egy új projektet egy "name" és "description" az új projekt csak megadásával. A projekt létrehozása után kattintson a projekt nevére nevét és leírását, a termék szerkesztése, majd jelölje ki a meg szeretné tekinteni a projekt összes alkalmazást.

![Home6][60]

Szerepkörök a következők:

* **Megjelenítő**: A megjelenítő az a felhasználó, aki csak megtekintheti a projekthez tartozó alkalmazásokat. A megjelenítő analytics eléréséhez és -adatok figyelése és Reach eredmények tekintse meg. A jelentésmegjelenítő nem módosíthatja bármely adatait, és nem kezelheti az alkalmazások vagy felhasználók számára. A jelentésmegjelenítő nem hozható létre, vagy módosítsa a Reach-kampány állapota.
* **Fejlesztői**: A fejlesztői olyan felhasználó, aki elérhető műveletek mindegyikét a megjelenítő is tegye, valamint kezelheti az alkalmazásokat. Egy fejlesztő engedélyezése és alkalmazások letiltása, módosítsa az alkalmazás adatait (például a csomagok és aláírásmappák), és Reach-kampányokat. A fejlesztők a felhasználók nem tudja kezelni.
* **Rendszergazda**: egy rendszergazda a felhasználó, aki elérhető műveletek mindegyikét a fejlesztők is tegye, valamint felhasználók kezeléséhez. A rendszergazda meghívná a felhasználókat, hogy csatlakozzon a projekthez, módosíthatja a felhasználói szerepkörök, és módosíthatja a projekt információkat. Alkalmazás szintű engedélyek is beállíthatók úgy, hogy a "beállítások".

Kattintson a projekt létrehozása a projekthez tartozó összes alkalmazás megtekintése. A következő kép bemutatja az összehasonlítási táblázat a kiválasztott alkalmazásokhoz.

![Home2][3]

## <a name="see-also"></a>Lásd még:
* [Alapfogalmak][Link 6]
* [Hibaelhárítási útmutató szolgáltatás][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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
