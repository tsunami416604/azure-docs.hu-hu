---
title: Számítási erőforrás kezelése az SQL-készlethez
description: Ismerje meg a teljesítmény horizontális felskálázási képességeit az Azure Synapse Analytics SQL-készletében. Horizontális felskálázás a DWUs-ok módosításával, vagy alacsonyabb költségek az adattárház szüneteltetésével.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4c6c4d97282387fbcee1d7e8b55b95c01e3dded5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351610"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Számítási feladatok kezelése az Azure Synapse Analytics adattárában

Ismerje meg a számítási erőforrások kezelését az Azure Synapse Analytics SQL-készletében. Csökkentse a költségeket az SQL-készlet szüneteltetésével, vagy méretezze át az adatraktárt a teljesítményigények nek megfelelően. 

## <a name="what-is-compute-management"></a>Mi a számítási felügyelet?

Az adattárház architektúrája elválasztja a tárolást és a számítást, lehetővé téve, hogy mindegyik egymástól függetlenül méretezhető. Ennek eredményeképp az adattárolástól függetlenül megfelelhet a számítási méretezés teljesítményigényeinek. Emellett szüneteltetheti és folytathatja az erőforrásokat. Ennek az architektúrának a természetes következménye, hogy a számítási és tárolási [számlázás](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) külön. Ha egy ideig nincs szüksége az adattárházra, a számítás szüneteltetésével számítási költségeket takaríthat meg. 

## <a name="scaling-compute"></a>Számítási feladatok méretezése

Az [SQL-készlet adattárházegység-beállításának](what-is-a-data-warehouse-unit-dwu-cdwu.md) módosításával kinagyíthatja vagy kicsinyítheti a számítást. A betöltési és lekérdezési teljesítmény lineárisan növekedik több adattárházegység hozzáadásával. 

A horizontális felskálázási lépéseket az [Azure Portal](quickstart-scale-compute-portal.md), a [PowerShell](quickstart-scale-compute-powershell.md)vagy a [T-SQL](quickstart-scale-compute-tsql.md) rövid útmutatók című témakörben található. A [REST API-val](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)kibővített műveleteket is végrehajthat.

Méretezési művelet végrehajtásához az SQL-készlet először megöli az összes bejövő lekérdezést, majd visszaállítja a tranzakciókat a konzisztens állapot biztosítása érdekében. A méretezés csak a tranzakciók visszaállítása után történik. Méretezési művelet esetén a rendszer leválasztja a tárolási réteget a számítási csomópontokról, hozzáadja a számítási csomópontokat, majd újra csatolja a tárolási réteget a Számítási réteghez. Minden SQL-készlet 60 disztribúcióként van tárolva, amelyek egyenletesen oszlanak el a számítási csomópontok között. További számítási csomópontok hozzáadása nagyobb számítási teljesítményt ad hozzá. A számítási csomópontok számának növekedésével csökken a számítási csomópontonkénti disztribúciók száma, így nagyobb számítási teljesítményt biztosít a lekérdezések számára. Hasonlóképpen az adatraktári egységek számának csökkentése csökkenti a számítási csomópontok számát, ami csökkenti a lekérdezések számítási erőforrásait.

Az alábbi táblázat bemutatja, hogyan változik a számítási csomópontonkénti felosztások száma az adatraktár-egységek változásával.  A DW30000c 60 számítási csomópontot biztosít, és sokkal nagyobb lekérdezési teljesítményt ér el, mint a DW100c. 

| Adattárházegységek  | \#számítási csomópontok | \#eloszlások csomópontonként |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Az adatraktári egységek megfelelő méretének megtalálása

A horizontális felskálázás teljesítménybeli előnyeit, különösen a nagyobb adattárház egységek esetében, legalább 1 TB-os adatkészletet szeretne használni. Az SQL-készlethez való legjobb adattárház-egységek megkereséséhez próbálja meg fel-le skálázni. Az adatok betöltése után futtasson néhány lekérdezést különböző számú adatraktár-egységgel. Mivel a skálázás gyors, kipróbálhatja a különböző teljesítményszintek egy óra vagy kevesebb. 

Javaslatok az adattárház-egységek legjobb számának megkereséséhez:

- A fejlesztés alatt álló SQL-készlet esetében először válasszon kisebb számú adatraktár-egységet.  Egy jó kiindulási pont A DW400c vagy DW200c.
- Az alkalmazás teljesítményének figyelése, a kiválasztott adattárház egységek számának figyelése a megfigyelt teljesítményhez képest.
- Tegyük fel, hogy egy lineáris skála, és határozza meg, hogy mennyit kell növelni vagy csökkenteni az adatraktár egységek. 
- Folytassa a módosításokat, amíg el nem éri az üzleti igényeinek megfelelő optimális teljesítményszintet.

## <a name="when-to-scale-out"></a>Mikor kell kibővíteni?

Az adatraktári egységek kiskálázása hatással van a teljesítmény alábbi szempontjaira:

- A lineárisan javítja a rendszer teljesítményét a vizsgálatok, az összesítések és a CTAS utasítások esetében.
- Növeli az adatok betöltéséhez felelős olvasók és írók számát.
- Az egyidejű lekérdezések és az egyidejűségi tárolóhelyek maximális száma.

Javaslatok az adattárház-egységek horizontális felskálázásához:

- Mielőtt nagy adatbetöltési vagy átalakítási műveletet hajtana végre, horizontális felskálázás, hogy az adatok gyorsabban elérhetők legyenek.
- Csúcsidőben munkaidőben horizontális an-túl nagyobb számú egyidejű lekérdezések befogadására. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Mi a teendő, ha a horizontális felskálázás nem javítja a teljesítményt?

