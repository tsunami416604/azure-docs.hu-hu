---
title: Állítsa be az Azure Key Vault Linux virtuális gépek |} Microsoft Docs
description: Hogyan állítható be Key Vault az Azure Resource Manager virtuális gép a CLI 2.0-val való használatra.
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
ms.openlocfilehash: 6bd039225062ac6010d432b930f601fe4678ed2c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>A virtuális gépek az Azure CLI 2.0 Key Vault beállítása

Az Azure Resource Manager-készletben, a titkos kulcsokat vagy tanúsítványokat Key Vault által biztosított erőforrásokhoz van modellezve. Az Azure Key Vault kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md) Ahhoz, hogy az Azure Resource Manager virtuális gépekhez használni kívánt Key Vault a *EnabledForDeployment* be kell állítani a Key Vault tulajdonságot igaz értékre. Ez a cikk bemutatja, hogyan használható az Azure virtuális gépekkel (VM) használata az Azure CLI 2.0 Key Vault beállítása. Az [Azure CLI 1.0-s](key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verziójával is elvégezheti ezeket a lépéseket.

A következő lépésekkel lesz szüksége a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót és a központi telepítési házirend hozzárendelése [az keyvault létrehozása](/cli/azure/keyvault#az_keyvault_create). Az alábbi példakód létrehozza nevű kulcstároló `myKeyVault` a a `myResourceGroup` erőforráscsoport:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Frissítés a kulcstároló, használja a virtuális gépek
A tároló a központi telepítés házirendet, a meglévő kulcs set [az keyvault frissítés](/cli/azure/keyvault#az_keyvault_update). A következő frissítése a kulcstartót nevű `myKeyVault` a a `myResourceGroup` erőforráscsoport:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>A sablonok segítségével Key Vault beállítása
Amikor egy sablont használ, meg kell adnia a `enabledForDeployment` tulajdonságot `true` a kulcsot tároló erőforráshoz az alábbiak szerint:

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
Más beállításokat konfigurálhatja, ha a sablonok használatával hoz létre a kulcstároló, lásd: [hozzon létre egy kulcstartót](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
