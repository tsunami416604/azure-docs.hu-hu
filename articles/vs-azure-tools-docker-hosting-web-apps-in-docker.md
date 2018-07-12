---
title: Az Azure Container Registry (ACR) egy ASP.NET Docker-tároló üzembe helyezése |} A Microsoft Docs
description: A Visual Studio Tools for Docker használata az ASP.NET Core-webalkalmazás üzembe helyezéséhez a továbbíthat egy tárolóregisztrációs adatbázisba
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: mlearned
ms.openlocfilehash: 58df17b17de1d93683875b68dd7c6c087bc6d16d
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972308"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Egy ASP.NET-tároló üzembe helyezése továbbíthat egy tárolóregisztrációs adatbázisba, a Visual Studio használatával
## <a name="overview"></a>Áttekintés
A docker egy olyan egyszerűsített tároló motor, bizonyos szempontból hasonlóak az egy virtuális gépet, mely alkalmazások üzemeltetését és a szolgáltatások segítségével.
Ez az oktatóanyag végigvezeti a tárolóalapú alkalmazás közzététele a Visual studióval egy [Azure Container Registry](https://azure.microsoft.com/services/container-registry).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez:

* Telepítse a legújabb verzióját, [Visual Studio 2017](https://azure.microsoft.com/downloads/) a "ASP.NET és webfejlesztési" számítási feladatok
* Telepítés [Windows készült Docker](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. ASP.NET Core-webalkalmazás létrehozása
A következő lépések végigvezetik egy alapszintű ASP.NET Core-alkalmazást, ebben az oktatóanyagban használt létrehozása.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. A tároló közzététele az Azure Container Registrybe
1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben** válassza **közzététel**.
2. A cél közzétételi párbeszédpanelre, válassza ki a **Container Registry** fülre.
3. Válasszon **új Azure Container Registry** kattintson **közzététel**.
4. Adja meg a kívánt értékeket a a **hozzon létre egy új Azure Container Registry**.

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-előtag** | Globálisan egyedi név | Amely egyedileg azonosítja a tárolóregisztrációs adatbázis nevét. |
    | **Előfizetés** | Válassza ki az előfizetését | A használandó előfizetés. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Az erőforráscsoport, amelyben létrehozza a tárolóregisztrációs adatbázis nevére. Válassza az **Új** elemet egy új erőforráscsoport létrehozásához.|
    | **[TERMÉKVÁLTOZAT](https://docs.microsoft.com/azure/container-registry/container-registry-skus)** | Standard | Szolgáltatási szinten a tárolóregisztrációs adatbázis  |
    | **Beállításjegyzék-helyhez** | Egy hely Önhöz | Válassza ki a hely egy [régió](https://azure.microsoft.com/regions/) Önhöz vagy a tároló-beállításjegyzék-t használó más szolgáltatások közelében. |
    ![A Visual Studio létrehozása az Azure Container Registry párbeszédpanel][0]
5. Kattintson a **Create** (Létrehozás) gombra

Most már olvashatók a tárolót a beállításjegyzékből bármelyik gazdagépe számára alkalmas, például a futó Docker-rendszerképeket, [Azure Container Instances](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
