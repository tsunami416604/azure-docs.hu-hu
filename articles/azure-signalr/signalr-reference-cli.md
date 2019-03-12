---
title: Azure CLI-minták – Azure SignalR szolgáltatás
description: Azure CLI-minták – Azure SignalR szolgáltatás
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: dedd9d14bafb342b0b34e3685ef3b7a46a54f9c8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555110"
---
# <a name="azure-cli-reference"></a>Azure CLI-referencia

A következő táblázat Azure CLI-t használó Azure SignalR szolgáltatás bash szkriptjeire mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Létrehozás**||
| [Új SignalR szolgáltatás és erőforráscsoport létrehozása](scripts/signalr-cli-create-service.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban.  |
|**Integrálás**||
| [A SignalR használatára konfigurált új SignalR szolgáltatás és webalkalmazás létrehozása](scripts/signalr-cli-create-with-app-service.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban. Valamint hozzáad egy új webalkalmazást és App Service-csomagot is a SignalR szolgáltatást használó ASP.NET Core Web App futtatására. A webalkalmazást alkalmazásbeállítással konfigurálhatja az új SignalR szolgáltatás-erőforráshoz való csatlakozáshoz. |
| [A SignalR és a GitHub OAuth használatára konfigurált új SignalR szolgáltatás és webalkalmazás létrehozása](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban. Valamint hozzáad egy új Azure-webalkalmazást és szolgáltatási csomagot is a SignalR Service-t használó ASP.NET Core Web App futtatására. A webalkalmazás alkalmazásbeállításokkal konfigurálható a SignalR szolgáltatás új erőforrásának kapcsolati sztringjével, valamint az ügyfélben használható titkos kulcsokkal a [GitHub-hitelesítés](https://developer.github.com/v3/guides/basics-of-authentication/)[hitelesítési oktatóanyagban](signalr-concept-authenticate-oauth.md) bemutatott módon történő támogatásához. A webalkalmazás úgy van konfigurálva, hogy egy helyi Git-adattárat használjon üzembehelyezési forrásként. |
| | |