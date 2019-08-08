---
title: SQL Server az Azure VM kibocsátási megjegyzései | Microsoft Docs
description: Ismerkedjen meg az Azure-beli virtuális gépek SQL Server új szolgáltatásaival és tökéletesítésével
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
ms.date: 08/01/2019
ms.openlocfilehash: e656cd4d901ad9f3180963047a85cd980e56df8b
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774203"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server Azure-beli virtuális gépek kibocsátási megjegyzései

Az Azure-ban a SQL Server beépített rendszerképpel rendelkező virtuális gépek helyezhetők üzembe. Ez a cikk összefoglalja az [Azure Virtual machines SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)legújabb kiadásaiban található új szolgáltatásokat és fejlesztéseket. A cikk felsorolja azokat a jelentős tartalmi frissítéseket is, amelyek nem kapcsolódnak közvetlenül a kiadáshoz, hanem ugyanabban az időkeretben vannak közzétéve. További Azure-szolgáltatások fejlesztése: szolgáltatás- [frissítések](https://azure.microsoft.com/updates)

## <a name="july-2019"></a>Július 2019

### <a name="documentation-improvements"></a>A dokumentáció fejlesztése

| Dokumentáció | Részletek |
| --- | --- |
| **SQL virtuális gép áthelyezése másik régióba** | A Azure Site Recovery használatával [áttelepítheti a SQL Server VM az egyik régióból](virtual-machines-windows-sql-move-different-region.md)a másikba. |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Június 2019

### <a name="service-improvements"></a>Szolgáltatások fejlesztése

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Új SQL-IaaS telepítési módjai** | Az SQL IaaS bővítményt [könnyű módban](virtual-machines-windows-sql-server-agent-extension.md) is telepítheti, hogy elkerülje SQL Server szolgáltatás újraindítását.  |
| **SQL Server kiadás módosítása** | Most már módosíthatja a SQL Server VM [kiadás tulajdonságát](virtual-machines-windows-sql-change-edition.md) . |
| **SQL VM RP-változások** | A [SQL Server VM az SQL VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) -szolgáltatóval (akár [Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms) rendszerképekkel) is regisztrálhatja – az új SQL IaaS módok használatával. |
| **BYOL-lemezképek AHUB használatával** | A piactéren üzembe helyezett BYOL-lemezképek mostantól a ["TB" típusra](virtual-machines-windows-sql-ahb.md#remarks)válthatják a licencük típusát.| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>2019. május

### <a name="service-improvements"></a>Szolgáltatások fejlesztése

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Új SQL VM-kezelés Azure Portal** | Mostantól új módon kezelheti SQL Server VM a Azure Portalban. További információt [a Azure Portal SQL Server VM kezelése](virtual-machines-windows-sql-manage-portal.md)című témakörben talál.  |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>A dokumentáció fejlesztése

| Dokumentáció | Részletek |
| --- | --- |
| **Új SQL virtuális gép portál kezelése** | Körülbelül egy tucat cikket frissítettek az új SQL VM felügyeleti portál felületén. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>Április 2019

### <a name="service-improvements"></a>Szolgáltatások fejlesztése

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **A SQL Server 2008/2008R2 támogatásának kiterjesztése** | [](virtual-machines-windows-sql-server-2008-eos-extend-support.md) A (SQL Server 2008 és SQL Server 2008 R2) támogatását kiterjesztheti az Azure *-* beli virtuális gépekre való áttelepítéssel. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Március 2019

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Egyéni rendszerkép-támogatás** | Most már telepítheti az [SQL IaaS bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) az egyéni operációs rendszerre és az SQL-lemezképekre, amely a [rugalmas licencelés](virtual-machines-windows-sql-ahb.md)korlátozott funkcionalitását kínálja. Ha az SQL-erőforrás-szolgáltatóval regisztrálja az egyéni rendszerképet, adja meg a licenc típusát "AHUB" néven, ellenkező esetben a regisztráció sikertelen lesz. | 
| **Megnevezett példányok támogatása** | Mostantól megnevezett példánnyal is használhatja az [SQL IaaS bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) , ha az alapértelmezett példány eltávolítása megfelelő volt. | 
| **Portál továbbfejlesztése** | A SQL Server VM üzembe helyezésének Azure Portali élményét a használhatóság javítása érdekében kialakítjuk. További információkért tekintse meg a rövid [](quickstart-sql-vm-create-portal.md) útmutatót, valamint a [](virtual-machines-windows-portal-sql-server-provision.md) SQL Server VM üzembe helyezésének részletes leírását.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Február 2019

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Portál fejlesztése** | Mostantól lehetősége van módosítani egy SQL Server VM licencelési modelljét az utólagos elszámolású használatból a saját licenc használatára a [Azure Portal](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider)|
|**Az AG üzembe helyezésének egyszerűsítése az Azure SQL VM parancssori felülettel** | Mostantól minden eddiginél könnyebben üzembe helyezhet egy rendelkezésre állási csoportot az Azure-SQL Server VM. Az [Azure SQL VM](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) parancssori felülete lehetővé teszi az WSFC, a ILB és az AG figyelő létrehozását a parancssorból, valamint a rekord időben. További információ: az Azure [SQL VM CLI használata az Always On rendelkezésre állási csoport konfigurálásához SQL Server Azure-beli virtuális gépen](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018. december

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Új SQL-fürt csoportjának erőforrás-szolgáltatója** | Egy új erőforrás-szolgáltató (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups), amely a Windows feladatátvevő fürt metaadatait határozza meg. SQL Server VM csatlakoztatása a *SqlVirtualMachineGroups* rendszerindításkor a Windows feladatátvételi fürtszolgáltatás és a fürthöz csatlakozik a virtuális géphez.  |
|**Rendelkezésre állási csoport üzembe helyezésének automatizálása Azure Gyorsindítás sablonokkal** |Most már lehetséges a Windows feladatátvevő fürt létrehozása, csatlakoztatás SQL Server virtuális gépekhez, a figyelő létrehozása és a belső Load Balancer konfigurálása két Azure-beli gyors üzembe helyezési sablonnal. További információt az Azure-beli virtuális gépeken [SQL Server az Always On rendelkezésre állási csoport konfigurálása az Azure gyorsindítási sablonnal](virtual-machines-windows-sql-availability-group-quickstart-template.md)című témakörben talál. | 
| **Automatikus SQL VM erőforrás-szolgáltató regisztrációja** | SQL Server a hónap után üzembe helyezett virtuális gépek automatikusan regisztrálva lesznek az új SQL Server erőforrás-szolgáltatónál. SQL Server a hónap előtt üzembe helyezett virtuális gépeket is manuálisan kell regisztrálni. További információ: [meglévő SQL virtuális gép regisztrálása SQL VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md)-szolgáltatóval.|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>November 2018

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Új SQL virtuális gép erőforrás-szolgáltatója** |  SQL Server virtuális gépek (Microsoft. SqlVirtualMachine) új erőforrás-szolgáltatója, amely jobb felügyeletet biztosít a SQL Server VM. A virtuális gép regisztrálásával kapcsolatos további információkért lásd: [meglévő SQL virtuális gép regisztrálása új erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md)-szolgáltatóval. |
|**Licencelési modell váltása** | Az Azure CLI vagy a PowerShell használatával mostantól válthat az SQL-alapú virtuális gép díjköteles használati és saját licencelési modellje között. További információ: [az SQL virtuális gép licencelési modelljének módosítása](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>További források

**Windows rendszerű virtuális gépek**:

* A [Windows rendszerű virtuális gépek SQL Serverának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md).
* [SQL Server Windows rendszerű virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md)
* [Adatbázis migrálása SQL Server Azure-beli virtuális gépen](virtual-machines-windows-migrate-sql.md)
* [Magas rendelkezésre állás és vész-helyreállítási SQL Server az Azure-ban Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez](virtual-machines-windows-sql-performance.md)
* [Azure-beli virtuális gépeken futó SQL Server – alkalmazásminták és fejlesztési stratégiák](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gépen lévő SQL Server áttekintése](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux rendszerű virtuális gép kiépítése](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Gyakori kérdések (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [A Linux rendszeren futó SQL Server dokumentációja](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
