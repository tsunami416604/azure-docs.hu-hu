---
title: Felügyeleti feladatok automatizálása SQL virtuális gépeken (klasszikus) | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan kezelhetők a SQL Server ügynök bővítmény, amely automatizálja az adott SQL Server felügyeleti feladatokat. Ezek közé tartozik az automatikus biztonsági mentés, az automatikus javítás és a Azure Key Vault integráció. Ez a témakör a klasszikus üzembe helyezési módot használja.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fe899eebb0139dffabef96da32ab1641c983f726
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84338407"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Felügyeleti feladatok automatizálása az Azure Virtual Machines a SQL Server Agent bővítménnyel (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md)
> * [Klasszikus](virtual-machines-windows-classic-sql-server-agent-extension.md)
> 
>
 
Az SQL Server IaaS-ügynök bővítmény (SQLIaaSAgent) az Azure Virtual Machines szolgáltatásban fut az adminisztrációs feladatok automatizálásához. Ez a témakör áttekintést nyújt a bővítmény által támogatott szolgáltatásokról, valamint a telepítésre, az állapotra és az eltávolításra vonatkozó utasításokról.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A cikk Resource Manager-verziójának megtekintéséhez tekintse meg a [SQL Server virtuális gépek erőforrás-kezelője SQL Server Agent bővítményét](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md).

## <a name="supported-services"></a>Támogatott szolgáltatások
A SQL Server IaaS-ügynök bővítmény a következő felügyeleti feladatokat támogatja:

| Felügyeleti funkció | Leírás |
| --- | --- |
| **SQL automatikus biztonsági mentés** |Automatizálja a biztonsági mentések ütemezését az összes adatbázishoz a virtuális gép SQL Server alapértelmezett példánya számára. További információ: [SQL Server automatikus biztonsági mentése az Azure Virtual Machines (klasszikus)](../classic/sql-automated-backup.md). |
| **SQL automatikus javítás** |Beállítja a karbantartási időszakot, amely alatt a virtuális gép fontos Windows-frissítései elhelyezhetők, így elkerülhetők a frissítések a számítási feladatok maximális ideje alatt. További információ: [SQL Server automatikus javítása az Azure Virtual Machines (klasszikus)](../classic/sql-automated-patching.md). |
| **Azure Key Vault integráció** |Lehetővé teszi Azure Key Vault automatikus telepítését és konfigurálását a SQL Server VM. További információ: [Azure Key Vault integrációjának konfigurálása SQL Server Azure-beli virtuális gépeken (klasszikus)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Előfeltételek
A SQL Server IaaS-ügynök bővítményének használatára vonatkozó követelmények a virtuális gépen:

### <a name="operating-system"></a>Operációs rendszer:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server verziók:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Töltse le és konfigurálja a legújabb Azure PowerShell parancsokat](/powershell/azure/overview).

Indítsa el a Windows PowerShellt, és kapcsolja hozzá az Azure-előfizetéséhez az **Add-AzureAccount** paranccsal.

    Add-AzureAccount

Ha több előfizetéssel rendelkezik, a **Select-azuresubscription parancsot** paranccsal válassza ki azt az előfizetést, amely a célként használt klasszikus virtuális gépet tartalmazza.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Ezen a ponton a **Get-AzureVM** paranccsal lekérheti a klasszikus virtuális gépek és a hozzájuk társított szolgáltatások nevét.

    Get-AzureVM

## <a name="installation"></a>Telepítés
A klasszikus virtuális gépek esetében a PowerShell használatával telepítenie kell a SQL Server IaaS-ügynök bővítményt, és konfigurálnia kell a társított szolgáltatásokat. A bővítmény telepítéséhez használja a **set-AzureVMSqlServerExtension PowerShell-** parancsmagot. A következő parancs például telepíti a bővítményt egy Windows Server rendszerű virtuális gépre (klasszikus), és az "SQLIaaSExtension" nevet.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Ha frissíti az SQL IaaS-ügynök bővítmény legújabb verzióját, a bővítmény frissítése után újra kell indítania a virtuális gépet.

> [!NOTE]
> A klasszikus virtuális gépeken nincs lehetőség az SQL IaaS-ügynök bővítmény telepítésére és konfigurálására a portálon keresztül.

> [!NOTE]
> A SQL Server IaaS-ügynök bővítmény csak [SQL Server VM Gallery-lemezképeken](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms) (utólagos elszámolású vagy saját licenc) támogatott. Nem támogatott, ha manuálisan telepíti a SQL Servert egy Windows Server RENDSZERű virtuális gépre, vagy ha testreszabott SQL Server VM virtuális merevlemezt telepít. Ezekben az esetekben a PowerShell használatával manuálisan is telepítheti és kezelheti a bővítményt, de erősen ajánlott a SQL Server VM katalógus rendszerképének telepítése, majd a testre szabása.

## <a name="status"></a>Állapot
A bővítmény telepítésének egyik módja az, ha megtekinti az ügynök állapotát az Azure Portalon. Válasszon ki egy virtuális gépet a virtuális gép paneljén, majd kattintson a **bővítmények**elemre. Ekkor meg kell jelennie a felsorolt **SQLIaaSAgent** -bővítménynek.

![SQL Server IaaS-ügynök bővítmény az Azure Portalon](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Használhatja a **Get-AzureVMSqlServerExtension** Azure PowerShell-parancsmagot is.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Eltávolítása
Az Azure Portalon távolítsa el a bővítményt a virtuális gép tulajdonságainak **bővítmények** paneljén lévő három pontra kattintva. Ezután kattintson az **Eltávolítás**gombra.

![A SQL Server IaaS-ügynök bővítmény eltávolítása az Azure Portalon](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Használhatja a **Remove-AzureVMSqlServerExtension PowerShell-** parancsmagot is.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Következő lépések
Kezdje el használni a bővítmény által támogatott szolgáltatások egyikét. További részletekért tekintse meg a jelen cikk [támogatott szolgáltatások](#supported-services) című részében hivatkozott témaköröket.

További információ az Azure Virtual Machines SQL Server futtatásáról: [SQL Server az Azure-on Virtual Machines áttekintés](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

