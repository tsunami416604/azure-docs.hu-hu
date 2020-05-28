---
title: SQL Server virtuális gépek kezelése az Azure-ban a Azure Portal használatával | Microsoft Docs
description: Megtudhatja, hogyan érheti el az SQL-alapú virtuális gép erőforrását az Azure-ban üzemeltetett SQL Server VM Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6cf6fac84abd1d996c77aae7240c0322c5ec53fb
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049070"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>SQL Server virtuális gépek kezelése az Azure-ban a Azure Portal használatával
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A [Azure Portal](https://portal.azure.com)az **SQL Virtual Machines** erőforrás egy független felügyeleti szolgáltatás. Használhatja az SQL Server virtuális gépek egyidejű megtekintésére és a SQL Server dedikált beállítások módosítására: 

![SQL-alapú virtuális gépek erőforrása](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>Megjegyzések

- Javasoljuk, hogy az **SQL Virtual Machines** erőforrás használatával tekintse meg és kezelje a SQL Server virtuális gépeket az Azure-ban. Az **SQL Virtual Machines** erőforrás jelenleg azonban nem támogatja a SQL Server virtuális gépek [támogatásának](sql-server-2008-extend-end-of-support.md) felügyeletét. A támogatási SQL Server virtuális gépek beállításainak kezeléséhez használja helyette az elavult [SQL Server konfiguráció fület](#access-the-sql-server-configuration-tab) . 
- Az **SQL Virtual Machines** erőforrás csak olyan SQL Server virtuális gépek számára érhető el, amelyek [regisztrálva vannak az SQL VM erőforrás-szolgáltatónál](sql-vm-resource-provider-register.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Hozzáférés az SQL Virtual Machines erőforráshoz
Az SQL-alapú **virtuális gépek** erőforrásának eléréséhez tegye a következőket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). 
1. Válassza **a minden szolgáltatás**lehetőséget. 
1. A keresőmezőbe írja be az **SQL Virtual Machines** kifejezést.
1. (Nem kötelező): válassza ki az SQL-alapú **virtuális gépek** melletti csillagot, és adja hozzá ezt a lehetőséget a **Kedvencek** menühöz. 
1. Válassza az SQL-alapú **virtuális gépek**lehetőséget. 

   ![SQL Server virtuális gépek keresése az összes szolgáltatásban](./media/manage-sql-vm-portal/sql-vm-search.png)

1. A portálon az előfizetésen belül elérhető összes SQL Server virtuális gép szerepel. Válassza ki a felügyelni kívánt elemet az **SQL Virtual Machines** -erőforrás megnyitásához. Ha a SQL Server VM nem jelenik meg, használja a keresőmezőt. 

   ![Az összes rendelkezésre álló SQL Server virtuális gép](./media/manage-sql-vm-portal/all-sql-vms.png)

   A SQL Server VM kiválasztásával megnyílik az **SQL Virtual Machines** erőforrás: 


   ![SQL-alapú virtuális gépek erőforrása](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> Az **SQL-alapú virtuális gépek** erőforrása dedikált SQL Server beállításokra szolgál. Válassza ki a **virtuális gép nevét a virtuális gép** mezőben, hogy megnyissa a virtuális géphez tartozó beállításokat, de nem kizárólag SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>A SQL Server konfiguráció lap elérése
A **SQL Server konfiguráció** lap elavult. Jelenleg ez az egyetlen módszer a támogatási SQL Server virtuális gépek [támogatásához](sql-server-2008-extend-end-of-support.md) , és olyan virtuális gépek SQL Server, amelyek nem lettek [regisztrálva az SQL VM erőforrás-szolgáltatóban](sql-vm-resource-provider-register.md).

Az elavult **SQL Server konfiguráció** lap megnyitásához nyissa meg a **virtuális gépek** erőforrást. Ehhez a következő lépések szükségesek:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). 
1. Válassza **a minden szolgáltatás**lehetőséget. 
1. A keresőmezőbe írja be a **virtuális gépeket** .
1. (Nem kötelező): válassza ki a **virtuális gépek** melletti csillagot, és adja hozzá ezt a lehetőséget a **Kedvencek** menühöz. 
1. Válassza a **Virtuális gépek** lehetőséget. 

   ![Virtuális gépek keresése](./media/manage-sql-vm-portal/vm-search.png)

1. A portál felsorolja az előfizetésben található összes virtuális gépet. Válassza ki a felügyelni kívánt elemet a **virtuális gépek** erőforrásának megnyitásához. Ha a SQL Server VM nem jelenik meg, használja a keresőmezőt. 
1. A **Beállítások** ablaktáblán válassza a **SQL Server konfiguráció** lehetőséget a SQL Server VM kezeléséhez. 

   ![SQL Server-konfiguráció](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>További lépések

További információért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](doc-changes-updates-release-notes.md)


