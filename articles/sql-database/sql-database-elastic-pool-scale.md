---
title: Méretezhető rugalmas készlet erőforrások – az Azure SQL Database |} Microsoft Docs
description: Ez a lap ismerteti az Azure SQL Database rugalmas készletek méretezési erőforrásait.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 0d15382f413485ccc287bac945b3c88eb748a9f6
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313317"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Az Azure SQL Database rugalmas készlet erőforrások méretezése

Ez a cikk ismerteti az Azure SQL Database rugalmas készletek és a készletezett adatbázisok rendelkezésre álló számítási és tárolási erőforrások méretezése. 

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>alapjául szolgáló vásárlási modell vCore-alapú: a rugalmas készlet tárolási méretének módosítása

- A maximális méretkorlát legfeljebb tárolók kiépítésével: 
 - Standard szintű tárolást, az növeli vagy 10 GB-os lépésekben méretének csökkentése
 - Prémium szintű storage, az növeli vagy 250 GB-os lépésekben méretének csökkentése
- Egy rugalmas készlet tárolási létesíthetők növelésével vagy csökkentésével a maximális méretét.
- Rugalmas készletek tároló árának érték a tároló és a szolgáltatási rétegben tárolási egységárát. Extra tároló árának részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>alapjául szolgáló vásárlási modell vCore-alapú: módosítsa a rugalmas készlet számítási erőforrások (vCores)

Növelheti vagy csökkentheti a teljesítményszintet igények erőforrást alapuló rugalmas készletek a [Azure-portálon](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), vagy a [REST API-t](/rest/api/sql/elasticpools/update).

- Amikor készletbe vCores rescaling, adatbázis-kapcsolatok röviden megszakadnak. Ez az a kívánt viselkedést eredményező beállítást, akkor fordul elő, amikor rescaling dtu-k (nem a készlet) egyetlen adatbázis. A telepítések időtartamát és kapcsolatok megszakadnak adatbázis rescaling műveletek során hatását a részletekért lásd: [Rescaling dtu-i egy önálló adatbázis](#single-database-change-storage-size). 
- Készlet vCores átméretezése idejét a készletben lévő összes adatbázisok által felhasznált tárterület teljes mennyisége függ. Általában a rescaling várakozási átlagolja 90 percig vagy / 100 GB-nál kisebb. Például ha használja a teljes lemezterület a készletben található összes adatbázis 200 GB-os, akkor a várt késését a készlet rescaling 3 óra vagy annál kisebb. Bizonyos esetekben a szabványos vagy Basic szint belül a rescaling várakozási függetlenül a használt terület mennyisége a öt perc lehet.
- Módosítsa a / adatbázis vagy a maximális vCores adatbázisonkénti minimális vCores időtartamot általában öt perc vagy kisebb.
- Amikor készletbe vCores downsizing, használt készlet mérete kisebb, mint a megengedett maximális méretét a célként megadott szolgáltatás réteg és a készlet vCores kell lennie.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>DTU-alapú alapjául szolgáló vásárlási modell: a rugalmas készlet tárolási méretének módosítása

- A rugalmas készlethez tartozó eDTU ár további költségek nélkül tárolási bizonyos mennyiségű tartalmazza. Megnövelt tárhely meghaladó szerepel az a maximális méretkorlát fel és 1 TB 250 GB-os lépésekben, majd a 256 GB felett 1 TB-os lépésekben akár további költségek telepíthető. Belefoglalt tárolási összegeket és a maximális méretkorlát [rugalmas készlet: tárterületet és teljesítményszintek](#elastic-pool-storage-sizes-and-performance-levels).
- Rugalmas készletek – megnövelt tárhely növeli a maximális méret használatával helyezhetők a [Azure-portálon](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), vagy a [REST API-n ](/rest/api/sql/elasticpools/update).
- Extra storage rugalmas készletek ára érték a megnövelt tárhely megszorozza a szolgáltatási rétegben – megnövelt tárhely egységárát. Extra tároló árának részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>DTU-alapú alapjául szolgáló vásárlási modell: módosítsa a rugalmas készlet számítási erőforrások (edtu-k)

Akkor növelheti vagy csökkentheti a számára egy rugalmas készlet igények erőforrást alapján elérhető erőforrások a [Azure-portálon](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), vagy a [ REST API-t](/rest/api/sql/elasticpools/update).

- Ha Edtu rescaling, adatbázis-kapcsolatok röviden eldobott. Ez az a kívánt viselkedést eredményező beállítást, akkor fordul elő, amikor rescaling dtu-k (nem a készlet) egyetlen adatbázis. A telepítések időtartamát és kapcsolatok megszakadnak adatbázis rescaling műveletek során hatását a részletekért lásd: [Rescaling dtu-i egy önálló adatbázis](#single-database-change-storage-size). 
- Edtu átméretezése idejét a készletben lévő összes adatbázisok által felhasznált tárterület teljes mennyisége függ. Általában a rescaling várakozási átlagolja 90 percig vagy / 100 GB-nál kisebb. Például ha használja a teljes lemezterület a készletben található összes adatbázis 200 GB-os, akkor a várt késését a készlet rescaling 3 óra vagy annál kisebb. Bizonyos esetekben a szabványos vagy Basic szint belül a rescaling várakozási függetlenül a használt terület mennyisége a öt perc lehet.
- Módosítsa a minimális Edtu / adatbázis vagy a maximális edtu-k adatbázisonkénti időtartamot általában öt perc vagy kisebb.
- Ha Edtu downsizing, használt készlet mérete kisebb, mint a megengedett maximális méret a célként megadott szolgáltatás réteg és a készlet edtu / kell lennie.
- Edtu rescaling, amikor egy extra tárolási költségű érvényes, ha a készlet (1) a tároló maximális mérete támogatja-e a célként megadott készlethez, és (2) a tároló maximális mérete meghaladja a célkészlettel a belefoglalt tárolási. Például ha egy 100 100 GB maximális mérettel szabványos készlet edtu-ra van downsized egy 50 szabványos készlet edtu-k számára, majd egy – megnövelt tárhely költség érvényes mert célkészlettel támogatja a maximális mérete 100 GB-nál, és a belefoglalt mennyisége csak 50 GB. Igen a megnövelt tárhely mérete 100 GB – 50 GB = 50 GB. Az árképzés további tárhelyet, lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). Ha a tényleges használt lemezterület mérete kisebb, mint a belefoglalt mennyisége, majd ez kapcsolódik további költség elkerülhető az adatbázis maximális méretét csökkentve a belefoglalt legkevesebb. 

## <a name="next-steps"></a>További lépések

Általános erőforrás-korlátok, lásd: [SQL-adatbázis vCore-alapú erőforrás korlátok - rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md) és [SQL Database DTU-alapú erőforrás korlátok - rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md).
