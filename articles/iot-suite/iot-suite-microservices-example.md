---
title: Módosítsa, majd telepítse újra a mikroszolgáltatási |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan módosítható, és telepítse újra a mikroszolgáltatási távoli felügyelete
services: ''
suite: iot-suite
author: giyeh
manager: hegate
ms.author: giyeh
ms.service: iot-suite
ms.date: 04/19/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: b9be74b4ef5a1239f6ce753ebf41af6b5dbacb5e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="customize-and-redeploy-a-microservice"></a>Testre szabhatja, és telepítse újra a mikroszolgáltatási

Ez az oktatóanyag bemutatja, hogyan módosítja a távoli figyelésére szolgáló megoldás a mikroszolgáltatások valamelyikét, a mikroszolgáltatási lemezképet készít, a lemezképet a docker-központnak és majd használni a távoli figyelésére szolgáló megoldás. A fogalom szemléltetésére az oktatóprogram egy alapvető forgatókönyv, ahol egy mikroszolgáltatási API hívása, és módosítsa az állapotüzenet tartalmazza a "Életben és jól" az "Új szerkesztése Made itt!"

Távoli figyelés a megoldás, amely egy docker-hubhoz kikerülnek docker lemezképek használatával készített mikroszolgáltatások használ. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Szerkesztése és a távoli figyelésére szolgáló megoldás az olyan mikroszolgáltatási létrehozása
> * Docker-lemezkép elkészítése
> * A docker-lemezkép leküldése a docker hub
> * Az új docker-lemezkép lekéréses
> * A módosítások megjelenítése 

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag az alábbiak szükségesek:

>[!div class="checklist"]
> * [A megoldás üzembe helyezéséhez távoli figyelési előre konfigurált helyileg](../iot-accelerators/iot-accelerators-remote-monitoring-deploy-local.md)
> * [Egy Docker-fiók](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) - szükséges API-válaszból megtekintése

## <a name="call-the-api-and-view-response-status"></a>Hívja az API és a nézet response állapot

Ez a kijelző meghívja az alapértelmezett IoT hub manager mikroszolgáltatási API. Az API-t adja vissza egy állapotüzenetet, amely a mikroszolgáltatási testreszabásával később módosíthatja.

