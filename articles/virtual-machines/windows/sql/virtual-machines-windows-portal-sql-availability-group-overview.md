---
title: SQL Server rendelkezésre állási csoportok – Azure-beli virtuális gépek – áttekintés |} A Microsoft Docs
description: Ez a cikk bemutatja az SQL Server rendelkezésre állási csoportok Azure-beli virtuális gépeken.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861842"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>SQL Server Always On rendelkezésre állási csoportok az Azure-beli virtuális gépek bemutatása #

Ez a cikk bemutatja az SQL Server rendelkezésre állási csoportok az Azure Virtual machines szolgáltatásban. 

Always On rendelkezésre állási csoportok az Azure Virtual Machinesben Always On rendelkezésre állási csoportok a helyszíni hasonlóak. További információkért lásd: [Always On rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Az ábra a teljes SQL Server rendelkezésre állási csoportot az Azure Virtual machines gépeken részeit.

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A fő rendelkezésre állási csoporthoz az Azure Virtual machines gépeken különbség az, hogy szükséges-e az Azure-beli virtuális gépek egy [terheléselosztó](../../../load-balancer/load-balancer-overview.md). A terheléselosztó IP-címek esetében a rendelkezésre állási csoport figyelőjének tárolja. Ha egynél több rendelkezésre állási csoport minden egyes van szüksége egy figyelőt. Egy terheléselosztó több kérésfigyelőt is támogatja.

Emellett az Azure IaaS virtuális gépek Vendég feladatátvevő fürtön, javasoljuk, hogy egyik hálózati Adapterre (fürtcsomópont) kiszolgálónként és a egy önálló alhálózati. Az Azure-hálózatok rendelkezik fizikai redundanciát, így további hálózati adapterek és alhálózatok a szükségtelen az Azure IaaS virtuális gépek Vendég-fürtön. A fürt ellenőrzési jelentésében figyelmeztetést ad, hogy a csomópontok használata csak egyetlen hálózaton elérhető legyen, bár ez a figyelmeztetés figyelmen kívül hagyhatja biztonságosan Azure IaaS virtuális gépek Vendég feladatátvevő fürtökön. 

|  | Windows Server-verzió | SQL Server-verzió | SQL Server Edition | WSFC Quorum Config | A többrégiós DR | Több alhálózatos támogatása | Egy meglévő AD támogatása | DR-többzónás ugyanabban a régióban | Nincs Active Directory-tartománynak dist – rendelkezésre állási csoport által támogatott | Nincs a fürt dist – rendelkezésre állási csoport által támogatott |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [AZ SQL VIRTUÁLIS GÉP CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | – Kiszolgálótelepítés megadása | Felhőbeli tanúsító | Nem | Igen | Igen | Igen | Nem | Nem |
| [Gyorsindítási sablonok](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | – Kiszolgálótelepítés megadása | Felhőbeli tanúsító | Nem | Igen | Igen | Igen | Nem | Nem |
| [Portálsablonja](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | – Kiszolgálótelepítés megadása | Fájlmegosztás | Nem | Nem | Nem | Nem | Nem | Nem |
| [Manuális](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Összes | Összes | Összes | Összes | Igen | Igen | Igen | Igen | Igen | Igen |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Ha készen áll egy SQL Server rendelkezésre állási csoport létrehozása az Azure Virtual machines szolgáltatásban, tekintse meg ezekben az oktatóanyagokban.

## <a name="manually-with-azure-cli"></a>Manuálisan az Azure CLI-vel
A javasolt megoldás Azure CLI használatával konfigurálhatja és telepítheti a rendelkezésre állási csoport, mivel a legjobb egyszerű és gyors üzembe helyezés tekintetében. Az Azure CLI-vel, SQL Server virtuális gépek csatlakoztatása a fürtöt, a Windows feladatátvevő fürt létrehozását, valamint a figyelőt és a belső terheléselosztó létrehozása az összes elérhető 30 percen belül. Ez a beállítás továbbra is a rendelkezésre állási csoport manuális létrehozását igényli, de automatizálja a további szükséges konfigurációs lépések. 

További információkért lásd: [használata Azure SQL virtuális gép CLI Azure virtuális Gépeken futó SQL Server AlwaysOn rendelkezésre állási csoport konfigurálása](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatikusan az Azure-Gyorssablonok
Az Azure gyorsindítási sablonok üzembe helyezése a Windows feladatátvevő fürt, az SQL Server virtuális gépek csatlakoztatása, hozza létre a figyelőt és a belső Load Balancer konfigurálása az SQL virtuális gép erőforrás-szolgáltató használatára. Ez a beállítás továbbra is egy manuális létrehozását, a rendelkezésre állási csoport, és a belső Load Balancer (ILB) igényel, de automatizálja a, és egyszerűbbé teszi az egyéb szükséges konfigurációs lépéseinek (beleértve az ILB konfigurációja). 

További információkért lásd: [használata az Azure gyorsindítási sablon egy Azure virtuális Gépen futó SQL Server AlwaysOn rendelkezésre állási csoport konfigurálása](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatikusan az Azure Portal-sablon

[Always On rendelkezésre állási csoport konfigurálása Azure-beli virtuális gépen automatikusan – Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manuálisan az Azure Portalon

Is létrehozhat a virtuális gépeket saját maga a sablon nélkül. Első lépésként teljesítse az előfeltételeket, majd hozza létre a rendelkezésre állási csoportot. A következő témakörökben: 

- [SQL Server Always On rendelkezésre állási csoportokra vonatkozó Előfeltételek konfigurálása az Azure Virtual machines szolgáltatásban](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Hozzon létre Always On rendelkezésre állási csoport javíthatja a rendelkezésre állás és vészhelyreállítás](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>További lépések

[Egy SQL Server Always On rendelkezésre állási csoport különböző régiókban lévő Azure virtuális gépeken futó konfigurálása](virtual-machines-windows-portal-sql-availability-group-dr.md)
