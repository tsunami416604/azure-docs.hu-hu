---
title: Azure virtuális gépeken az SQL Server IaaS-ügynök bővítményt a felügyeleti feladatok automatizálása |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezelheti az adott SQL Server felügyeleti feladatokat automatizálja az SQL Server agent bővítmény. Ezek közé tartozik az automatikus biztonsági mentés, automatikus javítás és az Azure Key Vault-integráció.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798052"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>Azure virtuális gépeken az SQL Server IaaS-ügynök bővítményt a felügyeleti feladatok automatizálása
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Az SQL Server IaaS-ügynök bővítmény (SqlIaasExtension) Azure-beli virtuális gépeken fut, és az adminisztrációs feladatok automatizálását végzi. Ez a cikk áttekintése és a bővítményt, valamint a vonatkozó telepítési, állapot és eltávolítási által támogatott szolgáltatások.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ez a cikk a klasszikus verzió megtekintéséhez lásd: [SQL Server Agent bővítmény SQL Server virtuális gépek klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Nincsenek az SQL IaaS-bővítményt három SQL kezelhetőségi mód: **Teljes**, **egyszerűsített**, és **NoAgent**. 

- **Teljes** mód összes funkciót biztosít, de újra kell indítania az SQL Server és a rendszergazdai engedélyek. Ez a lehetőség, amely alapértelmezés szerint telepítve van, és a egy SQL Server virtuális Gépet, egyetlen példánnyal kezelésére használható. 

- **Egyszerűsített** az SQL Server újraindítása nem szükséges, de csak a módosítás a licenc típusa és az SQL Server kiadása támogatja. Ez a beállítás használható az SQL Server virtuális gépek több példányt, vagy a Feladatátvevőfürt-példány (FCI) részt vevő kell lennie. 

- **NoAgent** dedikált SQL Server 2008 és Windows Server 2008 rendszeren telepített SQL Server 2008 R2. Információ felhasználásáról `NoAgent` mód a Windows Server 2008 lemezkép számára, lásd: [Windows Server 2008 regisztrációs](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms). 

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

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* A Windows Server 2019 

**SQL Server-verziók**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Töltse le és konfigurálja a legújabb Azure PowerShell-parancsok](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Felügyeleti mód megváltoztatása

Az SQL IaaS-ügynök az aktuális mód a PowerShell használatával tekintheti meg: 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Az SQL Server virtuális gépek, amelyek a *NoAgent* vagy *egyszerűsített* IaaS-kiterjesztés telepítése után a módot frissítheti *teljes* az Azure portal használatával. Nem alkalmas való visszalépést – így teljesen távolítsa el az SQL IaaS-bővítményt, majd újra kell telepítenie kell. 

A frissítési ügynök módra *teljes*, tegye a következőket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a [SQL virtuális gépek](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) erőforrás. 
1. Válassza ki az SQL Server virtuális gépet, majd **áttekintése**. 
1. Az SQL virtuális gépek, az a *NoAgent* vagy *egyszerűsített* IaaS módot, válassza ki az üzenet **csak a licenc típusa és edition frissítések érhetők el az SQL IaaS-bővítménnyel**.

    ![Indítsa el a módváltás a portálról](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Vállalja, hogy **indítsa újra az SQL Server szolgáltatás** szerint bejelöli a jelölőnégyzetet, és válassza ki **megerősítése** az IaaS módról "teljes" frissítése. 

    ![IaaS-bővítményt teljes felügyelet engedélyezése](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>Telepítés
Az SQL IaaS-bővítmény telepítve van az SQL Server virtuális Gépen való regisztrálásakor a [SQL virtuális gép erőforrás-szolgáltató](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). Azonban, ha szükséges, az SQL IaaS-ügynök is telepíthető manuálisan *teljes* vagy *egyszerűsített* módú telepítés. 

A *teljes* SQL Server IaaS-ügynök bővítmény automatikusan települ, amikor üzembe helyezi az SQL Server virtuális gép katalógus rendszerképeket az Azure portal használatával. 

### <a name="full-mode-installation"></a>Telepítési teljes üzemmód
A *teljes* SQL IaaS-bővítményt biztosít teljes körű kezelhetőségi egyetlen példányra, az SQL Server virtuális gépen. Ha egy alapértelmezett példány, majd a bővítményt az alapértelmezett példányt fog dolgozni, és azt nem fogja támogatni a többi példány kezelésével. Ha nem alapértelmezett példány, de csak egy megnevezett példányt, akkor az elnevezett példány fogja kezelni. Ha nem alapértelmezett példány, és több megnevezett példány van, a bővítmény meghiúsul telepítése. 

Telepíti a *teljes* mód az SQL IaaS újraindítja az SQL Server szolgáltatást. Az SQL Server szolgáltatás újraindításával elkerüléséhez telepítése a *egyszerűsített* mód a kezelhetőségi inkább korlátozott. 

Az SQL IaaS-ügynök telepítése *teljes* üzemmódban a PowerShell használatával:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Paraméter | Elfogadható értékek                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, vagy `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - A bővítmény nem telepítette, ha telepíti a **teljes** bővítmény újraindítja az SQL Server szolgáltatást. Használat **egyszerűsített** mód az SQL Server-szolgáltatás újraindítás elkerülése érdekében. 
> - Az SQL IaaS-bővítmény frissítése nem indítja újra az SQL Server szolgáltatást. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>A virtuális gép egy egyetlen megnevezett SQL Server-példányt telepítése
Az SQL IaaS-bővítményt működik egy megnevezett példányt egy SQL Server az alapértelmezett példányt eltávolítják és újratelepítik az IaaS-bővítményt.

Az SQL Server megnevezett példányt használ, tegye a következőket:
   1. SQL Server virtuális gép üzembe helyezése a piactérről. 
   1. Távolítsa el az IaaS-bővítményt belül a [az Azure portal](https://portal.azure.com).
   1. Távolítsa el az SQL Server teljesen belül az SQL Server rendszerű virtuális gép.
   1. Az SQL Server nevesített példánnyal belül az SQL Server rendszerű virtuális gép telepítése. 
   1. Az Azure Portalon az IaaS-bővítményének telepítése.  


### <a name="install-in-lightweight-mode"></a>Telepítése egyszerűsített módban
Egyszerűsített mód nem indítja újra az SQL Server szolgáltatást, de csak korlátozott funkciókat biztosít. 

Az SQL IaaS-ügynök telepítése *egyszerűsített* üzemmódban a PowerShell használatával:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Paraméter | Elfogadható értékek                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, vagy `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>Az SQL IaaS-bővítmény állapotának beolvasása
Egy győződjön meg arról, hogy telepítve van-e a bővítmény módja az ügynök állapotának megtekintése az Azure Portalon. Válassza ki **minden beállítás** a virtuális gép ablakban, majd kattintson a **bővítmények**. Megtekintheti a **SqlIaasExtension** felsorolt bővítmény.

![Az Azure Portalon az SQL Server IaaS-ügynök bővítmény](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Is használhatja a **Get-AzVMSqlServerExtension** Azure PowerShell-parancsmagot.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Az előző parancs megerősíti, hogy az ügynök telepítve van, és az általános állapotadatokat szolgáltat. Konkrét információk automatikus biztonsági mentés és a javítás az alábbi parancsokkal is beszerezheti.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Eltávolítás
Az Azure Portalon, akkor is távolítsa el a bővítményt a három pontra kattintva a **bővítmények** a virtuális gép tulajdonságainak ablakában. Ezután kattintson a **Törlés** gombra.

![Távolítsa el az SQL Server IaaS-ügynök bővítményt az Azure Portalon](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Is használhatja a **Remove-AzVMSqlServerExtension** PowerShell-parancsmagot.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>További lépések
A bővítmény által támogatott szolgáltatások használatának megkezdéséhez. További információkért lásd: a cikk a [támogatott szolgáltatások](#supported-services) című szakaszát.

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos további információkért lásd: [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

