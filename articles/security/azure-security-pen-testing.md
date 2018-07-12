---
title: Toll tesztelése |} A Microsoft Docs
description: A cikk a behatolásvizsgálatot kell végezniük (pentest) folyamat áttekintést nyújt, és hogyan hajthat végre pentest ellen az Azure-infrastruktúrában futó alkalmazások.
services: security
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: a64316eda25bd02f89b5afdd7b98c0193381d023
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970819"
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
* [Tesztelés fuzz](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) végpont
* [Portfigyelést](https://en.wikipedia.org/wiki/Port_scanner) végpont

Tesztelje, hogy nem végezhető el egy adott típusú ilyen típusú [szolgáltatásmegtagadásos (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) támadás. Ez magában foglalja egy szolgáltatásmegtagadási támadás önmagában kezdeményezésének, vagy előfordulhat, hogy határozza meg, bemutatják, vagy bármilyen típusú szolgáltatásmegtagadási támadás szimulálása kapcsolódó tesztek végrehajtása.

## <a name="next-steps"></a>További lépések

- Készen áll a kezdésre tollal teszteli a Microsoft Azure-ban üzemeltetett alkalmazások? Ha igen, akkor látogasson a el a [behatolási teszt áttekintése](https://technet.microsoft.com/library/mt784683.aspx) oldalon (és kattintson a létrehozás egy tesztelési kérelem gombot a lap alján. 
