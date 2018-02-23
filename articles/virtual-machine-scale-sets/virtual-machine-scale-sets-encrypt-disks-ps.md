---
title: "Az Azure virtuálisgép-méretezési csoportok lemez titkosítása |} Microsoft Docs"
description: "Ismerje meg, hogy a virtuálisgép-méretezési csoportok csatlakoztatott lemezek titkosítása."
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
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: dddcece9f7566961b256369330661e5dbd5d4665
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Az operációs rendszer és a virtuálisgép-méretezési csoportban lévő csatolt adatlemezek titkosítása
Azure [virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets/) támogatja az Azure disk encryption (ADE).  A Windows Azure lemez titkosítása akkor engedélyezhető, és Linux virtuálisgép-méretezési beállítja és a skála megvédeni az adatok iparági szabványos titkosítási technológiával aktívan állítja be. További információkért olvassa el a lemez titkosítás a Windows Azure és a Linux virtuális gépek.

> [!NOTE]
>  A virtuálisgép-méretezési csoportok Azure lemeztitkosítás jelenleg nyilvános előzetes verziójában, minden Azure nyilvános régióban elérhető.

Az Azure lemez titkosítást is támogatja:
- a skálázási készletek kezelt lemezek létre, és natív (vagy nem felügyelt) lemez méretezési készlet nem támogatott.
- az operációs rendszer- és adatkötetek számára a Windows-méretezési készlet. Tiltsa le a titkosítást az operációs rendszer- és adatkötetek számára a Windows-méretezési csoportok esetén támogatott.
- a Linux-méretezési csoportok az adatkötetek. Az operációs rendszer lemez titkosítása nem támogatott az aktuális előzetes a Linux-méretezési készlet.

A skálázási készlet VM lemezkép alaphelyzetbe és a frissítési műveletek nem támogatottak az aktuális előzetes. A virtuális gép méretezési készletek Preview Azure lemeztitkosítás csak tesztelési környezetben ajánlott. A kép ne engedélyezze a termelési környezetben, ahol előfordulhat, hogy frissítenie kell egy operációsrendszer-lemezképhez egy titkosított méretezési csoportban lévő adatok titkosítása.

## <a name="prerequisites"></a>Előfeltételek
Telepítse a legújabb verzióit [Azure Powershell](https://github.com/Azure/azure-powershell/releases), amely tartalmazza a titkosítási parancsokat.

A virtuális gép méretezési készletek Preview Azure lemeztitkosítás szükséges, hogy az előfizetés a következő PowerShell-parancsokkal önálló regisztrálása: 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Várjon körülbelül 10 percig, amíg a "Regisztrált" állapotú ad vissza a következő parancsot: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Hozzon létre egy új, a lemez titkosítás engedélyezve van az Azure key vault
Hozzon létre egy új kulcstartó ugyanazt az előfizetést és régió szerint a "EnabledForDiskEncryption" hozzáférési házirend és a skála.

```powershell
$rgname="windatadiskencryptiontest"
$VaultName="encryptionvault321"

New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $rgName -Location southcentralus -EnabledForDiskEncryption
``` 

Vagy egy meglévő kulcstároló azonos előfizetésbe és régió engedélyezze a méretezési készletben, a lemez titkosításához.

```powershell
$VaultName="encryptionvault321"
Set-AzureRmKeyVaultAccessPolicy -VaultName $VaultName -EnabledForDiskEncryption
```

## <a name="enable-encryption"></a>Titkosítás engedélyezése
A következő parancsokat egy futó méretezési készletben a kulcstároló használatát ugyanabban az erőforráscsoportban lévő adatlemezt titkosításához. Használhatja a sablonok futó lemezek titkosításához [Windows méretezési](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) vagy [Linux méretezési](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
$DiskEncryptionKeyVaultUrl="https://encryptionvault321.vault.azure.net"
$KeyVaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/windatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvault321"

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName `
    -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId –VolumeType Data
```

## <a name="check-encryption-progress"></a>Titkosítási folyamat állapotának ellenőrzése
Az alábbi parancsokkal a méretezési titkosítási állapotának megjelenítése.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName -InstanceId "4"
```

## <a name="disable-encryption"></a>Tiltsa le a titkosítást
Tiltsa le a titkosítást skálán futó virtuális gép beállítása a következő parancsok használatával. Használhatja a sablonok futó titkosítás letiltásához [Windows méretezési](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) vagy [Linux méretezési](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux).

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName
```
