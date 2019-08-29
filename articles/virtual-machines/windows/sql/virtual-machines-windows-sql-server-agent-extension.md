---
title: Felügyeleti feladatok automatizálása Azure-beli virtuális gépeken a SQL Server IaaS-ügynök bővítmény használatával | Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezelheti a SQL Server IaaS-ügynök bővítményt, amely automatizálja az adott SQL Server felügyeleti feladatokat. Ezek közé tartozik az automatikus biztonsági mentés, az automatikus javítás és a Azure Key Vault integráció.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f4dd529481a6216e43d35c76ecee734543d487f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100477"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Felügyeleti feladatok automatizálása Azure-beli virtuális gépeken a SQL Server IaaS-ügynök bővítmény használatával
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Az SQL Server IaaS-ügynök bővítmény (SqlIaasExtension) Azure-beli virtuális gépeken fut, és az adminisztrációs feladatok automatizálását végzi. Ez a cikk áttekintést nyújt a bővítmény által támogatott szolgáltatásokról. Ez a cikk a bővítmény telepítésére, állapotára és eltávolítására vonatkozó utasításokat is tartalmaz.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

A cikk klasszikus verziójának megtekintéséhez lásd: [SQL Server IaaS-ügynök bővítmény SQL Server virtuális gépekhez (klasszikus)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

A SQL Server IaaS bővítmény három kezelhetőségi módot biztosít: 

- A **teljes** mód biztosítja az összes funkciót, de a SQL Server és a rendszergazdai engedélyek újraindítását igényli. Ez a beállítás alapértelmezés szerint telepítve van. A SQL Server VM egyetlen példánnyal való felügyeletére használható. 

- A **Lightweight** nem igényli SQL Server újraindítását, de csak az SQL Server licenc típusának és kiadásának módosítását támogatja. Ezzel a beállítással SQL Server virtuális gépek több példánnyal, vagy a feladatátvevő fürt példányaiban való részvételre. 

- A Windows Server 2008-ra telepített SQL Server 2008-es és SQL Server 2008 R2-es verzió. A Windows Server 2008-rendszerkép ezen módjának használatáról további információt a [Windows server 2008 regisztrációja](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)című témakörben talál. 

## <a name="supported-services"></a>Támogatott szolgáltatások
A SQL Server IaaS-ügynök bővítmény a következő felügyeleti feladatokat támogatja:

| Felügyeleti funkció | Leírás |
| --- | --- |
| **Automatikus biztonsági mentés SQL Server** |Automatizálja a biztonsági mentések ütemezését az összes adatbázishoz az alapértelmezett példányhoz, vagy a virtuális gépen a SQL Server [megfelelően telepített](virtual-machines-windows-sql-server-iaas-faq.md#administration) példányát. További információ: [SQL Server automatikus biztonsági mentése az Azure Virtual Machines szolgáltatásban (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatikus javítás SQL Server** |Beállítja a karbantartási időszakot, amely alatt a virtuális gép fontos Windows-frissítései elhelyezhetők, így elkerülhetők a frissítések a számítási feladatok maximális ideje alatt. További információ: [SQL Server automatikus javítása az Azure Virtual Machines szolgáltatásban (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure Key Vault integráció** |Lehetővé teszi Azure Key Vault automatikus telepítését és konfigurálását a SQL Server VM. További információ: [Azure Key Vault integrációjának konfigurálása az Azure-beli SQL Serverhoz Virtual Machines (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

A SQL Server IaaS-ügynök bővítmény telepítése és futtatása után elérhetővé válik az adminisztrációs funkciók:

* A Azure Portal virtuális gép SQL Server paneljén, valamint az Azure Marketplace-en SQL Server rendszerképeken Azure PowerShellon keresztül.
* Azure PowerShell a bővítmény manuális telepítéséhez. 

## <a name="prerequisites"></a>Előfeltételek
Az alábbi követelmények vonatkoznak a SQL Server IaaS-ügynök bővítmény használatára a virtuális gépen:

**Operációs rendszer**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* A Windows Server 2019 

**SQL Server verziója**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [A legújabb Azure PowerShell parancsok letöltése és konfigurálása](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Felügyeleti üzemmódok módosítása

A SQL Server IaaS-ügynök aktuális módja a PowerShell használatával tekinthető meg: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server a *Lightweight* IaaS-bővítményt futtató virtuális gépek a Azure Portal használatával _teljes mértékben_ frissíthetik a módot. A _nem ügynök_ módban lévő virtuális gépek SQL Server az operációs rendszer Windows 2008 R2 vagy újabb verzióra való frissítése után _teljes egészében_ frissíthetnek. Nem lehetséges a visszalépés – ehhez teljesen el kell távolítania az SQL IaaS-bővítményt, és újra kell telepítenie. 

Az ügynök üzemmódjának teljes frissítése: 


# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az SQL-alapú [virtuális gépek](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) erőforrását. 
1. Válassza ki a SQL Server virtuális gépet, és válassza az **Áttekintés**lehetőséget. 
1. A nem ügynökkel vagy Lightweight IaaS-móddal rendelkező SQL Server virtuális gépek esetében válassza az **SQL IaaS kiterjesztési üzenetében csak a licenc típusát és a kiadási frissítéseket** .

   ![A mód a portálról való módosításának kiválasztása](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Jelölje be az Elfogadom a **SQL Server szolgáltatás újraindítása a virtuális gépen** jelölőnégyzetet, majd válassza a **megerősítés** lehetőséget a IaaS mód teljes állapotra való frissítéséhez. 

    ![A virtuális gépen SQL Server szolgáltatás újraindítását kérő jelölőnégyzet](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)

Futtassa a következőt a CLI Code kódrészlettel:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Futtassa a következő PowerShell-kódrészletet:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>Telepítés
A SQL Server IaaS bővítmény akkor települ, amikor regisztrálja az SQL Server VM az [SQL VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md)-szolgáltatóval. Ha szükséges, a SQL Server IaaS-ügynököt manuálisan is telepítheti teljes vagy könnyű mód használatával. 

A SQL Server IaaS-ügynök bővítményét a teljes módban automatikusan telepíti a rendszer, ha a Azure Portal használatával kiépíti az egyik SQL Server virtuális gép Azure Marketplace-lemezképét. 

### <a name="install-in-full-mode"></a>Telepítés teljes módban
A SQL Server IaaS bővítmény teljes módja teljes kezelhetőséget biztosít a SQL Server VM egyetlen példánya számára. Ha van alapértelmezett példány, a bővítmény az alapértelmezett példánnyal fog működni, és nem támogatja más példányok kezelését. Ha nincs alapértelmezett példány, de csak egyetlen elnevezett példány, akkor a rendszer a nevesített példányt fogja kezelni. Ha nincs alapértelmezett példány, és több megnevezett példány létezik, a bővítmény telepítése sikertelen lesz. 

Telepítse a SQL Server IaaS-ügynököt teljes módban a PowerShell használatával:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Install 'Full' SQL Server IaaS agent extension
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Paraméter | Elfogadható értékek                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` vagy `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> Ha a bővítmény még nincs telepítve, a teljes bővítmény telepítése újraindítja a SQL Server szolgáltatást. A SQL Server szolgáltatás újraindításának elkerüléséhez telepítse a kis kezelhetőséget a korlátozott kezelhetőséggel.
> 
> A SQL Server IaaS bővítmény frissítése nem indítja újra a SQL Server szolgáltatást. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Telepítés egyetlen elnevezett SQL Server-példánnyal rendelkező virtuális gépen
A SQL Server IaaS bővítmény a SQL Server megnevezett példányával fog működni, ha az alapértelmezett példány el lett távolítva, és a IaaS-bővítmény újratelepítése megtörténik.

SQL Server elnevezett példányának használata:
   1. SQL Server VM üzembe helyezése az Azure piactéren. 
   1. Távolítsa el a IaaS bővítményt a [Azure Portalból](https://portal.azure.com).
   1. Távolítsa el SQL Server teljesen a SQL Server VMon belül.
   1. Telepítse a SQL Servert egy megnevezett példánnyal a SQL Server VMon belül. 
   1. Telepítse a IaaS bővítményt a Azure Portal.  


### <a name="install-in-lightweight-mode"></a>Telepítés egyszerűsített módban
A könnyű mód nem indítja újra a SQL Server szolgáltatást, de korlátozott funkcionalitást biztosít. 

Telepítse a SQL Server IaaS-ügynököt egyszerűsített módban a PowerShell használatával:


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Paraméter | Elfogadható értékek                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` vagy `PAYG`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>A SQL Server IaaS-bővítmény állapotának beolvasása
A bővítmény telepítésének egyik módja az, hogy megtekintse az ügynök állapotát a Azure Portalban. Válassza a **minden beállítás** lehetőséget a virtuális gép ablakban, majd válassza a **bővítmények**lehetőséget. Ekkor meg kell jelennie a felsorolt **SqlIaasExtension** -bővítménynek.

![A SQL Server IaaS-ügynök bővítmény állapota a Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

A **Get-AzVMSqlServerExtension** Azure PowerShell parancsmagot is használhatja:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Az előző parancs megerősíti, hogy az ügynök telepítve van, és általános állapotinformációkat biztosít. Az automatikus biztonsági mentéssel és javítással kapcsolatos információkat az alábbi parancsokkal érheti el:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Eltávolítás
A Azure Portal távolítsa el a bővítményt a virtuális gép tulajdonságainak **bővítmények** ablakában található három pontot kiválasztva. Ezután válassza a **Törlés** elemet.

![A SQL Server IaaS-ügynök bővítményének eltávolítása Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

A **Remove-AzVMSqlServerExtension PowerShell-** parancsmagot is használhatja:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>További lépések
Kezdje el használni a bővítmény által támogatott szolgáltatások egyikét. További információkért tekintse meg a jelen cikk [támogatott szolgáltatások](#supported-services) című részében hivatkozott cikkeket.

További információ az Azure Virtual Machines SQL Server futtatásáról: [mi SQL Server az azure Virtual Machines?](virtual-machines-windows-sql-server-iaas-overview.md)

