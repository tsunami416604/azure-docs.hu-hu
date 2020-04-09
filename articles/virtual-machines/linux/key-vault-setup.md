---
title: Az Azure Key Vault beállítása Linuxos virtuális gépekhez
description: Key Vault beállítása azure Resource Manager virtuális géphez az Azure CLI-vel való használatra.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: mimckitt
ms.openlocfilehash: 59b11d22f054a98fe176e4393843606bd01cc872
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879479"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>A Key Vault beállítása virtuális gépekhez az Azure CLI-vel

Az Azure Resource Manager-veremben a titkos kulcsok/tanúsítványok a Key Vault által biztosított erőforrásokként vannak modellezve. Ha többet szeretne megtudni az Azure Key Vaultról, olvassa el [a Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md) Ahhoz, hogy a Key Vault használható legyen az Azure Resource Manager virtuális gépekkel, a Key Vault *EnabledForDeployment* tulajdonságát igaz értékre kell állítani. Ez a cikk bemutatja, hogyan állíthatja be a Key Vault azure-beli virtuális gépekkel (VM-ekkel) való használatra az Azure CLI használatával. 

A lépések végrehajtásához a legújabb [Azure CLI-t](/cli/azure/install-az-cli2) kell telepítenie, és be kell jelentkeznie egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index)

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy key vaultot, és rendelje hozzá a telepítési szabályzatot az [keyvault create](/cli/azure/keyvault). A következő példa létrehoz `myKeyVault` egy `myResourceGroup` key vault nevű az erőforráscsoportban:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Key Vault frissítése virtuális gépekhez
Állítsa be a központi telepítési szabályzatot egy meglévő keyvault-frissítéssel rendelkező [meglévő kulcstartóra.](/cli/azure/keyvault) A következő frissíti az `myKeyVault` `myResourceGroup` erőforráscsoportban megnevezett kulcstartót:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>A Key Vault beállítása sablonok használatával
Sablon használata esetén a tulajdonságot `enabledForDeployment` `true` a Key Vault erőforráshoz az alábbiak szerint kell beállítania:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>További lépések
A Key Vault sablonok használatával történő létrehozásakor konfigurálható egyéb beállításokról a [Key Vault létrehozása című](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)témakörben olvashat.
