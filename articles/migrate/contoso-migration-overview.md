---
title: Contoso áttelepítési sorozat | Microsoft Docs
description: Áttekintést nyújt a contoso által a helyszíni adatközpont Azure-ba való áttelepítéséhez használt áttelepítési stratégiáról és forgatókönyvekről.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: d37bee589eb7ee2e6e30c8dcea2531dd1f063481
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78939185"
---
# <a name="contoso-migration-series"></a>Contoso migrálási sorozat


Számos cikkből megtudhatja, hogy a fiktív szervezet contoso hogyan telepítse át a helyszíni infrastruktúrát a [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) felhőbe. 

Az adatsorozat olyan információkat és forgatókönyveket tartalmaz, amelyek bemutatják, hogyan állíthatja be az infrastruktúra áttelepítését, és hogyan futtathat különböző típusú áttelepítéseket. A forgatókönyvek az előrehaladásuk bonyolultságával növekednek. A cikkek bemutatják, hogy a contoso vállalat hogyan teljesíti az áttelepítési feladatot, de az általános olvasási és a konkrét utasításokra mutató mutatók az egész időszakban elérhetők.

## <a name="migration-articles"></a>Áttelepítési cikkek

A sorozat cikkei az alábbi táblázatban vannak összegezve.  

- A migrálási forgatókönyveket némileg eltérő üzleti célok vezérlik, amelyek meghatározzák a migrálási stratégiát.
- Minden egyes üzembehelyezési forgatókönyv esetében információt nyújtunk az üzleti motivációkról és célokról, a javasolt architektúráról, a migrálás lépéseiről, valamint a migrálás befejezése utáni tisztításról és további lépésekről.

**Cikk** | **Részletek** 
--- | --- 
[1. cikk: áttekintés](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | A cikk sorozata, a contoso áttelepítési stratégiája és az adatsorozatban használt minta alkalmazások áttekintése. 
[2. cikk: az Azure-infrastruktúra üzembe helyezése](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | A contoso felkészíti a helyszíni infrastruktúrát és az Azure-infrastruktúráját az áttelepítésre. A sorozat összes áttelepítési cikkéhez ugyanazt az infrastruktúrát használja a rendszer. 
[3. cikk: helyszíni erőforrások felmérése az Azure-ba való áttelepítéshez](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | A contoso a VMware-en futó helyszíni SmartHotel360-alkalmazás értékelését futtatja. A contoso a Azure Migrate szolgáltatással és az alkalmazás SQL Server-adatbázissal Data Migration Assistant használatával vizsgálja ki az alkalmazás virtuális gépeket.
[4. cikk: alkalmazások újraüzemeltetése Azure-beli virtuális gépen és SQL Database felügyelt példány](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | A contoso az Azure-ba helyezi át a helyszíni SmartHotel360-alkalmazáshoz való átállást. A contoso a [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)használatával telepíti át az alkalmazás ELŐTÉR-virtuális gépét. A contoso a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)használatával áttelepíti az alkalmazás-adatbázist egy Azure SQL Database felügyelt példányra.
[5. cikk: alkalmazások újraüzemeltetése Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | A contoso a Site Recovery szolgáltatás használatával áttelepíti a SmartHotel360-alkalmazás virtuális gépeit az Azure-beli virtuális gépekre. 
[6. cikk: alkalmazások újraüzemeltetése Azure-beli virtuális gépeken és SQL Server alAlwaysOnon rendelkezésre állási csoportban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |A contoso áttelepíti a SmartHotel360 alkalmazást. A contoso a Site Recovery használatával telepíti át az alkalmazás virtuális gépeket. A Database Migration Service használatával telepíti át az alkalmazás-adatbázist egy olyan SQL Server-fürtre, amelyet egy alAlwaysOnon rendelkezésre állási csoport véd. 
[7. cikk: Linux-alkalmazás újraüzemeltetése Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | A contoso az Site Recovery szolgáltatás használatával befejezi a linuxos osTicket-alkalmazásának az Azure-beli virtuális gépekre való átemelését és átváltását.
[8. cikk: Linux-alkalmazás újraüzemeltetése Azure-beli virtuális gépeken és Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | A contoso Site Recovery használatával áttelepíti a linuxos osTicket alkalmazást az Azure-beli virtuális gépekre. Az alkalmazás adatbázisát a MySQL Workbench használatával a MySQL Azure Database for MySQL-be migrálja. 
[9. cikk: alkalmazás újrabontása egy Azure-webalkalmazásban és Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | A contoso áttelepíti a SmartHotel360 alkalmazást egy Azure-webalkalmazásba, és áttelepíti az alkalmazás-adatbázist egy Azure SQL Server-példányra az adatbázis-Migration Assistant használatával.     
[10. cikk: Linux-alkalmazás újrabontása egy Azure-webalkalmazásban és Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | A contoso a Linux osTicket alkalmazást egy Azure-webalkalmazásba helyezi át több Azure-régióban, az Azure Traffic Manager használatával, a folyamatos teljesítés érdekében a GitHubral integráltan. A contoso áttelepíti az alkalmazás adatbázisát egy Azure Database for MySQL példányra. 
[11. cikk: újraTeam Foundation Server az Azure DevOps Services szolgáltatásban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | A contoso áttelepíti a helyszíni Team Foundation Server üzemelő példányát az Azure DevOps Services szolgáltatásba az Azure-ban.
[12. cikk: alkalmazások újratervezése Azure-tárolókban és Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | A contoso áttelepíti a SmartHotel alkalmazást az Azure-ba. Ezután újratervezi az alkalmazás webes szintjét az Azure Service Fabric-ban futtatott Windows-tárolóként, valamint az adatbázist Azure SQL Databasesal.
[13. cikk: alkalmazás újraépítése az Azure-ban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | A contoso számos Azure-képesség és-szolgáltatás használatával újraépíti a SmartHotel alkalmazást, beleértve az Azure App Service, az Azure Kubernetes Service (ak), az Azure Functions, az Azure Cognitive Services és a Azure Cosmos DB.
[14. cikk: Migrálás méretezése az Azure-ba](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Az áttelepítési kombinációk kipróbálása után a contoso arra készül, hogy az Azure-ba történő teljes áttelepítésre méretezhető legyen.

## <a name="next-steps"></a>További lépések

- [Ismerje meg a](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) felhő áttelepítését.
- Ismerje meg az áttelepítési stratégiákat más forgatókönyvek (forrás/cél párok) számára az [adatbázis áttelepítési útmutatójában](https://datamigration.microsoft.com/).
