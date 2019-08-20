---
title: Számítási erőforrások kezelése Azure SQL Data Warehouseban | Microsoft Docs
description: Ismerje meg a Azure SQL Data Warehouse teljesítményének kibővíthető képességeit. Az adattárház felfüggesztésével felskálázást végez a DWU és az alacsonyabb költségek módosításával.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f0935ccc4c4274bfab0c589ef158d4ea0bef455c
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575327"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Számítások kezelése Azure SQL Data Warehouseban
További információ a számítási erőforrások Azure SQL Data Warehouseban történő kezeléséről. Csökkentse az adattárház felfüggesztését, vagy méretezze át az adattárházat a teljesítményre vonatkozó igények kielégítése érdekében. 

## <a name="what-is-compute-management"></a>Mi a számítási felügyelet?
A SQL Data Warehouse architektúrája elkülöníti a tárolást és a számítást, így egymástól függetlenül méretezhetők. Ennek eredményeképpen a számítási kapacitást úgy méretezheti, hogy az adattárolástól függetlenül megfeleljen a teljesítményi igényeknek. A számítási erőforrások szüneteltetését és folytatását is elvégezheti. Ennek az architektúrának a természetes következménye [](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) , hogy a számítás és a tárolás számlázása külön történik. Ha egy ideig nem kell használnia az adattárházat, a számítási költségeket a számítási költségek felfüggesztésével mentheti. 

## <a name="scaling-compute"></a>Méretezési számítás
Az adatraktár adattárház- [egység](what-is-a-data-warehouse-unit-dwu-cdwu.md) beállításának módosításával kibővítheti vagy méretezheti a számítási kapacitást. A betöltés és a lekérdezés teljesítménye lineárisan növekedhet, ha további adattárház-egységeket ad hozzá. 

A kibővített lépésekért tekintse meg a [Azure Portal](quickstart-scale-compute-portal.md), a [PowerShell](quickstart-scale-compute-powershell.md)vagy a [T-SQL](quickstart-scale-compute-tsql.md) rövid útmutatót. Kibővíthető műveleteket is végrehajthat [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)használatával.

A méretezési művelet végrehajtásához SQL Data Warehouse először az összes bejövő lekérdezést, majd Visszagörgeti a tranzakciókat, hogy konzisztens állapotot biztosítson. A skálázás csak a tranzakció-visszaállítás befejeződése után történik meg. A méretezési műveletekhez a rendszer leválasztja a tárolási réteget a számítási csomópontokból, kiszámítja a számítási csomópontokat, majd újra csatolja a tárolási réteget a számítási réteghez. Az egyes adattárházak 60 Eloszlásként vannak tárolva, amelyek egyenletesen oszlanak el a számítási csomópontokon. További számítási csomópontok hozzáadásával további számítási kapacitást adhat hozzá. A számítási csomópontok számának növekedésével a számítási csomópontok száma csökken, és nagyobb számítási teljesítményt biztosít a lekérdezésekhez. Hasonlóképpen, a csökkenő adattárház-egységek csökkentik a számítási csomópontok számát, ami csökkenti a lekérdezések számítási erőforrásait.

A következő táblázat azt mutatja be, hogy a számítási csomópontok hány eloszlása változik az adatraktár-egységek változásakor.  A DWU6000 60 számítási csomópontot biztosít, és sokkal nagyobb lekérdezési teljesítményt érhet el, mint a DWU100. 

| Adattárházegységek  | \#a számítási csomópontok | \#/csomópontok eloszlása |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Az adatraktár-egységek megfelelő méretének megállapítása

Ha szeretné megtekinteni a horizontális felskálázás teljesítményének előnyeit, különösen a nagyobb adattárház-egységek esetében, legalább 1 TB-os adatkészletet szeretne használni. Az adatraktárhoz tartozó adatraktár-egységek legjobb számának megkereséséhez próbálja meg a felfelé és lefelé skálázást. Az adatok betöltése után futtasson néhány lekérdezést különböző számú adattárház-egységgel. Mivel a skálázás gyors, különböző teljesítményszint kipróbálására van lehetőség egy órán belül. 

Javaslatok a legjobb számú adatraktár-egység megtalálásához:

- A fejlesztés alatt álló adattárházak esetében kisebb számú adattárház-egységet válasszon ki.  Jó kiindulási pont a DW400 vagy a DW200.
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

## <a name="pausing-and-resuming-compute"></a>A számítási feladat felfüggesztése és folytatása
A számítási műveletek felfüggesztése miatt a tárolási réteg leválasztható a számítási csomópontokból. A számítási erőforrások kiadása a fiókból történik. Nem számítunk fel díjat, amíg a számítás szünetel. A számítás folytatása a számítási csomópontok számára újracsatlakoztatja a tárolót, és folytatja a számítási költségeket. Az adatraktár szüneteltetése esetén:

