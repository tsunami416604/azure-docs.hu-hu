---
title: Rugalmas készletek erőforrásainak méretezése
description: Ez az oldal a Azure SQL Database rugalmas készletei erőforrásainak méretezését ismerteti.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77462598"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Rugalmas készlet erőforrásainak méretezése Azure SQL Database

Ez a cikk bemutatja, hogyan méretezhetők a rugalmas készletekhez és a készletezett adatbázisokhoz rendelkezésre álló számítási és tárolási erőforrások Azure SQL Databaseban.

## <a name="change-compute-resources-vcores-or-dtus"></a>Számítási erőforrások módosítása (virtuális mag vagy DTU)

A virtuális mag vagy a Edtu számának első kiválasztását követően a rugalmas készletet dinamikusan felfelé vagy lefelé méretezheti a tényleges tapasztalatok alapján a [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), a [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), az [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)használatával.

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási réteg módosításának vagy a számítási méret átméretezésének a következményei

Egy rugalmas készlet szolgáltatási rétegének vagy számítási méretének módosítása hasonló mintát követ, mint az önálló adatbázisok esetében, és főleg a következő lépéseket hajtja végre:

1. Új számítási példány létrehozása a rugalmas készlethez  

    A rendszer létrehoz egy új számítási példányt a rugalmas készlethez a kért szolgáltatási szintű és számítási mérettel. A szolgáltatási szintek és a számítási méretek bizonyos kombinációinak változásakor az egyes adatbázisok replikáját az új számítási példányban kell létrehozni, amely az adatok másolását és a teljes késést is jelentősen befolyásolhatja. Függetlenül attól, hogy az adatbázisok online állapotban maradnak ebben a lépésben, és a kapcsolatok továbbra is az eredeti számítási példány adatbázisaira lesznek irányítva.

2. Az új számítási példánnyal létesített kapcsolatok útválasztásának váltása

    A rendszer elveti az eredeti számítási példány adatbázisainak meglévő kapcsolatait. Minden új kapcsolat létrejön az új számítási példány adatbázisaiban. A szolgáltatási szintek és a számítási méret változásainak bizonyos kombinációi esetén az adatbázisfájlok le lesznek választva, és a kapcsoló során újra vannak csatolva.  Függetlenül attól, hogy a kapcsoló egy rövid szolgáltatási megszakítást eredményezhet, ha az adatbázisok nem érhetők el általában kevesebb mint 30 másodpercig, és gyakran csak néhány másodpercig. Ha a kapcsolatok eldobásakor hosszú ideig futó tranzakciók futnak, a lépés időtartama a megszakított tranzakciók helyreállításához hosszabb időt vehet igénybe. A [gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md) csökkentheti a hosszan futó tranzakciók megszakításának hatását.

> [!IMPORTANT]
> A munkafolyamat egyik lépése sem vesz fel adatvesztést.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási réteg módosításának késése vagy a számítási méret átméretezése

A szolgáltatási réteg módosításának becsült késése vagy egy önálló adatbázis vagy rugalmas készlet számítási méretének átméretezése a következő paraméterekkel történik:

|Szolgáltatásszint|Alapszintű önálló adatbázis,</br>Standard (S0-S1)|Alapszintű rugalmas készlet,</br>Standard (S2-S12), </br>Nagy kapacitású </br>Önálló adatbázis vagy rugalmas készlet általános célú|Prémium vagy üzletileg kritikus önálló adatbázis vagy rugalmas készlet|
|:---|:---|:---|:---|
|**Alapszintű önálló</br> adatbázis, Standard (S0-S1)**|&bull;&nbsp;Állandó időbeli késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 5 perc|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Alapszintű rugalmas </br>készlet, Standard (S2-S12 </br>), </br>nagy kapacitású, általános célú önálló adatbázis vagy rugalmas készlet**|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Állandó időbeli késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 5 perc|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Prémium vagy üzletileg kritikus önálló adatbázis vagy rugalmas készlet**|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|

