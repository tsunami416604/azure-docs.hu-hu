---
title: Hozzon létre, és az Azure PowerShell használatával Windows virtuális gép titkosítása
description: Ez a rövid útmutatóban megismerheti, hogyan hozhat létre, és a egy Windows virtuális gép titkosítása az Azure PowerShell használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: c10c9c6bd757125fa1f91d4442fc1c8afd24d10e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081454"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Gyors útmutató: Hozzon létre, és a egy Windows virtuális gép titkosítása az Azure PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozzon létre egy Windows virtuális gépet (VM), hozzon létre egy Key Vaultot a titkosítási kulcsok tárolásához és a virtuális gép titkosítása az Azure PowerShell-modul segítségével. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoportok olyan logikai tárolók, amelyekbe a rendszer üzembe helyezi az Azure-erőforrásokat és kezeli azokat:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy Azure-beli virtuális gép [New-azvm parancsmag](/powershell/module/az.compute/new-azvm). Meg kell adnia a hitelesítő adatokat a parancsmaghoz. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

A virtuális gép üzembe helyezése eltarthat néhány percig. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási kulcsok konfigurált kulcstartó létrehozása

Az Azure disk encryption az Azure Key Vaultban tárolja a titkosítási kulcs. A Key Vault létrehozása [új AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Tárolja a titkosítási kulcsokat a Key Vault engedélyezéséhez használja a - EnabledForDiskEncryption paramétert.

> [!Important]
> Mindegyik Key Vault egyedi névvel kell rendelkeznie. A következő példában létrehozunk egy Key Vaultot nevű *myKV*, de meg kell Öné más nevet.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

A virtuális gép titkosítása [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension requires some values from your Key Vault object. Ezeket az értékeket az egyedi nevet a key vault való átadásával szerezhet [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Néhány perc múlva a folyamat a következő ad vissza:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

A titkosítási folyamat futtatásával ellenőrizheti [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

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

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmag segítségével távolítsa el az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrást:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, és létrehozott egy kulcstartót, titkosítási kulcsok engedélyezése a virtuális gép titkosított.  Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Disk Encryption előfeltétel-konfigurációs szkriptjeiről az IaaS virtuális gépek esetében.

> [!div class="nextstepaction"]
> [Az Azure Disk Encryption előfeltételei](azure-security-disk-encryption-prerequisites.md)