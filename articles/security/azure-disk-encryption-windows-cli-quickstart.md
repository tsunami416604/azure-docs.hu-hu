---
title: Hozzon létre, és az Azure CLI-vel Windows virtuális gép titkosítása
description: Ebben a rövid útmutató az Azure CLI használatával hozzon létre, és a egy Windows virtuális gép titkosítása
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 3cacb2e179cb7abe99d62d91d396a56fdeaf1ca2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081461"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Gyors útmutató: Hozzon létre, és az Azure CLI-vel Windows virtuális gép titkosítása

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozhat létre, és a egy Windows Server 2016 virtuális gép (VM) titkosítása az Azure CLI használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hoz létre. Ez a példa az *azureuser* rendszergazdanevet és a *myPassword12* jelszót használja. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő kimeneti példa azt mutatja be, hogy a virtuális gép létrehozási művelete sikeres volt.

```azurecli-interactive
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási kulcsok konfigurált kulcstartó létrehozása

Az Azure disk encryption az Azure Key Vaultban tárolja a titkosítási kulcs. Hozzon létre egy Key Vaultot az [az keyvault létrehozása](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Tárolja a titkosítási kulcsokat a Key Vault engedélyezéséhez használja a--engedélyezve-az-lemeztitkosítási paraméter.
> [!Important]
> Mindegyik Key Vault egyedi névvel kell rendelkeznie. A következő példában létrehozunk egy Key Vaultot nevű *myKV*, de meg kell Öné más nevet.

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

A virtuális gép titkosítása [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest), a--lemez-titkosítás-keyvault paraméter megadása az egyedi Key Vault nevét.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Ellenőrizheti, hogy a titkosítás engedélyezve van a virtuális Gépen való [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

A visszaadott kimenetben a következő jelenik meg:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [az csoport törlése](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és a Key Vault. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, és létrehozott egy kulcstartót, titkosítási kulcsok engedélyezése a virtuális gép titkosított.  Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Disk Encryption előfeltétel-konfigurációs szkriptjeiről az IaaS virtuális gépek esetében.

> [!div class="nextstepaction"]
> [Az Azure Disk Encryption előfeltételei](azure-security-disk-encryption-prerequisites.md)

