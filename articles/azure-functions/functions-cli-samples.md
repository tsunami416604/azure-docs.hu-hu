---
title: Azure CLI-minták – Azure Functions
description: Azure CLI-minták – Azure Functions
author: syntaxc4
ms.assetid: 577d2f13-de4d-40d2-9dfc-86ecc79f3ab0
ms.topic: sample
ms.date: 01/09/2018
ms.custom: mvc
ms.openlocfilehash: 365275d291244d3c08f856d2d919c7ee7d79161a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239643"
---
# <a name="azure-cli-samples"></a>Azure CLI-minták

A következő táblázat az Azure Functions az Azure CLI-t használó Bash-szkriptjeire mutató hivatkozásokat tartalmaz.

<a id="create"></a>

| Alkalmazás létrehozása | Leírás |
|---|---|
| [Függvényalkalmazás létrehozása kiszolgáló nélküli végrehajtáshoz](scripts/functions-cli-create-serverless.md) | Létrehoz egy függvényalkalmazást egy használatalapú csomagban.  |
| [Kiszolgáló nélküli Python-függvényalkalmazás létrehozása](scripts/functions-cli-create-serverless-python.md) | Létrehoz egy függvényalkalmazást egy dedikált App Service-csomagban. |
| [Függvényalkalmazás létrehozása méretezhető prémiumcsomagban](scripts/functions-cli-create-premium-plan.md) | Létrehoz egy függvényalkalmazást egy dedikált App Service-csomagban. |
| [Függvényalkalmazás létrehozása dedikált (App Service-) csomagban](scripts/functions-cli-create-app-service-plan.md) | Létrehoz egy függvényalkalmazást egy dedikált App Service-csomagban. |

| Integráció | Leírás|
|---|---|
| [Függvényalkalmazás létrehozása és csatlakoztatása egy tárfiókhoz](scripts/functions-cli-create-function-app-connect-to-storage-account.md) | Létrehoz egy függvényalkalmazást, és csatlakoztatja egy tárfiókhoz. |
| [Függvényalkalmazás létrehozása és csatlakoztatása egy Azure Cosmos DB-adatbázishoz](scripts/functions-cli-create-function-app-connect-to-cosmos-db.md) | Létrehoz egy függvényalkalmazást, és csatlakoztatja egy Azure Cosmos DB-adatbázishoz. |
| [Python-függvényalkalmazás létrehozása és Azure Files-megosztás csatlakoztatása](scripts/functions-cli-mount-files-storage-linux.md) | A Linux függvényalkalmazásmegosztás csatlakoztatásával kihasználhatja a meglévő gépi tanulási modelleket vagy a függvényekben lévő egyéb adatokat. | 

| Folyamatos üzembe helyezés | Leírás|
|---|---|
| [Üzembe helyezés a GitHubból](scripts/functions-cli-create-function-app-github-continuous.md) | Létrehoz egy függvényalkalmazást, amely egy GitHub-adattárból lesz üzembe helyezve.  |
| [Üzembe helyezés az Azure DevOpsból](scripts/functions-cli-create-function-app-vsts-continuous.md) | Hozzon létre egy függvényalkalmazást, amely egy Azure DevOps-tárházból telepít.  |
