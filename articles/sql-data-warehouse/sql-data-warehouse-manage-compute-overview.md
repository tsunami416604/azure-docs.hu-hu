---
title: Az Azure SQL Data Warehouse a számítási erőforrások kezelése |} A Microsoft Docs
description: Ismerje meg a teljesítmény horizontális felskálázás az Azure SQL Data Warehouse képességeket. Horizontális felskálázás az adattárház szüneteltetésével a dwu-k vagy alacsonyabb költségek módosításával.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 53a801a367e6948c3070224b7ff36a013a1faab3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300850"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse számítások kezelése
Ismerje meg az Azure SQL Data Warehouse a számítási erőforrások kezelése. Az adattárház szüneteltetésével a költségek csökkentése, vagy méretezheti a data warehouse-adattárházat teljesítményigények kielégítése. 

## <a name="what-is-compute-management"></a>Mi az számítási felügyeleti?
Az SQL Data Warehouse architektúrája elkülöníti a tárolást és számítást, így egymástól független méretezését. Ennek eredményeképpen a teljesítményigények kielégítése független adattárolás számítási skálázhatja. Is szüneteltetése és folytatása a számítási erőforrásokat. Ez az architektúra természetes következménye, hogy [számlázási](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) számítási és tárolási elkülönül. Ha nem szeretné használni az adattárház egy ideig, számítási költségeket takaríthat felfüggesztés számítási. 

## <a name="scaling-compute"></a>Számítás méretezése
Horizontális felskálázás, vagy vissza számítási méretezése módosításával a [adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) beállítása a data warehouse-hoz. Betöltés és lekérdezés teljesítmény költségráfordításokkal egyenes arányban növelhető további adattárházegységek hozzáadása során. 

