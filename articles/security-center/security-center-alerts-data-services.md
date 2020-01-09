---
title: Veszélyforrások észlelése a Azure Security Center adatszolgáltatásaiban | Microsoft Docs
description: Ez a cikk a Azure Security Centerban elérhető adatszolgáltatási riasztásokat ismerteti.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665734"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Veszélyforrások észlelése a Azure Security Center adatszolgáltatásaiban

 Azure Security Center elemzi az adattárolási szolgáltatások naplóit, és riasztásokat küld, amikor fenyegetést észlel az adaterőforrásaihoz. Ez a cikk felsorolja azokat a riasztásokat, amelyeket Security Center a következő szolgáltatásokhoz generál:

* [Azure SQL Database és Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database és SQL Data Warehouse<a name="data-sql"></a>

A Azure SQL Database komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A riasztások akkor jelennek meg, ha gyanús adatbázis-tevékenységek, potenciális sebezhetőségek vagy SQL-injektálási támadások, valamint az adatbázis-hozzáférés és a lekérdezési minták rendellenesek.

A Azure SQL Database és az SQL komplex veszélyforrások elleni védelme az Azure SQL Database-adatbázisokat Azure SQL Database, a felügyelt példányokat, a Azure SQL Data Warehouse-adatbázisokat és az Virtual Machines Azure-beli SQL-kiszolgálókat tartalmazó speciális SQL-alapú biztonsági képességek [(ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) egységes csomag részét képezi.

További információ eléréséhez lásd:

* [A komplex veszélyforrások elleni védelem engedélyezése a Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Az Azure-beli SQL serverek komplex veszélyforrások elleni védelmének engedélyezése Virtual Machines](security-center-iaas-advanced-data.md)
* [A veszélyforrások elleni védelmi riasztások listája SQL Database és SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)

## Azure Storage<a name="azure-storage"></a>

A tárolók komplex veszélyforrások elleni védelme (jelenleg csak a blob Storage esetében érhető el) szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, és segít a biztonsági monitorozási rendszerek kezelésében.

>[!NOTE]
>A komplex veszélyforrások elleni védelem jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

További információ eléréséhez lásd:

* [A komplex veszélyforrások elleni védelem engedélyezése az Azure Storage-ban](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Az Azure Storage veszélyforrások elleni védelmi értesítéseinek listája](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

A Azure Cosmos DB riasztások szokatlan és potenciálisan ártalmas kísérletekkel jönnek létre Azure Cosmos DB fiókok eléréséhez vagy kiaknázásához.

További információ eléréséhez lásd:

* [A Azure Cosmos DB komplex veszélyforrások elleni védelme (előzetes verzió)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A veszélyforrások elleni védelmi riasztások listája Azure Cosmos DB (előzetes verzió)](alerts-reference.md#alerts-azurecosmos)
