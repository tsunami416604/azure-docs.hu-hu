---
title: Azure CLI 2.0-minták – Egyéni virtuálisgép-rendszerképek használata | Microsoft Docs
description: Azure CLI 2.0-minták
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c13b8275b69579e6645a5a0fa0c9d446c0246a48
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697500"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-the-azure-cli-20"></a>Virtuálisgép-méretezési csoport létrehozása egy egyéni virtuálisgép-rendszerképből az Azure CLI 2.0 használatával
Ez a szkript létrehoz egy virtuálisgép-méretezési csoportot, amely egy egyéni virtuálisgép-rendszerképet használ a virtuális gépek példányainak forrásaként.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.sh "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuálisgép-méretezési csoport és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Létrehozza a virtuálisgép-méretezési csoportot, és csatlakoztatja a virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. Továbbá egy terheléselosztót is létrehoz, amely elosztja a forgalmat a virtuálisgép-példányok között. A parancs megadja emellett a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az group delete](/cli/azure/ad/group#delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
Az Azure CLI 2.0-val kapcsolatos további információért lásd az [Azure CLI 2.0 parancssori felületdokumentációját](https://docs.microsoft.com/cli/azure/overview).

A virtuálisgép-méretezési csoportokhoz kapcsolódó további Azure CLI 2.0-példaszkripteket az [Azure virtuálisgép-méretezési csoportok dokumentációjában](../cli-samples.md) találhat.