---
title: Mikroszolgáltatás módosítása és újratelepítése - Azure | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja, hogyan módosíthatja és helyezheti át a mikroszolgáltatást a távoli figyelésben
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447624"
---
# <a name="customize-and-redeploy-a-microservice"></a>Mikroszolgáltatás testreszabása és ismételt üzembe helyezése

Ez az oktatóanyag bemutatja, hogyan szerkesztheti a távoli figyelési megoldás egyik [mikroszolgáltatását,](https://azure.com/microservices) hogyan hozhat létre egy mikroszolgáltatás ról, telepítheti a lemezképet a docker hubra, majd használhatja azt a távoli figyelési megoldásban. Ennek a koncepciónak a bevezetéséhez az oktatóanyag egy alapvető forgatókönyvet használ, ahol meghívja a mikroszolgáltatás API-ját, és az állapotüzenetet "Élő és jól" állapotról "Új szerkesztések itt készült!"

A távoli figyelési megoldás olyan mikroszolgáltatásokat használ, amelyek docker-lemezképek használatával készültek, amelyek et egy docker hubból leállítottak. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Mikroszolgáltatás szerkesztése és létrehozása a távoli figyelési megoldásban
> * Docker-rendszerkép létrehozása
> * Docker-rendszerkép leküldése a docker-központba
> * Az új docker-lemezkép lekérése
> * A változások megjelenítése 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez a következőkre van szükség:

>[!div class="checklist"]
> * [A távfigyelési megoldás gyorsítójának helyi telepítése](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Docker-fiók](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) - Az API-válasz megtekintéséhez szükséges

## <a name="call-the-api-and-view-response-status"></a>Az API hívása és a válasz állapotának megtekintése

Ebben a részben az alapértelmezett IoT hub-kezelő mikroszolgáltatás-API-t hívja meg. Az API egy állapotüzenetet ad vissza, amelyet később a mikroszolgáltatás testreszabásával módosít.

1. Győződjön meg arról, hogy a távoli figyelési megoldás helyileg fut a számítógépen.
2. Keresse meg, hol töltötte le a Postmant, és nyissa meg.
3. A Postman mezőbe írja be `http://localhost:8080/iothubmanager/v1/status`a következőt a GET mezőbe: .
4. Tekintse meg a visszatérést, és látnia kell, "Állapot": "OK:Élő és jól".

    ![Él és jól postás üzenet](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Az állapot módosítása és a kép létrehozása

Most módosítsa az Iot Hub Manager mikroszolgáltatás állapotüzenetét "Új szerkesztések itt történt!" majd újra a docker-rendszerképet ezzel az új állapottal. Ha itt problémákba ütközik, olvassa el [a Hibaelhárítás](#Troubleshoot) című szakaszt.

1. Győződjön meg arról, hogy a terminál meg van nyitva, és váltson arra a könyvtárra, ahol klónozta a távoli figyelési megoldást. 
1. Módosítsa a könyvtárat "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services" névre.
1. Nyissa meg StatusService.cs bármely szövegszerkesztőben vagy IDE-ben, amely tetszik. 
1. Keresse meg a következő kódrészletet:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    és változtassa meg az alábbi kódot, és mentse el.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Lépjen vissza a terminálra, de most váltson a következő könyvtárra: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Az új docker-lemezkép létrehozásához írja be a

    ```sh
    sh build
    ```
    
    vagy Windows rendszeren:
    
    ```cmd
    ./build.cmd
    ```

7. Az új lemezkép sikeres létrehozásának ellenőrzéséhez írja be a következőt:

    ```cmd/sh
    docker images 
    ```

A tárháznak "azureiotpcs/iothub-manager-dotnet" legyen.

![Sikeres docker-rendszerkép](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>A rendszerkép címkézése és leküldése
Mielőtt lelökheti az új docker-rendszerképet egy docker hubra, a Docker elvárja, hogy a rendszerképek címkével legyenek ellátva. Ha itt problémákba ütközik, olvassa el [a Hibaelhárítás](#Troubleshoot) című szakaszt.

1. Keresse meg a beírással létrehozott docker-lemezkép képazonosítóját:

    ```cmd/sh
    docker images
    ```

2. A kép megjelölése "tesztelés" típusúra

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Ha az újonnan címkézett lemezképet a docker hubra szeretné leadni, írja be a

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Nyissa meg az internetböngészőt, és lépjen a [docker hubra,](https://hub.docker.com/) és jelentkezzen be.
5. Most már látnia kell az újonnan leküldéses docker-rendszerképet a docker hubon.
![Docker-rendszerkép a docker-központban](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>A távfigyelési megoldás frissítése
Most frissítenie kell a helyi docker-compose.yml az új docker-rendszerképet a docker hubból. Ha itt problémákba ütközik, olvassa el [a Hibaelhárítás](#Troubleshoot) című szakaszt.

1. Lépjen vissza a terminálra, és váltson a következő könyvtárra: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Nyissa meg a docker-compose.yml fájlt bármely szövegszerkesztőben vagy IDE-ben, amely tetszik.
3. Keresse meg a következő kódrészletet:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    és változtassa meg, hogy néz ki, mint az alábbi képet, és mentse el.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Az új válaszállapot megtekintése
Fejezze be a távoli figyelési megoldás helyi példányának újratelepítésével és az új állapotválasz postásban való megtekintésével.

1. Lépjen vissza a terminálra, és váltson a következő könyvtárra: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Indítsa el a távoli figyelési megoldás helyi példányát a következő parancs beírásával a terminálba:

    ```cmd/sh
    docker-compose up
    ```

3. Keresse meg, hol töltötte le a Postmant, és nyissa meg.
4. A Postman mezőbe írja be a `http://localhost:8080/iothubmanager/v1/status`következő kérést a GET mezőbe: . Most látnia kell az "Állapot" szót: "OK: New Edits Made Here!".

![Új szerkesztések Made Here postman üzenet](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Elhárítása

Ha problémákba ütközik, próbálja meg eltávolítani a docker-rendszerképeket és -tárolókat a helyi számítógépen.

1. Az összes tároló eltávolításához először le kell állítania az összes futó tárolót. Nyissa meg a terminált és írja be

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Az összes kép eltávolításához nyissa meg a terminált, és írja be a 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Ellenőrizheti, hogy vannak-e konténerek a gépen, ha beírja a

    ```cmd/sh
    docker ps -aq 
    ```

    Ha sikeresen eltávolította az összes tárolót, semmi sem jelenik meg.

4. Ellenőrizheti, hogy vannak-e képek a készüléken, ha beírja a

    ```cmd/sh
    docker images
    ```

    Ha sikeresen eltávolította az összes tárolót, semmi sem jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben a bemutatóban látta, hogyan:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Mikroszolgáltatás szerkesztése és létrehozása a távoli figyelési megoldásban
> * Docker-rendszerkép létrehozása
> * Docker-rendszerkép leküldése a docker-központba
> * Az új docker-lemezkép lekérése
> * A változások megjelenítése 

A következő dolog, hogy megpróbálja [az eszköz szimulátor mikroszolgáltatás a távoli figyelési megoldás](iot-accelerators-microservices-example.md)

A távoli figyelési megoldással kapcsolatos fejlesztői információk a következő témakörben találhatóak:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

