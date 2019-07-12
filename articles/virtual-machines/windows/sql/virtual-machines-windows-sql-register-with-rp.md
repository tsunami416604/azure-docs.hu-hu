---
title: Az SQL Servert futtató virtuális gép az Azure-ban regisztrálja az SQL virtuális gép erőforrás-szolgáltató |} A Microsoft Docs
description: Kezelhetőség javítása érdekében az SQL virtuális gép erőforrás-szolgáltató az SQL Server virtuális gép regisztrálja.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786748"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Az SQL Servert futtató virtuális gép az Azure-ban regisztrálja az SQL virtuális gép erőforrás-szolgáltató

Ez a cikk ismerteti, hogyan kell regisztrálni az Azure SQL Server virtuális gép (VM) az SQL virtuális gép erőforrás-szolgáltatónál. 

Az erőforrás-szolgáltató üzembe helyezése az Azure Portalon az SQL Server rendszerű virtuális gép Piactéri lemezképet automatikusan regisztrálja az SQL Server rendszerű virtuális gép. Azonban ha helyi telepítése az SQL Server Azure virtuális gépen az Azure Piactérről származó lemezkép kiválasztása helyett, majd regisztrálnia kell az SQL Server virtuális gép még ma az erőforrás-szolgáltatónál:

-  **Megfelelőségi** – Microsoft-termék feltételeinek megfelelően, használó ügyfeleink az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) tartalmaznia kell a Microsoft az Azure Hybrid Benefit – használatakor, és ehhez regisztrálnia kell az SQL virtuális gép erőforrás-szolgáltatónál. 

-  **A funkció előnyeit** -feloldja az erőforrás-szolgáltató az SQL Server virtuális gép regisztrálása [automatikus javítás](virtual-machines-windows-sql-automated-patching.md), [automatikus biztonsági mentés](virtual-machines-windows-sql-automated-backup-v2.md), figyelés és kezelhetőségi képességeit, valamint [licencelési](virtual-machines-windows-sql-ahb.md) és [edition](virtual-machines-windows-sql-change-edition.md) rugalmasságot. Korábban ezek voltak csak elérhető SQL Server Virtuálisgép-rendszerképek az Azure Marketplace-ről.

Egy Azure virtuális Gépen futó SQL Server önálló telepítése vagy egy egyéni VHD-vel az SQL Server Azure virtuális gép kiépítése a megfelelő SQL Serverhez készült Azure Hybrid Benefit juttatással azzal a feltétellel, hogy a felhasználók használják a Microsoft szerint regisztrálja az SQL VM-erőforrás szolgáltató. 

Az SQL virtuális gép erőforrás-szolgáltató használatához, is regisztrálnia kell az SQL virtuális gép erőforrás-szolgáltató az előfizetéséhez. Ez az az Azure portal, Azure CLI és PowerShell valósítható meg. 

## <a name="prerequisites"></a>Előfeltételek

