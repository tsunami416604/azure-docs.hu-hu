---
title: Azure CLI-példaszkript – ACS-fürt méretezése | Microsoft Docs
description: Azure CLI-példaszkript – ACS-fürt méretezése
services: container-service
documentationcenter: ''
author: neilpeterson
manager: jeconnoc
editor: ''
tags: acs, azure-container-service
keywords: Docker, tárolók, mikroszolgáltatások, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: nepeters
ms.openlocfilehash: 2daff49e90213b2fadc0f75e0209f668a94d3dea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="scale-an-azure-container-service-cluster"></a>Azure Container Service-fürt méretezése

Ez a példa egy Azure Container Service-t méretez. 

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

```azurecli
az acs scale --resource-group myResourceGroup --name myK8SCluster --new-agent-count 5
```

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az acs scale](/cli/azure/acs#az_acs_scale) | Egy ACS-fürt méretezése. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Container Service CLI-példaszkripteket az [Azure Container Service dokumentációjában](../cli-samples.md) találhat.

