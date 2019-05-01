---
title: Lemezek titkosítása az Azure méretezési csoportokhoz az Azure CLI-vel |} A Microsoft Docs
description: Az Azure CLI használata a Virtuálisgép-példányok és a egy Linux virtuálisgép-méretezési csoportot a csatlakoztatott lemezek titkosítása
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
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: 1264c7e4ebaf5e948e624fa49dc5fb0b4cdb31f0
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869054"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Az operációs rendszer és a egy virtuálisgép-méretezési csoportba az Azure CLI-vel a csatlakoztatott adatlemezekkel titkosítása

És iparági szabványos titkosítási technológiával az inaktív adatok megvédeni, a virtual machine scale sets támogatja az Azure Disk Encryption (ADE). Titkosítási engedélyezhető a Linux vagy Windows virtuálisgép-méretezési csoportokban. További információkért lásd: [Linux és Windows Azure Disk Encryption](../security/azure-security-disk-encryption.md).

Az Azure disk encryption támogatják:
- a méretezési csoportok felügyelt lemezekkel létrehozott, és natív (vagy nem felügyelt) lemezek méretezési csoportok esetében nem támogatott.
- Windows méretezési csoportokban lévő operációsrendszer- és kötetek. Tiltsa le titkosítás támogatott operációsrendszer- és kötetek Windows-méretezési csoportokhoz.
- a Linux rendszerű méretezési adatkötetnél. Az operációs rendszer lemeztitkosítás nem támogatott a Linux rendszerű méretezési csoportokhoz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a helyi telepítése és használata a parancssori felület választja, az oktatóanyaghoz, hogy futnak-e az Azure CLI 2.0.31-es verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Mielőtt létrehozhatna egy méretezési csoportot, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) paranccsal. A következő példa egy *myScaleSet* nevű méretezési csoportot hoz létre, amely automatikusan frissül a módosítások alkalmazásakor, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznének a *~/.ssh/id_rsa* helyen. A 32 GB méretű adatlemezzel csatolt minden egyes Virtuálisgép-példányhoz, és az Azure [egyéni szkriptek futtatására szolgáló bővítmény](../virtual-machines/linux/extensions-customscript.md) előkészítéséhez az adatlemezek használata használható [az vmss-bővítmény beállítása](/cli/azure/vmss/extension):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Hozzon létre egy Azure key vault engedélyezve a lemeztitkosítás

Az Azure Key Vaultban tárolhatók, kulcsok, titkos kódok és jelszavak, amelyek lehetővé teszik, hogy biztonságosan végrehajtja azokat az alkalmazásokat és szolgáltatásokat. Titkosítási kulcsok Azure Key Vault szoftver védelemmel vannak tárolva, vagy Ön importálhat vagy hozhat létre a kulcsokat hardveres biztonsági modulokban (HSM) certified FIPS 140-2 szabványnak megfelelő 2. szint. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. Ezek a titkosítási kulcsok feletti ellenőrzés megtartása és naplózhatja azok használatát.

Adja meg a saját egyedi *keyvault_name*. Ezután hozzon létre egy KeyVault- [az keyvault létrehozása](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) ugyanazt az előfizetést és a régióban, mint a méretezési csoport, állítsa be, és állítsa be a *--engedélyezve-az-lemeztitkosítási* hozzáférési szabályzatot.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Egy meglévő a Key Vault használata

Ez a lépés csak akkor van szükség, ha egy meglévő Key Vault használata a lemeztitkosítás kívánt. Hagyja ki ezt a lépést, ha az előző szakaszban létrehozott egy Key Vaultot.

Adja meg a saját egyedi *keyvault_name*. Ezt követően frissíteni a KeyVault- [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) és állítsa be a *--engedélyezve-az-lemeztitkosítási* hozzáférési szabályzatot.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Titkosítás engedélyezése

Egy méretezési csoportban lévő Virtuálisgép-példányok titkosítja, először kérje le néhány adatot a Key Vault erőforrás-azonosító a [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Ezeket a változókat használják, majd indítsa el a titkosítási folyamat a [az vmss-titkosítás engedélyezése](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

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

Egy-két, a titkosítási folyamat elindításához percig is eltarthat.

Mivel a méretezési csoporthoz a létrehozott méretezési csoporthoz a frissítési szabályzat egy korábbi lépésben értékre van állítva *automatikus*, a Virtuálisgép-példányok a titkosítási folyamat automatikusan elindul. A méretezési csoportok, ahol a frissítési szabályzat manuális van, indítsa el a titkosítási szabályzat, a VM-példányok [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Engedélyezze a titkosítást KEK használata a kulcs becsomagolása

A Kulcsalapú titkosítás kulcsa a fokozott biztonság használhatja a virtuálisgép-méretezési csoportot titkosításakor.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Ellenőrizheti a titkosítási folyamatot

Lemeztitkosítás állapotának ellenőrzéséhez használja [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Virtuálisgép-példányok titkosítva vannak, ha az állapotkód jelentések *EncryptionState/titkosított*, ahogy az alábbi példa kimenetében látható:

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

Ha már nem szeretne használni a Virtuálisgép-példányok titkosított lemezek, letilthatja a-titkosítással [az vmss titkosítás letiltásához](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) módon:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>További lépések

- Ebben a cikkben, az Azure CLI titkosításához használt virtuálisgép-méretezési csoportot. Is [Azure PowerShell-lel](virtual-machine-scale-sets-encrypt-disks-ps.md) vagy sablonok [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) vagy [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
- Ha azt szeretné, hogy az Azure Disk Encryption alkalmazása egy másik bővítmény kiépítése után, akkor használhatja [sorrendről](virtual-machine-scale-sets-extension-sequencing.md). Használhat [ezek a minták](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates) a kezdéshez.
- Linux rendszerű méretezési csoport adatok lemeztitkosítás teljes körű batch fájl példa található [Itt](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Ebben a példában egy erőforráscsoportot, a Linux rendszerű méretezési csoportot hoz létre, 5 GB-os adatlemezt csatlakoztat és titkosítja a virtuálisgép-méretezési csoportot.
