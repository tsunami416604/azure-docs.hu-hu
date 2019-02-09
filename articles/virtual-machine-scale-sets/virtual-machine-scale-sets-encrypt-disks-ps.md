---
title: Lemezek titkosítása az Azure méretezési csoportokhoz Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a Powershellt Virtuálisgép-példányok és a egy Windows virtuálisgép-méretezési csoportot a csatlakoztatott lemezek titkosítása
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: cynthn
ms.openlocfilehash: cc1405d2dd972aff6091a9d5b60ff9da18185286
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978102"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Az operációs rendszer és az Azure PowerShell használatával (előzetes verzió) egy virtuálisgép-méretezési csoport a csatlakoztatott adatlemezekkel titkosítása

És iparági szabványos titkosítási technológiával az inaktív adatok megvédeni, a virtual machine scale sets támogatja az Azure Disk Encryption (ADE). Titkosítási Windows és Linux rendszerű virtuális gép esetén is engedélyezhető a méretezési csoportokat. További információkért lásd: [Azure Disk Encryption a Windows és Linux rendszerű](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Virtuálisgép-méretezési csoportokhoz az Azure disk encryption jelenleg nyilvános előzetes verzióként, elérhető minden nyilvános Azure-régióban.

Az Azure disk encryption támogatják:
- a méretezési csoportok felügyelt lemezekkel létrehozott, és natív (vagy nem felügyelt) lemezek méretezési csoportok esetében nem támogatott.
- Windows méretezési csoportokban lévő operációsrendszer- és kötetek. Tiltsa le titkosítás támogatott operációsrendszer- és kötetek Windows-méretezési csoportokhoz.
- a Linux rendszerű méretezési adatkötetnél. Az operációs rendszer lemeztitkosítás nem támogatott a Linux rendszerű méretezési csoportokhoz az aktuális előzetes verzióban érhető el.

Skálázási készlet virtuális gép rendszerkép alaphelyzetbe állítását és a frissítési műveletek nem támogatottak az aktuális előzetes verzióban érhető el. A virtuális gép méretezési csoportok előzetes verzióra az Azure disk encryption csak tesztelési környezetben ajánlott. Az előzetes verzióban ne engedélyezze a lemeztitkosítás az éles környezetben, ahol előfordulhat, hogy frissíteni szeretne egy titkosított méretezési csoportban lévő egy operációsrendszer-lemezképben.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]


## <a name="register-for-disk-encryption-preview"></a>Lemez titkosítása előzetes regisztráció

Az Azure disk encryption virtuálisgép-méretezési csoportokhoz előzetes kell önálló regisztrálja az előfizetését [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature). Csak akkor kell hajtsa végre a következő lépéseket a lemeztitkosítási előzetes szolgáltatás első használatakor:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```


A regisztrációs kérelem propagálása akár 10 percet is igénybe vehet. A regisztrációs állapot ellenőrzéséhez [Get-AzProviderFeature](/powershell/module/az.resources/Get-AzProviderFeature). Ha a `RegistrationState` jelentések *regisztrált*, regisztrálja újra a *Microsoft.Compute* szolgáltató [Register-AzResourceProvider](/powershell/module/az.resources/Register-AzResourceProvider):


```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Hozzon létre egy Azure Key Vault engedélyezve a lemeztitkosítás

Az Azure Key Vaultban tárolhatók, kulcsok, titkos kódok és jelszavak, amelyek lehetővé teszik, hogy biztonságosan végrehajtja azokat az alkalmazásokat és szolgáltatásokat. Titkosítási kulcsok Azure Key Vault szoftver védelemmel vannak tárolva, vagy Ön importálhat vagy hozhat létre a kulcsokat hardveres biztonsági modulokban (HSM) certified FIPS 140-2 szabványnak megfelelő 2. szint. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. Ezek a titkosítási kulcsok feletti ellenőrzés megtartása és naplózhatja azok használatát.

A Key Vault létrehozása [új AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Ahhoz, hogy a Key Vault lemeztitkosítás használandó, állítsa be a *EnabledForDiskEncryption* paraméter. A következő példában változókat erőforráscsoport-nevet, a kulcstartó nevét és helyét is meghatározza. Adja meg a saját egyedi Key Vault-névre:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Egy meglévő a Key Vault használata

Ez a lépés csak akkor van szükség, ha egy meglévő Key Vault használata a lemeztitkosítás kívánt. Hagyja ki ezt a lépést, ha az előző szakaszban létrehozott egy Key Vaultot.

Engedélyezheti egy meglévő Key Vault azonos előfizetésben és régióban, a méretezési csoport és lemeztitkosításhoz [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Határozza meg a Kulcstárolónak a nevét a *$vaultName* változót az alábbiak szerint:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuálisgép-példányok rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [New-AzVmss](/powershell/module/az.compute/new-azvmss). A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A terheléselosztó olyan szabályokat tartalmaz, amelyek elosztják a 80-as TCP-porton beérkező forgalmat, valamint lehetővé teszi a távoli asztali forgalmat a 3389-es TCP-porton és a PowerShell távoli eljáráshívást az 5985-ös TCP-porton:

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

Egy méretezési csoportban lévő Virtuálisgép-példányok titkosításához, először néhány adatra a Key Vault URI és az erőforrás-azonosító [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Ezeket a változókat használják, majd indítsa el a titkosítási folyamat a [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Amikor a rendszer kéri, írja be a *y* folytatja, a titkosítási folyamat a méretezési csoport a virtuális gép beállítása példányok.

## <a name="check-encryption-progress"></a>Ellenőrizheti a titkosítási folyamatot

Lemeztitkosítás állapotának ellenőrzéséhez használja [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Ha Virtuálisgép-példányok titkosítva vannak, a *EncryptionSummary* jelentések code *ProvisioningState/sikeres* az alábbi példa kimenetében látható módon:

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

## <a name="disable-encryption"></a>Tiltsa le a titkosítást

Ha már nem szeretne használni a Virtuálisgép-példányok titkosított lemezek, letilthatja a-titkosítással [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) módon:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben, az Azure PowerShell titkosításához használt virtuálisgép-méretezési csoportot. Is használhatja a [Azure CLI-vel](virtual-machine-scale-sets-encrypt-disks-cli.md) vagy sablonok [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) vagy [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
