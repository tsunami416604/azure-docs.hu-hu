---
title: Azure CLI-minták – Egyzónás méretezési csoport | Microsoft Docs
description: Azure CLI-minták
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
ms.openlocfilehash: 12e8ebea994b39b7c4324b52068b574749002117
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883934"
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-the-azure-cli"></a>Hozzon létre egy Egyzónás virtuálisgép-méretezési csoportot az Azure CLI-vel
Ez a szkript egy Ubuntu rendszert futtató virtuálisgép-méretezési csoportot hoz létre egyetlen rendelkezésre állási zónában. A szkript futtatása után a virtuális gépet RDP-kapcsolaton keresztül érheti el.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuálisgép-méretezési csoport és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/ad/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vmss create](/cli/azure/vmss) | Létrehozza a virtuálisgép-méretezési csoportot, és csatlakoztatja a virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. Továbbá egy terheléselosztót is létrehoz, amely elosztja a forgalmat a virtuálisgép-példányok között. A parancs megadja emellett a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az group delete](/cli/azure/ad/group) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure/overview).

A virtuálisgép-méretezési csoportokhoz kapcsolódó további Azure CLI-példaszkripteket az [Azure virtuálisgép-méretezési csoportok dokumentációjában](../cli-samples.md) találhat.
