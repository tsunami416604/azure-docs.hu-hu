---
title: Azure CLI-példaszkript – Webes forgalom korlátozása | Microsoft Docs
description: Azure CLI-példaszkript – Alkalmazásátjáró létrehozása webalkalmazási tűzfallal és a forgalom korlátozásához OWASP-szabályokat használó virtuálisgép-méretezési csoporttal.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 39750465008be586c58d9da309ffe435b2030c59
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397008"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Webes forgalom korlátozása az Azure CLI-vel

Ez a szkript létrehoz egy webalkalmazási tűzfallal rendelkező alkalmazásátjárót, amely virtuálisgép-méretezési csoportot használ háttérkiszolgálókként. A webalkalmazási tűzfal OWASP-szabályok alapján korlátozza a webes forgalmat. A szkript futtatása után az alkalmazásátjárót annak nyilvános IP-címével tesztelheti.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő paranccsal eltávolítható az erőforráscsoport, az alkalmazásátjáró és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Virtuális hálózatot hoz létre. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Alhálózatot hoz létre egy virtuális hálózatban. |
| [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest) | Az alkalmazásátjáró nyilvános IP-címét hozza létre. |
| [az network application-gateway create](/cli/azure/network/application-gateway?view=azure-cli-latest) | Egy alkalmazásátjárót hoz létre. |
| [az vmss create](/cli/azure/vmss#az-vmss-create) | Létrehoz egy virtuálisgép-méretezési csoportot. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot. |
| [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) | Létrehoz egy tárfiókot. |
| [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) | Beszerzi az alkalmazásátjáró nyilvános IP-címét. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure/overview).

Az alkalmazásátjárókkal használható további CLI-példaszkripteket az [Azure Application Gateway dokumentációjában](../cli-samples.md) találhat.