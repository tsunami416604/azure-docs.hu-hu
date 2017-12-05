---
title: "Tároló beállításjegyzék oktatóanyag az Azure - webalkalmazást az Azure-tároló beállításjegyzék telepítése"
description: "A Linux-alapú webalkalmazás telepítése egy Azure-tárolót georeplikált beállításjegyzékből tároló lemezkép használata. A három részből sorozat két része."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: neilpeterson
tags: acr, azure-container-registry, geo-replication
keywords: "Docker, tárolók, beállításjegyzék, Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4c597ab33bf344717f82187f4f7e6e620fd64d3e
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-web-app-from-azure-container-registry"></a>Azure-tároló beállításjegyzékből webalkalmazás telepítése

Ez az egy háromrészes oktatóanyag sorozat két részre. A [rész](container-registry-tutorial-prepare-registry.md), egy saját, a georeplikált tároló beállításjegyzék lett létrehozva, és a tároló-lemezkép forrás tervezték és a beállításjegyzék leküldött. Ebben a cikkben a tároló be két különböző Azure-régiók előnyeit a hálózati bezárású szempontja, hogy a georeplikált beállításjegyzék két Web App-példány üzembe.

Ebben az oktatóanyagban a második rész az adatsorozat:

> [!div class="checklist"]
> * Tároló lemezkép telepítéséhez két *tárolók webalkalmazásait* példányok
> * Ellenőrizze a központilag telepített alkalmazás

Ha még nem hozott létre a georeplikált beállításjegyzék és a beállításjegyzéket, a tárolóalapú mintaalkalmazás képe leküldött térjen vissza az előző oktatóanyag az adatsorozat [előkészítése az Azure-tárolót georeplikált beállításjegyzékbeli](container-registry-tutorial-prepare-registry.md).

A sorozat következő része frissítse az alkalmazást, majd egy új tároló kép leküldése a beállításjegyzékben. Végezetül böngészéssel minden futó webalkalmazás-példány együtt, automatikusan megjelenik a módosítás megtekintéséhez Azure tároló beállításjegyzék georeplikáció és webhookokkal megjelenítő művelet.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>A webalkalmazások tárolók az automatikus központi telepítési

Azure tároló beállításjegyzék támogatást nyújt a tárolóalapú próbafiókokon közvetlenül [tárolók webalkalmazásait](../app-service/containers/index.yml). Az oktatóanyag segítségével az Azure-portálon az előző az oktatóanyag segítséget nyújt az Azure különböző régiókban található két webes alkalmazás tervek létrehozott tároló lemezkép központi telepítését.

Egy tároló lemezképből webalkalmazás üzembe helyezése a beállításjegyzékben, és egy georeplikált beállításjegyzék ugyanabban a régióban van, Azure tároló beállításjegyzék létrehoz egy lemezkép-telepítés [webhook](container-registry-webhook.md) meg. A tároló tárház új lemezkép leküldése, amikor a webhook szerzi be a módosítást, és automatikusan telepíti az új tároló-lemezkép a webes alkalmazást.

## <a name="deploy-a-web-app-for-containers-instance"></a>Tárolók példány webalkalmazás üzembe helyezése

Ebben a lépésben szereplő tárolókhoz példány a webalkalmazás létrehozása a *USA nyugati régiója* régióban.

Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és keresse meg a beállításjegyzéket, az előző oktatóanyag létrehozott.

Válassza ki **adattárak** > **acr-helloworld**, majd kattintson a jobb gombbal a a **v1** címke alatt **címkék** ,majd**Központi telepítése a webes alkalmazás**.

![Telepítése az app service az Azure-portálon][deploy-app-portal-01]

A **Web App az tárolókat** , amely akkor jelenik meg, a következő értékek mindegyikéhez:

| Beállítás | Érték |
|---|---|
| **Hely neve** | A webes alkalmazás globálisan egyedi nevét. Ebben a példában a formátumot használjuk `<acrName>-westus` egyszerű azonosításában a beállításjegyzék és a webes alkalmazás telepítve van a régióban. |
| **Erőforráscsoport** | **Meglévő** > `myResourceGroup` |
| **App service-csomag/hely** | Hozzon létre egy új csomagot nevű `plan-westus` a a **USA nyugati régiója** régióban. |
| **Kép** | `acr-helloworld:v1`

