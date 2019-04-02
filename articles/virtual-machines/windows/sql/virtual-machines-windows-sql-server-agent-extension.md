---
title: Az SQL virtuális gépeken (Resource Manager) felügyeleti feladatok automatizálása |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezelheti az adott SQL Server felügyeleti feladatokat automatizálja az SQL Server agent bővítmény. Ezek közé tartozik az automatikus biztonsági mentés, automatikus javítás és az Azure Key Vault-integráció.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
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
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 71878d5d033f0005d2c8c36d9f59799e125a19dd
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762701"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Azure virtuális gépeken az SQL Server Agent bővítmény (Resource Manager) a felügyeleti feladatok automatizálása
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Az SQL Server IaaS-ügynök bővítmény (SqlIaasExtension) Azure-beli virtuális gépeken fut, és az adminisztrációs feladatok automatizálását végzi. Ez a cikk a bővítményt, valamint a vonatkozó telepítési, állapot és eltávolítási által támogatott szolgáltatások áttekintést nyújt.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ez a cikk a klasszikus verzió megtekintéséhez lásd: [SQL Server Agent bővítmény SQL Server virtuális gépek klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Támogatott szolgáltatások
Az SQL Server IaaS-ügynök bővítményt a következő felügyeleti feladatokat támogatja:

| Felügyeleti szolgáltatás | Leírás |
| --- | --- |
| **SQL automatikus biztonsági mentés** |Automatizálja az ütemezés a biztonsági mentések az összes adatbázis vagy az alapértelmezett példány vagy egy [megfelelően telepített](virtual-machines-windows-sql-server-iaas-faq.md#administration) megnevezett példány az SQL Server virtuális gépen. További információkért lásd: [automatikus biztonsági mentés az SQL Server Azure Virtual Machines szolgáltatásban (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
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

**Azure PowerShell**:

* [Töltse le és konfigurálja a legújabb Azure PowerShell-parancsok](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Jelenleg a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) nem támogatott az SQL Server FCI az Azure-ban. Azt javasoljuk, hogy a virtuális gépeket, amelyek egy FCI részt távolítsa el a bővítményt. A bővítmény által támogatott szolgáltatások nem érhetők el az SQL virtuális gépeken az ügynök eltávolítása után.

## <a name="installation"></a>Telepítés
Az SQL Server IaaS-ügynök bővítmény automatikusan települ, amikor üzembe helyezi az SQL Server virtuálisgép-katalógus rendszerképek egyikét. Az SQL IaaS-bővítményt az SQL Server virtuális gépen egyetlen példányra kezelhetőségi kínál. Ha egy alapértelmezett példány, majd a bővítményt az alapértelmezett példányt fog dolgozni, és azt nem fogja támogatni a többi példány kezelésével. Ha nem alapértelmezett példány, de csak egy megnevezett példányt, akkor az elnevezett példány fogja kezelni. Ha nem alapértelmezett példány, és több megnevezett példány van, a bővítmény meghiúsul telepítése. 



Ha szeretne egy ilyen SQL Server virtuális gépet manuálisan telepítse újra a bővítményt, használja a következő PowerShell-parancsot:

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> Ha a bővítmény nem telepítette, a bővítmény telepítése újraindítja az SQL Server szolgáltatást. Azonban az SQL IaaS-bővítmény frissítése nem indítja újra az SQL Server szolgáltatást. 

> [!NOTE]
> Bár az SQL Server IaaS-ügynök bővítmény telepítése egyéni SQL Server-rendszerképeket, funkciója jelenleg csak [a licenctípus módosítása](virtual-machines-windows-sql-ahb.md). Az SQL IaaS-bővítmény egyéb funkcióit csak fog működni [SQL Server rendszerű virtuális gép katalógus rendszerképek](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (használatalapú fizetés vagy bring-your-saját licenc).

### <a name="use-a-single-named-instance"></a>Egyetlen megnevezett példány
Az SQL IaaS-bővítményt működik egy megnevezett példányt egy SQL Server-lemezképet, ha az alapértelmezett példányt eltávolítják megfelelően, és telepíti az IaaS-bővítményt.

Az SQL Server megnevezett példányt használ, tegye a következőket:
   1. SQL Server virtuális gép üzembe helyezése a piactérről. 
   1. Távolítsa el az IaaS-bővítményt belül a [az Azure portal](https://portal.azure.com).
   1. Távolítsa el az SQL Server teljesen belül az SQL Server rendszerű virtuális gép.
   1. Az SQL Server nevesített példánnyal belül az SQL Server rendszerű virtuális gép telepítése. 
   1. Az Azure Portalon az IaaS-bővítményének telepítése.  

## <a name="status"></a>status
Egy győződjön meg arról, hogy telepítve van-e a bővítmény módja az ügynök állapotának megtekintése az Azure Portalon. Válassza ki **minden beállítás** a virtuális gép ablakban, majd kattintson a **bővítmények**. Megtekintheti a **SqlIaasExtension** felsorolt bővítmény.

![Az Azure Portalon az SQL Server IaaS-ügynök bővítmény](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Is használhatja a **Get-AzVMSqlServerExtension** Azure PowerShell-parancsmagot.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Az előző parancs megerősíti, hogy az ügynök telepítve van, és az általános állapotadatokat szolgáltat. Konkrét információk automatikus biztonsági mentés és a javítás az alábbi parancsokkal is beszerezheti.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Eltávolítás
Az Azure Portalon, akkor is távolítsa el a bővítményt a három pontra kattintva a **bővítmények** a virtuális gép tulajdonságainak ablakában. Ezután kattintson a **Törlés** gombra.

![Távolítsa el az SQL Server IaaS-ügynök bővítményt az Azure Portalon](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Is használhatja a **Remove-AzVMSqlServerExtension** PowerShell-parancsmagot.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>További lépések
A bővítmény által támogatott szolgáltatások használatának megkezdéséhez. További részletekért lásd: a cikk a [támogatott szolgáltatások](#supported-services) című szakaszát.

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos további információkért lásd: [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

