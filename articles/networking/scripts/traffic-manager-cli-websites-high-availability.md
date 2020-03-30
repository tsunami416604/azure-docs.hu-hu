---
title: Azure CLI script minta - Útvonal forgalom magas rendelkezésre állású alkalmazások | Microsoft dokumentumok
description: Azure CLI-parancsfájlminta – Forgalom irányítása az alkalmazások magas rendelkezésre állása érdekében
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: 2a083520a0da324d7033d4597a6b734b43c025b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74049252"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>Útvonalforgalom az alkalmazások magas rendelkezésre állása érdekében - Azure CLI

Ez a parancsfájl létrehoz egy erőforráscsoportot, két alkalmazásszolgáltatási tervet, két webalkalmazást, egy forgalomkezelő profilt és két forgalomkezelő végpontot. Traffic Manager irányítja a forgalmat az alkalmazás egy régióban, mint az elsődleges régióban, és a másodlagos régióban, ha az alkalmazás az elsődleges régióban nem érhető el. A parancsfájl végrehajtása előtt a MyWebApp, a MyWebAppL1 és a MyWebAppL2 értékeket egyedi értékekre kell módosítania az Azure-ban. A parancsfájl futtatása után az elsődleges régióban lévő alkalmazást az URL-mywebapp.trafficmanager.net érheti el.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájlminta futtatása után a következő parancs az erőforráscsoport, az App Service-alkalmazás és az összes kapcsolódó erőforrás eltávolítására használható.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy webalkalmazás, egy Traffic Manager-profil és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Létrehoz egy App Service-csomagot. Ez olyan, mint egy kiszolgálófarm az Azure-webalkalmazáshoz. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Létrehoz egy Azure-webalkalmazást az App Service-csomagon belül. |
| [az hálózati forgalomkezelő profil létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Létrehoz egy Azure Traffic Manager-profilt. |
| [az hálózati forgalomkezelő végpont létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Hozzáad egy végpontot egy Azure Traffic Manager-profilhoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-parancsfájlminták az [Azure Networking dokumentációban](../cli-samples.md)találhatók.
