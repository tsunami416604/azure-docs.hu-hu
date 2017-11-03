---
title: "Az Azure CLI parancsfájl minta - leképezés funkció alkalmazásokhoz az egyéni tartomány |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - leképezés funkció alkalmazásokhoz az Azure-ban egyéni tartományt."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 87b79d6222f40e3dc1306ecace51bae50b06e484
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Egyéni tartomány leképezése egy függvény alkalmazást

Ez a parancsfájlpélda kapcsolódó erőforrások egy függvény alkalmazást hoz létre, és majd leképezi `www.<yourdomain>` rá. Hozzárendelése egyéni tartományhoz, a függvény app léteznie kell az App Service-csomagot, és nem egy fogyasztás tervben. Az Azure Functions csak akkor támogatja a leképezés az A rekord egyéni tartományt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha telepíti, és a parancssori felület helyileg, az Azure CLI 2.0-s vagy újabb verzióját kell használnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

Ezt a parancsfájlt az alábbi parancsokat használja: minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az storage-fiók létrehozása](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Létrehoz egy tárfiókot, a függvény alkalmazás által igényelt. |
| [az App Service-csomag létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Az App Service-csomag kell rendelni egy egyéni tartományt hoz létre. |
| [az functionapp létrehozása]() | Létrehoz egy függvény alkalmazást. |
| [az App Service web config állomásnév hozzáadása](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | Egy függvény app rendeli hozzá egyéni tartományt. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További funkciók CLI parancsfájl minták megtalálhatók a [dokumentáció az Azure Functions]().
