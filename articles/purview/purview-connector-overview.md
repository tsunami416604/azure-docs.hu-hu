---
title: A hatáskörébe-összekötő áttekintése
description: Ez a cikk a hatáskörébe tartozó különböző adattárakat és funkciókat ismerteti
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 26efa840bacd3ee542816cf861cf2ef586b0582f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780212"
---
# <a name="supported-data-stores"></a>Támogatott adattárak

A hatáskörébe a következő adattárakat támogatja. Az egyes adattárokra kattintva megtudhatja a támogatott funkciókat és a megfelelő konfigurációkat a részletek között.

## <a name="purview-data-sources"></a>A hatáskörébe tartozó adatforrások

|**Kategória**|  **Adattár**  |**Metaadatok kinyerése**|**Teljes vizsgálat**|**Növekményes vizsgálat**|**Hatókörön belüli vizsgálat**|**Osztályozás**|**Lineage**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Igen| Igen| Igen| Igen| Igen| Igen|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Igen| Igen| Igen| Igen| Igen| Igen|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Igen| Igen| Igen| Igen| Igen| Igen|
||[1. generációs Azure Data Lake Storage](register-scan-adls-gen1.md)|Igen| Igen| Igen| Igen| Igen| Igen|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Igen| Igen| Igen| Igen| Igen| Igen|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Igen| Igen| Nem| Igen| Igen| Igen|
||[Felügyelt Azure SQL Database-példány](register-scan-azure-sql-database-managed-instance.md)|Igen| Igen| Nem| Igen| Igen| Igen|
||[Azure Synapse Analytics (korábban SQL DW)](register-scan-azure-synapse-analytics.md)|Igen| Igen| Nem| Igen| Igen| Igen|
|Adatbázis|[SQL Server](register-scan-on-premises-sql-server.md)|Igen| Igen| Nem| Igen| Igen| Igen|
||[Teradata (előzetes verzió)](register-scan-teradata-source.md)|Igen| Igen| Nem| Nem| Nem| Igen|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Igen| Igen| Nem| Nem| Nem| Igen|

## <a name="next-steps"></a>Következő lépések

- [Azure Blob Storage-forrás regisztrálása és vizsgálata](register-scan-azure-blob-storage-source.md)