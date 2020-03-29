---
title: Az IoT egyéni szimulált eszközöket telepít - Azure | Microsoft dokumentumok
description: Ez az útmutató bemutatja, hogyan telepíthet egyéni szimulált eszközöket a távoli figyelési megoldás gyorsítójára.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66427564"
---
# <a name="deploy-a-new-simulated-device"></a>Új szimulált eszköz telepítése

A távoli figyelés i és az eszközszimulációs megoldásgyorsítók egyaránt lehetővé teszik a saját szimulált eszközök meghatározását. Ez a cikk bemutatja, hogyan telepíthet egy testreszabott hűtőeszköz-típust és egy új villanykörte-eszköztípust a távoli figyelési megoldásgyorsítóba.

A cikkben leírt lépések feltételezik, hogy befejezte az új szimulált eszköz útmutató létrehozása és tesztelése című [útmutatót,](iot-accelerators-remote-monitoring-create-simulated-device.md) és rendelkezik a testreszabott hűtőt és az új villanykörte-eszköztípusokat meghatározó fájlokkal.

Az útmutató lépései bemutatják, hogyan:

1. Az SSH segítségével elérheti a távoli figyelési megoldásgyorsítót tartalmazó virtuális gép fájlrendszerét.

1. Konfigurálja a Docker-t az eszközmodellek nek a Docker-tárolón kívüli helyről történő betöltéséhez.

1. Futtassa a Távoli figyelési megoldás gyorsítót egyéni eszközmodell-fájlokkal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Az útmutató lépései végrehajtásához aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató követéséhez a következőkre van szükség:

- A [távfigyelési megoldásgyorsító telepített példánya.](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2)
- A helyi **bash** shell `ssh` `scp` futtatni a és parancsokat. A Windows, egy egyszerű módja annak, hogy telepíteni **bash** telepíteni [git](https://git-scm.com/download/win).
- Az egyéni eszközmodell-fájlok, például az [új szimulált eszköz létrehozása és tesztelése](iot-accelerators-remote-monitoring-create-simulated-device.md)című részben leírtak.

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Docker konfigurálása

Ebben a szakaszban konfigurálja a Docker-t, hogy az eszközmodell-fájlokat a **/tmp/devicemodels** mappából töltse be a virtuális gépen, nem pedig a Docker-tárolón belülről. Futtassa a parancsokat ebben a szakaszban egy **bash** shell a helyi számítógépen:

Ebben a szakaszban konfigurálja a Docker-t, hogy az eszközmodell-fájlokat a **/tmp/devicemodels** mappából töltse be a virtuális gépen, nem pedig a Docker-tárolón belülről. Futtassa a parancsokat ebben a szakaszban egy **bash** shell a helyi számítógépen:

1. Az SSH használatával csatlakozhat a virtuális géphez az Azure-ban a helyi gépről. A következő parancs feltételezi, hogy a virtuális gép **vm-vikxv nyilvános IP-címe** **104.41.128.108** - cserélje le ezt az értéket a virtuális gép előző szakaszból származó nyilvános IP-címére:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Kövesse a következő utasításokat, hogy jelentkezzen be a virtuális gépre az előző szakaszban beállított jelszóval.

1. Konfigurálja az eszközszimulációs szolgáltatást úgy, hogy az eszközmodelleket a tárolón kívülről töltse be. Először nyissa meg a Docker konfigurációs fájlját:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Keresse meg az **eszközszimulációs** tároló beállításait, és az alábbi kódrészletben látható módon szerkesztse a **kötetek** beállítását:

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

1. Másolja a meglévő eszközmodell-fájlokat a tárolóból az új helyre. Először keresse meg az eszközszimulációs tároló azonosítóját:

    ```sh
    sudo docker ps
    ```

    Ezután másolja az eszközmodell fájljait a virtuális gép **tmp** mappájába. A következő parancs feltételezi, hogy a tárolóazonosító c378d6878407 – cserélje le ezt az értéket az eszközszimulációs tároló azonosítójára:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Tartsa nyitva a **bash** ablakot az SSH munkamenettel.

1. Másolja az egyéni eszközmodell-fájlokat a virtuális gépre. Futtassa ezt a parancsot egy másik **bash** shell a gépen, ahol létrehozta az egyéni eszköz modellek. Először keresse meg az eszközmodell JSON-fájljait tartalmazó helyi mappát. A következő parancsok feltételezik, hogy a virtuális gép nyilvános IP-címe **104.41.128.108** - cserélje le ezt az értéket a virtuális gép nyilvános IP-címével. Adja meg a virtuális gép jelszavát, amikor a rendszer kéri:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Indítsa újra az eszközszimulációs Docker-tárolót az új eszközmodellek használatához. Futtassa a következő parancsokat a **bash** shell a nyitott SSH-munkamenet a virtuális gépre:

    ```sh
    sudo /app/start.sh
    ```

    Ha a futó Docker-tárolók és azok tárolóazonosítóinak állapotát szeretné látni, használja a következő parancsot:

    ```sh
    sudo docker ps
    ```

    Ha meg szeretné tekinteni az eszközszimulációs tárolónaplóját, futtassa a következő parancsot. Cserélje le a tárolóazonosítót az eszközszimulációs tároló azonosítójára:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Szimuláció futtatása

Most már használhatja az egyéni eszközmodelleket a távoli figyelési megoldásban:

1. Indítsa el a Távoli figyelés irányítópultját a [Microsoft Azure IoT-megoldásgyorsítókból.](https://www.azureiotsolutions.com/Accelerators#dashboard)

1. Az **Eszközök** lapon szimulált eszközöket adhat hozzá. Amikor új szimulált eszközt ad hozzá, az új eszközmodellek választhatók.

1. Az irányítópult segítségével megtekintheti az eszköz telemetriai és hívási eszköz módszerek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy vizsgálja meg a további, hagyja a távoli figyelési megoldás gyorsító telepítve.

Ha már nincs szüksége a megoldásgyorsítóra, törölje azt a [Kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról, jelölje ki, majd kattintson a **Megoldás törlése**gombra.

## <a name="next-steps"></a>További lépések

Ez az útmutató bemutatja, hogyan telepítheti az egyéni eszközmodelleket a távoli figyelési megoldásgyorsítóba. A javasolt következő lépés az, hogy megtanulják, hogyan [csatlakoztathat egy valódi eszközt a távoli figyelési megoldáshoz.](iot-accelerators-connecting-devices-node.md)
