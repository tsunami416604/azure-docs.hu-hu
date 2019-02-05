---
title: Linux rendszerű virtuális gépekhez az Azure Key Vault beállítása |} A Microsoft Docs
description: Hogyan állítható be a Key Vault az Azure Resource Manager virtuális gép az Azure CLI-vel való használatra.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: c38d901be627d9ad2f18ebe708c7a1fcaa63cc15
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55726990"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>A Key Vault beállítása virtuális gépekhez az Azure CLI-vel

Az Azure Resource Manager veremben titkos kódok és tanúsítványok a Key Vault által biztosított erőforrásként vannak modellezve. Az Azure Key Vaulttal kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md) Ahhoz, hogy a Key Vault az Azure Resource Manager virtuális gépeken, amely használható a *EnabledForDeployment* állítson be a Key Vault tulajdonság igaz értékre. Ez a cikk bemutatja, hogyan állítható be a Key Vault az Azure virtuális gépeken (VM) az Azure CLI-vel való használatra. 

Ezeket a lépéseket a legújabb kell [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót, és rendelje hozzá a központi telepítési házirend [az keyvault létrehozása](/cli/azure/keyvault). Az alábbi példa létrehoz egy kulcstartót nevű `myKeyVault` a a `myResourceGroup` erőforráscsoportot:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Egy Key Vaultot használni a virtuális gépek frissítése
A tároló a központi telepítési házirendet, a meglévő kulcs set [az keyvault update](/cli/azure/keyvault). A következő frissíti a key vault nevű `myKeyVault` a a `myResourceGroup` erőforráscsoportot:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>A sablonok segítségével a Key Vault beállítása
Ha a sablon használatához be kell állítani a `enabledForDeployment` tulajdonságot `true` a Key Vault erőforrás a következő:

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
A többi lehetőségekről, amelyek konfigurálásával megadhatja a sablonok használatával hoz létre egy Key Vaultot [hozzon létre egy kulcstartót](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
