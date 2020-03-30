---
title: Felügyeleti feladatok automatizálása SQL virtuális gépeken (Klasszikus) | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogyan kezelhető az SQL Server ügynökbővítménye, amely automatizálja az SQL Server egyes felügyeleti feladatait. Ezek közé tartozik az automatikus biztonsági mentés, az automatikus javítás és az Azure Key Vault-integráció. Ez a témakör a klasszikus telepítési módot használja.
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
ms.openlocfilehash: b76ade40db1e85abc0fb42af2e6f4ab88cb092c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982287"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Felügyeleti feladatok automatizálása az Azure virtuális gépeken az SQL Server Agent Extension (Klasszikus) használatával
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasszikus](../classic/sql-server-agent-extension.md)
> 
>
 
Az SQL Server IaaS Agent Extension (SQLIaaSAgent) az Azure virtuális gépeken fut a felügyeleti feladatok automatizálásához. Ez a témakör áttekintést nyújt a bővítmény által támogatott szolgáltatásokról, valamint a telepítésre, állapotra és eltávolításra vonatkozó utasításokról.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A cikk Resource Manager-verziójának megtekintéséhez olvassa el [az SQL Server Agent Extension for SQL Server VMs Resource Manager című témakört.](../sql/virtual-machines-windows-sql-server-agent-extension.md)

## <a name="supported-services"></a>Támogatott szolgáltatások
Az SQL Server IaaS Ügynökbővítmény a következő felügyeleti feladatokat támogatja:

| Felügyeleti szolgáltatás | Leírás |
| --- | --- |
| **SQL automatikus biztonsági mentés** |Automatizálja a virtuális gép SQL Server alapértelmezett példányának összes adatbázisa biztonsági másolatainak ütemezését. További információ: [Automatikus biztonsági mentés az SQL Server az Azure virtuális gépek (Classic)](../classic/sql-automated-backup.md). |
| **SQL automatikus javítás** |Konfigurálja a karbantartási időszakot, amelynek során a virtuális gép fontos Windows-frissítései elhelyezhetőek, így elkerülheti a frissítéseket a munkaterhelés csúcsidején. További információ: [Automatikus javítás az SQL Server az Azure virtuális gépek (Classic)](../classic/sql-automated-patching.md). |
| **Azure Key Vault integrációja** |Lehetővé teszi az Azure Key Vault automatikus telepítését és konfigurálását az SQL Server virtuális gépén. További információ: [Az Azure Key Vault-integráció konfigurálása az SQL Server hez az Azure virtuális gépeken (Klasszikus) című témakörben.](../classic/ps-sql-keyvault.md) |

## <a name="prerequisites"></a>Előfeltételek
Az SQL Server IaaS-ügynökbővítmény virtuális gépen való használatának követelményei:

### <a name="operating-system"></a>Operációs rendszer:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>AZ SQL Server verziói:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Töltse le és konfigurálja a legújabb Azure PowerShell-parancsokat.](/powershell/azure/overview)

Indítsa el a Windows PowerShellt, és csatlakoztassa az Azure-előfizetéséhez az **Add-AzureAccount** paranccsal.

    Add-AzureAccount

Ha több előfizetéssel rendelkezik, a **Select-AzureSubscription** segítségével válassza ki a klasszikus célvirtuális gépét tartalmazó előfizetést.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Ezen a ponton a **Get-AzureVM** paranccsal lekaphatja a klasszikus virtuális gépek és a hozzájuk tartozó szolgáltatásnevek listáját.

    Get-AzureVM

## <a name="installation"></a>Telepítés
Klasszikus virtuális gépek esetén a PowerShell használatával kell telepítenie az SQL Server IaaS Ügynök bővítményt, és konfigurálnia kell a kapcsolódó szolgáltatásokat. Használja a **Set-AzureVMSqlServerExtension** PowerShell-parancsmag a bővítmény telepítéséhez. A következő parancs például telepíti a bővítményt egy Windows Server virtuális gépre (klasszikus), és "SQLIaaSExtension" nevet ad neki.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Ha az SQL IaaS Agent Extension legújabb verziójára frissít, a bővítmény frissítése után újra kell indítania a virtuális gépet.

> [!NOTE]
> A klasszikus virtuális gépek nem rendelkeznek az SQL IaaS Agent Extension telepítésére és konfigurálására a portálon keresztül.

> [!NOTE]
> Az SQL Server IaaS Agent Extension csak az [SQL Server virtuálisgép-gyűjtemény lemezképei](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (csak a csak akkor itáliai fizetéses vagy bring-your-own-license) támogatottak. Nem támogatott, ha manuálisan telepíti az SQL Server rendszert egy csak operációs rendszerű, csak Windows Server rendszerű virtuális gépen, vagy ha testreszabott SQL Server virtuális merevlemezt telepít. Ezekben az esetekben előfordulhat, hogy a bővítmény manuálisan telepíthető és kezelhető a PowerShell használatával, de erősen ajánlott telepíteni egy SQL Server virtuálisgép-gyűjtemény lemezképét, majd testre szabni.

## <a name="status"></a>status
A bővítmény telepítése az egyik módja az ügynök állapotának megtekintése az Azure Portalon. Jelöljön ki egy virtuális gépet a virtuális gép paneljén, majd kattintson a **Bővítmények**elemre. Meg kell jelennie az **SQLIaaSAgent** bővítmény szerepel.

![SQL Server IaaS-ügynökbővítmény az Azure Portalon](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Használhatja a **Get-AzureVMSqlServerExtension** Azure Powershell-parancsmag is.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Eltávolítása
Az Azure Portalon eltávolíthatja a bővítményt a virtuális gép **tulajdonságainak Bővítmények** paneljén a három pontra kattintva. Ezután kattintson **az Eltávolítás gombra.**

![Az SQL Server IaaS Agent Extension eltávolítása az Azure Portalon](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Használhatja az **Remove-AzureVMSqlServerExtension** Powershell parancsmag ját is.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Következő lépések
Kezdje el használni a bővítmény által támogatott szolgáltatások egyikét. További részletekért tekintse meg a cikk [Támogatott szolgáltatások](#supported-services) című részében hivatkozott témaköröket.

Az SQL Server Azure virtuális gépeken való futtatásáról az [SQL Server azure-beli virtuális gépek – áttekintés című témakörben olvashat bővebben.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

