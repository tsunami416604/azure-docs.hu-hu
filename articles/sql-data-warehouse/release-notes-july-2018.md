---
title: Az Azure SQL Data Warehouse kibocsátási megjegyzések a 2018 június |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216502"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Mi az új Azure SQL Data warehouse? 2018. július
Az Azure SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és a július 2018-ban bevezetett módosításokat.


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Kifinomultabb részletességgel több régióban és a kiszolgáló visszaállítása
Most már visszaállíthatja régiók és földrajzi redundáns biztonsági másolatokat, amelyeket a rendszer 24 óránként kiválasztása helyett bármely visszaállítási pont használó kiszolgálók között. Több régióban és a kiszolgáló visszaállítása mindkét felhasználó által vagy automatikus visszaállítási pontok további adatvédelemre kifinomultabb részletességgel engedélyezése esetén támogatottak. Az elérhető a további helyreállítási pontok biztos lehet abban is, hogy az adattárház logikailag konzisztens lesz régióban visszaállításakor.

![A restore parancs – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![visszaállítási lehetőségek – Azure SQL Data warehouse-bA](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

További információkért lásd: a [gyorsított és rugalmas visszaállítási pontok](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogbejegyzést.

## <a name="20-minute-restorations"></a>20 perces visszaállítását
Most kínál az SQL Data Warehouse, visszaállítás bármely adatraktár **kevesebb mint 20 perc alatt** ugyanabban a régióban, az adatbázis méretétől függetlenül. Rövidebb idő alatt vonatkozik-e a visszaállítást, de akár egy másik logikai kiszolgáló ugyanazon a régión belül. Emellett a pillanatkép-folyamat lett továbbfejlesztve, hogy kevesebb időt vesz igénybe egy visszaállítási pont létrehozása. Az alsó teljesítményszintek (alsó DWU-beállítást), a javítására van egy *2 x csökkentési* időpontot a pillanatkép létrehozása.

További információkért lásd: a [gyorsított és rugalmas visszaállítási pontok](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogbejegyzést.

## <a name="custom-restoration-configurations"></a>Egyéni helyreállítás konfigurációk
Most módosíthatja a teljesítményszint (dwu Használatát), az Azure Portalon visszaállításakor. Egy frissített Gen2 data warehouse-ba is helyreállíthatja. Általános 2 példányra visszaállítással most kiértékelheti előtt Gen2 hatásának [az Gen1 data warehouse frissítése](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Egyéni helyreállítás konfigurálása – az Azure SQL Data warehouse-bA](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>AZ SP_DESCRIBE_UNDECLARED_PARAMETERS
A [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) tárolt eljárás gyakran eszközök beszerzésére használatos Transact-SQL kötegben szereplő paraméterek metaadatait. Az eljárást minden paraméter egy sort a batch-a ítélte típussal kapcsolatos információk arra a paraméterre vonatkozóan adja vissza. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Az eredményhalmaz kapcsolatos metaadatokat tartalmaz a `@id` paraméter (részleges eredményeket látható)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```