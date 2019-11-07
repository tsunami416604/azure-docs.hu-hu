---
title: Azure SQL Database konfigurálása
description: Útmutató Azure SQL Database konfigurálásához és kezeléséhez.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: ccdeb883dc9cf2cba499e45e25ff4706bb120463
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689878"
---
# <a name="how-to-use-azure-sql-database"></a>A Azure SQL Database használata

Ebben a szakaszban különféle útmutatókat, parancsfájlokat és magyarázatokat talál, amelyek segíthetnek a Azure SQL Database kezelésében és konfigurálásában. Az [önálló adatbázisokhoz](sql-database-howto-single-database.md) és a [felügyelt példányokhoz](sql-database-howto-managed-instance.md)is találhat konkrét útmutatókat.

## <a name="load-data"></a>Adatok betöltése

- [Egyetlen adatbázis vagy készletezett adatbázis másolása az Azure-on belül](sql-database-copy.md)
- [ADATBÁZIS importálása BACPAC](sql-database-import.md)
- [ADATBÁZIS exportálása a BACPAC-be](sql-database-export.md)
- [Adatok betöltése a BCP használatával](sql-database-load-from-csv-with-bcp.md)
- [Adatok betöltése az ADF használatával](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Adatszinkronizálás

- [SQL-adatszinkronizálás](sql-database-sync-data.md)
- [Adatszinkronizálási ügynök](sql-database-data-sync-agent.md)
- [Séma módosításainak replikálása](sql-database-update-sync-schema.md)
- [Figyelés az OMS-szel](sql-database-sync-monitor-oms.md)
- [Ajánlott eljárások az adatszinkronizáláshoz](sql-database-best-practices-data-sync.md)
- [Az adatszinkronizálás hibáinak megoldása](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Monitorozás és finomhangolás

- [Manuális hangolás](sql-database-performance-guidance.md)
- [A DMV használata a teljesítmény figyeléséhez](sql-database-monitoring-with-dmvs.md)
- [A lekérdezési tároló használata a teljesítmény figyeléséhez](sql-database-operate-query-store.md)
- [Teljesítménnyel kapcsolatos hibák Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Intelligent Insights diagnosztikai napló használata](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Memóriabeli OLTP-terület figyelése](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Kiterjesztett események

- [Bővített események](sql-database-xevent-db-diff-from-svr.md)
- [Kiterjesztett események tárolása az Event file-ban](sql-database-xevent-code-event-file.md)
- [Kiterjesztett események tárolása gyűrűs pufferbe](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Funkciók konfigurálása

- [Az Azure AD-hitelesítés konfigurálása](sql-database-aad-authentication-configure.md)
- [Feltételes hozzáférés konfigurálása](sql-database-conditional-access.md)
- [Multi-Factor HRE-hitelesítés](sql-database-ssms-mfa-authentication.md)
- [Multi-Factor Auth konfigurálása](sql-database-ssms-mfa-authentication-configure.md)
- [Időbeli megőrzési szabály konfigurálása](sql-database-temporal-tables-retention-policy.md)
- [TDE konfigurálása a BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [TDE BYOK-kulcsok elforgatása](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [TDE-védő eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Memóriabeli OLTP konfigurálása](sql-database-in-memory-oltp-migration.md)
- [Azure Automation konfigurálása](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Alkalmazások fejlesztése

- [Kapcsolatok](sql-database-libraries.md)
- [Spark-összekötő használata](sql-database-spark-connector.md)
- [Alkalmazás hitelesítése](sql-database-client-id-keys.md)
- [Hibaüzenetek](sql-database-develop-error-messages.md)
- [Kötegelt feldolgozás használata a jobb teljesítmény érdekében](sql-database-use-batching-to-improve-performance.md)
- [Csatlakoztatási útmutató](sql-database-connectivity-issues.md)
- [DNS-aliasok](dns-alias-overview.md)
- [DNS-alias beállítása PowerShell](dns-alias-powershell.md)
- [Portok – ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C és C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Alkalmazások tervezése

- [Vészhelyreállítási terv](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Rugalmas készletek tervezése](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Alkalmazások verziófrissítésének tervezése](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Több-bérlős SaaS-alkalmazások tervezése

- [SaaS-kialakítási minták](saas-tenancy-app-design-patterns.md)
- [SaaS video Indexer](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS-alkalmazás biztonsága](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>További lépések

- További információ a [felügyelt példányokkal kapcsolatos útmutatókról](sql-database-howto-managed-instance.md).
- További információ [az önálló adatbázisok](sql-database-howto-single-database.md)útmutatóinak megismeréséről.
