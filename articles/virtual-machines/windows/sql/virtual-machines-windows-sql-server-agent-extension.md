---
title: "SQL virtuális gépeken (erőforrás-kezelő) felügyeleti feladatok automatizálására |} Microsoft Docs"
description: "Ez a témakör az SQL Server agent-kiterjesztés, automatizálja az adott SQL Server felügyeleti feladatok kezelését ismerteti. Ezek közé tartoznak az automatikus biztonsági mentés, automatikus javítás és az Azure Key Vault-integráció. Ez a témakör a Resource Manager telepítési módot használ."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7152d184bb6d1d4b81aeb47e2c7c9160ada36023
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Azure virtuális gépeken kiterjesztésű SQL Server Agent (erőforrás-kezelő) felügyeleti feladatok automatizálásához
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasszikus](../classic/sql-server-agent-extension.md)
> 
> 

Az SQL Server IaaS ügynök Extension (SQLIaaSExtension) fut az Azure virtuális gépeken felügyeleti feladatok automatizálására. Ez a témakör a bővítményt, valamint a vonatkozó telepítési, állapot és eltávolítási utasításokat támogatja a szolgáltatások áttekintését.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ez a cikk a klasszikus verzióra megtekintése: [SQL Server Agent bővítmény SQL Server VMs Classic](../classic/sql-server-agent-extension.md).

## <a name="supported-services"></a>Támogatott szolgáltatások
Az SQL Server IaaS ügynöke bővítmény a következő felügyeleti feladatokat támogatja:

| Felügyeleti szolgáltatás | Leírás |
| --- | --- |
| **SQL automatikus biztonsági mentés** |Automatizálja az ütemezés a biztonsági mentések az összes olyan adatbázis az alapértelmezett példány az SQL Server, a virtuális gépen. További információkért lásd: [automatikus biztonsági mentés az SQL Server az Azure virtuális gépek (erőforrás-kezelő)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL automatikus javítás** |Konfigurálja a karbantartási időszak során, ami a virtuális gép kerül sor frissítéseire, a munkaterheléshez csúcsidőben frissítések elkerülése érdekében. További információkért lásd: [automatikus javítás az SQL Server az Azure virtuális gépek (erőforrás-kezelő)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure Key Vault-integráció** |Lehetővé teszi, hogy automatikusan telepítse és konfigurálja az Azure Key Vault az SQL Server virtuális gép. További információkért lásd: [konfigurálása Azure Key Vault-integráció az SQL Server Azure virtuális gépeken (erőforrás-kezelő)](virtual-machines-windows-ps-sql-keyvault.md). |

Miután telepített és futó, az SQL Server IaaS ügynöke bővítmény elérhetővé teszi ezeket a felügyeleti szolgáltatásokat az SQL Server panelen, a virtuális gép az Azure portál és az SQL Server piactéren elérhető rendszerkép Azure PowerShell használatával, és Azure szolgáltatáson keresztül A bővítmény manuális telepítésekor PowerShell. 

## <a name="prerequisites"></a>Előfeltételek
A virtuális gép az SQL Server IaaS ügynöke bővítmény használatára vonatkozó követelményeket:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-verziók**:

* SQL Server 2012-ben
* SQL Server 2014
* SQL Server 2016

**Az Azure PowerShell**:

* [Töltse le és konfigurálja a legújabb Azure PowerShell-parancsok](/powershell/azure/overview)

## <a name="installation"></a>Telepítés
Az SQL Server IaaS ügynöke bővítmény automatikusan települ, amikor egy SQL Server virtuális gép gyűjtemény lemezképet. Ha szeretné manuálisan újra a bővítményt a virtuális gépeken SQL Server egyik, használja a következő PowerShell-parancsot:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

Akkor is telepíteni az SQL Server infrastruktúra-szolgáltatási ügynök kiterjesztés csak az operációs rendszer Windows Server virtuális géphez. Ez csak akkor támogatott, ha manuálisan is, hogy a gép van telepítve az SQL Server. Majd telepítse a bővítmény manuálisan azonos **Set-AzureVMSqlServerExtension** PowerShell-parancsmagot.

> [!NOTE]
> Ha manuálisan telepíti az SQL Server IaaS ügynöke bővítmény egy csak az operációs rendszer Windows Server virtuális gépen, az SQL Server-konfigurációs beállítások, az Azure portálon keresztül nem kezelheti. Ebben a forgatókönyvben módosítania kell az összes a PowerShell használatával.

## <a name="status"></a>status
Egy győződjön meg arról, hogy telepítve van-e a bővítmény módja az ügynök állapotát megtekintheti az Azure portálon. Válassza ki **összes beállítás** a virtuális gépek paneljét, majd a **bővítmények**. Megjelenik a **SQLIaaSExtension** felsorolt bővítmény.

![SQL Server IaaS-ügynök bővítmény Azure-portálon](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Használhatja a **Get-AzureVMSqlServerExtension** Azure Powershell-parancsmagot.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Az előző parancs megerősíti, hogy az ügynök telepítve van, és az általános állapotadatokat szolgáltat. Adott állapotinformációról automatikus biztonsági mentés és a javítás az alábbi parancsokkal is beszerezheti.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Eltávolítás
Az Azure portálon, eltávolíthatja a bővítményt a három pont parancsával a **bővítmények** panelen található a virtuálisgép-tulajdonságokat. Kattintson a **törlése**.

![Távolítsa el az SQL Server IaaS-ügynök bővítmény Azure-portálon](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Használhatja a **Remove-AzureRmVMSqlServerExtension** Powershell-parancsmagot.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Következő lépések
A bővítmény által támogatott szolgáltatások használatának megkezdéséhez. A hivatkozott témakörökben talál további részleteket a [támogatott szolgáltatások](#supported-services) című szakaszát.

További információ az SQL Servert futtató Azure virtuális gépeken: [SQL Server Azure virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

