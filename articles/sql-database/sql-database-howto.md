---
title: Azure SQL Database konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az Azure SQL Database.
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
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53440505"
---
# <a name="how-to-use-azure-sql-database"></a>Azure SQL Database használatával

Ebben a szakaszban található különböző útmutatók, parancsfájlok és leírásokkal, amelyek segítségével kezelheti és konfigurálhatja az Azure SQL Database. A konkrét útmutatókat is találhat [önálló adatbázis](sql-database-howto-single-database.md) és [felügyelt példány](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Adatok betöltése

- [Adatbázis másolása egy egyetlen Azure-ban](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [Adatbázis importálása BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [Adatbázis exportálása BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [Adatok betöltése a BCP használatával](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [Adatok betöltése az ADF használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Adatszinkronizálás

- [SQL Data Sync](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [Adatok szinkronizációs ügynök](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [Sémaváltozások replikálása](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [Figyelés az OMS-szel](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [Ajánlott eljárások az adatok szinkronizálása](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [Adatszinkronizálás hibaelhárítása](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>Monitorozás és finomhangolás

-  [Manuális hangolás](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Teljesítmény figyelése dinamikus felügyeleti nézetek használata](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Teljesítmény figyelése a lekérdezéstár használatával](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Az Intelligent Insights-teljesítményi hibák elhárítása](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Intelligens elemzési diagnosztikai naplót használja](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [Memóriabeli OLTP lemezterület figyelése](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>Bővített események

- [Bővített események](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [Bővített eseményeinek Store esemény fájlba](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [Gyűrűpuffer Store bővített események](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>Funkciók konfigurálása

- [Az Azure AD-hitelesítés konfigurálása](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [Feltételes hozzáférés konfigurálása](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [Multi-factor Authentication AAD-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Többtényezős hitelesítés konfigurálása](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [Időalapú adatmegőrzési szabály konfigurálása](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [A BYOK TDE konfigurálása](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [TDE BYOK kulcsainak rotálása](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [Távolítsa el a TDE-védőhöz](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [Memóriabeli OLTP konfigurálása](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Az Azure Automation konfigurálása](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>Alkalmazások fejlesztése

- [Kapcsolatok](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Spark-összekötő használata](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [Alkalmazás hitelesítése](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [Hibaüzenetek](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [A jobb teljesítmény érdekében kötegelés használata](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [Csatlakoztatási útmutató](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [DNS-aliasok](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [DNS PowerShell-alias beállítása](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [Portok – ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C és C ++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>Alkalmazások tervezése

- [Vész-helyreállítási terv](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [Rugalmas kialakítás](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [Alkalmazásfrissítések tervezése](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>Több-bérlős SaaS-alkalmazások tervezése

- [SaaS-kialakítási minták](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [SaaS-videóindexelő](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [SaaS-alkalmazásbiztonság](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [útmutató végigvezeti a felügyelt példány](sql-database-howto-managed-instance.md).
- Tudjon meg többet [útmutató végigvezeti az önálló adatbázis](sql-database-howto-single-database.md).
