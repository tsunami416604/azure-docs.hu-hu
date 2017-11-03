---
title: "Az Azure CLI parancsfájl minta - irányíthatja a forgalmat a magas rendelkezésre állású alkalmazások |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - irányíthatja a forgalmat a magas rendelkezésre állású alkalmazások"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: tysonn
tags: azure-infrastructure
ms.assetid: 
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 5c3754c3c3c96e1d2f0b5b52d8108ecc3903f788
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Irányíthatja a forgalmat a magas rendelkezésre állású alkalmazások

Ezt a parancsfájlt hoz létre egy erőforráscsoportot, két app service-csomagokról, két webes alkalmazásokat, egy traffic manager-profil és két traffic manager-végpont. A TRAFFIC Manager irányítja a forgalmat a alkalmazását egy régió tartozik, az elsődleges régióban, és a másodlagos régióban, ha az elsődleges régióban az alkalmazás nem érhető el. A parancsprogram végrehajtása előtt módosítania kell a MyWebApp, MyWebAppL1 és MyWebAppL2 értékek egyedi értékeket az egész Azure. A parancsfájl futtatása után érheti el az alkalmazást az URL-cím mywebapp.trafficmanager.net az elsődleges régióban.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot, az App Service-alkalmazást, és minden kapcsolódó erőforrásokat.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, webalkalmazás, traffic manager-profil és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az App Service-csomag létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service-csomag létrehozása. Ez olyan, mintha egy kiszolgálófarmon, az Azure webalkalmazás számára. |
| [az App Service webalkalmazás létrehozása](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_create) | Létrehoz egy Azure webalkalmazás az App Service-csomag belül. |
| [az hálózati forgalmat-manager-profil létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Az Azure Traffic Manager-profil létrehozása. |
| [az hálózati forgalmat-manager-végpont létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Az Azure Traffic Manager-profilt ad hozzá egy végpontot. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure Networking dokumentáció](../cli-samples.md).
