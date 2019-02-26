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
ms.date: 2/13/2019
ms.openlocfilehash: d5c003b2588813f8e8a1dfc2923b9d4c8d8c6cc8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820433"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Az SQL Server használata Azure virtuális gép kibocsátási megjegyzései

Az Azure lehetővé teszi, hogy az SQL Server beépített rendszerképpel rendelkező virtuális gép üzembe helyezése. Ez a cikk összefoglalja a új funkciókat és fejlesztéseket a legutóbbi kiadásaiban [Azure virtuális gépeken futó SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/). A cikk nem közvetlenül kapcsolódó kiadására, de az időkeretből közzétett jelentős tartalomfrissítéseket is megjeleníti. Más Azure-szolgáltatások fejlesztései, lásd: [szolgáltatási hírek](https://azure.microsoft.com/updates)


## <a name="february-2019"></a>2019. február

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Portál javítása** | Mostantól módosíthatja a licencelési modellt, az SQL Server virtuális gép fizetéses bring-your-saját licenc használata a [az Azure portal](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1).|
|**Rendelkezésre állási csoport üzembe helyezési egyszerűsítése az Azure SQL virtuális gép CLI-vel** | Mostantól egyszerűbb, mint valaha telepítenie kell egy rendelkezésre állási csoport egy SQL Server virtuális Gépet az Azure-ban. Az SQL virtuális gép az Azure CLI segítségével hozhat létre a WSFC, az ILB és a rendelkezésre állási csoport figyelőjét, mind a parancssorból, és a rögzítési idejéhez! További információkért lásd: [használata Azure SQL virtuális gép CLI Azure virtuális Gépeken futó SQL Server AlwaysOn rendelkezésre állási csoport konfigurálása](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció fejlesztései | Részletek |
| --- | --- |
|nincs | |
| | |

## <a name="december-2018"></a>2018. december

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Új SQL fürt csoport erőforrás-szolgáltató** | Új erőforrás-szolgáltató (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups), amely meghatározza a Windows feladatátvevő fürt metaadatait. Az SQL Server virtuális gép csatlakoztatása a *SqlVirtualMachineGroups* csatlakoztatja a Windows feladatátvételi fürtszolgáltatás és a virtuális gép csatlakozik a fürthöz.  |
|**Beállítása az Azure gyorsindítási sablonok egy rendelkezésre állási csoport központi telepítésének automatizálása** |Már lehetséges a Windows feladatátvevő fürt létrehozása, SQL Server virtuális gépek csatlakoztatása, hozza létre a figyelőt és a belső Load Balancer konfigurálása a két Azure gyorsindítási sablonok. További információkért lásd: [használata az Azure gyorsindítási sablon egy Azure virtuális Gépen futó SQL Server AlwaysOn rendelkezésre állási csoport konfigurálása](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Az automatikus SQL virtuális gép erőforrás-szolgáltatói regisztrációt** | Az SQL Server üzembe helyezett virtuális gépek után ebben a hónapban automatikusan regisztrálja az új SQL Server erőforrás-szolgáltató. Ez a hónap előtt telepített SQL Server virtuális gépek továbbra is szeretné manuálisan kell regisztrálni. További információkért lásd: [meglévő SQL virtuális gép SQL virtuális gép erőforrás-szolgáltató regisztrálásához](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>November 2018

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Új SQL virtuális gép erőforrás-szolgáltató** |  Új erőforrás-szolgáltató az SQL Server virtuális gépek (Microsoft.SqlVirtualMachine), amely az SQL Server virtuális gép nagyobb felügyeletet biztosít. További információ a virtuális gép regisztrálása: [meglévő SQL virtuális gép új erőforrás-szolgáltató regisztrálásához](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). |
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