Válassza ki **létrehozása** a webalkalmazás a kiépítése a *USA nyugati régiója* régióban.

![A webalkalmazás az Azure portálon Linux-konfiguráció][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>A telepített webalkalmazás megtekintése

Központi telepítés befejeződése után megtekintheti a futó alkalmazások navigáljon a böngészőben az URL-CÍMÉT.

Válassza a portál **alkalmazásszolgáltatások**, akkor a webalkalmazás üzembe az előző lépésben. Ebben a példában a webes alkalmazás neve *uniqueregistryname-westus*.

A jobb felső részén jelölje ki a hiperhivatkozással ellátott a webalkalmazás URL-CÍMÉT a **App Service** áttekintése a futó alkalmazás megtekintése a böngészőben.

![A webalkalmazás az Azure portálon Linux-konfiguráció][deploy-app-portal-04]

Miután a Docker-lemezkép központi telepítésekor a georeplikált tároló beállításjegyzékből, a webhely úgy jelenik meg az Azure-régió, a tároló beállításjegyzék üzemeltető jelölő kép.

![A webböngészőben üzembe helyezett webalkalmazás][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>Tárolók példány második webalkalmazás telepítése

Az előző szakaszban leírt eljárás segítségével a második webalkalmazás üzembe helyezése a *USA keleti régiója* régióban. A **Web App az tárolókat**, adja meg a következő értékeket:

| Beállítás | Érték |
|---|---|
| **Hely neve** | A webes alkalmazás globálisan egyedi nevét. Ebben a példában a formátumot használjuk `<acrName>-eastus` egyszerű azonosításában a beállításjegyzék és a webes alkalmazás telepítve van a régióban. |
| **Erőforráscsoport** | **Meglévő** > `myResourceGroup` |
| **App service-csomag/hely** | Hozzon létre egy új csomagot nevű `plan-eastus` a a **USA keleti régiója** régióban. |
| **Kép** | `acr-helloworld:v1`

Válassza ki **létrehozása** a webalkalmazás a kiépítése a *USA keleti régiója* régióban.

![A webalkalmazás az Azure portálon Linux-konfiguráció][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>A telepített webalkalmazás megtekintése

Mint előtt, megtekintheti a futó alkalmazások által a böngészőbe az URL-re navigáláskor.

Válassza a portál **alkalmazásszolgáltatások**, akkor a webalkalmazás üzembe az előző lépésben. Ebben a példában a webes alkalmazás neve *uniqueregistryname-eastus*.

A jobb felső részén jelölje ki a hiperhivatkozással ellátott a webalkalmazás URL-CÍMÉT a **App Service áttekintése** a futó alkalmazás megtekintése a böngészőben.

![A webalkalmazás az Azure portálon Linux-konfiguráció][deploy-app-portal-07]

Miután a Docker-lemezkép központi telepítésekor a georeplikált tároló beállításjegyzékből, a webhely úgy jelenik meg az Azure-régió, a tároló beállításjegyzék üzemeltető jelölő kép.

![A webböngészőben üzembe helyezett webalkalmazás][deployed-app-eastus]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure-tárolót georeplikált beállításjegyzékből tárolók példányok két Web App telepítette. Ebben az oktatóanyagban szereplő lépések végrehajtásával meg:

> [!div class="checklist"]
> * A tároló-lemezkép központi telepítése két *tárolók webalkalmazásait* példányok
> * A telepített alkalmazás ellenőrzése

A következő oktatóanyag frissítse, majd egy új tároló-lemezkép központi telepítését, a tároló beállításjegyzék továbblépés, majd győződjön meg arról, hogy mindkét régiókban futó webalkalmazások automatikusan frissültek-e.

> [!div class="nextstepaction"]
> [Georeplikált tároló lemezkép frissítés telepítése](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png