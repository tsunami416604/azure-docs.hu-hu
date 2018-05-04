---
title: Az Azure virtuálisgép-méretezési csoportok lemez titkosítása |} Microsoft Docs
description: Ismerje meg, hogy a virtuálisgép-méretezési csoportok csatlakoztatott lemezek titkosítása.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: 570764ad5d657a8b1efa2425423a89ddc518451c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
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

Linux lemez méretezési készlet adattitkosítási végpont kötegelt fájl példa található [Itt](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat).  Ez a példa létrehoz egy erőforráscsoport, a Linux-méretezési csoport, 5 GB-os adatlemezt csatlakoztatja, és a virtuálisgép-méretezési csoport titkosítja.

## <a name="prerequisites"></a>Előfeltételek
Telepítse a legújabb verzióit [Azure Powershell](https://github.com/Azure/azure-powershell/releases) vagy [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), a titkosítási parancsokat tartalmazza.

A virtuális gép méretezési készletek Preview Azure lemeztitkosítás szükséges, hogy az előfizetés a következő PowerShell-parancsokkal önálló regisztrálása: 

```powershell
Connect-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Várjon körülbelül 10 percig, amíg a "Regisztrált" állapotú ad vissza a következő parancsot: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Hozzon létre egy új, a lemez titkosítás engedélyezve van az Azure key vault
Hozzon létre egy új kulcstartó ugyanazt az előfizetést és régió szerint a "EnabledForDiskEncryption" hozzáférési házirend és a skála.

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

Vagy egy meglévő kulcstároló azonos előfizetésbe és régió engedélyezze a méretezési készletben, a lemez titkosításához.

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Titkosítás engedélyezése

A következő parancsokat egy futó méretezési készletben a kulcstároló használatát ugyanabban az erőforráscsoportban lévő adatlemezt titkosításához. Használhatja a sablonok futó lemezek titkosításához [Windows méretezési](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) vagy [Linux méretezési](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>Titkosítási folyamat állapotának ellenőrzése

Az alábbi parancsokkal a méretezési titkosítási állapotának megjelenítése.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>Tiltsa le a titkosítást
Tiltsa le a titkosítást skálán futó virtuális gép beállítása a következő parancsok használatával. Használhatja a sablonok futó titkosítás letiltásához [Windows Virtuálisgép-méretezési készlet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) vagy [Linux Virtuálisgép-méretezési készlet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux).

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

