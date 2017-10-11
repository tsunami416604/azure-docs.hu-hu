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
ms.date: 08/24/2017
ms.author: yurid
ms.openlocfilehash: 070e848f753452953b9e5dfe94799e7c0a314530
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="pen-testing"></a>Toll tesztelése
Alkalmazástesztelési és a központi telepítés a Microsoft Azure használatával kapcsolatban a nagyszerű dolog, nem szükséges hozzáfoghat fejlesztéséhez, tesztelése és az alkalmazások központi telepítése a helyszíni infrastruktúra. Az infrastruktúra van a Microsoft Azure platform szolgáltatásaiból által végrehajtott kezeli. Nem kell aggódnia requisitioning, beszerzése, és "lefejtési és halmozott" a saját helyszíni hardverre.

Ez remek –, de továbbra is szeretné győződjön meg arról, hogy a normál biztonsági elvégezhető megfelelő gondossággal. Az egyikét kell tennie a behatolást vagy a biztonság tesztelje az alkalmazásokat, központi telepítése az Azure-ban.

Előfordulhat, hogy már tudja, hogy elvégzi a Microsoft [behatolást vagy a biztonság, az Azure környezetben végzett teszteléséhez](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Ez segít javítani a platformot, és végig is vezeti a műveletek biztonsági vezérlők javítása, új biztonsági ellenőrzéseire bemutatása és a biztonsági folyamatok javítása.

Jelenleg nem toll meg az alkalmazás teszteléséhez, de azt szeretné, hogy és tesztelése a saját alkalmazásai toll végrehajtandó fog megértését. Amely azért hasznos, mert az alkalmazások biztonságának növeléséhez, amikor segítséget biztonságosabbá teszi a teljes Azure-ökoszisztéma.

Amikor Ön toll tesztelje az alkalmazásokat, azt nézhet ki például a támadás a számunkra. A Microsoft [folyamatosan figyelni](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) a támadási mintákat és fogja elindítani az incidensválasz-folyamata, ha igazolnia kell a. Azt nem oldották meg, és azt nem segítsen nekünk, ha azt indít az incidensekre adott reakciók, a megfelelő gondossággal toll teszteléssel miatt.

Mi a teendő ilyenkor?

Amikor készen áll a toll tesztelje az Azure által kezelt alkalmazásokat, lehetősége van egy [ossza meg velünk](https://portal.msrc.microsoft.com/en-us/engage/pentest). Miután tudjuk, hogy meghatározott tesztek végrehajtását fogja, azt nem véletlenül leállítás meg (például blokkolja a tesztelést IP-cím), mindaddig, amíg a feltételek tesztelés Azure toll megfelelnek a teszteket, és feltételek ismertetett [Microsoft felhő egyesített behatolást vagy a biztonság tesztelés szabályok az Engagement](https://technet.microsoft.com/en-us/mt784683).
Szabványos teszteket hajthat végre a következők:

* A végpontokat, hogy a vizsgálatok a [megnyitása webes alkalmazás biztonsági Project (OWASP) felső 10 biztonsági réseket](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Tesztelési fuzz](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) a végpontok
* [Port vizsgálatát](https://en.wikipedia.org/wiki/Port_scanner) a végpontok

Teszt nem végezhető el több típus ilyen típusú [szolgáltatásmegtagadásos (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) támadás. Ez magában foglalja a kezdeményezésének egy szolgáltatásmegtagadási támadás önmagában, vagy előfordulhat, hogy határozza meg, bemutatása vagy szolgáltatásmegtagadási támadás bármilyen típusú szimulálása kapcsolódó tesztek végrehajtása.

Készen áll arra, ismerkedjen meg toll tesztelése a Microsoft Azure szolgáltatásban üzemeltetett alkalmazásokat? Ha igen, akkor a központi a keresztül történő a [behatolást vagy a biztonság teszt áttekintése](https://technet.microsoft.com/library/mt784683.aspx) lapon (és kattintson az létrehozása egy tesztelési kérelem gombra az oldal alján. További információ a feltételek és hogyan lehet jelentést Azure vagy bármely más Microsoft szolgáltatással kapcsolatos biztonsági hiányosságokat hasznos mutató hivatkozások tesztelés toll is találhat.
