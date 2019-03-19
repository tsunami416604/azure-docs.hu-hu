---
title: Az Azure-beli SQL Server virtuális gép licencelési modelljét módosítása |} A Microsoft Docs
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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 66e94ed4f68ed43891ad3e81bd66cdc57799d204
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117897"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Az Azure-beli SQL Server virtuális gép licencelési modelljét módosítása
Ez a cikk bemutatja, hogyan módosíthatja az Azure-ban az új SQL Server virtuális gép licencelési modelljét SQL virtuális gép erőforrás-szolgáltató – **Microsoft.SqlVirtualMachine**. Kettő licencelési üzemeltető SQL Server – használatalapú fizetés, egy virtuális gépet (VM) modellt és a hozott licences (BYOL). És most már, PowerShell vagy az Azure CLI használatával, módosíthatja licencelési modellt az SQL Server virtuális gép használja. 

A **utólagos elszámolású** (PAYG) modell azt jelenti, hogy az a másodpercenkénti költség, az Azure virtuális Gépen futó tartalmazza-e az SQL Server-licenc költsége.

A **bring-your-saját licenc** modellje (BYOL) néven is ismert a [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), és lehetővé teszi, hogy a saját SQL Server-licencét használja az SQL Server rendszerű virtuális gép. Az árak kapcsolatos további információkért lásd: [SQL Server VM díjszabása útmutató](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Váltás a két licenc modell között felmerülő **állásidő nélkül**, nem indítja újra a virtuális Gépet, hozzáadja **további költségek nélkül** (valójában aktiválása AHB *csökkenti a* költség) ,és**azonnali hatállyal**. 

  >[!NOTE]
  > - A licencmodell konvertálása jelenleg csak használatalapú fizetéses SQL Server virtuálisgép-rendszerképek esetén érhető el. A rendszerképek saját licenccel használható verziói nem konvertálhatóak használatalapú fizetésessé.
  > - CSP-ügyfeleknek a AHB juttatás képes használni, először a használatalapú fizetést biztosító virtuális gépek telepítése, majd a bring-your-saját licenc által. 
  > - Ez a lehetőség jelenleg csak akkor használható a nyilvános felhőben telepítések.


## <a name="prerequisites"></a>Előfeltételek
Az SQL virtuális gép erőforrás-szolgáltató használatához az SQL IaaS-bővítményt. Emiatt a okból rendszerbetöltést végrehajtani az SQL virtuális gép erőforrás-szolgáltató folytatja, a következők szükségesek:
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- [Frissítési garanciával rendelkező](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A *utólagos elszámolású* [SQL Server rendszerű virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) együtt a [SQL IaaS-bővítményt](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) telepítve. 


## <a name="register-sql-resource-provider-with-your-subscription"></a>SQL típusú erőforrás-szolgáltató regisztrálása az előfizetéshez 

Lehetséges váltani a licencmodellek szükségesek az új SQL virtuális gép erőforrás-szolgáltató (Microsoft.SqlVirtualMachine) által biztosított funkciója. Az SQL Server üzembe helyezett virtuális gépek után a 2018 December automatikusan regisztrálja az új erőforrás-szolgáltató. Dátum előtt telepített meglévő virtuális gépek azonban még nem tud váltani a licencelési modellt az erőforrás-szolgáltató manuálisan regisztrálni kell. 

  > [!NOTE] 
  > Törölte-e az SQL Server VM-erőforrás, állítja vissza a kép megszegi licenc beállítása módosítva lett. 

Regisztrálja az SQL erőforrás-szolgáltató az SQL Server virtuális gép, regisztrálnia kell az erőforrás-szolgáltató az előfizetéshez. Ehhez az Azure CLI-vel, PowerShell, vagy az Azure portal használatával. 

### <a name="with-the-azure-portal"></a>Az Azure Portallal
Az alábbi lépéseket az Azure-előfizetését az Azure portal használatával fognak regisztrálni az erőforrás-szolgáltató SQL. 

1. Nyissa meg az Azure Portalon, és navigáljon a **minden szolgáltatás**. 
1. Navigáljon a **előfizetések** , és válassza ki az előfizetést a lényeges.  
1. Az a **előfizetések** panelen lépjen **erőforrás-szolgáltatók**. 
1. Típus `sql` viszi, megjelenik az SQL-kapcsolódó erőforrás-szolgáltatók a szűrőben. 
1. Válassza *regisztrálása*, *újraregisztrálni*, vagy *Unregister* számára a **Microsoft.SqlVirtualMachine** szolgáltató attól függően, a kívánt művelet. 

   ![A szolgáltató módosítása](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="with-azure-cli"></a>Az Azure CLI-vel
A következő kódrészletet az erőforrás-szolgáltató SQL regisztrálja az Azure-előfizetésében. 

```cli
# Register the new SQL resource provider for your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="with-powershell"></a>A PowerShell-lel
A következő kódrészletet az erőforrás-szolgáltató SQL regisztrálja az Azure-előfizetésében. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```


## <a name="register-sql-server-vm-with-sql-resource-provider"></a>Az erőforrás-szolgáltató SQL az SQL Server rendszerű virtuális gép regisztrálása
Miután az SQL erőforrás-szolgáltató regisztrálva lett az előfizetéshez, majd regisztrálhatja az SQL Server virtuális gép erőforrás-szolgáltatóval. Azure CLI és PowerShell használatával teheti meg. 

### <a name="with-azure-cli"></a>Az Azure CLI-vel

Regisztrálja az SQL Server rendszerű virtuális gép a következő kódrészletet az Azure CLI használatával: 

```cli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```

### <a name="with-powershell"></a>A PowerShell-lel

Regisztrálja az SQL Server rendszerű virtuális gép PowerShell használata a következő kódrészletet: 
```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="change-licensing-model"></a>Licencelési modell módosítása
Miután az SQL Server virtuális gép az erőforrás-szolgáltató regisztrálva van, a licencelési modellt, az Azure portal, az Azure parancssori felület vagy PowerShell használatával is módosíthatja. 


  >[!NOTE]
  >  A licencmodell konvertálása jelenleg csak használatalapú fizetéses SQL Server virtuálisgép-rendszerképek esetén érhető el. A rendszerképek saját licenccel használható verziói nem konvertálhatóak használatalapú fizetésessé. 

### <a name="with-the-azure-portal"></a>Az Azure Portallal
Módosíthatja a licencelési modell, közvetlenül a portálról. 

1. Keresse meg az SQL Server virtuális gép belül a [az Azure portal](https://portal.azure.com). 
1. Válassza ki **SQL Server-konfiguráció** a a **beállítások** ablaktáblán. 
1. Válassza ki **szerkesztése** a a **SQL Server-licenc** panelen módosíthatja a licencet. 

![AHB portálon](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Ez a beállítás nem érhető el bring-your-saját licenc rendszerképeket. 

### <a name="with-azure-cli"></a>Az Azure CLI-vel
Azure CLI segítségével módosíthatja a licencelési modellt.  

A következő kódrészletet a használatalapú licencelési modell átvált BYOL (vagy az Azure Hybrid Benefit használatával):
```azurecli
# Switch  your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

A következő kódrészletet a BYOL modell használatalapú fizetéses előfizetésre vált: 
```azurecli
# Switch  your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

### <a name="with-powershell"></a>A PowerShell-lel 
Licencelési modelljének módosítása a PowerShell használatával is. 

A következő kódrészletet a használatalapú licencelési modell átvált BYOL (vagy az Azure Hybrid Benefit használatával): 
```PowerShell
# Switch  your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

A következő kódrészletet a BYOL modell használatalapú fizetéses előfizetésre vált:
```PowerShell
# Switch  your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 


## <a name="view-current-licensing"></a>Számítógép aktuális licencelési megtekintése 

A következő kódrészletet lehetővé teszi, hogy az SQL Server virtuális gép aktuális licencelési modelljének megtekintése. 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
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

`Set-AzResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

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

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>A "Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < erőforráscsoport >' '< erőforráscsoport >' erőforráscsoportba tartozó erőforrás nem található. A "sqlServerLicenseType" tulajdonság nem található ehhez az objektumhoz. Győződjön meg arról, hogy a tulajdonság létezik-e, és akkor állítható be.
Ez a hiba akkor fordul elő, amikor az SQL Server rendszerű virtuális gép nincs regisztrálva az erőforrás-szolgáltató SQL. Az erőforrás-szolgáltatót regisztrálnia kell az [előfizetés](#register-sql-resource-provider-with-your-subscription), majd regisztrálja az SQL Server virtuális gép az SQL-lel [erőforrás-szolgáltató](#register-sql-server-vm-with-sql-resource-provider). 

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server használata a Windows virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Az SQL Server használata a Windows virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


