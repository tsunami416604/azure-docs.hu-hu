---
title: Oktatóanyag az Azure IoT Edge - eszköz üzembe helyezése Custom Vision osztályozó |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan, hogy az egy számítógépes látástechnológiai modellel futtatási Custom Vision és az IoT Edge használatával tárolójaként.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: c36e557f4e7c4a42726ee96de8bd73755d5e19c4
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193670"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Oktatóanyag: Hajtsa végre a képek besorolása a Custom Vision Service a peremhálózaton

Az Azure IoT Edge hatékonyabbá teheti IoT-megoldását a felhőben található számítási feladatok peremhálózatra történő áthelyezése által. Ez a képesség hasznos a nagy mennyiségű adatot feldolgozó szolgáltatások, például számítógépes látástechnológiai modellek esetében. A [Custom Vision Service](../cognitive-services/custom-vision-service/home.md) szolgáltatással egyéni képosztályozókat hozhat létre és helyezhet üzembe tárolóként egy eszközön. Ezzel a két szolgáltatással megállapításokat tehet képekről vagy videostreamekről anélkül, hogy az adatokat először át kellene helyeznie az oldalról. A Custom Vision egy olyan osztályozót biztosít, amely a megállapítások létrehozásához összehasonlítja a képet egy betanított modellel. 

Egy IoT Edge-eszközön található Custom Vision például meg tudja határozni, hogy egy autópályán az átlagosnál nagyobb vagy kisebb-e a forgalom, vagy hogy egy parkolóház egy bizonyos sorában elérhetők-e parkolóhelyek. Ezeket a megállapításokat műveletvégzés céljából meg lehet osztani egy másik eszközzel. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
>
> * Képosztályozó létrehozása a Custom Vision használatával.
> * IoT Edge-modul létrehozása, amely lekérdezi a Custom Vision eszközön található webkiszolgálót.
> * A képosztályozó eredményeinek küldése az IoT Hubnak.

