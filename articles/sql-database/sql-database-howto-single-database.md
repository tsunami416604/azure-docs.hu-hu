---
title: Egyetlen adatbázis konfigurálása
description: Az Azure SQL Database konfigurálása és kezelése – egyetlen adatbázis
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 4283e1a2c92b7fe738fb57a8103cea1deb0015a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76027714"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Egyetlen adatbázis használata az Azure SQL Database-ben

Ebben a szakaszban különböző útmutatókat, parancsfájlokat és magyarázatokat talál, amelyek segíthetnek az egyetlen adatbázis kezelésében és konfigurálásában az Azure SQL Database-ben

## <a name="migrate"></a>Migrate (Áttelepítés)

- [Áttelepítés sql-adatbázis –](sql-database-single-database-migrate.md) Ismerje meg az ajánlott áttelepítési folyamat és eszközök áttelepítés egy felügyelt példány.
- További információ az [SQL-adatbázis áttelepítés utáni kezeléséről.](sql-database-manage-after-migration.md)

## <a name="configure-features"></a>Funkciók konfigurálása

- [A tranzakciós replikáció konfigurálása](replication-to-sql-database.md) az adatbázisok közötti dátum replikálásához.
- [Állítsa be a fenyegetésészlelést,](sql-database-threat-detection.md) hogy az Azure SQL Database azonosíthassa a gyanús tevékenységeket, például az SQL Injectiont vagy a gyanús helyekről való hozzáférést.
- [A dinamikus adatmaszkolás konfigurálása](sql-database-dynamic-data-masking-get-started-portal.md) a bizalmas adatok védelme érdekében.
- Állítsa be a biztonsági mentés megőrzése egy adatbázis tartjuk a biztonsági mentéseket az Azure Blob [Storage.Configure backup adatmegőrzés](sql-database-long-term-backup-retention-configure.md) for a database to keep your backups on Azure Blob Storage. Alternatív megoldásként van [konfigurálása biztonsági mentés megőrzése az Azure Vault (elavult)](sql-database-long-term-backup-retention-configure-vault.md) megközelítés használatával.
- Állítsa be a [georeplikációt](sql-database-geo-replication-portal.md) úgy, hogy az adatbázis replikáját egy másik régióban tartsa.
- [A georeplikák biztonságának konfigurálása](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Az adatbázis figyelése és finomhangolása

- [Engedélyezze az automatikus hangolást,](sql-database-automatic-tuning-enable.md) hogy az Azure SQL Database optimalizálhassa a számítási feladatok teljesítményét.
- [Engedélyezze az automatikus hangoláshoz szükséges e-mail értesítéseket,](sql-database-automatic-tuning-email-notifications.md) hogy információt kapjon a finomhangolási javaslatokról.
- [Teljesítményre vonatkozó javaslatok alkalmazása](sql-database-advisor-portal.md) és az adatbázis optimalizálása.
- [Hozzon létre riasztásokat](sql-database-insights-alerts-portal.md) az Azure SQL Database értesítéseinek lehívásához.
- [A kapcsolódás immár a kapcsolódási problémák elhárítása,](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md) ha az alkalmazások és az adatbázis között kapcsolódási problémákat észlel. Az Erőforrás állapota is használható [kapcsolódási problémákesetén.](sql-database-resource-health.md)
- [A fájlterület kezelése](sql-database-file-space-management.md) az adatbázis tárhelyhasználatának figyeléséhez.

## <a name="query-distributed-data"></a>Lekérdezések elosztott adatai

- [Vertikálisan particionált adatok lekérdezése](sql-database-elastic-query-getting-started-vertical.md) több adatbázisban.
- [Jelentés a kibővített adatrétegen](sql-database-elastic-query-horizontal-partitioning.md)keresztül.
- [Lekérdezés különböző sémákkal rendelkező táblák között.](sql-database-elastic-query-vertical-partitioning.md)

## <a name="elastic-database-jobs"></a>Rugalmas adatbázis-feladatok

- [Létrehozás és kezelés](elastic-jobs-powershell.md) Rugalmas adatbázis-feladatok a PowerShell használatával.
- [Létrehozás és kezelés](elastic-jobs-tsql.md) Rugalmas adatbázis-feladatok transact-SQL használatával.
- [Migráljon a régi rugalmas feladatból.](elastic-jobs-migrate.md)

## <a name="database-sharding"></a>Adatbázis horizontális skálázása

- [Rugalmas adatbázis-ügyféltár frissítése](sql-database-elastic-scale-upgrade-client-library.md).
- [Szilánkos alkalmazás létrehozása.](sql-database-elastic-scale-get-started.md)
- [Horizontálisan szilánkos adatok lekérdezése](sql-database-elastic-query-getting-started.md).
- [Futtassa a többshard lekérdezéseket.](sql-database-elastic-scale-multishard-querying.md)
- [Szilánkos adatok áthelyezése](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurálja](sql-database-elastic-scale-split-merge-security-configuration.md) az adatbázis-szegmensek biztonságát.
- [Shard hozzáadása](sql-database-elastic-scale-add-a-shard.md) az adatbázis-szegmensek aktuális készletéhez.
- [A shard térképekkel kapcsolatos problémák megoldása](sql-database-elastic-database-recovery-manager.md).
- [A szilánkos adatbázis áttelepítése](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Számlálók létrehozása](sql-database-elastic-database-perf-counters.md).
- [Entitáskeretrendszer használatával](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) lekérdezheti a szilánkos adatokat.
- [A Dapper-keretrendszer használatával](sql-database-elastic-scale-working-with-dapper.md) lekérdezheti a szilánkos adatokat.

## <a name="next-steps"></a>További lépések
- További információ a [felügyelt példány útmutatóiról](sql-database-howto-managed-instance.md)