Horizontális felskálázás lépéseiért lásd: a [az Azure portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), vagy [T-SQL](quickstart-scale-compute-tsql.md) rövid útmutatók. Horizontális felskálázási műveletek is elvégezheti egy [REST API-val](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

SQL Data Warehouse egy skálázási művelet végrehajtásához először megszakítja az összes bejövő lekérdezés, és visszavon annak biztosítása érdekében a konzisztens állapotú tranzakciókat. Méretezés csak akkor fordul elő a tranzakció visszaállítása után. Skálázási művelet esetén a rendszer leválasztja a tárolási réteget a számítási csomópontokról, számítási csomópontokat ad hozzá, és ezután újracsatlakoztatja a tárolási réteg számítási rétegben. Minden adatraktár 60 elosztás, amely egyenletesen lesznek elosztva a számítási csomópontoknak van tárolva. További számítási csomópontokat ad hozzá hozzáadja a nagyobb számítási teljesítmény. Ahogy a számítási csomópontok száma növekszik, csökkenti a disztribúciók számítási csomópontok számát, a lekérdezések nagyobb számítási teljesítmény biztosítása. Hasonlóképpen csökkenő adattárházegységek csökkenti a számítási csomópontok számát, ami csökkenti a számítási erőforrások lekérdezésekhez.

Az alábbi táblázat bemutatja, hogyan disztribúciók egy számítási csomópont megváltozik, az adattárházegységek számának módosításához.  DWU6000 60 számítási csomópontok biztosít, és sokkal magasabb lekérdezési teljesítményt, mint a DWU100 éri el. 

| Adattárházegységek  | \# Számítási csomópontok | \# csomópontonként disztribúciók |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Adattárházegységek megfelelő méretű keresése

A horizontális felskálázás esetén különösen nagyobb adattárházegységek, a teljesítmény előnyeinek használni kívánt legalább 1 TB-os adatkészlet. Adattárházegységek ajánlott száma az adattárház talál, próbálkozzon a felfelé és lefelé skálázást. Az adatok betöltése után futtatott néhány lekérdezést az adattárházegységek különböző számú. Mivel a skálázás nem időigényes, megpróbálhatja különböző teljesítményszintek, vagy kevesebb mint egy órán belül. 

Javaslatok keresése a legjobb számát adattárházegységek:

- Egy fejlesztési célú adattárházban, az adattárházegységek kevesebb kiválasztásával kezdődik.  Jó kiindulási pont DW400 vagy DW200.
- Figyelje meg az alkalmazás teljesítményéhez, figyelje a teljesítmény megfigyelte képest kiválasztott adattárházegységek számát.
- Tegyük fel, lineáris, és állapítsa meg, mennyit növelése vagy csökkentése érdekében az adattárházegységek szükséges. 
- Folytassa, amíg el nem éri az optimális teljesítmény érdekében szintet az üzleti igények szerint a szükséges módosításokat.

## <a name="when-to-scale-out"></a>Mikor érdemes horizontális felskálázás
Horizontális felskálázás adattárházegységek hatással van a teljesítmény következő aspektusait:

- Lineárisan növeli a vizsgálatokat, összesítések és a CTAS utasítások a rendszer teljesítményét.
- Olvasói és írók számát növeli az adatok betöltéséhez.
- Egyidejű lekérdezések és egyidejű hely maximális számát.

Adatok horizontális vonatkozó ajánlások adattárházegységek:

- Mielőtt nagy mennyiségű adat betöltenie vagy átalakítási műveletet hajt végre horizontális felskálázása az adatok gyorsabban elérhetővé.
- Csúcsidőszak munkaidőben horizontális felskálázás nagyobb számú lekérdezést befogadásához. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Mi történik, ha horizontális felskálázás nem növelhető teljesítménye?

Növelje a párhuzamosság adattárházegységek hozzáadása. Ha van a munkahelyi egyenletesen ossza fel a számítási csomópontok között, a további párhuzamosság javítja a lekérdezések teljesítményét. A horizontális skálázás nem változnak a teljesítményt, ha nincsenek néhány oka, hogy miért Ez akkor fordulhat elő. Az adatok esetleg torzítja, a disztribúciók közötti, illetve előfordulhat, hogy a lekérdezések nagy mennyiségű adatok áthelyezése bemutatása. Lekérdezési teljesítmény kapcsolatos problémák kivizsgálása, lásd: [teljesítményproblémáinak elhárítása](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Számítás szüneteltetése és folytatása
Felfüggesztése számítási hatására a tárolási réteg leválasztása a számítási csomópontokon. A számítási erőforrások a fiókjából jelennek meg. Nem terheli a számítási szünetelteti a számítást, miközben. Számítási folytatása újracsatlakoztatja a tároló a számítási csomópontok, és folytatja a számítási díjakat. Ha az egérmutatót egy data warehouse-bA:

* Számítási és memória-erőforrások a rendszer visszairányítja az adatközpontban lévő elérhető erőforrások készletét
* Data warehouse egység díjakat a szünet időtartama nulla.
* Nincs hatással az adattárolás, és az adatok érintetlen marad. 
* Az SQL Data Warehouse elveti az összes futó vagy aszinkron művelet.

Ha folytatja a data warehouse-bA:

* Az SQL Data Warehouse számítási és memória-erőforrásokat az adattárházegységek beállítása a szerez be.
* COMPUTE díja a data warehouse egységek folytatása.
* Az adatok elérhetővé válnak.
* Után az adatraktár online állapotban, akkor kell indítania a számítási feladatok lekérdezéseit.

Ha azt szeretné, az adattárház elérhető, fontolja meg a legkisebb méretre méretezés, a felfüggesztés helyett. 

A felfüggeszteni és folytatni a lépéseket, lásd: a [az Azure portal](pause-and-resume-compute-portal.md), vagy [PowerShell](pause-and-resume-compute-powershell.md) rövid útmutatók. Is használhatja a [REST API-val szüneteltetése](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) vagy a [folytatódik a REST API-val](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tranzakciók ürítése felfüggesztés vagy méretezés előtt
Javasoljuk, hogy a meglévő tranzakciók befejeződik a felfüggesztés vagy méretezés művelet elindítása előtt.

Ha felfüggeszti vagy méretezi az SQL Data Warehouse-t, a felfüggesztési vagy méretezési kérelem elindításakor a színfalak mögött a lekérdezések megszakadnak.  Egy egyszerű választó lekérdezés megszakítása gyors művelet, amelynek szinte semmilyen hatása nincs az üzemelő példány felfüggesztéséhez vagy méretezéséhez szükséges időtartamra.  Azonban a tranzakciós lekérdezések, amelyek adatokat vagy az adatok szerkezetét is módosítják, nem biztos, hogy ilyen hamar le tudnak állni.  **A tranzakciós lekérdezéseknek definíciójuk szerint vagy teljesen be kell fejeződniük, vagy vissza kell állítaniuk az általuk végrehajtott módosításokat.**  A tranzakciós lekérdezések által elvégzett módosítások visszaállítása ugyanannyi, vagy akár hosszabb ideig is tarthat, mint a lekérdezés által elvégzett eredeti módosítás végrehajtása.  Például ha megszakít egy olyan lekérdezést, amely sorokat törölt és már egy órája futott, a rendszernek egy újabb órájába telhet, hogy a törölt sorokat visszaállítsa.  Ha a felfüggesztést vagy a méretezést olyankor futtatja, amikor épp tranzakciók vannak folyamatban, a felfüggesztés vagy a méretezés látszólag hosszú időt vehet igénybe, mivel e műveleteknek meg kell várniuk, amíg a visszaállítás lezajlik.

Lásd még: [tranzakciók megismerése](sql-data-warehouse-develop-transactions.md), és [tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Számítási felügyeleti automatizálása
A számítási műveletek automatizálása, tekintse meg a [kezelés compute az Azure functions](manage-compute-with-azure-functions.md).

A horizontális felskálázás, szüneteltetése és folytatása műveletek mindegyike több percet is igénybe vehet. Ha meg vannak méretezés, a felfüggesztés, vagy automatikusan folytatása, javasoljuk, hogy annak érdekében, hogy logikai végrehajtási bizonyos művelet befejeződött, egy másik művelet végrehajtása előtt. Az adatraktár állapota különböző végpontok ellenőrzése lehetővé teszi megfelelően automation az ilyen műveletek végrehajtásához. 

Az adattárház állapotának ellenőrzése, tekintse meg a [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) vagy [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) rövid. Az adatraktár állapota is ellenőrizheti a [REST API-val](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Engedélyek

Az adatraktár szükséges a leírt engedélyek [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Szünet és folytatás megkövetelése a [SQL-Adatbázisok Közreműködője](../role-based-access-control/built-in-roles.md#sql-db-contributor) engedéllyel, kifejezetten Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>További lépések
Számítási erőforrások kezelési szerepet játszó másik tényező különböző számítási erőforrásokat lefoglalni az egyes lekérdezések esetében. További információkért lásd: [erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).
