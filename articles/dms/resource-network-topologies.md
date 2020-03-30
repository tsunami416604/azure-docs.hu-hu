---
title: Hálózati topológiák SQL által felügyelt példányáttelepítéshez
titleSuffix: Azure Database Migration Service
description: Ismerje meg az Azure SQL Database által felügyelt példányok áttelepítésének forrás- és célkonfigurációit az Azure Database Migration Service használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254960"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Az Azure SQL DB felügyelt példányok áttelepítéséhez használt hálózati topológiák az Azure Database Migration Service használatával

Ez a cikk ismerteti a különböző hálózati topológiák, hogy az Azure Database Migration Service együtt tud működni, hogy átfogó áttelepítési élményt a helyszíni SQL-kiszolgálók az Azure SQL Database felügyelt példány.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Hibrid számítási feladatokhoz konfigurált Azure SQL Database felügyelt példány 

Ezt a topológiát akkor használja, ha az Azure SQL Database felügyelt példánya a helyszíni hálózathoz csatlakozik. Ez a megközelítés biztosítja a legegyszerűbbebb hálózati útválasztást, és maximális adatátviteli csatornát biztosít az áttelepítés során.

![Hálózati topológia hibrid számítási feladatokhoz](media/resource-network-topologies/hybrid-workloads.png)

**Követelmények**

- Ebben a forgatókönyvben az Azure SQL Database felügyelt példánya és az Azure Database Migration Service példány ugyanabban a Microsoft Azure virtuális hálózaton jön létre, de különböző alhálózatokat használnak.  
- Az ebben a forgatókönyvben használt virtuális hálózat is csatlakozik a helyszíni hálózathoz [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>A helyszíni hálózattól elkülönített Azure SQL Database Database felügyelt példány

Akkor használja ezt a hálózati topológiát, ha a környezetben az alábbi esetek közül egyre van szükség:

- Az Azure SQL Database felügyelt példánya el van különítve a helyszíni kapcsolat, de az Azure Database Migration Service-példány csatlakozik a helyszíni hálózathoz.
- Ha a szerepköralapú hozzáférés-vezérlési (RBAC) szabályzatok vannak érvényben, és korlátoznia kell a felhasználók számára, hogy ugyanazt az előfizetést, amely az Azure SQL Database felügyelt példánya található.
- Az Azure SQL Database felügyelt példányés az Azure Database Migration Service használt virtuális hálózatok különböző előfizetések.

![A felügyelt példány hálózati topológiája a helyszíni hálózattól elkülönítve](media/resource-network-topologies/mi-isolated-workload.png)

**Követelmények**

- Az Azure Database Migration Service által ebben a forgatókönyvben használt virtuális hálózatot ishttps://docs.microsoft.com/azure/expressroute/expressroute-introduction) csatlakoznia kell a helyszíni hálózathoz a ( vagy [a VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával.
- Virtuális [hálózati társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) beállítása az Azure SQL Database felügyelt példányához és az Azure Database Migration Service-hez használt virtuális hálózat között.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Felhőből felhőbe való áttelepítés: Megosztott virtuális hálózat

Ezt a topológiát akkor használja, ha a forrás SQL Server egy Azure virtuális gépben található, és ugyanazt a virtuális hálózatot osztja meg az Azure SQL Database felügyelt példányával és az Azure Database Migration Service szolgáltatással.

![Hálózati topológia a felhőből a felhőbe irányuló áttelepítéshez megosztott virtuális hálózattal](media/resource-network-topologies/cloud-to-cloud.png)

**Követelmények**

- Nincsenek további követelmények.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Felhőből felhőbe való migráció: Elszigetelt virtuális hálózat

Akkor használja ezt a hálózati topológiát, ha a környezetben az alábbi esetek közül egyre van szükség:

- Az Azure SQL Database felügyelt példány a kiépített egy elszigetelt virtuális hálózatban.
- Ha a szerepköralapú hozzáférés-vezérlési (RBAC) szabályzatok vannak érvényben, és korlátoznia kell a felhasználók számára, hogy ugyanazt az előfizetést, amely az Azure SQL Database felügyelt példánya található.
- Az Azure SQL Database felügyelt példányés az Azure Database Migration Service használt virtuális hálózatok különböző előfizetésekben vannak.

![Hálózati topológia a felhőből a felhőbe irányuló áttelepítéshez egy elkülönített virtuális hálózattal](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Követelmények**

- Virtuális [hálózati társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) beállítása az Azure SQL Database felügyelt példányához és az Azure Database Migration Service-hez használt virtuális hálózat között.

## <a name="inbound-security-rules"></a>Bejövő biztonsági szabály

| **NÉV**   | **Port** | **Protokoll** | **Forrás** | **Cél** | **Akció** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Bármelyik      | Bármelyik          | DMS-alhálózat | Bármelyik             | Engedélyezés      |

## <a name="outbound-security-rules"></a>Kimenő biztonsági szabályok

| **NÉV**                  | **Port**                                              | **Protokoll** | **Forrás** | **Cél**           | **Akció** | **A szabály oka**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| felügyelet                | 443,9354                                              | TCP          | Bármelyik        | Bármelyik                       | Engedélyezés      | Felügyeleti sík kommunikáció a Service Bus és az Azure blob storage keresztül. <br/>(Ha a Microsoft társviszony-létesítés engedélyezve van, előfordulhat, hogy nincs szüksége erre a szabályra.)                                                             |
| Diagnosztika               | 12000                                                 | TCP          | Bármelyik        | Bármelyik                       | Engedélyezés      | A DMS ezt a szabályt használja diagnosztikai információk összegyűjtésére hibaelhárítási célokra.                                                                                                                      |
| SQL forráskiszolgáló         | 1433 (vagy tcp IP-port, amelyet az SQL Server figyel) | TCP          | Bármelyik        | A helyszíni címtér | Engedélyezés      | SQL Server-forráskapcsolat a DMS-ből <br/>(Ha helyek közötti kapcsolattal rendelkezik, előfordulhat, hogy nincs szüksége erre a szabályra.)                                                                                       |
| SQL Server nevű példány | 1434                                                  | UDP          | Bármelyik        | A helyszíni címtér | Engedélyezés      | SQL Server nevű példányforrás-kapcsolat a DMS-ből <br/>(Ha helyek közötti kapcsolattal rendelkezik, előfordulhat, hogy nincs szüksége erre a szabályra.)                                                                        |
| SMB-megosztás                 | 445                                                   | TCP          | Bármelyik        | A helyszíni címtér | Engedélyezés      | SMB-hálózati megosztás a DMS számára az Azure SQL Database MI-be és SQL-kiszolgálókra való áttéréshez szükséges adatbázis-biztonsági mentési fájlok tárolására az Azure VM-en <br/>(Ha helyek közötti kapcsolattal rendelkezik, előfordulhat, hogy nincs szüksége erre a szabályra). |
| DMS_subnet                | Bármelyik                                                   | Bármelyik          | Bármelyik        | DMS_Subnet                | Engedélyezés      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Lásd még

- [SQL Server áttelepítése az Azure SQL Database felügyelt példányába](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Az Azure Database Migration Service használatának előfeltételeinek áttekintése](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Virtuális hálózat létrehozása az Azure Portallal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>További lépések

- Az Azure Database Migration Service áttekintését a [Mi az Azure-adatbázis-áttelepítési szolgáltatás?](dms-overview.md)cikkben találja.
- Az Azure Database Migration Service regionális elérhetőségéről a [Termékek régiónként lapon](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) tájékozódási beli információkért tekintse meg.
