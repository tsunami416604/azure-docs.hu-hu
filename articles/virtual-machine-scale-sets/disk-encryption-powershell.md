---
title: Az Azure PowerShell használatával az Azure Scale-készletek lemezeinek titkosítása
description: Megtudhatja, hogy az Azure PowerShell használatával hogyan titkosíthatja a virtuálisgép-példányokat és a csatlakoztatott lemezeket egy Windows virtuálisgép-méretezési csoportban
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: bd7f92c104e06896f4b3c8bb2adef45983cf5d4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278985"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Az operációs rendszer és a csatlakoztatott adatlemezek titkosítása egy virtuálisgép-méretezési csoportban az Azure PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható.  Ez a cikk bemutatja, hogyan azure PowerShell használatával hozzon létre és titkosítson egy virtuális gép méretezési csoport. Az Azure Disk Encryption virtuálisgép-méretezési csoportra történő alkalmazásáról az [Azure Lemezesítés a virtuálisgép-méretezési készletekhez című témakörben talál](disk-encryption-overview.md)további információt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Lemeztitkosításhoz engedélyezett Azure Key Vault létrehozása

Az Azure Key Vault olyan kulcsokat, titkos kulcsokat vagy jelszavakat tárolhat, amelyek lehetővé teszik azok biztonságos megvalósítását az alkalmazásokban és a szolgáltatásokban. A kriptográfiai kulcsok at az Azure Key Vault szoftvervédelemmel tárolja, vagy importálhatja vagy létrehozhatja a kulcsokat a HARDVERbiztonsági modulok (HSM) FIPS 140-2 szintű 2 szabványoknak megfelelően hitelesített hardverbiztonsági modulokban.Cryptographic keys are stored in Azure Key Vault using software-protection, or you can import or generate your keys in Hardware Security Modules (HSM) certified to FIPS 140-2 level 2 standards. Ezek a kriptográfiai kulcsok a virtuális géphez csatlakoztatott virtuális lemezek titkosítására és visszafejtésére szolgálnak. Ön megtartja a titkosítási kulcsok feletti ellenőrzést, és naplózhatja azok használatát.

Hozzon létre egy key vault [new-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Ha engedélyezni szeretné a Key Vault lemeztitkosításhoz való használatát, állítsa be az *EnabledForDiskEncryption* paramétert. A következő példa az erőforráscsoport nevének, a kulcstároló nevének és helyének változóit is meghatározza. Adja meg saját egyedi Key Vault-nevét:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Meglévő kulcstartó használata

Ez a lépés csak akkor szükséges, ha rendelkezik egy meglévő Key Vault, amely lemeztitkosítással használni kívánt. Hagyja ki ezt a lépést, ha az előző szakaszban létrehozott egy key vaultot.

A [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy)segítségével engedélyezheti a meglévő Kulcstartót ugyanabban az előfizetésben és régióban, mint a lemeztitkosítás méretezési készletét. Adja meg a meglévő Key Vault nevét a *$vaultName* változóban az alábbiak szerint:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuálisgép-példányok rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre egy virtuális gép méretezési készlet [New-AzVmss](/powershell/module/az.compute/new-azvmss). A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A terheléselosztó olyan szabályokat tartalmaz, amelyek elosztják a 80-as TCP-porton beérkező forgalmat, valamint lehetővé teszi a távoli asztali forgalmat a 3389-es TCP-porton és a PowerShell távoli eljáráshívást az 5985-ös TCP-porton:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Titkosítás engedélyezése

A virtuálisgép-példányok méretezési csoportban való titkosításához először szerezzen be néhány információt a Key Vault URI-jával és az erőforrás-azonosítóról a [Get-AzKeyVault segítségével.](/powershell/module/az.keyvault/Get-AzKeyVault) Ezek a változók a [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension)segítségével indítják el a titkosítási folyamatot:


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Amikor a rendszer kéri, írja be *az y* beírását a lemeztitkosítási folyamat folytatásához a méretezési csoport virtuálisgép-példányain.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Titkosítás engedélyezése a KEK használatával a kulcs burkolórendszeréhez

A virtuálisgép-méretezési csoport titkosításakor kulcstitkosítási kulcsot is használhat a nagyobb biztonság érdekében.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> A kulcstitkosítási kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Titkosítási folyamat ellenőrzése

A lemeztitkosítás állapotának ellenőrzéséhez használja a [Get-AzVmssDiskEncryption titkosítást:](/powershell/module/az.compute/Get-AzVmssDiskEncryption)


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Ha a vm-példányok titkosítva vannak, a *EncryptionSummary* kódjelentések *ProvisioningState/succeeded* a következő példa kimeneti:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Titkosítás letiltása

Ha a továbbiakban nem kíván titkosított virtuálisgép-példánylemezeket használni, az [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) titkosítással az alábbi műveleteket tilthatja le:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>További lépések

- Ebben a cikkben az Azure PowerShell használatával titkosítja a virtuális gép méretezési csoport. Az [Azure CLI](disk-encryption-cli.md) vagy az Azure Resource Manager sablonokat is [használhatja.](disk-encryption-azure-resource-manager.md)
- Ha azt szeretné, hogy az Azure Disk Encryption egy másik bővítmény kiépítése után alkalmazva legyen, [használhatja a bővítmény-szekvenálást.](virtual-machine-scale-sets-extension-sequencing.md)
