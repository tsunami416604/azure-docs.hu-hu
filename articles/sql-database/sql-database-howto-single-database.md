---
title: Azure SQL Database konfigurálása – egyetlen
description: Megtudhatja, hogyan konfigurálhat és kezelhet Azure SQL Database-önálló adatbázist
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 02/08/2019
ms.openlocfilehash: 45e25641092fae36cd58a59400cea025f74cbc1f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689904"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Egyetlen adatbázis használata Azure SQL Database

Ebben a szakaszban különféle útmutatókat, parancsfájlokat és magyarázatokat talál, amelyek segítségével kezelheti és konfigurálhatja az önálló adatbázist Azure SQL Database

## <a name="migrate"></a>Migrate (Áttelepítés)

- [Migrálás SQL Databasere](sql-database-single-database-migrate.md) – az ajánlott áttelepítési folyamat és a felügyelt példányra való áttelepítéshez szükséges eszközök ismertetése.
- Ismerje meg, hogyan [kezelheti az SQL Database](sql-database-manage-after-migration.md)-t az áttelepítés után.

## <a name="configure-features"></a>Funkciók konfigurálása

- [Konfigurálja a tranzakciós replikálást](replication-to-sql-database.md) az adatbázisok közötti dátum replikálásához.
- A [veszélyforrások észlelésének konfigurálásával](sql-database-threat-detection.md) Azure SQL Database azonosíthatók a gyanús tevékenységek, például az SQL-injektálás vagy a gyanús helyekről való hozzáférés.
- A bizalmas adatok védelme érdekében [konfigurálja a dinamikus adatmaszkolást](sql-database-dynamic-data-masking-get-started-portal.md) .
- A [biztonsági másolatok megőrzésének konfigurálása](sql-database-long-term-backup-retention-configure.md) egy adatbázis számára, hogy a biztonsági mentések megmaradjanak az Azure Blob Storage-on. Alternatív megoldásként [konfigurálhatja a biztonsági másolatok megőrzését az Azure-tároló (elavult) használatával](sql-database-long-term-backup-retention-configure-vault.md) .
- [Konfigurálja a Geo-replikálást](sql-database-geo-replication-portal.md) , hogy az adatbázis egy másik régióban maradjon.
- [Konfigurálja a Geo-replikák biztonságát](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Az adatbázis monitorozása és finomhangolása

- [Engedélyezze az automatikus hangolást](sql-database-automatic-tuning-enable.md) , hogy Azure SQL Database optimalizálja a számítási feladatok teljesítményét.
- Az [automatikus hangolással kapcsolatos e-mail-értesítések engedélyezése](sql-database-automatic-tuning-email-notifications.md) a hangolási javaslatokkal kapcsolatos információk beszerzéséhez.
- [Teljesítménnyel kapcsolatos javaslatok alkalmazása](sql-database-advisor-portal.md) és az adatbázis optimalizálása.
- [Riasztásokat hozhat létre](sql-database-insights-alerts-portal.md) Azure SQL Databaseről érkező értesítések beszerzéséhez.
- [Kapcsolódási hibák elhárítása](sql-database-troubleshoot-common-connection-issues.md) , ha az alkalmazások és az adatbázis közötti kapcsolódási problémákat tapasztal. [A kapcsolódási problémákhoz Resource Health](sql-database-resource-health.md)is használhatja.
- [Kezelheti](sql-database-file-space-management.md) a tárhelyet a tárterület-használat figyeléséhez az adatbázisban.

## <a name="query-distributed-data"></a>Lekérdezések elosztott adatai

- A [vertikálisan particionált adatforrásokat](sql-database-elastic-query-getting-started-vertical.md) több adatbázis között kérdezheti le.
- A kibővített [adatrétegek közötti jelentés](sql-database-elastic-query-horizontal-partitioning.md).
- [Lekérdezés különböző sémákkal rendelkező táblák között](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Rugalmas adatbázis-feladatok

- [Létrehozás és kezelés](elastic-jobs-powershell.md) Elastic Database feladatok a PowerShell használatával.
- [Létrehozás és kezelés](elastic-jobs-tsql.md) Feladatok Elastic Database a Transact-SQL használatával.
- [Migrálás a régi rugalmas feladatokból](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Adatbázis horizontális particionálása

- [Rugalmas adatbázis-ügyfél függvénytárának frissítése](sql-database-elastic-scale-upgrade-client-library.md).
- [Szilánkokra osztott alkalmazás létrehozása](sql-database-elastic-scale-get-started.md).
- [Horizontálisan szilánkokra bontott adathalmazok lekérdezése](sql-database-elastic-query-getting-started.md)
- [Több szegmensből álló lekérdezések](sql-database-elastic-scale-multishard-querying.md)futtatása.
- [Helyezze át a szilánkokra osztott adatátvitelt](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurálja a biztonságot](sql-database-elastic-scale-split-merge-security-configuration.md) az adatbázis-szegmensekben.
- [Adjon hozzá egy](sql-database-elastic-scale-add-a-shard.md) szegmenst az aktuális adatbázis-szegmensek jelenlegi készletéhez.
- [Kijavítottuk a szilánkok térképének problémáit](sql-database-elastic-database-recovery-manager.md).
- A [szilánkokra osztott adatbázis migrálása](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Számlálók létrehozása](sql-database-elastic-database-perf-counters.md)
- Az Entity Framework használatával lekérdezheti a szilánkokra osztott [adatkereteket](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) .
- A [felhasználható keretrendszer használatával](sql-database-elastic-scale-working-with-dapper.md) lekérdezheti a szilánkokra bontott adatkereteket.

## <a name="next-steps"></a>További lépések
- További információ a [felügyelt példányokkal kapcsolatos útmutatókról](sql-database-howto-managed-instance.md)
