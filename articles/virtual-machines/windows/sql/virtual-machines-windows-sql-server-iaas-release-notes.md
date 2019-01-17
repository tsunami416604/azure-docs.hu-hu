---
title: Az SQL Server használata Azure virtuális gép kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg az új funkciók és fejlesztések az SQL Server-beli virtuális gépen
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: e3f44181ca1a5ea64815aadf52aa7ea792a21416
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358575"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Az SQL Server használata Azure virtuális gép kibocsátási megjegyzései

Az Azure lehetővé teszi, hogy az SQL Server beépített rendszerképpel rendelkező virtuális gép üzembe helyezése. Ez a cikk felsorolja az új funkciók és fejlesztések, amelyek az SQL Server Azure virtuális gépként telepítve a legújabb verzió. 

## <a name="december-2018"></a>2018. december

| **Módosítása** | Részletek |
| --- | --- |
| **Új SQL fürt csoport erőforrás-szolgáltató** | Nincs új erőforrás-szolgáltató (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroup), amely meghatározza a Windows feladatátvevő fürt metaadatait. Az SQL Server virtuális gép csatlakoztatása a *SqlVirtualMachineGroup* csatlakoztatja a Windows feladatátvételi fürtszolgáltatás és a virtuális gép csatlakozik a fürthöz.  |
|**Beállítása az Azure gyorsindítási sablonok egy rendelkezésre állási csoport központi telepítésének automatizálása** |Már lehetséges a Windows feladatátvevő fürt létrehozása, SQL Server virtuális gépek csatlakoztatása, hozza létre a figyelőt és a belső Load Balancer konfigurálása a két Azure gyorsindítási sablonok. További információkért lásd: [WSFC létrehozása, a figyelőt, és konfigurálja az ILB Always On rendelkezésre állási csoporthoz az SQL Server virtuális gép az Azure gyorsindítási sablon](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Az automatikus SQL virtuális gép erőforrás-szolgáltatói regisztrációt** | Az SQL Server üzembe helyezett virtuális gépek után ebben a hónapban automatikusan regisztrálja az új SQL Server erőforrás-szolgáltató. Ez a hónap előtt telepített SQL Server virtuális gépek továbbra is szeretné manuálisan kell regisztrálni. További információkért lásd: [meglévő SQL virtuális gép új erőforrás-szolgáltató regisztrálásához](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>November 2018

| **Módosítása** | Részletek |
| --- | --- |
| **Új SQL virtuális gép erőforrás-szolgáltató** |  Egy új erőforrás-szolgáltató az SQL Server virtuális gépek (Microsoft.SqlVirtualMachine), amely lehetővé teszi, hogy jobb kezelése érdekében az SQL Server virtuális gép van. További információ a virtuális gép regisztrálása: [meglévő SQL virtuális gép új erőforrás-szolgáltató regisztrálásához](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider). |
|**Váltson a licencelési modell** |Mostantól válthat az SQL virtuális gép Azure parancssori felület vagy a Powershell használatával a használatalapú-per-használat és a bring-your-own-license modell között. További információkért lásd: [SQL virtuális gép licencelési modelljét módosítása](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>További források

**Windows virtuális gépek**:

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md).
* [Egy SQL Server Windows virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md)
* [Egy Azure virtuális Gépen futó SQL Server-adatbázis áttelepítése](virtual-machines-windows-migrate-sql.md)
* [Magas rendelkezésre állás és vészhelyreállítás Azure-beli virtuális gépeken az SQL Serverhez](virtual-machines-windows-sql-high-availability-dr.md)
* [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez](virtual-machines-windows-sql-performance.md)
* [Azure-beli virtuális gépeken futó SQL Server – alkalmazásminták és fejlesztési stratégiák](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gép az SQL Server használatának áttekintése](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Az SQL Server Linux virtuális gép kiépítése](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Gyakori kérdések (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [A Linux rendszeren futó SQL Server dokumentációja](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
