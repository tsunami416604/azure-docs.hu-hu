---
title: SQL Server rendelkezésre állási csoportok – Azure-beli virtuális gépek – áttekintés |} A Microsoft Docs
description: Ez a cikk bemutatja az SQL Server rendelkezésre állási csoportok Azure-beli virtuális gépeken.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
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
ms.openlocfilehash: 2c0c8b49d3f7122b71a08bc57ac615ba9e0c0108
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239440"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>SQL Server Always On rendelkezésre állási csoportok az Azure-beli virtuális gépek bemutatása #

Ez a cikk bemutatja az SQL Server rendelkezésre állási csoportok az Azure Virtual machines szolgáltatásban. 

Always On rendelkezésre állási csoportok az Azure Virtual Machinesben Always On rendelkezésre állási csoportok a helyszíni hasonlóak. További információkért lásd: [Always On rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Az ábra a teljes SQL Server rendelkezésre állási csoportot az Azure Virtual machines gépeken részeit.

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A fő rendelkezésre állási csoporthoz az Azure Virtual machines gépeken különbség az, hogy szükséges-e az Azure-beli virtuális gépek egy [terheléselosztó](../../../load-balancer/load-balancer-overview.md). A terheléselosztó IP-címek esetében a rendelkezésre állási csoport figyelőjének tárolja. Ha egynél több rendelkezésre állási csoport minden egyes van szüksége egy figyelőt. Egy terheléselosztó több kérésfigyelőt is támogatja.

Ha készen áll egy SQL Server rendelkezésre állási csoport létrehozása az Azure Virtual machines szolgáltatásban, tekintse meg ezekben az oktatóanyagokban.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Automatikus létrehozása egy rendelkezésre állási csoport sablon alapján

[Always On rendelkezésre állási csoport konfigurálása Azure-beli virtuális gépen automatikusan – Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Rendelkezésre állási csoport manuális létrehozása az Azure Portalon

Is létrehozhat a virtuális gépeket saját maga a sablon nélkül. Első lépésként teljesítse az előfeltételeket, majd hozza létre a rendelkezésre állási csoportot. A következő témakörökben: 

- [SQL Server Always On rendelkezésre állási csoportokra vonatkozó Előfeltételek konfigurálása az Azure Virtual machines szolgáltatásban](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Hozzon létre Always On rendelkezésre állási csoport javíthatja a rendelkezésre állás és vészhelyreállítás](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>További lépések

[Egy SQL Server Always On rendelkezésre állási csoport különböző régiókban lévő Azure virtuális gépeken futó konfigurálása](virtual-machines-windows-portal-sql-availability-group-dr.md).
