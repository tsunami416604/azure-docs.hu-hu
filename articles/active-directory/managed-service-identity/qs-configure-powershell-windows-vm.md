---
title: "MSI konfigurálása az Azure virtuális gép PowerShell használatával"
description: "Lépés által felügyelt szolgáltatás identitásának (MSI) konfigurálásához egy Azure virtuális gépen, PowerShell-lel részletes utasításokat."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 42c361ac69122d00df290f4c3c2eb2cfeeb9eb47
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) PowerShell használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és MSI eltávolítása egy Azure virtuális gép, PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Továbbá, telepítse [Azure PowerShell legújabb verziójának](https://www.powershellgallery.com/packages/AzureRM) (4.3.1 verzió vagy újabb) Ha még nem tette meg.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>MSI engedélyezése az Azure virtuális gép létrehozása

Az MSI-kompatibilis virtuális gép létrehozása:

1. A következő Azure virtuális gép Quickstarts, csak a szükséges szakaszok befejezése hivatkozik ("Jelentkezzen be az Azure", "Erőforráscsoport létrehozása", "Create hálózati csoport", "A virtuális gép létrehozása"). 

   > [!IMPORTANT] 
   > Amikor a "Hozzon létre a virtuális gép" szakaszban, a módosítások enyhe a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) parancsmag szintaxisát. Vegye fel a `-IdentityType "SystemAssigned"` telepítéséhez például a virtuális Géphez egy olyan MSI Csomaghoz, a paraméter:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [A Windows PowerShell használatával virtuális gép létrehozása](../../virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell-lel Linux virtuális gép létrehozása](../../virtual-machines/linux/quick-create-powershell.md)



2. Az MSI-VM bővítmény használatával hozzáadása a `-Type` paraméter a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", attól függően, hogy a típus, a virtuális gépet, és nevezze el a használatával a `-Name` paraméter. A `-Settings` paraméter határozza meg a jogkivonat beszerzése az OAuth-jogkivonat végpont által használt port:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Egy meglévő Azure virtuális gépen az MSI engedélyezése

Ha egy meglévő virtuális gépen az MSI engedélyezni kell:

1. Jelentkezzen be az Azure használatával `Login-AzureRmAccount`. Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```powershell
   Login-AzureRmAccount
   ```

2. Először kérjen le a virtuális gép tulajdonságok a `Get-AzureRmVM` parancsmag. Majd MSI engedélyezéséhez használja a `-IdentityType` váltani a [frissítés-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmagot:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Az MSI-VM bővítmény használatával hozzáadása a `-Type` paraméter a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", attól függően, hogy a típus, a virtuális gépet, és nevezze el a használatával a `-Name` paraméter. A `-Settings` paraméter határozza meg a jogkivonat beszerzése az OAuth-jogkivonat végpont által használt port. Adja meg a megfelelő `-Location` paraméter, a meglévő virtuális gép helyét megfelelő:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Az Azure virtuális gép MSI eltávolítása

Ha egy virtuális gép, amely már nem kell egy olyan MSI Csomaghoz, használhatja a `RemoveAzureRmVMExtension` parancsmag segítségével távolítsa el az MSI-fájl a virtuális gépből:

1. Jelentkezzen be az Azure használatával `Login-AzureRmAccount`. Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```powershell
   Login-AzureRmAccount
   ```

2. Használja a `-Name` kapcsoló és a [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) parancsmag, a bővítmény hozzáadásakor használt azonos név megadása:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Kapcsolódó tartalom

- [Felügyelt Szolgáltatásidentitás áttekintése](overview.md)
- A teljes Azure virtuális gépek létrehozására – Gyorsútmutatók lásd:
  
  - [A Windows rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md) 

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
















