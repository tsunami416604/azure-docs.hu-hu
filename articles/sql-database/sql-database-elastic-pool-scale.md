---
title: Rugalmas készletek erőforrásainak méretezése
description: Ez a lap ismerteti az Azure SQL Database rugalmas készletei erőforrások méretezését.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462598"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Rugalmas készleterőforrások méretezése az Azure SQL Database-ben

Ez a cikk ismerteti, hogyan skálázható a számítási és tárolási erőforrások rugalmas készletek és az Azure SQL Database-ben elérhető rugalmas készletek és készletezett adatbázisok.

## <a name="change-compute-resources-vcores-or-dtus"></a>Számítási erőforrások (virtuális magok vagy DT-k) módosítása

Miután először kiválasztotta a virtuális magok vagy eDVO-k számát, dinamikusan skálázhatja a rugalmas készletet az [Azure Portal,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)a [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)az [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)tényleges felhasználói élménye alapján.

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási szint módosításának vagy a számítási méret átméretezésének hatása

A rugalmas készlet szolgáltatási szintjének vagy számítási méretének módosítása hasonló mintát követ, mint az egyes adatbázisok esetében, és főként a következő lépéseket hajtja végre a szolgáltatás:

1. Új számítási példány létrehozása a rugalmas készlethez  

    A rugalmas készlet új számítási példánya jön létre a kért szolgáltatási szinttel és számítási mérettel. A szolgáltatási szint és a számítási méret változásainak egyes kombinációi hozatása érdekében az egyes adatbázisok replikáját kell létrehozni az új számítási példányban, amely adatok másolásával jár, és erősen befolyásolhatja az általános késést. Ettől függetlenül az adatbázisok online állapotban maradnak ebben a lépésben, és a kapcsolatok továbbra is az eredeti számítási példányban az adatbázisokhoz lesznek irányítva.

2. Kapcsolatok átirányítása új számítási példányra

    Az eredeti számítási példányban lévő adatbázisokhoz való meglévő kapcsolatok megszakadnak. Minden új kapcsolat jön létre az új számítási példány ban az adatbázisokhoz. A szolgáltatási szint és a számítási méret változásainak egyes kombinációi esetén az adatbázisfájlok leválnak és újracsatolódnak a váltás során.  Ettől függetlenül a kapcsoló rövid szolgáltatásmegszakítást eredményezhet, ha az adatbázisok általában 30 másodpercnél rövidebb ideig, de gyakran csak néhány másodpercig nem érhetők el. Ha a kapcsolatok elvetésekén hosszú ideig futó tranzakciók futnak, a lépés időtartama hosszabb időt vehet igénybe a megszakított tranzakciók helyreállítása érdekében. [A gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md) csökkentheti a hosszú ideig futó tranzakciók megszakításának hatását.

> [!IMPORTANT]
> A munkafolyamat bármely lépése során nem vesznek el adatok.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatásszint módosításának vagy a számítási méret átméretezésének késése

A szolgáltatási szint módosításának vagy egyetlen adatbázis vagy rugalmas készlet számítási méretének átméretezéséhez szükséges becsült késés a következőképpen van paraméterezve:

|Szolgáltatásszint|Alapvető egységes adatbázis,</br>Standard (S0-S1)|Alapvető rugalmas medence,</br>Szabvány (S2-S12), </br>Nagy léptékű, </br>Általános célú egyetlen adatbázis vagy rugalmas készlet|Prémium vagy üzleti legkritikusabb egyetlen adatbázis vagy rugalmas készlet|
|:---|:---|:---|:---|
|**Alapszintű adatbázis,</br> Standard (S0-S1)**|&bull;&nbsp;Állandó időkésleltetés a felhasznált területtől függetlenül</br>&bull;&nbsp;Jellemzően kevesebb, mint 5 perc|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|
|**Alapszintű rugalmas </br>készlet, Standard (S2-S12), </br>Nagykapacitású, </br>Általános célú egy-adatbázis vagy rugalmas készlet**|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|&bull;&nbsp;Állandó időkésleltetés a felhasznált területtől függetlenül</br>&bull;&nbsp;Jellemzően kevesebb, mint 5 perc|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|
|**Prémium vagy üzleti legkritikusabb egyetlen adatbázis vagy rugalmas készlet**|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|

