---
title: Windows rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre és titkosíthat az Azure PowerShell t a Windows virtuális gépek létrehozásához és titkosításához
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 6f564a9a4f13136bbe7e28a3600ca71892c82439
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081591"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Rövid útmutató: Windows os virtuális gépek létrehozása és titkosítása az Azure-ban a PowerShell segítségével

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure PowerShell-modult egy Windows virtuális gép (VM) létrehozásához, hozzon létre egy Key Vaultot a titkosítási kulcsok tárolásához, és titkosítsa a virtuális gépet. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup segítségével.](/powershell/module/az.resources/new-azresourcegroup) Az erőforráscsoportok olyan logikai tárolók, amelyekbe a rendszer üzembe helyezi az Azure-erőforrásokat és kezeli azokat:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy Azure virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm)segítségével. Meg kell adnia a parancsmag hitelesítő adatait. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

A virtuális gép üzembe helyezése eltarthat néhány percig. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási kulcsokhoz konfigurált kulcstartó létrehozása

Az Azure lemeztitkosítás tárolja a titkosítási kulcsot egy Azure Key Vaultban. Hozzon létre egy key vault [new-azkeyvault](/powershell/module/az.keyvault/new-azkeyvault). Ha engedélyezni szeretné, hogy a Key Vault tárolja a titkosítási kulcsokat, használja az -EnabledForDiskEncryption paramétert.

> [!Important]
> Minden Key Vault kell egy egyedi nevet. A következő példa létrehoz egy *myKV*nevű Key Vault-ot, de a tiédet valami másnak kell elneveznie.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

Titkosítsa a virtuális gép [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

A Set-AzVmDiskEncryptionExtension bizonyos értékeket igényel a Key Vault-objektumból. Ezeket az értékeket úgy szerezheti be, hogy átadja a kulcstartó egyedi nevét a [Get-AzKeyvault rendszernek.](/powershell/module/az.keyvault/get-azkeyvault)

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Néhány perc múlva a folyamat a következőt adja vissza:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

A titkosítási folyamatot a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)futtatásával ellenőrizheti.

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Ha a titkosítás engedélyezve van, a visszaadott kimenetben a következő látható lesz:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmaggal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, létrehozott egy Key Vaultot, amely engedélyezte a titkosítási kulcsokat, és titkosította a virtuális gépet.  Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Disk Encryption előfeltétel-konfigurációs szkriptjeiről az IaaS virtuális gépek esetében.

> [!div class="nextstepaction"]
> [Azure lemeztitkosítás – áttekintés](disk-encryption-overview.md)