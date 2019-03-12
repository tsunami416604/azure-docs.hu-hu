---
title: Toll tesztelése |} A Microsoft Docs
description: A cikk a behatolásvizsgálatot kell végezniük (pentest) folyamat áttekintést nyújt, és hogyan hajthat végre pentest ellen az Azure-infrastruktúrában futó alkalmazások.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 8835c4534b6dab1e8dbfb3546257ae4bc3b9d7af
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588652"
---
# <a name="penetration-testing"></a>Behatolástesztelés
Egy alkalmazás tesztelése és üzembe helyezéséhez az Azure használatának előnyeit, hogy az képes gyorsan létrehozott környezeteket. Nem kell aggódnia a requisitioning, beszerzése, és a "szoftverjavítási és rétegezést" a saját helyszíni hardvert.

Ez nagyszerű –, de továbbra is szeretné győződjön meg arról, hogy a normál biztonsági hajt végre, szerkezetkialakításra. Valószínűleg szeretné dolog egyik behatolási tesztelheti az alkalmazások központi telepítése az Azure-ban.

Előfordulhat, hogy már tudja, hogy elvégzi-e a Microsoft [behatolásvizsgálat, az Azure-környezet](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Ez segít a meghajtó Azure-bA funkcióhoz.

A Microsoft nem behatolási tesztet az alkalmazás az Ön számára, de tisztában vagyunk vele, hogy fogja szeretné és a saját alkalmazás teszteléséhez kell. Amely azért hasznos, mert ha az alkalmazások biztonságának növelése érdekében, még biztonságosabbá teheti a teljes Azure-ökoszisztéma.

2017. június 15. a Microsoft már nem igényel előzetes jóváhagyó elvégezheti az Azure-erőforrásokon behatolási tesztet. Formálisan dokumentum közelgő behatolástesztelést, szemben a Microsoft Azure együttműködésben azzal kivezetésre való felkészülés töltse ki a [Azure szolgáltatás behatolási tesztelés értesítés űrlap](https://portal.msrc.microsoft.com/en-us/engage/pentest). Ez a folyamat csak a Microsoft Azure, és nem vonatkozik a bármely más Microsoft-Felhőszolgáltatáshoz kapcsolódik.

>[!IMPORTANT]
>Miközben behatolásvizsgálatra tevékenységek a Microsoftnak küldött írásbeli már nem szükséges ügyfelek továbbra is meg kell felelnie, a [a Microsoft Cloud egyesített behatolási tesztelés szabályok az Engagement](https://technet.microsoft.com/mt784683).

Szabványos teszteket hajthat végre a következők:

* Tesztek alapján a végpontokon, fedje fel a [Open Web Application Security Project (OWASP) 10 biztonsági rések leggyakoribb](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Tesztelés fuzz](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) végpont
* [Portfigyelést](https://en.wikipedia.org/wiki/Port_scanner) végpont

Tesztelje, hogy nem végezhető el egy adott típusú ilyen típusú [szolgáltatásmegtagadásos (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) támadás. Ez magában foglalja egy szolgáltatásmegtagadási támadás önmagában kezdeményezésének, vagy előfordulhat, hogy határozza meg, bemutatják, vagy bármilyen típusú szolgáltatásmegtagadási támadás szimulálása kapcsolódó tesztek végrehajtása.

## <a name="next-steps"></a>További lépések

- Ha egy közelgő behatolástesztelést végezhetnek az olyan alkalmazások, Microsoft Azure-ban üzemeltetett hivatalosan dokumentálni szeretné látogasson a el a [behatolási tesztelés szabályok az Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) és a tesztelési értesítési űrlap kitöltésekor.
