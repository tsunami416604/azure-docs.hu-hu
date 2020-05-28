---
title: A &-tartalom kezelése hivatkozás konfigurálása
titleSuffix: Azure SQL Managed Instance
description: Az Azure SQL felügyelt példányának konfigurálását és kezelését bemutató dokumentáció.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: bc0c30e234310a4ee1d013d7a11ca556edd071c6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041115"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Az Azure SQL felügyelt példányának tartalmi referenciája
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ebben a cikkben a különböző útmutatókra, parancsfájlokra és magyarázatokra mutató hivatkozásokat talál, amelyek segítenek az Azure SQL felügyelt példányának kezelésében és konfigurálásában.

## <a name="load-data"></a>Adatok betöltése

- [Migrálás egy Azure SQL felügyelt példányra](migrate-to-instance-from-sql-server.md) – megismerheti az ajánlott áttelepítési folyamatot és az Azure SQL felügyelt példányra való áttelepítéshez szükséges eszközöket.
- [TDE-tanúsítvány átirányítása egy felügyelt Azure SQL-példányra](tde-certificate-migrate.md) – ha a SQL Server-adatbázist transzparens adattitkosítással (TDE) védik, át kell telepítenie azt a tanúsítványt, amelyet az SQL által felügyelt példányok használhatnak az Azure-ban visszaállítani kívánt biztonsági mentés visszafejtéséhez.
- [Adatbázis importálása BACPAC-ból](../database/database-import.md)
- [Adatbázis exportálása BACPAC-ba](../database/database-export.md)
- [Adatok betöltése a BCP használatával](../load-from-csv-with-bcp.md)
- [Adatok betöltése az ADF használatával](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Hálózati konfiguráció

- [Alhálózat méretének meghatározása](vnet-subnet-determine-size.md) Mivel az alhálózat nem méretezhető át az SQL felügyelt példányának telepítése után, ki kell számítania, hogy milyen IP-címtartományt kell megadni az alhálózatra telepítendő SQL felügyelt példányok számához és típusaihoz. 
- [Új VNet és alhálózat létrehozása](virtual-network-subnet-create-arm-template.md) Konfigurálja a virtuális hálózatot és az alhálózatot az [itt leírt hálózati követelmények](connectivity-architecture-overview.md#network-requirements) szerint 
- [Meglévő VNet és alhálózat konfigurálása](vnet-existing-add-subnet.md) Ellenőrizze a hálózati követelményeket, és konfigurálja a meglévő virtuális hálózatot és alhálózatot az SQL felügyelt példányának telepítéséhez. 
- [Egyéni DNS konfigurálása](custom-dns-configure.md) Konfigurálja az egyéni DNS-t úgy, hogy külső erőforrás-hozzáférést biztosítson az SQL felügyelt példányán lévő egyéni tartományokhoz az adatbázis-levelezési profilok csatolt kiszolgálóján keresztül. 
- [Hálózati konfiguráció szinkronizálása](azure-app-sync-network-configuration.md) Frissítse a hálózatkezelési konfigurációs tervet, ha nem tud kapcsolatot létesíteni az [alkalmazás Azure-Virtual Network való integrálását](../../app-service/web-sites-integrate-with-vnet.md) követően
- [Felügyeleti végpont IP-címének keresése](management-endpoint-find-ip-address.md) Határozza meg a nyilvános végpontot, amelyet az SQL felügyelt példánya felügyeleti célokra használ. 
- [Beépített tűzfalbeállítások ellenőrzése](management-endpoint-verify-built-in-firewall.md) Ellenőrizze, hogy az SQL felügyelt példánya csak a szükséges portokon és egyéb beépített tűzfalszabályok esetében engedélyezi-e a forgalmat. 
- [Alkalmazások összekötése](connect-application-instance.md) Ismerje meg az alkalmazások SQL felügyelt példányhoz való csatlakoztatásának különböző mintáit.

## <a name="feature-configuration"></a>Szolgáltatás konfigurációja

- [Azure AD-hitelesítés konfigurálása](../database/authentication-aad-configure.md)
- [Feltételes hozzáférés konfigurálása](../database/conditional-access-configure.md)
- [Többtényezős AAD-hitelesítés](../database/authentication-mfa-ssms-overview.md)
- [Többtényezős hitelesítés konfigurálása](../database/authentication-mfa-ssms-configure.md)
- [Ideiglenes adatmegőrzési szabály konfigurálása](../database/temporal-tables-retention-policy.md)
- [A TDE konfigurálása BYOK megoldással](../database/transparent-data-encryption-byok-configure.md)
- [TDE BYOK-kulcsok váltása](../database/transparent-data-encryption-byok-key-rotation.md)
- [A TDE-védő eltávolítása](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Memóriabeli OLTP konfigurálása](../in-memory-oltp-configure.md)
- [Azure Automation konfigurálása](../database/automation-manage.md)
- A [tranzakciós replikáció](replication-between-two-instances-configure-tutorial.md) lehetővé teszi az adatok replikálását az Azure SQL felügyelt példányai között, illetve a helyszíni SQL Serverból egy SQL felügyelt példányra, és fordítva.
- A [fenyegetések észlelésének konfigurálása](threat-detection-configure.md) – a [fenyegetések észlelése](../database/threat-detection-overview.md) egy beépített Azure SQL felügyelt példány funkciója, amely különböző lehetséges támadásokat, például SQL-injektálást vagy gyanús helyekről való hozzáférést észlel. 
- A [riasztások létrehozása](alerts-create.md) lehetővé teszi a riasztások beállítását a figyelt mérőszámokon, például a processzor kihasználtsága, a tárterület-felhasználás, a IOPS és mások számára az SQL felügyelt példányai esetében. 

## <a name="monitoring-and-tuning"></a>Monitorozás és finomhangolás

- [Manuális hangolás](../database/performance-guidance.md)
- [A teljesítmény monitorozása DMV-kkel](../database/monitoring-with-dmvs.md)
- [A teljesítmény monitorozása a lekérdezéstár használatával](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Teljesítményproblémák elhárítása az Intelligent Insights segítségével](../database/intelligent-insights-troubleshoot-performance.md)
- [Az Intelligent Insights diagnosztikai naplójának használata](../database/intelligent-insights-use-diagnostics-log.md)
- [Memóriabeli OLTP területének monitorozása](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Bővített események

- [Bővített események](../database/xevent-db-diff-from-svr.md)
- [Kiterjesztett események tárolása az Event file-ban](../database/xevent-code-event-file.md)
- [Kiterjesztett események tárolása gyűrűs pufferbe](../database/xevent-code-ring-buffer.md)

## <a name="develop-applications"></a>Alkalmazások fejlesztése

- [Kapcsolatok](../database/connect-query-content-reference-guide.md#libraries)
- [Spark-összekötő használata](../../cosmos-db/spark-connector.md)
- [Alkalmazás hitelesítése](../database/application-authentication-get-client-id-keys.md)
- [Kötegelt feldolgozás használata a jobb teljesítmény érdekében](../performance-improve-use-batching.md)
- [Csatlakoztatási útmutató](../database/troubleshoot-common-connectivity-issues.md)
- [DNS-aliasok](../database/dns-alias-overview.md)
- [DNS-alias beállítása PowerShell](../database/dns-alias-powershell-create.md)
- [Portok – ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C és C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Alkalmazások tervezése

- [Tervezés vészhelyreállításra](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Tervezés rugalmas készletekhez](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Tervezés alkalmazásfrissítésekhez](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Több-bérlős SaaS-alkalmazások tervezése

- [SaaS-kialakítási minták](../database/saas-tenancy-app-design-patterns.md)
- [SaaS-videóindexelő](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS-alkalmazásbiztonság](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)



## <a name="next-steps"></a>Következő lépések

Első lépések: [az SQL felügyelt példányának üzembe helyezése](instance-create-quickstart.md)
