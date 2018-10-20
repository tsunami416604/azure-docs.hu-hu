---
title: A méretezési csoport rugalmas készlet erőforrás - Azure SQL Database |} A Microsoft Docs
description: Ez az oldal méretezési erőforrásokat az Azure SQL Database rugalmas készletek ismerteti.
services: sql-database
ms.service: sql-database
subservice: elastic-pool
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 707304e6901002a58fe24a4b3a88be3f82ad320e
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469248"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Az Azure SQL Database rugalmas készlet erőforrások skálázása

Ez a cikk ismerteti az Azure SQL Database rugalmas készletek és a készletezett adatbázisok rendelkezésre álló számítási és tárolási erőforrások méretezése.

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>Virtuálismag-alapú vásárlási modell: a rugalmas készlet tárolási méretének módosítása

- A maximális méretkorlát legfeljebb tárolók kiépítésével:

  - Standard szintű tárolóra vonatkozó növelése, vagy 10 GB-os lépésekben méretének csökkentése
  - A Premium storage növelheti, vagy 250 GB-os lépésekben méretének csökkentése
- Rugalmas készletek tárolók kiépítésével növelésével vagy csökkentésével a maximális méretét.
- Tároló rugalmas készletek ára a tárolókapacitás, szorozva a szolgáltatási rétegben tárolási egységárát. Az extra tárterület ára a részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>Virtuálismag-alapú vásárlási modell: módosítsa a rugalmas készlet számítási erőforrásokat (virtuális mag)

Növelheti vagy csökkentheti a számítási méret egy rugalmas készlet igények kielégítéséhez használt erőforrás alapján a [az Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI-vel](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), vagy a [ REST API-val](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

- Amikor átméretezésekor rugalmas készletben található virtuális maggal, adatbázis-kapcsolatok röviden eldobásakor. Ez a viselkedés, amely akkor fordul elő, amikor átméretezésekor dtu-k egy önálló adatbázis viselkedést. Az időtartam és a megszakított kapcsolatokat adatbázis átméretezésekor műveletek során hatását a részletekért lásd: [módosítása számítási erőforrások (dtu-k)](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus).
- A virtuális mag a készlet átméretezése időtartamot a készletben található összes adatbázis által használt tárterület teljes mennyisége is függenek. Általánosságban véve a átméretezésekor késés átlagának kiszámítása 90 perc vagy annál kisebb 100 GB-onként. Például ha által használt a teljes terület a készletben található összes adatbázis, 200 GB-os, akkor az a készlet rescaling a várható késés 3 óra vagy kevesebb. Bizonyos esetekben belül a Standard vagy alapszintű csomagra a átméretezésekor késés lehet függetlenül a használt terület mennyiségét öt perc alatt.
- Módosíthatja a min virtuális magok adatbázisonkénti vagy maximális virtuális magok száma az időtartam általában öt perc vagy annál kisebb.
- Amikor downsizing készlet virtuális maggal, használt készlet mérete kisebb, mint a megengedett maximális mérete a cél szolgáltatási szint és a készlet virtuális magok kell lennie.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>DTU-alapú vásárlási modell: a rugalmas készlet tárolási méretének módosítása

- Rugalmas készlet edtu-k díjszabása tartalmaz egy bizonyos mennyiségű tárolási további költségek nélkül. A csomagban foglalt adatmennyiségen felüli extra tárterület legfeljebb 250 GB-os fel 1 TB-os egységekben, majd, 256 GB 1 TB-os léptékben maximális méretkorlátot díjfizetés mellett bővítheti. Belefoglalt tárterület összegek és a maximális méret korlátok [rugalmas készlet: tárterületet és számítási méretek](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Extra tárterület rugalmas készletek bővítheti a maximális méret használatával növelje a [az Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI-vel](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), vagy a [REST API-val ](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Az extra tárterület rugalmas készletek ára az extra tárterület keletkezett a szolgáltatási rétegben extra tárterület egységára megszorozza. Az extra tárterület ára a részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>DTU-alapú vásárlási modell: módosítsa a rugalmas készlet számítási erőforrásokat (edtu-k)

Növelheti vagy csökkentheti a igények kielégítéséhez használt erőforrás alapján rugalmas készletek számára elérhető erőforrások a [az Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI-vel](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), vagy a [ REST API-val](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

- Amikor átméretezésekor készlet edtu-k, adatbázis-kapcsolatok röviden eldobásakor. Ez a viselkedés, amely akkor fordul elő, amikor átméretezésekor dtu-k egy önálló adatbázis viselkedést. Az időtartam és a megszakított kapcsolatokat adatbázis átméretezésekor műveletek során hatását a részletekért lásd: [módosítása számítási erőforrások (dtu-k)](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus).
- A készlet edtu-k átméretezése időtartamot a készletben található összes adatbázis által használt tárterület teljes mennyisége is függenek. Általánosságban véve a átméretezésekor késés átlagának kiszámítása 90 perc vagy annál kisebb 100 GB-onként. Például ha által használt a teljes terület a készletben található összes adatbázis, 200 GB-os, akkor az a készlet rescaling a várható késés 3 óra vagy kevesebb. Bizonyos esetekben belül a Standard vagy alapszintű csomagra a átméretezésekor késés lehet függetlenül a használt terület mennyiségét öt perc alatt.
- Az időtartam módosítása az adatbázis vagy a maximális edtu-k adatbázisonkénti minimális edtu-k általában öt perc vagy annál kisebb.
- Rugalmas készletek edtu-k downsizing, ha a használt készlet mérete kisebb, mint a megengedett maximális mérete a cél szolgáltatási szint és a készlet edtu-k kell lennie.
- Átméretezésekor rugalmas készletek edtu-k, amikor egy extra tárterület költséget akkor érvényes, ha (1) a tároló a készlet maximális mérete a célként megadott készlet által támogatott, és (2) a tároló maximális mérete meghaladja a célkészletet belefoglalt tárterület mennyiségét. Például ha egy 100 edtu-s Standard készlet 100 GB-os maximális mérettel van egy 50 edtu méretű Standard készletben való downsized, majd egy extra tárterület költség érvényes mert célkészlet támogatja a 100 GB-os maximális méretét, és a belefoglalt tárterület-mennyiség csak 50 GB-ot. Tehát az extra tárterület keletkezett a 100 GB – 50 GB = 50 GB. Extra tárterület díjszabással kapcsolatos információk: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). Ha a tényleges felhasznált lemezterület mérete kisebb, mint a csomagban foglalt tárhely, majd ezzel többletköltség elkerülhető az adatbázis maximális méretének csökkentésével a csomagban foglalt adatmennyiségen.

## <a name="next-steps"></a>További lépések

Általános erőforrás-korlátozásaival, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md) és [SQL Database DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md).
