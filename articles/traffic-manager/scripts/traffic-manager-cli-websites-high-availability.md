---
title: Az Azure CLI parancsfájl minta - irányíthatja a forgalmat a magas rendelkezésre állású alkalmazások |} Microsoft Docs
description: Az Azure CLI parancsfájl minta - irányíthatja a forgalmat a magas rendelkezésre állású alkalmazások
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 82264e51fcd64615a41f5fa652c4ad58ad9dabfb
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32313047"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Irányíthatja a forgalmat a magas rendelkezésre állás, az alkalmazások Azure parancssori felület használatával

Ezt a parancsfájlt hoz létre egy erőforráscsoportot, két app service-csomagokról, két webes alkalmazásokat, egy traffic manager-profil és két traffic manager-végpont. A TRAFFIC Manager irányítja a forgalmat a alkalmazását egy régió tartozik, az elsődleges régióban, és a másodlagos régióban, ha az elsődleges régióban az alkalmazás nem érhető el. A parancsprogram végrehajtása előtt módosítania kell a MyWebApp, MyWebAppL1 és MyWebAppL2 értékek egyedi értékeket az egész Azure. A parancsfájl futtatása után érheti el az alkalmazást az URL-cím mywebapp.trafficmanager.net az elsődleges régióban.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot, az App Service-alkalmazást, és minden kapcsolódó erőforrásokat.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy webalkalmazás, egy Traffic Manager-profil és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service-csomag létrehozása. Ez olyan, mintha egy kiszolgálófarmon, az Azure webalkalmazás számára. |
| [az App Service webalkalmazás létrehozása](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_create) | Létrehoz egy Azure webalkalmazás az App Service-csomag belül. |
| [az hálózati forgalmat-manager-profil létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Létrehoz egy Azure Traffic Manager-profilt. |
| [az hálózati forgalmat-manager-végpont létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Az Azure Traffic Manager-profilt ad hozzá egy végpontot. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI parancsfájl minták megtalálhatók a [Azure Networking dokumentáció](../cli-samples.md).