> [!NOTE]
>
> - A szolgáltatási szint módosítása vagy egy rugalmas készlet átméretezése esetén a készlet összes adatbázisában használt terület összegzését kell használni a becslés kiszámításához.
> - Az adatbázis rugalmas készletbe való áthelyezése esetén csak az adatbázis által használt terület befolyásolja a késést, nem pedig a rugalmas készlet által használt terület.
>
> [!TIP]
> A folyamatban lévő műveletek figyeléséhez lásd: [Műveletek kezelése az SQL REST API használatával](https://docs.microsoft.com/rest/api/sql/operations/list), Műveletek kezelése [CLI használatával](/cli/azure/sql/db/op), Műveletek [figyelése T-SQL használatával,](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) és a két PowerShell-parancs: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) és [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>További szempontok a szolgáltatási szint módosításakor vagy a számítási méret átméretezésekor

- Virtuális magok vagy eDT-k rugalmas készlet hez történő lebontásakor a készlet használt területkisebbnek kell lennie, mint a cél szolgáltatási szint és a készlet eDT-k maximális anamfora.
- Rugalmas készlet eDUS-ok átméretezésekor többlettárolási költség akkor jelentkezik, ha (1) a tároló maximális méretét a célkészlet támogatja, és (2) a tárolási maximális mérete meghaladja a célkészlet foglalt tárolási mennyiségét. Ha például egy 100 eDTU standard készlet maximális mérete 100 GB van -ig egy 50 eDTU Standard készlet, majd egy extra tárolási költség vonatkozik, mivel a célkészlet támogatja a maximális mérete 100 GB, és a benne foglalt tárolási összeg csak 50 GB. Így az extra tárhely 100 GB – 50 GB = 50 GB. Az extra tárhely díjszabásáról az [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/)című témakörben található. Ha a ténylegesen felhasznált terület kisebb, mint a benne foglalt tárterület, akkor ez a többletköltség elkerülhető az adatbázis maximális méretének a benne foglalt összegre történő csökkentésével.

### <a name="billing-during-rescaling"></a>Számlázás átméretezés közben

A számlázás minden órában az adatbázis létezik a legmagasabb szolgáltatási szint + számítási méret, amely az adott órában alkalmazott, függetlenül a ttól, hogy a használat, vagy hogy az adatbázis aktív volt kevesebb, mint egy óra. Ha például egyetlen adatbázist hoz létre, és öt perccel később törli, a számla egy adatbázis-óra díját tükrözi.

## <a name="change-elastic-pool-storage-size"></a>Rugalmas készlettárolási méret módosítása

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

- A tárterület a maximális méretkorlátig építhető ki:

  - A normál vagy általános célú szolgáltatási szinteken történő tárolás esetén növelje vagy csökkentse a méretet 10 GB-os lépésekben
  - A prémium vagy üzleti legkritikusabb szolgáltatási szinteken való tárolás esetén 250 GB-os lépésekben növelje vagy csökkentse a méretet
- A rugalmas készlet tárolása a maximális méret növelésével vagy csökkentésével építhető ki.
- A rugalmas készlet tárolási ára a tárolási összeg és a szolgáltatási szint tárolási egységára szorozva. Az extra tárhely áráról az [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/)című témakörben talál részleteket.

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

- A rugalmas készlet eDTU-ára tartalmaz egy bizonyos mennyiségű tárhelyet további költségek nélkül. A benne foglalt összeget meghaladó további tárterület a maximális méretkorlátig 250 GB-ig, 1 TB-ig, majd 1 TB-on túl256 GB-os lépésekben is kiépíthető. A benne foglalt tárolási mennyiségeket és a maximális méretkorlátokat [lásd: Rugalmas készlet: tárolóméretek és számítási méretek.](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)
- A rugalmas készlet további tárháza az [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), a [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)az [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)használatával történő maximális méretének növelésével építhető ki.
- A rugalmas készlet extra tárkészlettöbblet-tárházára az extra tárterület és a szolgáltatási szint extra tárolási egységára megszorzható. Az extra tárhely áráról az [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/)című témakörben talál részleteket.

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

## <a name="next-steps"></a>További lépések

Az általános erőforráskorlátokat az [SQL Database virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md) és SQL Database [DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md)című témakörben tartalmazza.
