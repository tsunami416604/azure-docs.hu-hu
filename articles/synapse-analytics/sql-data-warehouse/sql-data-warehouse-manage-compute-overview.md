---
title: Az SQL-készlet számítási erőforrásának kezelése
description: Ismerje meg az Azure szinapszis Analytics SQL-készlet teljesítmény-Felskálázási funkcióit. Az adattárház felfüggesztésével felskálázást végez a DWU és az alacsonyabb költségek módosításával.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 90815d52e6884efe6cff9a7860c093b4b5c1bc94
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204541"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Számítások kezelése az Azure szinapszis Analytics-adattárházban

Tudnivalók a számítási erőforrások kezeléséről az Azure szinapszis Analytics SQL-készletben. Csökkentse az SQL-készlet felfüggesztését, vagy méretezze át az adattárházat a teljesítmény iránti igények kielégítése érdekében.

## <a name="what-is-compute-management"></a>Mi a számítási felügyelet?

Az adatraktár architektúrája elkülöníti a tárolást és a számítást, ami lehetővé teszi, hogy mindegyik egymástól függetlenül méretezhető legyen. Ennek eredményeképp az adattárolástól függetlenül megfelelhet a számítási méretezés teljesítményigényeinek. Emellett szüneteltetheti és folytathatja az erőforrásokat. Ennek az architektúrának a természetes következménye, hogy a számítás és a tárolás [számlázása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) külön történik. Ha egy ideig nincs szüksége az adattárházra, a számítás szüneteltetésével számítási költségeket takaríthat meg.

## <a name="scaling-compute"></a>Méretezési számítás

Az SQL-készlet [adatraktár-egységeinek](what-is-a-data-warehouse-unit-dwu-cdwu.md) beállításával kibővítheti vagy méretezheti a számítási kapacitást. A betöltési és lekérdezési teljesítmény lineárisan növekedik több adattárházegység hozzáadásával.

A kibővített lépésekért tekintse meg a [Azure Portal](quickstart-scale-compute-portal.md), a [PowerShell](quickstart-scale-compute-powershell.md)vagy a [T-SQL](quickstart-scale-compute-tsql.md) rövid útmutatót. Kibővíthető műveleteket is végrehajthat [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)használatával.

A méretezési művelet végrehajtásához az SQL-készlet először megöli az összes bejövő lekérdezést, majd Visszagörgeti a tranzakciókat, így biztosítva a konzisztens állapotot. A méretezés csak a tranzakciók visszaállítása után történik. A méretezési műveletekhez a rendszer leválasztja a tárolási réteget a számítási csomópontokból, kiszámítja a számítási csomópontokat, majd újra csatolja a tárolási réteget a számítási réteghez. A rendszer minden SQL-készletet 60-Eloszlásként tárol, amelyek egyenletesen oszlanak el a számítási csomópontokon. További számítási csomópontok hozzáadásával további számítási kapacitást adhat hozzá. A számítási csomópontok számának növekedésével a számítási csomópontok száma csökken, és nagyobb számítási teljesítményt biztosít a lekérdezésekhez. Hasonlóképpen, a csökkenő adattárház-egységek csökkentik a számítási csomópontok számát, ami csökkenti a lekérdezések számítási erőforrásait.

A következő táblázat azt mutatja be, hogy a számítási csomópontok hány eloszlása változik az adatraktár-egységek változásakor.  A DW30000c 60 számítási csomópontot biztosít, és sokkal nagyobb lekérdezési teljesítményt érhet el, mint a DW100c.

| Adattárházegységek  | \#a számítási csomópontok | \#/csomópontok eloszlása |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c lehetőséget   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>Az adatraktár-egységek megfelelő méretének megállapítása