* A számítási és memória-erőforrásokat a rendszer visszaadja az adatközpontban elérhető erőforrások készletének.
* Az adatraktár egységének költségei a Szüneteltetés időtartamára nulla értékűek.
* Az adattárolás nem érinti az adattárolást, és az adatai érintetlenek maradnak. 
* SQL Data Warehouse megszakítja az összes futó vagy várólistán lévő műveletet.

Amikor folytatja az adattárházat:

* A SQL Data Warehouse számítási és memória-erőforrásokat vásárol az adattárház-egységek beállításához.
* Az adatraktár-egységek számítási díjai folytatódnak.
* Az adatai elérhetővé válnak.
* Miután az adattárház online állapotú, újra kell indítania a munkaterhelés-lekérdezéseket.

Ha mindig elérhetővé szeretné tenni az adattárházat, érdemes lehet a legkisebb méretre méretezni a Szüneteltetés helyett. 

A szüneteltetési és folytatási lépésekért tekintse meg a [Azure Portal](pause-and-resume-compute-portal.md)vagy a [PowerShell](pause-and-resume-compute-powershell.md) rövid útmutatóit. Használhatja a [pause REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) vagy a [resume REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)is.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tranzakciók ürítése felfüggesztés vagy méretezés előtt
Azt javasoljuk, hogy a szüneteltető vagy a skálázási művelet megkezdése előtt engedélyezze a meglévő tranzakciók befejezését.

Ha felfüggeszti vagy méretezi az SQL Data Warehouse-t, a felfüggesztési vagy méretezési kérelem elindításakor a színfalak mögött a lekérdezések megszakadnak.  Egy egyszerű választó lekérdezés megszakítása gyors művelet, amelynek szinte semmilyen hatása nincs az üzemelő példány felfüggesztéséhez vagy méretezéséhez szükséges időtartamra.  Azonban a tranzakciós lekérdezések, amelyek adatokat vagy az adatok szerkezetét is módosítják, nem biztos, hogy ilyen hamar le tudnak állni.  **A tranzakciós lekérdezéseknek definíciójuk szerint vagy teljesen be kell fejeződniük, vagy vissza kell állítaniuk az általuk végrehajtott módosításokat.**  A tranzakciós lekérdezések által elvégzett módosítások visszaállítása ugyanannyi, vagy akár hosszabb ideig is tarthat, mint a lekérdezés által elvégzett eredeti módosítás végrehajtása.  Például ha megszakít egy olyan lekérdezést, amely sorokat törölt és már egy órája futott, a rendszernek egy újabb órájába telhet, hogy a törölt sorokat visszaállítsa.  Ha a felfüggesztést vagy a méretezést olyankor futtatja, amikor épp tranzakciók vannak folyamatban, a felfüggesztés vagy a méretezés látszólag hosszú időt vehet igénybe, mivel e műveleteknek meg kell várniuk, amíg a visszaállítás lezajlik.

Lásd még: [tranzakciók ismertetése](sql-data-warehouse-develop-transactions.md)és [tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Számítási felügyelet automatizálása
A számítási felügyeleti műveletek automatizálását lásd: [a számítások kezelése az Azure functions](manage-compute-with-azure-functions.md)használatával.

A kibővített, szüneteltetési és folytatási műveletek végrehajtása több percet is igénybe vehet. Ha a méretezés, a felfüggesztés vagy a folytatás automatikusan történik, javasoljuk, hogy végezze el a logikát, és győződjön meg arról, hogy bizonyos műveletek befejeződtek, mielőtt továbblép egy másik művelettel. Az adatraktár állapotának különböző végpontokon keresztüli ellenőrzése lehetővé teszi az ilyen műveletek automatizálásának megfelelő megvalósítását. 

Az adatraktár állapotának vizsgálatához tekintse meg a [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) vagy a [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) rövid útmutatóját. Az adatraktár állapotát [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)is megtekintheti.


## <a name="permissions"></a>Engedélyek

Az adatraktár skálázásához az [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)utasításban ismertetett engedélyek szükségesek.  A Szüneteltetés és folytatás megköveteli az [SQL-adatbázis közreműködői](../role-based-access-control/built-in-roles.md#sql-db-contributor) engedélyét, különösen a Microsoft. SQL/kiszolgálók/adatbázisok/művelet esetében.


## <a name="next-steps"></a>További lépések
A számítási erőforrások kezelésével kapcsolatos [](manage-compute-with-azure-functions.md) további tudnivalókat lásd a különböző számítási erőforrások lefoglalása az egyes lekérdezésekhez című témakör útmutatását. További információ: erőforrás- [osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).