Adattárház egységek hozzáadása a párhuzamosság növeléséhez. Ha a munka egyenletesen oszlik meg a számítási csomópontok között, a további párhuzamosság javítja a lekérdezési teljesítményt. Ha a horizontális felskálázás nem módosítja a teljesítményt, ennek oka lehet. Előfordulhat, hogy az adatok ferdeek a disztribúciók között, vagy a lekérdezések nagy mennyiségű adatmozgást vezetnek be. A lekérdezési teljesítménnyel kapcsolatos problémák vizsgálatához olvassa el [a Teljesítményhibaelhárítás című témakört.](sql-data-warehouse-troubleshoot.md#performance) 

## <a name="pausing-and-resuming-compute"></a>Számítás szüneteltetése és folytatása

A számítás szüneteltetése miatt a tárolási réteg leválik a számítási csomópontokról. A számítási erőforrások felszabadulnak a fiókjából. A számítási idő szüneteltetése alatt nem számítunk fel díjat a számításért. A számítás folytatása újra csatlakoztatja a tárolót a számítási csomópontokhoz, és folytatja a számítási díjakat. SQL-készlet szüneteltetésekor:

* A számítási és memória-erőforrások visszakerülnek az adatközpontban rendelkezésre álló erőforrások készletébe
* Az adattárház egységköltsége a szünet szünetidejére nulla.
* Az adattárolást ez nem érinti, és az adatok érintetlenek maradnak. 
* Minden futó vagy várólistára helyezett művelet megszakad.

Amikor folytatja az SQL-készletet:

* Az SQL-készlet beszerzi az adattárház-egységek beállításszámítási és memória-erőforrásait.
* Az adattárház-egységek díjainak kiszámítása folytatódik.
* Az adatok elérhetővé válnak.
* Miután az SQL-készlet online állapotba került, újra kell indítania a számítási feladatok lekérdezéseit.

Ha mindig azt szeretné, hogy az SQL-készlet elérhető legyen, fontolja meg a legkisebb méretre való leskálázást, nem pedig szüneteltetést. 

A szüneteltetési és folytatási lépések megtekintéséhez tekintse meg az [Azure Portalon,](pause-and-resume-compute-portal.md)vagy a [PowerShell-rövid](pause-and-resume-compute-powershell.md) útmutatók. Használhatja a [pause REST API-t](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) vagy a [rektor REST API-t](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)is.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tranzakciók ürítése felfüggesztés vagy méretezés előtt

Azt javasoljuk, hogy engedélyezze a meglévő tranzakciók befejezését, mielőtt szüneteltetett vagy méretezési műveletet kezdeményezne.

Az SQL-készlet szüneteltetésekor vagy méretezésekor a kulisszák mögött a lekérdezések megszakadnak a szüneteltetési vagy méretezési kérelem indításakor. Egy egyszerű választó lekérdezés megszakítása gyors művelet, amelynek szinte semmilyen hatása nincs az üzemelő példány felfüggesztéséhez vagy méretezéséhez szükséges időtartamra.  Azonban a tranzakciós lekérdezések, amelyek adatokat vagy az adatok szerkezetét is módosítják, nem biztos, hogy ilyen hamar le tudnak állni. **A tranzakciós lekérdezéseknek definíciójuk szerint vagy teljesen be kell fejeződniük, vagy vissza kell állítaniuk az általuk végrehajtott módosításokat.** A tranzakciós lekérdezések által elvégzett módosítások visszaállítása ugyanannyi, vagy akár hosszabb ideig is tarthat, mint a lekérdezés által elvégzett eredeti módosítás végrehajtása. Például ha megszakít egy olyan lekérdezést, amely sorokat törölt és már egy órája futott, a rendszernek egy újabb órájába telhet, hogy a törölt sorokat visszaállítsa. Ha a felfüggesztést vagy a méretezést olyankor futtatja, amikor épp tranzakciók vannak folyamatban, a felfüggesztés vagy a méretezés látszólag hosszú időt vehet igénybe, mivel e műveleteknek meg kell várniuk, amíg a visszaállítás lezajlik.

Lásd [még: A tranzakciók ismertése](sql-data-warehouse-develop-transactions.md)és [a tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md)című témakört.

## <a name="automating-compute-management"></a>A számításkezelés automatizálása

A számítási felügyeleti műveletek automatizálásához olvassa el a [Számítás kezelése Azure-funkciókkal című témakört.](manage-compute-with-azure-functions.md)

A horizontális felskálázási, szüneteltetési és folytatási műveletek mindegyike több percet is igénybe vehet. Ha automatikusan skálázást, szüneteltetést vagy újraegyesítést használ, javasoljuk, hogy a logika megvalósítása győződjön meg arról, hogy bizonyos műveletek befejeződtek egy másik művelet folytatása előtt. Az SQL-készlet állapotának különböző végpontokon keresztüli ellenőrzése lehetővé teszi az ilyen műveletek automatizálásának megfelelő megvalósítását. 

Az SQL-készlet állapotának ellenőrzéséhez tekintse meg a [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) vagy a [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) gyorsindítás. Az SQL-készlet állapotát rest [API-val](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)is ellenőrizheti.


## <a name="permissions"></a>Engedélyek

Az SQL-készlet méretezéséhez az [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  A szüneteltetéshez és a folytatáshoz az [SQL DB Contributor](../../role-based-access-control/built-in-roles.md#sql-db-contributor) engedélyre, különösen a Microsoft.Sql/servers/databases/action-re van szükség.


## <a name="next-steps"></a>További lépések
Tekintse meg, hogyan útmutató a [számítási számítási](manage-compute-with-azure-functions.md) egy másik szempont a számítási erőforrások kezelése a különböző számítási erőforrások hozzárendelése az egyes lekérdezések. További információ: [Resource classes for workload management](resource-classes-for-workload-management.md).
