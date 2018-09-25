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
ms.openlocfilehash: c39df127e07601dbc5125d246f25da22ce4a0e40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981763"
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

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="register-for-disk-encryption-preview"></a>Lemez titkosítása előzetes regisztráció

Az Azure disk encryption virtuálisgép-méretezési csoportokhoz előzetes kell önálló regisztrálja az előfizetését [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature). Csak akkor kell hajtsa végre a következő lépéseket a lemeztitkosítási előzetes szolgáltatás első használatakor:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

A regisztrációs kérelem propagálása akár 10 percet is igénybe vehet. A regisztrációs állapot ellenőrzéséhez [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Ha a `RegistrationState` jelentések *regisztrált*, regisztrálja újra a *Mirosoft.Compute* szolgáltató [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Hozzon létre egy Azure Key Vault engedélyezve a lemeztitkosítás

Az Azure Key Vaultban tárolhatók, kulcsok, titkos kódok és jelszavak, amelyek lehetővé teszik, hogy biztonságosan végrehajtja azokat az alkalmazásokat és szolgáltatásokat. Titkosítási kulcsok Azure Key Vault szoftver védelemmel vannak tárolva, vagy Ön importálhat vagy hozhat létre a kulcsokat hardveres biztonsági modulokban (HSM) certified FIPS 140-2 szabványnak megfelelő 2. szint. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. Ezek a titkosítási kulcsok feletti ellenőrzés megtartása és naplózhatja azok használatát.

A Key Vault létrehozása [új AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Ahhoz, hogy a Key Vault lemeztitkosítás használandó, állítsa be a *EnabledForDiskEncryption* paraméter. A következő példában változókat erőforráscsoport-nevet, a kulcstartó nevét és helyét is meghatározza. Adja meg a saját egyedi Key Vault-névre:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Egy meglévő a Key Vault használata

Ez a lépés csak akkor van szükség, ha egy meglévő Key Vault használata a lemeztitkosítás kívánt. Hagyja ki ezt a lépést, ha az előző szakaszban létrehozott egy Key Vaultot.

Engedélyezheti egy meglévő Key Vault azonos előfizetésben és régióban, a méretezési csoport és lemeztitkosításhoz [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). Határozza meg a Kulcstárolónak a nevét a *$vaultName* változót az alábbiak szerint:

```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuálisgép-példányok rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre egy virtuálisgép-méretezési csoportot a [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) paranccsal. A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A terheléselosztó olyan szabályokat tartalmaz, amelyek elosztják a 80-as TCP-porton beérkező forgalmat, valamint lehetővé teszi a távoli asztali forgalmat a 3389-es TCP-porton és a PowerShell távoli eljáráshívást az 5985-ös TCP-porton:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzureRmVmss `
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

Egy méretezési csoportban lévő Virtuálisgép-példányok titkosításához, először néhány adatra a Key Vault URI és az erőforrás-azonosító [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault). Ezeket a változókat használják, majd indítsa el a titkosítási folyamat a [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension):

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Amikor a rendszer kéri, írja be a *y* folytatja, a titkosítási folyamat a méretezési csoport a virtuális gép beállítása példányok.

## <a name="check-encryption-progress"></a>Ellenőrizheti a titkosítási folyamatot

Lemeztitkosítás állapotának ellenőrzéséhez használja [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption):

```azurepowershell-interactive
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
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

Ha már nem szeretne használni a Virtuálisgép-példányok titkosított lemezek, letilthatja a-titkosítással [Disable-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption) módon:

```azurepowershell-interactive
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben, az Azure PowerShell titkosításához használt virtuálisgép-méretezési csoportot. Is használhatja a [Azure CLI-vel](virtual-machine-scale-sets-encrypt-disks-cli.md) vagy sablonok [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) vagy [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
