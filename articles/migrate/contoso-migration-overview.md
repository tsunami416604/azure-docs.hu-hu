---
title: Contoso áttelepítési sorozat | Microsoft dokumentumok
description: A Contoso-ra mutató példaáttelepítési forgatókönyvekre mutató hivatkozások az Azure-ba való migráláshoz.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676330"
---
# <a name="contoso-migration-series"></a>Contoso migrálási sorozat


Cikksorozatot készítünk, amely bemutatja, hogy a Contoso fiktív szervezet hogyan telepíti át helyszíni infrastruktúráját a Microsoft Azure-felhőbe. [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) 

A sorozat olyan forgatókönyveket tartalmaz, amelyek bemutatják az infrastruktúra-áttelepítés beállítását és a különböző típusú áttelepítések futtatását. A forgatókönyvek összetettsége a fejlődés előrehaladtával növekszik. A cikkek azt mutatják, hogy a Contoso vállalat hogyan kezeli az áttelepítést, de az általános utasítások és mutatók az egész.

## <a name="migration-articles"></a>Áttelepítési cikkek

A sorozat cikkeit az alábbi táblázat foglalja össze.  

- Minden áttelepítési forgatókönyvet némileg eltérő üzleti követelmények vezérelnek, amelyek meghatározzák az áttelepítési stratégiát.
- Minden egyes telepítési forgatókönyv, adunk tájékoztatást az üzleti vezetők és célok, a javasolt architektúra, az áttelepítés végrehajtásának lépéseit, és javaslatokat a karbantartás és a következő lépéseket az áttelepítés befejezése után.


**Cikk** | **Részletek** 
--- | --- 
[1. cikk: Áttekintés](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | A cikksorozat, a Contoso áttelepítési stratégiája és a sorozatban használt mintaalkalmazások áttekintése. 
[2. cikk: Az Azure-infrastruktúra telepítése](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | A Contoso előkészíti a helyszíni infrastruktúrát és az Azure-infrastruktúrát az áttelepítéshez. Ugyanez az infrastruktúra használatos a sorozat összes cikke. 
[3. cikk: Az Azure-ba való áttelepítés helyszíni erőforrásainak felmérése](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | A Contoso a VMware-en futó helyszíni SmartHotel360 alkalmazás értékelését futtatja. A Contoso értékeli az Azure Migrate szolgáltatás és az SQL Server alkalmazás adatbázisát használó alkalmazásvirtuális gépeket az Adatáttelepítési segéd használatával.
[4. cikk: Alkalmazás újraüzemeltetése Egy Azure virtuális gépen és az SQL-adatbázis felügyelt példányán](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | A Contoso lift-and-shift áttelepítést futtat az Azure-ba a helyszíni SmartHotel360 alkalmazásához. A Contoso áttelepíti az alkalmazás előtér-virtuális gépét az [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview)használatával. A Contoso áttelepíti az alkalmazásadatbázist egy Azure SQL Database Felügyelt példányba az [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)használatával.
[5. cikk: Alkalmazás újraüzemeltetése az Azure virtuális gépein](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | A Contoso áttelepíti a SmartHotel360 alkalmazás virtuális gépeit az Azure-beli virtuális gépekre az Azure Migrate szolgáltatás használatával. 
[6. cikk: Alkalmazás újraüzemeltetése az Azure virtuális gépein és egy SQL Server AlwaysOn rendelkezésre állási csoportban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | A Contoso áttelepíti a SmartHotel360 alkalmazást. A Contoso az Azure Migrate segítségével telepíti át az alkalmazás virtuális gépeit. Az Adatbázis-áttelepítési szolgáltatás segítségével telepíti át az alkalmazásadatbázist egy AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtbe. 
[7. cikk: Linux-alkalmazás újraüzemeltetése Az Azure virtuális gépein](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | A Contoso befejezi a Linux osTicket alkalmazás ának az Azure-beli virtuális gépekre való feloldását és áthelyezését az Azure Migrate használatával.
[8. cikk: Linux-alkalmazás újraüzemeltetése az Azure virtuális gépein és a MySQL Azure-adatbázisában](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | A Contoso áttelepíti a Linux osTicket alkalmazást az Azure-beli virtuális gépekre az Azure Migrate használatával. Áttelepíti az alkalmazás-adatbázist az Azure Database for MySQ-ba az Azure Database Migration Service használatával (alternatív lehetőséget tartalmaz a MySQL Workbench használatával).
[9. cikk: Alkalmazás újrafaktoriba számítása egy Azure-webalkalmazásban és az Azure SQL Database-ben](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | A Contoso áttelepíti a SmartHotel360 alkalmazást egy Azure-webalkalmazásba, és az Azure Database Migration Service használatával áttelepíti az alkalmazásadatbázist egy Azure SQL Server-példányba.
[10. cikk: Windows-alkalmazások újrafaktoriba számítása az Azure App Services és az SQL felügyelt példány használatával](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | A Contoso áttelepít egy helyszíni Windows-alapú alkalmazást egy Azure-webalkalmazásba, és áttelepíti az alkalmazásadatbázist egy Azure SQL felügyelt példányba az Azure Database Migration Service használatával.
[11. cikk: Linux-alkalmazás újrafaktoriba számítása egy Azure-webalkalmazásban és az Azure Database for MySQL-ben](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | A Contoso áttelepíti Linux osTicket alkalmazását egy Azure-webalkalmazásba több Azure-régióban, az Azure Traffic Manager használatával, amely a GitHubbal integrálva van a folyamatos kézbesítéshez. Contoso áttelepíti az alkalmazás-adatbázis egy Azure-adatbázis a MySQL-példány. 
[12. cikk: Refactor Team Foundation Server az Azure DevOps-szolgáltatásokon](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | A Contoso áttelepíti a helyszíni Team Foundation Server telepítését az Azure DevOps-szolgáltatásokba az Azure-ban.
[13. cikk: Alkalmazás újraépítése az Azure-ban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | A Contoso újraépíti SmartHotel-alkalmazását számos Azure-képesség és -szolgáltatás használatával, többek között az Azure App Service, az Azure Kubernetes Service (AKS), az Azure Functions, az Azure Cognitive Services és az Azure Cosmos DB használatával.
[14. cikk: Az Azure-ba való áttelepítés méretezése](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Miután kipróbálta az áttelepítési kombinációkat, a Contoso előkészíti az Azure-ba való teljes áttelepítésre való skálázást.



## <a name="next-steps"></a>További lépések

- [További információ a felhőbe](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) való migrálásról.
- Az [Adatbázis-áttelepítési útmutatóban](https://datamigration.microsoft.com/)további információ az egyéb forgatókönyvekhez (forrás-/célpárokhoz) való áttelepítési stratégiákról.
