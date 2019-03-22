---
title: Azure CLI-minták – Azure SignalR szolgáltatás
description: Azure CLI-minták – Azure SignalR szolgáltatás
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992186"
---
# <a name="azure-cli-reference"></a>Azure CLI-referencia

A következő táblázat Azure CLI-t használó Azure SignalR szolgáltatás bash szkriptjeire mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Létrehozás**||
| [Új SignalR szolgáltatás és erőforráscsoport létrehozása](scripts/signalr-cli-create-service.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban.  |
|**Integrálás**||
| [A SignalR használatára konfigurált új SignalR szolgáltatás és webalkalmazás létrehozása](scripts/signalr-cli-create-with-app-service.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban. Új webalkalmazás és App Service csomag ASP.NET Core-webalkalmazás a SignalR Service szolgáltatást használó üzemeltetésére is hozzáadja. A webalkalmazást alkalmazásbeállítással konfigurálhatja az új SignalR szolgáltatás-erőforráshoz való csatlakozáshoz. |
| [A SignalR és a GitHub OAuth használatára konfigurált új SignalR szolgáltatás és webalkalmazás létrehozása](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban. Is hozzáad egy új Azure-webalkalmazást, és a szolgáltatási csomag ASP.NET Core-webalkalmazás a SignalR Service szolgáltatást használó gazdagépre. A webalkalmazás alkalmazásbeállításokkal konfigurálható a SignalR szolgáltatás új erőforrásának kapcsolati sztringjével, valamint az ügyfélben használható titkos kulcsokkal a [GitHub-hitelesítés](https://developer.github.com/v3/guides/basics-of-authentication/)[hitelesítési oktatóanyagban](signalr-concept-authenticate-oauth.md) bemutatott módon történő támogatásához. A webalkalmazás úgy van konfigurálva, hogy egy helyi Git-adattárat használjon üzembehelyezési forrásként. |
| | |
