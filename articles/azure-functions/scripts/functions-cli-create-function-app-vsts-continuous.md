---
title: "Visual Studio Team Servicesből üzembe helyezett függvény létrehozása az Azure-ban | Microsoft Docs"
description: "Függvényalkalmazás létrehozása és függvénykód üzembe helyezése a Visual Studio Team Servicesből"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 01/09/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 789f4e0b325475ddc3ff7aeb6e014f3814ac3458
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-function-app-and-deploy-function-code-from-visual-studio-team-services"></a>Függvényalkalmazás létrehozása és függvénykód üzembe helyezése a Visual Studio Team Servicesből

Ez a témakör bemutatja, hogyan hozhat létre az Azure Functions segítségével egy [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) függvényalkalmazást a [használatalapú csomag](../functions-scale.md#consumption-plan) segítségével. A függvényalkalmazást, amely az Ön függvényeinek tárolója, folyamatosan üzembe helyezzük egy Visual Studio Team Services- (VSTS-) adattárból. 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

A témakör teljesítéséhez a következőkre lesz szüksége:

* Egy VSTS-adattár, amely tartalmazza a függvényalkalmazás projektjét, és amelyhez Ön rendszergazdai engedélyekkel rendelkezik.
* Egy [személyes hozzáférési jogkivonat (PAT)](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) a VSTS-adattár eléréséhez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha inkább a helyi Azure CLI-t használja, akkor az Azure CLI 2.0-s vagy újabb verzióját kell telepítenie és futtatnia. Az Azure CLI verziójának megállapításához futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript

Ez a példa létrehoz egy Azure-függvényalkalmazást, és üzembe helyezi a függvény kódját a Visual Studio Team Servicesből.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, tárfiók, függvényalkalmazás és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Létrehoz egy App Service-csomagot. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | Társít egy függvényalkalmazást egy Git- vagy Mercurial-adattárhoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure/overview).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
