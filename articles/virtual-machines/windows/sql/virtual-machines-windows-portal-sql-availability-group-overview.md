---
title: A rendelkezésre állási csoportok áttekintése
description: Ez a cikk bemutatja az SQL Server rendelkezésre állási csoportok az Azure virtuális gépeken.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
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
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037499"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Az SQL Server rendelkezésre állási csoportjainak bemutatása az Azure virtuális gépein

Ez a cikk bemutatja az SQL Server rendelkezésre állási csoportjait az Azure virtuális gépeken. 

Mindig a rendelkezésre állási csoportok az Azure virtuális gépeken hasonlóak a helyszíni Mindig rendelkezésre állási csoportokhoz. További információt a [Mindig rendelkezésre állási csoportok (SQL Server) című témakörben talál.](https://msdn.microsoft.com/library/hh510230.aspx) 

Az ábra egy teljes SQL Server-rendelkezésre állási csoport részeit mutatja be az Azure virtuális gépekben.

![Rendelkezésreállási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Az Azure virtuális gépek en elérhető ségi csoport számára a legfontosabb különbség az, hogy az Azure virtuális gépei [terheléselosztót](../../../load-balancer/load-balancer-overview.md)igényelnek. A terheléselosztó rendelkezik a rendelkezésre állási csoport figyelőjének IP-címeivel. Ha egynél több rendelkezésre állási csoport minden csoport igényel egy figyelőt. Egy terheléselosztó több figyelőt is támogathat.

Emellett egy Azure IaaS virtuális gép vendég feladatátvételi fürt, azt javasoljuk, hogy kiszolgálónként (fürtcsomópont) és egyetlen alhálózat onként egy hálózati adapter. Az Azure-hálózatok fizikai redundanciával rendelkeznek, így nincs szükség további hálózati adapterre és alhálózatra az Azure IaaS virtuális gépek vendégfürtjén. Bár a fürtellenőrzési jelentés figyelmeztetést küld, amely szerint a csomópontok csak egyetlen hálózaton érhetők el, ez a figyelmeztetés nyugodtan figyelmen kívül hagyható az Azure IaaS virtuális gépek vendég feladatátvevő fürtjein. 

A redundancia és a magas rendelkezésre állás növelése érdekében az SQL Server virtuális gépei nek vagy ugyanabban a [rendelkezésre állási csoportban,](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)vagy különböző [rendelkezésre állási zónákban](/azure/availability-zones/az-overview)kell lenniük. 

|  | Windows Server verzió | SQL Server verzió | SQL Server Edition | WSFC Kvórum konfiguráció | DR többrégiós | Többalhálózat támogatása | Meglévő AD támogatása | DR többzónás azonos régióval | Dist-AG támogatás AD-tartomány nélkül | Dist-AG támogatás fürt nélkül |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL virtuális gép CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Felhőbeli tanúsító | Nem | Igen | Igen | Igen | Nem | Nem |
| [Gyorsútmutató sablonok](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Felhőbeli tanúsító | Nem | Igen | Igen | Igen | Nem | Nem |
| [Portálsablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Fájlmegosztás | Nem | Nem | Nem | Nem | Nem | Nem |
| [Kézi](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Összes | Összes | Összes | Összes | Igen | Igen | Igen | Igen | Igen | Igen |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Ha készen áll egy SQL Server-rendelkezésre állási csoport létrehozására az Azure virtuális gépeken, tekintse meg ezeket az oktatóanyagokat.

## <a name="manually-with-azure-cli"></a>Manuálisan az Azure CLI-vel
Az Azure CLI használata egy rendelkezésre állási csoport konfigurálásához és üzembe helyezéséhez ajánlott megoldás, mivel ez a legjobb az egyszerűség és az üzembe helyezés sebessége szempontjából. Az Azure CLI-vel a Windows feladatátvevő fürt létrehozása, az SQL Server virtuális gépek fürthöz való csatlakozása, valamint a figyelő és a belső terheléselosztó létrehozása 30 perc alatt érhető el. Ez a beállítás továbbra is megköveteli a rendelkezésre állási csoport manuális létrehozását, de automatizálja az összes többi szükséges konfigurációs lépést. 

További információ: [Az Azure SQL VM CLI használata az SQL Server mindig rendelkezésre állási csoportjának konfigurálásához egy Azure virtuális gépen című témakörben.](virtual-machines-windows-sql-availability-group-cli.md) 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatikusan az Azure gyorsindítási sablonokkal
Az Azure gyorsindítási sablonjai az SQL VM erőforrás-szolgáltatót használják a Windows feladatátvevő fürt telepítéséhez, az SQL Server virtuális gépekhez való csatlakozáshoz, a figyelő létrehozásához és a belső terheléselosztó konfigurálásához. Ez a beállítás továbbra is megköveteli a rendelkezésre állási csoport manuális létrehozását, és a belső terheléselosztó (ILB), de automatizálja és egyszerűsíti az összes többi szükséges konfigurációs lépéseket (beleértve az ILB konfigurációját). 

További információ: [Az Azure gyorsindítási sablon használata az SQL Server mindig rendelkezésre állási csoportjának konfigurálásához Azure-beli virtuális gépen.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


## <a name="automatically-with-an-azure-portal-template"></a>Automatikusan egy Azure Portal-sablonnal

[A Mindig rendelkezésre állási csoport konfigurálása az Azure Virtuális gépben automatikusan – Erőforrás-kezelő](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manuálisan az Azure Portalon

A virtuális gépeket saját maga is létrehozhatja a sablon nélkül. Először töltse ki az előfeltételeket, majd hozza létre a rendelkezésre állási csoportot. Lásd a következő témaköröket: 

- [Az SQL Server Always On rendelkezésre állási csoportjainak előfeltételeinek konfigurálása az Azure virtuális gépeken](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Mindig rendelkezésre állási csoport létrehozása a rendelkezésre állás és a vészhelyreállítás javítása érdekében](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>További lépések

[SQL Server mindig rendelkezésre állási csoport konfigurálása azure-beli virtuális gépeken különböző régiókban](virtual-machines-windows-portal-sql-availability-group-dr.md)
