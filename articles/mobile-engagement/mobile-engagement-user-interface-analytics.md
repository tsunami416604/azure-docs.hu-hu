---
title: "Az Azure Mobile Engagement felhasználói felület - elemzés"
description: "Útmutató: Azure Mobile Engagement segítségével az alkalmazás előzményadatainak elemzése"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6b2533ac-b8ec-4e35-872c-d563895bdc0c
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ad05676919d6c254d60fd010c3f589f663c4745d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-analyze-historical-data-about-your-application"></a>Az alkalmazás előzményadatainak elemzése
Ez a cikk ismerteti a **ANALYTICS** lapján a **a Mobile Engagement** portálon. Használja a **a Mobile Engagement** portal felügyeletét és kezelését a mobile apps szolgáltatásban. Vegye figyelembe, hogy a portál használatának megkezdéséhez először hozzon létre egy **Azure Mobile Engagement** fiók.

A felhasználói felület Analytics szakaszból összesített információkat az alkalmazásról, 24 óránként frissül történelmi adatokon alapulnak. Az információk megjelennek a különböző irányítópultok vonal vagy sáv/tortadiagramok, hálózatok és a maps állnak. Az adatok CSV-fájlok formájában is letölthetők. Ezt az információt a legtöbb valós időben a felhasználói felület figyelő szakaszában érhető el, és azt is elérhetők az Analytics API-t.

> [!NOTE]
> Sok szakasza a **a Mobile Engagement** portál felhasználói felületének tartalmaz a **megjelenítése SÚGÓ** gombra. Nyomja le az erre a gombra kattintva szakasz környezetfüggő ismertetése.

## <a name="standard-and-custom-analytics"></a>Normál és egyéni elemzés
Az Azure Mobile Engagement biztosít alapszintű, standard TénylegesKöltség, amint az SDK az alkalmazás integrálja az alkalmazások elemzési adatait. Az Azure Mobile Engagement is lehetővé teszi további egyéni analytics információt gyűjteni, amelyet a végfelhasználók viselkedése. Ehhez hozzon létre egy egyéni "címkék (app-info)", alapján létrehozott címkézési terv **beállítások** , hogy az Azure Mobile Engagement a további adatokat gyűjthet meg.

## <a name="analytics"></a>Elemzés
* Irányítópult: Az új és beillesztené felhasználók és a trendek általános információkat jeleníti meg.
* Felhasználók: Felhasználók azonosítják az eszközazonosító: minden eszköznél egyedi (egy új felhasználó tulajdonképpen egy új eszközt). A felhasználó akkor tekinthető új egy adott időintervallumban, ha az első munkamenete az adott időszakban történt. A felhasználó akkor tekinthető megtartottnak, ha az elmúlt 7 napban legalább egy munkamenetet elvégzett. Aktív felhasználók a felhasználókat, hogy legalább egy munkamenetet egy adott időszakban. Rendezheti, havonta, hetente, nap, napi vagy óránkénti időszakok. A diagram összes hasonló, de lehetővé teszi a különböző szolgáltatások, például az alkalmazás verziójától szűrés, majd egy adott időn belül rendezés. A szabványos információkat gyűjti az adatokat az SDK integrálásával a következőket tartalmazza: az aktív felhasználók, új felhasználói, a munkamenetek számának, minden munkamenet, az ország, helyi változók, helyet, nyelvi szolgáltatónként, eszközök, belső vezérlőprogramok, kapcsolatos műszaki adatok hossza hálózati (Wi-Fi) , az alkalmazás és az SDK-t és az ügyfelek által használt verzióját. Ezek az információk is megtekinthetők valós idejű figyelése szakaszából.

> [!NOTE]
> Az adott időszakban, amelynek telefonhoz helytelenül állította be a dátum a felhasználó a megfelelő időszakban sikerült megjelenítése a felhasználói eszközbeállítások, a dátumot alapul.

