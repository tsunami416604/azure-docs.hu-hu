---
title: Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és titkosíthat Linux rendszerű virtuális gépeket az Azure CLI használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a9c859c32ef9ad387c56d5fc20f2173ee14fac5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975178"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Gyors útmutató: linuxos virtuális gép létrehozása és titkosítása az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozhat létre és titkosíthat linuxos virtuális gépet (VM) az Azure CLI használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hoz létre.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő kimeneti példa azt mutatja be, hogy a virtuális gép létrehozási művelete sikeres volt.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási kulcsokhoz konfigurált Key Vault létrehozása

Az Azure Disk Encryption egy Azure Key Vault tárolja a titkosítási kulcsát. Hozzon létre egy Key Vault az [az Key Vault Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)paranccsal. Ha engedélyezni szeretné a Key Vault a titkosítási kulcsok tárolásához, használja az--enabled-for-Disk-Encryption paramétert.

> [!Important]
> Minden Key vaultnak rendelkeznie kell egy egyedi névvel az Azure-ban. Az alábbi példákban cserélje le a <saját egyedi-kulcstartó-Name> a választott névre.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

Titkosítsa a virtuális gépet az [az VM encryption](/cli/azure/vm/encryption?view=azure-cli-latest)paranccsal, és adjon egyedi Key Vault nevet a--Disk-Encryption-kulcstartó paraméternek.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

A folyamat visszatérési időpontja után a rendszer elfogadta a titkosítási kérelmet. A folyamat figyeléséhez használja a "show" parancsot. " A "show" parancs az az [VM show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

Ha engedélyezve van a titkosítás, a visszaadott kimenetben a következő jelenik meg:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és a Key Vault. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, létrehozott egy Key Vault, amely engedélyezte a titkosítási kulcsokat, és titkosította a virtuális gépet.  A következő cikkből többet tudhat meg a Linux rendszerű virtuális gépek további Azure Disk Encryptionéről.

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
