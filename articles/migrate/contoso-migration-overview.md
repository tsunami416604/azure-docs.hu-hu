---
title: Contoso áttelepítési sorozat |} A Microsoft Docs
description: A migrálás stratégiájával és a forgatókönyvek az Azure-bA migrálhatja helyszíni adatközpontját a Contoso által használt áttekintést nyújt.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: raynew
ms.openlocfilehash: 048772edadca36a63870a2965c703ca7e6ec8c63
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729889"
---
# <a name="contoso-migration-series"></a>Contoso migrálási sorozat


Rendelkezünk egy cikksorozatot, amely azt ismerteti, hogyan a áttelepíti a Contoso nevű fiktív szervezet helyszíni infrastruktúrát a [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) felhő. 

A sorozat adatait és forgatókönyveit, amelyek bemutatják, hogyan infrastruktúra áttelepítését, és futtatjuk a különböző típusú migrálások tartalmazza. Forgatókönyvek egyre összetettebbé válnak, azok halad. A cikkek bemutatják, hogyan a Contoso vállalat befejezi a migrálási feladatát, de általános olvasásra és konkrét utasításokat mutatók egész rendelkeznek.

## <a name="migration-articles"></a>Migrálásról szóló cikksorozat

A sorozat a cikkek az alábbi táblázat foglalja össze.  

- Minden egyes áttelepítési forgatókönyv határozzák meg, amelyek meghatározzák az áttelepítési stratégia némileg eltérő üzleti céljainak megvalósítását.
- Egyes üzembe helyezési forgatókönyvekben biztosítunk a stratégiai és a célokat, javasolt architektúra, a lépéseket hajtsa végre az áttelepítést, és javaslatot karbantartása és a további lépésekről a migrálás befejezése után kapcsolatos információk.

**Cikk** | **Részletek** 
--- | --- 
[1. cikk: – Áttekintés](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | A cikk sorozat, a Contoso-áttelepítési stratégia és az adatsorozathoz használt mintaalkalmazások áttekintése. 
[2. cikk: Azure-infrastruktúra üzembe helyezése](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. 
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso fut, annak a helyszíni SmartHotel360 app VMware-en futó értékelését. Contoso értékeli az alkalmazás virtuális gépek az Azure Migrate szolgáltatás és a Data Migration Assistant szolgáltatást használó alkalmazás SQL Server-adatbázis használatával.
[4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel360 alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése egy Azure SQL Database felügyelt példánya a a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[5. cikk: Az Azure virtuális gépein egy alkalmazás újratárolása](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso Azure virtuális gépekre a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással. 
[6. cikk: Egy Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazás újratárolása](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso áttelepíti a SmartHotel360 alkalmazást. Contoso Site Recovery használatával az alkalmazás virtuális gépek áttelepítéséhez. A Database Migration Service használatával az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt. 
[7. cikk: Egy Azure-beli virtuális gépeken Linux alkalmazás újratárolása](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso az Azure virtuális gépek, a Site Recovery szolgáltatás használatával Linux osTicket alkalmazás lift-and-shift áttelepítés befejeződött.
[8. cikk: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure Database for MySQL-hez](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure virtuális gépek Site Recovery használatával. Az áttelepítése az alkalmazás-adatbázis az Azure Database for MySQL-hez a MySQL Workbench használatával. 
[9. cikk: Az Azure web App alkalmazásban és az Azure SQL Database alkalmazás újrabontása](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso a SmartHotel360 alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése egy Azure SQL Server-példány az a Database Migration Assistant szolgáltatást.     
[10. cikk: Refaktorovat egy Linux-alkalmazást egy Azure-webalkalmazást és az Azure Database for MySQL-hez](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso áttelepíti a Linux-osTicket alkalmazás egy Azure-webalkalmazást az Azure Traffic Managerrel, a folyamatos készregyártás a GitHub integrált több Azure-régióban található. Contoso áttelepíti az alkalmazás-adatbázis egy Azure Database for MySQL-példányt. 
[11. cikk: Refaktorovat a Team Foundation Serverrel az Azure DevOps-szolgáltatásokkal](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso áttelepíti a helyi Team Foundation Server központi Azure DevOps-szolgáltatásokkal az Azure-ban.
[12. cikk: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso annak SmartHotel app áttelepíti az Azure-bA. Ezután azt rearchitects az alkalmazás webes réteg az Azure Service Fabric és az adatbázis az Azure SQL Database-ban futó Windows tárolójaként. 
[13. cikk: Építse újra az alkalmazást az Azure-ban](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso újraépíti a SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service (AKS), az Azure Functions, Azure Cognitive Services és az Azure Cosmos DB használatával.  
[14. cikk: Áttelepítés az Azure-bA méretezése](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. 


    

## <a name="next-steps"></a>További lépések

[Ismerje meg](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migrálás a felhőbe. 

