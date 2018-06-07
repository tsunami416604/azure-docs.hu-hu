---
title: Az ASP.NET Docker-tároló Azure tároló beállításjegyzék (ACR) telepítése |} Microsoft Docs
description: Az ASP.NET Core webes alkalmazás telepítése egy tároló beállításjegyzék Docker Visual Studio eszközök használata
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
ms.openlocfilehash: 4442c1d763f4ed21a5efeedbe957727254e2a0b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658471"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Az ASP.NET tároló üzembe egy tárolót beállításjegyzék Visual Studio használatával
## <a name="overview"></a>Áttekintés
Docker olyan egyszerűsített tároló, virtuális géphez, amely állomás alkalmazások és szolgáltatások segítségével bizonyos értelemben hasonló.
Ez az oktatóanyag végigvezeti a tárolóalapú alkalmazás közzététele a Visual Studio használatával egy [Azure tároló beállításjegyzék](https://azure.microsoft.com/en-us/services/container-registry).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez:

* Telepítse a legújabb verzióját [Visual Studio 2017](https://azure.microsoft.com/en-us/downloads/) "ASP.NET és a webes fejlesztési" munkaterheléssel
* Telepítés [Windows Docker](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. ASP.NET Core-webalkalmazás létrehozása
A következő lépések végigvezetik egy egyszerű ASP.NET Core alkalmazást ebben az oktatóanyagban használt létrehozása.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. A tároló közzétételére az Azure-tároló beállításjegyzék
1. Kattintson a jobb gombbal a projektre a **Megoldáskezelőben** válassza **közzététel**.
2. A publish-tároló párbeszédpanelen válassza ki a **tároló beállításjegyzék** fülre.
3. Válasszon **új Azure-tároló beállításjegyzék** kattintson **közzététel**.
4. Töltse ki a kívánt értékeket a a **hozzon létre egy új Azure-tároló beállításjegyzék**.

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-előtagja** | Globálisan egyedi név | Az neve, amely egyedileg azonosítja a tároló beállításjegyzék. |
    | **Előfizetés** | Válassza ki az előfizetését | A használandó előfizetés. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  A tároló beállításjegyzék létrehozásához az erőforráscsoport neve. Válasszon **új** egy új erőforráscsoport létrehozásához.|
    | **[TERMÉKVÁLTOZAT](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-skus)** | Standard | A tároló beállításjegyzék szolgáltatási szint  |
    | **Beállításjegyzékbeli hely** | Egy Önhöz legközelebb eső helyet | Válassza ki a helyet, a [régió](https://azure.microsoft.com/regions/) környéken vagy egyéb szolgáltatások, a tároló beállításjegyzék fog használni. |
    ![A Visual Studio Azure tároló beállításjegyzék párbeszédpanel létrehozása][0]
5. Kattintson a **Create** (Létrehozás) gombra

Akkor is most lekéréses a tároló a beállításjegyzékből bármely gazdagépre, Docker-lemezképek, például futtatására képes, vagyis [Azure tároló példányok](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