1. Győződjön meg arról, hogy a távoli figyelésére szolgáló megoldás helyben fut a számítógépen.
2. Keresse meg, amelybe letöltötte a Postman, majd nyissa meg.
3. Postman, adja meg a GET a következő: http://localhost:8080/iothubmanager/v1/status.
4. A visszatérési tekintheti meg és kell megjelennie, "Status": "OK: Alive és jól".
![Alive és jól Postman üzenet](media/iot-suite-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>A következő állapotokat és a lemezkép

Mostantól megváltoztatható az "Új módosításokat végezni itt!" az Iot Hub Manager mikroszolgáltatási állapotüzenet és majd építse újra az új állapot a docker képet. Ha itt problémákat tapasztal, tekintse meg a [hibaelhárítás](#Troubleshoot) szakasz.

1. Győződjön meg arról, hogy nyitva-e a terminálon, és módosítsa a könyvtárat, ahol a távoli figyelésére szolgáló megoldás rendelkezik klónozása. 
2. Módosítsa a könyvtárat, a ".. azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/WebService/v1/Controllers".
3. Nyissa meg a StatusController.cs bármely szöveg- vagy kínálatából IDE. 
4. Keresse meg a következő kódot:

    ```javascript
    return new StatusApiModel(true, "Alive and well");
    ```

    és módosítsa azt a kódot, és mentse.

    ```javascript
    return new StatusApiModel(true, "New Edits Made Here!");
    ```

5. Lépjen vissza a terminálon, de most nyissa meg azt a következő könyvtárat: "... azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/scripts/docker".
6. Írja be az új docker-lemezkép létrehozásához

    ```cmd/sh
    sh build
    ```

7. Ellenőrizze, hogy az új lemezképet sikeresen létrejött-e, írja be a következőt

    ```cmd/sh
    docker images 
    ```

A tárház "azureiotpcs/IOT hubbal-manager-dotnet" kell lennie.

![Sikeres docker kép](media/iot-suite-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Címke és leküldéses a kép
Mielőtt az új docker-lemezkép egy docker-hubhoz tolható ki, Docker vár a képek történő címkézését. Ha itt problémákat tapasztal, tekintse meg a [hibaelhárítás](#Troubleshoot) szakasz.

1. Keresse meg a lemezkép-Azonosítót a docker kép létrehozott beírásával:

    ```cmd/sh
    docker images
    ```

2. A lemezkép címkézésére "tesztelés" típussal.

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Az újonnan TIFF leküldése a docker hub, írja be a következőt

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Nyissa meg a böngészőben, és navigáljon a [docker hub](https://hub.docker.com/) , jelentkezzen be.
5. Meg kell jelennie a újonnan megnyomott docker-lemezkép a docker központ.
![A docker központban docker kép](media/iot-suite-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>A távoli figyelésére szolgáló megoldás frissítése
Most a helyi docker-compose.yml az új docker-lemezkép a docker hubról le tudja frissíteni kell. Ha itt problémákat tapasztal, tekintse meg a [hibaelhárítás](#Troubleshoot) szakasz.

1. Lépjen vissza a terminál és nyissa meg azt a következő könyvtárat: "... Azure-IOT-PCs-Remote-monitoring-DotNet/scripts/Local".
2. Nyissa meg a docker-compose.yml bármely szöveg- vagy kínálatából IDE.
3. Keresse meg a következő kódot:

    ```docker
    image: azureiotpcs/pcs-auth-dotnet:testing
    ```

    és módosítsa úgy, hogy az alábbi képen hasonló, és mentse azt.

    ```cmd/sh
    image: [docker ID]/pcs-auth-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Az új válasz állapotának megtekintése
Ismételt üzembe helyezéssel a távoli figyelésére szolgáló megoldás helyi példányát, és tekintse meg az új állapotválasz a Postman befejezéséhez.

1. Lépjen vissza a terminálon, és nyissa meg azt a következő könyvtárat: "... Azure-IOT-PCs-Remote-monitoring-DotNet/scripts/Local".
2. Indítsa el a távoli figyelésére szolgáló megoldás helyi példányát a terminálon a következő parancs beírásával:

    ```cmd/sh
    docker-compose up
    ```

3. Keresse meg, amelybe letöltötte a Postman, majd nyissa meg.
4. Postman, adja meg a GET a következő kérelmet: http://localhost:8080/iothubmanager/v1/status. Meg kell jelennie, "Status": "OK: az itt végzett módosítások új!".

![Új szerkesztése Itt a végrehajtott postman üzenet](media/iot-suite-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Hibáinak elhárítása

Ha a probléma futtatja, távolítsa el a docker-lemezképek és a tárolók a helyi számítógépen.

1. Az összes tároló eltávolításához először az összes futó tároló leállítására. Nyissa meg a terminál és típusa

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Távolítsa el az összes lemezkép, nyissa meg a terminált, és írja be 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Ellenőrizheti, ha nincsenek tárolókkal a számítógépen írja be a

    ```cmd/sh
    docker ps -aq 
    ```

    Ha sikeresen eltávolította az összes tároló, semmi sem kell jelenik meg.

4. Ellenőrizheti, ha nincsenek képeket a számítógépen írja be a

    ```cmd/sh
    docker images
    ```

    Ha sikeresen eltávolította az összes tároló, semmi sem kell jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Szerkesztése és a távoli figyelésére szolgáló megoldás az olyan mikroszolgáltatási létrehozása
> * Docker-lemezkép elkészítése
> * A docker-lemezkép leküldése a docker hub
> * Az új docker-lemezkép lekéréses
> * A módosítások megjelenítése 

A következő művelet kipróbálásához [az eszköz szimulátor mikroszolgáltatási a távoli figyelésére szolgáló megoldás a testreszabása](iot-suite-remote-monitoring-test.md)

A távoli figyelésére szolgáló megoldás fejlesztői ismertetését lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

