---
title: Az Azure CLI-példaszkript – forgalom irányítása az alkalmazások magas rendelkezésre állású |} A Microsoft Docs
description: Az Azure CLI-példaszkript – forgalom irányítása az alkalmazások magas rendelkezésre állás
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
ms.openlocfilehash: 6c610a1cddb0854878d4c2bd5531f88a1cf2ec51
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009104"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Forgalom irányítása az Azure CLI-vel használó alkalmazások magas rendelkezésre állás

Ez a szkript létrehoz egy erőforráscsoportot, két app service-csomagot, két webalkalmazást, egy traffic manager-profilt és két traffic manager-végpontot. A TRAFFIC Manager irányítja a forgalmat az alkalmazás egy adott régióban, az elsődleges régióban, és a másodlagos régióba, amikor az alkalmazás az elsődleges régióban nem érhető el. Előtt hajtsa végre a parancsprogramot, módosítania kell a MyWebApp, MyWebAppL1 és MyWebAppL2 értékek egyedi értékeket az Azure-ban. A szkript futtatása után az alkalmazás az elsődleges régióban, az az URL-cím mywebapp.trafficmanager.net érheti el.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után az alábbi parancs segítségével törölheti az erőforráscsoportot, App Service-alkalmazást, és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy webalkalmazás, egy Traffic Manager-profil és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Létrehoz egy App Service-csomagot. Ez olyan, mint az Azure-alapú webes alkalmazás kiszolgálófarmot. |
| [az webapp webalkalmazás létrehozása](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Létrehoz egy Azure-webalkalmazást az App Service-csomag belül. |
| [az network traffic-manager-profil létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Létrehoz egy Azure Traffic Manager-profilt. |
| [az network traffic-manager-végpont létrehozása](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Hozzáad egy végpontot egy Azure Traffic Manager-profilhoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-példaszkripteket megtalálható a [dokumentáció az Azure-hálózatot](../cli-samples.md).
