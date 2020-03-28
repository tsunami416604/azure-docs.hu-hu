---
title: 'Oktatóanyag: Egyéni modulok létrehozása és üzembe helyezése – Machine Learning az Azure IoT Edge-en'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és helyezhet üzembe IoT Edge-modulokat, amelyek egy gépi tanulási modellen keresztül feldolgozzák az adatokat a levéleszközökről, majd elküldik az elemzéseket az IoT Hubnak.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3cba7781ac80ae567b2bfd54c4131429ed94b90f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75772363"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Oktatóanyag: Egyéni IoT Edge-modulok létrehozása és üzembe helyezése

> [!NOTE]
> Ez a cikk az Azure Machine Learning IoT Edge-en való használatával kapcsolatos oktatóanyag sorozatának része. Ha megérkezett ezt a cikket közvetlenül, javasoljuk, hogy kezdődik az [első cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredményt.

Ebben a cikkben három IoT Edge-modult hozunk létre, amelyek üzeneteket fogadnak a levéleszközökről, futtatják az adatokat a gépi tanulási modellen keresztül, majd továbbítják az elemzéseket az IoT Hubnak.

Az IoT Edge hub megkönnyíti a modul-kommunikációt. Az IoT Edge hub üzenetközvetítőként való használata a modulokat egymástól függetlenné teszi. A moduloknak csak azokat a bemeneteket kell megadniuk, amelyekre üzeneteket fogadnak, és azokat a kimeneteket, amelyekre üzeneteket írnak.

Azt szeretnénk, hogy az IoT Edge-eszköz négy dolgot érjen el számunkra:

* Adatok fogadása a levéleszközökről
* Az adatokat küldő eszköz hátralévő hasznos élettartamának (RUL) előrejelzése
* Üzenet küldése csak az eszköz RUL-jával az IoT Hubnak (ez a funkció módosítható, hogy csak akkor küldjön adatokat, ha az RUL valamilyen szint alá esik)
* Mentse a levél eszköz adatait egy helyi fájlba az IoT Edge-eszközön. Ezt az adatfájlt rendszeresen feltölti az IoT Hub fájlfeltöltés a gépi tanulási modell betanításának finomítása érdekében. A fájlfeltöltés használata az állandó üzenettovábbítás helyett költséghatékonyabb.

E feladatok elvégzéséhez három egyéni modult használunk:

* **RUL osztályozó:** A turboFanRulClassifier modul, amelyet a Train és [az Azure Machine Learning-modell üzembe helyezése során](tutorial-machine-learning-edge-04-train-model.md) hoztunk létre, egy szabványos gépi tanulási modul, amely egy "amlInput" nevű bemenetet és egy "amlOutput" nevű kimenetet tetszhet. Az "amlInput" azt várja, hogy a bemeneti pontosan úgy néz ki, mint az ACI-alapú webszolgáltatásnak küldött bemenet. Hasonlóképpen az "amlOutput" ugyanazokat az adatokat adja vissza, mint a webszolgáltatás.

* **Avro író:** Ez a modul üzeneteket fogad az "avroModuleInput" bemeneten, és a message avro formátumban a lemezre későbbi feltöltés ioT Hub.

* **Útválasztó modul:** Az útválasztó modul üzeneteket fogad az alsóbb rétegbeli levéleszközökről, majd formázza és elküldi az üzeneteket az osztályozónak. A modul ezután megkapja az üzeneteket az osztályozótól, és továbbítja az üzenetet az Avro írómodulra. Végül a modul csak a RUL-előrejelzést küldi az IoT Hubnak.

  * Bemenetek:
    * **deviceInput**: üzeneteket fogad levéleszközökről
    * **rulInput:** üzeneteket fogad az "amlOutput"

  * Kimenetek:
    * **osztályzat:** üzeneteket küld az "amlInput"
    * **writeAvro:** üzeneteket küld az "avroModuleInput"
    * **toIotHub:** üzeneteket küld $upstream, amely továbbítja az üzeneteket a csatlakoztatott IoT Hub

Az alábbi ábra bemutatja a modulokat, bemeneteket, kimeneteket és az IoT Edge Hub-útvonalakat a teljes megoldáshoz:

![IoT Edge három modul architektúradiagramja](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

A cikkben ismertetett lépéseket általában egy felhőalapú fejlesztő hajtja végre.

## <a name="create-a-new-iot-edge-solution"></a>Új IoT Edge-megoldás létrehozása

Két Azure-jegyzetfüzetünk közül a második végrehajtása során létrehoztunk és közzétettünk egy rul-modellt tartalmazó tárolórendszerképet. Az Azure Machine Learning a lemezkép-létrehozási folyamat részeként úgy csomagolta ezt a modellt, hogy a rendszerkép Azure IoT Edge-modulként telepíthető. Ebben a lépésben egy Azure IoT Edge-megoldást fogunk létrehozni az "Azure Machine Learning" modul használatával, és a modult az Azure Notebooks használatával közzétett lemezképre fogjuk mutatni.

1. Nyisson meg egy távoli asztali munkamenetet a fejlesztői gépre.

2. Nyissa meg a **C mappát:\\forrás\\IoTEdgeAndMlSample** a Visual Studio-kódban.

3. Kattintson a jobb gombbal a felfedező panelre (az üres helyen), és válassza az **Új IoT Edge-megoldás lehetőséget**.

    ![Új IoT Edge-megoldás létrehozása](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Fogadja el az **edgesolution**alapértelmezett megoldásnevet.

5. Válassza az **Azure Machine Learning** modulsablonként.

6. Nevezze el a **modulturbofanRulClassifier**.

7. Válassza ki a gépi tanulási munkaterületet.

8. Válassza ki az Azure-jegyzetfüzet futtatása közben létrehozott lemezképet.

9. Tekintse meg a megoldást, és figyelje meg a létrehozott fájlokat:

   * **deployment.template.json:** Ez a fájl tartalmazza a megoldásban lévő modulok mindegyikének definícióját. Három szakaszra kell figyelni ebben a fájlban:

     * **Rendszerleíró hitelesítő adatok:** A megoldásban használt egyéni tárolójegyzékek készletét határozza meg. Most tartalmaznia kell a beállításjegyzéket a gépi tanulási munkaterületről, amely az Azure Machine Learning-lemezkép tárolásának helye. Tetszőleges számú tároló-nyilvántartási rendszerrel rendelkezhet, de az egyszerűség kedvéért ezt az egy rendszerleíró adatbázist használjuk az összes modulhoz

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Modulok:** Ez a szakasz a megoldással együtt a felhasználó által definiált modulok készletét tartalmazza. Észre fogja venni, hogy ez a szakasz jelenleg két modult tartalmaz: SimulatedTemperatureSensor és turbofanRulClassifier. A SimulatedTemperatureSensor-t a Visual Studio Code sablon telepítette, de ehhez a megoldáshoz nincs szükségünk. Törölheti a SimulatedTemperatureSensor modul definícióját a modulok szakaszból. Vegye figyelembe, hogy a turbofanRulClassifier modul definíciója a rendszerképre mutat a tároló rendszerleíró adatbázisában. Ahogy további modulokat adunk a megoldáshoz, akkor megjelennek ebben a szakaszban.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
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

     * **Útvonalak:** mi lesz a munka útvonalak egy kicsit ez a bemutató. Az útvonalak határozzák meg, hogyan kommunikálnak egymással a modulok. A sablon által meghatározott két útvonal nem egyezik meg a szükséges útválasztással. Az első útvonal az osztályozó bármely kimenetéből származó összes adatot elküldi az IoT Hubnak ($upstream). A másik útvonal a SimulatedTemperatureSensor, amit csak törölt. Törölje a két alapértelmezett útvonalat.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **deployment.debug.template.json:** ez a fájl a deployment.template.json hibakeresési verziója. A deployment.template.json összes módosítását tükröznünk kell ebbe a fájlba.

   * **.env:** ez a fájl az, ahol meg kell adnia a felhasználónevet és a jelszót a rendszerleíró adatbázis eléréséhez.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Kattintson a jobb gombbal a deployment.template.json fájlra a Visual Studio Kódkezelőben, és válassza **az IoT Edge-megoldás összeállítása parancsot.**

11. Figyelje meg, hogy ez a parancs létrehoz egy konfigurációs mappát egy deployment.amd64.json fájllal. Ez a fájl a megoldás konkrét telepítési sablonja.

## <a name="add-router-module"></a>Útválasztó hozzáadása modul

Ezután hozzáadjuk a Router modult a megoldásunkhoz. A Router modul számos feladatot lát el a megoldásunkhoz:

* **Üzenetek fogadása levéleszközökről:** ahogy az üzenetek az IoT Edge-eszközre érkeznek az alsóbb rétegbeli eszközökről, az útválasztó modul fogadja az üzenetet, és megkezdi az üzenet irányításának vezénylését.
* **Üzenetek küldése a RUL Classifier modulnak:** amikor új üzenet érkezik egy alsóbb rétegbeli eszközről, az Útválasztó modul az üzenetet a RUL-osztályozó által elvárt formátumra alakítja át. Az útválasztó rul-előrejelzésesetén elküldi az üzenetet a RUL-osztályozónak. Miután az osztályozó készített egy előrejelzést, visszaküldi az üzenetet az útválasztó modulnak.
* **RUL-üzenetek küldése az IoT Hub:** amikor az útválasztó üzeneteket fogad az osztályozótól, átalakítja az üzenetet, hogy csak az alapvető információkat, az eszközazonosítót és a RUL-t tartalmazza, és elküldi a rövidített üzenetet az IoT hubnak. Egy további finomítás, amely et itt nem tettünk meg, csak akkor küldene üzeneteket az IoT Hubnak, ha a RUL-előrejelzés egy küszöbérték alá esik (például ha a rul kevesebb, mint 100 ciklus). Szűrés ily módon csökkentené az üzenetek mennyiségét, és csökkenti az IoT hub költségeit.
* **Küldjön üzenetet az Avro Writer modulnak:** az alsóbb rétegbeli eszköz által küldött összes adat megőrzéséhez az útválasztó modul elküldi az osztályozótól kapott teljes üzenetet az Avro Writer modulnak, amely megmarad, és feltölti az adatokat az IoT Hub fájlfeltöltés használatával.

> [!NOTE]
> A modul felelősségi körök leírása lehet, hogy a feldolgozás szekvenciálisnak tűnik, de a folyamat üzenet/esemény alapú. Ezért van szükségünk egy vezénylési modulra, mint a Router modulunk.

### <a name="create-module-and-copy-files"></a>Modul létrehozása és másolása

1. Kattintson a jobb gombbal a Modulok mappára a Visual Studio Kód ban, és válassza **az IoT Edge module hozzáadása parancsot**.

2. Válassza a **C# modult.**

3. Nevezze el a modul **turbofanRouter**.

4. Amikor a docker-rendszertár, használja a rendszerleíró adatbázist a gépi tanulási munkaterület (megtalálhatja a rendszerleíró adatbázisa a registryCredentials csomópont a *deployment.template.json* fájl). Ez az érték a rendszerleíró adatbázis teljesen minősített címe, ** \<például a rendszerleíró\>adatbázis .azurecr.io/turbofanrouter**.

    > [!NOTE]
    > Ebben a cikkben az Azure Machine Learning-munkaterület által létrehozott Azure Container Registry-t használjuk, amelyet az osztályozónk betanításához és üzembe helyezéséhez használtunk. Ez csak a kényelem. Létrehozhattunk volna egy új konténer-nyilvántartást, és ott is közzétehettük volna a moduljainkat.

5. Nyisson meg egy új terminálablakot a Visual Studio Kód (**Terminál****megtekintése** > ) alkalmazásban, és másolja a fájlokat a modulok könyvtárából.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Amikor a rendszer kéri a `y` program.cs `Enter`felülírását, nyomja meg, majd nyomja meg a gombot.

### <a name="build-router-module"></a>Útválasztó-modul létrehozása

1. A Visual Studio-kódban válassza a > **Terminálkonfigurálás alapértelmezett buildfeladatát**. **Terminal**

2. Kattintson a **Tasks.json fájl létrehozása sablonból lehetőségre.**

3. Kattintson a **.NET Core gombra.**

4. Amikor a tasks.json megnyílik, cserélje le a tartalmat a következőkre:

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

5. A tasks mentése és bezárása.json.

6. Futtassa `Ctrl + Shift + B` a buildet vagy **a** > **Terminálfuttatási létrehozási feladattal.**

### <a name="set-up-module-routes"></a>Modulútvonalak beállítása

Mint már említettük, az IoT Edge futásidejű a *deployment.template.json* fájlban konfigurált útvonalakat használ a lazán összekapcsolt modulok közötti kommunikáció kezeléséhez. Ebben a részben, mi fúró, hogyan kell beállítani az útvonalakat a turbofanRouter modul. Először a bemeneti útvonalakat fedjük le, majd a kimeneteket.

#### <a name="inputs"></a>Bemenetek

1. Az Init() metódusProgram.cs regisztráljon két visszahívást a modulhoz:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Az első visszahívás figyeli az eszközre küldött **üzeneteketInput fogadó.** A fenti ábrán azt látjuk, hogy szeretnénk átirányítani üzeneteket bármely levél eszköz erre a bemenetre. A *deployment.template.json* fájlban adjon hozzá egy útvonalat, amely megmondja a peremhálózati hubnak, hogy továbbítsa az IoT Edge-eszköz által fogadott üzeneteket, amelyeket nem egy IoT Edge-modul küldött a turbofanRouter modul "deviceInput" nevű bemenetére:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Ezután adjunk hozzá egy útvonalat az üzeneteket a rulClassifier modul a turbofanRouter modul:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Kimenetek

Adjon hozzá további négy útvonalat a $edgeHub útvonalparaméterhez, hogy kezelni tudja az útválasztó modul kimeneteit.

1. Program.cs a SendMessageToClassifier() metódust határozza meg, amely a modulügyfelet használja üzenet küldésére a RUL-osztályozónak az útvonal használatával:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. A SendRulMessageToIotHub() a modulügyfél segítségével csak az eszköz RUL-adatait küldi el az IoT Hubnak az útvonalon keresztül:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. A SendMessageToAvroWriter() a modulügyfél segítségével küldi el az üzenetet az avroFileWriter modulhoz hozzáadott RUL-adatokkal.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() sikertelen üzeneteket küld az IoT Hub onstream, ahol később átirányíthatók.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Az összes útvonal együttesen a "$edgeHub" csomópont kell kinéznie a következő JSON:

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
> Hozzáadása a turbofanRouter modul létrehozta `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`a következő további útvonal: . Távolítsa el ezt az útvonalat, és csak a fent felsorolt útvonalakat hagyja meg a deployment.template.json fájlban.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Útvonalak másolása a deployment.debug.template.json alkalmazásba

Utolsó lépésként a fájlok szinkronban tartásához tükrözze a deployment.template.json fájlon végzett módosításokat a deployment.debug.template.json fájlban.

## <a name="add-avro-writer-module"></a>Avro Writer modul hozzáadása

Az Avro Writer modulnak két feladata van a megoldásunkban, az üzenetek tárolása és a fájlok feltöltése.

* **Üzenetek tárolása:** amikor az Avro Writer modul üzenetet kap, a helyi fájlrendszerbe írja az üzenetet Avro formátumban. Egy kötéscsatlakoztatást használunk, amely egy könyvtárat (ebben az esetben /data/avrofiles) csatlakoztat a modul tárolójának elérési útvonalába. Ez a csatlakoztatás lehetővé teszi, hogy a modul írjon egy helyi elérési útra (/avrofiles), és ezeket a fájlokat közvetlenül az IoT Edge-eszközről érhető el.

* **Fájlok feltöltése:** az Avro Writer modul az Azure IoT Hub fájlfeltöltési funkcióját használja a fájlok Azure-tárfiókba való feltöltéséhez. A fájl sikeres feltöltése után a modul törli a fájlt a lemezről

### <a name="create-module-and-copy-files"></a>Modul létrehozása és másolása

1. A parancspalettán keresse meg a **Python: Select Interpreter (Értelmező) lehetőséget.**

1. Válassza ki a C:\\Python37-ben található értelmezőt.

1. Nyissa meg újra a parancspalettát, és keressen rá, majd válassza a **Terminál: Válassza az Alapértelmezett rendszerhéj lehetőséget.**

1. Amikor a rendszer kéri, válassza a **Parancssor lehetőséget.**

1. Nyisson meg egy új terminálhéjat, **az** > **Új Terminál terminált.**

1. Kattintson a jobb gombbal a Modulok mappára a Visual Studio Kód ban, és válassza **az IoT Edge module hozzáadása parancsot**.

1. Válassza a **Python Module** (Python-modul) lehetőséget.

1. Nevezze el a modult "avroFileWriter".

1. Amikor a rendszer kéri a Docker-rendszertár, használja ugyanazt a rendszerleíró adatbázist, mint az útválasztó modul hozzáadásakor használt.

1. Fájlok másolása a mintamodulból a megoldásba.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Ha a rendszer kéri a `y` main.py `Enter`felülírását, írja be a szöveget, majd nyomja meg a programot.

1. Figyelje meg, hogy filemanager.py és schema.py lett hozzáadva a megoldáshoz, és main.py frissítve lett.

> [!NOTE]
> Amikor megnyit egy Python-fájlt, előfordulhat, hogy a rendszer kéri a pylint telepítését. Az oktatóanyag befejezéséhez nem kell telepítenie a lintert.

### <a name="bind-mount-for-data-files"></a>Kötéscsatlakoztatás adatfájlokhoz

Mint már említettük az intro, az író modul támaszkodik a jelenléte bind mount írni Avro fájlokat az eszköz fájlrendszer.

#### <a name="add-directory-to-device"></a>Könyvtár hozzáadása az eszközhöz

1. Csatlakozzon az IoT Edge-eszköz virtuális gépéhez az SSH használatával.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Hozza létre azt a könyvtárat, amely a mentett levéleszköz-üzeneteket fogja tárolni.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Frissítse a címtárengedélyeket, hogy a tároló írható legyen.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. A könyvtár hitelesítése most már rendelkezik írási (w) engedéllyel a felhasználó, a csoport és a tulajdonos számára.

   ```bash
   ls -la /data
   ```

   ![Az avrofájlok könyvtárengedélyei](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Könyvtár hozzáadása a modulhoz

A könyvtár hozzáadása a modul tárolójához, módosítjuk az avroFileWriter modulhoz társított Dockerfiles. A modulhoz három Docker-fájl van társítva: Dockerfile.amd64, Dockerfile.amd64.debug és Dockerfile.arm32v7. Ezeket a fájlokat szinkronban kell tartani arra az esetre, ha hibakeresést vagy üzembe helyezést szeretnénk egy arm32 eszközre. Ebben a cikkben csak a Dockerfile.amd64 fájlra összpontosítson.

1. A fejlesztői gépen nyissa meg a **Dockerfile.amd64** fájlt.

2. Módosítsa úgy a fájlt, hogy az a következő példához hasonlóan jelensen meg:

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

   A `mkdir` `chown` és a parancsok utasítják a Docker build folyamat, hogy hozzon létre egy legfelső szintű könyvtár nevű /avrofiles a lemezképben, majd hogy a moduleuser tulajdonosa, hogy a könyvtár. Fontos, hogy ezek a parancsok a modulfelhasználó hozzáadása után `useradd` és a modulfelhasználó modulfelhasználóra való váltása előtt a modulfelhasználóra (USER moduleuser) kerüljön be.

3. Végezze el a megfelelő módosításokat a Dockerfile.amd64.debug és a Dockerfile.arm32v7 fájlon.

#### <a name="update-the-module-configuration"></a>A modul konfigurációjának frissítése

A kötés létrehozásának utolsó lépése a deployment.template.json (és deployment.debug.template.json) fájlok frissítése a kötési adatokkal.

1. Nyissa meg a deployment.template.json t.

2. Módosítsa az avroFileWriter moduldefinícióját `Binds` a tárolókönyvtár /avrofiles paraméterének a peremhálózati eszköz helyi könyvtárához való hozzáadásával. A moduldefiníciónak meg kell egyeznie ezzel a példával:

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

3. Hajtsa végre a megfelelő módosításokat a deployment.debug.template.json számítógépen.

### <a name="bind-mount-for-access-to-configyaml"></a>Kötéstartó a config.yaml fájlhoz való hozzáféréshez

Még egy kötést kell hozzáadnunk az írómodulhoz. Ez a kötés hozzáférést biztosít a modulnak a kapcsolati karakterlánc olvasásához az IoT Edge-eszköz /etc/iotedge/config.yaml fájlból. Szükségünk van a kapcsolati karakterlánc hoz létre egy IoTHubClient, hogy meg tudjuk hívni a feltöltési\_blob\_async metódus fájlokat feltölteni az IoT hubra. A kötés hozzáadásának lépései hasonlóak az előző szakaszban felsoroltakhoz.

#### <a name="update-directory-permission"></a>Címtárengedély frissítése

1. Csatlakozzon az IoT Edge-eszközhöz az SSH használatával.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Adjon olvasási engedélyt a config.yaml fájlhoz.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Az engedélyek ellenőrzése megfelelően van beállítva.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Győződjön meg arról, hogy a config.yaml engedélyei **-r--r----**.

#### <a name="add-directory-to-module"></a>Könyvtár hozzáadása a modulhoz

1. A fejlesztői gépen nyissa meg a **Dockerfile.amd64** fájlt.

2. Adjon hozzá egy `mkdir` `chown` további készletet és parancsokat a fájlhoz, hogy a következőképpen nézhessen ki:

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

3. Végezze el a megfelelő módosításokat a Dockerfile.amd64.debug és a Dockerfile.arm32v7 fájlon.

#### <a name="update-the-module-configuration"></a>A modul konfigurációjának frissítése

1. Nyissa meg a **deployment.template.json** fájlt.

2. Módosítsa az avroFileWriter moduldefinícióját egy második `Binds` sor hozzáadásával a paraméterhez, amely a tárolókönyvtárat (/app/iotconfig) az eszköz helyi könyvtárához (/etc/iotedge) pontozzák.

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

3. Hajtsa végre a megfelelő módosításokat a deployment.debug.template.json számítógépen.

## <a name="install-dependencies"></a>Függőségek telepítése

Az írómodul két Python-kódtáraktól, a fastavro-tól és a PyYAML-től függ. Telepítenünk kell a függőségeket a fejlesztői gépünkre, és utasítanunk kell a Docker-buildfolyamatot, hogy telepítse őket a modulunk rendszerképében.

### <a name="pyyaml"></a>PyyaML között

1. A fejlesztői gépen nyissa meg a **requirements.txt** fájlt, és adja hozzá a pyyaml fájlt.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Nyissa meg a **Dockerfile.amd64** fájlt, és adjon hozzá egy `pip install` parancsot a telepítőeszközök frissítéséhez.

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

3. Végezze el a megfelelő módosításokat a Dockerfile.amd64.debug fájlon. <!--may not be necessary. Add 'if needed'?-->

4. A pyyaml helyi telepítése terminál megnyitásával a Visual Studio Code alkalmazásban, és gépelés

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro között

1. A requirements.txt fájlban a pyyaml után adja hozzá a fastavro-t.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Telepítse a fastavro-t a fejlesztőgépre a Visual Studio Code terminálsegítségével.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Az IoT Hub újrakonfigurálása

Az IoT Edge-eszköz és -modulok rendszerbe való bevezetésével megváltoztattuk az elvárásainkat azzal kapcsolatban, hogy milyen adatokat küld a hubnak, és milyen célból. Át kell konfigurálnunk az útvonalat a központban, hogy megbirkózzunk az új valósággal.

> [!NOTE]
> A modulok üzembe helyezése előtt újrakonfiguráljuk a központot, mert a hub egyes beállításait, különösen a fájlfeltöltést, megfelelően be kell állítani az avroFileWriter modul megfelelő futtatásához

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>RUL-üzenetek útvonalának beállítása az IoT Hubban

Az útválasztó és az osztályozó a helyén, azt várjuk, hogy rendszeres üzeneteket, amelyek csak az eszköz azonosítóját és a RUL előrejelzésaz eszköz. Azt szeretnénk, hogy a RUL-adatokat a saját tárolási helyére, ahol nyomon követheti az eszközök állapotát, jelentéseket és tűzriasztásokat készíthet, ha szükséges. Ugyanakkor azt szeretnénk, hogy minden olyan eszközadatokat, amelyek továbbra is közvetlenül egy levél eszköz, amely még nem csatlakozik az IoT Edge-eszköz továbbra is az aktuális tárolási helyre.

#### <a name="create-a-rul-message-route"></a>RUL-üzenetútvonal létrehozása

1. Az Azure Portalon keresse meg az IoT Hub.

2. A bal oldali navigációs sávon válassza az **Üzenetútválasztás lehetőséget.**

3. Válassza a **Hozzáadás** lehetőséget.

4. Nevezze el a **RulMessageRoute útvonalat.**

5. Válassza a **Hozzáadás** lehetőséget a **Végpontválasztó** mellett, és válassza a **Blob Storage (Blob storage) lehetőséget.**

6. A **Tárolási végpont hozzáadása** képernyőn nevezze el a végpont **ruldata**nevet.

7. Válassza **a Tároló kiválasztása**lehetőséget.

8. Válassza ki az oktatóanyagban használt tárfiókot, amely nek a neve például **iotedgeandml\<egyedi utótag\>**.

9. Válassza ki a **ruldata** tárolót, és kattintson **a Kijelölés gombra.**

10. A tárolási végpont létrehozásához kattintson a **Létrehozás** gombra.

11. Az **Útválasztás lekérdezéshez**írja be a következő lekérdezést:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Bontsa ki a **Teszt** szakaszt, majd az **Üzenet törzsszakaszt.** Cserélje le az üzenetet a várt üzenetek e példájára:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Válassza a **Tesztútvonal lehetőséget.** Ha a teszt sikeres, az "Az üzenet megfelelt a lekérdezésnek" üzenet jelenik meg.

14. Kattintson a **Mentés** gombra.

#### <a name="update-turbofandevicetostorage-route"></a>TurbofanDeviceToStorage útvonal frissítése

Nem szeretnénk az új előrejelzési adatokat a régi tárolási helyre irányítani, ezért frissítse az útvonalat, hogy megakadályozza azt.

1. Az IoT Hub **üzenetút-elvezető** lapján válassza az **Útvonalak** lapot.

2. Válassza ki **turbofanDeviceDataToStorage**, vagy bármilyen nevet adott a kezdeti eszköz adatútvonal.

3. Az útválasztási lekérdezés frissítése

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Bontsa ki a **Teszt** szakaszt, majd az **Üzenet törzsszakaszt.** Cserélje le az üzenetet a várt üzenetek e példájára:

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

5. Válassza a **Tesztútvonal lehetőséget.** Ha a teszt sikeres, az "Az üzenet megfelelt a lekérdezésnek" üzenet jelenik meg.

6. Kattintson a **Mentés** gombra.

### <a name="configure-file-upload"></a>Fájlfeltöltés konfigurálása

Konfigurálja az IoT Hub fájlfeltöltési funkcióját, hogy a fájlíró modul fájlokat tölthet fel a tárolóba.

1. Az IoT Hub bal oldali navigátorában válassza a **Fájlfeltöltés**lehetőséget.

2. Válassza az **Azure Storage Container lehetőséget.**

3. Válassza ki a tárfiókot a listából.

4. Jelölje ki a **uploadturbofanfiles tárolót,** és kattintson **a Kijelölés gombra.**

5. Kattintson a **Mentés** gombra. A portál értesíti, ha a mentés befejeződött.

> [!Note]
> Nem kapcsoljuk be az oktatóanyag feltöltési értesítését, de a [fájlfeltöltési értesítés fogadása](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) a fájlfeltöltési értesítés kezelésével kapcsolatos részletekért olvassa el a fájlfeltöltési értesítés kezelését.

## <a name="build-publish-and-deploy-modules"></a>Modulok létrehozása, közzététele és üzembe helyezése

Most, hogy elhajtottuk a konfigurációs módosításokat, készen állunk a lemezképek létrehozásához és az Azure-tároló beállításjegyzékében való közzétételére. A buildfolyamat a deployment.template.json fájlt használja annak meghatározásához, hogy mely modulokat kell kiépíteni. Az egyes modulok beállításai, beleértve a verziót is, a module.json fájlban találhatók a modul mappában. A buildfolyamat először egy Docker-buildet futtat a Docker-fájlokon, amelyek megfelelnek a module.json fájlban található aktuális konfigurációnak egy lemezkép létrehozásához. Ezután közzéteszi a lemezképet a rendszerleíró adatbázisban a module.json fájl egy verziócímke megegyezik az egyik a module.json fájlt. Végül létrehoz egy konfiguráció-specifikus központi telepítési jegyzékfájl (például deployment.amd64.json), amelyet az IoT Edge-eszköz üzembe helyezünk. Az IoT Edge-eszköz beolvassa az információkat a központi telepítési jegyzékfájlból, és az utasítások alapján letölti a modulokat, konfigurálja az útvonalakat, és beállítja a kívánt tulajdonságokat. Ennek a telepítési módszernek két mellékhatása van, amelyeket tudnia kell:

* **Üzembe helyezési késés:** mivel az IoT Edge futásidejű fel kell ismernie a kívánt tulajdonságok változását, mielőtt újrakonfigurálna, a modulok üzembe helyezése után némi időt vehet igénybe, amíg a futásidejű felveszi őket, és elkezdi frissíteni az IoT Edge-eszközt.

* **A modulverziók számítanak:** ha egy modul tárolójának új verzióját teszi közzé a tároló beállításjegyzékében az előző modullal megegyező verziócímkék használatával, a futásidejű nem tölti le a modul új verzióját. Összehasonlítást végez a helyi lemezkép verziócímkéjével és a központi telepítési jegyzékből a kívánt lemezképpel. Ha ezek a verziók egyeznek, a futásidejű nem tesz semmit. Ezért fontos, hogy minden alkalommal, amikor új módosításokat kíván telepíteni, a modul verzióját is meg kell adni. A verzió növekményének módosításával a **verzió** tulajdonság a **tag** tulajdonság a module.json fájlt a modul módosítása. Ezután hozza létre és tegye közzé a modult.

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

### <a name="build-and-publish"></a>Összeállítás és közzététel

1. A fejlesztői virtuális gép Visual Studio-kódjában nyisson meg egy Visual Studio-kód terminálablakot, és jelentkezzen be a tároló beállításjegyzékébe.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. A Visual Studio-kódban kattintson a jobb gombbal a deployment.template.json elemre, és válassza **az IoT Edge-megoldás összeállítása és leküldése parancsot.**

### <a name="view-modules-in-the-registry"></a>Modulok megtekintése a beállításjegyzékben

Miután a build sikeresen befejeződött, az Azure Portal használatával áttekinthetjük a közzétett modulokat.

1. Az Azure Portalon keresse meg az Azure Machine Learning-munkaterületet, és kattintson a **registry**hivatkozásra.

    ![Navigálás a rendszerleíró adatbázisba a gépi tanulási szolgáltatás munkaterületéről](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. A rendszerleíró adatbázis oldali navigátorában válassza az **Adattárak**lehetőséget.

3. Ne feledje, hogy mindkét létrehozott modul, **az avrofilewriter** és **a turbofanrouter**tárolóként jelenik meg.

4. Válassza ki **a turbofanrouter,** és vegye figyelembe, hogy már megjelent egy kép címkézett 0.0.1-amd64.

   ![Tekintse meg az első címkézett változata turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Modulok üzembe helyezése Az IoT Edge-eszközre

A modulokat a megoldásunkban építettük és konfiguráltuk, most telepítjük a modulokat az IoT Edge-eszközre.

1. A Visual Studio-kódban kattintson a jobb gombbal a **cett.amd64.json** fájlra a konfigurációs mappában.

2. Válassza **a Központi telepítés létrehozása egyetlen eszközhöz**lehetőséget.

3. Válassza ki az IoT Edge eszközt, **az aaTurboFanEdgeDevice-t.**

4. Frissítse az Azure IoT Hub eszközök panelt a Visual Studio Kódkezelőben. Látnia kell, hogy a három új modul telepítve van, de még nem fut.

5. Frissítsen újra néhány perc múlva, és látni fogja, hogy a modulok futnak.

   ![Futó modulok megtekintése a Visual Studio-kódban](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> A modulok indítása és állandó futási állapotba kerülése több percig is eltarthat. Ez idő alatt előfordulhat, hogy a modulok elindulnak és leállnak, amikor megpróbálnak kapcsolatot létesíteni az IoT Edge hub modullal.

## <a name="diagnosing-failures"></a>Hibák diagnosztizálása

Ebben a szakaszban megosztunk néhány technikát annak megértéséhez, hogy mi romlott el egy modullal vagy modulokkal. Gyakran előfordul, hogy a hiba először a Visual Studio-kód ban található állapotból észlelhető.

### <a name="identify-failed-modules"></a>Sikertelen modulok azonosítása

* **Visual Studio kód:** Tekintse meg az Azure IoT Hub eszközök panelen. Ha a legtöbb modul futó állapotban van, de az egyik le van állítva, meg kell vizsgálnia, hogy a leállított modul tovább. Ha az összes modul hosszú ideig leállított állapotban van, az meghibásodást is jelezhet.

* **Azure-portál:** Az IoT hubra való navigálás a portálon, majd az eszköz részletes lapjának megkeresésével (az IoT Edge-ben, az eszközbe való részletezés) előfordulhat, hogy egy modul hibát jelentett, vagy soha nem jelentett semmit az IoT hubnak.

### <a name="diagnosing-from-the-device"></a>Diagnosztizálás a készülékről

Az IoT Edge-eszközbe való bejelentkezéssel számos információhoz férhet hozzá a modulok állapotáról. Az általunk használt fő mechanizmus a Docker-parancsok, amelyek lehetővé teszik az eszközön lévő tárolók és képek vizsgálatát.

1. Az összes futó tároló felsorolása. Azt várjuk, hogy egy tároló minden modul egy nevet, amely megfelel a modul. Ez a parancs a tároló pontos képét sorolja fel, beleértve a verziót is, így megfelelhet az elvárásainak. A képeket úgy is felsorolhatja, hogy a "képet" a "tároló" helyett a parancsban helyettesíti.

   ```bash
   sudo docker container ls
   ```

2. Szerezd meg a naplókat egy konténer. Ez a parancs a tárolóban lévő StdErr és StdOut írást adja ki. Ez a parancs olyan tárolóknál működik, amelyek valamilyen okból elkezdődtek, majd meghaltak. Az edgeAgent vagy edgeHub-tárolókkal történt események megértéséhez is hasznos.

   ```bash
   sudo docker container logs <container name>
   ```

3. Vizsgálja meg a tartályt. Ez a parancs rengeteg információt ad a képről. Az adatok szűrhetők attól függően, hogy mit keres. Például, ha meg szeretné nézni, hogy az avroFileWriter kötései helyesek-e, használhatja a következő parancsot:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Csatlakozzon egy futó tárolóhoz. Ez a parancs akkor lehet hasznos, ha a tárolót futás közben szeretné megvizsgálni:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehoztunk egy IoT Edge-megoldást a Visual Studio-kódban három modullal, egy osztályozóval, egy útválasztóval és egy fájlíróval/-feltöltővel. Úgy állítottuk be az útvonalakat, hogy a modulok kommunikálhassanak egymással a peremhálózati eszközön, módosítottuk a peremhálózati eszköz konfigurációját, és frissítettük a Dockerfiles-okat a függőségek telepítéséhez, és hozzáadjuk a kötési csatlakoztatásokat a modulok tárolóihoz. Ezután frissítettük az IoT Hub konfigurációját, hogy az üzeneteket típus alapján irányítsuk, és kezeljük a fájlfeltöltéseket. Minden nelkül üzembe helyeztük a modulokat az IoT Edge-eszközre, és biztosítottuk, hogy a modulok megfelelően fussanak.

További információ a következő oldalakon található:

* [Ismerje meg, hogyan telepíthet modulokat és hozhat létre útvonalakat az IoT Edge-ben](module-composition.md)
* [IoT-központ üzenet-útválasztásának lekérdezési szintaxisa](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub-üzenet-útválasztás: most az üzenettörzsen lévő útválasztással](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Fájlok feltöltése az IoT Hubbal](../iot-hub/iot-hub-devguide-file-upload.md)
* [Fájlok feltöltése az eszközről a felhőbe az IoT Hub segítségével](../iot-hub/iot-hub-python-python-file-upload.md)

Folytassa a következő cikkel az adatok küldésének megkezdéséhez és a megoldás működés közbeni megtekintéséhez.

> [!div class="nextstepaction"]
> [Adatok küldése transzparens átjárón keresztül](tutorial-machine-learning-edge-07-send-data-to-hub.md)
