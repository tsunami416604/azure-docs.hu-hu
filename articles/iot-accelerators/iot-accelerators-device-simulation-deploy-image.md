---
title: Egyéni eszköz szimulációs rendszerképének üzembe helyezése – Azure | Microsoft Docs
description: Ebben a útmutatóban megismerheti, hogyan helyezhet üzembe egy egyéni Docker-rendszerképet az eszköz-szimulációs megoldásról az Azure-ban.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "61448399"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Egyéni eszköz-szimulációs Docker-rendszerkép üzembe helyezése

Az eszköz-szimulációs megoldás egyéni funkciók hozzáadására is módosítható. Például a [szerializálási telemetria a protokoll-pufferek használatával](iot-accelerators-device-simulation-protobuf.md) című cikk bemutatja, hogyan adhat hozzá olyan egyéni eszközt a megoldáshoz, amely protokoll-puffereket (protopuf) használ a telemetria küldéséhez. A módosítások helyi tesztelése után a következő lépés az Azure-ban az eszköz-szimulációs példány módosításainak üzembe helyezése. A feladat elvégzéséhez létre kell hoznia és telepítenie kell egy Docker-rendszerképet, amely tartalmazza a módosított szolgáltatást.

A útmutató lépései a következőket mutatják be:

1. Fejlesztési környezet előkészítése
1. Új Docker-rendszerkép létrehozása
1. Az eszköz szimulációjának konfigurálása az új Docker-rendszerkép használatára
1. Szimuláció futtatása az új rendszerkép használatával

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

* Egy üzembe helyezett [eszköz szimulációs](quickstart-device-simulation-deploy.md) példánya.
* Docker. Töltse le a platform [Docker Community Edition kiadását](https://www.docker.com/products/docker-engine#/download) .
* Egy [Docker hub-fiók](https://hub.docker.com/) , amelyen feltöltheti a Docker-rendszerképeit. Hozzon létre egy **eszköz-szimuláció**nevű nyilvános tárházat a Docker hub-fiókjában.
* Módosított és tesztelt [eszköz-szimulációs megoldás](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) a helyi gépen. Módosíthatja például a megoldást, hogy a [telemetria szerializálja a protokoll-pufferek használatával](iot-accelerators-device-simulation-protobuf.md).
* Egy olyan rendszerhéj, amely képes az SSH futtatására. Ha a git for Windowst telepíti, használhatja a th telepítés részét képező **bash** shellt. Használhatja a [Azure Cloud Shell](https://shell.azure.com/)is.

A cikkben szereplő utasítások feltételezik, hogy a Windowst használja. Ha másik operációs rendszert használ, előfordulhat, hogy módosítania kell néhány fájl elérési útját és parancsát a környezetének megfelelően.

## <a name="create-a-new-docker-image"></a>Új Docker-rendszerkép létrehozása

Ha saját módosításokat kíván üzembe helyezni az eszköz-szimulációs szolgáltatásban, szerkesztenie kell a build és a Deployment szkripteket a **scripts\docker** mappában a tárolók Docker-hub-fiókba való feltöltéséhez.

### <a name="modify-the-docker-scripts"></a>A Docker-parancsfájlok módosítása

Módosítsa a Docker **Build. cmd**és a **Publish. cmd**fájlt, majd futtassa a **scripts\docker** mappában található **. cmd** parancsfájlokat a Docker hub adattárával kapcsolatos információkkal. Ezek a lépések feltételezik, hogy létrehozott egy **eszköz-szimuláció**nevű nyilvános tárházat:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Frissítse a **Docker-compose. YML** fájlt a következőképpen:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>A megoldás konfigurálása új fájlok belefoglalásához

Ha új eszköz-modell-fájlokat adott hozzá, explicit módon fel kell vennie azokat a megoldásba. Adjon hozzá egy bejegyzést a **Services/Services. csproj** -hez minden további belefoglalni kívánt fájlhoz. Ha például elvégezte a [szerializálási telemetria a protokoll-pufferek használatával](iot-accelerators-device-simulation-protobuf.md) , adja hozzá a következő bejegyzéseket:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Új Docker-rendszerképek előállítása és leküldése a Docker hub-ra

Tegye közzé az új Docker-rendszerképet a Docker hub használatával a Docker-hub-fiókkal:

1. Nyisson meg egy parancssort, és navigáljon az eszköz szimulációs tárházának helyi példányához.

1. Navigáljon a **Docker** mappájához:

    ```cmd
    cd scripts\docker
    ```

1. Futtassa a következő parancsot a Docker-rendszerkép létrehozásához:

    ```cmd
    build.cmd
    ```

1. Futtassa a következő parancsot a Docker-rendszerkép közzétételéhez a Docker hub-tárházban. Jelentkezzen be a Docker-be a Docker hub hitelesítő adataival:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>A szolgáltatás frissítése

Az eszköz szimulációs tárolójának az egyéni rendszerkép használatára való frissítéséhez hajtsa végre a következő lépéseket:

* Használja az SSH-t az eszköz szimulációs példányát üzemeltető virtuális géphez való kapcsolódáshoz. Használja az előző szakaszban jegyzett IP-címet és jelszót:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navigáljon a **/app** könyvtárba:

    ```sh
    cd /app
    ```

* Szerkessze a **Docker-compose. YML** fájlt:

    ```sh
    sudo nano docker-compose.yml
    ```

    Módosítsa a **rendszerképet** úgy, hogy a Docker hub-adattárba feltöltött egyéni **eszköz-szimulációs** képet mutassa:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Mentse a módosításokat.

* Futtassa a következő parancsot a Service-szolgáltatások újraindításához:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>A szimuláció futtatása

Most már futtathat egy szimulációt a testreszabott eszköz-szimulációs megoldás használatával:

1. Indítsa el az eszköz szimulációs webes FELÜLETét [Microsoft Azure IoT megoldás-gyorssegédek](https://www.azureiotsolutions.com/Accelerators#dashboard)közül.

1. A webes KEZELŐFELÜLET használatával konfigurálhatja és futtathatja a szimulációkat. Ha korábban befejezte a [szerializálási telemetria a protokoll-pufferek használatával](iot-accelerators-device-simulation-protobuf.md), használhatja az egyéni eszköz modelljét is.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan helyezhet üzembe egy egyéni eszköz-szimulációs lemezképet, érdemes megismernie, hogyan [használhat egy meglévő IoT hubot az eszköz-szimulációs megoldás-gyorsító használatával](iot-accelerators-device-simulation-choose-hub.md).