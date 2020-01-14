---
title: Az Azure Security teljesítményteszt bemutatása
description: Biztonsági teljesítményteszt bemutatása
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 3169959250eb45346456c64e606f25efb0ea44f0
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934231"
---
# <a name="azure-security-benchmark-introduction"></a>Az Azure Security teljesítményteszt bemutatása

A helyszíni számítástechnika több évet vagy akár több évtizedes tapasztalatot is igénybe vehet. Tudja, hogyan védheti meg ezeket az üzembe helyezéseket; a felhő azonban eltér. Honnan tudhatja, hogy a felhőben üzemelő példányok biztonságosak-e? Mi a különbség a helyszíni rendszerek és a Felhőbeli üzemelő példányok biztonsági eljárásai között?

A felhő biztonságossá tételéhez nagy mennyiségű tanulmány, ajánlott eljárás, hivatkozási architektúra, webes útmutató, nyílt forráskódú eszközök, kereskedelmi megoldások, intelligencia-hírcsatornák és egyéb információk találhatók. Melyik beállítást érdemes használni? Mit tehet a Felhőbeli biztonság elfogadható szintjének eléréséhez? 

A Felhőbeli üzemelő példányok biztonságossá tételének egyik legjobb módja a Felhőbeli biztonsági teljesítménytesztekre vonatkozó javaslatok bevezetése. A szolgáltatásokhoz való hozzáférésre vonatkozó teljesítményteszt-javaslatok a kiberbiztonsági kockázatának és kezelésének alapvető ismeretével kezdődnek. Ezt az értelmezést használhatja, ha a felhőalapú szolgáltató által meghatározott biztonsági konfigurációs beállításokat szeretne kiválasztani a környezetében. 

Az Azure biztonsági teljesítményteszt olyan nagy hatású biztonsági javaslatokat tartalmaz, amelyek segítségével biztonságossá tehető az Azure-ban használt szolgáltatások nagy része. Ezeket a javaslatokat "általános" vagy "szervezeti" néven tekintheti meg, mivel azok a legtöbb Azure-szolgáltatásra alkalmazhatók. Az Azure biztonsági teljesítménytesztekre vonatkozó javaslatokat ezután minden egyes Azure-szolgáltatáshoz testre kell szabni, és ez a testreszabott útmutató a szolgáltatási javaslatok cikkeiben található. 

Az Azure Security benchmark dokumentációja a biztonsági vezérlőket és a szolgáltatási javaslatokat tartalmazza.

- **Biztonsági vezérlők**: az Azure biztonsági teljesítménytesztekre vonatkozó javaslatokat a biztonsági vezérlők kategorizálják. A biztonsági vezérlők magas szintű gyártótól független biztonsági követelményeket jelentenek, például a hálózati biztonságot és az adatvédelmet. Az egyes biztonsági vezérlők számos biztonsági javaslattal és útmutatással rendelkeznek, amelyek segítségével implment ezeket az ajánlásokat. 
- **Szolgáltatási javaslatok**: ha elérhető, az Azure-szolgáltatásokra vonatkozó teljesítményteszt-javaslatok tartalmazzák az Azure-beli biztonsági teljesítménytesztek ajánlásait, amelyek kifejezetten az adott szolgáltatáshoz vannak igazítva. 

A "Control", a "benchmark" és az "Baseline" kifejezések gyakran az Azure biztonsági teljesítményteszt dokumentációjában használatosak, és fontos tisztában lenni azzal, hogy az Azure hogyan használja ezeket a feltételeket. 

| Időtartam | Leírás | Példa |
|--|--|--|
| Vezérlés | A **vezérlő** egy olyan szolgáltatás vagy tevékenység magas szintű leírása, amelyet kezelni kell, és nem a technológiára vagy a megvalósításra jellemző. | Az adatvédelem az egyik biztonsági vezérlő. Ez a vezérlő olyan konkrét műveleteket tartalmaz, amelyeket meg kell oldani az adatvédelem biztosítása érdekében. |
| Külső viszonyítási alap | A **teljesítménytesztek** biztonsági javaslatokat tartalmaznak egy adott technológiára, például az Azure-ra. Az ajánlásokat azon vezérlőelem kategorizálja, amelyhez tartoznak. | Az Azure biztonsági teljesítményteszt az Azure platformra jellemző biztonsági ajánlásokat tartalmazza  |
| Alapterv | Az alapkonfiguráció a szervezet biztonsági követelményei. A biztonsági követelmények a teljesítményteszt ajánlásain alapulnak. Az egyes szervezetek határozzák meg, hogy melyik teljesítményteszt-ajánlásokat kell tartalmazniuk az alaptervbe. | A contoso vállalat a biztonsági alaptervet úgy hozza létre, hogy az Azure biztonsági Teljesítménytesztben meghatározott javaslatok megkövetelését választja. |

Szívesen fogadjuk visszajelzését az Azure biztonsági teljesítményteszttel kapcsolatban! Javasoljuk, hogy adjon meg megjegyzéseket az alábbi visszajelzési területeken. Ha inkább a saját bemenetét szeretné megosztani az Azure Security teljesítményteszt csapatával, kérjük, töltse ki az űrlapot https://aka.ms/AzSecBenchmark 