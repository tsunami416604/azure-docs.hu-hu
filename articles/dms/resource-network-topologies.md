---
title: SQL felügyelt példányok áttelepítésének hálózati topológiái
titleSuffix: Azure Database Migration Service
description: A Azure Database Migration Service használatával megismerheti a Azure SQL Database felügyelt példányok áttelepítésének forrás-és célként megadott konfigurációit.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 9a313ea798519273ce57961544ec5b37c4d9c5ca
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749266"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Hálózati topológiák az Azure SQL DB felügyelt példányainak Azure Database Migration Service használatával történő áttelepítéséhez

Ez a cikk ismerteti a különböző hálózati topológiákat, amelyekkel a Azure Database Migration Service együttműködve biztosíthatja a helyszíni SQL Server-kiszolgálók átfogó áttelepítési élményét Azure SQL Database felügyelt példányra.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database felügyelt példány hibrid számítási feladatokhoz konfigurálva 

Akkor használja ezt a topológiát, ha a Azure SQL Database felügyelt példánya a helyszíni hálózathoz csatlakozik. Ez a megközelítés biztosítja a legtöbb egyszerűsített hálózati útválasztást, és az áttelepítés során maximális adatátviteli sebességet eredményez.

![A hibrid számítási feladatok hálózati topológiája](media/resource-network-topologies/hybrid-workloads.png)

**Követelmények**

- Ebben az esetben a Azure SQL Database felügyelt példány és a Azure Database Migration Service példány ugyanabban a Microsoft Azure Virtual Network jön létre, de különböző alhálózatokat használ.  
- Az ebben a forgatókönyvben használt virtuális hálózat a helyszíni hálózathoz is csatlakozik a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>A helyszíni hálózatról elkülönített felügyelt példány Azure SQL Database

Akkor használja ezt a hálózati topológiát, ha a környezete a következő esetekben legalább egyet igényel:

- A Azure SQL Database felügyelt példány el van különítve a helyszíni kapcsolattal, de a Azure Database Migration Service-példány a helyszíni hálózathoz csatlakozik.
- Ha a szerepköralapú Access Control (RBAC) házirendek vannak érvényben, és korlátozni kell a felhasználókat, hogy hozzáférjenek ugyanahhoz az előfizetéshez, amely a Azure SQL Database felügyelt példányt üzemelteti.
- A Azure SQL Database felügyelt példányhoz és Azure Database Migration Servicehoz használt virtuális hálózatok különböző előfizetésekben találhatók.

![A helyszíni hálózatról elkülönített felügyelt példány hálózati topológiája](media/resource-network-topologies/mi-isolated-workload.png)

**Követelmények**

- Az ehhez a forgatókönyvhöz Azure Database Migration Service által használt virtuális hálózatot a (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával is csatlakoztatni kell a helyszíni hálózathoz.
- A [VNet hálózati](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) társítás beállítása a Azure SQL Database felügyelt példányhoz és Azure Database Migration Servicehoz használt virtuális hálózat között.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Felhőből felhőbe történő Migrálás: megosztott virtuális hálózat

Akkor használja ezt a topológiát, ha a forrás SQL Server egy Azure-beli virtuális GÉPEN fut, és ugyanazokkal a virtuális hálózattal rendelkezik, Azure SQL Database felügyelt példánnyal és Azure Database Migration Service.

![A felhőből a felhőbe irányuló Migrálás hálózati topológiája megosztott VNet](media/resource-network-topologies/cloud-to-cloud.png)

**Követelmények**

- Nincsenek további követelmények.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Felhőbeli Migrálás: elkülönített virtuális hálózat

Akkor használja ezt a hálózati topológiát, ha a környezete a következő esetekben legalább egyet igényel:

- A Azure SQL Database felügyelt példány egy elkülönített virtuális hálózatban van kiépítve.
- Ha a szerepköralapú Access Control (RBAC) házirendek vannak érvényben, és korlátozni kell a felhasználókat, hogy hozzáférjenek ugyanahhoz az előfizetéshez, amely a Azure SQL Database felügyelt példányt üzemelteti.
- Azure SQL Database felügyelt példányhoz és Azure Database Migration Servicehoz használt virtuális hálózatok különböző előfizetésekben találhatók.

![A felhőből a felhőbe irányuló Migrálás hálózati topológiája elkülönített VNet](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Követelmények**

- A [VNet hálózati](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) társítás beállítása a Azure SQL Database felügyelt példányhoz és Azure Database Migration Servicehoz használt virtuális hálózat között.

## <a name="inbound-security-rules"></a>Bejövő biztonsági szabály

| **név**   | **PORT** | **PROTOKOLL** | **FORRÁS** | **CÉL** | **MŰVELET** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Bármelyik      | Bármelyik          | DMS-ALHÁLÓZAT | Bármelyik             | Engedélyezés      |

## <a name="outbound-security-rules"></a>Kimenő biztonsági szabályok

| **név**                  | **PORT**                                              | **PROTOKOLL** | **FORRÁS** | **CÉL**           | **MŰVELET** | **Szabály oka**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| felügyelet                | 443, 9354                                              | TCP          | Bármelyik        | Bármelyik                       | Engedélyezés      | A felügyeleti sík Service Bus és az Azure Blob Storage szolgáltatáson keresztül kommunikál. <br/>(Ha a Microsoft-társak engedélyezve vannak, előfordulhat, hogy nincs szüksége erre a szabályra.)                                                             |
| Diagnosztika               | 12000                                                 | TCP          | Bármelyik        | Bármelyik                       | Engedélyezés      | A DMS ezt a szabályt használja a diagnosztikai adatok gyűjtéséhez hibaelhárítási célból.                                                                                                                      |
| SQL-forráskiszolgáló         | 1433 (vagy TCP IP-port, amelyet a SQL Server figyel) | TCP          | Bármelyik        | A helyszíni címtér | Engedélyezés      | SQL Serveri forrás kapcsolódás a DMS-ből <br/>(Ha helyek közötti kapcsolattal rendelkezik, előfordulhat, hogy nincs szüksége erre a szabályra.)                                                                                       |
| Megnevezett példány SQL Server | 1434                                                  | UDP          | Bármelyik        | A helyszíni címtér | Engedélyezés      | SQL Server megnevezett példány forrásának kapcsolata a DMS-ből <br/>(Ha helyek közötti kapcsolattal rendelkezik, előfordulhat, hogy nincs szüksége erre a szabályra.)                                                                        |
| SMB-megosztás                 | 445                                                   | TCP          | Bármelyik        | A helyszíni címtér | Engedélyezés      | SMB hálózati megosztás a DMS-hez az adatbázis biztonsági mentési fájljainak áttelepítéséhez az Azure-beli virtuális gépen Azure SQL Database a MI és az SQL Server rendszerre <br/>(Ha helyek közötti kapcsolattal rendelkezik, előfordulhat, hogy nincs szüksége erre a szabályra). |
| DMS_subnet                | Bármelyik                                                   | Bármelyik          | Bármelyik        | DMS_Subnet                | Engedélyezés      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Lásd még:

- [SQL Server migrálása Azure SQL Database felügyelt példányra](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [A Azure Database Migration Service használatának előfeltételeinek áttekintése](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Virtuális hálózat létrehozása az Azure Portallal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Következő lépések

- A Azure Database Migration Service áttekintése: [Mi a Azure Database Migration Service?](dms-overview.md).
- A Azure Database Migration Service regionális elérhetőségével kapcsolatos aktuális információkért tekintse meg az [elérhető termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) lapon.
