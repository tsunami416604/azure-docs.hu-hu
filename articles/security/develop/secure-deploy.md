---
title: Biztonságos alkalmazások telepítése a Microsoft Azure-ban
description: Ez a cikk ismerteti a webes alkalmazás projekt kiadási és válaszfázisai során figyelembe vesszen ajánlott eljárásokat.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934889"
---
# <a name="deploy-secure-applications-on-azure"></a>Biztonságos alkalmazások üzembe helyezése az Azure-ban
Ebben a cikkben bemutatjuk a biztonsági tevékenységek és vezérlők, amelyeket figyelembe kell venni, amikor alkalmazásokat telepít a felhőbe. A Microsoft [security development lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) kiadási és válaszfázisai során figyelembe veendő biztonsági kérdések és fogalmak. A cél az, hogy segítsen meghatározni a tevékenységek et és az Azure-szolgáltatásokat, amelyek segítségével egy biztonságosabb alkalmazás üzembe helyezéséhez.

Ez a cikk a következő SDL fázisokat ismerteti:

- Kiadás
- Válasz

## <a name="release"></a>Kiadás
A kiadási fázis középpontjában egy projekt nyilvános kiadásra való előkészítése áll.
Ez magában foglalja a kiadás utáni karbantartási feladatok hatékony elvégzésének és a később fellépő biztonsági rések kezelésének módjait.

### <a name="check-your-applications-performance-before-you-launch"></a>Az alkalmazás teljesítményének ellenőrzése az indítás előtt

Ellenőrizze az alkalmazás teljesítményét, mielőtt elindítana, vagy telepíti a frissítéseket éles környezetben. A felhőalapú [terhelési teszteket](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) a Visual Studio segítségével futtathatja az alkalmazás teljesítményproblémáinak megkereséséhez, javíthatja a központi telepítés minőségét, győződjön meg arról, hogy az alkalmazás mindig rendelkezésre áll vagy elérhető, és hogy az alkalmazás képes kezelni az indítási forgalmat.

### <a name="install-a-web-application-firewall"></a>Webalkalmazás-tűzfal telepítése

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Ezek között gyakori a biztonsági rések közé tartozik az SQL injection támadások és a helyek közötti parancsfájlok támadások. Az alkalmazáskódban elkövetett támadások megelőzése kihívást jelenthet. Előfordulhat, hogy az alkalmazás topológiájának számos rétegében szigorú karbantartást, javítást és figyelést igényel. A központi WAF megkönnyíti a biztonságkezelést. A WAF-megoldások úgy is reagálhatnak a biztonsági fenyegetésre, hogy egy ismert biztonsági rést egy központi helyen javítanak, és minden egyes webalkalmazás tetsző biztonsági rezsije.

Az [Azure Application Gateway WAF](../../application-gateway/waf-overview.md) központosított védelmet nyújt a webalkalmazások a gyakori biztonsági rések és biztonsági rések. A WAF az OWASP 3.0 vagy 2.2.9-es [alapszabálykészletének](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) szabályain alapul.

### <a name="create-an-incident-response-plan"></a>Incidensmegoldási terv létrehozása

Az incidenselhárítási terv elkészítése elengedhetetlen ahhoz, hogy segítsen kezelni az okat az új fenyegetéseket, amelyek idővel előfordulhatnak. Az incidens-elhárítási terv előkészítése magában foglalja a megfelelő biztonsági vészhelyzeti kapcsolattartók azonosítását, valamint a szervezet más csoportjaitól örökölt kód és a harmadik fél től származó licencelt külső kódok biztonsági karbantartási terveinek létrehozását.

### <a name="conduct-a-final-security-review"></a>A biztonsági felülvizsgálat végleges vizsgálata

Az elvégzett összes biztonsági tevékenység szándékos áttekintése segít biztosítani a szoftverkiadás vagy -alkalmazás felkészültségét. A végső biztonsági felülvizsgálat (FSR) általában magában foglalja a fenyegetésmodellek, az eszközök kimenetének és a teljesítmény vizsgálatát a minőségi kapuk és hibasávok ellen, amelyeket a követelmények fázisában határoztak meg.

### <a name="certify-release-and-archive"></a>Kiadás és archiválás hitelesítése

A szoftver kiadás előtti hitelesítése segít a biztonsági és adatvédelmi követelmények betartásának biztosításában. A vonatkozó adatok archiválása elengedhetetlen a kiadás utáni karbantartási feladatok elvégzéséhez. Az archiválás segít csökkenteni a tartós szoftverfejlesztéssel kapcsolatos hosszú távú költségeket is.

## <a name="response"></a>Válasz
A kiadás utáni válasz fázis a fejlesztői csapat azon központja, hogy képes és elérhető a szoftverfenyegetésekés biztonsági résekről szóló jelentések megfelelő reagálására.

### <a name="execute-the-incident-response-plan"></a>Az incidens-választerv végrehajtása

A kiadási fázisban bevezetett incidens-választerv megvalósításának alapvető fontosságú az ügyfelek szoftverbiztonsági vagy adatvédelmi biztonsági rések elleni védelméhez.

### <a name="monitor-application-performance"></a>Alkalmazások teljesítményének figyelése

Az alkalmazás folyamatos figyelése a telepítés után potenciálisan segít a teljesítménybeli problémák és a biztonsági rések észlelésében.
Az alkalmazások figyelését segítő Azure-szolgáltatások a következők:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Az Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető alkalmazásteljesítmény-kezelési (APM) szolgáltatás több platformon a webfejlesztők számára. Az élő webalkalmazásának figyelésére használhatja. Az Application Insights automatikusan észleli a teljesítményanomáliákat. Hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és annak megértésében, hogy a felhasználók valójában mit csinálnak az alkalmazással. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

#### <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](../../security-center/security-center-intro.md) segítségével megelőzheti, észlelheti és reagálhat a fenyegetésekre, és nagyobb rálátással (és az okra) az Azure-erőforrások biztonságának szabályozásával, beleértve a webalkalmazásokat is. Az Azure Security Center segít észlelni azokat a fenyegetéseket, amelyek egyébként észrevétlenek maradnak. Különböző biztonsági megoldásokkal működik.

A Security Center ingyenes szintje csak az Azure-erőforrások korlátozott biztonságát kínálja. A [Security Center standard szint](../../security-center/security-center-onboarding.md) kiterjeszti ezeket a képességeket a helyszíni erőforrásokra és más felhőkre.
A Security Center Standard a következőket segíti:

  - Biztonsági rések keresése és javítása.
  - A kártékony tevékenységek blokkolása érdekében alkalmazás- és alkalmazásvezérlőket alkalmazhat.
  - A fenyegetések észlelése elemzésés intelligencia segítségével.
  - Gyorsan reagáljon, ha támadás alatt áll.

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben olyan biztonsági vezérlőket és tevékenységeket javasoljuk, amelyek segítségével biztonságos alkalmazásokat tervezhet és fejleszthet.

- [Biztonságos alkalmazások tervezése](secure-design.md)
- [Biztonságos alkalmazások fejlesztése](secure-develop.md)
