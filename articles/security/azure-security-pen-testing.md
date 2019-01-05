---
title: Toll tesztelése |} A Microsoft Docs
description: A cikk a behatolásvizsgálatot kell végezniük (pentest) folyamat áttekintést nyújt, és hogyan hajthat végre pentest ellen az Azure-infrastruktúrában futó alkalmazások.
services: security
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 3f7d44a2a34f6b52c2229cf6491e99c65d5bb525
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052989"
---
# <a name="pen-testing"></a>Toll tesztelése
Egy alkalmazás tesztelése és üzembe helyezéséhez az Azure használatának előnyeit, hogy az képes gyorsan létrehozott környezeteket.  Nem kell aggódnia a requisitioning, beszerzése, és a "szoftverjavítási és rétegezést" a saját helyszíni hardvert.

Ez nagyszerű –, de továbbra is szeretné győződjön meg arról, hogy a normál biztonsági hajt végre, szerkezetkialakításra. A dolgot kell tennie egyik behatolási tesztelheti az alkalmazások központi telepítése az Azure-ban.

Előfordulhat, hogy már tudja, hogy elvégzi-e a Microsoft [behatolásvizsgálat, az Azure-környezet](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Ez segít a meghajtó Azure-bA funkcióhoz.

A Microsoft nem tollas tesztelheti alkalmazását az Ön számára, de tisztában vagyunk azzal is szeretné és kell elvégeznie a saját alkalmazások behatolásvizsgálatra lesz. Amely azért hasznos, mivel az alkalmazások biztonságának növelése, ha Ön segítségével biztonságosabbá teszi a teljes Azure-ökoszisztéma.

Mi a teendő ilyenkor?

2017. június 15., a Microsoft már nem igényel előzetes jóváhagyó elvégezheti egy behatolási teszteket az Azure-erőforrásokon. Formálisan dokumentum közelgő behatolástesztelést, szemben a Microsoft Azure együttműködésben azzal kivezetésre való felkészülés töltse ki a [Azure szolgáltatás behatolási tesztelés értesítés űrlap](https://portal.msrc.microsoft.com/en-us/engage/pentest). Ez a folyamat csak a Microsoft Azure, és nem vonatkozik a bármely más Microsoft-Felhőszolgáltatáshoz kapcsolódik.

>[!IMPORTANT]
>Miközben behatolásvizsgálatra tevékenységek a Microsoftnak küldött írásbeli már nem szükséges ügyfelek továbbra is meg kell felelnie, a [a Microsoft Cloud egyesített behatolási tesztelés szabályok az Engagement](https://technet.microsoft.com/mt784683).

Szabványos teszteket hajthat végre a következők:

* Tesztek alapján a végpontokon, fedje fel a [Open Web Application Security Project (OWASP) 10 biztonsági rések leggyakoribb](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Tesztelés fuzz](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) végpont
* [Portfigyelést](https://en.wikipedia.org/wiki/Port_scanner) végpont

Tesztelje, hogy nem végezhető el egy adott típusú ilyen típusú [szolgáltatásmegtagadásos (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) támadás. Ez magában foglalja egy szolgáltatásmegtagadási támadás önmagában kezdeményezésének, vagy előfordulhat, hogy határozza meg, bemutatják, vagy bármilyen típusú szolgáltatásmegtagadási támadás szimulálása kapcsolódó tesztek végrehajtása.

## <a name="next-steps"></a>További lépések

- Készen áll a kezdésre tollal teszteli a Microsoft Azure-ban üzemeltetett alkalmazások? Ha igen, akkor látogasson a el a [behatolási tesztelés szabályok az Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) és a tesztelési értesítési űrlap kitöltésekor.
