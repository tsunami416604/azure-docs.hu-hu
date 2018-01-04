---
title: "Rövid útmutató – Az első Azure Container Instances-tároló létrehozása az Azure Portal segítségével"
description: "Az Azure Container Instances üzembe helyezése és az első lépések"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 823d06d8524a937a8d2098262cf97f868672f4d0
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Az első tároló létrehozása az Azure Container Instances szolgáltatásban

Az Azure Container Instances segítségével egyszerűen hozhat létre és felügyelhet tárolókat az Azure-ban. Ebben a rövid útmutatóban létrehozhat egy tárolót az Azure-ban, és közzéteheti az interneten egy nyilvános IP-címen keresztül. Ez a művelet az Azure Portal használatával hajtható végre. Néhány kattintás után ezt fogja látni a böngészőben:

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-portal-07]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-container-instance"></a>Tárolópéldány létrehozása

Válassza a következőket: **Új** > **Tárolók** > **Azure Container Instances (előnézet)**.

![Új tárolópéldány létrehozásának megkezdése az Azure Portalon][aci-portal-01]

Írja be a következő értékeket a **Tároló neve**, **Tárolórendszerkép**, és **Erőforráscsoport** szövegmezőkbe. A többi értéket hagyja az alapértelmezett értéken, majd kattintson az **OK** gombra.

* Tárolónév: `mycontainer`
* Tárolórendszerkép: `microsoft/aci-helloworld`
* Erőforráscsoport: `myResourceGroup`

![Új tárolópéldány alapbeállításainak konfigurálása az Azure Portalon][aci-portal-03]

Windows- és Linux-tárolókat is létrehozhat az Azure Container Instances használatával. Ebben a rövid ismertetőben meghagyjuk a **Linux** alapértelmezett beállítását, mivel az előző lépésben Linux-alapú tárolót (`microsoft/aci-helloworld`) adtunk meg.

Hagyja meg a **Konfiguráció** területen található többi beállítás alapértelmezett értékét, majd kattintson az **OK** gombra a konfiguráció érvényesítéséhez.

![Új tárolópéldány konfigurálása az Azure Portalon][aci-portal-04]

Miután az érvényesítés befejeződött, egy összefoglalás jelenik meg a tároló beállításairól. Kattintson az **OK** gombra a tároló üzembe helyezési kérelmének elküldéséhez.

![Új tárolópéldány beállításainak összefoglalása az Azure Portalon][aci-portal-05]

Az üzembe helyezés kezdetekor az irányítópulton egy csempe jelenik meg, amely az üzembehelyezési folyamatot jelzi. Miután az üzembe helyezés befejeződött, a csempe frissül, és az új**mycontainer-myc1** tárolócsoportot mutatja.

![Új tárolópéldány létrehozásának folyamata az Azure Portalon][aci-portal-08]

Válassza ki a **mycontainer-myc1** tárolócsoportot a tárolócsoport tulajdonságainak megjelenítéséhez. Jegyezze fel a tárolócsoport **IP-címét** és a tároló **ÁLLAPOT** alatt található értékét.

![Tárolócsoport áttekintése az Azure Portalon][aci-portal-06]

Miután a tároló **Fut** állapotra váltott, navigáljon az előző lépésben feljegyzett IP-címhez az új tárolóban található alkalmazás megjelenítéséhez.

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-portal-07]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy tárolópéldányt egy rendszerképből egy nyilvános Docker Hub adattárban. Ha próbaképpen szeretne saját maga létrehozni és üzembe helyezni egy tárolót az Azure Container Instances szolgáltatásban az Azure Container Registry használatával, folytassa az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyagai](./container-instances-tutorial-prepare-app.md)