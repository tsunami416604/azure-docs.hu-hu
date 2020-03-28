---
title: Oktatóanyag – Egyéni vision-osztályozó üzembe helyezése egy eszközre az Azure IoT Edge használatával
description: Ebben az oktatóanyagban megtudhatja, hogyan futtathat egy számítógépes látási modellt tárolóként a Custom Vision és az IoT Edge használatával.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/15/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 07350ffe4a57bfe4a79bfce5d821b51535867935
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76167013"
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

![Diagram – Oktatóanyag-architektúra, színpad és üzembe helyezési osztályozó](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

>[!TIP]
>Ez az oktatóanyag a Custom Vision és az Azure IoT Edge egyszerűsített verziója [egy Raspberry Pi 3 mintaprojekten.](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) Ez az oktatóanyag úgy tervezték, hogy egy felhőalapú virtuális gépen fusson, és statikus képeket használ a lemezkép-osztályozó betanításához és teszteléséhez, ami hasznos lehet valaki számára, aki csak most kezdi kiértékelni az Egyéni látást az IoT Edge-en. A mintaprojekt fizikai hardvert használ, és éles kameraképet állít be a képosztályozó betanításához és teszteléséhez, ami hasznos annak, aki egy részletesebb, valós forgatókönyvet szeretne kipróbálni.

Az oktatóanyag megkezdése előtt át kellett volna mennie az előző oktatóanyagon, hogy beállítsa a környezetet a Linux-tárolók fejlesztéséhez: [IoT Edge modulok fejlesztése Linux-eszközökhöz](tutorial-develop-for-linux.md). Az oktatóanyag kitöltésével a következő előfeltételeknek kell megrendelkezniük:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge-et futtató Linux-eszköz](quickstart-linux.md)
* A tároló beállításjegyzék, mint [az Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/)
* Az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)konfigurált [Visual Studio-kód.](https://code.visualstudio.com/)
* [A Docker CE](https://docs.docker.com/install/) linuxos tárolók futtatására van konfigurálva.

IoT Edge-modul fejlesztéséhez a Custom Vision szolgáltatással telepítse a következő további előfeltételeket a fejlesztői gépre:

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Python-bővítmény a Visual Studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

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
   | Név | Adja meg a projekt nevét, például az **EdgeTreeClassifier** nevet. |
   | Leírás | Választható projektleírás. |
   | Erőforrás | Válassza ki az egyik Azure-erőforráscsoportok, amely tartalmazza a Custom Vision Service-erőforrás, vagy **hozzon létre újat,** ha még nem adott hozzá egyet. |
   | Project Types (Projekttípusok) | **Classification** (Osztályozás) |
   | Classification Types (Osztályozási típusok) | **Multiclass (single tag per image)** (Multiclass (egyetlen címke képenként)) |
   | Tartományok | **General (compact)** (Általános (kompakt)) |
   | Exportálási lehetőségek | **Alapvető platformok (Tensorflow, CoreML, ONNX, ...)** |

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

   ![Hemlock címkézett fájlok feltöltése a Custom Vision alkalmazásba](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Amikor a képek feltöltése sikeresen befejeződött, válassza a **Done** (Kész) lehetőséget.

7. Ismét válassza az **Add images** (Képek hozzáadása) lehetőséget.

8. Tallózással keresse meg a második, **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry** nevű képmappát. Válassza ki a mappában található 10 képet, majd válassza az **Open** lehetőséget.

9. Adja hozzá a **japanese cherry** (japán cseresznye) címkét ehhez a képcsoporthoz, és nyomja le az **enter** billentyűt a címke alkalmazásához.

10. Válassza az **Upload 10 files** (10 fájl feltöltése) lehetőséget. Amikor a képek feltöltése sikeresen befejeződött, válassza a **Done** (Kész) lehetőséget.

11. A két képhalmaz címkével történő ellátása és feltöltése után válassza a **Train** (Betanítás) lehetőséget az osztályozó betanításához.

### <a name="export-your-classifier"></a>Az osztályozó exportálása

1. Az osztályozó betanítását követően válassza az **Export** (Exportálás) lehetőséget az osztályozó Performance (Teljesítmény) lapján.

   ![A betanított képosztályozó exportálása](./media/tutorial-deploy-custom-vision/export.png)

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

1. A VS Code parancspaletta megnyitásához válassza a **Nézet** > **parancspaletta** lehetőséget. 

1. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon leíró jellegű nevet a megoldásnak, például a **CustomVisionSolution** nevet, vagy fogadja el az alapértelmezett nevet. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget. |
   | Provide a module name (Modulnév megadása) | Adja a modulnak a **classifier** nevet.<br><br>Fontos, hogy ez a modulnév csak kisbetűket tartalmazzon. Az IoT Edge a modulokra történő hivatkozáskor megkülönbözteti a kis- és nagybetűket, és ez a megoldás egy olyan kódtárat használ, amely minden kérést kisbetűs formátumúvá konvertál. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon.<br><br>Az utolsó karakterlánc a ** \<rendszerleíró adatbázis\>"azurecr.io/classifier.** |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-deploy-custom-vision/repository.png)

A Visual Studio Code-ablak betölti az IoT Edge-megoldás munkaterületét.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a célarchitektúrát

Jelenleg a Visual Studio Code modulokat fejleszthet Linux AMD64 és Linux ARM32v7 eszközökhöz. Ki kell választania, hogy az egyes megoldásokkal melyik architektúrát célozza meg, mert a tároló az egyes architektúratípusokhoz másképp en fut. Az alapértelmezett a Linux AMD64, amit ebben az oktatóanyagban fogunk használni. 

1. Nyissa meg a parancspalettát, és keressen az **Azure IoT Edge: Set Default Target Platform for Edge Solution (Alapértelmezett célplatform az edge-megoldáshoz) kifejezésre,** vagy válassza ki az ablak alján lévő oldalsávon található parancsikont. 

2. A parancspalettán válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu virtuális gépet használunk IoT Edge eszközként, így megtartja az alapértelmezett **amd64-et.** 

### <a name="add-your-image-classifier"></a>A képosztályozó hozzáadása

A Visual Studio Code Python-modulsablonja tartalmaz néhány mintakódot, amelyeket futtathat az IoT Edge teszteléséhez. Ebben a forgatókönyvben ezt a kódot nem fogja használni. Helyette a jelen szakaszban leírt lépésekkel cserélje le a mintakódot a korábban exportált képosztályozó-tárolóra. 

1. A fájlkezelőben tallózással keresse meg a letöltött és kibontott Custom Vision-csomagot. Másolja a tartalmakat a kibontott csomagból. Ez az **app** és az **azureml** mappákat, valamint a **Dockerfile** és a **README** fájlokat jelenti. 

2. A fájlkezelőben tallózással keresse meg azt a könyvtárat, ahol a Visual Studio Code létrehozta az IoT Edge-megoldást. 

3. Nyissa meg az osztályozó modulmappáját. Ha az előző szakaszban a javasolt értékeket használta, a mappastruktúrának a következőhöz hasonlóan kell kinéznie: **CustomVisionSolution / modules / classifier**. 

4. Illessze be a fájlokat a **classifier** mappába. 

5. Térjen vissza a Visual Studio Code-ablakhoz. A megoldás munkaterületének most a modulmappában található képosztályozó-fájlokat kell mutatnia. 

   ![Megoldás munkaterülete képosztályozó-fájlokkal](./media/tutorial-deploy-custom-vision/workspace.png)

6. Nyissa meg a **module.json** fájlt az osztályozó mappájában. 

7. Frissítse a **platformok** paramétert, hogy mutasson az új Dockerfile, amely hozzáadott, és távolítsa el az összes lehetőséget mellett AMD64, amely az egyetlen architektúra vagyunk használ vatünk az oktatóanyag. 

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

   | Kérdés | Érték | 
   | ------ | ----- |
   | Select deployment template file (Üzembehelyezési sablonfájl kiválasztása) | Válassza a deployment.template.json fájlt a CustomVisionSolution mappában. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget |
   | Provide a module name (Modulnév megadása) | Adja a modulnak a **cameraCapture** nevet |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére.<br><br>Az utolsó karakterlánc a ** \<rendszerleíró adatbázis .azurecr.io/cameracapture .\>** |

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
                print("No response from classification service")
                return None

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
                if classification:
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

1. Keresse meg a tesztképet, amely a Cognitive-CustomVision-Windows Samples Images Test **(Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test ) helyen**található. 

2. Másolja a **test_image.jpg** fájlt 

3. Tallózással keresse meg az IoT Edge-megoldás könyvtárát, és illessze be a tesztképet a **modulok** / **cameraCapture** mappájába. A képnek ugyanabban a mappában kell lennie, mint ahol az előző szakaszban szerkesztett main.py fájl található. 

4. A Visual Studio Code-ban nyissa meg a cameraCapture modul **Dockerfile.amd64** fájlját.

5. A `WORKDIR /app` munkakönyvtárat létrehozó sor után adja hozzá az alábbi kódsort:

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. Mentse a Dockerfile-t.

### <a name="prepare-a-deployment-manifest"></a>Üzembehelyezési jegyzék előkészítése

Ebben az oktatóanyagban eddig betanított egy Custom Vision-modellt a fákat ábrázoló képek osztályozására, valamint becsomagolta a modellt IoT Edge-modulként. Ezután létrehozott egy második modult, amely le tudja kérdezni a képosztályozási kiszolgálót, és jelenteni tudja az eredményeket az IoT Hubnak. Ezzel készen áll az üzembehelyezési jegyzék létrehozására, amely jelzi egy IoT Edge-eszköznek, hogyan indítsa el és futtassa a két modult együtt. 

A Visual Studio Code-hoz készült IoT Edge-bővítmény egy sablont biztosít az egyes IoT Edge-megoldásokban, amelynek segítségével üzembehelyezési jegyzéket hozhat létre. 

1. Nyissa meg a **deployment.template.json** fájlt a megoldás mappájában. 

2. Keresse meg a **modulok szakaszt,** amelynek három modult kell tartalmaznia: a két létrehozott, osztályozó és cameraCapture, és egy harmadik, amely alapértelmezés szerint szerepel, SimulatedTemperatureSensor. 

3. Törölje a **SimulatedTemperatureSensor modult** az összes paraméterével. Ez a modul azért szerepel, hogy mintaadatokat biztosítson a tesztelési forgatókönyvekben, azonban ebben az üzembe helyezésben nem lesz rá szükségünk. 

4. Ha a képosztályozó modulnak nem a **classifier** nevet adta, ellenőrizze a nevet, és győződjön meg arról, hogy csak kisbetűket tartalmaz. A cameraCapture modul meghívja az osztályozómodult egy kéréskódtár használatával, amely az összes kérést kisbetűs formátumúvá konvertálja, és az IoT Edge megkülönböztetni a kis- és nagybetűket. 

5. Frissítse a cameraCapture modul **createOptions** paraméterét az alábbi JSON használatával. Ez az információ olyan környezeti változókat hoz létre a modultárolóban, amelyek a main.py folyamatban vannak lekérve. Azáltal, hogy belefoglalja ezt az információt az üzembehelyezési jegyzékbe, módosíthatja a képet vagy a végpontot anélkül, hogy a modulképet újra létre kellene hoznia. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Ha a Custom Vision-modulnak nem a *classifier* nevet adta, frissítse úgy a képfeldolgozó végpont értékét, hogy megegyezzen a modul nevével. 

6. A fájl alján frissítse a $edgeHub modul **routes** paraméterét. Az előrejelzés eredményeit javasolt a cameraCapture modulból az IoT Hubba irányítani.

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

1. A VS Code explorer ben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza **az IoT Edge-megoldás összeállítása és leküldése parancsot.** A műveleti folyamatot végigkövetheti a VS Code integrált termináljában. 
2. Figyelje meg, hogy egy új mappát adtak hozzá a megoldás, **config**. Bontsa ki ezt a mappát, és nyissa meg a **központi.json** fájlt.
3. Tekintse át a deployment.json fájlban szereplő információkat. A deployment.json fájl a konfigurált üzembehelyezési sablonfájl és a megoldásból származó információk, például az .env és a module.json fájl alapján automatikusan jön létre (vagy frissül). 

Ezután válassza ki az eszközt, és telepítse a megoldást.

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

A Visual Studio-kódból kattintson a jobb gombbal az IoT Edge-eszköz nevére, és válassza **a Beépített eseményvégpont indítása parancsot.** 

A Custom Vision-modul eredményei, amelyek a cameraCapture modulból üzenetekként lettek elküldve, rendelkeznek azzal a valószínűséggel, hogy a kép egy hemlokfenyőt vagy egy cseresznyefát ábrázol. Mivel a kép egy hemlokfenyőt ábrázol, a valószínűségnek 1,0 értéket kell mutatnia. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások, amelyek ebben a cikkben a költségek elkerülése érdekében. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban betanított egy Custom Vision-modellt, és modulként üzembe helyezte azt egy IoT Edge-eszközön. Ezután létrehozott egy modult, amely le tudja kérdezni a képosztályozási szolgáltatást, és visszajelzést tud küldeni az eredményekről az IoT Hubnak. 

Ha ki szeretné próbálni a jelen forgatókönyvnek egy élő kameracsatornával rendelkező, részletesebb verzióját, tekintse meg [Custom Vision és Azure IoT Edge a Raspberry Pi 3 eszközökön](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) című GitHub-projektet. 

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal](tutorial-store-data-sql-server.md)
