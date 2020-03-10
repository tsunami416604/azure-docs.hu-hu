---
title: Azure Key Vault beállítása Linux rendszerű virtuális gépekhez
description: Key Vault beállítása Azure Resource Manager virtuális géppel az Azure CLI-vel való használatra.
author: singhkays
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: kasing
ms.openlocfilehash: c9659c38e349bd62f22ed0d0839d97651b1b3b66
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944816"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Virtuális gépek Key Vaultának beállítása az Azure CLI-vel

A Azure Resource Manager veremben a titkok/tanúsítványok a Key Vault által biztosított erőforrásként vannak modellezve. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md) Ahhoz, hogy a Key Vault Azure Resource Manager virtuális gépekkel lehessen használni, a Key Vault *EnabledForDeployment* tulajdonságát True értékre kell állítani. Ez a cikk bemutatja, hogyan állíthatja be Key Vault az Azure Virtual Machines (VM) használatával az Azure CLI-vel való használatra. 

Ezen lépések elvégzéséhez szüksége lesz a legújabb [Azure CLI](/cli/azure/install-az-cli2) -re, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót, és rendelje hozzá a központi telepítési szabályzatot az [az Key Vault Create](/cli/azure/keyvault)paranccsal. Az alábbi példa egy `myKeyVault` nevű kulcstartót hoz létre a `myResourceGroup` erőforráscsoporthoz:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Key Vault frissítése virtuális gépekkel való használatra
Állítsa be a központi telepítési szabályzatot egy meglévő kulcstartóba az [az Key Vault Update paranccsal](/cli/azure/keyvault). A következő frissíti a `myKeyVault` nevű kulcstartót a `myResourceGroup` erőforráscsoporthoz:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Sablonok használata a Key Vault beállításához
Ha sablont használ, a `enabledForDeployment` tulajdonságot úgy kell beállítania, hogy `true` a Key Vault erőforráshoz az alábbiak szerint:

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
