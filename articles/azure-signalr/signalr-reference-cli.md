---
title: Azure CLI-minták – Azure SignalR szolgáltatás
description: Kövesse a valódi mintákat az Azure ClI Azure SignalR-szolgáltatáshoz való tanulásához. Megtudhatja, hogyan hozhat létre SignalR-szolgáltatást több Azure-szolgáltatással.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158131"
---
# <a name="azure-cli-reference"></a>Azure CLI-referencia

A következő táblázat Azure CLI-t használó Azure SignalR szolgáltatás bash szkriptjeire mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Létrehozás**||
| [Új SignalR szolgáltatás és erőforráscsoport létrehozása](scripts/signalr-cli-create-service.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban.  |
|**Integrálás**||
| [A SignalR használatára konfigurált új SignalR szolgáltatás és webalkalmazás létrehozása](scripts/signalr-cli-create-with-app-service.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban. Is hozzáad egy új Web App és App Service-csomag a fogadó egy ASP.NET Core Web App, amely a SignalR szolgáltatást használja. A webalkalmazást alkalmazásbeállítással konfigurálhatja az új SignalR szolgáltatás-erőforráshoz való csatlakozáshoz. |
| [A SignalR és a GitHub OAuth használatára konfigurált új SignalR szolgáltatás és webalkalmazás létrehozása](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban. Is hozzáad egy új Azure Web App és üzemeltetési csomag üzemeltetni egy ASP.NET Core Web App, amely a SignalR szolgáltatást használja. A webalkalmazás alkalmazásbeállításokkal konfigurálható a SignalR szolgáltatás új erőforrásának kapcsolati sztringjével, valamint az ügyfélben használható titkos kulcsokkal a [GitHub-hitelesítés](https://developer.github.com/v3/guides/basics-of-authentication/)[hitelesítési oktatóanyagban](signalr-concept-authenticate-oauth.md) bemutatott módon történő támogatásához. A webalkalmazás úgy van konfigurálva, hogy egy helyi Git-adattárat használjon üzembehelyezési forrásként. |
| | |
