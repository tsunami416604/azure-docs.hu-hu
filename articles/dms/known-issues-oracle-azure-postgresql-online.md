---
title: 'Ismert problémák: Áttelepítés az Oracle-ről a PostgreSQL Azure-adatbázisába'
titleSuffix: Azure Database Migration Service
description: Ismerje meg az Oracle-ből az Azure Database for PostgreSQL-Single kiszolgálóra az Azure Database-t az Azure Database Migration Service használatával történő online áttelepítésekkel kapcsolatos ismert problémákat és áttelepítési korlátozásokat.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235239"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Ismert problémák/áttelepítési korlátozások az Oracle-ről az Azure DB-re a PostgreSQL-Single kiszolgálóra való online áttelepítéssel kapcsolatban

Az Oracle-ről az Azure Database for PostgreSQL-Single kiszolgálóra történő online áttelepítéssel kapcsolatos ismert problémákat és korlátozásokat a következő szakaszok ismertetik.

## <a name="oracle-versions-supported-as-a-source-database"></a>Forrásadatbázisként támogatott Oracle-verziók

Az Azure Database Migration Service támogatja a csatlakozást:

- Oracle verzió 10g, 11g, és 12c.
- Oracle Enterprise, Standard, Express és Personal Edition.

Az Azure Database Migration Service nem támogatja a több-bérlős tárolóadatbázisokhoz (CDB)-hez való csatlakozást.

## <a name="postgresql-versions-supported-as-a-target-database"></a>A postgreSQL verziók céladatbázisként támogatottak

Az Azure Database Migration Service támogatja az Azure Database for PostgreSQL-Single server 9.5, 9.6, 10 és 11 verziójú Azure Database szolgáltatásba való áttelepítést. Tekintse meg a [cikk támogatott PostgreSQL adatbázis-verziók](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) aktuális információkat verziótámogatás az Azure Database for PostgreSQL-Single server.

## <a name="datatype-limitations"></a>Adattípus-korlátozások

A következő adattípusok **nem** lesznek áttelepítve:

- BFILE
- SORAZONOSÍTÓ
- Ref
- UROWID
- ANYDATA (ANYDATA)
- SDO_GEOMETRY
- Beágyazott táblázatok
- Felhasználó által definiált adattípusok
- Megjegyzések
- Virtuális oszlopok
- Materializált nézetek a ROWID oszlop alapján

Emellett az üres BLOB/CLOB oszlopok null értékre vannak leképezve a célon.

## <a name="lob-limitations"></a>Lob-korlátozások

- Ha a korlátozott méretű LOB mód engedélyezve van, az Oracle forrás üres LOB-jai NULL értékként lesznek replikálva.
- A hosszú objektumnevek (30 bájtnál több) nem támogatottak.
- A LONG és A LONG RAW oszlopban lévő adatok nem haladhatják meg a 64k-et. A 64k-en túli adatok csonkulnak.
- Csak az Oracle 12-ben a LOB-oszlopok módosításai nem támogatottak (áttelepítésre kerülnek).
- Az XMLTYPE és a LOB-oszlopok updates nem támogatott (áttelepítés).

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

- Az ügyfeleknek a SYSDBA segítségével kell csatlakozniuk az Oracle-hez.
- A partíciós/alpartíció-műveletekből (ADD, DROP, EXCHANGE és TRUNCATE) eredő adatváltozások nem kerülnek áttelepítésre, és a következő hibákat okozhatják:
  - Add műveletek esetén a hozzáadott adatok frissítései és törlései "0 érintett sor" figyelmeztetést adhatnak vissza.
  - Drop és TRUNCATE műveletek esetén az új beszúrások "ismétlődések" hibákat okozhatnak.
  - Exchange-műveletek esetén "0 érintett sor" figyelmeztetés és "ismétlődések" hibák fordulhatnak elő.
- Azok a táblák, amelyek neve aposztrófot tartalmaz, nem replikálhatók.
