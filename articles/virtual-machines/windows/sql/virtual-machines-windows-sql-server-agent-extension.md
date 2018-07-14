---
title: Az SQL virtuális gépeken (Resource Manager) felügyeleti feladatok automatizálása |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezelheti az adott SQL Server felügyeleti feladatokat automatizálja az SQL Server agent bővítmény. Ezek közé tartozik az automatikus biztonsági mentés, automatikus javítás és az Azure Key Vault-integráció.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: jroth
ms.openlocfilehash: c663aec02d4d1808426a9f05a6674d5504563a63
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009405"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Azure virtuális gépeken az SQL Server Agent bővítmény (Resource Manager) a felügyeleti feladatok automatizálása
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Az SQL Server IaaS-ügynök bővítmény (SQLIaaSExtension) fut a felügyeleti feladatok automatizálása Azure virtuális gépeken. Ez a cikk a bővítményt, valamint a vonatkozó telepítési, állapot és eltávolítási által támogatott szolgáltatások áttekintést nyújt.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ez a cikk a klasszikus verzió megtekintéséhez lásd: [SQL Server Agent bővítmény SQL Server virtuális gépek klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Támogatott szolgáltatások
Az SQL Server IaaS-ügynök bővítményt a következő felügyeleti feladatokat támogatja:

| Felügyeleti szolgáltatás | Leírás |
| --- | --- |
| **SQL automatikus biztonsági mentés** |Automatizálja az ütemezés a biztonsági mentések minden adatbázis esetén a virtuális gép az SQL Server alapértelmezett példánya esetében. További információkért lásd: [automatikus biztonsági mentés az SQL Server Azure Virtual Machines szolgáltatásban (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL automatikus javítás** |Konfigurálja a karbantartási időszak során, ami fontos Windows-frissítések a virtuális gép akkor kerül sor, a számítási feladatok csúcsidőben frissítések elkerülése érdekében. További információkért lásd: [automatikus javítása az SQL Server Azure Virtual Machines szolgáltatásban (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure Key Vault-integráció** |Automatikus telepítés és az SQL Server virtuális gép konfigurálása az Azure Key Vault lehetővé teszi. További információkért lásd: [konfigurálása az Azure Key Vault-integráció az SQL Server Azure virtuális gépeken (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Ha telepítve van és fut, az SQL Server IaaS-ügynök bővítmény elérhetővé teszi ezeket a felügyeleti funkciókat a virtuális gép az Azure Portalon és az SQL Server-Piactérről származó rendszerképek az Azure Powershellen keresztül, és Azure-on keresztül az SQL Server panel A bővítmény manuális telepítése a PowerShell. 

## <a name="prerequisites"></a>Előfeltételek
Az SQL Server IaaS-ügynök bővítmény használata a virtuális gépre vonatkozó követelmények:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-verziók**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Az Azure PowerShell**:

* [Töltse le és konfigurálja a legújabb Azure PowerShell-parancsok](/powershell/azure/overview)

> [!IMPORTANT]
> Jelenleg a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) nem támogatott az SQL Server FCI az Azure-ban. Azt javasoljuk, hogy a virtuális gépeket, amelyek egy FCI részt távolítsa el a bővítményt. A bővítmény által támogatott szolgáltatások nem érhetők el az SQL virtuális gépeken az ügynök eltávolítása után.

## <a name="installation"></a>Telepítés
Az SQL Server IaaS-ügynök bővítmény automatikusan települ, amikor üzembe helyezi az SQL Server virtuálisgép-katalógus rendszerképek egyikét. Ha szeretne egy ilyen SQL Server virtuális gépet manuálisan telepítse újra a bővítményt, használja a következő PowerShell-parancsot:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

> [!IMPORTANT]
> Ha a bővítmény nem telepítette, a bővítmény telepítése újraindítja az SQL Server szolgáltatást.

> [!NOTE]
> Az SQL Server IaaS-ügynök bővítmény csak a támogatott [SQL Server rendszerű virtuális gép katalógus rendszerképek](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (használatalapú fizetés vagy bring-your-saját licenc). Ha manuálisan telepíti az SQL Server csak az operációs rendszer Windows Server virtuális gépen, vagy ha telepít egy testre szabott SQL Server virtuális gép virtuális merevlemez nem támogatott. Ezekben az esetekben, előfordulhat, hogy lehet telepíteni, és a bővítmény manuális kezelése PowerShell használatával, de nem kapják meg az SQL Server-konfigurációs beállítások az Azure Portalon. Azonban erősen ajánlott egy SQL Server VM image z galerie telepítenie és testre szabni.

## <a name="status"></a>status
Egy győződjön meg arról, hogy telepítve van-e a bővítmény módja az ügynök állapotának megtekintése az Azure Portalon. Válassza ki **minden beállítás** a virtuális gép ablakban, majd kattintson a **bővítmények**. Megtekintheti a **SQLIaaSExtension** felsorolt bővítmény.

![Az Azure Portalon az SQL Server IaaS-ügynök bővítmény](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Is használhatja a **Get-AzureRmVMSqlServerExtension** Azure PowerShell-parancsmagot.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Az előző parancs megerősíti, hogy az ügynök telepítve van, és az általános állapotadatokat szolgáltat. Konkrét információk automatikus biztonsági mentés és a javítás az alábbi parancsokkal is beszerezheti.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Eltávolítás
Az Azure Portalon, akkor is távolítsa el a bővítményt a három pontra kattintva a **bővítmények** a virtuális gép tulajdonságainak ablakában. Ezután kattintson a **Törlés** gombra.

![Távolítsa el az SQL Server IaaS-ügynök bővítményt az Azure Portalon](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Is használhatja a **Remove-AzureRmVMSqlServerExtension** PowerShell-parancsmagot.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>További lépések
A bővítmény által támogatott szolgáltatások használatának megkezdéséhez. További részletekért lásd: a cikk a [támogatott szolgáltatások](#supported-services) című szakaszát.

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos további információkért lásd: [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

