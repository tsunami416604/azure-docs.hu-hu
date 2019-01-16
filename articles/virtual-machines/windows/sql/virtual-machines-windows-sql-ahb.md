---
title: Az Azure SQL virtuális gép licencelési modelljét módosítása |} A Microsoft Docs
description: Ismerje meg, hogyan lehet váltani, az Azure SQL virtuális gép licencelési.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c516bf9b48164f2ef8dc7fea6fb834bdae00a0d1
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332149"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Az Azure-beli SQL Server virtuális gép licencelési modelljét módosítása
Ez a cikk bemutatja, hogyan módosíthatja az Azure-ban az új SQL Server virtuális gép licencelési modelljét erőforrás-szolgáltató az SQL - **Microsoft.SqlVirtualMachine**. Kettő licencelési üzemeltető SQL Server - használatalapú-per-használatot, egy virtuális gépet (VM) modellt és a hozott licences (BYOL). És most már, PowerShell vagy az Azure CLI használatával, módosíthatja licencelési modellt az SQL virtuális gép használja. 

A **Használatalapú-per-használat** modell azt jelenti, hogy az a másodpercenkénti költség, az Azure virtuális Gépen futó tartalmazza-e az SQL Server-licenc költsége.

A **Bring-your-saját licenc** modellje más néven a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), és lehetővé teszi, hogy a saját SQL Server-licencét használja az SQL Server rendszerű virtuális gép. Az árak kapcsolatos további információkért lásd: [SQL virtuális gép díjszabási útmutató](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Váltás a két licenc modell között felmerülő **állásidő nélkül**, nem indítja újra a virtuális Gépet, hozzáadja **további költségek nélkül** (valójában a AHB aktiválása csökkenti a költségeket), és **azonnali hatállyal**. 

## <a name="prerequisites"></a>Előfeltételek
Az SQL virtuális gép erőforrás-szolgáltató használatához az SQL IaaS-bővítményt. Emiatt a okból rendszerbetöltést végrehajtani az SQL virtuális gép erőforrás-szolgáltató folytatja, a következők szükségesek:
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- A [SQL Server rendszerű virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) együtt a [SQL IaaS-bővítményt](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) telepítve. 

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Az új erőforrás-szolgáltató meglévő SQL virtuális gép regisztrálása
Lehetséges váltani a licencmodellek szükségesek az új SQL virtuális gép erőforrás-szolgáltató (Microsoft.SqlVirtualMachine) által biztosított funkciója. Jelenleg tudni váltani a licencelési modellt, először az új szolgáltató előfizetésének regisztrálása és majd regisztrálja a meglévő virtuális Gépet az új SQL virtuális gép erőforrás-szolgáltatónál. Az SQL virtuális gép erőforrás-szolgáltató használatához, is telepítenie kell az SQL IaaS-bővítményt. Így lehetővé teheti, hogy regisztrálja a telepített virtuális gép VHD-vel. További információkért lásd: [SQL IaaS-bővítményt](virtual-machines-windows-sql-server-agent-extension.md). 

  >[!IMPORTANT]
  > Törölte-e az SQL virtuális gép erőforrást, állítja vissza a kép megszegi licenc beállítása módosítva lett. 
  

### <a name="powershell"></a>PowerShell


A következő kódrészletet, Azure kapcsolódik, és ellenőrizze, melyik előfizetés-azonosító használata. 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

Az alábbi kódrészlet először a az új SQL erőforrás-szolgáltató előfizetésének regisztrálása és majd regisztrálja a meglévő SQL virtuális Gépet az új erőforrás-szolgáltató. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your existing SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Portál
Az új SQL virtuális gép erőforrás-szolgáltató a portálon is rögzítheti. Ezért kövesse az alábbi lépéseket:
1. Nyissa meg az Azure Portalon, és navigáljon a **minden szolgáltatás**. 
1. Navigáljon a **előfizetések** , és válassza ki az előfizetést a lényeges.  
1. Az a **előfizetések** panelen lépjen **erőforrás-szolgáltató**. 
1. Típus `sql` viszi, megjelenik az SQL-kapcsolódó erőforrás-szolgáltatók a szűrőben. 
1. Válassza *regisztrálása*, *újraregisztrálni*, vagy *Unregister* számára a **Microsoft.SqlVirtualMachine** szolgáltató attól függően, a kívánt művelet. 

  ![A szolgáltató módosítása](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>A PowerShell használata 
Licencelési modelljének módosítása a PowerShell használatával is.  Győződjön meg arról, hogy az SQL virtuális gép már regisztrálva van az új SQL erőforrás-szolgáltató a licencelési modell váltása előtt. 

A következő kódrészletet a használatalapú-per-használati licenc modell átvált BYOL (vagy az Azure Hybrid Benefit használatával): 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
``` 

A következő kódrészletet a BYOL modell átvált a használatalapú-per-használat:
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Licencek közötti váltáshoz kell használnia az új SQL virtuális gép erőforrás-szolgáltató. Futtassa az alábbi parancsokat az új szolgáltató az SQL virtuális gép regisztrálása előtt meg, ha előfordulhat, hogy ez a hiba jelentkezik: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Ha ezt a hibát látja, [regisztrálja az új erőforrás-szolgáltató az SQL virtuális gép](#register-existing-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Azure CLI segítségével módosíthatja a licencelési modellt.  Győződjön meg arról, hogy az SQL virtuális gép már regisztrálva van az új SQL erőforrás-szolgáltató a licencelési modell váltása előtt. 

A következő kódrészletet a használatalapú-per-használati licenc modell átvált BYOL (vagy az Azure Hybrid Benefit használatával):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

A következő kódrészletet a BYOL modell átvált a használatalapú-per-használat: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Licencek közötti váltáshoz kell használnia az új SQL virtuális gép erőforrás-szolgáltató. Futtassa az alábbi parancsokat az új szolgáltató az SQL virtuális gép regisztrálása előtt meg, ha előfordulhat, hogy ez a hiba jelentkezik: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Ha ezt a hibát látja, [regisztrálja az új erőforrás-szolgáltató az SQL virtuális gép](#register-existing-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Számítógép aktuális licencelési megtekintése 

A következő kódrészletet lehetővé teszi, hogy az SQL virtuális gép aktuális licencelési modelljének megtekintése. 

```PowerShell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>Ismert hibák

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Az SQL IaaS-bővítményt a virtuális gépen nincs telepítve
Az SQL IaaS-bővítményt az SQL virtuális gép erőforrás-szolgáltató az SQL Server virtuális gép regisztrálása szükséges előfeltétel. Ha megpróbálja regisztrálni az SQL Server virtuális gép az SQL IaaS-bővítmény telepítése előtt, a következő hiba fog történni:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

A probléma megoldásához telepítse az SQL IaaS-bővítményt az SQL Server virtuális gép regisztrálása előtt. 

  > [!NOTE]
  > Az SQL IaaS telepítése bővítmény újraindítja az SQL Server szolgáltatást, és csak akkor ajánlott végrehajtani egy karbantartási időszak alatt. További információkért lásd: [SQL IaaS-bővítmény telepítése](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>"Sku" paraméter nem lehet érvényesíteni.
Ez a hiba jelentkezhetnek, ha próbál módosítani az SQL Server rendszerű virtuális gép licencelési modellt, ha az Azure PowerShell > 4.0:

`Set-AzureRmResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Ez a hiba elhárításához távolítsa el ezeket a sorokat a korábban említett PowerShell kódrészletet licencelési modelljének váltáskor: 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Az Azure PowerShell-verzió ellenőrzéséhez használja a következő kódot:

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server használata a Windows virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Az SQL Server használata a Windows virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


