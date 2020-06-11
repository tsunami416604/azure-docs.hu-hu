---
title: A SQL Server always on rendelkezésre állási csoportok áttekintése
description: Ez a cikk SQL Server always on rendelkezésre állási csoportokat mutatja be az Azure Virtual Machines-on.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f3c7009e5ecb43a809b9a3f703fc5ba289a2fd00
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669273"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>SQL Server always on rendelkezésre állási csoportok bemutatása az Azure-on Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk SQL Server rendelkezésre állási csoportokat mutatja be az Azure Virtual Machines-on. 

Az Azure Virtual Machines always on rendelkezésre állási csoportok hasonlók a helyszíni always on rendelkezésre állási csoportokhoz. További információ: [Always On rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Az alábbi ábra az Azure Virtual Machines teljes SQL Server rendelkezésre állási csoportjának részeit mutatja be.

![Rendelkezésreállási csoport](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Az Azure-beli rendelkezésre állási csoport fő különbsége, hogy ezek a virtuális gépek (VM-EK) [terheléselosztó](../../../load-balancer/load-balancer-overview.md)szükségesek a Virtual machines. A terheléselosztó tárolja a rendelkezésre állási csoport figyelő IP-címeit. Ha egynél több rendelkezésre állási csoporttal rendelkezik, a csoportoknak figyelőre van szükségük. Egy terheléselosztó több figyelőt is támogat.

Emellett egy Azure IaaS VM vendég feladatátvevő fürtön egyetlen hálózati ADAPTERt (fürtcsomópont) és egyetlen alhálózatot is ajánlunk. Az Azure-hálózatkezelés fizikai redundanciával rendelkezik, ami felesleges hálózati adaptereket és alhálózatokat tesz lehetővé az Azure IaaS VM-vendég fürtön. Bár a fürtellenőrzési jelentés figyelmeztetést küld, amely szerint a csomópontok csak egyetlen hálózaton érhetők el, ez a figyelmeztetés nyugodtan figyelmen kívül hagyható az Azure IaaS virtuális gépek vendég feladatátvevő fürtjein. 

A redundancia és a magas rendelkezésre állás növeléséhez a SQL Server virtuális gépeknek ugyanahhoz a [rendelkezésre állási csoporthoz](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets)vagy különböző [rendelkezésre állási zónákhoz](/azure/availability-zones/az-overview)kell tartoznia. 

|  | Windows Server-verzió | SQL Server verziója | SQL Server kiadás | WSFC kvórum konfigurációja | DR több régióval | Több alhálózatot támogató támogatás | Meglévő AD támogatása | DR többzónás azonos régióval | Dist-AG támogatás AD-tartomány nélkül | A dist-AG támogatása fürt nélkül |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VIRTUÁLIS GÉP PARANCSSORI FELÜLETE](availability-group-az-cli-configure.md) | 2016 | 2017 </br>2016   | ENT | Felhőbeli tanúsító | No | Igen | Igen | Igen | Nem | Nem |
| [Gyorsindítás sablonok](availability-group-quickstart-template-configure.md) | 2016 | 2017</br>2016  | ENT | Felhőbeli tanúsító | No | Igen | Igen | Igen | Nem | Nem |
| [Kézi](availability-group-manually-configure-prerequisites-tutorial.md) | Mind | Mind | Mind | Mind | Igen | Igen | Igen | Igen | Igen | Igen |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

A **SQL Server AlwaysOn-fürt (előzetes verzió)** sablon el lett távolítva az Azure Marketplace-ről, és már nem érhető el. 

Ha készen áll egy SQL Server rendelkezésre állási csoport létrehozására az Azure Virtual Machines-on, tekintse meg ezeket az oktatóanyagokat.

## <a name="manually-with-azure-cli"></a>Manuális az Azure CLI-vel

A rendelkezésre állási csoport konfigurálásához és üzembe helyezéséhez ajánlott az Azure CLI használata, mivel ez a legegyszerűbb és leggyorsabb üzembe helyezés. Az Azure CLI-vel a Windows feladatátvevő fürt létrehozása, SQL Server virtuális gépek csatlakoztatása a fürthöz, valamint a figyelő és a belső Load Balancer létrehozása 30 percen belül is elvégezhető. Ez a lehetőség továbbra is a rendelkezésre állási csoport manuális létrehozását igényli, de automatizálja az összes többi szükséges konfigurációs lépést. 

További információ: az Azure [SQL VM CLI használata az Always On rendelkezésre állási csoport konfigurálásához SQL Server Azure-beli virtuális gépen](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatikus az Azure Gyorsindítás sablonjaival

Az Azure rövid útmutató sablonjai az SQL virtuális gép erőforrás-szolgáltatóját használják a Windows feladatátvevő fürt üzembe helyezéséhez, a SQL Server virtuális gépekhez való csatlakozáshoz, a figyelő létrehozásához és a belső Load Balancer konfigurálásához. Ehhez a lehetőséghez továbbra is a rendelkezésre állási csoport és a belső Load Balancer (ILB) manuális létrehozása szükséges. Ez azonban automatizálja és leegyszerűsíti az összes többi szükséges konfigurációs lépést, beleértve a ILB konfigurációját is. 

További információt az Azure-beli virtuális gépeken [SQL Server az Always On rendelkezésre állási csoport konfigurálása az Azure gyorsindítási sablonnal](availability-group-quickstart-template-configure.md)című témakörben talál.


## <a name="automatically-with-an-azure-portal-template"></a>Automatikus Azure Portal sablonnal

[Az Always On rendelkezésre állási csoport konfigurálása az Azure-beli virtuális gépen automatikusan – Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>Manuálisan a Azure Portal

Saját maga is létrehozhatja a virtuális gépeket a sablon nélkül. Először hajtsa végre az előfeltételeket, majd hozza létre a rendelkezésre állási csoportot. Tekintse meg a következő témaköröket: 

- [SQL Server always on rendelkezésre állási csoportok előfeltételeinek konfigurálása az Azure-ban Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md)

- [Always On rendelkezésre állási csoport létrehozása a rendelkezésre állás és a vész-helyreállítás javítása érdekében](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Következő lépések

[SQL Server always on rendelkezésre állási csoport konfigurálása az Azure Virtual Machines különböző régiókban](availability-group-manually-configure-multiple-regions.md)
