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
ms.openlocfilehash: 47578d5b6bb4a0094ba1a2e9e7dc7f92d0b0e231
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607093"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Az SQL Server használata Azure virtuális gép kibocsátási megjegyzései

Az Azure lehetővé teszi, hogy az SQL Server beépített rendszerképpel rendelkező virtuális gép üzembe helyezése. Ez a cikk összefoglalja a új funkciókat és fejlesztéseket a legutóbbi kiadásaiban [Azure virtuális gépeken futó SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/). A cikk nem közvetlenül kapcsolódó kiadására, de az időkeretből közzétett jelentős tartalomfrissítéseket is megjeleníti. Más Azure-szolgáltatások fejlesztései, lásd: [szolgáltatási hírek](https://azure.microsoft.com/updates)


## <a name="june-2019"></a>Június 2019

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Új SQL IaaS-telepítési módok** | Mostantól telepítheti az SQL IaaS-bővítményt [egyszerűsített mód](virtual-machines-windows-sql-server-agent-extension.md) elkerülése érdekében az SQL Server-szolgáltatás újraindítása.  |
| **Az SQL Server edition módosítása** | Most módosíthatja a [vlastnost edition](virtual-machines-windows-sql-change-edition.md) az SQL Server virtuális gép. |
| **Az SQL virtuális gép RP módosítások** | Is [regisztrálja az SQL virtuális gép erőforrás-szolgáltató az SQL Server virtuális gép](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) – még akkor is [Windows 2008 lemezképek](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms) – új SQL IaaS módot a. |
| **BYOL-lemezképeknek AHUB használatával** | Mostantól válthat a marketplace-ről üzembe helyezett BYOL-lemezképeknek a [licenc típusa "Használatalapú fizetéses"](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |



## <a name="may-2019"></a>2019. május

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Új SQL virtuális gépek felügyelete az Azure Portalon** | Most már rendelkezésre áll egy új úgy kezelheti az SQL Server virtuális gép az Azure Portalon. További információkért lásd: [felügyelete az SQL Server rendszerű virtuális gép az Azure Portalon](virtual-machines-windows-sql-manage-portal.md).  | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció | Részletek |
| --- | --- |
| **Új SQL virtuális gép portál kezelése** | Számos cikkek frissítése az új SQL virtuális gép felügyeleti portáljának felületén. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>Április 2019

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Az SQL Server 2008/2008R2 támogatásának kiterjesztése** | [Támogatás kiterjesztése](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server 2008 és az SQL Server 2008 R2 áttelepítése révén *,-van* egy Azure virtuális géphez. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>2019. március

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Egyéni rendszerkép támogatási lehetőségek** | Most már telepítheti a [SQL IaaS-bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) egyéni operációs rendszer és az SQL-rendszerképek, amelyek kínál korlátozott működésének [rugalmas licencelési](virtual-machines-windows-sql-ahb.md). Ha az egyéni rendszerkép Regisztrálás az SQL erőforrás-szolgáltató adja meg a licenc típusa "AHUB" Ellenkező esetben a regisztráció sikertelen lesz. | 
| **Nevesített példány támogatási lehetőségek** | Mostantól használhat a [SQL IaaS-bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) nevesített példánnyal, ha az alapértelmezett példány megfelelően el lett távolítva. | 
| **Portál a fejlesztés** | Az SQL Server virtuális gép üzembe helyezése az Azure portal felületének rendelkezik lett technológiájú, használhatóság javítása érdekében. További információkért lásd: a tájékoztatót [rövid](quickstart-sql-vm-create-portal.md) és alaposabb [útmutató](virtual-machines-windows-portal-sql-server-provision.md) útmutató, amellyel egy SQL Server virtuális gép üzembe helyezése.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>2019. február

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Portál javítása** | Mostantól módosíthatja a licencelési modellt, az SQL Server virtuális gép fizetéses bring-your-saját licenc használata a [Azure Portalon](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider)|
|**Rendelkezésre állási csoport üzembe helyezési egyszerűsítése az Azure SQL virtuális gép CLI-vel** | Mostantól egyszerűbb, mint valaha telepítenie kell egy rendelkezésre állási csoport egy SQL Server virtuális Gépet az Azure-ban. [Az Azure SQL virtuális gép CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) lehetővé teszi, hogy a WSFC, az ILB és a rendelkezésre állási csoport figyelőjének létrehozásához, mind a parancssorból, és a rögzítési idejéhez! További információkért lásd: [használata Azure SQL virtuális gép CLI Azure virtuális Gépeken futó SQL Server AlwaysOn rendelkezésre állási csoport konfigurálása](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018. december

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Új SQL fürt csoport erőforrás-szolgáltató** | Új erőforrás-szolgáltató (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups), amely meghatározza a Windows feladatátvevő fürt metaadatait. Az SQL Server virtuális gép csatlakoztatása a *SqlVirtualMachineGroups* csatlakoztatja a Windows feladatátvételi fürtszolgáltatás és a virtuális gép csatlakozik a fürthöz.  |
|**Beállítása az Azure gyorsindítási sablonok egy rendelkezésre állási csoport központi telepítésének automatizálása** |Már lehetséges a Windows feladatátvevő fürt létrehozása, SQL Server virtuális gépek csatlakoztatása, hozza létre a figyelőt és a belső Load Balancer konfigurálása a két Azure gyorsindítási sablonok. További információkért lásd: [használata az Azure gyorsindítási sablon egy Azure virtuális Gépen futó SQL Server AlwaysOn rendelkezésre állási csoport konfigurálása](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Az automatikus SQL virtuális gép erőforrás-szolgáltatói regisztrációt** | Az SQL Server üzembe helyezett virtuális gépek után ebben a hónapban automatikusan regisztrálja az új SQL Server erőforrás-szolgáltató. Ez a hónap előtt telepített SQL Server virtuális gépek továbbra is szeretné manuálisan kell regisztrálni. További információkért lásd: [meglévő SQL virtuális gép SQL virtuális gép erőforrás-szolgáltató regisztrálásához](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>November 2018

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| **Új SQL virtuális gép erőforrás-szolgáltató** |  Új erőforrás-szolgáltató az SQL Server virtuális gépek (Microsoft.SqlVirtualMachine), amely az SQL Server virtuális gép nagyobb felügyeletet biztosít. További információ a virtuális gép regisztrálása: [meglévő SQL virtuális gép új erőforrás-szolgáltató regisztrálásához](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Váltson a licencelési modell** | Mostantól válthat az SQL virtuális gép Azure parancssori felület vagy a Powershell használatával a használatalapú-per-használat és a bring-your-own-license modell között. További információkért lásd: [SQL virtuális gép licencelési modelljét módosítása](virtual-machines-windows-sql-ahb.md). | 
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
