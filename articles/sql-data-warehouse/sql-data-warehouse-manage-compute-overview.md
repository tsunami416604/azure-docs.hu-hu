---
title: Az Azure SQL Data Warehouse számítási erőforrás kezelése |} Microsoft Docs
description: További információk a képességek az Azure SQL Data Warehouse teljesítményét kibővítési. Horizontális felskálázás az adatraktár felfüggesztésével dwu-k vagy alacsonyabb költségek beállításával.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ca6d34d3b670bfd05a9b65fe9e6b260120e3a5b8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse számítási kezelése
További tudnivalók az Azure SQL Data Warehouse számítási erőforrások kezelése. Az adatraktár felfüggesztésével csökkenthetők a költségek, vagy skálázhatja azt az adatraktár a teljesítményigények kielégítése érdekében. 

## <a name="what-is-compute-management"></a>Mi az az számítási management?
Az SQL Data Warehouse architektúrája elválasztja a tárolást és számítást, hogy egymástól független méretezését. Ennek eredményeképpen a teljesítményigények kielégítése érdekében független adattárolási számítási méretezheti. Is szüneteltetése és folytatása a számítási erőforrásokat. Ez az architektúra természetes következménye, hogy [számlázási](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) számítási és tárolási elkülönül. Ha nem szeretné használni az adatraktár egy ideig, számítási költségeket takaríthat felfüggesztéséhez számítási. 

## <a name="scaling-compute"></a>Számítás méretezése
Horizontális felskálázás, vagy vissza számítási méretezése beállításával a [az adatraktár-egység](what-is-a-data-warehouse-unit-dwu-cdwu.md) az adatraktár beállítása. Be- és a lekérdezési teljesítmény lineárisan növelheti a további adattárházegységek való hozzáadása során. 

