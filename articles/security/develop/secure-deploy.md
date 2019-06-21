---
title: Helyezzen üzembe biztonságos alkalmazásokat a Microsoft Azure
description: Ez a cikk ismerteti az ajánlott eljárások az web projektet kiadás és válasz szakaszaiban.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144450"
---
# <a name="deploy-secure-applications-on-azure"></a>Helyezzen üzembe biztonságos alkalmazásokat az Azure-ban
Ebben a cikkben azt jelenthet biztonsági tevékenységeket és a vezérlőket, fontolja meg a felhőalapú alkalmazások központi telepítésekor. Biztonsági kérdések és fogalmak kell figyelembe venni a Microsoft release-válasz szakaszaiban [biztonságos fejlesztési Életciklussal (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) terjed ki. A célja, hogy a segítségével meghatározhatja a tevékenységeket és az Azure-szolgáltatások, amellyel biztonságosabb üzembe helyezése.

Ebben a cikkben a következő SDL fázisok terjed ki:

- Kiadás
- Válasz

## <a name="release"></a>Kiadás
A lépéseknek az ismertetése, a kiadási fázisban van readying nyilvános kiadása egy projektet.
Ez gyakorlatilag kiadás utáni karbantartási feladatok végrehajtása, és később felmerülő biztonsági biztonsági rések megszüntetését elősegítő megtervezését foglalja magában.

### <a name="check-your-applications-performance-before-you-launch"></a>Ellenőrizze, hogy az alkalmazás teljesítményében indítása előtt

Ellenőrizze, hogy az alkalmazás teljesítményében előtt indítsa el az eszközt, vagy az éles környezetben telepítheti a frissítéseket. Futtassa a felhő alapú [terheléses teszteket](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) található teljesítményproblémákat okozhat az alkalmazásban a Visual Studio használatával üzembe helyezési minőségének javítására, győződjön meg arról, hogy az alkalmazás mindig felfelé vagy nem érhető el, és, hogy az alkalmazás kezelni tudja a forgalmat, a bevezetésre.

### <a name="install-a-web-application-firewall"></a>Webalkalmazási tűzfal telepítése

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Közös ilyen jellegű támadások között a SQL-injektálási támadások és a többhelyes parancsfájlok futtatására. Ezek támadások, az alkalmazás kódjában kihívást jelenthet. Szigorúan betartandó, javítást, és az alkalmazás topológiájának több rétegeken megfigyelést igényelhet. Egy központi WAF segítségével egyszerűbbé teszik a biztonsági felügyelet. WAF-megoldás is reagálhat a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, és minden egyes webalkalmazás biztonságossá tételéhez.

A [Azure Application Gateway WAF](https://docs.microsoft.com/azure/application-gateway/waf-overview) a webalkalmazásoknak a gyakori támadások és biztonsági rések központi védelmet nyújt. A WAF-szabályai alapján a [alapvető OWASP-szabálykészletek](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0-s vagy 2.2.9-es verzióinak.

### <a name="create-an-incident-response-plan"></a>Incidensmegoldási terv létrehozása

Az incidensmegoldási terv előkészítése elengedhetetlen segítséget cím idővel előfordulhat, hogy feloldja új fenyegetéseket. Az incidensmegoldási terv előkészítése magában foglalja a megfelelő biztonsági Rendkívüli kapcsolattartók azonosításával és karbantartási tervek kódot, amely örökli a szervezet többi csoport pedig licenccel rendelkező külső kód biztonsági létrehozó.

### <a name="conduct-a-final-security-review"></a>A végső biztonsági áttekintés

Minden biztonsági tevékenység végrehajtott szándékosan megtekintésével biztosítja, hogy a készenléti a szoftver kiadás vagy-alkalmazásához. Az utolsó biztonsági felülvizsgálat (FSR Működésétől) általában magukban foglalnak modelljei eszközök kimenetek és a minőség kapuk és a követelmények szakaszban definiált hiba sávok teljesítményről vizsgálata folyamatban van.

### <a name="certify-release-and-archive"></a>Kiadás és az archív tanúsítása

Tanúsító szoftver, mielőtt kiadás biztosítja, hogy a biztonsági és adatvédelmi követelmények teljesülnek-e. Az összes kapcsolódó adatok archiválása elengedhetetlen kiadás utáni karbantartási feladatok végrehajtásához. Archiválás is segít csökkentheti a hosszú távú tartósan szoftverfőmérnöke költségeket.

## <a name="response"></a>Válasz
A válasz kiadás utáni fázist helyezi a hangsúlyt a fejlesztői csapat tudja és nem érhető el a megfelelő választ az újonnan felbukkanó fenyegetések és biztonsági rések jelentéseket.

### <a name="execute-the-incident-response-plan"></a>Hajtsa végre az incidensmegoldási terv

Képes arra, hogy a kiadási fázisában indított incidensmegoldási terv megvalósításához elengedhetetlen ügyfelek védelme a szoftver biztonsági vagy adatvédelmi biztonsági rések bontakozik ki.

### <a name="monitor-application-performance"></a>Alkalmazások teljesítményének figyelése

Folyamatos figyelés az alkalmazás, potenciálisan üzembe helyezését követően segít a teljesítménnyel kapcsolatos problémákat, valamint a biztonsági rések észlelését.
Azure-szolgáltatások, amelyek a alkalmazásfigyeléssel segíti a következők:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Az Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek, több platformon. Az élő webalkalmazásának figyelésére használhatja. Az Application Insights automatikusan felismeri a teljesítményanomáliákat. Ez magában foglalja a nagy teljesítményű analitikai eszközök segítségével diagnosztizálhatja a problémákat, és megismerheti, mit felhasználók valójában hogyan használják az alkalmazását. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

#### <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) megelőzése, észlelése, és háríthatja el a fenyegetéseket átláthatóbbá és (és szabályozhatóbbá) segít az Azure-erőforrások, például a webes alkalmazások biztonságát. Azure Security Center segítségével az egyébként észrevétlenül maradó fenyegetések észlelésére. Különféle biztonsági megoldások is működik.

A Security Center ingyenes csomag az Azure-erőforrások csak korlátozott biztonságot nyújt. A [Security Center Standard csomagja](https://docs.microsoft.com/azure/security-center/security-center-onboarding) kiterjeszti ezeket a képességeket a helyszíni erőforrásokhoz és más felhőkben.
A Security Center segítségével:

  - Megkeresheti és elháríthatja a biztonsági rések.
  - Rosszindulatú tevékenység blokkolása a hozzáférés- és alkalmazásvezérlőket vonatkoznak.
  - Fenyegetések észlelése analytics és az intelligencia használatával.
  - Támadás alatt gyorsan reagálhat.

## <a name="next-steps"></a>További lépések
Az alábbi cikkeket azt javasoljuk, hogy a biztonsági vezérlők és tevékenységek, amelyek segítségével tervezése és biztonságos alkalmazások fejlesztéséhez.

- [Biztonságos alkalmazások tervezése](secure-design.md)
- [Biztonságos alkalmazások fejlesztése](secure-develop.md)
