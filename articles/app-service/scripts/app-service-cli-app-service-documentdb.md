---
title: "Az Azure CLI-parancsfájlt minta - webalkalmazás csatlakozzon Cosmos Adatbázishoz |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – Cosmos DB webes alkalmazás csatlakoztatása"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: b581f8d5a4e924e0aa88bf614839c917345eb570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-web-app-to-cosmos-db"></a>Webes alkalmazás csatlakoztatása az Cosmos-Adatbázishoz

Ebben a forgatókönyvben, megtudhatja, hogyan hozzon létre egy Azure Cosmos DB fiókot és egy Azure webalkalmazás számára. A Cosmos DB majd összekapcsolja a webes alkalmazás alkalmazás-beállításokkal.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsok segítségével hozzon létre egy erőforráscsoportot, webalkalmazás, Cosmos DB, és az összes kapcsolódó erőforrásokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az App Service-csomag létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service-csomag létrehozása. Ez olyan, mintha egy kiszolgálófarmon, az Azure webalkalmazás számára. |
| [az alkalmazás-kulcs létrehozása](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Létrehoz egy Azure-webalkalmazásban. |
| [az cosmosdb létrehozása](https://docs.microsoft.com/en-us/cli/azure/cosmosdb#az_cosmosdb_create) | Létrehoz egy Cosmos-DB-fiókot. Ez az adatok tárolásához. |
| [az cosmosdb lista-kulcsok](https://docs.microsoft.com/en-us/cli/azure/cosmosdb#az_cosmosdb_list_keys) | A megadott Cosmos DB-fiók elérési kulcsainak listázása. |
| [az alkalmazás kulcs appsettings konfiguráció](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Létrehozza vagy frissíti az Azure-webalkalmazás Alkalmazásbeállítás. Alkalmazásbeállítások az alkalmazások környezeti változóként érhetők el. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure App Service-dokumentáció](../app-service-cli-samples.md).
