---
title: Lemezek titkosítani az Azure-skálázási készletekben az Azure parancssori felülettel |} Microsoft Docs
description: Az Azure CLI 2.0 használata a Virtuálisgép-példányok és a csatlakoztatott lemezek Linux virtuálisgép-méretezési csoportban lévő titkosításához
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
ms.date: 04/30/2018
ms.author: iainfou
ms.openlocfilehash: 22d3c763317def137b4e0beb155f28585d7c6ae1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-20-preview"></a>Az operációs rendszer és az Azure CLI 2.0 (előzetes verzió) beállítása egy virtuálisgép-méretezési csatolt adatlemezek titkosítása

És iparági szabványos titkosítási technológiával inaktív adatok megvédeni, hogy a virtuálisgép-méretezési csoportok Azure lemez titkosítás (ADE) támogatja. Titkosítási engedélyezhető a Linux és a Windows virtuális gép méretezési készlet. További információkért lásd: [a Linux és a Windows Azure Disk Encryption](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  A virtuálisgép-méretezési csoportok Azure lemeztitkosítás jelenleg nyilvános előzetes verziójában, minden Azure nyilvános régióban elérhető.

Az Azure lemez titkosítást is támogatja:
- a skálázási készletek kezelt lemezek létre, és natív (vagy nem felügyelt) lemez méretezési készlet nem támogatott.
- az operációs rendszer- és adatkötetek számára a Windows-méretezési készlet. Tiltsa le a titkosítást az operációs rendszer- és adatkötetek számára a Windows-méretezési csoportok esetén támogatott.
- a Linux-méretezési csoportok az adatkötetek. Az operációs rendszer lemez titkosítása nem támogatott az aktuális előzetes a Linux-méretezési készlet.

A skálázási készlet VM lemezkép alaphelyzetbe és a frissítési műveletek nem támogatottak az aktuális előzetes. A virtuális gép méretezési készletek Preview Azure lemeztitkosítás csak tesztelési környezetben ajánlott. A kép ne engedélyezze a termelési környezetben, ahol előfordulhat, hogy frissítenie kell egy operációsrendszer-lemezképhez egy titkosított méretezési csoportban lévő adatok titkosítása.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.31 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="register-for-disk-encryption-preview"></a>A lemez titkosítása preview regisztrálása

Az Azure disk encryption virtuálisgép-skálázási készletekben preview rendszert igényel önálló Regisztrálás az előfizetés [az szolgáltatás regisztrálása](/cli/azure/feature#az_feature_register). Csak kell végeznie az alábbi lépéseket a lemez titkosítási előzetes szolgáltatás első használatakor:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

A regisztrációs kérelem való terjesztése akár 10 percet is igénybe vehet. Akkor is ellenőrizhesse az regisztrációs állapotát [az szolgáltatás megjelenítése](/cli/azure/feature#az_feature_show). Ha a `State` jelentések *regisztrált*, regisztrálja újra a *Mirosoft.Compute* szolgáltató [az szolgáltató regisztrálása](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Mielőtt létrehozhatna egy méretezési csoportot, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss#az_vmss_create) paranccsal. A következő példa egy *myScaleSet* nevű méretezési csoportot hoz létre, amely automatikusan frissül a módosítások alkalmazásakor, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznének a *~/.ssh/id_rsa* helyen. 32 GB-os adatlemezt csatolt minden egyes Virtuálisgép-példány és az Azure [egyéni parancsprogramok futtatására szolgáló bővítmény](../virtual-machines/linux/extensions-customscript.md) az adatlemezek való előkészítéséhez használt [az vmss bővítmény set](/cli/azure/vmss/extension#az_vmss_extension_set):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Hozzon létre egy új, a lemez titkosítás engedélyezve van az Azure key vault

Az Azure Key Vault kulcsok, a titkos kulcsok és a jelszót, amely engedélyezi, hogy biztonságosan végrehajtja az alkalmazások és szolgáltatások a tárolhatja. Titkosítási kulcsok Azure Key Vault szoftver-védelemmel vannak tárolva, vagy importálhat vagy a tanúsított FIPS 140-2 2. szint szabványok hardveres biztonsági modulokkal (HSM) a kulcsok létrehozásához. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. A titkosítási kulcsokat a felügyeletet, és naplózhatja a használatukat.

Adja meg a saját egyedi *keyvault_name*. Ezután hozzon létre egy a KeyVault [az keyvault létrehozása](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) előfizetéshez és a régióban legyen, mint a skála állítsa be, és állítsa be a *--engedélyezve-az--lemeztitkosítás* házirendhez.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Használjon egy meglévő kulcstároló

Ez a lépés csak akkor szükséges, hogy van-e egy meglévő kulcstároló, amelyet meg kíván használni az adatok titkosítása. Ha az előző szakaszban létrehozott a kulcstároló, kihagyhatja ezt a lépést.

Adja meg a saját egyedi *keyvault_name*. Akkor módosul a KeyVault rendelkező [az keyvault frissítés](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) és állítsa be a *--engedélyezve-az--lemeztitkosítás* házirendhez.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Titkosítás engedélyezése

Virtuálisgép-példányok méretezési csoportban lévő titkosításához, először néhány adatra a Key Vault erőforrás-azonosító a [az keyvault megjelenítése](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Ezeket a változókat szolgálnak majd indítsa el a titkosítási folyamat [az vmss titkosítás engedélyezése](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Szükség lehet egy percet, amíg a titkosítási folyamat elindításához.

Mivel a méretezési frissítési házirendet, a méretezési készletben létrehozni a korábbi lépésben beállítása *automatikus*, a Virtuálisgép-példányok a titkosítási folyamat automatikusan elindul. A házirend esetén kézi méretezési csoportok, indítsa el a Virtuálisgép-példányok a titkosítási házirend [az vmss frissítés-példányok](/cli/azure/vmss#az-vmss-update-instances).

## <a name="check-encryption-progress"></a>Titkosítási folyamat állapotának ellenőrzése

Lemeztitkosítás állapotának ellenőrzéséhez használja [az vmss titkosítási megjelenítése](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Amikor Virtuálisgép-példányok titkosítva vannak, az állapotkód: jelentések *EncryptionState/titkosított*, ahogy az a következő egy példa a kimenetre:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Tiltsa le a titkosítást

Ha már nem szeretné használni a Virtuálisgép-példányok titkosított lemezek, a titkosítási letilthatja [az vmss titkosítási letiltása](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) az alábbiak szerint:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure CLI 2.0 egy virtuálisgép-méretezési csoport titkosításához használt. Is [Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) vagy a sablonok [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) vagy [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

Linux lemez méretezési készlet adattitkosítási végpont kötegelt fájl példa található [Itt](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Ez a példa létrehoz egy erőforráscsoport, a Linux-méretezési csoport, 5 GB-os adatlemezt csatlakoztatja, és a virtuálisgép-méretezési csoport titkosítja.