---
title: "Toll tesztelése |} Microsoft Docs"
description: "A cikk áttekintést a behatolást vagy a biztonság tesztelése (pentest) folyamat, és hogyan hajtsa végre az Azure-infrastruktúra futó alkalmazások elleni pentest."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: 5f2a72d133218e72d1db86b787c998cc50269d3a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="pen-testing"></a>Toll tesztelése
Az Azure alkalmazás tesztelése és üzembe helyezés előnyei egyik is gyorsan ki, hogy létre környezetekben.  Nem kell aggódnia requisitioning, beszerzése, és "lefejtési és halmozott" a saját helyszíni hardverre.

Ez remek –, de továbbra is szeretné győződjön meg arról, hogy a normál biztonsági elvégezhető megfelelő gondossággal. Az egyikét kell tennie a behatolást vagy a biztonság tesztelje az alkalmazásokat, központi telepítése az Azure-ban.

Előfordulhat, hogy már tudja, hogy elvégzi a Microsoft [behatolást vagy a biztonság, az Azure környezetben végzett teszteléséhez](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Ez segítséget nyújt az Azure fejlesztései meghajtó. 

Jelenleg nem toll meg az alkalmazás teszteléséhez, de azt szeretné, hogy és tesztelése a saját alkalmazásai toll végrehajtandó fog megértését. Amely azért hasznos, mert az alkalmazások biztonságának növeléséhez, amikor segítséget biztonságosabbá teszi a teljes Azure-ökoszisztéma.

Mi a teendő ilyenkor?

2017. június 15. frissítésétől Microsoft többé nem kell a behatolást vagy a biztonság elvégzésére előtti jóváhagyási tesztek Azure-erőforrások ellen. Az ügyfelek, akik hivatalosan dokumentum jövőbeli behatolást vagy a biztonság tesztelése a Microsoft Azure elleni kapcsolattartás során szeretné hosszúan töltse ki a [Azure szolgáltatás behatolást vagy a biztonság tesztelés értesítési űrlap](https://portal.msrc.microsoft.com/en-us/engage/pentest). Ez a folyamat csak kapcsolódik a Microsoft Azure és a nem alkalmazható egyéb felhőalapú Microsoft-szolgáltatást. 

>[!IMPORTANT] 
>Amíg értesítésére toll tevékenységek tesztelése a Microsoft már nem szükséges az ügyfelek továbbra is megfeleljen a [Microsoft felhő egyesített behatolást vagy a biztonság tesztelés szabályok az Engagement](https://technet.microsoft.com/en-us/mt784683). 

Szabványos teszteket hajthat végre a következők:

* A végpontokat, hogy a vizsgálatok a [megnyitása webes alkalmazás biztonsági Project (OWASP) felső 10 biztonsági réseket](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Tesztelési fuzz](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) a végpontok
* [Port vizsgálatát](https://en.wikipedia.org/wiki/Port_scanner) a végpontok

Teszt nem végezhető el több típus ilyen típusú [szolgáltatásmegtagadásos (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) támadás. Ez magában foglalja a kezdeményezésének egy szolgáltatásmegtagadási támadás önmagában, vagy előfordulhat, hogy határozza meg, bemutatása vagy szolgáltatásmegtagadási támadás bármilyen típusú szimulálása kapcsolódó tesztek végrehajtása.

## <a name="next-steps"></a>További lépések

- Készen áll arra, ismerkedjen meg toll tesztelése a Microsoft Azure szolgáltatásban üzemeltetett alkalmazásokat? Ha igen, akkor a központi a keresztül történő a [behatolást vagy a biztonság teszt áttekintése](https://technet.microsoft.com/library/mt784683.aspx) lapon (és kattintson az létrehozása egy tesztelési kérelem gombra az oldal alján. 