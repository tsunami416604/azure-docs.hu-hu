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
ms.openlocfilehash: 9a62dd6e50d2d2e9cd4b825a95d2a20e8469ff30
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997501"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Az Azure-beli SQL Server virtuális gép licencelési modelljét módosítása
Ez a cikk bemutatja, hogyan módosíthatja az Azure-ban az új SQL Server virtuális gép licencelési modelljét erőforrás-szolgáltató az SQL - **Microsoft.SqlVirtualMachine**. Kettő licencelési üzemeltető SQL Server - használatalapú-per-használatot, egy virtuális gépet (VM) modellt és a hozott licences (BYOL). És most már, PowerShell vagy az Azure CLI használatával, módosíthatja licencelési modellt az SQL virtuális gép használja. 

A **Használatalapú-per-használat** modell azt jelenti, hogy az a másodpercenkénti költség, az Azure virtuális Gépen futó tartalmazza-e az SQL Server-licenc költsége.

A **Bring-your-saját licenc** modellje más néven a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), és lehetővé teszi, hogy a saját SQL Server-licencét használja az SQL Server rendszerű virtuális gép. Az árak kapcsolatos további információkért lásd: [SQL virtuális gép díjszabási útmutató](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Váltás a két licenc modell között felmerülő **állásidő nélkül**, nem indítja újra a virtuális Gépet, hozzáadja **további költségek nélkül** (valójában a AHB aktiválása csökkenti a költségeket), és **azonnali hatállyal**. 


## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Az új erőforrás-szolgáltató meglévő SQL virtuális gép regisztrálása
Lehetséges váltani a licencmodellek szükségesek az új SQL virtuális gép erőforrás-szolgáltató (Microsoft.SqlVirtualMachine) által biztosított funkciója. Jelenleg tudni váltani a licencelési modellt, először az új szolgáltató előfizetésének regisztrálása és majd regisztrálja a meglévő virtuális Gépet az új SQL virtuális gép erőforrás-szolgáltatónál. 

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
$SqlVm | Set-AzureRmResource -Force 
``` 

A következő kódrészletet a BYOL modell átvált a használatalapú-per-használat:
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Licencek közötti váltáshoz kell használnia az új SQL virtuális gép erőforrás-szolgáltató. Ezek a parancsok futtatása előtt az új szolgáltató az SQL virtuális gép regisztrálása meg, ha ez a hiba jelentkezik: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` ezt a hibaüzenetet, ha [regisztrálja az új erőforrás-szolgáltató az SQL virtuális gép](#register-existing-SQL-vm-with-new-resource-provider). 
 

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
  >Licencek közötti váltáshoz kell használnia az új SQL virtuális gép erőforrás-szolgáltató. Ezek a parancsok futtatása előtt az új szolgáltató az SQL virtuális gép regisztrálása meg, ha ez a hiba jelentkezik: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` ezt a hibaüzenetet, ha [regisztrálja az új erőforrás-szolgáltató az SQL virtuális gép](#register-existing-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Számítógép aktuális licencelési megtekintése 

A következő kódrészletet lehetővé teszi, hogy az SQL virtuális gép aktuális licencelési modelljének megtekintése. 

```PowerShell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server használata a Windows virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Az SQL Server használata a Windows virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


