---
title: A méretezési csoport rugalmas készlet erőforrás - Azure SQL Database |} A Microsoft Docs
description: Ez az oldal méretezési erőforrásokat az Azure SQL Database rugalmas készletek ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/06/2019
ms.openlocfilehash: b2ad701115a69520658c2aa9cea53dbda90cf868
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726754"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Az Azure SQL Database rugalmas készlet erőforrások skálázása

Ez a cikk ismerteti az Azure SQL Database rugalmas készletek és a készletezett adatbázisok rendelkezésre álló számítási és tárolási erőforrások méretezése.

## <a name="change-compute-resources-vcores-or-dtus"></a>Módosítsa a számítási erőforrásokat (virtuális magok vagy dtu-k)

Miután kiválasztotta a virtuális magok vagy edtu-k számát, skálázhatja rugalmas készlet felfelé vagy lefelé dinamikusan használatával a tényleges tapasztalatok alapján a [az Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), a [Azure CLI-vel ](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), vagy a [REST API-val](https://docs.microsoft.com/rest/api/sql/elasticpools/update).


### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Szolgáltatási szint vagy átméretezésekor számítási méret módosításának hatása

A szolgáltatás díjcsomag vagy számítási rugalmas készlet mérete mint önálló adatbázisok esetében hasonló mintát követi, és főként magában foglalja a szolgáltatás a következő lépésekkel:

1. Új számítási példány a rugalmas készlet létrehozása  

    A rugalmas készlet új számítási példány a kért szolgáltatási szint és a számítási méret jön létre. Szolgáltatási szint és a számítási változásokat néhány kombinációit minden egyes adatbázis-replika kell létrehozni az új számítási példány, amelyeket át kell másolnia az adatokat, és erősen befolyásolhatja a teljes késést. Függetlenül attól ezzel a lépéssel az adatbázis online állapotban maradnak, és a rendszer az eredeti compute-példány adatbázisai kapcsolatok továbbra is.

2. Váltson a kapcsolatok új számítási példány Útválasztás

    Az eredeti számítási példány adatbázishoz meglévő kapcsolatok megszakadnak. Minden olyan új létesít kapcsolatokat, az adatbázisok a az új számítási példány. A szolgáltatási szint és a számítási változásokat egyes kombinációi adatbázisfájlok leválasztott és csatolni a váltás során.  Függetlenül attól a kapcsoló eredményezhet a szolgáltatás rövid megszakítás amikor adatbázis nem érhető el, általában kevesebb mint 30 másodpercig és gyakran csak néhány másodpercig. Ha hosszú ideig futó tranzakció futtatását, ha a kapcsolatok megszakadnak, ez a lépés időtartama hosszabb időt vehet igénybe megszakított tranzakciók száma a helyreállításhoz. [A gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md) csökkentheti a hosszú ideig futó tranzakció megszakad.

> [!IMPORTANT]
> Nincs adat a munkafolyamat bármely lépése során elvész.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási szint vagy átméretezésekor számítási mérete változó késés

A késést, módosíthatja a szolgáltatásszintet, vagy egy önálló adatbázis vagy a rugalmas készletet a számítási méretezés a paraméteres módon:

|Szolgáltatásszint|Alapszintű önálló adatbázis,</br>Standard szintű (S0-S1 esetén)|Alapszintű rugalmas készletek</br>Standard (S2-S12) </br>Nagy kapacitású, </br>Általános célú önálló adatbázist vagy a rugalmas készlet|Prémium szintű és az üzletileg kritikus önálló adatbázist vagy rugalmas készlet|
|:---|:---|:---|:---|
|**Alapszintű önálló adatbázis,</br> standard szintű (S0-S1 esetén)**|&bull; &nbsp;Állandó késleltetés független a felhasznált lemezterület</br>&bull; &nbsp;Általában, kevesebb mint 5 perc|&bull; &nbsp;Adatbázis elfoglalt miatt az adatmásolás arányos késést</br>&bull; &nbsp;Általában, kevesebb, mint a felhasznált lemezterület GB-onként 1 perc|&bull; &nbsp;Adatbázis elfoglalt miatt az adatmásolás arányos késést</br>&bull; &nbsp;Általában, kevesebb, mint a felhasznált lemezterület GB-onként 1 perc|
|**Alapszintű rugalmas készletek </br>(S2-S12), Standard </br>nagy kapacitású, </br>általános célú önálló adatbázist vagy rugalmas készlet**|&bull; &nbsp;Adatbázis elfoglalt miatt az adatmásolás arányos késést</br>&bull; &nbsp;Általában, kevesebb, mint a felhasznált lemezterület GB-onként 1 perc|&bull; &nbsp;Állandó késleltetés független a felhasznált lemezterület</br>&bull; &nbsp;Általában, kevesebb mint 5 perc|&bull; &nbsp;Adatbázis elfoglalt miatt az adatmásolás arányos késést</br>&bull; &nbsp;Általában, kevesebb, mint a felhasznált lemezterület GB-onként 1 perc|
|**Prémium szintű és az üzletileg kritikus önálló adatbázist vagy rugalmas készlet**|&bull; &nbsp;Adatbázis elfoglalt miatt az adatmásolás arányos késést</br>&bull; &nbsp;Általában, kevesebb, mint a felhasznált lemezterület GB-onként 1 perc|&bull; &nbsp;Adatbázis elfoglalt miatt az adatmásolás arányos késést</br>&bull; &nbsp;Általában, kevesebb, mint a felhasznált lemezterület GB-onként 1 perc|&bull; &nbsp;Adatbázis elfoglalt miatt az adatmásolás arányos késést</br>&bull; &nbsp;Általában, kevesebb, mint a felhasznált lemezterület GB-onként 1 perc|

> [!NOTE]
>
> - A szolgáltatási szint módosítása vagy rugalmas készletek számítási átméretezése, az összegzés a készletben található összes adatbázis teljes használt terület használandó alapján számítja ki a becslést.
> - Adatbázis áthelyezése rugalmas készletek és a, esetén csak az adatbázis által használt területet a késést, nem az a rugalmas készlet által használt területet hatással van.
>
> [!TIP]
> A folyamatban lévő műveletek monitorozására, tekintse meg: [SQL REST API használatával műveleteinek kezelésére](https://docs.microsoft.com/rest/api/sql/operations/list), [kezelése CLI használatával az operations](/cli/azure/sql/db/op), [T-SQL használatával végzett műveletek monitorozására](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) és a következő két PowerShell-parancsokat: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) and [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>További szempontok módosításakor szolgáltatási szint vagy átméretezésekor számítási mérete

- Amikor virtuális magok vagy rugalmas készletek edtu-k downsizing, használt készlet mérete kisebb, mint a megengedett maximális mérete a cél szolgáltatási szint és a készlet edtu-k kell lennie.
- Átméretezésekor virtuális magok vagy rugalmas készletek edtu-k, amikor egy extra tárterület költséget akkor érvényes, ha (1) a tároló a készlet maximális mérete a célként megadott készlet által támogatott, és (2) a tároló maximális mérete meghaladja a célkészletet belefoglalt tárterület mennyiségét. Például ha egy 100 edtu-s Standard készlet 100 GB-os maximális mérettel van egy 50 edtu méretű Standard készletben való downsized, majd egy extra tárterület költség érvényes mert célkészlet támogatja a 100 GB-os maximális méretét, és a belefoglalt tárterület-mennyiség csak 50 GB-ot. Tehát az extra tárterület keletkezett a 100 GB – 50 GB = 50 GB. Extra tárterület díjszabással kapcsolatos információk: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). Ha a tényleges felhasznált lemezterület mérete kisebb, mint a csomagban foglalt tárhely, majd ezzel többletköltség elkerülhető az adatbázis maximális méretének csökkentésével a csomagban foglalt adatmennyiségen.

### <a name="billing-during-rescaling"></a>A számlázás során átméretezésekor

Számlázása óránként, a legmagasabb szolgáltatási szintet létezik adatbázis + compute-méretet, létezése alatt, hogy egy óránál kevesebb ideig volt az adatbázis aktív függetlenül. Például ha egy önálló adatbázis létrehozása, és öt perc múlva törli azt a számla egy adatbázisóráért díját tükrözi.

## <a name="change-elastic-pool-storage-size"></a>Rugalmas készlet tárolási méretének módosítása

### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

- A maximális méretkorlát legfeljebb tárolók kiépítésével:

  - A standard vagy általános célú szolgáltatásszinten tároláshoz növelése, vagy mérete 10 GB-os lépésekben csökkentése
  - A prémium szintű vagy üzletileg kritikus tárolás szolgáltatásszintek, növelje vagy csökkentse a 250-GB onként növelhető tárhelyet
- Rugalmas készletek tárolók kiépítésével növelésével vagy csökkentésével a maximális méretét.
- Tároló rugalmas készletek ára a tárolókapacitás, szorozva a szolgáltatási rétegben tárolási egységárát. Az extra tárterület ára a részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

- Rugalmas készlet edtu-k díjszabása tartalmaz egy bizonyos mennyiségű tárolási további költségek nélkül. A csomagban foglalt adatmennyiségen felüli extra tárterület legfeljebb 250 GB-os fel 1 TB-os egységekben, majd, 256 GB 1 TB-os léptékben maximális méretkorlátot díjfizetés mellett bővítheti. Belefoglalt tárterület összegek és a maximális méret korlátok [rugalmas készlet: tárterületet és számítási méretek](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Extra tárterület rugalmas készletek bővítheti a maximális méret használatával növelje a [az Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), a [Azure CLI-vel](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), vagy a [REST API-val ](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Az extra tárterület rugalmas készletek ára az extra tárterület keletkezett a szolgáltatási rétegben extra tárterület egységára megszorozza. Az extra tárterület ára a részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="next-steps"></a>További lépések

Általános erőforrás-korlátozásaival, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md) és [SQL Database DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md).
