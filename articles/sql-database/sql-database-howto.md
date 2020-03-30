---
title: Konfigurálás és kezelés
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az Azure SQL Database-t.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 11/14/2019
ms.openlocfilehash: c3f7b33e4b42b08334cfb687024985c878dc3713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209428"
---
# <a name="how-to-use-azure-sql-database"></a>Az Azure SQL Database használata

Ebben a szakaszban különböző útmutatókat, parancsfájlokat és magyarázatokat talál, amelyek segíthetnek az Azure SQL-adatbázis kezelésében és konfigurálásában. Az [egységes adatbázishoz](sql-database-howto-single-database.md) és a felügyelt példányhoz tartozó útmutatókat is [megtalálhatja.](sql-database-howto-managed-instance.md)

## <a name="load-data"></a>Adatok betöltése

- [Egyetlen adatbázis vagy készletbe adott adatbázis másolása az Azure-on belül](sql-database-copy.md)
- [Adatbázis importálása BACPAC-ból](sql-database-import.md)
- [Adatbázis exportálása BACPAC-ba](sql-database-export.md)
- [Adatok betöltése BCP protokollal](sql-database-load-from-csv-with-bcp.md)
- [Adatok betöltése az ADF használatával](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Adatszinkronizálás

- [SQL-adatszinkronizálás](sql-database-sync-data.md)
- [Adatszinkronizálási ügynök](sql-database-data-sync-agent.md)
- [Sémaváltozások replikálása](sql-database-update-sync-schema.md)
- [Figyelés az OMS-szel](sql-database-sync-monitor-oms.md)
- [Ajánlott eljárások az adatszinkronizáláshoz](sql-database-best-practices-data-sync.md)
- [Az adatszinkronizálás hibaelhárítása](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Monitorozás és finomhangolás

- [Manuális hangolás](sql-database-performance-guidance.md)
- [A teljesítmény monitorozása DMV-kkel](sql-database-monitoring-with-dmvs.md)
- [A teljesítmény monitorozása a lekérdezéstár használatával](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Teljesítményproblémák elhárítása az Intelligent Insights segítségével](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Az Intelligent Insights diagnosztikai naplójának használata](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Memóriabeli OLTP területének monitorozása](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Bővített események

- [Bővített események](sql-database-xevent-db-diff-from-svr.md)
- [Kiterjesztett események tárolása eseményfájlba](sql-database-xevent-code-event-file.md)
- [Kiterjesztett események tárolása gyűrűpufferbe](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Funkciók konfigurálása

- [Azure AD-hitelesítés konfigurálása](sql-database-aad-authentication-configure.md)
- [Feltételes hozzáférés konfigurálása](sql-database-conditional-access.md)
- [Többtényezős AAD-hitelesítés](sql-database-ssms-mfa-authentication.md)
- [Többtényezős hitelesítés konfigurálása](sql-database-ssms-mfa-authentication-configure.md)
- [Ideiglenes adatmegőrzési szabály konfigurálása](sql-database-temporal-tables-retention-policy.md)
- [A TDE konfigurálása BYOK megoldással](transparent-data-encryption-byok-azure-sql-configure.md)
- [TDE BYOK-kulcsok váltása](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [A TDE-védő eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Memóriabeli OLTP konfigurálása](sql-database-in-memory-oltp-migration.md)
- [Az Azure Automation konfigurálása](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Alkalmazások fejlesztése

- [Kapcsolat](sql-database-libraries.md)
- [Spark-összekötő használata](sql-database-spark-connector.md)
- [Alkalmazás hitelesítése](sql-database-client-id-keys.md)
- [Kötegelés használata a jobb teljesítmény érdekében](sql-database-use-batching-to-improve-performance.md)
- [Csatlakoztatási útmutató](sql-database-connectivity-issues.md)
- [DNS-aliasok](dns-alias-overview.md)
- [A PowerShell DNS-alias beállítása](dns-alias-powershell.md)
- [Portok – ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C és C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Alkalmazások tervezése

- [Tervezés vészhelyreállításra](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Tervezés rugalmas készletekhez](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Tervezés alkalmazásfrissítésekhez](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Több-bérlős SaaS-alkalmazások tervezése

- [SaaS-kialakítási minták](saas-tenancy-app-design-patterns.md)
- [SaaS-videóindexelő](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS-alkalmazásbiztonság](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>További lépések

- További információ [a felügyelt példányok útmutatóiról.](sql-database-howto-managed-instance.md)
- További információ [az egyes adatbázisok útmutatóiról.](sql-database-howto-single-database.md)
