---
title: "Az Azure virtuálisgép-méretezési beállítja a lemez titkosítása |} Microsoft Docs"
description: "Virtuálisgép-példányok és a virtuálisgép-méretezési csoportok csatlakoztatott lemezek titkosítására Azure PowerShell használata"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: iainfou
ms.openlocfilehash: 856d4bc7dd636b3a2f3d072a10989cafd7efd6a6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Az operációs rendszer és a virtuálisgép-méretezési csoportban lévő csatolt adatlemezek titkosítása
És iparági szabványos titkosítási technológiával inaktív adatok megvédeni, hogy a virtuálisgép-méretezési csoportok Azure lemez titkosítás (ADE) támogatja. Titkosítási engedélyezhető a Windows és Linux rendszerű virtuális gép méretezési készlet. További információkért lásd: [lemez titkosítás a Windows Azure és a Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  A virtuálisgép-méretezési csoportok Azure Disk Encryption jelenleg előzetes, minden Azure nyilvános régióban elérhető. 
>
> A skálázási készlet VM lemezkép alaphelyzetbe és a frissítési műveletek nem támogatottak az aktuális előzetes. A képen méretezési készlet titkosítás csak tesztelési környezetben ajánlott. A kép ne engedélyezze a lemeztitkosítás éles környezetben, ahol lehetséges, hogy frissítenie kell egy operációsrendszer-lemezképben.

Az Azure lemez titkosítást is támogatja:
- a skálázási készletek kezelt lemezek létre, és natív (vagy nem felügyelt) lemez méretezési készlet nem támogatott.
- az operációs rendszer- és adatkötetek számára a Windows-méretezési készlet. Tiltsa le a titkosítást az operációs rendszer- és adatkötetek számára a Windows-méretezési csoportok esetén támogatott.
- a Linux-méretezési csoportok az adatkötetek. Az operációs rendszer lemez titkosítása nem támogatott az aktuális előzetes a Linux-méretezési készlet.


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk az Azure PowerShell modul verziója 5.3.0 szükséges vagy újabb. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Az Azure subsription az adatok titkosítása a virtuálisgép-méretezési előnézete beállítja a register [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature): 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Várjon, amíg körülbelül 10 percig a *regisztrált* által visszaadott állapotát [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature), majd regisztrálja a `Microsoft.Compute` szolgáltató: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```


## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Hozzon létre egy Azure Key Vault lemez titkosítás engedélyezve
Az Azure Key Vault kulcsok, a titkos kulcsok és a jelszót, amely engedélyezi, hogy biztonságosan végrehajtja az alkalmazások és szolgáltatások a tárolhatja. Titkosítási kulcsok Azure Key Vault szoftver-védelemmel vannak tárolva, vagy importálhat vagy a tanúsított FIPS 140-2 2. szint szabványok hardveres biztonsági modulokkal (HSM) a kulcsok létrehozásához. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. A titkosítási kulcsokat a felügyeletet, és naplózhatja a használatukat.

Hozzon létre egy kulcstartót a [új-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). A Key Vault a lemez titkosításához használt engedélyezéséhez állítsa a *EnabledForDiskEncryption* paraméter. Az alábbi példa is erőforráscsoport-név, kulcsot tároló neve és helye változó határozza meg. Adja meg a saját egyedi kulcstároló neve:

```powershell
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```


### <a name="use-an-existing-key-vault"></a>Használjon egy meglévő kulcstároló
Ez a lépés csak akkor szükséges, hogy van-e egy meglévő kulcstároló, amelyet meg kíván használni az adatok titkosítása. Ha az előző szakaszban létrehozott a kulcstároló, kihagyhatja ezt a lépést.

A méretezési készletben lemez titkosításra, engedélyezheti az előfizetés és régió egy meglévő kulcstároló [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). A Kulcstárolónak a nevét adja meg a *$vaultName* változót az alábbiak szerint:

```powershell
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Első lépésként állítsa be a rendszergazda felhasználónevét és jelszavát a Virtuálisgép-példányok [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Most hozzon létre egy virtuálisgép-méretezési csoportot a [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) paranccsal. A Virtuálisgép-példányokhoz felé irányuló forgalom terjesztéséhez terheléselosztót is létrejön. A load balancer forgalom 80-as TCP-porton terjesztése, valamint a távoli asztali forgalom 3389-es TCP-porton és a TCP-portot az 5985 PowerShell-távelérés engedélyezése szabályokat tartalmazza:

```powershell
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
Virtuálisgép-példányok méretezési csoportban lévő titkosításához, először néhány adatra a Key Vault URI és erőforrás-azonosító [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault). Ezeket a változókat szolgálnak majd indítsa el a titkosítási folyamat [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension):

```powershell
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Amikor a rendszer kéri, írja be a *y* folytatja, a titkosítási folyamat a skálán beállítani a Virtuálisgép-példányok.


## <a name="check-encryption-progress"></a>Titkosítási folyamat állapotának ellenőrzése
Lemeztitkosítás állapotának ellenőrzéséhez használja [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption):

```powershell
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Ha Virtuálisgép-példányok titkosítva vannak, a *EncryptionSummary* jelentések code *ProvisioningState/sikeres* látható módon a következő egy példa a kimenetre:

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
Ha már nem szeretné használni a Virtuálisgép-példányok titkosított lemezek, a titkosítási letilthatja [Disable-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption) az alábbiak szerint:

```powershell
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```


## <a name="next-steps"></a>További lépések
Ebben a cikkben, az Azure PowerShell titkosításához használt virtuálisgép-méretezési készlet. Használhatja a [Azure CLI 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) vagy a sablonok [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) vagy [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
