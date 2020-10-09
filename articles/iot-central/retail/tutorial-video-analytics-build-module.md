---
title: Oktatóanyag – a IoT Edge Live Video Analytics-modulok módosítása
description: Ebből az oktatóanyagból megtudhatja, hogyan módosíthatja és építheti ki az élő videó elemzési átjáró modulokat, amelyeket a video Analytics-Object és a Motion Detection alkalmazás sablonja használ.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d21eb8d8d79ec04f0f7e766b4eeb370811553e64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88038372"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Oktatóanyag: az élő videó elemzési átjáró moduljainak módosítása és összeállítása

Ez az oktatóanyag bemutatja, hogyan módosíthatja a IoT Edge modul kódját az élő videó-elemzési (LVA) modulokhoz.

Az előző oktatóanyagok a modulok előre elkészített képeit használják.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez a következőkre lesz szüksége:

* [Node.js](https://nodejs.org/en/download/) V10-es vagy újabb verzió
* A [Visual Studio Code](https://code.visualstudio.com/Download) és a [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) bővítmény telepítve van
* [Docker](https://www.docker.com/products/docker-desktop) -motor
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a modulok verzióinak üzemeltetéséhez.
* Egy [Azure Media Services](https://docs.microsoft.com/azure/media-services/) -fiók. Ha befejezte az előző oktatóanyagokat, újra felhasználhatja a korábban létrehozottt.

## <a name="clone-the-repository"></a>A tárház klónozása

Ha még nem klónozotta a tárházat, a következő paranccsal klónozást végez a helyi gépen található megfelelő helyre:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Nyissa meg a local *Live-Video-Analytics* adattár MAPPÁT a vs Code használatával.

## <a name="edit-the-deploymentamd64json-file"></a>A fájl deployment.amd64.jsszerkesztése

1. Ha még nem tette meg, hozzon létre egy *ref-apps/LVA-Edge-IOT-Central-Gateway/Storage* nevű mappát a **LVA-Gateway** adattár helyi példányában. Ezt a mappát a git figyelmen kívül hagyja, hogy megakadályozza a bizalmas információk véletlen ellenőrzését.
1. Másolja a fájlt *deployment.amd64.jsa* *telepítési* mappájából a *Storage* mappába.
1. A VS Code-ban nyissa meg a *Storage/deployment.amd64.js* fájlt.
1. Szerkessze a `registryCredentials` szakaszt Azure Container Registry hitelesítő adatainak hozzáadásához.
1. A `LvaEdgeGatewayModule` modul szakasz szerkesztésével adja meg a rendszerkép nevét és az AMS-fiók nevét a következőben: `env:amsAccountName:value` .
1. Szerkessze a `lvaYolov3` modul szakaszt, és adja hozzá a rendszerkép nevét.
1. Szerkessze a `lvaEdge` modul szakaszt, és adja hozzá a rendszerkép nevét.
1. A konfiguráció végrehajtásával kapcsolatos további információkért tekintse meg a [video Analytics-alkalmazás létrehozása az Azure-ban IoT Central](tutorial-video-analytics-create-app.md) .

## <a name="build-the-code"></a>A kód létrehozása

1. Mielőtt először megpróbálja felépíteni a kódot, használja a VS Code terminált a parancs futtatásához `npm install` . Ez a parancs telepíti a szükséges csomagokat, és futtatja a telepítési parancsfájlokat.

    > [!TIP]
    > Ha az adattár GitHub-tárházának újabb verzióját kéri le, törölje a *node_modules* mappát, majd futtassa `npm install` újra.

1. Szerkessze a *./setup/imageConfig.js* fájlt a tároló beállításjegyzékének neve alapján a nevű rendszerkép frissítéséhez:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. A VS Code Terminal használatával futtassa a `docker login [your server].azurecr.io` parancsot. Használja ugyanazokat a hitelesítő adatokat, amelyeket az üzembe helyezési jegyzékben megadott a modulokhoz.

1. A VS Code Terminal használatával futtassa a **NPM Version patch** parancsot. Ez a Build szkript telepíti a lemezképeket a tároló-beállításjegyzékbe. A VS Code Terminal ablak kimenete azt mutatja, hogy a Build sikeres-e.

1. A **LvaEdgeGatewayModule** -rendszerkép verziója a Build befejeződése után minden alkalommal növekszik. Ezt a verziót kell használnia a telepítési jegyzékfájlban.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a video Analytics – Object és a mozgásérzékelő alkalmazás sablonját, valamint a LVA IoT Edge modulokat, a javasolt következő lépés az alábbiak megismerése:

> [!div class="nextstepaction"]
> [Kiskereskedelmi megoldások létrehozása az Azure IoT Centrallal](overview-iot-central-retail.md)
