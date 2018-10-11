---
title: Hálózati topológiák az Azure SQL Database felügyelt példányába áttelepítéshez az Azure Database Migration Service segítségével |} A Microsoft Docs
description: Ismerje meg, a Database Migration Service forrás- és konfigurációkat.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 39bcea36f3599530413aa9fc4dbb308ee2fb1681
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49066853"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Hálózati topológiák az Azure SQL DB felügyelt példányainak áttelepítése az Azure Database Migration Service segítségével
Ez a cikk ismerteti a különböző hálózati topológiákat, amely az Azure Database Migration Service, a helyszíni SQL Server-kiszolgálók teljes körű migrálási felhasználói élményt nyújtson az Azure SQL Database felügyelt példányain használható.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Az Azure SQL Database felügyelt példányain konfigurált hibrid számítási feladatokhoz 
Ez a topológia akkor használja, ha az Azure SQL Database felügyelt példánya a helyszíni hálózathoz csatlakozik. Ez a megközelítés a legtöbb egyszerűsített hálózati útválasztást biztosít, és adatok maximális átviteli sebességet eredményez az áttelepítés során.

![Hálózati topológia hibrid számítási feladatokhoz](media\resource-network-topologies\hybrid-workloads.png)

**Követelmények**
- Ebben a forgatókönyvben az Azure SQL Database felügyelt példányát és az Azure Database Migration Service-példány jönnek létre az azonos Azure virtuális hálózat, de különböző alhálózatokon használnak.  
- Ebben a forgatókönyvben használt virtuális hálózat is kapcsolódik a helyszíni hálózat használatával [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Az Azure SQL Database felügyelt példánya a helyszíni hálózatról elszigetelt
Akkor használja ezt a hálózati topológiát, ha környezete megköveteli egy vagy több, az alábbi esetekben:
- Az Azure SQL Database felügyelt példánya el különítve a helyszíni kapcsolat, de az Azure Database Migration Service-példány a helyszíni hálózathoz csatlakozik.
- Ha a szerepkör alapú hozzáférés-vezérlés (RBAC) házirendek legyenek érvényben, és korlátozza a felhasználók férhet hozzá ugyanahhoz az előfizetéshez kell, hogy az Azure SQL Database felügyelt példánya üzemelteti.
- Az Azure SQL Database felügyelt példánya a virtuális hálózatok használt, és az Azure Database Migration Service különböző előfizetésekhez tartoznak.

![Felügyelt példány a helyszíni hálózatról elszigetelt hálózati topológia](media\resource-network-topologies\mi-isolated-workload.png)

**Követelmények**
- A virtuális Hálózattal, Azure Database Migration Service használja az ebben a forgatókönyvben is kapcsolódnia kell a helyszíni hálózat használatával (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Állítsa be a [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure SQL Database felügyelt példánya és az Azure Database Migration Service használt virtuális hálózat között.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Felhő – felhőbeli migrálás: a megosztott virtuális hálózat

Ez a topológia akkor használja, ha a forrás SQL Server Azure virtuális gép üzemel, és ugyanazon a VNETEN osztja meg az Azure SQL Database felügyelt példányába, és az Azure Database Migration Service.

![A felhőbe irányuló felhő-migrálási parancsait a megosztott virtuális hálózat hálózati topológia](media\resource-network-topologies\cloud-to-cloud.png)

**Követelmények**
- Nincsenek további követelmények.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>A migrálás a felhőbe, felhő: virtuális hálózat elszigetelt

Akkor használja ezt a hálózati topológiát, ha környezete megköveteli egy vagy több, az alábbi esetekben:
- Az Azure SQL Database felügyelt példány egy elkülönített virtuális hálózaton van kiépítve.
- Ha a szerepkör alapú hozzáférés-vezérlés (RBAC) házirendek legyenek érvényben, és korlátozza a felhasználók férhet hozzá ugyanahhoz az előfizetéshez kell, hogy az Azure SQL Database felügyelt példánya üzemelteti.
- Az Azure SQL Database felügyelt példánya és az Azure Database Migration Service használt virtuális hálózatok különböző előfizetésekben találhatóak.

![A felhőbe irányuló felhő-migrálási parancsait a egy elkülönített virtuális hálózatok közötti hálózati topológia](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**Követelmények**
- Állítsa be a [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure SQL Database felügyelt példánya és az Azure Database Migration Service használt virtuális hálózat között.


## <a name="see-also"></a>Lásd még:
- [SQL Server migrálása az Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Az Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Virtuális hálózat létrehozása az Azure Portallal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>További lépések
Az Azure Database Migration Service és a régiónkénti rendelkezésre állás, a nyilvános előzetes verzióban áttekintését lásd: a cikk [Mi az az Azure-adatbázis áttelepítése Service előzetes](dms-overview.md). 