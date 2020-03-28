---
title: Azure CLI-parancsfájlminta – Az App Service integrálása az Application Gateway-rel | Microsoft dokumentumok
description: Azure CLI-parancsfájlminta – Az App Service integrálása az Application Gateway-rel
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: ee5e50bdba0a798d335641dc8a0c7ae69832d8f6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74979853"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Az App Service integrálása az Application Gateway alkalmazásával a CLI használatával

Ez a mintaparancsfájl létrehoz egy Azure App Service-webalkalmazást, egy Azure virtuális hálózatot és egy alkalmazásátjárót. Ezután korlátozza a forgalmat a webalkalmazás csak az Application Gateway alhálózatból származik.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, az Azure CLI 2.0.74-es vagy újabb verziójára van szüksége. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja egy erőforráscsoport, az App Service-alkalmazás, a Cosmos DB és az összes kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az network vnet create`](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) | Virtuális hálózatot hoz létre. |
| [`az network public-ip create`](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) | Egy nyilvános IP-címet hoz létre. |
| [`az network public-ip show`](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) | Nyilvános IP-cím részleteinek megjelenítése. |
| [`az appservice plan create`](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Létrehoz egy App Service-webalkalmazást. |
| [`az webapp show`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-show) | Az App Service-webalkalmazás részleteinek megjelenítése. |
| [`az webapp config access-restriction add`](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest#az-webapp-config-access-restriction-add) | Hozzáférési korlátozáshozzáadása az App Service webalkalmazáshoz. |
| [`az network application-gateway create`](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-create) | Létrehoz egy alkalmazásátjárót. |
| [`az network application-gateway http-settings update`](https://docs.microsoft.com/cli/azure/network/application-gateway/http-settings?view=azure-cli-latest#az-network-application-gateway-http-settings-update) | Frissíti az Application Gateway HTTP-beállításait. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
