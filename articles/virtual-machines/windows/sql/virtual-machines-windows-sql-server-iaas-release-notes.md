---
title: Az Azure Virtual Machines SQL Server kibocsátási megjegyzései | Microsoft Docs
description: Ismerkedjen meg az Azure-beli virtuális gépek SQL Serverjának új szolgáltatásaival és tökéletesítésével
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
ms.openlocfilehash: 61bf9d53b5ceda9420ae594680466a70b1e3d0cd
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882319"
---
# <a name="release-notes-for-sql-server-on-azure-virtual-machines"></a>Az Azure-beli SQL Server kibocsátási megjegyzései Virtual Machines

Az Azure-ban a virtuális gépek (VM-EK) üzembe helyezését SQL Server beépített lemezképével végezheti el. Ez a cikk az [Azure Virtual Machines SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)közelmúltbeli kiadásaiban ismertetett új funkciókat és fejlesztéseket foglalja össze. A cikk felsorolja azokat a jelentős tartalmi frissítéseket is, amelyek nem kapcsolódnak közvetlenül a kiadáshoz, hanem ugyanabban az időkeretben vannak közzétéve. Az egyéb Azure-szolgáltatásokkal kapcsolatos további információkért lásd: [szolgáltatások frissítései](https://azure.microsoft.com/updates).

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
| **Új SQL-IaaS telepítési módjai** | A SQL Server szolgáltatás újraindításának elkerülése érdekében mostantól a SQL Server IaaS-bővítményt [egyszerűsített módban](virtual-machines-windows-sql-server-agent-extension.md) is telepítheti.  |
| **SQL Server kiadás módosítása** | Most már módosíthatja a SQL Server VM [kiadás tulajdonságát](virtual-machines-windows-sql-change-edition.md) . |
| **Az SQL virtuális gép erőforrás-szolgáltatójának módosításai** | A [SQL Server VM az SQL VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md) -szolgáltatóban regisztrálhatja az új SQL IaaS módok használatával. Ez a funkció [Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)rendszerképeket tartalmaz.|
| **Saját licencű lemezképek a Azure Hybrid Benefit használatával** | Az Azure Marketplace-ről üzembe helyezett saját licencű lemezképek mostantól az [utólagos](virtual-machines-windows-sql-ahb.md#remarks)elszámolású licencre válthatnak.| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>2019. május

### <a name="service-improvements"></a>Szolgáltatások fejlesztése

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Új SQL Server VM-kezelés a Azure Portal** | Most már lehetőség van a SQL Server VM kezelésére a Azure Portal. További információ: [SQL Server virtuális gépek kezelése a Azure Portalban](virtual-machines-windows-sql-manage-portal.md).  | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>A dokumentáció fejlesztése

| Dokumentáció | Részletek |
| --- | --- |
| **Új SQL Server VM felügyeleti portál** | Körülbelül egy tucat cikket frissítettek az új SQL Server VM felügyeleti portál felületén. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>Április 2019

### <a name="service-improvements"></a>Szolgáltatások fejlesztése

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Kiterjesztett támogatás a SQL Server 2008/2008 R2 rendszerhez** | Az Azure-beli virtuális gépekre való áttelepítéssel bővítheti SQL Server 2008 és SQL Server 2008 R2 [támogatását](virtual-machines-windows-sql-server-2008-eos-extend-support.md) . | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Március 2019

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Egyéni rendszerkép-támogatás** | Most már telepítheti a [SQL Server IaaS bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) az egyéni operációs rendszerre és az SQL-lemezképekre, amely a [rugalmas licencelés](virtual-machines-windows-sql-ahb.md)korlátozott funkcionalitását kínálja. Ha az egyéni rendszerképét az SQL-erőforrás-szolgáltatóval regisztrálja, adja meg a licenc típusát "AHUB" néven. Ellenkező esetben a regisztráció sikertelen lesz. | 
| **Megnevezett példányok támogatása** | Mostantól a [SQL Server IaaS bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) megnevezett példánnyal is használhatja, ha az alapértelmezett példány eltávolítása megfelelő volt. | 
| **Portál továbbfejlesztése** | A SQL Server VM üzembe helyezésének Azure Portali élményét a használhatóság javítása érdekében kialakítjuk. További információkért tekintse meg a rövid [](quickstart-sql-vm-create-portal.md) útmutatót, valamint a SQL Server VM üzembe helyezésének részletes [leírását](virtual-machines-windows-portal-sql-server-provision.md) .|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Február 2019

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Portál fejlesztése** | Mostantól lehetőség van egy SQL Server VM licencelési modelljének módosítására, ha a saját licencét a [Azure Portal](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider)használatával szeretné felvenni.|
|**A rendelkezésre állási csoport központi telepítésének egyszerűsítése az Azure SQL Server VM parancssori felülettel** | Mostantól minden eddiginél könnyebben üzembe helyezhet egy rendelkezésre állási csoportot az Azure-SQL Server VM. Az [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) használatával létrehozhatja a Windows feladatátvevő fürtöt, a belső terheléselosztó és a rendelkezésre állási csoport figyelőit a parancssorból. További információ: [az azure SQL Server VM CLI használata az Always On rendelkezésre állási csoport konfigurálásához az Azure-beli virtuális](virtual-machines-windows-sql-availability-group-cli.md)gépeken való SQL Serverhoz. | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018. december

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Új erőforrás-szolgáltató SQL Server-fürthöz** | Az új erőforrás-szolgáltató (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) a Windows feladatátvevő fürt metaadatait határozza meg. SQL Server VM csatlakoztatása a *SqlVirtualMachineGroups* a Windows Server feladatátvételi fürt (WSFC) szolgáltatáshoz csatlakozik, és csatlakoztatja a virtuális gépet a fürthöz.  |
|**Rendelkezésre állási csoport üzembe helyezésének automatikus beállítása az Azure Gyorsindítás sablonjaival** |Most már lehetséges a Windows feladatátvevő fürt létrehozása, csatlakoztatás SQL Server virtuális gépekhez, a figyelő létrehozása és a belső terheléselosztó konfigurálása két Azure-beli gyors üzembe helyezési sablonnal. További információt az Azure-beli virtuális gépeken [elérhető SQL Server Azure-beli Gyorsindítás sablonokkal való konfigurálását](virtual-machines-windows-sql-availability-group-quickstart-template.md)ismertető témakörben talál. | 
| **Automatikus regisztráció az SQL VM erőforrás-szolgáltatóhoz** | SQL Server a hónap után üzembe helyezett virtuális gépek automatikusan regisztrálva lesznek az új SQL Server erőforrás-szolgáltatónál. SQL Server a hónap előtt üzembe helyezett virtuális gépeket is manuálisan kell regisztrálni. További információ: [SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>November 2018

| Szolgáltatások fejlesztése | Részletek |
| --- | --- |
| **Új SQL virtuális gép erőforrás-szolgáltatója** |  Az új erőforrás-szolgáltató (Microsoft. SqlVirtualMachine) jobb felügyeletet biztosít a SQL Server virtuális gépeken. A virtuális gépek regisztrálásával kapcsolatos további információkért lásd: [SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Licencelési modell váltása** | Az Azure CLI vagy a PowerShell használatával mostantól válthat a díjköteles használat és a saját licencek között a SQL Server VMhoz. További információkért lásd: [SQL Server virtuális gép licencelési modelljének módosítása az Azure-ban](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>További források

**Windows rendszerű virtuális gépek**:

* [Windows rendszerű virtuális gépek SQL Server áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows rendszerű virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md)
* [Adatbázis migrálása SQL Server Azure-beli virtuális gépen](virtual-machines-windows-migrate-sql.md)
* [Magas rendelkezésre állás és vész-helyreállítási SQL Server az Azure-ban Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Az Azure-beli SQL Server teljesítményének bevált eljárásai Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Alkalmazási minták és fejlesztési stratégiák az Azure-beli SQL Serverhoz Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gépen lévő SQL Server áttekintése](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux rendszerű virtuális gép kiépítése](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Gyakori kérdések (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [A Linux rendszeren futó SQL Server dokumentációja](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
