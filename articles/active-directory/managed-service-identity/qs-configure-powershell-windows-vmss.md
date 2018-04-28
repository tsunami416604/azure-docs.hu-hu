---
title: MSI konfigurálása az Azure VMSS PowerShell használatával
description: Lépésről lépésre egy rendszer és a felhasználó konfigurálására vonatkozó útmutatásokat hozzárendelt identitások az Azure VMSS a PowerShell használatával.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 2b3651eaf702cfe2f73320fcaf2ab469dd7c478a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Konfigurálja a VMSS felügyelt szolgáltatás identitás (MSI) PowerShell használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megismerheti, hogyan hajthat végre a következő felügyelt Szolgáltatásidentitás műveletek a egy Azure virtuális gép méretezési beállítása (VMSS), PowerShell használatával:
- Engedélyezheti vagy letilthatja a rendszer egy Azure VMSS identitásának hozzárendelve
- Hozzáadhat és eltávolíthat egy felhasználó lehet hozzárendelve egy Azure VMSS identitása

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy hozzárendelt rendszer és a felhasználói identitás](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.
- Telepítés [Azure PowerShell legújabb verziójának](https://www.powershellgallery.com/packages/AzureRM) Ha még nem tette meg. 

## <a name="system-assigned-managed-identity"></a>Rendszer adott felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti, és távolítsa el a rendszer hozzárendelt identitást Azure PowerShell használatával.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Identitás hozzárendelve egy Azure VMSS létrehozása közben rendszer engedélyezése

A hozzárendelt rendszeridentitás engedélyezve van a VMSS létrehozása:

1. Tekintse meg *1. példa* a a [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) parancsmag áttekintésével foglalkozó cikkben hozzárendelt rendszer identitással egy VMSS létrehozásához.  A paraméter hozzáadása `-IdentityType SystemAssigned` számára a `New-AzureRmVmssConfig` parancsmagot:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Választható) Az MSI VMSS bővítmény használatával hozzáadása a `-Name` és `-Type` paraméter a [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", attól függően, hogy a típus, a virtuális gépet, és nevezze el a használatával a `-Name` paraméter. A `-Settings` paraméter határozza meg a jogkivonat beszerzése az OAuth-jogkivonat végpont által használt port:

    > [!NOTE]
    > Ez a lépés nem kötelező, hasonlóan a Azure példány metaadatok szolgáltatás (IMDS) identitás végpont, valamint a jogkivonatok beolvasása.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>A rendszer egy meglévő Azure VMSS identitásának hozzárendelt engedélyezése

Ha egy meglévő Azure VMSS egy hozzárendelt rendszer identitásának engedélyezni kell:

1. Jelentkezzen be az Azure használatával `Login-AzureRmAccount`. Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```powershell
   Login-AzureRmAccount
   ```

2. Először kérjen le a VMSS tulajdonságok a [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) parancsmag. Ezután egy hozzárendelt rendszeridentitás engedélyezéséhez használja a `-IdentityType` váltani a [frissítés-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmagot:

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Az MSI VMSS bővítmény használatával hozzáadása a `-Name` és `-Type` paraméter a [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", attól függően, hogy a típus, a virtuális gépet, és nevezze el a használatával a `-Name` paraméter. A `-Settings` paraméter határozza meg a jogkivonat beszerzése az OAuth-jogkivonat végpont által használt port:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Tiltsa le a rendszer egy Azure VMSS hozzárendelt identitás

> [!NOTE]
> A virtuálisgép-méretezési készlet által felügyelt Szolgáltatásidentitás letiltása jelenleg nem támogatott. Időközben válthat rendszer rendelve, és a felhasználó hozzárendelt identitások segítségével.

Ha egy virtuálisgép-méretezési csoportban, amely már nincs szüksége a rendszer identitás hozzárendelt, de továbbra is hozzá kell a felhasználói identitások, használja a következő parancsmagot:

1. Jelentkezzen be az Azure használatával `Login-AzureRmAccount`. Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

2. Futtassa a következő parancsmagot:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>A felhasználói identitás

Ebben a szakaszban megismerheti, hogyan hozzáadása és eltávolítása a felhasználói identitás rendelve egy VMSS Azure PowerShell használatával a.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Rendelje hozzá a felhasználóhoz rendelt identitás egy Azure VMSS létrehozása során

A felhasználó identitását egy új VMSS létrehozása jelenleg nem támogatott Powershellen keresztül. Egy felhasználó lehet hozzárendelve-identitás hozzáadása egy meglévő VMSS a következő szakaszban olvashat. Biztonsági frissítések ellenőrzése.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Felhasználói azonosítót rendel egy meglévő Azure VMSS

Egy a felhasználói identitás egy meglévő Azure VMSS hozzárendelése:

1. Jelentkezzen be az Azure használatával `Connect-AzureRmAccount`. Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Először kérjen le a virtuális gép tulajdonságok a `Get-AzureRmVM` parancsmag. Egy felhasználó lehet hozzárendelve identitás hozzárendelése az Azure VMSS, kövesse a `-IdentityType` és `-IdentityID` váltani a [frissítés-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmag. Cserélje le `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` saját értékekkel.

   > [!IMPORTANT]
   > A felhasználói identitások a különleges karakterek (pl. aláhúzásjel) nevében létrehozása jelenleg nem támogatott. Alfanumerikus karaktereket használja. Biztonsági frissítések ellenőrzése.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Egy Azure VMSS identitás hozzárendelt felhasználó eltávolítása

> [!NOTE]
> Az összes hozzárendelt felhasználói azonosítók eltávolítása egy virtuálisgép-méretezési csoportban jelenleg nem támogatott, kivéve, ha az identitás hozzárendelt rendszer. Biztonsági frissítések ellenőrzése.

Ha a VMSS több hozzárendelt felhasználói identitást, is távolítja el az alábbi parancsokkal legutóbb. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VMSS NAME>` paraméterértékeket a saját értékekkel. A `<MSI NAME>` a VMSS marad, a felhasználó identitása name tulajdonság. Ez az információ VMSS használva az identitási szakaszban találhatók `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Ha a VMSS a hozzárendelt rendszer és a felhasználói identitások is rendelkezik, eltávolíthatja az összes felhasználó hozzárendelt identitások váltásával hozzárendelt csak a rendszer. Használja az alábbi parancsot:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Kapcsolódó tartalom

- [Felügyelt Szolgáltatásidentitás áttekintése](overview.md)
- A teljes Azure virtuális gépek létrehozására – Gyorsútmutatók lásd:
  
  - [A Windows rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md) 

















