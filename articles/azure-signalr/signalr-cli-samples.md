---
title: Azure CLI-minták – Azure SignalR szolgáltatás
description: Azure CLI-minták – Azure SignalR szolgáltatás
author: sffamily
ms.service: signalr
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a39f0abf4f3a8cc30d6f8f83dcad7e9069f348e4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258780"
---
# <a name="azure-cli-samples"></a>Azure CLI-minták

A következő táblázat Azure CLI-t használó Azure SignalR szolgáltatás bash szkriptjeire mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Létrehozás**||
| [Új SignalR szolgáltatás és erőforráscsoport létrehozása](scripts/signalr-cli-create-service.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban.  |
|**Integrálás**||
| [A SignalR használatára konfigurált új SignalR szolgáltatás és webalkalmazás létrehozása](scripts/signalr-cli-create-with-app-service.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban. Valamint hozzáad egy új webalkalmazást és App Service-csomagot is a SignalR szolgáltatást használó ASP.NET Core Web App futtatására. A webalkalmazást alkalmazásbeállítással konfigurálhatja az új SignalR szolgáltatás-erőforráshoz való csatlakozáshoz. |
| [A SignalR és a GitHub OAuth használatára konfigurált új SignalR szolgáltatás és webalkalmazás létrehozása](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatás-erőforrást egy új erőforráscsoportban. Valamint hozzáad egy új Azure-webalkalmazást és szolgáltatási csomagot is a SignalR Service-t használó ASP.NET Core Web App futtatására. A webalkalmazás alkalmazásbeállításokkal konfigurálható a SignalR szolgáltatás új erőforrásának kapcsolati sztringjével, valamint az ügyfélben használható titkos kulcsokkal a [GitHub-hitelesítés](https://developer.github.com/v3/guides/basics-of-authentication/)[hitelesítési oktatóanyagban](signalr-authenticate-oauth.md) bemutatott módon történő támogatásához. A webalkalmazás úgy van konfigurálva, hogy egy helyi Git-adattárat használjon üzembehelyezési forrásként. |
| | |