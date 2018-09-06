---
title: Azure CLI-minták – Azure SignalR szolgáltatás | Microsoft Docs
description: Azure CLI-minták – Azure SignalR szolgáltatás
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a1ca02a5e058816c133accf3027f0a951db42cf2
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664003"
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