Ha szeretné megtekinteni a horizontális felskálázás teljesítményének előnyeit, különösen a nagyobb adattárház-egységek esetében, legalább 1 TB-os adatkészletet szeretne használni. Az SQL-készlethez tartozó adatraktár-egységek legjobb számának megkereséséhez próbálja meg felfelé és lefelé méretezni a méretezést. Az adatok betöltése után futtasson néhány lekérdezést különböző számú adattárház-egységgel. Mivel a skálázás gyors, különböző teljesítményszint kipróbálására van lehetőség egy órán belül.

Javaslatok a legjobb számú adatraktár-egység megtalálásához:

- Egy fejlesztés alatt álló SQL-készlet esetében kisebb számú adattárház-egységet válasszon ki.  Jó kiindulási pont a DW400c vagy a DW200c.
- Figyelheti az alkalmazás teljesítményét, és megfigyelheti a kiválasztott adatraktár-egységek számát a megfigyelt teljesítményhez képest.
- Tegyük fel a lineáris skálázást, és határozza meg, hogy mennyit kell fokoznia vagy csökkentenie az adatraktár-egységeket.
- Folytassa a módosításokat, amíg el nem éri az üzleti igényeknek megfelelő optimális teljesítményt.

## <a name="when-to-scale-out"></a>Mikor kell kibővíteni

Az adatraktár-egységek horizontális felskálázása a következő teljesítménybeli szempontokat befolyásolja:

- A rendszer lineárisan javítja a rendszerek teljesítményét a vizsgálatok, összesítések és CTAS utasításokhoz.
- Növeli az olvasók és az írók számát az betöltéshez.
- Az egyidejű lekérdezések és a párhuzamossági bővítőhelyek maximális száma.

Javaslatok az adatraktár-egységek kiskálázásának idejére:

- A nagy mennyiségű adat-betöltési vagy átalakítási művelet végrehajtása előtt bővítse az adatok gyorsabb elérhetővé tételét.
- A munkaidőn kívüli időszakban az egyidejű lekérdezések nagyobb számának megfelelően méretezhető.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Mi a teendő, ha a horizontális felskálázás nem javítja a teljesítményt?

