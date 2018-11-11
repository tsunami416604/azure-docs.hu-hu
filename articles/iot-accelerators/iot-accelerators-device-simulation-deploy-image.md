---
title: Egyéni Eszközszimuláció rendszerképet – Azure |} A Microsoft Docs
description: Ez az útmutató elsajátíthatja, hogyan helyezhet üzembe egyéni Docker-rendszerkép az Eszközszimuláció megoldás az Azure-bA.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51284914"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Eszközszimuláció egyéni docker-rendszerkép üzembe helyezése

Módosíthatja az Eszközszimuláció megoldás egyéni funkciókat. Például a [szerializálni a telemetriai adatok segítségével Protokollpuffereket](iot-accelerators-device-simulation-protobuf.md) a cikk bemutatja, hogyan egyéni eszköz hozzáadása a megoldás, amely telemetriát küldjön (Protopuf) Protokollpuffereket használ. Módosítások helyi tesztelését, követően a következő lépés az telepítse a módosításokat az Eszközszimuláció példány az Azure-ban. Ez a feladat végrehajtásához kell létrehozni és üzembe helyezni egy Docker-rendszerképet, amely tartalmazza a módosított szolgáltatást.

A útmutatóval-to-útmutatóban leírt lépések bemutatják, hogyan való:

1. A fejlesztőkörnyezet előkészítése
1. Hozzon létre egy új Docker-rendszerképet
1. Az új Docker-rendszerkép használata az Eszközszimuláció konfigurálása
1. Az új lemezkép használatával a szimuláció futtatása

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések végrehajtásához szüksége:

* Egy telepített [Eszközszimuláció](quickstart-device-simulation-deploy.md) példány.
* A docker. Töltse le a [a Docker Community Edition](https://www.docker.com/products/docker-engine#/download) a platformhoz.
* A [Docker Hub-fiók](https://hub.docker.com/) , feltöltheti a Docker-rendszerképeket. A Docker Hub-fiókban, hozzon létre egy nyilvános tárházban nevű **eszközszimuláció**.
* A módosított és tesztelt [Eszközszimuláció megoldás](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) a helyi gépen. Például módosíthatja a megoldás [szerializálni a telemetriai adatok segítségével Protokollpuffereket](iot-accelerators-device-simulation-protobuf.md).
* Olyan felület, SSH futtathatja. Ha telepíti a Git For Windows, használhatja a **bash** -felület, th telepítést része. Is használhatja a [Azure Cloud Shell](https://shell.azure.com/).

A jelen cikkben lévő utasítások feltételezik, hogy Windows használata esetén. Ha egy másik operációs rendszert használ, szükség lehet néhány fájlelérési utakat és parancsok a környezethez illeszkedve kell módosítani.

## <a name="create-a-new-docker-image"></a>Hozzon létre egy új Docker-rendszerképet

A saját módosítások az Eszközszimuláció szolgáltatás üzembe helyezéséhez kell szerkesztenie a buildelési és üzembe helyezési parancsfájlok **scripts\docker** a docker hub-fiókjába a tárolók feltölteni kívánt mappa

### <a name="modify-the-docker-scripts"></a>A docker-parancsfájlok módosítása

Módosítsa a Docker **build.cmd**, **publish.cmd**, és **run.cmd** a szkriptek a **scripts\docker** mappát a Docker hub Adattár információit. Ezek a lépések feltételezik, hogy létrehozott egy nyilvános tárházban nevű **eszközszimuláció**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Frissítés a **docker-compose.yml** fájlt az alábbiak szerint:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>A megoldás konfigurálása arra, hogy minden olyan új fájlokról

Ha hozzáadott minden új eszköz szolgáltatásmodell-fájlokból, a megoldás tünteti szeretne. Adjon hozzá egy bejegyzést, hogy a **services/services.csproj** további fájlokat tartalmazza. Például, ha végrehajtotta a [szerializálni a telemetriai adatok segítségével Protokollpuffereket](iot-accelerators-device-simulation-protobuf.md) útmutató, adja hozzá az alábbi bejegyzéseket:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Új Docker-rendszerképek létrehozása és leküldése a Docker hubba

Tegye közzé az új Docker-rendszerképet a Docker hubba a docker hub-fiók használatával:

1. Nyisson meg egy parancssort, és keresse meg az eszköz szimulálása adattár helyi példányában.

1. Keresse meg a **docker** mappa:

    ```cmd
    cd scripts\docker
    ```

1. Futtassa a következő parancsot a Docker-rendszerkép létrehozásához:

    ```cmd
    build.cmd
    ```

1. Futtassa a következő parancsot a Docker-rendszerképet a Docker Hub adattárából közzétenni. Jelentkezzen be a Docker a Docker Hub hitelesítő adataival:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>A szolgáltatás frissítése

Az egyéni rendszerkép használatára az Eszközszimuláció tároló frissítéséhez hajtsa végre az alábbi lépéseket:

* Az SSH használatával csatlakozhat a virtuális gép, amelyen az Eszközszimuláció példány. Az IP-címet és jelszót jegyezze fel az előző szakaszban tette használja:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Keresse meg a **/app** könyvtár:

    ```sh
    cd /app
    ```

* Szerkessze a **docker-compose.yml** fájlt:

    ```sh
    sudo nano docker-compose.yml
    ```

    Módosítsa a **kép** , mutasson az egyéni **eszközszimuláció** a Docker Hub adattárából a feltöltött kép:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Mentse a módosításokat.

* A következő paranccsal indítsa újra a mikroszolgáltatás-alapú:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>A szimuláció futtatása

A szimuláció, a testreszabott Eszközszimuláció megoldás segítségével is futtathatja:

1. Indítsa el az Eszközszimuláció webes felhasználói felülete a [a Microsoft Azure IoT-Megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. A webes felhasználói felületének használatával konfigurálhatja és a egy szimuláció futtatása. Ha korábban már elvégezte [szerializálni a telemetriai adatok segítségével Protokollpuffereket](iot-accelerators-device-simulation-protobuf.md), az egyéni modell használható.

## <a name="next-steps"></a>További lépések

Most már bemutattuk, hogyan lehet egy egyéni Eszközszimuláció rendszerképének üzembe helyezéséhez, érdemes lehet megtudhatja, hogyan [egy meglévő IoT hub használata a Eszközszimuláció megoldásgyorsító](iot-accelerators-device-simulation-choose-hub.md).