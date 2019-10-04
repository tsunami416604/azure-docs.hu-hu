---
title: Hálózati topológiák az Azure Database Migration Service segítségével az Azure SQL Database felügyelt példányain áttelepítéshez |} A Microsoft Docs
description: Ismerje meg, hogy a forrás- és konfigurációkat, Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/07/2019
ms.openlocfilehash: 74613599903f7cde606295a1e2d9eaaa0924cf50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808424"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Hálózati topológiák az Azure SQL DB felügyelt példányainak áttelepítése az Azure Database Migration Service segítségével

Ez a cikk ismerteti a különböző hálózati topológiákat, Azure Database Migration Service a teljes körű migrálási felhasználói élményt nyújtson az Azure SQL Database felügyelt példánya a helyszíni SQL Server-kiszolgálók is együttműködik.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Az Azure SQL Database felügyelt példányain konfigurált hibrid számítási feladatokhoz 

Ez a topológia akkor használja, ha az Azure SQL Database felügyelt példánya a helyszíni hálózathoz csatlakozik. Ez a megközelítés a legtöbb egyszerűsített hálózati útválasztást biztosít, és adatok maximális átviteli sebességet eredményez az áttelepítés során.

![Hálózati topológia hibrid számítási feladatokhoz](media/resource-network-topologies/hybrid-workloads.png)

**Követelmények**

- Ebben a forgatókönyvben az Azure SQL Database felügyelt példány és az Azure Database Migration Service-példány jönnek létre az azonos Azure virtuális hálózat, de különböző alhálózatokon használnak.  
- Ebben a forgatókönyvben használt virtuális hálózat is kapcsolódik a helyszíni hálózat használatával [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Az Azure SQL Database felügyelt példánya a helyszíni hálózatról elszigetelt

Akkor használja ezt a hálózati topológiát, ha környezete megköveteli egy vagy több, az alábbi esetekben:

- Az Azure SQL Database felügyelt példány el különítve a helyszíni kapcsolat, de az Azure Database Migration Service-példány a helyszíni hálózathoz csatlakozik.
- Ha a szerepkör alapú hozzáférés-vezérlés (RBAC) házirendek legyenek érvényben, és ugyanahhoz az előfizetéshez, amelyen az Azure SQL Database felügyelt példány elérése a felhasználók korlátozása kell.
- Az Azure SQL Database felügyelt példányába, valamint az Azure Database Migration Service szolgáló virtuális hálózatok különböző előfizetésekben találhatóak.

![Felügyelt példány a helyszíni hálózatról elszigetelt hálózati topológia](media/resource-network-topologies/mi-isolated-workload.png)

**Követelmények**

- A virtuális hálózattal, Azure Database Migration Service használja az ebben a forgatókönyvben is kapcsolódnia kell a helyszíni hálózat használatával (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Állítsa be a [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) között a virtuális hálózathoz, használja az Azure SQL Database felügyelt példány és az Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Felhő – felhőbeli migrálás: a megosztott virtuális hálózat

Ez a topológia akkor használja, ha a forrás SQL Server Azure virtuális gép üzemel, és ugyanazon a VNETEN osztja meg az Azure SQL Database felügyelt példány és az Azure Database Migration Service.

![Hálózati topológia a felhőbe irányuló felhőalapú áttelepítések megosztott virtuális hálózathoz](media/resource-network-topologies/cloud-to-cloud.png)

**Követelmények**

- Nincsenek további követelmények.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>A migrálás a felhőbe, felhő: virtuális hálózat elszigetelt

Akkor használja ezt a hálózati topológiát, ha környezete megköveteli egy vagy több, az alábbi esetekben:

- Az Azure SQL Database felügyelt példány egy elkülönített virtuális hálózaton van kiépítve.
- Ha a szerepkör alapú hozzáférés-vezérlés (RBAC) házirendek legyenek érvényben, és ugyanahhoz az előfizetéshez, amelyen az Azure SQL Database felügyelt példány elérése a felhasználók korlátozása kell.
- Az Azure SQL Database felügyelt példánya és az Azure Database Migration Service használt virtuális hálózatok különböző előfizetésekben találhatóak.

![Hálózati topológia a felhőbe irányuló felhőalapú áttelepítések egy elkülönített virtuális hálózattal](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Követelmények**

- Állítsa be a [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) között a virtuális hálózathoz, használja az Azure SQL Database felügyelt példány és az Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Bejövő biztonsági szabály

| **NAME**   | **PORT** | **PROTOCOL** | **FORRÁS** | **CÉL** | **A MŰVELET** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Bármely      | Bármely          | A DMS-ALHÁLÓZAT | Bármely             | Engedélyezés      |

## <a name="outbound-security-rules"></a>Kimenő biztonsági szabályok

| **NAME**                  | **PORT**                                              | **PROTOCOL** | **FORRÁS** | **CÉL**           | **A MŰVELET** | **A szabály oka**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Felügyeleti                | 443,9354                                              | TCP          | Bármely        | Bármely                       | Engedélyezés      | Felügyeleti sík kommunikáció a service bus és az Azure blob storage-bA. <br/>(Ha a Microsoft társviszony-létesítés engedélyezve van, előfordulhat, hogy nem kell Ez a szabály.)                                                             |
| Diagnosztika               | 12000                                                 | TCP          | Bármely        | Bármely                       | Engedélyezés      | A DMS Ez a szabály hibaelhárítási célokra diagnosztikai információkat gyűjthet használ.                                                                                                                      |
| SQL-forráskiszolgáló         | 1433-as (vagy, mely a SQL Server TCP IP-port) | TCP          | Bármely        | A helyszíni címtér | Engedélyezés      | Az SQL Server adatforrás kapcsolat DMS <br/>(Ha helyek közötti kapcsolat, akkor előfordulhat, hogy nem szükséges ez a szabály.)                                                                                       |
| Az SQL Server-példányt | 1434                                                  | UDP          | Bármely        | A helyszíni címtér | Engedélyezés      | SQL Server megnevezett példány DMS forrás kapcsolat <br/>(Ha helyek közötti kapcsolat, akkor előfordulhat, hogy nem szükséges ez a szabály.)                                                                        |
| SMB-megosztás                 | 445                                                   | TCP          | Bármely        | A helyszíni címtér | Engedélyezés      | Az SMB hálózati megoszthatja a DMS-adatbázis áttelepítése az Azure SQL Database MI és az SQL Server-kiszolgálók biztonsági mentési fájljait tárolja az Azure virtuális gépen <br/>(Ha helyek közötti kapcsolat, nincs szükség lehet ez a szabály). |
| DMS_subnet                | Bármely                                                   | Bármely          | Bármely        | DMS_Subnet                | Engedélyezés      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Lásd még

- [SQL Server migrálása az Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Virtuális hálózat létrehozása az Azure Portallal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>További lépések

- Azure Database Migration Service áttekintését lásd: a cikk [Mi az Azure Database Migration Service?](dms-overview.md).
- Azure Database Migration Service régiónkénti rendelkezésre állás kapcsolatos aktuális információkért tekintse meg a [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) lapot.
