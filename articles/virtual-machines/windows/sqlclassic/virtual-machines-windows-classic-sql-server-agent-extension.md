---
title: SQL virtuális gépek (klasszikus) a felügyeleti feladatok automatizálása |} A Microsoft Docs
description: Ez a témakör ismerteti, hogyan kezelheti az adott SQL Server felügyeleti feladatokat automatizálja az SQL Server agent bővítmény. Ezek közé tartozik az automatikus biztonsági mentés, automatikus javítás és az Azure Key Vault-integráció. Ez a témakör a klasszikus üzembe helyezési módot használja.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2b719185aabd39cd70b9cb890a9599aa06ca4ff4
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330534"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Azure-beli virtuális gépeken az SQL Server ügynök bővítménnyel (klasszikus) felügyeleti feladatok automatizálása
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasszikus](../classic/sql-server-agent-extension.md)
> 
>
 
Az SQL Server IaaS-ügynök bővítmény (SQLIaaSAgent) fut a felügyeleti feladatok automatizálása Azure virtuális gépeken. Ez a témakör a bővítményt, valamint a vonatkozó telepítési, állapot és eltávolítási által támogatott szolgáltatások áttekintést nyújt.

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ez a cikk Resource Managerre vonatkozó verziójának megtekintéséhez lásd: [SQL Server Agent bővítmény SQL Server virtuális gépek Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Támogatott szolgáltatások
Az SQL Server IaaS-ügynök bővítményt a következő felügyeleti feladatokat támogatja:

| Felügyeleti szolgáltatás | Leírás |
| --- | --- |
| **SQL automatikus biztonsági mentés** |Automatizálja az ütemezés a biztonsági mentések minden adatbázis esetén a virtuális gép az SQL Server alapértelmezett példánya esetében. További információkért lásd: [automatikus biztonsági mentés az SQL Server Azure Virtual Machines (klasszikus)](../classic/sql-automated-backup.md). |
| **SQL automatikus javítás** |Konfigurálja a karbantartási időszak során, ami fontos Windows-frissítések a virtuális gép akkor kerül sor, a számítási feladatok csúcsidőben frissítések elkerülése érdekében. További információkért lásd: [automatikus javítása az SQL Server Azure Virtual Machines (klasszikus)](../classic/sql-automated-patching.md). |
| **Azure Key Vault-integráció** |Automatikus telepítés és az SQL Server virtuális gép konfigurálása az Azure Key Vault lehetővé teszi. További információkért lásd: [konfigurálása az Azure Key Vault-integráció az SQL Server Azure virtuális gépeken (klasszikus)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Előfeltételek
Az SQL Server IaaS-ügynök bővítmény használata a virtuális gépre vonatkozó követelmények:

### <a name="operating-system"></a>Operációs rendszer:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Az SQL Server következő verziói:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Töltse le és konfigurálja a legújabb Azure PowerShell-parancsok](/powershell/azure/overview).

Indítsa el a Windows Powershellt, és csatlakoztassa az Azure-előfizetésbe a **Add-AzureAccount** parancsot.

    Add-AzureAccount

Ha több előfizetéssel rendelkezik, használja a **Select-AzureSubscription** válassza ki az előfizetést, amely tartalmazza a cél a klasszikus virtuális gép.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Ezen a ponton a klasszikus virtuális gépeket és a társított szolgáltatás nevét listáját megtekintheti a **Get-AzureVM** parancsot.

    Get-AzureVM

## <a name="installation"></a>Telepítés
A klasszikus virtuális gépeket a PowerShell az SQL Server IaaS-ügynök bővítmény telepítését és konfigurálását a hozzá társított szolgáltatásokat kell használnia. Használja a **Set-AzureVMSqlServerExtension** PowerShell-parancsmag használatával telepítse a bővítményt. Például a következő parancsot a bővítmény telepítését a Windows Server virtuális gép (klasszikus), és neveket, "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Ha az SQL IaaS-ügynök bővítmény legújabb verziójára frissíti, a bővítmény frissítése után újra kell indítani a virtuális gép.

> [!NOTE]
> A klasszikus virtuális gépek nem rendelkeznek egy beállítást, telepítése és konfigurálása az SQL IaaS-ügynök bővítményt a portálon keresztül.

> [!NOTE]
> Az SQL Server IaaS-ügynök bővítmény csak a támogatott [SQL Server rendszerű virtuális gép katalógus rendszerképek](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (használatalapú fizetés vagy bring-your-saját licenc). Ha manuálisan telepíti az SQL Server csak az operációs rendszer Windows Server virtuális gépen, vagy ha telepít egy testre szabott SQL Server virtuális gép virtuális merevlemez nem támogatott. Ezekben az esetekben, előfordulhat, hogy lehet telepíteni, és a bővítmény manuális kezelése PowerShell használatával, de erősen ajánlott egy SQL Server VM image z galerie telepítenie és testre szabni.

## <a name="status"></a>status
Egy győződjön meg arról, hogy telepítve van-e a bővítmény módja az ügynök állapotának megtekintése az Azure Portalon. Válassza ki a virtuális gép panelen felsorolt virtuális gépet, és kattintson a **bővítmények**. Megtekintheti a **SQLIaaSAgent** felsorolt bővítmény.

![Az SQL Server IaaS-ügynök bővítmény az Azure Portalon](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Is használhatja a **Get-AzureVMSqlServerExtension** Azure Powershell-parancsmagot.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Eltávolítás
Az Azure Portalon, akkor is távolítsa el a bővítményt a három pontra kattintva a **bővítmények** a virtuális gép tulajdonságok paneljén. Kattintson a **Eltávolítás**.

![Távolítsa el az Azure Portalon az SQL Server IaaS-ügynök bővítmény](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Is használhatja a **Remove-AzureVMSqlServerExtension** Powershell-parancsmagot.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>További lépések
A bővítmény által támogatott szolgáltatások használatának megkezdéséhez. További részletekért tekintse meg a hivatkozott témakörökben a [támogatott szolgáltatások](#supported-services) című szakaszát.

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos további információkért lásd: [SQL Server on Azure Virtual Machines – áttekintés](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

