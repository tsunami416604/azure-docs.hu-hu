---
title: "Az Azure Data Factory használja az SQL Data Warehouse szolgáltatással |} Microsoft Docs"
description: "Tippek az Azure Data Factory (ADF) használata az Azure SQL Data Warehouse adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6adfa1264c9d196d6c6e57f1d108710b9ee73265
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Az Azure Data Factory használja az SQL Data Warehouse szolgáltatással
Az Azure Data Factory lehetővé teszi a teljes körűen felügyelt koordinálása az adatok átvitelét és az SQL Data warehouse tárolt eljárások végrehajtása.  Ez lehetővé teszi a könnyebb beállítás és ütemezése összetett bontsa ki az átalakítási és betöltési (ETL) eljárások az SQL Data Warehouse szolgáltatással. Azure Data Factory részletesebb áttekintéséért lásd: a [Azure Data Factory dokumentáció][Azure Data Factory documentation].

## <a name="data-movement"></a>Adatáthelyezés
Az Azure Data Factory lehetővé teszi, hogy a helyszíni adatforrások és a különböző Azure-szolgáltatások közötti adatátvitelt jelölik.  Azure Data Factory átfogó, a jelenlegi integráció az alábbi helyekről érkező vagy oda irányuló adatmozgás támogatja:

* Az Azure blob-tároló
* Azure SQL Database
* A helyszíni SQL Server
* SQL Server IaaS

Információk az adatok beállítása a másolási tevékenység: [másolja az adatokat az Azure Data Factoryvel][Copy data with Azure Data Factory]

## <a name="stored-procedures"></a>Tárolt eljárások
 Megegyező módon használható adatátvitel ütemezése Azure Data Factory használatával is lehet levezényelni a tárolt eljárások végrehajtása.  Így összetettebb kimenetátirányítási hozható létre, és kibővíti az Azure Data Factory képes használni az SQL Data Warehouse számítási teljesítménnyel rendelkezik.

## <a name="next-steps"></a>Következő lépések
Az integráció áttekintéséért lásd: [SQL Data Warehouse integrációjának áttekintése][SQL Data Warehouse integration overview].
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/copy-activity-overview.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/

