---
title: A Linux rendszerű virtuális gépekhez tartozó Azure Key Vault beállítása | Microsoft Docs
description: Key Vault beállítása Azure Resource Manager virtuális géppel az Azure CLI-vel való használatra.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: kasing
ms.openlocfilehash: cbc8b6be09fcf4232636b580dc0c62482b83bd60
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002166"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Virtuális gépek Key Vaultának beállítása az Azure CLI-vel

A Azure Resource Manager veremben a titkok/tanúsítványok a Key Vault által biztosított erőforrásként vannak modellezve. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md) Ahhoz, hogy a Key Vault Azure Resource Manager virtuális gépekkel lehessen használni, a Key Vault *EnabledForDeployment* tulajdonságát True értékre kell állítani. Ez a cikk bemutatja, hogyan állíthatja be Key Vault az Azure Virtual Machines (VM) használatával az Azure CLI-vel való használatra. 

Ezen lépések elvégzéséhez szüksége lesz a legújabb [Azure CLI](/cli/azure/install-az-cli2) -re, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót, és rendelje hozzá a központi telepítési szabályzatot az [az Key Vault Create](/cli/azure/keyvault)paranccsal. A következő példában létrehozunk egy nevű `myKeyVault` kulcstárolót az `myResourceGroup` erőforráscsoporthoz:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Key Vault frissítése virtuális gépekkel való használatra
Állítsa be a központi telepítési szabályzatot egy meglévő kulcstartóba az [az Key Vault Update paranccsal](/cli/azure/keyvault). A következő frissítés az `myKeyVault` `myResourceGroup` erőforráscsoport nevű kulcstartót frissíti:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Sablonok használata a Key Vault beállításához
Ha sablont használ, a `enabledForDeployment` `true` tulajdonságot a következő értékre kell állítania a Key Vault erőforráshoz:

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
