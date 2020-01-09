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
ms.openlocfilehash: 2d0cceca841a2aff2538dcf8763d245ca8cfd13d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564360"
---
# <a name="azure-security-benchmarks-introduction"></a>Az Azure biztonsági teljesítménymutatóinak bemutatása

A helyszíni számítástechnika több évet vagy akár több évtizedes tapasztalatot is igénybe vehet. Tudja, hogyan védheti ezeket a központi telepítéseket. A felhő azonban eltér. Honnan tudhatja, hogy a felhőben üzemelő példányok biztonságosak-e? Mi a különbség a helyszíni rendszerek és a Felhőbeli üzemelő példányok biztonsági eljárásai között?

A felhő biztonságossá tételéhez nagy mennyiségű tanulmány, ajánlott eljárás, hivatkozási architektúra, webes útmutató, nyílt forráskódú eszközök, kereskedelmi megoldások, intelligencia-hírcsatornák és egyebek is használható. Melyik beállítást érdemes használni? Mit tehet a Felhőbeli biztonság elfogadható szintjének eléréséhez? 

A Felhőbeli üzemelő példányok biztonságossá tételének egyik legjobb módja a Felhőbeli biztonsági teljesítménytesztekre vonatkozó javaslatok bevezetése. A szolgáltatások biztosításához szükséges teljesítményteszt-javaslatok a kiberbiztonsági kockázatának és kezelésének alapvető ismeretével kezdődnek. Ezt az értelmezést használhatja, ha a felhőalapú szolgáltató által meghatározott biztonsági konfigurációs beállításokat szeretne kiválasztani a környezetében. 

Az Azure biztonsági teljesítményteszt olyan nagy hatású biztonsági javaslatokat tartalmaz, amelyek segítségével biztonságossá tehető az Azure-ban használt szolgáltatások nagy része. Ezeket a javaslatokat "általános" vagy "szervezeti" néven tekintheti meg, mivel azok a legtöbb Azure-szolgáltatásra alkalmazhatók. Az Azure biztonsági teljesítménytesztekre vonatkozó javaslatokat ezután minden egyes Azure-szolgáltatáshoz testre kell szabni, és ez a testreszabott útmutató a szolgáltatási javaslatok cikkeiben található. 

Az Azure Security benchmark dokumentációja a biztonsági vezérlőket és a szolgáltatásokra vonatkozó ajánlásokat tartalmaz.

- **Biztonsági vezérlők**: az Azure biztonsági teljesítménytesztekre vonatkozó javaslatokat a biztonsági vezérlők kategorizálják. A biztonsági vezérlők magas szintű gyártótól független biztonsági követelményeket jelentenek, például a hálózati biztonságot és az adatvédelmet. Az egyes biztonsági vezérlők számos biztonsági javaslattal és útmutatással rendelkeznek, amelyek segítenek a javaslatok engedélyezésében. 
- A **szolgáltatásra vonatkozó javaslatok**: ha elérhető, az Azure-szolgáltatásokra vonatkozó teljesítményteszt-javaslatok a szolgáltatásra szabott Azure biztonsági teljesítménytesztekre vonatkozó javaslatokat, valamint az adott szolgáltatásra jellemző további javaslatokat is tartalmaznak. 

A "Control", a "benchmark" és az "Baseline" kifejezések gyakran az Azure biztonsági teljesítményteszt dokumentációjában használatosak, és fontos tisztában lenni azzal, hogy az Azure hogyan használja ezeket a feltételeket. 

| Időtartam | Leírás | Példa |
|--|--|--|
| Vezérlés | A **vezérlő** egy olyan szolgáltatás vagy tevékenység magas szintű leírása, amelyet kezelni kell, és nem kifejezetten a technológiára vagy a megvalósításra. | Az adatvédelem az egyik biztonsági vezérlő. Ez a vezérlő olyan konkrét műveleteket tartalmaz, amelyeket meg kell oldani az adatvédelem biztosítása érdekében. |
| Külső viszonyítási alap | A **teljesítménytesztek** biztonsági javaslatokat tartalmaznak egy adott technológiára, például az Azure-ra. Az ajánlásokat azon vezérlőelem kategorizálja, amelyhez tartoznak. | Az Azure biztonsági teljesítményteszt az Azure platformra jellemző biztonsági ajánlásokat tartalmazza  |
| Alapterv | Az alapkonfiguráció a szervezet biztonsági követelményei. A biztonsági követelmények a teljesítményteszt ajánlásain alapulnak. Az egyes szervezetek határozzák meg, hogy melyik teljesítményteszt-ajánlásokat kell tartalmazniuk az alaptervbe. | A contoso vállalat a biztonsági alaptervet úgy hozza létre, hogy az Azure biztonsági Teljesítménytesztben meghatározott javaslatok megkövetelését választja. |

Szívesen fogadjuk visszajelzését az Azure biztonsági teljesítményteszttel kapcsolatban! Javasoljuk, hogy adjon meg megjegyzéseket az alábbi visszajelzési területeken. Ha inkább a saját bemenetét szeretné megosztani az Azure Security teljesítményteszt csapatával, kérjük, töltse ki az űrlapot https://aka.ms/AzSecBenchmark 