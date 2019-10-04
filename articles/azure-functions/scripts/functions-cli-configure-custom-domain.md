---
title: Azure CLI-példaszkript – Egyéni tartomány leképezése egy függvényalkalmazásra | Microsoft Docs
description: Azure CLI-példaszkript – Egyéni tartomány leképezése egy függvényalkalmazásra az Azure-ban.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 19fd73dff045d6d784dd70d1f3ad539a22faf413
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075468"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Egyéni tartomány leképezése egy függvényalkalmazásra

Ez a példaszkript egy függvényalkalmazást hoz létre egy App Service-csomagban, majd leképezi a megadott egyéni tartományhoz. Amikor a függvényalkalmazás szolgáltatásban üzemeltetett egy [prémium szintű csomag](../functions-scale.md#premium-plan) vagy egy [App Service-csomag](../functions-scale.md#app-service-plan), leképezhet egy egyéni tartományt egy olyan CNAME REKORDOT vagy egy A rekordot. [Használatalapú csomagban](../functions-scale.md#consumption-plan) lévő függvényalkalmazások esetében csak a CNAME lehetőség támogatott. Ez a példa létrehoz egy App Service-csomagot, és egy A rekordra van szüksége a tartomány leképezéséhez. 

A példaszkript futtatásához előzetesen konfigurálnia kell egy olyan A rekordot az egyéni tartományban, amely a webalkalmazás alapértelmezett tartománynevére mutat. További információ: [Egyéni tartományutasítások leképezése az Azure App Service-ben](https://aka.ms/appservicecustomdns). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verzióját kell használnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 


## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot, amely a függvényalkalmazáshoz szükséges. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Létrehoz egy App Service-csomagot, amely az egyéni tartomány leképezéséhez szükséges. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást az App Service-csomagban. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Leképez egy egyéni tartományt egy függvényalkalmazásra. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
