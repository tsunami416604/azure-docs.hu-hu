---
title: Az Azure SQL Database – önálló konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az Azure SQL Database – önálló adatbázis
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: bf126caa378eafe569b83f211d9d2bbfd37e18f9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477794"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Önálló adatbázis használata Azure SQL Database-ben

Ebben a szakaszban található különböző útmutatók, parancsfájlok és leírásokkal, amelyek segítségével kezelheti és konfigurálhatja az önálló adatbázis az Azure SQL Database-ben

## <a name="migrate"></a>Migrate (Áttelepítés)

- [Az SQL Database áttelepítése](sql-database-cloud-migrate.md) – további információ a javasolt áttelepítési folyamat és az eszközök a migrálás felügyelt példányra.
- Ismerje meg, hogyan [SQL database kezelése a migrálás után](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Funkciók konfigurálása

- [Tranzakciós replikáció konfigurálása](replication-to-sql-database.md) replikálni a dátum adatbázisok között.
- [Fenyegetésészlelés konfigurálása](sql-database-threat-detection.md) ahhoz, hogy az Azure SQL Database azonosíthatja a gyanús tevékenységeket, például SQL-injektálás vagy gyanús helyekről való hozzáférés.
- [Dinamikus adatmaszkolás konfigurálása](sql-database-dynamic-data-masking-get-started-portal.md) a bizalmas adatok védelme érdekében.
- [A biztonsági másolatok konfigurálása](sql-database-long-term-backup-retention-configure.md) , hogy a biztonsági másolatokat az Azure Blob Storage-adatbázishoz. Nincs alternatív [használata az Azure vault (elavult) a biztonsági másolatok konfigurálása](sql-database-long-term-backup-retention-configure-vault.md) megközelítést.
- [Georeplikáció konfigurálása](sql-database-geo-replication-portal.md) , hogy az adatbázis replikája egy másik régióban.
- [Biztonság geo-replikák konfigurálása](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Figyelése és finomhangolása az adatbázis

- [Automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md) ahhoz, hogy az Azure SQL Database optimalizálása a számítási feladatok teljesítményére.
- [Az automatikus hangolási e-mail-értesítések engedélyezése](sql-database-automatic-tuning-email-notifications.md) finomhangolási javaslatai információt szeretne kapni.
- [Teljesítménnyel kapcsolatos javaslatok alkalmazása](sql-database-advisor-portal.md) és optimalizálhatja adatbázisát.
- [Riasztások létrehozása](sql-database-insights-alerts-portal.md) , értesítéseket kaphat az Azure SQL Database-ből.
- [Kapcsolatok hibaelhárítása](sql-database-troubleshoot-common-connection-issues.md) Ha láthatja, hogy az alkalmazások és az adatbázis közötti kapcsolódási problémák. Is [kapcsolattal fennálló problémákat a Resource Health](sql-database-resource-health.md).
- [Lapozófájl-terület kezelésére](sql-database-file-space-management.md) az adatbázis tárolási felhasználásának figyelésére.

## <a name="query-distributed-data"></a>Lekérdezések elosztott adatai

- [Függőlegesen particionált adatok lekérdezése](sql-database-elastic-query-getting-started-vertical.md) több adatbázis között.
- [Horizontálisan felskálázott adatszint között jelentés](sql-database-elastic-query-horizontal-partitioning.md).
- [Lekérdezés különböző sémákkal rendelkező táblákat lefedő](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Rugalmas adatbázis-feladatok

- [Létrehozásához és kezeléséhez](elastic-jobs-powershell.md) Elastic Database-feladatok PowerShell használatával.
- [Létrehozásához és kezeléséhez](elastic-jobs-tsql.md) Elastic Database-feladatok Transact-SQL használatával.
- [Telepítse át régi rugalmas feladat](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Adatbázis horizontális skálázása

- [Frissítési elastic database-ügyfélkódtár](sql-database-elastic-scale-upgrade-client-library.md).
- [Szilánkokra osztott alkalmazás létrehozása](sql-database-elastic-scale-get-started.md).
- [Horizontálisan skálázott adatok lekérdezése](sql-database-elastic-query-getting-started.md).
- Futtatás [több horizontális partíciós lekérdezések](sql-database-elastic-scale-multishard-querying.md).
- [Szilánkokra osztott adatok áthelyezése](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Biztonság konfigurálása](sql-database-elastic-scale-split-merge-security-configuration.md) az adatbázis-szilánkból.
- [Szilánk hozzáadása](sql-database-elastic-scale-add-a-shard.md) szeretné az aktuális adatbázis-szilánkból.
- [Szilánkleképezési problémák javítása](sql-database-elastic-database-recovery-manager.md).
- [Szilánkokra osztott adatbázis migrálása](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Számlálók létrehozása](sql-database-elastic-database-perf-counters.md).
- [Entitás-keretrendszer használata](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) horizontálisan skálázott adatokat lekérdezni.
- [Használja a Dapper keretrendszer](sql-database-elastic-scale-working-with-dapper.md) horizontálisan skálázott adatokat lekérdezni.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [útmutató végigvezeti a felügyelt példány](sql-database-howto-managed-instance.md)
