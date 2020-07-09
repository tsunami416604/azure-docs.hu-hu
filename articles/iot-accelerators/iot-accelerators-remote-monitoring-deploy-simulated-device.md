---
title: Egyéni szimulált eszközök IoT üzembe helyezése – Azure | Microsoft Docs
description: Ez a útmutató bemutatja, hogyan helyezhet üzembe egyéni szimulált eszközöket a távoli figyelési megoldás gyorsítása érdekében.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "66427564"
---
# <a name="deploy-a-new-simulated-device"></a>Új szimulált eszköz üzembe helyezése

A távoli figyelés és az eszközök szimulációs megoldásának gyorsítása lehetővé teszi saját szimulált eszközök definiálását. Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy testreszabott Chiller-eszközt és egy új villanykörte-eszközt a távoli figyelési megoldás gyorsítása érdekében.

A cikkben ismertetett lépések azt feltételezik, hogy végrehajtotta az [új szimulált eszköz létrehozása és tesztelése](iot-accelerators-remote-monitoring-create-simulated-device.md) című útmutatót, valamint azokat a fájlokat, amelyek meghatározzák a testreszabott hűtőt és az új villanykörte típusú eszközöket.

A útmutató lépései a következőket mutatják be:

1. Használja az SSH-t a távoli figyelési megoldás-gyorsító szolgáltatást futtató virtuális gép fájlrendszerének eléréséhez.

1. Konfigurálja a Docker-t az eszköz modelljeinek a Docker-tárolón kívüli helyről való betöltéséhez.

1. Futtassa a távoli figyelési megoldás Gyorssegédjét egyéni eszköz-modell fájlok használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A jelen útmutató lépéseinek végrehajtásához aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

A útmutató követéséhez a következőkre lesz szüksége:

- A [távoli figyelési megoldás gyorsító](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2)üzembe helyezett példánya.
- Egy helyi **bash** -rendszerhéj a és a parancsok futtatásához `ssh` `scp` . Windowson a **bash** telepítésének egyszerű módja a [git](https://git-scm.com/download/win)telepítése.
- Az egyéni eszköz-modell fájljai, például az [új szimulált eszköz létrehozása és tesztelése](iot-accelerators-remote-monitoring-create-simulated-device.md)című témakörben leírtak.

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>A Docker konfigurálása

Ebben a szakaszban úgy konfigurálja a Docker-t, hogy az eszköz modell-fájljait a virtuális gép **/tmp/devicemodels** mappájából töltse be ahelyett, hogy a Docker-tárolón belül lenne. Futtassa az ebben a szakaszban található parancsokat a helyi gépen lévő **bash** -rendszerhéjban:

Ebben a szakaszban úgy konfigurálja a Docker-t, hogy az eszköz modell-fájljait a virtuális gép **/tmp/devicemodels** mappájából töltse be ahelyett, hogy a Docker-tárolón belül lenne. Futtassa az ebben a szakaszban található parancsokat a helyi gépen lévő **bash** -rendszerhéjban:

1. Az SSH használatával csatlakozzon az Azure-beli virtuális géphez a helyi gépről. Az alábbi parancs feltételezi, hogy a virtuálisgép **-vikxv** virtuális gép nyilvános IP-címe **104.41.128.108** – ezt az értéket a virtuális gép nyilvános IP-címére cseréli le az előző szakaszban:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Az utasításokat követve jelentkezzen be a virtuális gépre az előző szakaszban beállított jelszóval.

1. Konfigurálja az eszköz szimulációs szolgáltatását az eszköz modelljeinek a tárolón kívülről való betöltéséhez. Először nyissa meg a Docker konfigurációs fájlját:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Keresse meg a **devicesimulation** tároló beállításait, és szerkessze a **kötetek** beállítást az alábbi kódrészletben látható módon:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Mentse a módosításokat.

1. Másolja a meglévő eszköz-modell fájljait a tárolóból az új helyre. Először keresse meg az eszköz szimulációs tárolójának tároló-AZONOSÍTÓját:

    ```sh
    sudo docker ps
    ```

    Ezután másolja az eszköz-modell fájljait a virtuális gép **tmp** mappájába. A következő parancs feltételezi, hogy a tároló azonosítója c378d6878407 – ezt az értéket cserélje le az eszköz szimulációs tárolójának azonosítójával:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Tartsa meg a **bash** ABLAKOT az SSH-munkamenet megnyitásával.

1. Másolja az egyéni eszköz-modell fájljait a virtuális gépre. Futtassa ezt a parancsot egy másik **bash** -rendszerhéjban azon a gépen, amelyen létrehozta az egyéni eszköz modelljeit. Először Navigáljon arra a helyi mappára, amely az eszköz modell JSON-fájljait tartalmazza. A következő parancsok feltételezik, hogy a virtuális gép nyilvános IP-címe **104.41.128.108** – ezt az értéket cserélje le a virtuális gép nyilvános IP-címére. Ha a rendszer kéri, adja meg a virtuális gép jelszavát:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Indítsa újra az eszköz szimulációs Docker-tárolóját az új eszköz modellek használatához. Futtassa a következő parancsokat a **bash** -rendszerhéjban az Open SSH-munkamenettel a virtuális géphez:

    ```sh
    sudo /app/start.sh
    ```

    Ha meg szeretné tekinteni a futó Docker-tárolók és a tároló-azonosítók állapotát, használja a következő parancsot:

    ```sh
    sudo docker ps
    ```

    Ha meg szeretné tekinteni a naplót az eszköz szimulációs tárolójában, futtassa a következő parancsot. Cserélje le a Container ID-t az eszköz szimulációs tárolójának azonosítójával:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Szimuláció futtatása

Mostantól használhatja az egyéni eszközöket a távoli figyelési megoldásban:

1. Indítsa el a távoli monitorozási irányítópultot [Microsoft Azure IoT megoldás-gyorssegédek](https://www.azureiotsolutions.com/Accelerators#dashboard)közül.

1. Szimulált eszközök hozzáadásához használja az **eszközök** lapot. Új szimulált eszköz hozzáadásakor az új eszköz modelljei közül választhat.

1. Az irányítópult használatával megtekintheti az eszközök telemetria, és meghívhatja az eszköz metódusait.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha azt tervezi, hogy további felfedezést szeretne, hagyja üzembe a távoli figyelési megoldáshoz tartozó gyorssegédet.

Ha már nincs szüksége a megoldás-gyorssegédre, törölje azt a [kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról, jelölje ki, majd kattintson a **megoldás törlése**elemre.

## <a name="next-steps"></a>További lépések

Ez az útmutató bemutatja, hogyan helyezhet üzembe egyéni eszközöket a távoli figyelési megoldás-gyorsító eszközön. A következő lépés a [valós eszközök távoli figyelési megoldáshoz való csatlakoztatásának](iot-accelerators-connecting-devices-node.md)megismerése.
