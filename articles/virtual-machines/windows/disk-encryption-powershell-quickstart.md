---
title: Windows rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és titkosíthat Windows rendszerű virtuális gépeket a Azure PowerShell használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a50b62a7c6064672dfbf7d609b6053d7be6fdb77
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89079489"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Gyors útmutató: Windows rendszerű virtuális gép létrehozása és titkosítása az Azure-ban a PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja a Azure PowerShell modult egy Windows rendszerű virtuális gép (VM) létrehozásához, a titkosítási kulcsok tárolásához Key Vault létrehozásához és a virtuális gép titkosításához. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoportok olyan logikai tárolók, amelyekbe a rendszer üzembe helyezi az Azure-erőforrásokat és kezeli azokat:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy Azure-beli virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm). A parancsmaghoz meg kell adnia a hitelesítő adatokat. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

A virtuális gép üzembe helyezése eltarthat néhány percig. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási kulcsokhoz konfigurált Key Vault létrehozása

Az Azure Disk Encryption egy Azure Key Vault tárolja a titkosítási kulcsát. Hozzon létre egy Key Vaultt a [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Ha engedélyezni szeretné a Key Vault számára a titkosítási kulcsok tárolását, használja a-EnabledForDiskEncryption paramétert.

> [!Important]
> Minden Key Vault egyedi névvel kell rendelkeznie. Az alábbi példa egy *myKV*nevű Key Vault hoz létre, de a tiéd nevet kell megadnia.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

Titkosítsa a virtuális gépet a [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

A set-AzVmDiskEncryptionExtension-nek néhány értéket kell megadnia a Key Vault objektumtól. Ezeket az értékeket a Key Vault egyedi nevének a [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault)értékre való átadásával szerezheti be.

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Néhány perc elteltével a folyamat a következőt fogja visszaadni:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

A [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)futtatásával ellenőrizheti a titkosítási folyamatot.

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Ha engedélyezve van a titkosítás, a visszaadott kimenetben a következő jelenik meg:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmagot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, létrehozott egy Key Vault, amely engedélyezte a titkosítási kulcsokat, és titkosította a virtuális gépet.  Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Disk Encryption előfeltétel-konfigurációs szkriptjeiről az IaaS virtuális gépek esetében.

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