Az erőforrás-szolgáltató az SQL Server virtuális gép regisztrálásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- A [SQL Servert futtató virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Az Azure CLI](/cli/azure/install-azure-cli) és [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Az SQL virtuális gép erőforrás-szolgáltató regisztrálása
Ha a [SQL IaaS-bővítményt](virtual-machines-windows-sql-server-agent-extension.md) már telepítve van a virtuális Gépet, majd az SQL virtuális gép erőforrás-szolgáltató regisztrálása egyszerűen hoz létre egy metaadat-erőforrás Microsoft.SqlVirtualMachine/SqlVirtualMachines típusú. Alább a kódrészletet, regisztrálhat az SQL virtuális gép erőforrás-szolgáltató, ha az SQL IaaS-bővítmény már telepítve van a virtuális gép van. Meg kell adnia az SQL Server-licencét regisztrálhatja az SQL virtuális gép erőforrás-szolgáltató vagy a kívánt típusát "PAYG" vagy "AHUB". 

Regisztrálja az SQL Server rendszerű virtuális gép PowerShell használata a következő kódrészletet:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

Ha a virtuális gépen nincs telepítve az SQL IaaS-bővítményt, majd regisztrálhatja az SQL virtuális gép erőforrás-szolgáltató SQL egyszerűsített felügyeleti mód megadásával. Könnyen használható SQL-felügyeleti módban, SQL virtuális gép erőforrás-szolgáltató a rendszer automatikusan telepítse az SQL IaaS-bővítményt [egyszerűsített mód](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) , és ellenőrizze az SQL Server példány-metaadat; ezzel nem indítsa újra az SQL Server szolgáltatást. Meg kell adnia az SQL Server-licencét regisztrálhatja az SQL virtuális gép erőforrás-szolgáltató vagy a kívánt típusát "PAYG" vagy "AHUB". 

Könnyen használható SQL felügyeleti üzemmódban a PowerShell használatával a következő kódrészletet az SQL Server rendszerű virtuális gép regisztrálása:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
Az SQL virtuális gép erőforrás-szolgáltató regisztrálása [egyszerűsített mód](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) fog kizárására megfelelőség és rugalmas licencelési engedélyezése, valamint a helyszíni SQL Server-kiadás frissítéseket. Feladatátvevőfürt-példányokat és többpéldányos központi telepítések SQL virtuális gép erőforrás-szolgáltató csak egyszerűsített módban is regisztrálható. Frissítés az Azure Portalon található utasításokat kövesse [teljes mód](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) és átfogó kezelhetőségi szolgáltatás beállítása az SQL Server újraindítását bármikor engedélyezheti. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Az SQL Server 2008/R2, a Windows Server 2008 rendszerű virtuális gépek regisztrálása

Az SQL Server 2008 és 2008 R2, Windows Server 2008 rendszeren telepített regisztrálhatók az erőforrás-szolgáltató az SQL virtuális gép a [NoAgent](virtual-machines-windows-sql-server-agent-extension.md) mód. Ez a beállítás biztosítsa, és lehetővé teszi, hogy az SQL Server virtuális gép korlátozott funkciókkal figyelni az Azure Portalon.

Az alábbi táblázat ismerteti az elfogadható értékek a regisztráció során megadott paraméterek:

| Paraméter | Elfogadható értékek                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, vagy `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` vagy `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Az SQL Server 2008 vagy 2008 R2, Windows Server 2008-példány regisztrálásához használja a következő Powershell-kódrészletet:  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>Regisztrációs állapotának ellenőrzése
Ha az SQL-kiszolgáló már regisztrálva van az SQL virtuális gép erőforrás-szolgáltató az Azure Portalon, az Azure CLI vagy a PowerShell használatával ellenőrizheti. 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Az Azure portal használatával regisztrációs állapotának ellenőrzéséhez tegye a következőket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Keresse meg a [SQL virtuális gépek](virtual-machines-windows-sql-manage-portal.md).
1. Válassza ki az SQL Server virtuális Gépet a listából. Ha az SQL Server virtuális gép nem szerepel itt, valószínű, az SQL Server virtuális gép nincs regisztrálva az SQL virtuális gép erőforrás-szolgáltatónál. 
1. Megtekintheti az érték a `Status`. Ha `Status = Succeeded`, majd az SQL Server rendszerű virtuális gép regisztrálva van az SQL virtuális gép erőforrás-szolgáltató sikeresen megtörtént. 

    ![Az SQL regisztrálni az erőforrás állapotának ellenőrzése](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Ellenőrizze az SQL Server rendszerű virtuális gép a következő paranccsal AZ CLI aktuális regisztrációs állapot. `ProvisioningState` jelennek meg `Succeeded` , ha a regisztráció sikeres volt. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Ellenőrizze a jelenlegi SQL Server rendszerű virtuális gép regisztrációs állapotát a következő PowerShell-parancsmaggal. `ProvisioningState` jelennek meg `Succeeded` , ha a regisztráció sikeres volt. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
A hiba azt jelzi, hogy az SQL Server rendszerű virtuális gép nincs regisztrálva az erőforrás-szolgáltatónál. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Előfizetés az SQL virtuális gép erőforrás-szolgáltató regisztrálása 

Regisztrálja az SQL virtuális gép erőforrás-szolgáltató az SQL Server virtuális gép, regisztrálnia kell az erőforrás-szolgáltató az előfizetéshez. Az Azure portal vagy az Azure CLI-vel rendelkező teheti.

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)


Az alábbi lépéseket fogja regisztrálni az SQL virtuális gép erőforrás-szolgáltató az Azure-előfizetéshez az Azure portal használatával. 

1. Nyissa meg az Azure Portalon, és navigáljon a **minden szolgáltatás**. 
1. Navigáljon a **előfizetések** , és válassza ki az előfizetést a lényeges.  
1. Az a **előfizetések** lapon, lépjen a **erőforrás-szolgáltatók**. 
1. Típus `sql` viszi, megjelenik az SQL-kapcsolódó erőforrás-szolgáltatók a szűrőben. 
1. Válassza *regisztrálása*, *újraregisztrálni*, vagy *Unregister* számára a **Microsoft.SqlVirtualMachine** szolgáltató attól függően, a kívánt művelet. 

   ![A szolgáltató módosítása](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Az alábbi kódrészlet regisztrálja az SQL virtuális gép erőforrás-szolgáltató az Azure-előfizetéshez. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Az alábbi PowerShell-kódrészlet regisztrálja az SQL virtuális gép erőforrás-szolgáltató az Azure-előfizetéshez.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Megjegyzések

 - Az SQL virtuális gép erőforrás-szolgáltató csak a Resource Manager használatával telepített SQL Server virtuális gépek támogatja. A telepített SQL Server virtuális gépek használatával a klasszikus modellt nem támogatottak. 
 - Az SQL virtuális gép erőforrás-szolgáltató csak támogatja az SQL Server virtuális gépek üzembe helyezhető a nyilvános felhőben. A privát vagy a kormányzati felhő, központi telepítések nem támogatottak. 

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server használata a Windows virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Az SQL Server használata a Windows virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