Kibővített lépéseiért lásd: a [Azure-portálon](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), vagy [T-SQL](quickstart-scale-compute-tsql.md) quickstarts. A kibővített műveleteket is végezhet egy [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

A skálázási művelet végrehajtása, az SQL Data Warehouse először használhatatlanná teszi az összes bejövő lekérdezés és visszavon tranzakciók konzisztens állapotú biztosításához. Skálázás csak akkor fordul elő a tranzakció-visszagörgetés befejezése után. A skálázási művelet a rendszer leválasztja a tárolási réteg a számítási csomópontok, hozzáadja a számítási csomópontok és majd reattaches a tárolási rétegben a számítási rétegét. Minden adatraktár 60 azokat a terjesztéseket, amelyek egyenletesen oszlanak meg a számítási csomópontok tárolja. További számítási csomópontok hozzáadása ad hozzá több számítási teljesítményt. A számítási csomópontok száma növekszik, azokat a terjesztéseket egyes számítási csomópontok száma csökken, további számítási teljesítményt biztosít a lekérdezések. Hasonlóképpen csökkenő adattárházegységek csökkenti a számítási csomópontok, ami csökkenti a számítási erőforrásokat, a lekérdezések száma.

Az alábbi táblázat bemutatja, hogyan számítási csomópont változik a adattárházegységek / terjesztéseket száma módosítani.  DWU6000 60 számítási csomópontok biztosít, és sokkal nagyobb lekérdezési teljesítményt, mint DWU100 éri el. 

| Adattárházegységek  | \# a számítási csomópontok | \# az azokat a terjesztéseket, csomópontonként |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>A megfelelő a mérete adattárházegységek keresése

A teljesítmény előnyeinek kihasználása érdekében nagyobb adattárházegységek, különösen a kiterjesztése használni kívánt legalább 1 TB méretű adatkészlet. Adattárházegységek ajánlott száma az adatraktár megkereséséhez próbálkozzon a skálázás felfelé és lefelé. Különböző számú adattárházegységek néhány lekérdezést futtassa az adatok betöltése után. Mivel a skálázás nem időigényes, próbálja meg különböző teljesítményszintek vagy kevesebb mint egy óra alatt. 

Javaslatok a legjobb számát kereséséhez adatraktár-egység:

- Az adatok a fejlesztési először egy kisebb számú adattárházegységek kijelölése.  Jó kiindulópont DW400 vagy DW200.
- A figyelheti az alkalmazások teljesítménye, betartásával erőforrásigények teljesítéséhez képest kijelölt adattárházegységek száma.
- Skálán lineáris tegyük fel, és határozza meg, hogy mennyit kell növeli vagy csökkenti a adattárházegységek. 
- Folytatható, amíg el nem éri az optimális teljesítmény szintű üzleti igényeinek a szükséges módosításokat.

## <a name="when-to-scale-out"></a>Mikor érdemes horizontális felskálázása
Adattárházegységek kiterjesztése hatással van a teljesítmény következő aspektusait:

- Lineárisan javítja a teljesítményt, a rendszer a vizsgálatokat, összesítések és CTAS utasításokat.
- Az adatok betöltése olvasók és írók száma növekszik.
- Maximális száma párhuzamos lekérdezések és feldolgozási tárolóhelye.

Mikor érdemes méretezni kimenő javaslatok adatraktár-egység:

- Az adatok gyorsabban elérhetővé kiterjesztése előtt nagy mennyiségű adat betöltenie vagy átalakítási műveletet hajt végre.
- Csúcsidőszak munkaidőben horizontális felskálázás párhuzamos lekérdezések nagy mennyiségű befogadásához. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Mi történik, ha kiterjesztése nem növelhető teljesítménye?

Adattárházegységek párhuzamosságát növelése hozzáadása. Ha a munkahelyi egyenlően elosztva a számítási csomópontok, további párhuzamosságát növeli a teljesítmény-küszöbérték. Méretezési out nem változik a teljesítményt, ha nincsenek okok miatt miért Ez akkor fordulhat elő. Az adatok a előfordulhat, hogy a megfelelő kiadásának között válik egyenetlenné, vagy előfordulhat, hogy a lekérdezések adatátvitel nagy mennyiségű bemutatása. Vizsgálja meg a lekérdezés teljesítménnyel kapcsolatos problémákat, tekintse meg a [teljesítmény hibaelhárítási](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Számítási felfüggesztése és folytatása
Felfüggesztése számítási azt eredményezi, a tárolási rétegben válassza le a számítási csomópontok. A számítási erőforrásokat kiadott fiókjából. Van nem szó, a számítás során a számítási fel van függesztve. A Folytatás számítási reattaches tárhelyet a számítási csomópontok és díjak számítás folytatja. Ha az egérmutatót egy adatraktár:

* Számítási és memória-erőforrások a rendszer visszairányítja az adatközpontban lévő rendelkezésre álló erőforrások készlete
* Az adatraktár egység költségek olyan nulla a szünet időtartama.
* Ez nem befolyásolja az adatok tárolási és az adatok érintetlen marad. 
* Az SQL Data Warehouse visszavonja az összes futó vagy aszinkron művelet.

Ha egy adatraktár folytathat:

* Az SQL Data Warehouse az adattárházegységek beállítása a szerez be számítási és memória-erőforrásokat.
* A data warehouse egységek folytatása költségekkel számítási.
* Az adatok elérhetővé válnak.
* Miután az adatraktár online állapotban, szüksége indítsa újra a munkaterhelés lekérdezések.

Ha azt szeretné, az adatraktár érhető el, fontolja meg a skálázás azt le a legkisebb mérete, ahelyett hogy felfüggesztése. 

A szüneteltetése és folytatása lépéseket, tekintse meg a [Azure-portálon](pause-and-resume-compute-portal.md), vagy [PowerShell](pause-and-resume-compute-powershell.md) quickstarts. Is használhatja a [REST API szüneteltetése](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) vagy a [folytatása REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tranzakciók ürítése felfüggesztés vagy méretezés előtt
Azt javasoljuk, így a meglévő tranzakció befejezését, mielőtt elindít egy szüneteltetése és a skálázási művelet.

Ha felfüggeszti vagy méretezi az SQL Data Warehouse-t, a felfüggesztési vagy méretezési kérelem elindításakor a színfalak mögött a lekérdezések megszakadnak.  Egy egyszerű választó lekérdezés megszakítása gyors művelet, amelynek szinte semmilyen hatása nincs az üzemelő példány felfüggesztéséhez vagy méretezéséhez szükséges időtartamra.  Azonban a tranzakciós lekérdezések, amelyek adatokat vagy az adatok szerkezetét is módosítják, nem biztos, hogy ilyen hamar le tudnak állni.  **A tranzakciós lekérdezéseknek definíciójuk szerint vagy teljesen be kell fejeződniük, vagy vissza kell állítaniuk az általuk végrehajtott módosításokat.**  A tranzakciós lekérdezések által elvégzett módosítások visszaállítása ugyanannyi, vagy akár hosszabb ideig is tarthat, mint a lekérdezés által elvégzett eredeti módosítás végrehajtása.  Például ha megszakít egy olyan lekérdezést, amely sorokat törölt és már egy órája futott, a rendszernek egy újabb órájába telhet, hogy a törölt sorokat visszaállítsa.  Ha a felfüggesztést vagy a méretezést olyankor futtatja, amikor épp tranzakciók vannak folyamatban, a felfüggesztés vagy a méretezés látszólag hosszú időt vehet igénybe, mivel e műveleteknek meg kell várniuk, amíg a visszaállítás lezajlik.

Lásd még: [ismertetése tranzakciók](sql-data-warehouse-develop-transactions.md), és [tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Számítási felügyeletének automatizálására
A számítási felügyeleti műveletek automatizálását, lásd: [kezelése számítási az Azure functions](manage-compute-with-azure-functions.md).

Egyes a kibővített, szüneteltetése és folytatása művelet több percet is igénybe vehet. Ha van folyamatban, szüneteltetése, vagy automatikusan folytatása, azt javasoljuk, végrehajtási programot annak biztosításához, hogy bizonyos műveletek végrehajtását befejeződött egy másik művelet folytatása előtt. Az adatraktár állapota keresztül különböző végpontok ellenőrzése lehetővé teszi a megfelelő valósítja meg az ilyen műveletek automatizálását. 

Az adatraktár állapota ellenőrzéséhez tekintse meg a [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) vagy [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) gyors üzembe helyezés. Az adatraktár állapota is ellenőrizheti a [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Engedélyek

Az adatraktár skálázás az engedélyekkel kell rendelkeznie a leírt [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Szüneteltetése és folytatása szükséges a [SQL DB Contributor](../role-based-access-control/built-in-roles.md#sql-db-contributor) engedéllyel, akkor a kifejezetten Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>További lépések
Számítási erőforrások kezelési szerepet játszó másik tényező különböző számítási erőforrásokat lefoglalni az egyes lekérdezések. További információkért lásd: [erőforrás-osztályok a munkaterhelés felügyeleti](resource-classes-for-workload-management.md).
