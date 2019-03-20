---
title: Módosítása és ismételt üzembe helyezése a mikroszolgáltatások – Azure |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan módosíthatja, és telepítse újra a távoli figyelési mikroszolgáltatások
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004108"
---
# <a name="customize-and-redeploy-a-microservice"></a>Mikroszolgáltatás testreszabása és ismételt üzembe helyezése

Ez az oktatóanyag bemutatja, hogyan szerkesztheti az egyik a [mikroszolgáltatások](https://azure.com/microservices) a távoli figyelési megoldás létrehozása a mikroszolgáltatási képe, a rendszerkép üzembe helyezése a docker hub és indítására használhatja a távoli figyelési megoldás. Bevezetni a fogalom, az oktatóanyagban egy egyszerű forgatókönyvet, amelyen mikroszolgáltatások API hívása és módosíthatja az állapotüzenet tartalmazza a "Tartási és jól" "Új szerkeszti Made itt!"

Távoli megfigyelési megoldás használ, amelyek felhasználásával történik, amely a docker hub kikerülnek docker-rendszerképeket. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Szerkesztheti, és a távoli figyelési megoldásban mikroszolgáltatások készítése
> * Elkészíthet egy docker-rendszerképet
> * Docker-rendszerkép leküldése a docker hub
> * Kérje le az új docker-rendszerképet
> * A változások megjelenítése 

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag az alábbiak szükségesek:

>[!div class="checklist"]
> * [A távoli figyelési megoldásgyorsító helyileg üzembe helyezése](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Egy Docker-fiók](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) – az API-válasz megtekintéséhez szükséges

## <a name="call-the-api-and-view-response-status"></a>Hívja az API-t és a nézet válasz állapota

Ez a rész az alapértelmezett IoT hub manager mikroszolgáltatás API hívható meg. Az API-t adja vissza egy állapotüzenetet, amely szerint a mikroszolgáltatás testreszabása később módosíthatja.

1. Győződjön meg arról, hogy a távoli figyelési megoldás helyben fut a gépen.
2. Keresse meg a letöltött Postman, és nyissa meg.
3. A Postman adja meg a következő GET: `http://localhost:8080/iothubmanager/v1/status`.
4. A visszaadandó megtekintése, és megjelenik, "Állapot": "OK: tartási és jól".

    ![Tartási és jól Postman-üzenet](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Az állapotváltás és a rendszerkép létrehozásához

Módosítsa az "Új módosításokat az itt végzett!" az Iot Hub-kezelőből mikroszolgáltatás állapotüzenet és ezután építse újra az új állapot a docker-rendszerképet. Ha itt problémákat tapasztal, tekintse meg a [hibaelhárítás](#Troubleshoot) szakaszban.

1. Ellenőrizze, hogy meg nyitva a terminált, és váltson arra a könyvtárra, amelybe klónozta van a távoli figyelési megoldás. 
1. Módosítsa a címtárban "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Nyissa meg StatusService.cs bármilyen szövegszerkesztővel, vagy IDE, adja meg. 
1. Keresse meg a következő kódrészletet:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    és módosítsa az alábbi kódot, és mentse azt.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Lépjen vissza a terminálban, de módosítsa a következő könyvárra: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Írja be az új docker-rendszerkép létrehozásához

    ```sh
    sh build
    ```
    
    vagy a Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Annak ellenőrzéséhez, hogy az új rendszerkép sikeresen létrejött, írja be a

    ```cmd/sh
    docker images 
    ```

A tárház "azureiotpcs/iothub-kezelője – dotnet" kell lennie.

![A sikeres docker-rendszerkép](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>A rendszerkép címkézése és leküldése
Az új docker-rendszerkép leküldése a docker hub, előtt a Docker vár a képek lesz megjelölve. Ha itt problémákat tapasztal, tekintse meg a [hibaelhárítás](#Troubleshoot) szakaszban.

1. Keresse meg a lemezkép-azonosító, a docker-rendszerkép létrehozott beírásával:

    ```cmd/sh
    docker images
    ```

2. A címke a rendszerkép "tesztelés" típusú

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Az újonnan címkézett rendszerkép leküldése a docker hub, írja be a következőt

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Nyissa meg a webböngészőben, és nyissa meg a [docker hub](https://hub.docker.com/) , és jelentkezzen be.
5. Meg kell jelennie az újonnan leküldött docker-rendszerképet a docker hubon.
![Docker-rendszerképet a docker hubon](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>A távoli figyelési megoldás frissítése
Akkor most frissítenie kell a helyi docker-compose.yml lekérni az új docker-rendszerképet a docker hubról. Ha itt problémákat tapasztal, tekintse meg a [hibaelhárítás](#Troubleshoot) szakaszban.

1. Lépjen vissza a terminálon, és módosítsa a következő könyvtárban: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Nyissa meg a docker-compose.yml bármilyen szövegszerkesztővel, vagy IDE, adja meg.
3. Keresse meg a következő kódrészletet:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    és módosítsa az alábbi képhez hasonlóan, és mentse azt.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Az új válasz állapotának megtekintése
Végül újbóli üzembe helyezés a távoli figyelési megoldás helyi példányát, és az új válasz állapotának megtekintése a Postman.

1. Lépjen vissza a terminált, és módosítsa a következő könyvárra: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Indítsa el a távoli figyelési megoldás a helyi példányát, írja be a következő parancsot a terminálon:

    ```cmd/sh
    docker-compose up
    ```

3. Keresse meg a letöltött Postman, és nyissa meg.
4. A Postman adja meg a következő GET kérelmet: `http://localhost:8080/iothubmanager/v1/status`. Meg kell jelennie, "Állapot": "OK: Új, az itt végzett módosítások! ".

![Új szerkesztése Itt a végrehajtott postman üzenet](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Hibaelhárítása

Ha problémákat futtatja, távolítsa el a docker-rendszerképeket és a tárolók a helyi gépen.

1. Szeretné eltávolítani az összes tárolót, először szüksége minden futó tárolók leállításához. Nyissa meg a terminált, és típusa

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Távolítsa el az összes rendszerkép, nyissa meg a terminált, és írja be 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Ellenőrizheti, hogy van-e bármilyen tárolók a gépen beírásával

    ```cmd/sh
    docker ps -aq 
    ```

    Sikerült eltávolítani az összes tárolót, ha semmit nem kell jelennek meg.

4. Ellenőrizheti, hogy van-e képeket a gépen beírásával

    ```cmd/sh
    docker images
    ```

    Sikerült eltávolítani az összes tárolót, ha semmit nem kell jelennek meg.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan lehet:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Szerkesztheti, és a távoli figyelési megoldásban mikroszolgáltatások készítése
> * Elkészíthet egy docker-rendszerképet
> * Docker-rendszerkép leküldése a docker hub
> * Kérje le az új docker-rendszerképet
> * A változások megjelenítése 

Próbálkozzon a következő lépésben hozzunk [a távoli figyelési megoldásban az eszköz szimulátor mikroszolgáltatás testreszabása](iot-accelerators-microservices-example.md)

A távoli figyelési megoldás fejlesztői ismertetését lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

