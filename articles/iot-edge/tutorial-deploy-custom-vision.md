---
title: Tutorial deploy Custom Vision classifier to a device - Azure IoT Edge | Microsoft Docs
description: In this tutorial, learn how to make a computer vision model run as a container using Custom Vision and IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3418c57493e19580f0d3dbd9ea979b0322d930b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457289"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Oktatóanyag: Képosztályozás végrehajtása a peremhálózaton a Custom Vision Service használatával

Az Azure IoT Edge hatékonyabbá teheti IoT-megoldását a felhőben található számítási feladatok peremhálózatra történő áthelyezése által. Ez a képesség hasznos a nagy mennyiségű adatot feldolgozó szolgáltatások, például számítógépes látástechnológiai modellek esetében. A [Custom Vision Service](../cognitive-services/custom-vision-service/home.md) szolgáltatással egyéni képosztályozókat hozhat létre és helyezhet üzembe tárolóként egy eszközön. Ezzel a két szolgáltatással megállapításokat tehet képekről vagy videostreamekről anélkül, hogy az adatokat először át kellene helyeznie az oldalról. A Custom Vision egy olyan osztályozót biztosít, amely a megállapítások létrehozásához összehasonlítja a képet egy betanított modellel.

Egy IoT Edge-eszközön található Custom Vision például meg tudja határozni, hogy egy autópályán az átlagosnál nagyobb vagy kisebb-e a forgalom, vagy hogy egy parkolóház egy bizonyos sorában elérhetők-e parkolóhelyek. Ezeket a megállapításokat műveletvégzés céljából meg lehet osztani egy másik eszközzel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Képosztályozó létrehozása a Custom Vision használatával.
> * IoT Edge-modul létrehozása, amely lekérdezi a Custom Vision eszközön található webkiszolgálót.
> * A képosztályozó eredményeinek küldése az IoT Hubnak.

<center>

