---
title: 'Ismert problémák: áttelepítés Oracle-ből Azure Database for PostgreSQLba'
titleSuffix: Azure Database Migration Service
description: Ismerkedjen meg az ismert problémákkal és az áttelepítési korlátozásokkal az Oracle-ből az Azure PostgreSQL-Single Database-be való online áttelepítéssel az Azure Database Migration Service használatával.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 2316e0314c9d8f36aee123d4a516f382f58e83e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291656"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Ismert problémák/migrációs korlátozások az Oracle-ből az Azure-ba való online áttelepítéssel PostgreSQL-Single Serverre

Az Oracle-ből az Azure Database-be PostgreSQL-Single-kiszolgálóra való online áttelepítéssel kapcsolatos ismert problémák és korlátozások a következő szakaszokban olvashatók.

## <a name="oracle-versions-supported-as-a-source-database"></a>A forrás-adatbázisként támogatott Oracle-verziók

A Azure Database Migration Service a következőhöz való csatlakozást támogatja:

- Az Oracle 10g-, 11g-és 12c-verziója.
- Oracle Enterprise, standard, Express és Personal kiadás.

Azure Database Migration Service nem támogatja a több-bérlős tároló adatbázisaihoz (CDBs) való csatlakozást.

## <a name="postgresql-versions-supported-as-a-target-database"></a>A PostgreSQL-verziók a célként megadott adatbázisként támogatottak

Azure Database Migration Service támogatja az Azure Database-be való áttelepítést PostgreSQL-Single Server 9,5, 9,6, 10 és 11 verzióra. Tekintse meg a [PostgreSQL-adatbázis támogatott](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) verzióit az Azure database for PostgreSQL-Single Server verziójának támogatásáról szóló aktuális információkért.

## <a name="datatype-limitations"></a>Adattípus-korlátozások

A következő adattípusok **nem** lesznek áttelepítve:

- BFÁJL
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Beágyazott táblák
- Felhasználó által definiált adattípusok
- Jegyzetek
- Virtuális oszlopok
- ROWID oszlopon alapuló, lényeges nézetek

Továbbá üres BLOB-/CLOB-oszlopok vannak leképezve NULL értékre a célhelyen.

## <a name="lob-limitations"></a>LOB-korlátozások

- Ha a korlátozott méretű LOB mód engedélyezve van, az Oracle-forrás üres LOBs NULL értékként lesznek replikálva.
- A hosszú objektumok neve (30 bájtnál több) nem támogatott.
- A hosszú és hosszú nyers oszlopban lévő adatértékek nem haladhatják meg a 64 KB-ot. A 64 KB-nál nagyobb adatértékek csonkítva lesznek.
- Csak Oracle 12 esetén a LOB-oszlopok módosításai nem támogatottak (migrálva).
- A XMLTYPE és a LOB-oszlopok frissítései nem támogatottak (migrálva).

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

- A felhasználónak DBA jogosultsággal kell rendelkeznie az Oracle-kiszolgálón.
- A partíció/alpartíciós műveletek (Hozzáadás, eldobás, EXCHANGE és CSONKÍTás) miatti adatváltozások nem lesznek áttelepítve, és a következő hibákhoz vezethetnek:
  - A HOZZÁADÁSi műveletekhez a hozzáadott adatokhoz tartozó frissítések és törlések "0 sor érintett" figyelmeztetést adhatnak vissza.
  - A DROP és a CSONKÍTás műveletekhez az új beszúrások "duplikált" hibákat okozhatnak.
  - Az EXCHANGE-műveletek esetében a "0 sor érintett" figyelmeztetés és az "ismétlődések" hibaüzenet jelenhet meg.
- Azok a táblák, amelyek neve aposztrófokat tartalmaz, nem replikálhatók.
