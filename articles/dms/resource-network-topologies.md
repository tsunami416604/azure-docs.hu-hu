---
title: "Hálózati topológia az Azure SQL DB felügyelt példány áttelepítéshez az Azure-adatbázis áttelepítése szolgáltatással |} Microsoft Docs"
description: "Ismerje meg, hogy a forrás és cél konfigurációk adatbázis áttelepítési szolgáltatás."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/06/2018
ms.openlocfilehash: 892cff02b5b70f09236bb37ae786f180ddca9316
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Az Azure SQL DB felügyelt példány áttelepítéshez az Azure-adatbázis áttelepítése szolgáltatással hálózati topológiák
Ebből a cikkből megtudhatja, hogyan különböző hálózati topológiákat, hogy Azure adatbázis áttelepítési szolgáltatás együttműködhet az áttelepítési zökkenőmentes élményt biztosíthat az Azure SQL Database felügyelt-példányt a helyszíni SQL Server-kiszolgálók.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Az Azure SQL adatbázis felügyelt példány konfigurálva hibrid munkaterhelések 
Ez a topológia akkor használja, ha az Azure SQL adatbázis-felügyelt példány a helyi hálózathoz csatlakozik. Ezt a módszert biztosít a leggyakrabban egyszerűsített hálózati, és adatok legnagyobb átviteli sebességet eredményez az áttelepítés során.

![A hibrid munkaterhelések hálózati topológia](media\resource-network-topologies\hybrid-workloads.png)

**Követelmények**
- Ebben a forgatókönyvben az Azure SQL adatbázis-felügyelt példány és az Azure adatbázis áttelepítési szolgáltatáspéldány ugyanazon Azure virtuális jönnek létre, de különböző alhálózatokon használnak.  
- Az ebben a forgatókönyvben használt VNET is a helyszíni hálózathoz csatlakozik ExpressRoute vagy VPN használatával.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Az Azure SQL adatbázis felügyelt példány különítve a helyszíni hálózat
A hálózati topológia használja, ha a környezetében szükséges legalább egy, a következő esetekben:
- Az Azure SQL adatbázis-felügyelt példány el különítve a helyi kapcsolat, de az Azure adatbázis áttelepítési szolgáltatáspéldány a helyszíni hálózathoz csatlakozik.
- Szerepkör alapú hozzáférés-vezérlés (RBAC) házirendek legyenek érvényben, és ugyanahhoz az előfizetéshez, amelyen az Azure SQL adatbázis felügyelt példány felhasználói hozzáférés korlátozására.
- Az Azure SQL Database felügyelt-példányt és Azure adatbázis áttelepítési szolgáltatás Vnetekhez különböző előfizetésekhez vannak.

![A helyszíni hálózat különítve felügyelt példány hálózati topológia](media\resource-network-topologies\mi-isolated-workload.png)

**Követelmények**
- A VNET Azure adatbázis áttelepítési szolgáltatás által használt ebben a forgatókönyvben is kapcsolódnia kell a helyszíni hálózat ExpressRoute vagy VPN használatával.
- Hozzon létre egy virtuális hálózat hálózati társviszony-létesítés a VNETBEN használt Azure SQL adatbázis felügyelt példányhoz, és Azure adatbázis áttelepítési szolgáltatás között.


## <a name="cloud-to-cloud-migrations"></a>Felhő áttelepítések felhőbe
Ez a topológia használja, ha a forrás SQL Server Azure virtuális gépre.

![Hálózati topológia felhő felhő áttelepítések](media\resource-network-topologies\cloud-to-cloud.png)

**Követelmények**
- Hozzon létre egy virtuális hálózat hálózati társviszony-létesítés a VNETBEN használt Azure SQL adatbázis felügyelt példányhoz, és Azure adatbázis áttelepítési szolgáltatás között.

## <a name="see-also"></a>Lásd még:
- [Az Azure SQL adatbázis felügyelt példány SQL-kiszolgáló áttelepítése](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Az Azure-adatbázis áttelepítési szolgáltatás használatára vonatkozó előfeltételek áttekintése](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Virtuális hálózat létrehozása az Azure Portallal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>További lépések
Az Azure-adatbázis áttelepítése szolgáltatás és a nyilvános előzetes regionális rendelkezésre állási áttekintését lásd: a cikk [Mi az az Azure adatbázis áttelepítési szolgáltatás előzetes](dms-overview.md). 