![Diagram - Tutorial architecture, stage and deploy classifier](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

>[!TIP]
>This tutorial is a simplified version of the [Custom Vision and Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) sample project. This tutorial was designed to run on a cloud VM and uses static images to train and test the image classifier, which is useful for someone just starting to evaluate Custom Vision on IoT Edge. The sample project uses physical hardware and sets up a live camera feed to train and test the image classifier, which is useful for someone who wants to try a more detailed, real-life scenario.

Before beginning this tutorial, you should have gone through the previous tutorial to set up your environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing that tutorial, you should have the following prerequisites in place: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module with the Custom Vision service, install the following additional prerequisites on your development machine: 

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Python extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 

## <a name="build-an-image-classifier-with-custom-vision"></a>Képosztályozó létrehozása a Custom Vision használatával

Képosztályozó létrehozásakor létre kell hoznia egy Custom Vision-projektet, és meg kell adnia betanítási képeket. A szakaszban elvégezhető lépésekkel kapcsolatos további információkért tekintse meg az [Osztályozó létrehozása a Custom Vision használatával](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md) című cikket.

A képosztályozó létrehozása és betanítása után exportálhatja azt Docker-tárolóként, és üzembe helyezheti egy IoT Edge-eszközön. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

1. A webböngészőben lépjen a [Custom Vision weblapjára](https://customvision.ai/).

2. Válassza a **Sign in** (Bejelentkezés) gombot, és jelentkezzen be az Azure-erőforrásokhoz használt fiókkal. 

3. Válassza a **New project** (Új projekt) lehetőséget.

4. Hozza létre a projektet az alábbi értékekkel:

   | Mező | Value (Díj) |
   | ----- | ----- |
   | Név | Adja meg a projekt nevét, például az **EdgeTreeClassifier** nevet. |
   | Leírás | Választható projektleírás. |
   | Erőforrás | Select one of your Azure resource groups that includes a Custom Vision Service resource or **create new** if you haven't yet added one. |
   | Project Types (Projekttípusok) | **Classification** (Osztályozás) |
   | Classification Types (Osztályozási típusok) | **Multiclass (single tag per image)** (Multiclass (egyetlen címke képenként)) |
   | Domains (Tartományok) | **General (compact)** (Általános (kompakt)) |
   | Export Capabilities | **Basic platforms (Tensorflow, CoreML, ONNX, ...)** |

5. Válassza a **Create project** (Projekt létrehozása) lehetőséget.

### <a name="upload-images-and-train-your-classifier"></a>Képek feltöltése és az osztályozó betanítása

A képosztályozó létrehozásához betanítási képek egy halmazára és tesztképekre van szükség. 

1. Klónozzon vagy töltsön le mintaképeket a [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) adattárból a helyi fejlesztői gépre. 

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Térjen vissza a Custom Vision-projekthez, és válassza az **Add images** (Képek hozzáadása) lehetőséget. 

3. Tallózással keresse meg az Ön által helyileg klónozott Git-adattárat, és lépjen az első, **Cognitive-CustomVision-Windows / Samples / Images / Hemlock** nevű képmappára. Válassza ki a mappában található 10 képet, majd válassza az **Open** lehetőséget. 

4. Adja hozzá a **hemlock** (hemlok) címkét ehhez a képcsoporthoz, és nyomja le az **enter** billentyűt a címke alkalmazásához. 

5. Válassza az **Upload 10 files** (10 fájl feltöltése) lehetőséget. 

   ![Upload hemlock tagged files to Custom Vision](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Amikor a képek feltöltése sikeresen befejeződött, válassza a **Done** (Kész) lehetőséget.

7. Ismét válassza az **Add images** (Képek hozzáadása) lehetőséget.

8. Tallózással keresse meg a második, **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry** nevű képmappát. Válassza ki a mappában található 10 képet, majd válassza az **Open** lehetőséget. 

9. Adja hozzá a **japanese cherry** (japán cseresznye) címkét ehhez a képcsoporthoz, és nyomja le az **enter** billentyűt a címke alkalmazásához. 

10. Válassza az **Upload 10 files** (10 fájl feltöltése) lehetőséget. Amikor a képek feltöltése sikeresen befejeződött, válassza a **Done** (Kész) lehetőséget. 

11. A két képhalmaz címkével történő ellátása és feltöltése után válassza a **Train** (Betanítás) lehetőséget az osztályozó betanításához. 

### <a name="export-your-classifier"></a>Az osztályozó exportálása

1. Az osztályozó betanítását követően válassza az **Export** (Exportálás) lehetőséget az osztályozó Performance (Teljesítmény) lapján. 

   ![Export your trained image classifier](./media/tutorial-deploy-custom-vision/export.png)

2. Platformként válassza a **DockerFile** lehetőséget. 

3. A verzió megadásakor válassza a **Linux** lehetőséget.  

4. Válassza az **Export** (Exportálás) lehetőséget. 

   ![DockerFile-ként történő exportálás Linux-tárolókkal](./media/tutorial-deploy-custom-vision/export-2.png)

5. Ha az exportálás befejeződött, válassza a **Download** (Letöltés) lehetőséget, és mentse a .zip csomagot helyileg a számítógépre. Bontsa ki a fájlokat a csomagból. Ezeket a fájlokat egy olyan IoT Edge-modul létrehozásához fogja használni, amely a képosztályozó kiszolgálóját tartalmazza. 

Ha elérte ezt a pontot, befejezte a Custom Vision-projekt létrehozását és betanítását. Az exportált fájlokat a következő szakaszban használhatja, a Custom Vision weblappal azonban már elkészült. 

## <a name="create-an-iot-edge-solution"></a>IoT Edge-megoldás létrehozása

Most már rendelkezik a képosztályozó tárolóverziójához szükséges fájlokkal a fejlesztői gépen. Ebben a szakaszban úgy konfigurálhatja a képosztályozó-tárolót, hogy IoT Edge-modulként fusson. Létrehozhat egy második modult is, amely a képosztályozóval együtt lesz üzembe helyezve. A második modul kéréseket tesz közzé az osztályozó számára, és elküldi az eredményeket üzenetekként az IoT Hubnak. 

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

Egy megoldás logikus módját jelenti több modul fejlesztésének és megszervezésének egyetlen IoT Edge üzemelő példány számára. Egy megoldás tartalmazza egy vagy több modul kódját, valamint az üzembehelyezési jegyzéket, amely deklarálja, hogyan kell a modulokat konfigurálni egy IoT Edge-eszközön. 

1. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)**  > **Command Palette (Parancskatalógus)** elemet. 

1. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 

   | Mező | Value (Díj) |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon leíró jellegű nevet a megoldásnak, például a **CustomVisionSolution** nevet, vagy fogadja el az alapértelmezett nevet. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget. |
   | Provide a module name (Modulnév megadása) | Adja a modulnak a **classifier** nevet.<br><br>Fontos, hogy ez a modulnév csak kisbetűket tartalmazzon. Az IoT Edge a modulokra történő hivatkozáskor megkülönbözteti a kis- és nagybetűket, és ez a megoldás egy olyan kódtárat használ, amely minden kérést kisbetűs formátumúvá konvertál. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon.<br><br>The final string looks like **\<registry name\>.azurecr.io/classifier**. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-deploy-custom-vision/repository.png)

A Visual Studio Code-ablak betölti az IoT Edge-megoldás munkaterületét.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64, which is what we'll use for this tutorial. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="add-your-image-classifier"></a>A képosztályozó hozzáadása

A Visual Studio Code Python-modulsablonja tartalmaz néhány mintakódot, amelyeket futtathat az IoT Edge teszteléséhez. Ebben a forgatókönyvben ezt a kódot nem fogja használni. Helyette a jelen szakaszban leírt lépésekkel cserélje le a mintakódot a korábban exportált képosztályozó-tárolóra. 

1. A fájlkezelőben tallózással keresse meg a letöltött és kibontott Custom Vision-csomagot. Másolja a tartalmakat a kibontott csomagból. Ez az **app** és az **azureml** mappákat, valamint a **Dockerfile** és a **README** fájlokat jelenti. 

2. A fájlkezelőben tallózással keresse meg azt a könyvtárat, ahol a Visual Studio Code létrehozta az IoT Edge-megoldást. 

3. Nyissa meg az osztályozó modulmappáját. Ha az előző szakaszban a javasolt értékeket használta, a mappastruktúrának a következőhöz hasonlóan kell kinéznie: **CustomVisionSolution / modules / classifier**. 

4. Illessze be a fájlokat a **classifier** mappába. 

5. Térjen vissza a Visual Studio Code-ablakhoz. A megoldás munkaterületének most a modulmappában található képosztályozó-fájlokat kell mutatnia. 

   ![Megoldás munkaterülete képosztályozó-fájlokkal](./media/tutorial-deploy-custom-vision/workspace.png)

6. Nyissa meg a **module.json** fájlt az osztályozó mappájában. 

7. Update the **platforms** parameter to point to the new Dockerfile that you added, and remove all the options besides AMD64, which is the only architecture we're using for this tutorial. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Mentse a módosításokat. 

### <a name="create-a-simulated-camera-module"></a>Szimulált kameramodul létrehozása

Egy valós Custom Vision-beli üzemelő példányban rendelkezik egy kamerával, amely élő képeket és videostreameket biztosít. Ebben a forgatókönyvben szimulálhatja a kamerát egy olyan modul létrehozásával, amely egy tesztképet küld a képosztályozónak. 

#### <a name="add-and-configure-a-new-module"></a>Új modul hozzáadása és konfigurálása

Ebben a szakaszban hozzáadhatja az új modult a meglévő CustomVisionSolution megoldáshoz, és létrehozhatja a kóddal a szimulált kamerát. 

1. Ugyanabban a Visual Studio Code-ablakban, a parancskatalógusban futtassa az **Azure IoT Edge: Add IoT Edge Module** (Azure IoT Edge: Hozzáadás az IoT Edge modulhoz) parancsot. A parancskatalógusban adja meg az alábbi információkat az új modul létrehozásához: 

   | Kérdés | Value (Díj) | 
   | ------ | ----- |
   | Select deployment template file (Üzembehelyezési sablonfájl kiválasztása) | Válassza a deployment.template.json fájlt a CustomVisionSolution mappában. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget |
   | Provide a module name (Modulnév megadása) | Adja a modulnak a **cameraCapture** nevet |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére.<br><br>A sztring végül a következőképpen néz ki: **\<tárolóregisztrációs adatbázis neve\>.azurecr.io/cameracapture**. |

   A VS Code-ablak betölti az új modult a megoldás munkaterületén, és frissíti a deployment.template.json fájlt. Most két modulmappát kell látnia: a classifier és a cameraCapture mappákat. 

2. Nyissa meg a **modules** / **cameraCapture** mappa **main.py** fájlját. 

3. Cserélje le a teljes fájlt az alábbi kódra. A mintakód POST kéréseket küld az osztályozómodulban futó rendszerkép-feldolgozó szolgáltatásnak. Ehhez a modultárolóhoz adjon hozzá egy mintaképet, amelyet a modultároló a kérésekben fog használni. A modultároló ezután becsomagolja a választ IoT Hub-üzenetként, és elküldi azt egy kimeneti üzenetsornak.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("Response from classification service: (" + str(response.status_code))

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. Mentse a **main.py** fájlt. 

5. Nyissa meg a **requirements.txt** fájlt. 

6. Adjon meg egy új sort, amelyet egy kódtár tartalmazni fog a tárolóban.

   ```Text
   requests
   ```

7. Mentse a **requirements.txt** fájlt.


#### <a name="add-a-test-image-to-the-container"></a>Tesztkép hozzáadása a tárolóhoz

Ebben a forgatókönyvben ahelyett, hogy valódi kamerát használnánk egy képcsatorna biztosításához, egyetlen tesztképet használunk. A tesztképet az a GitHub-adattár tartalmazza, amelyet a betanítási képekhez töltött le korábban ebben az oktatóanyagban. 

1. Lépjen a tesztképhez, amely a következő helyen található: **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**. 

2. Másolja a **test_image.jpg** fájlt 

3. Tallózással keresse meg az IoT Edge-megoldáskönyvtárat, és illessze be a tesztképet a **modules** / **cameraCapture** mappába. A képnek ugyanabban a mappában kell lennie, mint ahol az előző szakaszban szerkesztett main.py fájl található. 

3. A Visual Studio Code-ban nyissa meg a cameraCapture modul **Dockerfile.amd64** fájlját. 

4. A `WORKDIR /app` munkakönyvtárat létrehozó sor után adja hozzá az alábbi kódsort: 

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

5. Mentse a Dockerfile-t. 

### <a name="prepare-a-deployment-manifest"></a>Üzembehelyezési jegyzék előkészítése

Ebben az oktatóanyagban eddig betanított egy Custom Vision-modellt a fákat ábrázoló képek osztályozására, valamint becsomagolta a modellt IoT Edge-modulként. Ezután létrehozott egy második modult, amely le tudja kérdezni a képosztályozási kiszolgálót, és jelenteni tudja az eredményeket az IoT Hubnak. Ezzel készen áll az üzembehelyezési jegyzék létrehozására, amely jelzi egy IoT Edge-eszköznek, hogyan indítsa el és futtassa a két modult együtt. 

A Visual Studio Code-hoz készült IoT Edge-bővítmény egy sablont biztosít az egyes IoT Edge-megoldásokban, amelynek segítségével üzembehelyezési jegyzéket hozhat létre. 

1. Nyissa meg a **deployment.template.json** fájlt a megoldás mappájában. 

2. Find the **modules** section, which should contain three modules: the two that you created, classifier and cameraCapture, and a third that's included by default, SimulatedTemperatureSensor. 

3. Delete the **SimulatedTemperatureSensor** module with all of its parameters. Ez a modul azért szerepel, hogy mintaadatokat biztosítson a tesztelési forgatókönyvekben, azonban ebben az üzembe helyezésben nem lesz rá szükségünk. 

4. Ha a képosztályozó modulnak nem a **classifier** nevet adta, ellenőrizze a nevet, és győződjön meg arról, hogy csak kisbetűket tartalmaz. A cameraCapture modul meghívja az osztályozómodult egy kéréskódtár használatával, amely az összes kérést kisbetűs formátumúvá konvertálja, és az IoT Edge megkülönböztetni a kis- és nagybetűket. 

5. Frissítse a cameraCapture modul **createOptions** paraméterét az alábbi JSON használatával. Ez az információ olyan környezeti változókat hoz létre a modultárolóban, amelyek a main.py folyamatban vannak lekérve. Azáltal, hogy belefoglalja ezt az információt az üzembehelyezési jegyzékbe, módosíthatja a képet vagy a végpontot anélkül, hogy a modulképet újra létre kellene hoznia. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Ha a Custom Vision-modulnak nem a *classifier* nevet adta, frissítse úgy a képfeldolgozó végpont értékét, hogy megegyezzen a modul nevével. 

5. A fájl alján frissítse a $edgeHub modul **routes** paraméterét. Az előrejelzés eredményeit javasolt a cameraCapture modulból az IoT Hubba irányítani. 

    ```json
        "routes": {
          "CameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Ha a második modulnak nem a *cameraCapture* nevet adta, frissítse úgy az átirányítás értékét, hogy megegyezzen a modul nevével. 

7. Mentse a **deployment.template.json** fájlt.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Az IoT Edge-megoldás buildelése és üzembe helyezése

Most, hogy mindkét modult létrehozta, és az üzembehelyezési jegyzék sablonja konfigurálva van, készen áll a tárolórendszerkép létrehozására és azok leküldésére a tárolóregisztrációs adatbázisba. 

Amikor a rendszerképek a beállításjegyzékben vannak, üzembe helyezheti a megoldást az IoT Edge-eszközön. Az IoT Hub felületén keresztül modulokat állíthat be egy eszközön, de az IoT Hubhoz és az eszközökhöz a Visual Studio Code felületén keresztül is hozzáférhet. Ebben a szakaszban az IoT Hubhoz való hozzáférést fogja beállítani, majd a VS Code használatával üzembe fogja helyezni a megoldást az IoT Edge-eszközön.

Először hozza létre, és küldje le a megoldást a tárolóregisztrációs adatbázisba. 

1. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. A műveleti folyamatot végigkövetheti a VS Code integrált termináljában. 
2. Notice that a new folder was added to your solution, **config**. Expand this folder and open the **deployment.json** file inside.
3. Tekintse át a deployment.json fájlban szereplő információkat. A deployment.json fájl a konfigurált üzembehelyezési sablonfájl és a megoldásból származó információk, például az .env és a module.json fájl alapján automatikusan jön létre (vagy frissül). 

Next, select your device and deploy your solution.

1. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 
2. Kattintson a jobb gombbal az üzembe helyezés céleszközére, majd válassza a **Create deployment for a single device** (Üzemelő példány létrehozása egyetlen eszközhöz) lehetőséget. 
3. A fájlkezelőben keresse meg a megoldás **config** mappáját, és jelölje ki a **deployment.json** fájlt. Kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. 

Ha az üzembe helyezés sikeres volt, a VS Code kimenetében egy megerősítő üzenet jelenik meg. A VS Code Explorerben bontsa ki az üzembe helyezéshez használt IoT Edge-eszköz részleteit. Mozgassa az egérmutatót az **Azure IoT Hub Devices** (Azure-beli IoT Hub-eszközök) fejléc fölé a frissítés gomb engedélyezéséhez, ha a modulok nem jelennek meg azonnal. Eltarthat néhány pillanatig, amíg a modulok elindulnak, és visszajelzést küldenek az IoT Hubnak. 

Azt is ellenőrizheti, hogy működik-e az összes modul magán az eszközön. Futtassa az alábbi parancsot az IoT Edge-eszközön a modulok állapotának megtekintéséhez. Eltarthat néhány pillanatig, amíg a modulok elindulnak.

   ```bash
   iotedge list
   ```

## <a name="view-classification-results"></a>Osztályozási eredmények megtekintése

A modulok eredményeit kétféleképpen tekintheti meg: magán az eszközön az üzenetek létrehozásakor és küldésekor, vagy a Visual Studio Code-ban az üzenetek IoT Hubhoz történő megérkezésekor. 

Ha az eszközön tekinti meg az eredményeket, tekintse meg a cameraCapture modul naplóit, illetve azokon belül az elküldött üzeneteket, és a megerősítést arról, hogy az IoT Hub fogadta őket. 

   ```bash
   iotedge logs cameraCapture
   ```

From Visual Studio Code, right-click on the name of your IoT Edge device and select **Start Monitoring Built-in Event Endpoint**. 

A Custom Vision-modul eredményei, amelyek a cameraCapture modulból üzenetekként lettek elküldve, rendelkeznek azzal a valószínűséggel, hogy a kép egy hemlokfenyőt vagy egy cseresznyefát ábrázol. Mivel a kép egy hemlokfenyőt ábrázol, a valószínűségnek 1,0 értéket kell mutatnia. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban betanított egy Custom Vision-modellt, és modulként üzembe helyezte azt egy IoT Edge-eszközön. Ezután létrehozott egy modult, amely le tudja kérdezni a képosztályozási szolgáltatást, és visszajelzést tud küldeni az eredményekről az IoT Hubnak. 

Ha ki szeretné próbálni a jelen forgatókönyvnek egy élő kameracsatornával rendelkező, részletesebb verzióját, tekintse meg [Custom Vision és Azure IoT Edge a Raspberry Pi 3 eszközökön](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) című GitHub-projektet. 

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal](tutorial-store-data-sql-server.md)
