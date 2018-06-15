---
title: Az SQL Server rendelkezésre állási csoportok – az Azure virtuális gépek – áttekintés |} Microsoft Docs
description: Ez a cikk Azure virtuális gépeken futó SQL Server rendelkezésre állási csoportok be.
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
ms.openlocfilehash: 4a531fb87d9cd2743138ba7a027bdf0d132b9747
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29396431"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>SQL Server Always On rendelkezésre állási csoportok az Azure virtuális gépeken bemutatása #

Ez a cikk be az SQL Server rendelkezésre állási csoportok Azure virtuális gépeken. 

Always On rendelkezésre állási csoportok Azure virtuális gépeken Always On rendelkezésre állási csoportok a helyszíni hasonlóak. További információkért lásd: [Always On rendelkezésre állási csoportok (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

Az ábrán látható, a teljes SQL Server rendelkezésre állási csoport az Azure virtuális gépek részei.

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A kulcs egy rendelkezésre állási csoporthoz tartozó Azure virtuális gépek különbség, hogy szükséges-e az Azure virtuális gépek egy [terheléselosztó](../../../load-balancer/load-balancer-overview.md). A terheléselosztó a rendelkezésre állási csoport figyelője az IP-címek tartalmazza. Ha egynél több rendelkezésre állási csoport minden egyes csoport szükséges egy figyelő. Egy terheléselosztó több figyelői képes támogatni.

Ha készen áll a hozhat létre egy SQL Server rendelkezésre állási aroup Azure virtuális gépeken, tekintse meg ezek az oktatóanyagok.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Rendelkezésre állási csoport automatikusan létrehozása sablonból

[Always On rendelkezésre állási csoport konfigurálásához Azure virtuális gép automatikusan - erőforrás-kezelő](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Hozzon létre manuálisan egy rendelkezésre állási csoport Azure-portálon

Is létrehozhat a virtuális gépek saját kezűleg a sablon nélkül. Először végezze el az előfeltételeket, majd a rendelkezésre állási csoport létrehozására. A következő témakörökben: 

- [Előfeltételek az SQL Server Always On rendelkezésre állási csoportok konfigurálása Azure virtuális gépeken](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Hozzon létre mindig a rendelkezésre állási csoport rendelkezésre állás és vészhelyreállítás javítása érdekében](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>További lépések

[SQL-kiszolgáló mindig konfigurálása az Azure virtuális gépeken különböző régiókban rendelkezésre állási csoporton](virtual-machines-windows-portal-sql-availability-group-dr.md).
