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
ms.openlocfilehash: c68bae87440bddf704d18b575aeb1f4ba4760bbb
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578243"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Az Azure-beli SQL Server virtuális gép licencelési modelljét módosítása
Ez a cikk bemutatja, hogyan módosíthatja az Azure-ban az új SQL Server virtuális gép licencelési modelljét SQL virtuális gép erőforrás-szolgáltató – **Microsoft.SqlVirtualMachine**. Kettő licencelési üzemeltető SQL Server – használatalapú fizetés, egy virtuális gépet (VM) modellt és a hozott licences (BYOL). És most az Azure portal, az Azure parancssori felület vagy PowerShell használatával módosíthatja licencelési modellt az SQL Server virtuális gép használja. 

A **utólagos elszámolású** (PAYG) modell azt jelenti, hogy az a másodpercenkénti költség, az Azure virtuális Gépen futó tartalmazza-e az SQL Server-licenc költsége.

A **bring-your-saját licenc** modellje (BYOL) néven is ismert a [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), és lehetővé teszi, hogy a saját SQL Server-licencét használja az SQL Server rendszerű virtuális gép. Az árak kapcsolatos további információkért lásd: [SQL Server VM díjszabása útmutató](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Váltás a két licenc modell között felmerülő **állásidő nélkül**, nem indítja újra a virtuális Gépet, hozzáadja **további költségek nélkül** (valójában aktiválása AHB *csökkenti a* költség) ,és**azonnali hatállyal**. 

## <a name="remarks"></a>Megjegyzések

 - CSP-ügyfeleknek a AHB juttatás képes használni, először a használatalapú fizetést biztosító virtuális gépek telepítése, majd a bring-your-saját licenc által. 
 - Az erőforrás-szolgáltató az SQL Server rendszerű virtuális gép egyéni rendszerkép regisztráció során adja meg a licenc típusa = "AHUB". Hagyja a licenc adja meg, üres, vagy adja meg a "Használatalapú" okoz a regisztráció sikertelen lesz. 
 - Törölte-e az SQL Server VM-erőforrás, állítja vissza a kódolt licenc beállításait, a kép. 
 - Megváltoztathatja a licencelési modell funkciója az SQL virtuális gép erőforrás-szolgáltató. Az erőforrás-szolgáltató SQL Server virtuális gép üzembe helyezése az Azure Portalon keresztül Piactéri lemezképet automatikusan regisztrálja. Azonban ügyfelek, akik saját telepíti az SQL Server rendszer manuálisan kell [regisztrálja az SQL Server rendszerű virtuális gép](#register-sql-server-vm-with-the-sql-vm-resource-provider). 

 
## <a name="limitations"></a>Korlátozások

 - A licencmodell konvertálása jelenleg csak használatalapú fizetéses SQL Server virtuálisgép-rendszerképek esetén érhető el. A rendszerképek saját licenccel használható verziói nem konvertálhatóak használatalapú fizetésessé.
 - Licencelési modelljének megváltoztatása csak a Resource Manager modellel üzembe helyezett virtuális gépek esetén támogatott. A klasszikus modellel üzembe helyezett virtuális gépek nem támogatottak. 
 - Licencelési modelljének megváltoztatása csak engedélyezve van a nyilvános felhőalapú telepítések.
 - Licencelési modelljének megváltoztatása csak virtuális gépek, amelyek egyetlen hálózati adapter (hálózati adapter) támogatott. Az egynél több hálózati Adapterrel rendelkező virtuális gépek, el kell távolítani egy hálózati adapter (az Azure portal használatával) mielőtt elkezdené az eljárást. Ellenkező esetben hiba történt az alábbihoz hasonlóan fog futtatni: `The virtual machine '\<vmname\>' has more than one NIC associated.` Bár előfordulhat, hogy térjen vissza a virtuális gép hozzáadása a hálózati adapter, a licencelési módot módosítása után, az automatikus javítás és a biztonsági mentés, például az SQL konfigurációs panelen keresztül végzett műveletek már nem tekinthető támogatott.

## <a name="prerequisites"></a>Előfeltételek

Az SQL virtuális gép erőforrás-szolgáltató használatához az SQL IaaS-bővítményt. Emiatt a okból rendszerbetöltést végrehajtani az SQL virtuális gép erőforrás-szolgáltató folytatja, a következők szükségesek:
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- [Frissítési garanciával rendelkező](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A *utólagos elszámolású* [SQL Server rendszerű virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) együtt a [SQL IaaS-bővítményt](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) telepítve. 

## <a name="with-the-azure-portal"></a>Az Azure Portallal

Módosíthatja a licencelési modell, közvetlenül a portálról. 

1. Keresse meg az SQL Server virtuális gép belül a [az Azure portal](https://portal.azure.com). 
1. Válassza ki **SQL Server-konfiguráció** a a **beállítások** ablaktáblán. 
1. Válassza ki **szerkesztése** a a **SQL Server-licenc** panelen módosíthatja a licencet. 

![AHB portálon](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Ez a beállítás nem érhető el bring-your-saját licenc rendszerképeket. 

## <a name="with-azure-cli"></a>Az Azure CLI-vel

Azure CLI segítségével módosíthatja a licencelési modellt.  

A következő kódrészletet a használatalapú licencelési modell átvált BYOL (vagy az Azure Hybrid Benefit használatával):

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

A következő kódrészletet a bring-your-saját licenc modell használatalapú fizetéses előfizetésre vált: 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```
## <a name="with-powershell"></a>A PowerShell-lel
Licencelési modelljének módosítása a PowerShell használatával is.

A következő kódrészletet a használatalapú licencelési modell átvált BYOL (vagy az Azure Hybrid Benefit használatával):

```powershell
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
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

```powershell
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Az SQL virtuális gép erőforrás-szolgáltató az SQL Server rendszerű virtuális gép regisztrálása
Bizonyos esetekben szükség lehet manuálisan kell regisztrálni az SQL Server virtuális gép az SQL virtuális gép erőforrás-szolgáltatónál. Ehhez szükség lehet manuálisan kell regisztrálni az erőforrás-szolgáltató az előfizetéséhez. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Előfizetés az SQL virtuális gép erőforrás-szolgáltató regisztrálása 

Regisztrálja az SQL erőforrás-szolgáltató az SQL Server virtuális gép, regisztrálnia kell az erőforrás-szolgáltató az előfizetéshez. Az Azure portal vagy az Azure CLI-vel rendelkező teheti. 

#### <a name="with-the-azure-portal"></a>Az Azure Portallal
Az alábbi lépéseket fogja regisztrálni az SQL erőforrás-szolgáltató az Azure-előfizetéshez az Azure portal használatával. 

1. Nyissa meg az Azure Portalon, és navigáljon a **minden szolgáltatás**. 
1. Navigáljon a **előfizetések** , és válassza ki az előfizetést a lényeges.  
1. Az a **előfizetések** panelen lépjen **erőforrás-szolgáltatók**. 
1. Típus `sql` viszi, megjelenik az SQL-kapcsolódó erőforrás-szolgáltatók a szűrőben. 
1. Válassza *regisztrálása*, *újraregisztrálni*, vagy *Unregister* számára a **Microsoft.SqlVirtualMachine** szolgáltató attól függően, a kívánt művelet. 

   ![A szolgáltató módosítása](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Az Azure CLI-vel
Az alábbi kódrészlet regisztrálja az SQL virtuális gép erőforrás-szolgáltató az Azure-előfizetéshez. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>A PowerShell-lel

Az alábbi kódrészlet regisztrálja az SQL erőforrás-szolgáltató az Azure-előfizetéshez.

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Az erőforrás-szolgáltató SQL az SQL Server rendszerű virtuális gép regisztrálása
Miután az SQL virtuális gép erőforrás-szolgáltató regisztrálva lett az előfizetéshez, majd regisztrálhatja az SQL Server virtuális gép az Azure CLI-vel az erőforrás-szolgáltató. 

#### <a name="with-azure-cli"></a>Az Azure CLI-vel
Regisztrálja az SQL Server rendszerű virtuális gép a következő kódrészletet az Azure CLI használatával: 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```
#### <a name="with-powershell"></a>A PowerShell-lel
Regisztrálja az SQL Server rendszerű virtuális gép PowerShell használata a következő kódrészletet:

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Proper
```



## <a name="known-errors"></a>Ismert hibák

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Az SQL IaaS-bővítményt a virtuális gépen nincs telepítve
Az SQL IaaS-bővítményt az SQL virtuális gép erőforrás-szolgáltató az SQL Server virtuális gép regisztrálása szükséges előfeltétel. Ha megpróbálja regisztrálni az SQL Server virtuális gép az SQL IaaS-bővítmény telepítése előtt, a következő hiba fog történni:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

A probléma megoldásához telepítse az SQL IaaS-bővítményt az SQL Server virtuális gép regisztrálása előtt. 

  > [!NOTE]
  > Az SQL IaaS telepítése bővítmény újraindítja az SQL Server szolgáltatást, és csak akkor ajánlott végrehajtani egy karbantartási időszak alatt. További információkért lásd: [SQL IaaS-bővítmény telepítése](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>A "Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < erőforráscsoport >' '< erőforráscsoport >' erőforráscsoportba tartozó erőforrás nem található. A "sqlServerLicenseType" tulajdonság nem található ehhez az objektumhoz. Győződjön meg arról, hogy a tulajdonság létezik-e, és akkor állítható be.
Ez a hiba akkor fordul elő, amikor próbál módosítani a licencelési modellt, amely nincs regisztrálva az erőforrás-szolgáltató SQL az SQL Server virtuális gépen. Az erőforrás-szolgáltatót regisztrálnia kell az [előfizetés](#register-sql-vm-resource-provider-with-subscription), majd regisztrálja az SQL Server virtuális gép az SQL-lel [erőforrás-szolgáltató](#register-sql-server-vm-with-sql-resource-provider). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>"Sku" paraméter nem lehet érvényesíteni.
Ez a hiba jelentkezhetnek, ha próbál módosítani az SQL Server rendszerű virtuális gép licencelési modellt, ha az Azure PowerShell > 4.0: Set-AzResource : "Sku" paraméter nem lehet érvényesíteni. Az argumentum null értékű vagy üres. Adjon meg egy argumentum, amely nem null értékű vagy üres, és próbálkozzon újra a parancsot.
Ez a hiba elhárításához távolítsa el ezeket a sorokat a korábban említett PowerShell kódrészletet licencelési modelljének váltáskor:

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Az Azure PowerShell-verzió ellenőrzéséhez használja a következő kódot:

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server használata a Windows virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Az SQL Server használata a Windows virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


