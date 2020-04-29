---
title: Contoso áttelepítési sorozat | Microsoft Docs
description: A contoso példa áttelepítési forgatókönyvekre mutató hivatkozások az Azure-ba való áttelepítéshez.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676330"
---
# <a name="contoso-migration-series"></a>Contoso migrálási sorozat


Számos cikkből megtudhatja, hogy a fiktív szervezet contoso hogyan telepíti át a helyszíni infrastruktúrát a [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) felhőbe. 

Az adatsorozat olyan forgatókönyveket tartalmaz, amelyek bemutatják, hogyan állíthatja be az infrastruktúra-áttelepítést, és hogyan futtathat különböző típusú áttelepítéseket. A forgatókönyvek az előrehaladásuk bonyolultságával növekednek. A cikkek bemutatják, hogyan kezeli a contoso vállalat az áttelepítést, de az általános utasítások és mutatók a teljes egészében elérhetők.

## <a name="migration-articles"></a>Áttelepítési cikkek

A sorozat cikkei az alábbi táblázatban vannak összegezve.  

- Az áttelepítési forgatókönyveket némileg eltérő üzleti követelmények vezérlik, amelyek meghatározzák az áttelepítési stratégiát.
- Minden egyes üzembe helyezési forgatókönyv esetében információt nyújtunk az üzleti illesztőprogramokról és a célokról, a javasolt architektúráról, az áttelepítés elvégzésének lépéseiről, valamint az áttelepítés befejezését követő további lépésekről.


**Cikk** | **Részletek** 
--- | --- 
[1. cikk: áttekintés](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | A cikk sorozata, a contoso áttelepítési stratégiája és az adatsorozatban használt minta alkalmazások áttekintése. 
[2. cikk: az Azure-infrastruktúra üzembe helyezése](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | A contoso felkészíti a helyszíni infrastruktúrát és az Azure-infrastruktúrát az áttelepítésre. Ugyanaz az infrastruktúra van használatban az adatsorozat összes cikkében. 
[3. cikk: helyszíni erőforrások felmérése az Azure-ba való áttelepítéshez](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | A contoso a VMware-en futó helyszíni SmartHotel360-alkalmazás értékelését futtatja. A contoso a Azure Migrate szolgáltatással és az alkalmazás SQL Server-adatbázissal Data Migration Assistant használatával vizsgálja ki az alkalmazás virtuális gépeket.
[4. cikk: alkalmazások újraüzemeltetése Azure-beli virtuális gépen és SQL Database felügyelt példány](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | A contoso az Azure-ba helyezi át a helyszíni SmartHotel360-alkalmazáshoz való átállást. A contoso a [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview)használatával telepíti át az alkalmazás ELŐTÉR-virtuális gépét. A contoso áttelepíti az alkalmazás-adatbázist egy Azure SQL Database felügyelt példányra a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)használatával.
[5. cikk: alkalmazások újraüzemeltetése Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | A contoso a Azure Migrate szolgáltatás használatával áttelepíti a SmartHotel360-alkalmazás virtuális gépeit az Azure-beli virtuális gépekre. 
[6. cikk: alkalmazások újraüzemeltetése Azure-beli virtuális gépeken és SQL Server alAlwaysOnon rendelkezésre állási csoportban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | A contoso áttelepíti a SmartHotel360 alkalmazást. A contoso a Azure Migrate használatával telepíti át az alkalmazás virtuális gépeket. A Database Migration Service használatával telepíti át az alkalmazás-adatbázist egy olyan SQL Server-fürtre, amelyet egy alAlwaysOnon rendelkezésre állási csoport véd. 
[7. cikk: Linux-alkalmazás újraüzemeltetése Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | A contoso az Azure Migrate használatával befejezi a linuxos osTicket-alkalmazásának az Azure-beli virtuális gépekre való átemelését és átváltását.
[8. cikk: Linux-alkalmazás újraüzemeltetése Azure-beli virtuális gépeken és Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | A contoso Azure Migrate használatával áttelepíti a linuxos osTicket alkalmazást az Azure-beli virtuális gépekre. Áttelepíti az alkalmazás-adatbázist az Azure Database-be a MySQ-hoz, Azure Database Migration Service használatával (a MySQL Workbench használatával egy másik lehetőség is).
[9. cikk: alkalmazás újrabontása egy Azure-webalkalmazásban és Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | A contoso áttelepíti a SmartHotel360 alkalmazást egy Azure-webalkalmazásba, és áttelepíti az alkalmazás-adatbázist egy Azure SQL Server-példányra a Azure Database Migration Service használatával.
[10. cikk: Windows-alkalmazás újrabontása az Azure App Services és az SQL felügyelt példányának használatával](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | A contoso egy helyszíni Windows-alapú alkalmazást telepít át egy Azure-webalkalmazásba, és áttelepíti az alkalmazás adatbázisát egy felügyelt Azure SQL-példányra a Azure Database Migration Service használatával.
[11. cikk: Linux-alkalmazás újrabontása egy Azure-webalkalmazásban és Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | A contoso a Linux osTicket alkalmazást egy Azure-webalkalmazásba helyezi át több Azure-régióban, az Azure Traffic Manager használatával, a folyamatos teljesítés érdekében a GitHubral integráltan. A contoso áttelepíti az alkalmazás adatbázisát egy Azure Database for MySQL példányra. 
[12. cikk: újraTeam Foundation Server az Azure DevOps Services szolgáltatásban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | A contoso áttelepíti a helyszíni Team Foundation Server üzemelő példányát az Azure DevOps Services szolgáltatásba az Azure-ban.
[13. cikk: alkalmazás újraépítése az Azure-ban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | A contoso a SmartHotel-alkalmazást számos Azure-képesség és-szolgáltatás használatával újraépíti, beleértve a Azure App Service, az Azure Kubernetes Service (ak), az Azure Functions, az Azure Cognitive Services és a Azure Cosmos DB.
[14. cikk: Migrálás méretezése az Azure-ba](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Az áttelepítési kombinációk kipróbálása után a contoso arra készül, hogy az Azure-ba történő teljes áttelepítésre méretezhető legyen.



## <a name="next-steps"></a>További lépések

- [Ismerje meg a](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) felhő áttelepítését.
- Ismerje meg a más forgatókönyvek (forrás/cél párok) áttelepítési stratégiáit az [adatbázis áttelepítési útmutatójában](https://datamigration.microsoft.com/).
