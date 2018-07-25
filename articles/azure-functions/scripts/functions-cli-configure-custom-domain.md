---
title: Azure CLI-példaszkript – Egyéni tartomány leképezése egy függvényalkalmazásra | Microsoft Docs
description: Azure CLI-példaszkript – Egyéni tartomány leképezése egy függvényalkalmazásra az Azure-ban.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 0650fffeb54ebc4390c82fb2711d7c89e0ac4572
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989418"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Egyéni tartomány leképezése egy függvényalkalmazásra

Ez a példaszkript egy függvényalkalmazást hoz létre egy App Service-csomagban, majd leképezi a megadott egyéni tartományhoz. Ha a függvényalkalmazása egy [App Service-csomagban](../functions-scale.md#app-service-plan) üzemel, egy CNAME vagy A rekord segítségével képezhet le egy egyéni tartományt. [Használatalapú csomagban](../functions-scale.md#consumption-plan) lévő függvényalkalmazások esetében csak a CNAME lehetőség támogatott. Ez a példa létrehoz egy App Service-csomagot, és egy A rekordra van szüksége a tartomány leképezéséhez. 

A példaszkript futtatásához előzetesen konfigurálnia kell egy olyan A rekordot az egyéni tartományban, amely a webalkalmazás alapértelmezett tartománynevére mutat. További információ: [Egyéni tartományutasítások leképezése az Azure App Service-ben](https://aka.ms/appservicecustomdns). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verzióját kell használnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

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
