---
title: Lemezek titkosítása Windows virtuális gépen az Azure-ban
description: Virtuális lemezek titkosítása Windows virtuális gépen a fokozott biztonság érdekében az Azure PowerShell használatával
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 096fe779c077424b01df3ead5965cb799866a03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033531"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Virtuális lemezek titkosítása Windows virtuális gépen
A továbbfejlesztett virtuálisgép-biztonság és megfelelőség érdekében az Azure-beli virtuális lemezek titkosíthatók. A lemezek titkosítása az Azure Key Vaultban védett kriptográfiai kulcsokkal történik. Ön vezérelheti ezeket a titkosítási kulcsokat, és naplózhatja azok használatát. Ez a cikk ismerteti, hogyan titkosíthatja a virtuális lemezek a Windows virtuális gép en az Azure PowerShell használatával. Azt is [titkosítja a Linux virtuális gépek](../linux/disk-encryption-overview.md).

 

## <a name="overview-of-disk-encryption"></a>Lemeztitkosítás – áttekintés
A Windows virtuális gépeken lévő virtuális lemezek in-t a BitLocker használatával titkosítva vannak. Az Azure-ban a virtuális lemezek titkosítása díjmentes. A kriptográfiai kulcsokat szoftvervédelem melinvel tárolják az Azure Key Vaultban, vagy importálhatja vagy létrehozhatja a kulcsokat a FIPS 140-2 szintű 2 szabványainak megfelelő Hardverbiztonsági modulokban (HSM). A kriptográfiai kulcsok a virtuális géphez csatlakoztatott virtuális lemezek titkosítására és visszafejtésére szolgálnak. Ön tartja az irányítást ezek felett a titkosítási kulcsok, és auditálhatja azok használatát. 

A virtuális gép titkosításának folyamata a következő:

1. Hozzon létre egy kriptográfiai kulcsot egy Azure Key Vaultban.
1. Állítsa be úgy a kriptográfiai kulcsot, hogy használható legyen a lemezek titkosításához.
1. Engedélyezze a lemeztitkosítást a virtuális lemezeken.
1. A szükséges titkosítási kulcsokat az Azure Key Vault kéri.
1. A virtuális lemezek titkosítva vannak a megadott titkosítási kulccsal.


## <a name="requirements-and-limitations"></a>Követelmények és korlátozások

A lemeztitkosítás támogatott forgatókönyvei és követelményei:

* Titkosítás engedélyezése az Azure Marketplace-ről származó új Windows virtuális gépeken vagy egyéni virtuális merevlemez-lemezképeken.
* Titkosítás engedélyezése meglévő Windows virtuális gépeken az Azure-ban.
* Titkosítás engedélyezése a Tárolóhelyek használatával konfigurált Windows virtuális gépeken.
* A titkosítás letiltása operációs rendszerés adatmeghajtókon Windows virtuális gépeken.
* Standard szintű virtuális gépek, például A, D, DS, G és GS sorozatú virtuális gépek.

    > [!NOTE]
    > Minden erőforrásnak (beleértve a Key Vaultot, a storage-fiókot és a virtuális gépet) ugyanabban az Azure-régióban és előfizetésben kell lennie.

A lemeztitkosítás jelenleg nem támogatott a következő esetekben:

* Alapszintű virtuális gépek.
* A klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek.
* A titkosítási kulcsok frissítése egy már titkosított virtuális gépen.
* Integráció a helyszíni kulcskezelő szolgáltatással.


## <a name="create-an-azure-key-vault-and-keys"></a>Azure-kulcstároló és kulcsok létrehozása
Mielőtt elkezdené, győződjön meg arról, hogy az Azure PowerShell-modul legújabb verziója telepítve van. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. A következő parancspéldákban cserélje le az összes példaparamétert a saját nevével, helyével és kulcsértékeivel, például *a myResourceGroup*, *a myKeyVault*, *a myVM*és így tovább.

Az első lépés egy Azure Key Vault a titkosítási kulcsok tárolására. Az Azure Key Vaults olyan kulcsokat, titkos kulcsokat vagy jelszavakat tárolhat, amelyek lehetővé teszik, hogy biztonságosan megvalósítsa azokat az alkalmazásokban és a szolgáltatásokban. A virtuális lemez titkosításához hozzon létre egy Key Vault-ot a virtuális lemezek titkosítására vagy visszafejtésére használt kriptográfiai kulcs tárolására. 

Engedélyezze az Azure Key Vault-szolgáltatót az Azure-előfizetésen belül a [Register-AzResourceProvider szolgáltatással,](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)majd hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)szolgáltatással. A következő példa létrehoz *myResourceGroup* egy sajka erőforráscsoport nevet az *USA keleti részén:*

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Az Azure Key Vault a kriptográfiai kulcsok és a kapcsolódó számítási erőforrások, például a tárolás és a virtuális gép maga kell minden ugyanabban a régióban. Hozzon létre egy Azure Key Vault [new-azkeyvault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) és engedélyezi a Key Vault használható lemeztitkosítás. Adja meg a *keyVaultName* egyedi Key Vaultname-nevét az alábbiak szerint:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

A kriptográfiai kulcsokat szoftveres vagy hardverbiztonsági modell (HSM) védelemmel tárolhatja.  A szabványos Key Vault csak szoftveresen védett kulcsokat tárol. A HSM használatához felár ellenében prémium szintű Key Vaultra van szükség. Prémium szintű Key Vault létrehozásához az előző lépésben adja hozzá a *-Sku "Premium"* paramétert. A következő példa szoftveresen védett kulcsokat használ, mivel létrehoztunk egy szabványos Key Vaultot. 

Mindkét védelmi modell esetében az Azure platform hozzáférést kell biztosítani a kriptográfiai kulcsok kéréséhez, amikor a virtuális gép elindul a virtuális lemezek visszafejtéséhez. Hozzon létre egy kriptográfiai kulcsot a Key Vault-ban [az Add-AzureKeyVaultKey segítségével.](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) A következő példa létrehoz egy *myKey*nevű kulcsot:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
A titkosítási folyamat teszteléséhez hozzon létre egy virtuális gép [new-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). A következő példa létrehoz egy *myVM* nevű virtuális gép egy *Windows Server 2016* Datacenter-lemezképet. Amikor a rendszer hitelesítő adatokat kér, adja meg a virtuális géphez használandó felhasználónevet és jelszót:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Virtuális gép titkosítása
Titkosítsa a virtuális gép [set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) az Azure Key Vault kulcs használatával. A következő példa beolvassa az összes kulcsfontosságú információt, majd titkosítja a virtuális gép nevű *myVM:*

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Fogadja el a kérdést a virtuális gép titkosításának folytatásához. A virtuális gép újraindul a folyamat során. Miután a titkosítási folyamat befejeződött, és a virtuális gép újraindult, tekintse át a titkosítási [állapotget-AzVmDiskEncryptionStatus:](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus)

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

A kimenet a következő példához hasonló:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>További lépések
* Az Azure Key Vault kezeléséről a [Key Vault beállítása virtuális gépekhez](key-vault-setup.md)című témakörben talál további információt.
* A lemeztitkosításról, például egy titkosított egyéni virtuális gép Azure-ba való feltöltésének előkészítéséről az [Azure lemeztitkosítás című](../../security/fundamentals/encryption-overview.md)témakörben talál további információt.
