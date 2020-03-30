---
title: SQL Server virtuális gépek kezelése az Azure-ban az Azure Portalhasználatával | Microsoft dokumentumok
description: Megtudhatja, hogyan érheti el az SQL virtuálisgép-erőforrást az Azure Portalon az Azure-ban üzemeltetett SQL Server virtuális géphez.
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
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243210"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>SQL Server virtuális gépek kezelése az Azure-ban az Azure Portal használatával

Az [Azure Portalon](https://portal.azure.com)az **SQL virtuális gépek** erőforrás egy független felügyeleti szolgáltatás. Ezzel egyidejűleg megtekintheti az összes SQL Server virtuális gépet, és módosíthatja az SQL Server számára kijelölt beállításokat: 

![SQL virtuális gépek erőforrás](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Megjegyzések

- Azt javasoljuk, hogy az **SQL virtuális gépek** erőforrás használatával tekintse meg és kezelje az SQL Server virtuális gépek az Azure-ban. De jelenleg az **SQL virtuális gépek** erőforrás nem támogatja a [végfelhasználói](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server virtuális gépek kezelését. A támogatás megszűnése utáni SQL Server virtuális gépek beállításainak kezeléséhez használja az elavult [SQL Server konfigurációs lapot.](#access-the-sql-server-configuration-tab) 
- Az **SQL virtuális gépek** erőforrás csak az SQL [VM erőforrás-szolgáltatónál regisztrált](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server virtuális gépek számára érhető el. 


## <a name="access-the-sql-virtual-machines-resource"></a>Az SQL virtuális gépek erőforrásának elérése
A **virtuális SQL-gépek** erőforrásának eléréséhez tegye a következőket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). 
1. Válassza ki **az összes szolgáltatást**. 
1. Írja be az **SQL virtuális gépeket** a keresőmezőbe.
1. (Nem kötelező): Válassza ki a csillag mellett **SQL virtuális gépek** hozzá ezt a lehetőséget a **Kedvencek** menüben. 
1. Válassza az **SQL virtuális gépek**lehetőséget. 

   ![SQL Server virtuális gépek keresése az összes szolgáltatásban](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. A portál felsorolja az összes SQL Server virtuális gép elérhető az előfizetésen belül. Válassza ki azt, amelyiknek meg szeretné nyitni az **SQL virtuális gépek** erőforrását. Használja a keresőmezőt, ha az SQL Server virtuális gép nem jelenik meg. 

   ![Az összes elérhető SQL Server virtuális gép](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   Az SQL Server virtuális gép kiválasztása megnyitja az **SQL virtuális gépek erőforrását:** 


   ![SQL virtuális gépek erőforrás](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> Az **SQL virtuális gépek** erőforrás dedikált SQL Server-beállításokhoz tartozik. Válassza ki a virtuális gép nevét a **virtuális gép** mezőben a virtuális gépre jellemző, de nem kizárólag az SQL Serverre vonatkozó beállítások megnyitásához. 

## <a name="access-the-sql-server-configuration-tab"></a>Az SQL Server konfigurációs lapjának elérése
Az **SQL Server konfigurációs** lapja elavult. Jelenleg ez az egyetlen módszer a [támogatás megszűnése](virtual-machines-windows-sql-server-2008-eos-extend-support.md) utáni SQL Server virtuális gépek és [az SQL Virtuálisgép-erőforrás-szolgáltatóval nem regisztrált](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server virtuális gépek kezelésére.

Az SQL Server elavult **konfigurációs** lapjának eléréséhez nyissa meg a **Virtuális gépek** erőforrást. Ehhez a következő lépések szükségesek:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). 
1. Válassza ki **az összes szolgáltatást**. 
1. Írja be a **virtuális gépeket** a keresőmezőbe.
1. (Nem kötelező): Válassza ki a **virtuális gépek** melletti csillagot, ha hozzá szeretné adni ezt a lehetőséget a **Kedvencek** menühöz. 
1. Válassza a **Virtuális gépek** lehetőséget. 

   ![Virtuális gépek keresése](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. A portál felsorolja az összes virtuális gép az előfizetésben. Válassza ki azt, amelyiknek meg szeretné nyitni a **Virtuális gépek** erőforrást. Használja a keresőmezőt, ha az SQL Server virtuális gép nem jelenik meg. 
1. Válassza az **SQL Server konfigurációját** a **Beállítások** ablaktáblán az SQL Server virtuális gép kezeléséhez. 

   ![SQL Server-konfiguráció](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server áttekintése Windows virtuális gépen](virtual-machines-windows-sql-server-iaas-overview.md)
* [Gyakori kérdések az SQL Server rendszerrel kapcsolatos kérdésekről Windows VM rendszeren](virtual-machines-windows-sql-server-iaas-faq.md)
* [Az SQL Server díjszabási útmutatója Windows virtuális gépen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Kibocsátási megjegyzések az SQL Server rendszerhez Windows vM rendszeren](virtual-machines-windows-sql-server-iaas-release-notes.md)


