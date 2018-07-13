---
title: Az MSI konfigurálása Azure virtuális gép PowerShell-lel
description: Útmutató Azure virtuális gép, PowerShell-lel konfigurálja a Felügyeltszolgáltatás-identitás (MSI) lépésben.
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9a466a5c695277a7b5833f997e2ad7281c962f3f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610999"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás (MSI) PowerShell-lel

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és távolítsa el az MSI egy Azure virtuális gép, PowerShell-lel.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Emellett telepítse [az Azure PowerShell legújabb verzióját](https://www.powershellgallery.com/packages/AzureRM) (4.3.1-es vagy újabb) Ha még nem tette.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Az MSI engedélyezéséhez egy Azure virtuális gép létrehozása során

Az MSI-kompatibilis virtuális gép létrehozásához:

1. Tekintse meg a következő Azure virtuális gép gyors útmutatók elvégzése csak a szükséges szakaszok egyikét ("Jelentkezzen be az Azure-bA", "Create resource group", "Létrehozása a hálózati csoport", "A virtuális gép létrehozása"). 

   > [!IMPORTANT] 
   > Ha az "Az VM létrehozása" szakaszban kap, a módosítások kisebb a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) parancsmag szintaxisát. Ügyeljen arra, hogy adjon hozzá egy `-IdentityType "SystemAssigned"` például kiépítéséhez a virtuális Gépet egy olyan MSI Csomaghoz, a paraméter:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Hozzon létre egy Windows virtuális gépet PowerShell-lel](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [Hozzon létre egy Linux rendszerű virtuális gép PowerShell-lel](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. Adja hozzá az MSI-VM bővítmény használatával a `-Type` paraméterrel a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Egy meglévő Azure virtuális gépen az MSI engedélyezéséhez

Ha szeretne egy meglévő virtuális gépen az MSI engedélyezéséhez:

1. Jelentkezzen be Azure-bA `Connect-AzureRmAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális Gépen, például a "Virtuális gép közreműködő":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Először kérjen le a virtuális gép tulajdonságainak használata a `Get-AzureRmVM` parancsmagot. Ezután az MSI engedélyezéséhez használja a `-IdentityType` váltani a [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmag:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Adja hozzá az MSI-VM bővítmény használatával a `-Type` paraméterrel a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port. Ügyeljen arra, hogy adja meg a megfelelő `-Location` paraméter, egyező a meglévő virtuális gép helye:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Egy Azure virtuális gép MSI eltávolítása

Ha egy virtuális gépet, amely már nem kell egy olyan MSI Csomaghoz, használhatja a `RemoveAzureRmVMExtension` távolítsa el a virtuális gép MSI-parancsmagot:

1. Jelentkezzen be Azure-bA `Connect-AzureRmAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális Gépen, például a "Virtuális gép közreműködő":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Használja a `-Name` kapcsolja össze a [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) parancsmagot, ugyanazt a bővítmény hozzáadásakor használt név megadásával:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Kapcsolódó tartalom

- [Felügyelt Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md)
- A teljes Azure virtuális gépek létrehozása rövid útmutatók lásd:
  
  - [Windows virtuális gép létrehozása a PowerShell-lel](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](~/articles/virtual-machines/linux/quick-create-powershell.md) 

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.
