Adattárház-egységek hozzáadása a párhuzamosság növeléséhez. Ha a munka egyenletesen oszlik meg a számítási csomópontok között, a további párhuzamosság javítja a lekérdezési teljesítményt. Ha a horizontális felskálázás nem változtatja meg a teljesítményt, bizonyos okok miatt előfordulhat, hogy ez megtörténik. Előfordulhat, hogy az adatai elferdítik a disztribúciókat, vagy a lekérdezések nagy mennyiségű adatáthelyezést mutatnak be. A lekérdezések teljesítményével kapcsolatos problémák kivizsgálásához tekintse meg a [teljesítménnyel kapcsolatos hibaelhárítás](sql-data-warehouse-troubleshoot.md#performance)témakört.

## <a name="pausing-and-resuming-compute"></a>Számítás szüneteltetése és folytatása

A számítási műveletek felfüggesztése miatt a tárolási réteg leválasztható a számítási csomópontokból. A számítási erőforrások kiadása a fiókból történik. Nem számítunk fel díjat, amíg a számítás szünetel. A számítás folytatása a számítási csomópontok számára újracsatlakoztatja a tárolót, és folytatja a számítási költségeket.
SQL-készlet szüneteltetése esetén:

- A számítási és memória-erőforrásokat a rendszer visszaadja az adatközpontban elérhető erőforrások készletének.
- Az adatraktár egységének költségei a Szüneteltetés időtartamára nulla értékűek.
- Az adattárolás nem érinti az adattárolást, és az adatai érintetlenek maradnak.
- Minden futó vagy várólistán lévő művelet meg lett szakítva.

SQL-készlet folytatásakor:

- Az SQL-készlet a számítási és a memória-erőforrásokat szerzi be az adattárház-egységek beállításához.
- Az adatraktár-egységek számítási díjai folytatódnak.
- Az adatai elérhetővé válnak.
- Ha az SQL-készlet online állapotban van, újra kell indítania a munkaterhelés-lekérdezéseket.

Ha mindig elérhetővé szeretné tenni az SQL-készletet, akkor a felfüggesztés helyett érdemes a legkisebb méretre méreteznie.

A szüneteltetési és folytatási lépésekért tekintse meg a [Azure Portal](pause-and-resume-compute-portal.md)vagy a [PowerShell](pause-and-resume-compute-powershell.md) rövid útmutatóit. Használhatja a [pause REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) vagy a [resume REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)is.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tranzakciók ürítése felfüggesztés vagy méretezés előtt

Azt javasoljuk, hogy a szüneteltető vagy a skálázási művelet megkezdése előtt engedélyezze a meglévő tranzakciók befejezését.

Az SQL-készlet szüneteltetése vagy skálázása során a rendszer megszakítja a lekérdezéseket a felfüggesztési vagy méretezési kérelem elindításakor. Egy egyszerű választó lekérdezés megszakítása gyors művelet, amelynek szinte semmilyen hatása nincs az üzemelő példány felfüggesztéséhez vagy méretezéséhez szükséges időtartamra.  Azonban a tranzakciós lekérdezések, amelyek adatokat vagy az adatok szerkezetét is módosítják, nem biztos, hogy ilyen hamar le tudnak állni. **A tranzakciós lekérdezéseknek definíciójuk szerint vagy teljesen be kell fejeződniük, vagy vissza kell állítaniuk az általuk végrehajtott módosításokat.** A tranzakciós lekérdezések által elvégzett módosítások visszaállítása ugyanannyi, vagy akár hosszabb ideig is tarthat, mint a lekérdezés által elvégzett eredeti módosítás végrehajtása. Például ha megszakít egy olyan lekérdezést, amely sorokat törölt és már egy órája futott, a rendszernek egy újabb órájába telhet, hogy a törölt sorokat visszaállítsa. Ha a felfüggesztést vagy a méretezést olyankor futtatja, amikor épp tranzakciók vannak folyamatban, a felfüggesztés vagy a méretezés látszólag hosszú időt vehet igénybe, mivel e műveleteknek meg kell várniuk, amíg a visszaállítás lezajlik.

Lásd még: [tranzakciók ismertetése](sql-data-warehouse-develop-transactions.md)és [tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Számítási felügyelet automatizálása

A számítási felügyeleti műveletek automatizálását lásd: [a számítások kezelése az Azure functions](manage-compute-with-azure-functions.md)használatával.

A kibővített, szüneteltetési és folytatási műveletek végrehajtása több percet is igénybe vehet. Ha a méretezés, a felfüggesztés vagy a folytatás automatikusan történik, javasoljuk, hogy végezze el a logikát, és győződjön meg arról, hogy bizonyos műveletek befejeződtek, mielőtt továbblép egy másik művelettel. Az SQL-készlet állapotának különböző végpontokon keresztüli ellenőrzése lehetővé teszi az ilyen műveletek automatizálásának megfelelő megvalósítását.

Az SQL-készlet állapotának megtekintéséhez tekintse meg a [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) vagy a [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) rövid útmutatóját. Az SQL-készlet állapotát [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)is megtekintheti.

## <a name="permissions"></a>Engedélyek

Az SQL-készlet skálázásához az [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)utasításban leírt engedélyek szükségesek.  A Szüneteltetés és folytatás megköveteli az [SQL-adatbázis közreműködői](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) engedélyét, különösen a Microsoft. SQL/kiszolgálók/adatbázisok/művelet esetében.

## <a name="next-steps"></a>További lépések

A számítási erőforrások kezelésével kapcsolatos [további](manage-compute-with-azure-functions.md) tudnivalókat lásd a különböző számítási erőforrások lefoglalása az egyes lekérdezésekhez című témakör útmutatását. További információ: erőforrás- [osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).
