---
title: Kezelheti az SQL Server virtuális gépek az Azure-ban az Azure portal használatával |} A Microsoft Docs
description: Ismerje meg, hogyan érhetik el az SQL virtuális gép típusú erőforrást az Azure Portalon az Azure-ban üzemeltetett SQL Server virtuális gép.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59a85e855c9fab9f2a3437c83c867b8076f55049
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607210"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Az Azure portal segítségével Azure-beli SQL Server virtuális gépek kezelése

Az SQL Server virtuális gép Azure-beli kezeléséhez új hozzáférési pont a [az Azure portal](https://portal.azure.com). 

A **SQL virtuális gépek** erőforrás már egy független szolgáltatást, amely lehetővé teszi, hogy az SQL Server virtuális gépek mindegyike egyszerre megtekintheti és módosíthatja a dedikált SQL Server beállításai: 

![Az SQL virtuális gépek erőforrás](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Megjegyzések

- A **SQL virtuális gépek** erőforrás megtekintése és kezelése az SQL Server virtuális gépek javasolt módja. Azonban, jelenleg a **SQL virtuális gépek** erőforrás nem támogatja az [támogatásuk (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server rendszerű virtuális gépekhez. Az EOS SQL Server rendszerű virtuális gépek beállításainak kezeléséhez használja az elavult [SQL Server-konfiguráció lapon](#access-sql-server-configuration-tab) helyette. 
- A **SQL virtuális gépek** erőforrás csak érhető el az SQL Server virtuális gépek, amelyeken [az SQL virtuális gép erőforrás-szolgáltató regisztrálva](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-sql-virtual-machine-resource"></a>Hozzáférés az SQL virtuális gép típusú erőforrást
Az SQL virtuális gépek erőforrás elérésére, tegye a következőket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). 
1. Válassza ki **minden szolgáltatás**. 
1. Típus `SQL virtual machines` kifejezést a keresőmezőbe.
1. (Nem kötelező): Válassza ki a csillag melletti **SQL virtuális gépek** , ez a beállítás felvétele a Kedvencek menübe. 
1. Válassza ki **SQL virtuális gépek**. 

   ![SQL virtuális gép virtuális gépek összes szolgáltatás megkeresése](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Ez felsorolja az összes SQL Server virtuális gépen elérhető az előfizetésen belül. Válassza ki a szeretné elindítani kezelése a **SQL virtuális gépek** erőforrás. Ha az SQL Server virtuális gép nem látható azonnal, használja a keresőmezőt. 

![Az összes elérhető SQL virtuális gépek](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

Az SQL Server virtuális gép kiválasztásával megnyílik az **SQL virtuális gépek** erőforrás: 


![Az SQL virtuális gépek erőforrás](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > A **SQL virtuális gépek** erőforrás van a dedikált SQL Server-beállítások. Válassza ki a virtuális gép nevét a **virtuális gép** mezőt, navigáljon a beállítások, amelyek az adott virtuális géphez, de nem kizárólagos, az SQL Serverhez. 

## <a name="access-sql-server-configuration-tab"></a>Hozzáférés az SQL Server konfigurálása lap
Az SQL Server-konfiguráció lapon elavult. Jelenleg az egyetlen módszer kezelésére [támogatásuk (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server virtuális gépek és az SQL Server virtuális gépek, amelyek nincsenek [az SQL virtuális gép erőforrás-szolgáltató regisztrálva](virtual-machines-windows-sql-register-with-resource-provider.md).

Keresse meg a kell elérni az SQL-kiszolgáló elavult konfiguráció lapon, a **virtuális gépek** erőforrás. Ehhez tegye a következőket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). 
1. Válassza ki **minden szolgáltatás**. 
1. Típus `virtual machines` kifejezést a keresőmezőbe.
1. (Nem kötelező): Válassza ki a csillag melletti **virtuális gépek** , ez a beállítás felvétele a Kedvencek menübe. 
1. Válassza ki **virtuális gépek**. 

   ![Virtuális gépek keresése](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Ez felsorolja az előfizetésben található összes virtuális gépet. Válassza ki a szeretné elindítani kezelése a **virtuális gép** erőforrás. Ha az SQL Server virtuális gép nem látható azonnal, használja a keresőmezőt. 
1. Válassza ki **SQL Server-konfiguráció** a a **beállítások** ablaktábla az SQL Server kezeléséhez. 

![SQL Server-konfiguráció](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server használata a Windows virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Az SQL Server használata a Windows virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


