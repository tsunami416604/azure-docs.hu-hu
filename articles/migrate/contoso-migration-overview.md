---
title: Contoso áttelepítési sorozat | Microsoft dokumentumok
description: Áttekintést nyújt az áttelepítési stratégia és a forgatókönyvek által használt Contoso áttelepíteni a helyszíni adatközpont az Azure-ba.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: a1805d2c7568d3104ce499c67c516c0bf94b2db7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460256"
---
# <a name="contoso-migration-series"></a>Contoso migrálási sorozat


Cikksorozatot készítünk, amely bemutatja, hogy a Contoso fiktív szervezet hogyan telepíti át a helyszíni infrastruktúrát a [Microsoft Azure-felhőbe.](https://azure.microsoft.com/overview/what-is-azure/) 

A sorozat olyan információkat és forgatókönyveket tartalmaz, amelyek bemutatják az infrastruktúra áttelepítésének beállítását és a különböző típusú áttelepítések futtatását. A forgatókönyvek összetettsége a fejlődés előrehaladtával növekszik. A cikkek azt mutatják, hogy a Contoso vállalat hogyan teljesíti az áttelepítési küldetést, de az általános olvasásra és a konkrét utasításokra vonatkozó mutatók az egész.

## <a name="migration-articles"></a>Áttelepítési cikkek

A sorozat cikkeit az alábbi táblázat foglalja össze.  

- A migrálási forgatókönyveket némileg eltérő üzleti célok vezérlik, amelyek meghatározzák a migrálási stratégiát.
- Minden egyes üzembehelyezési forgatókönyv esetében információt nyújtunk az üzleti motivációkról és célokról, a javasolt architektúráról, a migrálás lépéseiről, valamint a migrálás befejezése utáni tisztításról és további lépésekről.

**Cikk** | **Részletek** 
--- | --- 
[1. cikk: Áttekintés](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | A cikksorozat, a Contoso áttelepítési stratégiája és a sorozatban használt mintaalkalmazások áttekintése. 
[2. cikk: Az Azure-infrastruktúra telepítése](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | A Contoso előkészíti a helyszíni infrastruktúrát és az Azure-infrastruktúrát az áttelepítéshez. Ugyanazt az infrastruktúrát használja a sorozat összes áttelepítési cikke. 
[3. cikk: Az Azure-ba való áttelepítés helyszíni erőforrásainak felmérése](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | A Contoso a VMware-en futó helyszíni SmartHotel360 alkalmazás értékelését futtatja. A Contoso értékeli az Azure Migrate szolgáltatás és az SQL Server alkalmazás adatbázisát használó alkalmazásvirtuális gépeket az Adatáttelepítési segéd használatával.
[4. cikk: Alkalmazás újraüzemeltetése Egy Azure virtuális gépen és az SQL-adatbázis felügyelt példányán](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | A Contoso lift-and-shift áttelepítést futtat az Azure-ba a helyszíni SmartHotel360 alkalmazásához. Contoso áttelepíti az alkalmazás előtér-virtuális gép az [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)használatával. A Contoso áttelepíti az alkalmazásadatbázist egy Azure SQL Database Felügyelt példányba az [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)használatával.
[5. cikk: Alkalmazás újraüzemeltetése az Azure virtuális gépein](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | A Contoso áttelepíti a SmartHotel360 alkalmazás virtuális gépeit az Azure virtuális gépekre a Site Recovery szolgáltatás használatával. 
[6. cikk: Alkalmazás újraüzemeltetése az Azure virtuális gépein és egy SQL Server AlwaysOn rendelkezésre állási csoportban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |A Contoso áttelepíti a SmartHotel360 alkalmazást. A Contoso a Site Recovery segítségével telepíti át az alkalmazás virtuális gépeit. Az Adatbázis-áttelepítési szolgáltatás segítségével telepíti át az alkalmazásadatbázist egy AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtbe. 
[7. cikk: Linux-alkalmazás újraüzemeltetése Az Azure virtuális gépein](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | A Contoso befejezi a Linux osTicket alkalmazás ának az Azure-beli virtuális gépekre való feloldását a Site Recovery szolgáltatás használatával.
[8. cikk: Linux-alkalmazás újraüzemeltetése az Azure virtuális gépein és a MySQL Azure-adatbázisában](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | A Contoso áttelepíti a Linux osTicket alkalmazást az Azure-beli virtuális gépekre a Site Recovery használatával. Az alkalmazás adatbázisát a MySQL Workbench használatával a MySQL Azure Database for MySQL-be migrálja. 
[9. cikk: Alkalmazás újrafaktoriba számítása egy Azure-webalkalmazásban és az Azure SQL Database-ben](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | A Contoso áttelepíti a SmartHotel360 alkalmazást egy Azure-webalkalmazásba, és áttelepíti az alkalmazásadatbázist egy Azure SQL Server-példányba az adatbázis-áttelepítési segéddel.     
[10. cikk: Linux-alkalmazás újrafaktoriba számítása egy Azure-webalkalmazásban és az Azure Database for MySQL-ben](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | A Contoso áttelepíti Linux osTicket alkalmazását egy Azure-webalkalmazásba több Azure-régióban az Azure Traffic Manager használatával, amely a GitHubbal integrálva van a folyamatos kézbesítés érdekében. Contoso áttelepíti az alkalmazás-adatbázis egy Azure-adatbázis a MySQL-példány. 
[11. cikk: Refactor Team Foundation Server az Azure DevOps-szolgáltatásokon](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | A Contoso áttelepíti a helyszíni Team Foundation Server telepítését az Azure DevOps-szolgáltatásokba az Azure-ban.
[12. cikk: Alkalmazás újraépítése az Azure-ban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | A Contoso az Azure-képességek és -szolgáltatások széles választékával újjáépíti SmartHotel-alkalmazását, többek között az Azure App Service, az Azure Kubernetes Service (AKS), az Azure Functions, az Azure Cognitive Services és az Azure Cosmos DB használatával.
[13. cikk: Az Azure-ba való áttelepítés méretezése](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Miután kipróbálta az áttelepítési kombinációkat, a Contoso előkészíti az Azure-ba való teljes áttelepítésre való skálázást.

## <a name="next-steps"></a>További lépések

- [További információ a felhőbe](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) való migrálásról.
- Az [Adatbázis-áttelepítési útmutatóban](https://datamigration.microsoft.com/)az egyéb forgatókönyvekhez (forrás-/célpárokhoz) való áttelepítési stratégiákról olvashat.
