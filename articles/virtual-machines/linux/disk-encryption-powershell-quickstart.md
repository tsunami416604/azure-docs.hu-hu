---
title: Linuxos virtuális gép létrehozása és titkosítása az Azure Powershell használatával
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre és titkosíthat linuxos virtuális gépet az Azure Powershell használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: a2cb8919ac0752c42f22e064d6201c7120fbc9b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970552"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Rövid útmutató: Linuxos virtuális gép létrehozása és titkosítása az Azure PowerShell segítségével

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure PowerShell-modult egy Linux virtuális gép (VM) létrehozásához, hozzon létre egy Key Vaultot a titkosítási kulcsok tárolásához, és titkosítsa a virtuális gépet. Ez a rövid útmutató az Ubuntu 16.04 LTS piactéri lemezképet használja a Canonical és a VM Standard_D2S_V3 mérete. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup segítségével.](/powershell/module/az.resources/new-azresourcegroup) Az erőforráscsoportok olyan logikai tárolók, amelyekbe a rendszer üzembe helyezi az Azure-erőforrásokat és kezeli azokat:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy Azure virtuális gépet [a New-AzVM-mel,](/powershell/module/az.compute/new-azvm)és adja át a fent létrehozott virtuális gép konfigurációs objektumot.

```powershell-interactive
$cred = = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest -Size Standard_D2S_V3
```

A virtuális gép üzembe helyezése eltarthat néhány percig. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási kulcsokhoz konfigurált kulcstartó létrehozása

Az Azure lemeztitkosítás tárolja a titkosítási kulcsot egy Azure Key Vaultban. Hozzon létre egy key vault [new-azkeyvault](/powershell/module/az.keyvault/new-azkeyvault). Ha engedélyezni szeretné, hogy a Key Vault tárolja a titkosítási kulcsokat, használja az -EnabledForDiskEncryption paramétert.

> [!Important]
> Minden key vault kell egy nevet, amely egyedi az Azure-ban. Az alábbi példákban cserélje le <egyedi keyvault-neve> a választott nevet.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

Titkosítsa a virtuális gép [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

A Set-AzVmDiskEncryptionExtension bizonyos értékeket igényel a Key Vault-objektumból. Ezeket az értékeket úgy szerezheti be, hogy átadja a kulcstartó egyedi nevét a [Get-AzKeyvault rendszernek.](/powershell/module/az.keyvault/get-azkeyvault)

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Néhány perc múlva a folyamat a következőt adja vissza:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

A titkosítási folyamatot a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)futtatásával ellenőrizheti.

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Ha a titkosítás engedélyezve van, a visszaadott kimenetben a következő látható lesz:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmaggal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, létrehozott egy Key Vaultot, amely engedélyezte a titkosítási kulcsokat, és titkosította a virtuális gépet.  Továbbad a következő cikkhez, hogy többet tudjon meg az Azure Disk Encryption linuxos virtuális gépekhez.

> [!div class="nextstepaction"]
> [Azure lemeztitkosítás – áttekintés](disk-encryption-overview.md)