* Megőrzési: A felhasználó akkor tekinthető megtartottnak egy adott időintervallumban, ha az első munkamenete az adott időszakban történt. Az időközök megtartott felhasználók (és az új felhasználók) számlálási időintervalluma módosítható óra, nap, hét és hónap. A felhasználó megőrzési analytics cohorts épül. Egy kohorszok az új felhasználók észlelt egy adott időszakra (azaz a végrehajtása az első munkamenete az ebben az időszakban felhasználók csoportja). Az 1 napos, 2 napos, 4 napos, 7 napos és 1 hónapos cohorts használjuk. Egy kohorszok megadott, minden 1 napos, 2 napos, 4 napos, 7 napos és 1 hónapos, az Azure Mobile Engagement kiszámítja a kohorszok és azok tartozó összes felhasználó készletét még aktívak (azaz a felhasználók, akik az időszak alatt legalább egy munkamenetet elvégzett beállítása). A felhasználók egy kohorszok verzió nevezzük. (Az azure Mobile Engagement láthatja, hogy hány felhasználó használják az alkalmazást, de csak a platform adott tároló segítségével megállapíthatja, hogy hány felhasználó eltávolítva az alkalmazás – például GooglePlay, iTunes, a Windows áruház, stb.).
* Munkamenetek: Az alkalmazás a felhasználó használja. Munkamenetek jönnek létre a felhasználó által végrehajtott műveletek sorozata (tevékenység általában társítva a következőhöz: az alkalmazás egy képernyőt használatát, de ez a módszer az alkalmazásba integrálva van az SDK függően változhat). A felhasználó egyszerre csak hajthat végre egy tevékenység: a munkamenet akkor kezdődik, amint a felhasználó elindítja az első tevékenységet, és befejezésével ér véget, hogy az utolsó tevékenység. Ha a felhasználó csak néhány másodpercig marad, a tevékenység végrehajtása nélkül, majd a tevékenységek sorrendjét két különböző munkamenetek egy van osztva.
* Tevékenységek: A következő képernyőkön minden képernyőn, az alkalmazás nevét, és amikor a felhasználók mennyi töltött. Tevékenységek egy egyéni analitikus beállítást, amely a saját alkalmazás beállítása "alkalmazásadatok" címkék felel meg a következők:
* Felhasználói elérési út: Mutatja, hogy a felhasználók hogyan navigálnak az alkalmazás tevékenységei (képernyői). A részletességi szint a csúszkával módosítható. Kék csomópontok jelölik a tevékenységeket az alkalmazásban. A méretük azzal arányos, hogy az idő felhasználók mennyi töltöttek velük. A fehér csomópontok a munkamenetek kezdetét és végét jelölik. Piros csomópontok jelölik a összeomlik. A nyilak az alkalmazás tevékenységei (vagy a tevékenységek és az összeomlások) közötti váltásokat jelölik. Kattintson a kívánt csomópontra vagy összekötőelemre kattintva megjelenik egy elemleírás, amely további információkkal szolgál az adatokról: a képernyőn töltött időről egy adott, a váltások számáról, valamint a forrástevékenység a céltevékenységre való váltás százalékos. (A---60 %---> B azt jelenti, hogy a B tevékenységre kerül a tevékenységről a felhasználók 60 %-ában.) A grafikon átrendezéséhez kívánt egyértelművé; minden alkalommal, amikor módosítja a rendszer menti a pozícióját. Az összeomlások (crash) elrejtésével áttekinthetőbbé teheti a grafikont.
* Események: A az alkalmazás a felhasználó által végrehajtott műveleteket adott. A terjesztési események munkamenetenként felhasználónként események száma jelenik meg. Egy esemény valamilyen azonnali műveletet, például egy gombra való kattintást vagy egy értesítés fogadását a jelöli. (Az esemény pontos jelentése függ az SDK hogyan lett integrálva az alkalmazásban.) Egy esemény egy munkamenet vagy feladat során fordulhatnak elő, vagy önállóan.
* Feladatok: Hasonló események, kivéve azokat a művelet mennyi összpontosítanak. Például feladatok volt meg, mennyi ideig tart a tartalom betöltése vagy webes szolgáltatás hívása technikai információkat. Azt is megjelenítése sikertelen, hogy mennyi ideig tart a felhasználót, hogy az űrlap kitöltése, hozzon létre egy fiókot, vagy a vásárlás. Egy feladatot a feladatok, időtartamát például egy letöltési művelet vagy időtartama a fejléc megjelenik a képernyőn. (A jelentése függ az SDK hogyan lett integrálva az alkalmazásban.) Feladatok általában olyan kapcsolódnak háttér feladatok végrehajtására kerül sor a hatókör (azaz felhasználói tevékenység nélkül) a munkameneten kívül.
* Technicals: Technikai információkat az eszközöket a felhasználók az alkalmazás követheti nyomon, például a nyelv, vivőjel, hálózati, eszköz, belső vezérlőprogramok, és képernyőn mérete a felhasználói eszközökön, és a az alkalmazás és az alkalmazásban használt SDK-verziója is.
* Hibák: Hibákkal kapcsolatos információkért műszaki az alkalmazáson belüli nem indítják el az alkalmazás összeomlik. Hiba közvetlen problémát, például hálózati meghibásodást vagy rendellenes kezelést jelent. (Az esemény pontos jelentése függ az SDK hogyan lett integrálva az alkalmazásban.) Hiba történhet munkamenet vagy feladat közben, vagy önállóan.
* Összeomlások: Miatt az alkalmazás összeomlás-hibákkal kapcsolatos információkért. Egy összeomlást (crash) nem várt állapotot, ha az alkalmazás nem képes tovább ellátni a tőle elvárt funkciókat, és le kell állítani. Egy összeomlást (crash) általában az alkalmazás hiba miatt.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>A megőrzési áttekintése elérése
![Analytics3][12]

