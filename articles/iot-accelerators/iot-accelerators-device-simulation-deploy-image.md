---
title: Egyéni eszközszimulációs rendszerkép üzembe helyezése – Azure| Microsoft dokumentumok
description: Ebben az útmutatóútmutatóban megtudhatja, hogyan helyezheti üzembe az eszközszimulációs megoldás egyéni Docker-rendszerképét az Azure-ba.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61448399"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Egyéni eszközszimulációs docker-rendszerkép üzembe helyezése

Az Eszközszimulációs megoldás egyéni funkciók hozzáadásához módosíthatja. Például a [szerializálása telemetria protokollpufferek](iot-accelerators-device-simulation-protobuf.md) cikk bemutatja, hogyan adhat hozzá egy egyéni eszközt a megoldás, amely protokollpufferek (Protobuf) telemetriai adatok küldéséhez. Miután helyileg tesztelte a módosításokat, a következő lépés a módosítások üzembe helyezése az Eszközszimulációs példányban az Azure-ban. A feladat végrehajtásához létre kell hoznia és telepítenie kell egy Docker-lemezképet, amely tartalmazza a módosított szolgáltatást.

Az útmutató lépései bemutatják, hogyan:

1. Fejlesztési környezet előkészítése
1. Új Docker-lemezkép létrehozása
1. Az eszközszimuláció konfigurálása az új Docker-lemezkép használatára
1. Szimuláció futtatása az új képpel

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek végrehajtásához a következőkre van szükség:

* Telepített [eszközszimulációs](quickstart-device-simulation-deploy.md) példány.
* Docker. Töltse le a [Docker Community Edition-t](https://www.docker.com/products/docker-engine#/download) platformjára.
* Egy [Docker Hub-fiók,](https://hub.docker.com/) ahol feltöltheti a Docker-rendszerképeket. A Docker Hub-fiókban hozzon létre egy **eszközszimulációnevű**nyilvános tárházat.
* Módosított és tesztelt [eszközszimulációs megoldás](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) a helyi számítógépen. Módosíthatja például a megoldást a [telemetria imiszidására a protokollpufferek használatával.](iot-accelerators-device-simulation-protobuf.md)
* Egy héj, amely képes futtatni az SSH-t. Ha telepíti a Git For Windows, használhatja a **bash** shell, amely része a th telepítés. Használhatja az [Azure Cloud Shellt](https://shell.azure.com/)is.

A cikkben található utasítások feltételezik, hogy a Windows rendszert használja. Ha más operációs rendszert használ, előfordulhat, hogy a környezetének megfelelően módosítania kell néhány fájlelérési utat és parancsot.

## <a name="create-a-new-docker-image"></a>Új Docker-lemezkép létrehozása

Az Eszközszimulációs szolgáltatás saját módosításai telepítéséhez módosítania kell a build- és telepítési parancsfájlokat a **parancsfájlok\docker** mappában a tárolók docker-hub-fiókba való feltöltéséhez.

### <a name="modify-the-docker-scripts"></a>A docker-parancsfájlok módosítása

Módosítsa a Docker **build.cmd**, **publish.cmd**, és **run.cmd** parancsfájlok a **scripts\docker** mappában a Docker Hub tárház adatait. Ezek a lépések feltételezik, hogy létrehozott egy **eszközszimulációnevű**nyilvános tárházat:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Frissítse a **docker-compose.yml** fájlt az alábbiak szerint:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>A megoldás konfigurálása új fájlokra

Ha új eszközmodell-fájlokat adott hozzá, explicit módon fel kell tüntetnie őket a megoldásban. Adjon hozzá egy bejegyzést a **services/services.csproj** fájlhoz minden további fájlhoz. Ha például a [Szerializálási telemetriaprotokoll-pufferek útmutatójával](iot-accelerators-device-simulation-protobuf.md) végzett, adja hozzá a következő bejegyzéseket:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Új Docker-lemezképek létrehozása és leküldése a Docker Hubra

Tegye közzé az új Docker-rendszerképet a Docker Hubon a docker-hub-fiók használatával:

1. Nyisson meg egy parancssort, és keresse meg az eszközszimulációs tárház helyi példányát.

1. Keresse meg a **docker** mappát:

    ```cmd
    cd scripts\docker
    ```

1. Futtassa a következő parancsot a Docker-lemezkép létrehozásához:

    ```cmd
    build.cmd
    ```

1. Futtassa a következő parancsot a Docker-rendszerkép a Docker Hub-tárházban való közzétételéhez. Jelentkezzen be a Docker-be a Docker Hub hitelesítő adataival:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>A szolgáltatás frissítése

Ha frissíteni szeretné az Eszközszimuláció tárolót az egyéni lemezkép használatára, hajtsa végre az alábbi lépéseket:

* Az SSH segítségével csatlakozhat az eszközszimulációs példányt üzemeltető virtuális géphez. Használja az előző szakaszban feljegyezte az IP-címet és a jelszót:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Keresse meg az **/app** könyvtárat:

    ```sh
    cd /app
    ```

* A **docker-compose.yml fájl szerkesztése:**

    ```sh
    sudo nano docker-compose.yml
    ```

    Módosítsa a **lemezképet** úgy, hogy a Docker Hub-tárházba feltöltött egyéni **eszközszimulációs** rendszerképre mutasson:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Mentse a módosításokat.

* A mikroszolgáltatások újraindításához futtassa a következő parancsot:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Futtassa a szimuláció

Most már futtathat szimulációt a testreszabott eszközszimulációs megoldással:

1. Indítsa el az Eszközszimuláció webes felhasználói felületét a [Microsoft Azure IoT-megoldásgyorsítókból.](https://www.azureiotsolutions.com/Accelerators#dashboard)

1. A webes felhasználói felület segítségével konfigurálhatja és futtathatja a szimulációt. Ha korábban befejezte [a telemetriai adatokat a protokollpufferek használatával, használhatja](iot-accelerators-device-simulation-protobuf.md)az egyéni eszközmodellt.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan telepíthet egyéni eszközszimulációs lemezképet, érdemes lehet megtanulni, hogyan [használhat egy meglévő IoT-központot az eszközszimulációs megoldásgyorsítóval.](iot-accelerators-device-simulation-choose-hub.md)