> [!NOTE]
>
> - A szolgáltatási szint vagy a rugalmas készletre vonatkozó számítási kapacitás módosítása esetén a készletben lévő összes adatbázisban használt terület összesítését kell használni a becslés kiszámításához.
> - Ha az adatbázist egy rugalmas készletre vagy-re helyezi át, csak az adatbázis által használt terület befolyásolja a késést, nem a rugalmas készlet által használt területet.
>
> [!TIP]
> A folyamatban lévő műveletek figyeléséhez tekintse meg a következő témakört: [műveletek kezelése az SQL REST API használatával](https://docs.microsoft.com/rest/api/sql/operations/list), műveletek [kezelése a CLI](/cli/azure/sql/db/op)használatával, a műveletek [FIGYELÉSe a T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) használatával és a következő két PowerShell-paranccsal: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) és [stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási réteg módosításának és a számítási méret átméretezésének további szempontjai

- A rugalmas készlet virtuális mag vagy Edtu lecsökkentésekor a készlet felhasznált területének kisebbnek kell lennie, mint a cél szolgáltatási szintje és a készlet Edtu maximálisan megengedett mérete.
- A rugalmas készletek Edtu átméretezése esetén a rendszer extra tárterületet alkalmaz, ha (1) a készlet tárterületének maximális méretét a cél készlet támogatja, és (2) a tárterület maximális mérete meghaladja a cél készletben foglalt tárterületet. Ha például egy 100 eDTU standard szintű, 100 GB-os maximális mérettel rendelkező készletet egy 50 eDTU standard készletbe foglal le, akkor a rendszer extra tárolási díjat számít fel, mivel a célként megadott készlet támogatja a 100 GB-os maximális méretet, és a benne foglalt tárolási mennyiség csak 50 GB. Így a további tárterület mérete 100 GB – 50 GB = 50 GB. Az extra tárterület díjszabását lásd: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/). Ha a felhasznált terület tényleges mennyisége kisebb, mint a foglalt tárterület, akkor ez az extra díj elkerülhető, ha az adatbázis maximális méretét a benne foglalt mennyiségre csökkenti.

### <a name="billing-during-rescaling"></a>Számlázás az átméretezés során

Az adatbázis óránkénti számlázása az adott órában alkalmazott legmagasabb szolgáltatási réteg + számítási méret alapján történik, függetlenül a használattól, illetve attól, hogy az adatbázis egy óránál kevesebb ideig volt-e aktív. Ha például létrehoz egy adatbázist, és öt perccel később törli azt, akkor a számla egy adatbázis-órára vonatkozó díjat számít fel.

## <a name="change-elastic-pool-storage-size"></a>Rugalmas készlet tárolási méretének módosítása

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

- A tárterület a maximális méretkorlát alapján állítható be:

  - A standard vagy általános célú szolgáltatási rétegekben történő tároláshoz a 10 GB-os növekmények méretének növelése vagy csökkentése
  - A prémium vagy az üzleti szempontból kritikus szolgáltatási rétegekben történő tároláshoz a méret növelése vagy csökkentése 250 GB-onként
- A rugalmas készletek tárterületét a maximális méret növelésével vagy csökkentésével lehet kiépíteni.
- A rugalmas készlet tárterületének ára a tárolási kapacitás, a szolgáltatási szintet tartalmazó tárolási egység árával megszorozva. További információ az extra tárterület díjszabásáról: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

- A rugalmas készlet eDTU díja bizonyos mennyiségű tárterületet foglal magában, többletköltség nélkül. A benne foglalt mennyiségen túli extra tárterület kiépíthető a maximális méretkorlát 250 GB-ig, 1 TB-ig, majd 256 GB-onként 1 TB-nál nagyobb mértékben. A foglalt tárolási és a maximális méretkorlát esetében lásd [: rugalmas készlet: a tárolási méretek és a számítási méretek](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- A rugalmas készletek extra tárterületét a [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), a [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), az [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)használatával lehet kiépíteni a maximális méret növelésével.
- A rugalmas készlet extra tárterületének díja a további tárterület mérete, a szolgáltatási szinten található extra tárolási egység árával megszorozva. További információ az extra tárterület díjszabásáról: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](sql-database-file-space-management.md).

## <a name="next-steps"></a>További lépések

A teljes erőforrás-korlátokkal kapcsolatban lásd: [SQL Database virtuális mag-alapú erőforrás-korlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md) és [SQL Database DTU-alapú erőforrás-korlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md).
