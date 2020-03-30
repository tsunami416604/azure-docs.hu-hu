---
title: Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre és titkosíthat linuxos virtuális gépet az Azure CLI használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 1362844de0a6b5d8cee4555c3d24833affe71640
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385146"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Rövid útmutató: Linuxos virtuális gép létrehozása és titkosítása az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure CLI-t egy Linux virtuális gép (VM) létrehozásához és titkosításához.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

Ha úgy dönt, hogy telepíti és használja az Azure CLI helyileg, ez a rövid útmutató megköveteli, hogy az Azure CLI 2.0.30-as vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hoz létre.

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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási kulcsokhoz konfigurált kulcstartó létrehozása

Az Azure lemeztitkosítás tárolja a titkosítási kulcsot egy Azure Key Vaultban. Hozzon létre egy key vault az [keyvault létrehozása.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Ha engedélyezni szeretné, hogy a Key Vault tárolja a titkosítási kulcsokat, használja a --enabled-for-disk-encryption paramétert.

> [!Important]
> Minden key vault kell egy nevet, amely egyedi az Azure-ban. Az alábbi példákban cserélje le <egyedi keyvault-neve> a választott nevet.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

Titkosítsa a virtuális gép [az vm titkosítással,](/cli/azure/vm/encryption?view=azure-cli-latest)amely megadja az egyedi Key Vault nevét a --disk-encryption-keyvault paraméter.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Egy pillanat múlva a folyamat visszatér, "A titkosítási kérelmet elfogadták. Kérjük, használja a "show" parancsot a folyamat figyeléséhez.". A "show" parancs az [vm show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name "myVM" -g "MyResourceGroup"
```

Ha a titkosítás engedélyezve van, a visszaadott kimenetben a következő látható lesz:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, az [az csoport törlése](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gép és a key vault. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, létrehozott egy Key Vaultot, amely engedélyezte a titkosítási kulcsokat, és titkosította a virtuális gépet.  A következő cikkhez továbbfejlesztve további információarról, ha többet szeretne megtudni a Linux os virtuális gépekhez való további lemeztitkosításról.

> [!div class="nextstepaction"]
> [Azure lemeztitkosítás – áttekintés](disk-encryption-overview.md)