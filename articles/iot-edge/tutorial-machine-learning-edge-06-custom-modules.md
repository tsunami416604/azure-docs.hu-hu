---
title: Hozhat létre és telepíthet egyéni modulok – Machine Learning az Azure IoT Edge |} A Microsoft Docs
description: Létrehozhat és telepíthet, amelyek levéleszközök egy gépi tanulási modellt az adatok feldolgozására, és ezután elküldheti az elemzéseket az IoT hub IoT Edge-modulok.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 571a72d396c003bab363682559464715b180458a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081391"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Oktatóanyag: Hozhat létre és telepíthet egyéni IoT Edge-modulok

> [!NOTE]
> Ez a cikk egy sorozat része az IoT Edge-ben az Azure Machine Learning használatával kapcsolatos oktatóanyagot. Ha érkezett, ez a cikk közvetlenül, javasoljuk, hogy először a [először cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredmények.

Ebben a cikkben hozunk létre, üzeneteket fogadjon levéleszközök, a gépi tanulási modellt az adatok futtatása és insights ezután továbbítja az IoT Hub három IoT Edge-modulok.

IoT Edge hubot modult a modul kommunikációt létesít. Az IoT Edge használatával üzenetközvetítőként hub tartja a modulok egymástól független. Modulok csak adja meg, amelyen a üzenetek és a kimeneteket, amelyhez a üzeneteket írhat elfogadják a bemeneteket kell.

Szeretnénk az IoT Edge-eszköz szolgáltatást, mert négy feladat elvégzéséhez:

* A levéleszközök adatok fogadása
* Az eszközt, hogy küldték az adatokat az RUL előrejelzése
* Az eszköz csak az RUL-üzenet küldése az IoT Hub (Ez a függvény sikerült módosítani az adatok csak küldése, ha az RUL bizonyos szint alá csökken)
* A levél eszköz adatok mentése egy helyi fájlba az IoT Edge-eszközön. Az adatok rendszeres időközönként feltölti az IoT hub finomíthatja a machine learning-modellhez képzése fájlfeltöltés-n keresztül. Költséghatékonyabb ahelyett, hogy a fájl feltöltése állandó üzenet-adatfolyam használata.

Ezek a feladatok elvégzéséhez, három egyéni modult használjuk:

* **RUL osztályozó:** A létrehozott turboFanRulClassifier modul [Train és üzembe helyezése az Azure Machine Learning-modell](tutorial-machine-learning-edge-04-train-model.md) van egy standard szintű machine learning modult, amely egy "amlInput" és a egy kimeneti "amlOutput" nevű bemeneti tesz elérhetővé. A "amlInput" vár a következő módon pontosan a bemenet, amely az ACI-alapú webszolgáltatás küldtünk a bemeneti. Hasonlóképpen a "amlOutput" ugyanazokat az adatokat a web service adja vissza.

* **Az Avro-író:** Ez a modul a "avroModuleInput" bemeneti az üzeneteket fogadja, és továbbra is fennáll az üzenetet az IoT Hub való későbbi feltöltéshez lemezre Avro formátumban.

* **Útválasztó modul:** Az útválasztó modul üzeneteket fogad az alárendelt levéleszközök, majd formázza és elküldi az üzeneteket az osztályozó által igénybe vett. Ezután a modul fogadja az üzeneteket ebből az osztályozó által igénybe vett, és továbbítja az Avro-író modul be az üzenetet. Végül a modul csak az RUL előrejelzési küld az IoT Hub.

  * Bemenetek:
    * **deviceInput**: üzeneteket fogad levéleszközök
    * **rulInput:** "amlOutput" érkező üzenetek fogadása

  * Kimenetek:
    * **besorolása:** "amlInput" üzeneteket küld
    * **writeAvro:** üzeneteket "avroModuleInput"
    * **toIotHub:** üzeneteket küld az $felső, amely továbbítja az üzeneteket a csatlakoztatott IoT Hub

Az alábbi ábra bemutatja a modulokat, bemenetek, kimenetek és a teljes megoldás az IoT Edge hubot útvonalakat:

![IoT Edge három modulok architektúra ábrája](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Ez a cikk lépéseit egy felhőszolgáltatás-fejlesztői általában végzi.

## <a name="create-a-new-iot-edge-solution"></a>Hozzon létre egy új IoT Edge-megoldás

A két Azure-jegyzetfüzetet a második végrehajtásakor hogy létrehozott és közzétett egy tárolórendszerképet, amely tartalmazza az RUL-modellről. Az Azure Machine Learning, a lemezkép létrehozását, az egyes elemek, hogy a rendszerkép üzembe helyezhető, mint az Azure IoT Edge-modul beépített része. Ebben a lépésben hozunk létre az Azure IoT Edge-megoldásokat az "Azure Machine Learning" modul használatával fog, és a modul az Azure notebookok használatával közzétett azt lemezképre mutathat.

1. Nyisson meg egy távoli asztali munkamenetet a fejlesztői gépére.

2. Mappa megnyitása **C:\\forrás\\IoTEdgeAndMlSample** Visual Studio Code-ban.

3. A jobb gombbal a az explorer panelen (az üres helyet), és válassza ki **új IoT Edge-megoldás**.

    ![Új IoT Edge-megoldás létrehozása](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Fogadja el az alapértelmezett megoldás **EdgeSolution**.

5. Válasszon **Azure Machine Learning** modul sablonként.

6. A modul neve **turbofanRulClassifier**.

7. Válassza ki a machine learning-munkaterület.

8. Válassza ki a lemezképet hozott létre az Azure-jegyzetfüzet futtatása során.

9. Tekintse meg a megoldást, és figyelje meg, hogy a létrehozott fájlokat:

   * **deployment.template.json:** Ez a fájl tartalmazza a modulok a megoldás meghatározása. Nincsenek nagy figyelmet fordítani az ebben a fájlban három szakaszra osztható:

     * **A tárolójegyzék hitelesítő adatainak:** Határozza meg, egyéni tároló-beállításjegyzékek használata a megoldását. Jobb, tartalmaznia kell a beállításjegyzéket a machine learning-munkaterület, azaz az Azure Machine Learning lemezkép tárolására. Tároló-beállításjegyzékek tetszőleges számú rendelkezhet, de az egyszerűség érdekében használjuk ezt egy beállításjegyzék minden modul

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io”
         }
       }
       ```

     * **Modulok:** Ez a szakasz azokat a felhasználó által definiált modulokat, amelyek ezzel a megoldással. Megfigyelheti, hogy ez a szakasz jelenleg tartalmaz két modult: tempSensor és turbofanRulClassifier. A tempSensor telepítette a Visual Studio Code-sablont, de nincs rá szükségünk ebben a megoldásban. A modulok szakaszban törölheti a tempSensor modul definíciója. Vegye figyelembe, hogy a turbofanRulClassifier modul definíciója a rendszerképet a tárolóregisztrációs adatbázis mutat. További modulok hozzáadása a megoldáshoz, akkor jelennek meg ebben a szakaszban.

       ```json
       "modules": {
         "tempSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Útvonalak:** eszközét használjuk az útvonalakat meglehetősen egy kicsit ebben az oktatóanyagban. Útvonalak megadása, hogyan modulok kommunikálnak egymással. A két, a sablon által definiált útvonalak nem egyeznek az útválasztási van szükségünk. Az első útvonal az osztályozó kimenetet az összes adatot küld az IoT Hub (felső$). A többi útvonal tempSensor, amely az éppen törölt szól. Törölje a két alapértelmezett útvonalakat.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **Deployment.Debug.template.JSON:** ezt a fájlt az deployment.template.json hibakeresési verziója. Mi kell a deployment.template.json a módosításokat tükrözik a ebbe a fájlba.

   * **.env:** Ez a fájl, ahol kell adniuk a felhasználónevet és jelszót a beállításjegyzék eléréséhez.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. A deployment.template.json fájlra a Visual Studio Code Explorerben kattintson jobb gombbal, majd válassza ki **IoT Edge megoldás fordítása**.

11. Figyelje meg, hogy ez a parancs létrehoz egy config mappába deployment.amd64.json-fájllal. Ez a fájl a megoldás a tényleges telepítési sablon.

## <a name="add-router-module"></a>Útválasztó-modul hozzáadása

Ezután hozzáadjuk az útválasztó modul a megoldáshoz. Az útválasztó modul kezeli a megoldás több feladatokról:

* **Üzenet fogadása levéleszközök:** üzenetek érkezésekor az IoT Edge-eszközön az alsóbb rétegbeli eszközök, az útválasztó modul fogadja az üzenetet, és megkezdi a replikálásával segít a vállalatnak az üzenet útválasztást.
* **Üzenetek küldése az RUL osztályozó modul:** új üzenet érkezik egy alsóbb rétegbeli eszközről, ha az útválasztó modul átalakítja a formátumot, az RUL osztályozó vár az üzenetre. Az útválasztó az RUL osztályozó az RUL előrejelzési az üzenetet küld. Az osztályozó által végrehajtott előrejelzési, miután az üzenetet küld vissza az útválasztó modul.
* **RUL üzenetek küldése az IoT hubhoz:** az útválasztó üzeneteket fogad az osztályozó által igénybe vett, ha az üzenet csak a lényeges információkat tartalmazzák, Eszközazonosítót és az RUL, alakítja át, és a rövidített üzenetet küld az IoT hub. További finomítható, és nem eddig még itt, lenne üzenetek küldése az IoT Hub csak akkor, ha az RUL előrejelzési a küszöbérték alá esik, (például, ha az RUL 100-nál kevesebb ciklusok). Ezzel a módszerrel szűrés lenne minimalizálható az üzenetek és a költségek csökkentésére az IoT hub.
* **Üzenet küldése az Avro-író modul:** meg szeretné őrizni az alsóbb rétegbeli eszközök által küldött összes adatot, az útválasztó modul az osztályozó által igénybe vett kapott az Avro-író modul, amely továbbra is fennáll, és töltse fel az adatokat az IoT Hub-fájllal a teljes üzenetet küld Töltse fel.

> [!NOTE]
> A modul felelősségi leírása elérhetővé teheti a feldolgozást, úgy tűnik, egymást követő, de a folyamat üzenet/esemény alapján. Ezért az útválasztó modul például egy vezénylési modul van szükségünk.

### <a name="create-module-and-copy-files"></a>A modul, és átmásolja a fájlokat létrehozása

1. A jobb gombbal a Visual Studio Code-ban a modulok mappát, és válassza a **IoT Edge-modul hozzáadása**.

2. Válasszon  **C# modul**.

3. A modul neve **turbofanRouter**.

4. Ha a rendszer kéri a Docker-rendszerkép adattárba, a beállításjegyzék használata a machine learning-munkaterület (a registryCredentials csomópontjában találhatja meg a beállításjegyzék a *deployment.template.json* fájlt). Ezt az értéket az a teljes cím, a regisztrációs adatbázisba, például  **\<a beállításjegyzék\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > Ez a cikk az Azure Container Registry használtuk, betanítását és üzembe helyezése az osztályozó által igénybe vett Azure Machine Learning szolgáltatás munkaterület által létrehozott használjuk. Ez csupán kényelmi célokat szolgál a. Sikerült létrehozni egy új tároló-beállításjegyzéket azt, és a modulok van közzétéve.

5. Nyisson meg egy új terminálablakban a Visual Studio Code (**nézet** > **terminál**), és másolja a fájlokat a modulok könyvtárból.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Amikor a rendszer kéri, írja felül a program.cs, nyomja le az ENTER `y` majd nyomja le az `Enter`.

### <a name="build-router-module"></a>Útválasztó modul létrehozása

1. Válassza ki a Visual Studio Code- **terminálon** > **konfigurálása alapértelmezett készítése feladatot**.

2. Kattintson a **tasks.json fájl létrehozása sablon alapján**.

3. Kattintson a **.NET Core**.

4. Tasks.json megnyitásakor cserélje le a tartalmak:

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Mentse és zárja be a tasks.json.

6. A futtatási build `Ctrl + Shift + B` vagy **terminálon** > **létrehozása feladat futtatása**.

### <a name="set-up-module-routes"></a>A modul útvonalak beállítása

Ahogy említettük, az IoT Edge-futtatókörnyezet használ-e a konfigurált útvonalak a *deployment.template.json* fájl közötti kommunikáció kezelése lazán összekapcsolva modulok. Ebben a szakaszban ráközelítünk az útvonalakat a turbofanRouter modul beállítása. Hogy először terjed ki a bemeneti útvonalakat, és aztán folytassa a kimenetek.

#### <a name="inputs"></a>Bemenetek

1. A Program.cs Init() módszer Regisztráljon a modul két visszahívások:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Az első visszahívási figyeli a küldött üzenetek a **deviceInput** fogadó. A fenti ábrán láthatjuk, hogy szeretnénk üzenetek továbbítását a bemeneti bármely levél eszközről. Az a *deployment.template.json* fájlt, adjon hozzá egy útvonalat, amely arra utasítja az edge hub által az IoT Edge-eszköz, amely az IoT Edge-modul nem küldött be a "deviceInput" nevű bemeneti Beérkezett üzenetek irányíthatja az turbofanRouter modulban:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Ezután adjon hozzá egy útvonalat üzenetek rulClassifier modulból a turbofanRouter modulba:

   ```json
   "classifierToRouter": "FROM /messages/modules/classifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Kimenetek

Négy további útvonalakat adjon a $edgeHub útvonal paramétert, az útválasztó modul kimenetei kezelésére.

1. Program.cs határozza meg a SendMessageToClassifier(), amely a modul-ügyfelet használ, egy üzenet küldéséhez az RUL osztályozó az útvonal segítségével módszert:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/classifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() a modul-ügyfelet használ, az eszköz csak az RUL adatokat küldhet az IoT Hub, az útvonal-n keresztül:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() a modul ügyfél elküldi az üzenetet az RUL adatokat hozzáadni a avroFileWriter modul használja.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() küld sikertelen üzeneteket az IoT Hub, ahol azok átirányíthatók későbbi használatra.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Összefoglalva az "$edgeHub" az összes útvonal a csomópont a következő JSON hasonlóan kell kinéznie:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> Az alábbi további útvonal hozzáadása a turbofanRouter modul létrehozott: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Távolítsa el ezt az útvonalat, és csak az útvonalakat a deployment.template.json fájl fent felsorolt.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Copy routes to deployment.debug.template.json

Utolsó lépésként szinkronizálja a fájlokat, hogy tükrözze a deployment.template.json deployment.debug.template.json a végrehajtott módosításokat.

## <a name="add-avro-writer-module"></a>Az Avro-író modul hozzáadása

Az Avro-író modul rendelkezik két feladataikat a megoldásban az üzenetek tárolására, és töltse fel a fájlokat.

* **Üzenetek Store**: az Avro-író modul egy üzenetet kap, amikor az üzenetet ír a helyi fájlrendszerben Avro formátumban. A kötés csatlakoztatási, amivel be egy útvonalat a modul tárolóban egy könyvtárat (a ez megkülönbözteti a kis /data/avrofiles) használunk. A csatlakoztatási lehetővé teszi, hogy a modul egy helyi elérési útra (/ avrofiles), és ezeket a fájlokat közvetlenül az IoT Edge-eszközön elérhető.

* **Fájlok feltöltése**: az Avro-író modul az Azure IoT Hub fájlfeltöltési funkcióval használ a fájlok feltöltése egy Azure storage-fiókot. Miután a fájl sikeresen feltöltve, a modul törli a fájlt a lemezről

### <a name="create-module-and-copy-files"></a>A modul, és átmásolja a fájlokat létrehozása

1. A parancskatalógus, keresse meg, majd válassza ki **Python: Válassza ki a Interpret**.

1. Válassza ki a C: találhatók-értelmező\\Python37.

1. Nyissa meg a parancskatalógust újra, és keresse meg és jelölje ki **terminálon: Válassza ki az alapértelmezett rendszerhéját**.

1. Amikor a rendszer kéri, válassza ki a **parancssor**.

1. Nyisson meg egy új terminál kezelőfelületet **terminál** > **új terminál**.

1. A jobb gombbal a Visual Studio Code-ban a modulok mappát, és válassza a **IoT Edge-modul hozzáadása**.

1. Válassza a **Python Module** (Python-modul) lehetőséget.

1. A modul "avroFileWriter" nevet.

1. Ha a rendszer kéri a Docker-rendszerkép adattárba, használja ugyanazt a beállításjegyzék, az útválasztó modul való hozzáadása során alkalmazott.

1. Fájlok másolása a mintamodul a megoldásba.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Ha main.py felülírása kéri, írja be a `y` majd nyomja le az `Enter`.

1. Figyelje meg, hogy filemanager.py és schema.py hozzá a megoldáshoz, és main.py frissítve lett.

> [!NOTE]
> Amikor megnyit egy Python-fájlt telepíteni pylint kérheti. Nem kell telepíteni a linter az oktatóanyag elvégzéséhez.

### <a name="bind-mount-for-data-files"></a>Az adatfájlok csatlakoztatási kötése

A bemutatása említik, mivel az író modul kötési csatlakoztatási írni az Avro-fájlok az eszköz fájlrendszerében jelenlétére támaszkodik.

#### <a name="add-directory-to-device"></a>Könyvtár hozzáadása eszközhöz

1. Csatlakozhat az IoT Edge-eszköz virtuális gép SSH-val.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Hozzon létre a könyvtárat, amely tartalmazza majd a mentett levél az eszközre.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Frissítse a directory-jogosultságokat, győződjön meg arról, hogy írható a tároló által.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. A könyvtár ellenőrzése most a felhasználó, csoport és a tulajdonos írás (w) engedéllyel rendelkezik.

   ```bash
   ls -la /data
   ```

   ![Könyvtár engedélyeinek avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>A modul könyvtár hozzáadása

A könyvtár hozzáadása a modul tárolóhoz, a docker-fájlok a avroFileWriter modul társított fog módosítjuk. Van a modul társított három docker-fájlok: Dockerfile.AMD64 Dockerfile.amd64.debug és Dockerfile.arm32v7. Ezeket a fájlokat kell szinkronban abban az esetben kívánjuk osztani a hibakeresést, vagy egy arm32-eszközre telepíteni. Ebben a cikkben csak a Dockerfile.amd64 összpontosítanak.

1. A fejlesztői gépén, nyissa meg a **Dockerfile.amd64** fájlt.

2. A fájl módosítása, hogy az az alábbi példához hasonlóan néz ki:

   ```dockerfile
   FROM ubuntu:xenial  

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   A `mkdir` és `chown` parancsok utasítja a Docker-létrehozási folyamat /avrofiles nevű az ábrán egy legfelső szintű könyvtár létrehozására, majd, hogy a moduleuser könyvtárban tulajdonosa. Fontos, hogy ezek a parancsok egészül ki a modul felhasználó bekerül a rendszerképet a `useradd` parancsot, mielőtt a környezet vált a moduleuser (felhasználói moduleuser).

3. A megfelelő módosításokat Dockerfile.amd64.debug és Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>A modul konfigurációjának frissítése

Az utolsó lépés a kötés létrehozására a kötési információ deployment.template.json (és deployment.debug.template.json) fájlok frissítéséhez.

1. Open deployment.template.json.

2. A modul definíciója avroFileWriter hozzáadásával módosítsa a `Binds` paraméter, amely a tároló könyvtár /avrofiles mutat az edge-eszközön a helyi könyvtárban. A modul definition meg kell egyeznie az ebben a példában:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Végezze el a megfelelő módosításokat deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Hozzáférés a csatlakoztatási config.yaml kötése

Hozzá kell adnunk a író modul egy további kötés. Ez a kötés hozzáférést biztosít a modul a kapcsolati karakterlánc olvasásához az IoT Edge-eszközön a /etc/iotedge/config.yaml fájlból. Igazolnia kell, hogy a kapcsolati karakterlánc létrehozása egy Iothubclientről úgy, hogy a feltöltés meghívhatjuk\_blob\_fájlok feltöltése az IoT hub async módszert. Az ezen kötési hozzáadásának lépései hasonlóak az előző szakaszban azokat.

#### <a name="update-directory-permission"></a>Könyvtárengedélyek frissítése

1. Az IoT Edge-eszköz SSH használatával csatlakozhat.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Adja hozzá a config.yaml fájl olvasási engedéllyel.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Ellenőrizze az engedélyek helyes beállítását.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Ellenőrizze, hogy vannak-e config.yaml engedélyeinek **- r – r – r--** .

#### <a name="add-directory-to-module"></a>Könyvtár hozzáadása a modul

1. A fejlesztői gépén, nyissa meg a **Dockerfile.amd64** fájlt.

2. Adjon hozzá további `mkdir` és `chown` parancsok a fájlt úgy, hogy néz:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. A megfelelő módosításokat Dockerfile.amd64.debug és Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>A modul konfigurációjának frissítése

1. Nyissa meg a **deployment.template.json** fájlt.

2. A második sor való hozzáadásával avroFileWriter modul definíciója módosítsa a `Binds` paraméter, amely a helyi könyvtárban (/ etc/iotedge) az eszközön a tároló könyvtár (/ app/iotconfig) mutat.

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Végezze el a megfelelő módosításokat deployment.debug.template.json.

## <a name="install-dependencies"></a>Függőségek telepítése

Az író modul függőséget vesz fel két Python-kódtárakat, fastavro és PyYAML. Telepítse a függőségeket a fejlesztői gépen, és kérje meg a Docker buildelési folyamat, hogy telepítse őket a modul képen kell.

### <a name="pyyaml"></a>PyYAML

1. A fejlesztői gépén, nyissa meg a **requirements.txt** fájlt, és pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Nyissa meg a **Dockerfile.amd64** fájlt, és adjon hozzá egy `pip install` parancs setuptools frissítése.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Végezze el a megfelelő módosításokat Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Pyyaml helyi telepítése és megnyitása a parancsot egy terminálban a Visual Studio Code

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. A Requirements.txt fájlt adja hozzá a fastavro pyyaml után.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. A fejlesztői gépére a terminál a Visual Studio Code telepítése a fastavro.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Konfigurálja újra az IoT Hub

A rendszer az IoT Edge-eszköz és a modulok bevezetésével úgy változtattuk arról, hogy milyen adatokat küld a hubhoz, és milyen célra az elvárásokat. Konfigurálja újra az agyban az új valóság foglalkozik az Útválasztás kell.

> [!NOTE]
> Azt konfigurálja újra a hub, mert egyes hub beállításai, kifejezetten a fájl feltöltése, igényeinek megfelelően beállítandó megfelelő működéséhez a avroFileWriter modult a modulok üzembe helyezése előtt

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Állítsa be a RUL üzeneteket az IoT Hub az útvonalat

Az útválasztó és helyen osztályozó Terveink szerint már csak az eszköz Azonosítójával és az eszköz az RUL előrejelzést tartalmazó rendszeres üzeneteket fogadni. Szeretnénk az RUL adatainak átirányítása a saját tárolási helyre, ahol azt is az eszközök állapotának figyelése, jelentések készítése és a riasztások igény szerint. Egy időben szeretnénk minden olyan eszköz, amely még nem lett csatolva az IoT Edge-eszköz továbbra is a jelenlegi tárolási hely átirányítása levél eszköz általi közvetlen továbbra is elküldött adatokat.

#### <a name="create-a-rul-message-route"></a>Üzenet útvonal rul-JÉNEK létrehozása

1. Az Azure Portalon lépjen az IoT hubnak.

2. A bal oldali navigációs sávján válassza **üzenet-útválasztása**.

3. Válassza a **Hozzáadás** lehetőséget.

4. Nevezze el az útvonal **RulMessageRoute**.

5. Válassza ki **Hozzáadás** mellett a **végpont** választó, és válassza a **a Blob storage-** .

6. Az a **egy storage-végpont hozzáadása** űrlap be egy nevet a végpont **ruldata**.

7. Válassza ki **tároló kiválasztása**.

8. Válassza ki a tárfiókot, oktatóanyag, amely hasonló nevű során használt **iotedgeandml\<egyedi utótagja\>** .

9. Válassza ki a **ruldata** tárolóra, majd kattintson **kiválasztása**.

10. Kattintson a **létrehozás** a storage-végpont létrehozásához.

11. Az a **útválasztási lekérdezés**, írja be a következő lekérdezést:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Bontsa ki a **teszt** szakaszban, majd a **üzenet törzse** szakaszban. Cserélje le az üzenet ebben a példában a várt üzenetek:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Válassza ki **vizsgálati útvonal**. Ha a teszt sikeres, megjelenik "a üzenet megfeleltetni a lekérdezést."

14. Kattintson a **Save** (Mentés) gombra.

#### <a name="update-turbofandevicetostorage-route"></a>Frissítés turbofanDeviceToStorage útvonal

Nem szeretnénk irányíthatja a régi tárhelyet az új előrejelzési adatokat, így megakadályozva annak Útvonalfrissítés.

1. Az IoT hub **üzenet-útválasztása** lapon válassza ki a **útvonalak** fülre.

2. Válassza ki **turbofanDeviceDataToStorage**, vagy bármi nevét, a kezdeti adatok útvonalhoz megadott.

3. Az útválasztási lekérdezés frissítése

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Bontsa ki a **teszt** szakaszban, majd a **üzenet törzse** szakaszban. Cserélje le az üzenet ebben a példában a várt üzenetek:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Válassza ki **vizsgálati útvonal**. Ha a teszt sikeres, megjelenik "a üzenet megfeleltetni a lekérdezést."

6. Kattintson a **Mentés** gombra.

### <a name="configure-file-upload"></a>Fájlfeltöltés konfigurálása

Az IoT Hub fájlfeltöltési funkcióval ahhoz, hogy a fájlok feltöltése a storage író modul konfigurálása.

1. Válassza a bal oldali navigációs az IoT hub, **fájlfeltöltés**.

2. Válassza ki **Azure Storage-tároló**.

3. A listáról válassza ki a tárfiókját.

4. Válassza ki a **uploadturbofanfiles** tárolóra, majd kattintson **kiválasztása**.

5. Kattintson a **Mentés** gombra. A portál értesíti, amikor befejeződött a mentés.

> [!Note]
> Ebben az oktatóanyagban fájlfeltöltési értesítés bekapcsolása nem egyelőre lásd: [fájlfeltöltési üzenetet kap](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) a megtudhatja, hogyan kezelje a fájl feltöltése értesítést.

## <a name="build-publish-and-deploy-modules"></a>Hozhat létre, közzététel és modulok telepítése

Most, hogy a konfigurációs módosításokat hajtott végre, hogy a lemezképeket, és közzéteheti őket az Azure container registrybe készen áll. A létrehozási folyamat deployment.template.json fájlt használja, melyik modulokat kell meghatározni. A beállítások minden egyes modul verzióját, beleértve a module.json fájlt a modul mappájában található. A létrehozási folyamat először futtat egy Docker build a docker-fájlok hozzon létre egy rendszerképet a module.json fájlban található aktuális konfigurációja megfelelő. Majd közzéteszi a rendszerképet a regisztrációs adatbázisba a module.json fájlból egy, a module.json fájl megfelelő verziója címkével ellátott. Végül küld egy konfiguráció-specifikus manifest nasazení (például deployment.amd64.json), amely fogjuk üzembe helyezni az IoT Edge-eszközön. Az IoT Edge-eszköz beolvasása az információkat az utasításokat az üzemelő példányból jegyzékfájl és alapján töltse le a modulok, az útvonalak konfigurálása, és állítsa be a kívánt tulajdonságokat. Ez a telepítési módszer van, akkor célszerű tisztában lennie a két hatásai:

* **Üzembe helyezés késés:** az IoT Edge-futtatókörnyezet segítségével konfigurálja újra a megkezdése előtt fel kell ismernie a kívánt tulajdonságainak módosítása, mert néhány mennyi ideig, amíg a futtatókörnyezet felveszi őket, és elindítja az IoT Edge frissíteni a modulok telepítése után is igénybe vehet az eszköz.

* **A modul verziók függetlenül attól, hogy:** egy modul tároló új verziójának a container registry-mint az előző modul azonos verziójú címkék használatával tesz közzé, ha a futtatókörnyezet nem tölt le az új verziót a modult. Manifest nasazení a hajtja végre a helyi rendszerképet, és a kívánt lemezkép verzió címkéjének összehasonlítása. Ha verziókat egyezik, a futásidejű hajt végre semmilyen műveletet. Ezért fontos a modul verzióját növekszik minden alkalommal, amikor szeretne telepíteni az új módosításokat. A verzióját növekszik módosításával a **verzió** tulajdonság alatt a **címke** tulajdonság módosítja a modul module.json fájlban. Ezután létrehozhatja és közzététele a modult.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Létrehozása és közzététele

1. Nyissa meg a virtuális gép fejlesztés a Visual Studio Code egy Visual Studio Code-terminálablakot, és jelentkezzen be a tárolóregisztrációs adatbázis.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. A Visual Studio Code-deployment.template.json kattintson a jobb gombbal, és válassza a **Build és a leküldéses IoT Edge-megoldás**.

### <a name="view-modules-in-the-registry"></a>A beállításjegyzékben modulok megjelenítése

A build sikeres befejezése után azt fogja tudni a közzétett modulok tekintse át az Azure portal használatával.

1. Az Azure Portalon lépjen az Azure Machine Learning szolgáltatás munkaterületén, és kattintson a hivatkozásra **beállításjegyzék**.

    ![Keresse meg a machine learning-munkaterület szolgáltatási beállításjegyzék](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Válassza ki a beállításjegyzék ügyféloldali kezelő **Tárházak**.

3. Vegye figyelembe, hogy mindkét modult hozta létre, **avrofilewriter** és **turbofanrouter**, tárházak jelennek meg.

4. Válassza ki **turbofanrouter** , és jegyezze fel, hogy közzétette-e megjelölve 0.0.1-amd64 egy rendszerképet.

   ![Nézet első címkézett verziója turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Modulok IoT Edge-eszköz üzembe helyezése

A Microsoft beépített és konfigurálni a modulok a megoldásban a most üzembe fogjuk helyezni a modulokat az IoT Edge-eszközön.

1. A Visual Studio Code, kattintson a jobb gombbal a **deployment.amd64.json** config mappában található fájl.

2. Válasszon **hozzon létre telepítést az adott eszköz**.

3. Válassza ki az IoT Edge-eszköz **aaTurboFanEdgeDevice**.

4. Frissítse az Azure IoT Hub-Eszközök panel a Visual Studio Code Explorerben. Megtekintheti, hogy a három új modulok telepítve vannak, de még nem működik.

5. Újra néhány perc múlva frissítse, és látni fogja a futó modulok.

   ![Visual Studio Code-ban futó modulok megtekintése](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> A modulok elindításához és a egy állandó működőképes állapotba kerülnek több percet is igénybe vehet. Ez idő alatt látni modulok elindítása és leállítása, amikor az IoT Edge hubot modullal kapcsolatot létesíteni.

## <a name="diagnosing-failures"></a>Hibák diagnosztizálása

Ebben a szakaszban osztjuk meg néhány technikák megértésében, hogy milyen merült fel egy modult vagy modulokat. Gyakran hiba is először kell pedig a pöttyös, a Visual Studio Code az állapotát.

### <a name="identify-failed-modules"></a>Sikertelen modulok azonosítása

* **Visual Studio Code:** Tekintse meg az Azure IoT Hub-Eszközök panelen. A legtöbb modul fut, de az egyik le van állítva, ha kell, hogy leállt a modul további vizsgálata. Minden modul egy hosszú ideig leállított állapotban van, ha azt jelentheti, valamint a sikertelen.

* **Az Azure Portalon:** Az IoT hubhoz a portálon, és keresse meg az eszköz részleteit megjelenítő oldalon (IoT Edge, a részletes az eszköz) azt tapasztalhatja, hogy egy modul hibát jelzett, vagy soha nem jelentett semmit az IoT hubnak.

### <a name="diagnosing-from-the-device"></a>Az eszközről diagnosztizálása

Az IoT Edge-eszköz bejelentkezve is megkapta a hozzáférést egy jó teljesítményű a modulok állapotára vonatkozó információkat. A fő mechanizmus használjuk, tudassa velünk vizsgálja meg a tárolók és az eszköz-rendszerképek a Docker-parancsokat is.

1. Az összes futó tárolók listája. Terveink szerint már megtekintheti az egyes modulok, amelynek neve megegyezik a modul egy tárolót. Ez a parancs felsorolja a pontos képet a tároló verziója többek között, így az az elvárás egyeztetéséhez. Képek is listázhatja az "image" a "container" parancsban le.

   ```bash
   sudo docker container ls
   ```

2. A naplók lekérése tárolóhoz. Ez a parancs kimenete a tároló StdOut és StdErr bármilyen lett írva. Ez a parancs működik tárolók használatába, és valamilyen okból majd megszűnt. Emellett akkor is hasznos, mi van lett edgeAgent vagy edgeHub tárolókkal megértésében.

   ```bash
   sudo docker container logs <container name>
   ```

3. Vizsgálja meg a tárolóban. Ez a parancs a kép információ rengeteg biztosít. Az adatok szűrhetők, attól függően, amit keres. Például ha meg szeretné tekinteni, ha a összekapcsolja a avroFileWriter a helyesek a parancsot használja:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Csatlakozás a futó tárolót. Ez a parancs lehet hasznos, ha meg szeretné vizsgálni a tároló működés közben:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy IoT Edge-megoldás a három modult a Visual Studio Code egy osztályozó, és a egy fájl író/feltöltő. Azt az útvonalak beállításához, hogy a modulokat az edge-eszközön kommunikálnak egymással, az edge-eszköz konfigurációjának módosítva, és a függőségek telepítése és a kötési csatlakoztatása ad hozzá a modulok tárolók docker-fájlok frissítése. Ezután frissítettük az üzenetek típus alapján, és kezelni a fájlok feltöltése az IoT Hub konfigurációját. Minden helyen hogy a modulok IoT Edge-eszközökön üzembe helyezett és biztosítani, hogy a modulok megfelelően futottak.

További információkat tekinthet meg a következő lapokon:

* [Ismerje meg, hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat IoT Edge-ben](module-composition.md)
* [Az IoT Hub üzenet-útválasztás lekérdezési szintaxis](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub üzenet-útválasztása: mostantól az üzenettörzs a Útválasztás](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Fájlok feltöltése az IoT Hubbal](../iot-hub/iot-hub-devguide-file-upload.md)
* [Töltse fel a fájlokat az eszközről a felhőbe, az IoT hubbal](../iot-hub/iot-hub-python-python-file-upload.md)

A következő cikk adatokat küldjön, és láthatja a megoldás a művelet továbbra is.

> [!div class="nextstepaction"]
> [Transzparens átjáró adatküldést](tutorial-machine-learning-edge-07-send-data-to-hub.md)
