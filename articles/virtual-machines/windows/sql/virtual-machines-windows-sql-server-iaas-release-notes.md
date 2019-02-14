---
title: Az SQL Server használata Azure virtuális gép kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg az új funkciók és fejlesztések az SQL Server-beli virtuális gépen
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/5/2019
ms.openlocfilehash: b59ac4798260381c11ab22adb7358ff63e5e1d77
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245422"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Az SQL Server használata Azure virtuális gép kibocsátási megjegyzései

Az Azure lehetővé teszi, hogy az SQL Server beépített rendszerképpel rendelkező virtuális gép üzembe helyezése. Ez a cikk összefoglalja a új funkciókat és fejlesztéseket a legutóbbi kiadásaiban [Azure virtuális gépeken futó SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/). A cikk nem közvetlenül kapcsolódó kiadására, de az időkeretből közzétett jelentős tartalomfrissítéseket is megjeleníti. Más Azure-szolgáltatások fejlesztései, lásd: [szolgáltatási hírek](https://azure.microsoft.com/updates)

## <a name="december-2018"></a>2018. december

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Új SQL fürt csoport erőforrás-szolgáltató** | Új erőforrás-szolgáltató (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups), amely meghatározza a Windows feladatátvevő fürt metaadatait. Az SQL Server virtuális gép csatlakoztatása a *SqlVirtualMachineGroups* csatlakoztatja a Windows feladatátvételi fürtszolgáltatás és a virtuális gép csatlakozik a fürthöz.  |
|**Beállítása az Azure gyorsindítási sablonok egy rendelkezésre állási csoport központi telepítésének automatizálása** |Már lehetséges a Windows feladatátvevő fürt létrehozása, SQL Server virtuális gépek csatlakoztatása, hozza létre a figyelőt és a belső Load Balancer konfigurálása a két Azure gyorsindítási sablonok. További információkért lásd: [WSFC létrehozása, a figyelőt, és konfigurálja az ILB Always On rendelkezésre állási csoporthoz az SQL Server virtuális gép az Azure gyorsindítási sablon](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Az automatikus SQL virtuális gép erőforrás-szolgáltatói regisztrációt** | Az SQL Server üzembe helyezett virtuális gépek után ebben a hónapban automatikusan regisztrálja az új SQL Server erőforrás-szolgáltató. Ez a hónap előtt telepített SQL Server virtuális gépek továbbra is szeretné manuálisan kell regisztrálni. További információkért lásd: [meglévő SQL virtuális gép új erőforrás-szolgáltató regisztrálásához](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció fejlesztései | Részletek |
| --- | --- |
|nincs | |
| | |

## <a name="november-2018"></a>November 2018

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Új SQL virtuális gép erőforrás-szolgáltató** |  Új erőforrás-szolgáltató az SQL Server virtuális gépek (Microsoft.SqlVirtualMachine), amely az SQL Server virtuális gép nagyobb felügyeletet biztosít. További információ a virtuális gép regisztrálása: [meglévő SQL virtuális gép új erőforrás-szolgáltató regisztrálásához](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider). |
|**Váltson a licencelési modell** |Mostantól válthat az SQL virtuális gép Azure parancssori felület vagy a Powershell használatával a használatalapú-per-használat és a bring-your-own-license modell között. További információkért lásd: [SQL virtuális gép licencelési modelljét módosítása](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció fejlesztései | Részletek |
| --- | --- |
|nincs | |
| | |

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