<center>
![Diagram – oktatóanyag architektúra tesztelése és üzembe helyezése az osztályozó által igénybe vett](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [linuxos rövid útmutató](quickstart-linux.md) lépéseit követve.
* A Custom Vision modul az x64-alapú architektúrákhoz jelenleg csak Linux-tárolóként érhető el. 

Felhőerőforrások:

* Egy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 
* Egy tárolóregisztrációs adatbázis. Ez az oktatóanyag az [Azure Container Registryt](https://docs.microsoft.com/azure/container-registry/) használja. 
* Kérje le a tárolóregisztrációs adatbázis [rendszergazdai fiókjának](../container-registry/container-registry-authentication.md#admin-account) hitelesítő adatait.

Fejlesztési erőforrások:

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)-bővítmény a Visual Studio Code-hoz
* [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)-bővítmény a Visual Studio Code-hoz
* [Docker CE](https://docs.docker.com/install/).

## <a name="build-an-image-classifier-with-custom-vision"></a>Képosztályozó létrehozása a Custom Vision használatával

Képosztályozó létrehozásakor létre kell hoznia egy Custom Vision-projektet, és meg kell adnia betanítási képeket. A szakaszban elvégezhető lépésekkel kapcsolatos további információkért tekintse meg az [Osztályozó létrehozása a Custom Vision használatával](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md) című cikket.

A képosztályozó létrehozása és betanítása után exportálhatja azt Docker-tárolóként, és üzembe helyezheti egy IoT Edge-eszközön. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

1. A webböngészőben lépjen a [Custom Vision weblapjára](https://customvision.ai/).

2. Válassza a **Sign in** (Bejelentkezés) gombot, és jelentkezzen be az Azure-erőforrásokhoz használt fiókkal. 

3. Válassza a **New project** (Új projekt) lehetőséget.

4. Hozza létre a projektet az alábbi értékekkel:

   | Mező | Érték |
   | ----- | ----- |
   | Name (Név) | Adja meg a projekt nevét, például az **EdgeTreeClassifier** nevet. |
   | Leírás | Választható projektleírás. |
   | Erőforráscsoport | Fogadja el az alapértelmezett **Limited trial** (Korlátozott próbaverzió) lehetőséget. |
   | Project Types (Projekttípusok) | **Classification** (Osztályozás) |
   | Classification Types (Osztályozási típusok) | **Multiclass (single tag per image)** (Multiclass (egyetlen címke képenként)) | 
   | Tartományok | **General (compact)** (Általános (kompakt)) |

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

   ![Egyéni vizuális hemlock címkével ellátott fájlok feltöltése](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Amikor a képek feltöltése sikeresen befejeződött, válassza a **Done** (Kész) lehetőséget.

7. Ismét válassza az **Add images** (Képek hozzáadása) lehetőséget.

8. Tallózással keresse meg a második, **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry** nevű képmappát. Válassza ki a mappában található 10 képet, majd válassza az **Open** lehetőséget. 

9. Adja hozzá a **japanese cherry** (japán cseresznye) címkét ehhez a képcsoporthoz, és nyomja le az **enter** billentyűt a címke alkalmazásához. 

10. Válassza az **Upload 10 files** (10 fájl feltöltése) lehetőséget. Amikor a képek feltöltése sikeresen befejeződött, válassza a **Done** (Kész) lehetőséget. 

11. A két képhalmaz címkével történő ellátása és feltöltése után válassza a **Train** (Betanítás) lehetőséget az osztályozó betanításához. 

### <a name="export-your-classifier"></a>Az osztályozó exportálása

1. Az osztályozó betanítását követően válassza az **Export** (Exportálás) lehetőséget az osztályozó Performance (Teljesítmény) lapján. 

   ![A betanított kép osztályozó exportálása](./media/tutorial-deploy-custom-vision/export.png)

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

1. A Visual Studio Code-ban válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet a VS Code integrált terminál megnyitásához.

2. Az integrált terminálban írja be a következő parancsot a **cookiecutter** telepítéséhez (vagy frissítéséhez), amellyel létrehozhatja az IoT Edge-hez elérhető Python-modulsablont a VS Code-ban:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Győződjön meg, hogy a cookiecutter telepítési könyvtára a környezet `Path` útvonalán legyen, hogy egy parancssorból meg lehessen hívni.

3. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet. 

4. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon leíró jellegű nevet a megoldásnak, például a **CustomVisionSolution** nevet, vagy fogadja el az alapértelmezett nevet. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget. |
   | Provide a module name (Modulnév megadása) | Adja a modulnak a **classifier** nevet.<br><br>Fontos, hogy ez a modulnév csak kisbetűket tartalmazzon. Az IoT Edge a modulokra történő hivatkozáskor megkülönbözteti a kis- és nagybetűket, és ez a megoldás egy olyan kódtárat használ, amely minden kérést kisbetűs formátumúvá konvertál. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. A sztring végül a következőképpen néz ki: \<tárolóregisztrációs adatbázis neve\>.azurecr.io/classifier. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-deploy-custom-vision/repository.png)

A Visual Studio Code-ablak betölti az IoT Edge-megoldás munkaterületét.

### <a name="add-your-image-classifier"></a>A képosztályozó hozzáadása

A Visual Studio Code Python-modulsablonja tartalmaz néhány mintakódot, amelyeket futtathat az IoT Edge teszteléséhez. Ebben a forgatókönyvben ezt a kódot nem fogja használni. Helyette a jelen szakaszban leírt lépésekkel cserélje le a mintakódot a korábban exportált képosztályozó-tárolóra. 

1. A fájlkezelőben tallózással keresse meg a letöltött és kibontott Custom Vision-csomagot. Másolja a tartalmakat a kibontott csomagból. Ez az **app** és az **azureml** mappákat, valamint a **Dockerfile** és a **README** fájlokat jelenti. 

2. A fájlkezelőben tallózással keresse meg azt a könyvtárat, ahol a Visual Studio Code létrehozta az IoT Edge-megoldást. 

3. Nyissa meg az osztályozó modulmappáját. Ha az előző szakaszban a javasolt értékeket használta, a mappastruktúrának a következőhöz hasonlóan kell kinéznie: **CustomVisionSolution / modules / classifier**. 

4. Illessze be a fájlokat a **classifier** mappába. 

5. Térjen vissza a Visual Studio Code-ablakhoz. A megoldás munkaterületének most a modulmappában található képosztályozó-fájlokat kell mutatnia. 

   ![Megoldás munkaterülete képosztályozó-fájlokkal](./media/tutorial-deploy-custom-vision/workspace.png)

6. Nyissa meg a **module.json** fájlt az osztályozó mappájában. 

7. Frissítse a **platforms** paramétert úgy, hogy az az új, hozzáadott Dockerfile-ra mutasson, majd távolítsa el az ARM32 architektúrát és az AMD64.debug lehetőségeket, amelyek a Custom Vision-modul esetében jelenleg nem támogatottak. 

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

1. Ugyanebben a Visual Studio Code ablakban, használja a parancskatalógus futtatásához **Azure IoT Edge: IoT Edge-modul hozzáadása**. A parancskatalógusban adja meg az alábbi információkat az új modul létrehozásához: 

   | Kérdés | Érték | 
   | ------ | ----- |
   | Select deployment template file (Üzembehelyezési sablonfájl kiválasztása) | Válassza a deployment.template.json fájlt a CustomVisionSolution mappában. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget |
   | Provide a module name (Modulnév megadása) | Adja a modulnak a **cameraCapture** nevet |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A sztring végül a következőképpen néz ki: **\<tárolóregisztrációs adatbázis neve\>.azurecr.io/cameracapture**. |

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

    import iothub_client
    # pylint: disable=E0611
    from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError
    # pylint: disable=E0401

    # messageTimeout - the maximum time in milliseconds until a message times out.
    # The timeout period starts at IoTHubModuleClient.send_event_async.
    MESSAGE_TIMEOUT = 10000

    # Choose HTTP, AMQP or MQTT as transport protocol.  
    PROTOCOL = IoTHubTransportProvider.MQTT

    # global counters
    SEND_CALLBACKS = 0

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = IoTHubMessage(bytearray(strMessage, 'utf8'))
        hubManager.send_event_to_output("output1", message, 0)

    # Callback received when the message that we send to IoT Hub is processed.
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        SEND_CALLBACKS += 1
        print ( "Confirmation received for message with result = %s" % result )
        print ( "   Total calls confirmed: %d \n" % SEND_CALLBACKS )

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

    class HubManager(object):
        def __init__(self, protocol, message_timeout):
            self.client_protocol = protocol
            self.client = IoTHubModuleClient()
            self.client.create_from_environment(protocol)
            # set the time until a message times out
            self.client.set_option("messageTimeout", message_timeout)
            
        # Sends a message to an output queue, to be routed by IoT Edge hub. 
        def send_event_to_output(self, outputQueueName, event, send_context):
            self.client.send_event_async(
                outputQueueName, event, send_confirmation_callback, send_context)

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global hubManager 
                hubManager = HubManager(PROTOCOL, MESSAGE_TIMEOUT)
            except IoTHubError as iothub_error:
                print ( "Unexpected error %s from IoTHub" % iothub_error )
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

3. A Visual Studio Code-ban nyissa meg a cameraCapture modul **Dockerfile.amd64** fájlját. (A Custom Vision modul jelenleg nem támogatja az ARM32-t.) 

4. A `WORKDIR /app` munkakönyvtárat létrehozó sor után adja hozzá az alábbi kódsort: 

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

5. Mentse a Dockerfile-t. 

### <a name="prepare-a-deployment-manifest"></a>Üzembehelyezési jegyzék előkészítése

Ebben az oktatóanyagban eddig betanított egy Custom Vision-modellt a fákat ábrázoló képek osztályozására, valamint becsomagolta a modellt IoT Edge-modulként. Ezután létrehozott egy második modult, amely le tudja kérdezni a képosztályozási kiszolgálót, és jelenteni tudja az eredményeket az IoT Hubnak. Ezzel készen áll az üzembehelyezési jegyzék létrehozására, amely jelzi egy IoT Edge-eszköznek, hogyan indítsa el és futtassa a két modult együtt. 

A Visual Studio Code-hoz készült IoT Edge-bővítmény egy sablont biztosít az egyes IoT Edge-megoldásokban, amelynek segítségével üzembehelyezési jegyzéket hozhat létre. 

1. Nyissa meg a **deployment.template.json** fájlt a megoldás mappájában. 

2. Keresse meg a **modules** szakaszt, amelynek három modult kell tartalmaznia: a két, Ön által létrehozott classifier és cameraCapture modult, valamint egy harmadik, alapértelmezés szerint hozzáadott modult, a tempSensort. 

3. Törölje a **tempSensor** modult minden paraméterével együtt. Ez a modul azért szerepel, hogy mintaadatokat biztosítson a tesztelési forgatókönyvekben, azonban ebben az üzembe helyezésben nem lesz rá szükségünk. 

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

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

Az oktatóanyag előfeltételei felsoroltak egy tárolóregisztrációs adatbázist, amely szükséges a létrehozott modulok tárolórendszerképeinek tárolásához. A beállításjegyzékez tartozó hozzáférési hitelesítő adatokat két helyen kell megadnia: a Visual Studio Code-ban, hogy létrehozhassa és leküldhesse a képeket a beállításjegyzékbe, valamint az üzembehelyezési jegyzékben, hogy az IoT Edge-eszköze lekérhesse és üzembe helyezhesse a képeket. 

Ha az Azure Container Registryt használja, győződjön meg arról, hogy tudja a [rendszergazdai fiókhoz](../container-registry/container-registry-authentication.md#admin-account) tartozó felhasználónevet, bejelentkezési kiszolgálót és jelszót. 

1. A Visual Studio Code-ban válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet az integrált terminál megnyitásához. 

2. Az integrált terminálon írja be a következő parancsot: 

    ```csh/sh
    docker login -u <registry username> <registry login server>
    ```

3. Amikor a rendszer kéri, adja meg a beállításjegyzékhez tartozó jelszót, és nyomja le az **Enter** billentyűt.

4. Nyissa meg az **.env** fájlt a megoldás mappájában. Ezt a fájlt a Git figyelmen kívül hagyja, illetve a fájl tárolja a beállításjegyzékhez tartozó hitelesítő adatokat, hogy ne kelljen azokat szoftveresen kötnie az üzembehelyezési sablonfájlban. 

5. Adja meg a tárolóregisztrációs adatbázishoz tartozó felhasználónevet és jelszót úgy, hogy az értékek ne legyenek idézőjelek között. 

6. Mentse az **.env** fájlt.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Az IoT Edge-megoldás buildelése és üzembe helyezése

Most, hogy mindkét modult létrehozta, és az üzembehelyezési jegyzék sablonja konfigurálva van, készen áll a tárolórendszerkép létrehozására és azok leküldésére a tárolóregisztrációs adatbázisba. 

Amikor a rendszerképek a beállításjegyzékben vannak, üzembe helyezheti a megoldást az IoT Edge-eszközön. Az IoT Hub felületén keresztül modulokat állíthat be egy eszközön, de az IoT Hubhoz és az eszközökhöz a Visual Studio Code felületén keresztül is hozzáférhet. Ebben a szakaszban az IoT Hubhoz való hozzáférést fogja beállítani, majd a VS Code használatával üzembe fogja helyezni a megoldást az IoT Edge-eszközön.

Először hozza létre, és küldje le a megoldást a tárolóregisztrációs adatbázisba. 

1. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. A műveleti folyamatot végigkövetheti a VS Code integrált termináljában. 
2. Figyelje meg, hogy hozzá lett adva egy új, **config** nevű mappa a megoldáshoz. Bontsa ki a mappát, és nyissa meg a benne található **deployment.json** fájlt.
3. Tekintse át a deployment.json fájlban szereplő információkat. A deployment.json fájl a konfigurált üzembehelyezési sablonfájl és a megoldásból származó információk, például az .env és a module.json fájl alapján automatikusan jön létre (vagy frissül). 

Ezután állítsa be a hozzáférést az IoT Hubhoz a Visual Studio Code-ban. 

1. Válassza ki a VS Code parancskatalógus **Azure IoT hubbal: Válassza ki az IoT Hub**.
2. Az utasításokat követve jelentkezzen be Azure-fiókjába. 
3. A parancskatalógusban válassza ki saját Azure-előfizetését, majd IoT Hubját. 

Végül jelölje ki az eszközt, és helyezze üzembe a megoldást.

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

Ha a Visual Studio Code-ban tekinti meg az eredményeket, kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Start monitoring D2C message** (D2C-üzenetek monitorozásának megkezdése) parancsot. 

A Custom Vision-modul eredményei, amelyek a cameraCapture modulból üzenetekként lettek elküldve, rendelkeznek azzal a valószínűséggel, hogy a kép egy hemlokfenyőt vagy egy cseresznyefát ábrázol. Mivel a kép egy hemlokfenyőt ábrázol, a valószínűségnek 1,0 értéket kell mutatnia. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban betanított egy Custom Vision-modellt, és modulként üzembe helyezte azt egy IoT Edge-eszközön. Ezután létrehozott egy modult, amely le tudja kérdezni a képosztályozási szolgáltatást, és visszajelzést tud küldeni az eredményekről az IoT Hubnak. 

Ha ki szeretné próbálni a jelen forgatókönyvnek egy élő kameracsatornával rendelkező, részletesebb verzióját, tekintse meg [Custom Vision és Azure IoT Edge a Raspberry Pi 3 eszközökön](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) című GitHub-projektet. 

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Átlagértékek keresése lebegő Azure Stream Analytics-ablakban](tutorial-deploy-stream-analytics.md)