A megőrzési áttekintése oszlanak meg a középső több kártyával, az egyes bizonyos megőrzési ideje a áttekintése látható. A példa a 2 napos megőrzési időtartam látható. A többi kártya megjelenítése a 4 napos és 7 napos megőrzési időtartamát.

## <a name="understanding-the-retention-overview-cards"></a>A megőrzési áttekintése kártyák ismertetése
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Minden egyes kártya 3 fő részből áll:
1. 1: a kohorszok és az időszak
2. 2 – 4: a jelenlegi időszakhoz megőrzési
3. 5: az előzmények értékgörbe

### <a name="here-is-detailed-information-about-each-element"></a>Minden elem részletes információkat a következő:
1. Kohorszok és időszak: A főcím ad kohorszok típusú. Itt a "2 napos időszak" azt jelenti, hogy követően áttekintjük a felhasználók viselkedésének 2 nap alatt, nap, és hogy egy meghatározott időtartamra vonatkozóan 2 érkező felhasználók csatlakozik a következő tömbökben, 2 napos. A fenti példa úgy véli, hogy a tevékenység a felhasználók a 21 és november 22nd között.
2. A megőrzési arányának a 21 és november 22, a felhasználók 19 és 20 november érkező így. Itt azt kellett között a 21 és 22nd, 1 aktív felhasználói keresztül, melyeket a 19th és 20 közötti új felhasználók a 3.
3. A vizuális kijelző ad ugyanazokat az információkat a fenti grafikusan. (A kör a harmadik pedig a 33 % számától.) A szín nyújt további információt: zöld állapot azt jelzi, hogy ez a szám az előző számítási az egyre. Sárga azt jelenti, hogy a stabil, és a piros azt jelenti, hogy csökkentése.
4. Ez azt jelzi, hogy a számításhoz használt értékek.
5. Ez az értékgörbe az előzmények megőrzési értéket. Lehetővé teszi a múltban szeretné, hogy hogyan továbbfejlődtek széleskörű nézetét értékeinek megtekintéséhez.

## <a name="see-also"></a>Lásd még:
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
