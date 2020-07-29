---
title: Azure Key Vault beállítása Linux rendszerű virtuális gépekhez
description: Key Vault beállítása Azure Resource Manager virtuális géppel az Azure CLI-vel való használatra.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/24/2017
ms.author: mimckitt
ms.openlocfilehash: 435d62787e9a8b61ebfe68b48f8c19bb73b58686
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283282"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Virtuális gépek Key Vaultának beállítása az Azure CLI-vel

A Azure Resource Manager veremben a titkok/tanúsítványok a Key Vault által biztosított erőforrásként vannak modellezve. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/general/overview.md) Ahhoz, hogy a Key Vault Azure Resource Manager virtuális gépekkel lehessen használni, a Key Vault *EnabledForDeployment* tulajdonságát True értékre kell állítani. Ez a cikk bemutatja, hogyan állíthatja be Key Vault az Azure Virtual Machines (VM) használatával az Azure CLI-vel való használatra. 

Ezen lépések elvégzéséhez szüksége lesz a legújabb [Azure CLI](/cli/azure/install-az-cli2) -re, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót, és rendelje hozzá a központi telepítési szabályzatot az [az Key Vault Create](/cli/azure/keyvault)paranccsal. A következő példában létrehozunk egy nevű kulcstárolót `myKeyVault` az `myResourceGroup` erőforráscsoporthoz:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Key Vault frissítése virtuális gépekkel való használatra
Állítsa be a központi telepítési szabályzatot egy meglévő kulcstartóba az [az Key Vault Update paranccsal](/cli/azure/keyvault). A következő frissítés az erőforráscsoport nevű kulcstartót frissíti `myKeyVault` `myResourceGroup` :

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Sablonok használata a Key Vault beállításához
Ha sablont használ, a tulajdonságot a következő értékre kell állítania `enabledForDeployment` `true` a Key Vault erőforráshoz:

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
A Key Vault sablonok használatával történő létrehozásakor konfigurálható egyéb beállításokért lásd: [kulcstartó létrehozása](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
