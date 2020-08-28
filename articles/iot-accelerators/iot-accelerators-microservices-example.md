---
title: Webszolgáltatás módosítása és újbóli üzembe helyezése – Azure | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan módosíthatja és telepítheti újra a szolgáltatást a távoli Figyelésben
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ff3e12721628e244f247e174af101e71ea91191
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998322"
---
# <a name="customize-and-redeploy-a-microservice"></a>Mikroszolgáltatás testreszabása és ismételt üzembe helyezése

Ebből az oktatóanyagból megtudhatja, hogyan szerkesztheti a távoli figyelési megoldás egyik [szolgáltatását](https://azure.com/microservices) , hogyan készíthet lemezképet a szolgáltatásról, hogyan helyezheti üzembe a lemezképet a Docker hub-ban, és hogyan használhatja azt a távoli figyelési megoldásban. A koncepció bevezetéséhez az oktatóanyag egy olyan alapszintű forgatókönyvet használ, amelyben egy Service API-t hív meg, és az állapotjelző üzenetet "Alive and Well" értékről "új szerkesztésre" változtatja.

A távoli figyelési megoldás olyan, a Docker-központból lekért Docker-rendszerképeket használó, 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Webszolgáltatás szerkesztése és létrehozása a távoli figyelési megoldásban
> * Docker-rendszerkép létrehozása
> * Docker-rendszerkép leküldése a Docker-hubhoz
> * Az új Docker-rendszerkép lekérése
> * A módosítások megjelenítése 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez a következőkre lesz szüksége:

>[!div class="checklist"]
> * [A távoli figyelési megoldás-gyorsító helyi telepítése](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Egy Docker-fiók](https://hub.docker.com/)
> * [Poster](https://www.getpostman.com/) – az API-válasz megtekintéséhez szükséges

## <a name="call-the-api-and-view-response-status"></a>Az API meghívása és a válasz állapotának megtekintése

Ebben a részben az alapértelmezett IoT hub Manager Service API-t hívja meg. Az API egy állapotjelző üzenetet ad vissza, amelyet később a szolgáltatás testreszabásával módosíthat.

1. Győződjön meg arról, hogy a távoli figyelési megoldás helyileg fut a gépen.
2. Keresse meg a Poster letöltött helyét, és nyissa meg.
3. A Poster mezőben adja meg a következőt a GET: `http://localhost:8080/iothubmanager/v1/status` .
4. Tekintse meg a visszaadott értéket, és tekintse meg az "állapot": "OK: Alive and Well" kifejezést.

    ![Élő és jó postás üzenet](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Az állapot módosítása és a rendszerkép felépítése

Most módosítsa az IOT hub Manager-szolgáltatás állapotjelző üzenetét az "új szerkesztés ide!" értékre. Ezután hozza létre újra a Docker-rendszerképet ezzel az új állapottal. Ha itt problémákba ütközik, tekintse meg a [hibaelhárítási](#Troubleshoot) szakaszt.

1. Győződjön meg arról, hogy a terminál nyitva van, és váltson arra a könyvtárra, ahol a távoli figyelési megoldást klónozotta. 
1. Módosítsa a könyvtárat "Azure-IOT-PCs-Remote-Monitoring-DotNet/Services/iothub-Manager/Services" értékre.
1. Nyissa meg a StatusService.cs bármely olyan szövegszerkesztőben vagy IDE, amelyet szeretne. 
1. Keresse meg a következő kódot:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    és módosítsa az alábbi kódra, és mentse.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Lépjen vissza a terminálra, de most váltson a következő könyvtárra: "Azure-IOT-PCs-Remote-Monitoring-DotNet/Services/iothub-Manager/Scripts/Docker".
6. Az új Docker-rendszerkép létrehozásához írja be a következőt

    ```sh
    sh build
    ```
    
    vagy Windows rendszeren:
    
    ```cmd
    ./build.cmd
    ```

7. Az új rendszerkép sikeres létrehozásának ellenőrzéséhez írja be a következőt:

    ```cmd/sh
    docker images 
    ```

A tárháznak "azureiotpcs/iothub-Manager-DotNet" értékűnek kell lennie.

![Sikeres Docker-rendszerkép](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>A rendszerkép címkézése és leküldése
Mielőtt leküldi az új Docker-rendszerképet egy Docker-hubhoz, a Docker a képek címkézését várja. Ha itt problémákba ütközik, tekintse meg a [hibaelhárítási](#Troubleshoot) szakaszt.

1. Keresse meg az alábbiak beírásával létrehozott Docker-rendszerkép képazonosítóját:

    ```cmd/sh
    docker images
    ```

2. A rendszerkép címkézése a "tesztelés" típussal

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Ha az újonnan címkézett képet a Docker hub-ra szeretné leküldeni, írja be a következőt

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Nyissa meg az Internet böngészőt, és lépjen a [Docker hub](https://hub.docker.com/) -ba, és jelentkezzen be.
5. Ekkor megjelenik az újonnan leküldett Docker-rendszerkép a Docker hub-on.
![Docker-rendszerkép a Docker hub-ban](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>A távoli figyelési megoldás frissítése
Most frissítenie kell a helyi Docker-compose. YML, hogy lekérje az új Docker-rendszerképet a Docker hub-ból. Ha itt problémákba ütközik, tekintse meg a [hibaelhárítási](#Troubleshoot) szakaszt.

1. Lépjen vissza a terminálra, és váltson a következő könyvtárra: "Azure-IOT-PC-Remote-Monitoring-DotNet/Services/Scripts/local".
2. Nyissa meg a Docker-compose. YML alkalmazást bármilyen szövegszerkesztőben vagy IDE, amelyet szeretne.
3. Keresse meg a következő kódot:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    és módosítsa úgy, hogy az alábbi képre hasonlítson, és mentse.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Az új válasz állapotának megtekintése
Fejezze be a távoli figyelési megoldás helyi példányának újbóli üzembe helyezését, és tekintse meg az új állapot választ a Poster-ben.

1. Lépjen vissza a terminálra, és váltson a következő könyvtárra: "Azure-IOT-PC-Remote-Monitoring-DotNet/Scripts/local".
2. Indítsa el a távoli figyelési megoldás helyi példányát úgy, hogy beírja a következő parancsot a terminálba:

    ```cmd/sh
    docker-compose up
    ```

3. Keresse meg a Poster letöltött helyét, és nyissa meg.
4. A Poster mezőben adja meg a következő kérelmet a GET: `http://localhost:8080/iothubmanager/v1/status` . Ekkor a "status" (állapot): "OK: új szerkesztés itt!" kifejezés látható.

![Új szerkesztési üzenet](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Hibaelhárítás

Ha problémákba ütközik, próbálja meg eltávolítani a Docker-lemezképeket és-tárolókat a helyi gépen.

1. Az összes tároló eltávolításához először le kell állítania az összes futó tárolót. Nyissa meg a terminált és a típust

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Az összes rendszerkép eltávolításához nyissa meg a terminált és a típust 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. A következő beírásával ellenőrizhető, hogy vannak-e tárolók a gépen:

    ```cmd/sh
    docker ps -aq 
    ```

    Ha sikeresen eltávolította az összes tárolót, semmi nem jelenik meg.

4. A következő beírásával ellenőrizhető, hogy vannak-e lemezképek a gépen

    ```cmd/sh
    docker images
    ```

    Ha sikeresen eltávolította az összes tárolót, semmi nem jelenik meg.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket látta el:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Webszolgáltatás szerkesztése és létrehozása a távoli figyelési megoldásban
> * Docker-rendszerkép létrehozása
> * Docker-rendszerkép leküldése a Docker-hubhoz
> * Az új Docker-rendszerkép lekérése
> * A módosítások megjelenítése 

A következő lépés a [Device Simulator-szolgáltatás testreszabása a távoli figyelési megoldásban](iot-accelerators-microservices-example.md)

A távoli figyelési megoldással kapcsolatos további információkért lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

