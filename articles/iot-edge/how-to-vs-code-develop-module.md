---
title: Fejlesztés és hibakeresés modulok az Azure IoT Edge |} A Microsoft Docs
description: A Visual Studio Code használatával fejlesztése, elkészítéséhez és az Azure IoT Edge használatával egy modul hibakeresése C#, Python, Node.js, Java vagy C
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 02/20/2019
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9a7d83070caad86d7d66b62a9592dbb1e0017f97
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884239"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Fejlesztés és hibakeresés modulok az Azure IoT Edge-hez a Visual Studio Code használatával

Az Azure IoT Edge kapcsolhatja az üzleti logikára modulokat. Ez a cikk bemutatja a Visual Studio Code használata a fő eszköz fejlesztésről és hibakeresésről modulok.

## <a name="prerequisites"></a>Előfeltételek

Egy számítógép vagy a Windows, macOS vagy Linux rendszerű futtató fejlesztői gépen virtuális gépet is használhat. Az IoT Edge-eszközöket egy másik fizikai eszköz lehet.

Az írt modulok C#, vagy a Node.js, Java, hibakeresése a Visual Studio Code-modulja két módja van: Csatolhat egy folyamatot egy modul tárolóban, vagy indítsa el a modul kód hibakeresési módban. Python vagy C nyelven írt modulokhoz akkor is csak hibakereséséhez Linux amd64 tárolókban folyamatok csatolásával.

> [!TIP]
> Ha nem ismeri a Visual Studio Code hibakeresési képességeit, olvassa el [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Telepítés [Visual Studio Code](https://code.visualstudio.com/) első majd adja hozzá a következő bővítményeket:

- [Az Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- A Visual Studio fájlformátum(ok) kiterjesztését vagy kiterjesztéseit adott nyelven fejleszt, a:
  - C#, beleértve az Azure Functions: [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  - Python: [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [A Visual Studio Code-Java-bővítménycsomag](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Emellett szüksége annak érdekében, hogy a modul fejlesztése néhány további, a nyelvspecifikus eszközök telepítése:

- C#, beleértve az Azure Functions: [.NET Core SDK-t 2.1-es verziója](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) és [Pip](https://pip.pypa.io/en/stable/installing/#installation) (általában a Python-telepítés részét képező) Python-csomagok telepítéséhez. Ha a Pip telepítve, telepítse a **Cookiecutteru** csomag a következő paranccsal:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

- Node.js: [Node.js](https://nodejs.org). Is szeretné telepíteni [Yeoman](https://www.npmjs.com/package/yo) és a [Azure IoT Edge Node.js modult generátor](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) és [Maven](https://maven.apache.org/). Kell [állítsa be a `JAVA_HOME` környezeti változó](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) , hogy a JDK telepítési mutasson.

Annak érdekében, hogy hozhat létre, és a modul rendszerképének üzembe helyezéséhez, szüksége van a modul rendszerképének és a egy tároló-beállításjegyzéket, amely tárolja a modul rendszerképének létrehozása Docker:

- [A docker Community Edition](https://docs.docker.com/install/) a fejlesztői gépen.

- [Az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Egy helyi Docker-beállításjegyzék prototípus és a egy felhőbeli beállításjegyzék helyett tesztelési célokra használható.

Kivéve, ha a modul C nyelven fejleszt, emellett a Python-alapú [Azure IoT EdgeHub fejlesztőeszközt](https://pypi.org/project/iotedgehubdev/) annak érdekében, hogy a helyi fejlesztési környezet beállítása hibakeresése, futtassa, és az IoT Edge-megoldás teszteléséhez. Ha ezt még nem tette meg, telepítse a [Python (2.7-es és 3.6) és a Pip](https://www.python.org/) , majd telepítse **iotedgehubdev** futtassa ezt a parancsot a terminálban.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> A modul egy eszközön teszteléséhez kell legalább egy IoT Edge-eszköz active IoT hub. Az IoT Edge-eszközöket a számítógép használja, kövesse a rövid útmutatóban a [Linux](quickstart-linux.md) vagy [Windows](quickstart.md). IoT Edge-démon futtatja a fejlesztői gépen, ha szüksége lehet, hogy leállította EdgeHub és EdgeAgent helyez át a következő lépéssel.

## <a name="create-a-new-solution-template"></a>Új megoldássablon létrehozásához

A következő lépések bemutatják, hogyan hozhat létre egy IoT Edge-modul az előnyben részesített fejlesztési nyelven (többek között az Azure Functions-ben írt C#) Visual Studio Code és az Azure IoT-eszközök használatával. Indítsa el a megoldás létrehozásával és majd létrehozni ebben a megoldásban az első modult. Egyes megoldások a több modul is tartalmazhat.

1. Válassza ki **nézet** > **paletta parancs**.

1. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**.

   ![Új IoT Edge-megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

1. Keresse meg a mappát, ahová szeretné az új megoldás létrehozása, és válassza ki a **mappa kiválasztása**.

1. Adja meg a megoldás nevét.

1. Válassza ki az első modulban, a megoldás lehet az előnyben részesített fejlesztési nyelv modul sablonját.

1. Adja meg a modul nevét. Válassza ki, amely a tárolóregisztrációs adatbázis egyedi nevét.

1. Adja meg a lemezképtárban a modul nevét. A Visual Studio Code autopopulates a modul neve a **localhost:5000 / < a modulnév\>**. Cserélje le a saját beállításjegyzék-információkat. Ha használja a helyi Docker-beállításjegyzék tesztelési, majd **localhost** nem okoz gondot. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló néz ***\<beállításjegyzék neve\>*. azurecr.io**. Csak cserélje le a **localhost:5000** része a karakterláncot, hogy néz ki a végső eredményt **\<* beállításjegyzék neve*\>.azurecr.io/* \<a modulnév\>x.

   ![Docker-rendszerkép adattárának megadása](./media/how-to-develop-csharp-module/repository.png)

A Visual Studio Code-ot tart a megadott információt, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakban.

Nincsenek a megoldáson belül a négy elemek:

- A **.vscode** mappa hibakeresési konfigurációkat tartalmaz.

- A **modulok** a mappában megtalálja az egyes modulok almappát. Ezen a ponton csak eggyel rendelkezik. A paranccsal a parancskatalógus több adhat hozzá, de **Azure IoT Edge: IoT Edge-modul hozzáadása**.

- Egy **.env** sorolja a környezeti változók. Ha az Azure Container Registry a beállításjegyzékbe, lesz egy Azure Container Registry-felhasználónév és jelszó szerepel.

  > [!NOTE]
  > A környezet fájl csak akkor jön létre, ha a modul adja meg egy lemezképtárban. Ha korábban elfogadta a localhost alapértelmezett tesztelése és hibakeresése helyileg, majd, nem kell környezeti változók deklarálása.

- A **deployment.template.json** sorolja az új modul és a egy minta **tempSensor** modul, amely szimulálja az adatokat, teszteléshez használható. Hogyan az üzembe helyezés jegyzékfájlok munkahelyi kapcsolatos további információkért lásd: [megtudhatja, hogyan telepítési jegyzékek használatával hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat](module-composition.md).

## <a name="add-additional-modules"></a>További modulok hozzáadása

Adjon hozzá további modulok a megoldáshoz, futtassa a parancsot **Azure IoT Edge: IoT Edge-modul hozzáadása** a parancskatalógus. Még jobb gombbal a **modulok** mappa vagy a `deployment.template.json` fájlt a Visual Studio Code Explorerben nézetben, és válassza ki **IoT Edge-modul hozzáadása**.

## <a name="develop-your-module"></a>A modul fejlesztése

A megoldás az alapértelmezett modul kódja megtalálható a következő helyen:

- Azure-függvény (C#): **modulok >  *&lt;a modulnév&gt;* > *&lt;a modulnév&gt;*.cs**
- C#: **modulok > *&lt;a modulnév&gt;* > Program.cs**
- Python: **modulok > *&lt;a modulnév&gt;* > main.py**
- NODE.js: **modulok > *&lt;a modulnév&gt;* > app.js**
- Java: **modulok > *&lt;a modulnév&gt;* > src > fő > java > com > edgemodulemodules > App.java**
- C: **modulok > *&lt;a modulnév&gt;* > main.c**

A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul csak igénybe vehet a bemeneti forrásból (ami jelen esetben a tempSensor modul, amely szimulálja az adatokat), és átadhatja azt az IoT hubhoz való használatra készült.

Ha készen áll a saját kód a sablon testreszabásához, használja a [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) hozhat létre a modulok a kulcsot kell IoT-megoldások, például a biztonság, a kezelés és a megbízhatóság címmel.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Egy tároló nélkül a modul hibakeresése (C#, Node.js, Java)

Ha a saját fejlesztésű C#, vagy a Node.js, Java, a modul használatát igényli egy **ModuleClient** objektum az alapértelmezett modul kódban, hogy azt start, futtassa, és továbbítani az üzeneteket. Emellett fogja használni az alapértelmezett bemeneti csatorna **input1** beavatkozásra, amikor a modul üzeneteket fogad.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge-szimulátort az IoT Edge-megoldás beállítása

A fejlesztői gépen megkezdheti az IoT Edge szimulátor helyett az IoT Edge biztonsági démon telepítésével, hogy az IoT Edge-megoldást futtathatja.

1. A device Explorerben bal oldalán kattintson a jobb gombbal az IoT Edge-eszköz azonosítója, és válassza **beállítása IoT Edge-szimulátor** a szimulátor indításához az eszköz kapcsolati karakterlánccal.
1. Láthatja, hogy az IoT Edge-szimulátor sikeresen be lett állítva a folyamat részletei az integrált terminálon elolvasásával.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>IoT Edge-szimulátor egy modul alkalmazás beállítása

Állítsa be, és a szimulátor indításához futtassa a parancsot **Azure IoT Edge: Indítsa el az IoT Edge hubot szimulátor egyetlen modul** a Visual Studio Code parancs palettáról. Amikor a rendszer kéri, használja az értéket **input1** alapértelmezett modul kódja (vagy a megfelelő érték a code-ból), a bemeneti nevet az alkalmazásnak. A parancs eseményindítók a **iotedgehubdev** CLI, majd elindítja az IoT Edge-szimulátor és a egy tesztelési segédprogram modul tárolót. A kimenetek alább az integrált terminálon láthatja, ha a szimulátor lett egy modul módban sikeresen elindult. Emellett megtekintheti a `curl` parancs segítségével keresztül küldött. Erre később még szüksége lesz.

   ![IoT Edge-szimulátor egy modul alkalmazás beállítása](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   A modul futási állapotának megtekintéséhez használhatja a Visual Studio Code-ban a Docker-Tallózó nézet.

   ![Simulátor modul állapota](media/how-to-develop-csharp-module/simulator-status.png)

   A **edgeHubDev** tároló-e a fő elemei a helyi IoT Edge-szimulátort. A fejlesztési számítógépén az IoT Edge biztonsági démon nélkül futtathatja és a natív modul vagy a modul tárolók környezeti beállítások biztosít. A **bemeneti** tároló híd üzenetek könnyíti meg a célként megadott bemeneti csatorna a modul a REST API-kat tesz elérhetővé.

### <a name="debug-module-in-launch-mode"></a>Indítási módban modul hibakeresése

1. Előkészítik a környezetét a hibakeresés, a fejlesztői nyelvek követelményeinek megfelelően, állítson be egy töréspontot a modult, és válassza ki a hibakeresési konfigurációt használni:
   - **C#**
     - A Visual Studio Code integrált terminálon lépjen be a ***&lt;a modulnév&gt;*** mappát, és futtassa a következő parancsot a .NET Core-alkalmazás létrehozásához.

       ```cmd
       dotnet build
       ```

     - Nyissa meg a fájlt `Program.cs` , és adjon hozzá egy töréspontot.

     - Keresse meg a Visual Studio Code hibakereső nézet kiválasztásával **Nézet > Debug**. Válassza ki a hibakeresési konfigurációt  ***&lt;a modulnév&gt;* helyi hibakeresés (.NET Core)** a legördülő listából.

        > [!NOTE]
        > Ha a .NET Core `TargetFramework` replikája nem konzisztens a program elérési útja a `launch.json`, manuálisan frissítheti a program elérési útja a kell `launch.json` megfelelően a `TargetFramework` úgy, hogy a Visual Studio Code sikeresen indíthatják el ezt a .csproj fájlban a program.

   - **Node.js**
     - A Visual Studio Code integrált terminálon lépjen be a ***&lt;a modulnév&gt;*** mappát, és futtassa a következő parancsot a Node-csomagok telepítéséhez

       ```cmd
       npm install
       ```

     - Nyissa meg a fájlt `app.js` , és adjon hozzá egy töréspontot.

     - Keresse meg a Visual Studio Code hibakereső nézet kiválasztásával **Nézet > Debug**. Válassza ki a hibakeresési konfigurációt  ***&lt;a modulnév&gt;* helyi hibakeresés (Node.js)** a legördülő listából.
   - **Java**
     - Nyissa meg a fájlt `App.java` , és adjon hozzá egy töréspontot.

     - Keresse meg a Visual Studio Code hibakereső nézet kiválasztásával **Nézet > Debug**. Válassza ki a hibakeresési konfigurációt  ***&lt;a modulnév&gt;* helyi hibakeresés (Java)** a legördülő listából.

1. Kattintson a **Start Debugging** vagy nyomja le az **F5** a hibakeresési munkamenet elindításához.

1. A Visual Studio Code integrált terminálon futtassa a következő parancsot, küldhet egy **Hello World** üzenet a modulnak. Ez az IoT Edge-szimulátor beállításakor az előző lépésekben látható a parancsot.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows használja, ha van gondoskodik róla, hogy a rendszerhéj a Visual Studio Code integrált terminál **a Git Bash** vagy **WSL Bash**. Nem lehet futtatni a `curl` parancsot a PowerShell vagy az parancssor használatával.
   > [!TIP]
   > Is [PostMan](https://www.getpostman.com/) vagy más API-eszközök helyett keresztül üzenetek küldéséhez `curl`.

1. A Visual Studio Code Debug nézetben láthatja a változókat a bal oldali panelen.

1. A hibakeresési munkamenet leállításához válassza ki a Stop gombra vagy nyomja meg **Shift + F5**, majd futtassa a **Azure IoT Edge: Állítsa le az IoT Edge-szimulátor** a parancskatalógus állítsa le a szimulátor és karbantartása.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>A hibakeresési mód csatolása az IoT Edge-szimulátor (C#, Node.js, Java, az Azure Functions)

Az alapértelmezés szerinti megoldás két modult tartalmaz, egy egy szimulált hőmérsékleti érzékelő modul a másik pedig a függőleges vonal modul. A szimulált hőmérséklet-érzékelő a függőleges vonal modul üzeneteket küld, és az IoT Hub az üzenetek vannak eredményez. A modul mappában létrehozott nincsenek különböző tároló esetében több Docker-fájlok. E célból kiterjesztésű fájlokat használjon **.debug** hozhat létre a teszteléshez modul.

Jelenleg a hibakeresés csatolása módban támogatott csak az alábbiak szerint:

- C#modulok, beleértve az Azure Functions szolgáltatáshoz, a Linux-tárolók amd64 hibakeresés támogatása
- Linux-amd64 arm32v7 tárolók és a Windows-amd64 tárolók hibakeresés a node.js modulok támogatása
- A Linux-tárolók amd64 és arm32v7 hibakeresés Java-modulok támogatása

> [!TIP]
> Átválthat az alapértelmezett platform beállításai között az IoT Edge-megoldás a Visual Studio Code állapotsor elemére kattintva.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge-szimulátort az IoT Edge-megoldás beállítása

A fejlesztői gépen akkor kezdhet egy IoT Edge-szimulátor helyett az IoT Edge biztonsági démon telepítésével, hogy az IoT Edge-megoldást futtathatja.

1. A device Explorerben bal oldalán kattintson a jobb gombbal az IoT Edge-eszköz azonosítója, és válassza **beállítása IoT Edge-szimulátor** a szimulátor indításához az eszköz kapcsolati karakterlánccal.

1. Láthatja, hogy az IoT Edge-szimulátor sikeresen be lett állítva a folyamat részletei az integrált terminálon elolvasásával.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Létrehozásához és futtatásához hibakereséshez és a hibakeresési tároló a csatolása mód

1. Nyissa meg a modul fájlt (`Program.cs`, `app.js`, `App.java`, vagy `<your module name>.cs`), és adjon hozzá egy töréspontot.

1. A Visual Studio Code Explorerben nézetben kattintson a jobb gombbal a `deployment.debug.template.json` megoldás fájlt, és válassza ki **készítése és futtatása az IoT Edge-szimulátorban történő megoldás**. Megnézheti a modul összes tároló-naplók ugyanabban az ablakban. A Docker nézetre, és tekintse meg a tároló állapota is elérheti.

   ![Változók Watch](media/how-to-develop-csharp-module/view-log.png)

1. Nyissa meg a Visual Studio Code Debug nézetet, és válassza ki a hibakeresési konfigurációs fájlt a modul. A hibakeresési beállítás neve legyen hasonló  ***&lt;a modulnév&gt;* távoli hibakeresése**

1. Válassza ki **Start Debugging** vagy nyomja le az **F5**. Jelölje be a csatlakoztatni kívánt folyamatot.

1. A Visual Studio Code hibakereső nézet láthatja a változókat a bal oldali panelen.

1. A hibakeresési munkamenet leállításához először válassza a Leállítás gombra vagy nyomja meg **Shift + F5**, majd válassza ki **Azure IoT Edge: Állítsa le az IoT Edge-szimulátor** a parancskatalógus.

> [!NOTE]
> Az előző példa bemutatja, hogyan lehet hibákat keresni a tárolók IoT Edge-modulok. Ez a modul tárolóhoz adni elérhetővé tett port `createOptions` beállításait. Miután elvégezte a modulok hibakeresés, javasoljuk, hogy eltávolítja ezeket a közzétett éles használatra kész IoT Edge-modulok portokat.
>
> Írt modulok C#, beleértve az Azure Functions, ebben a példában, a hibakeresési verzió alapján `Dockerfile.amd64.debug`, amely tartalmazza a .NET Core parancssori hibakereső (VSDBG) a tároló rendszerképének összeállítása során. Miután hibakeresése a C# modulok, azt javasoljuk, hogy közvetlenül használja a docker-fájl nélkül VSDBG éles használatra kész IoT Edge-modulok.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet-modul hibakeresése

Minden modul mappában nincsenek különböző tároló esetében több Docker-fájlok. E célból kiterjesztésű fájlokat használjon **.debug** hozhat létre a teszteléshez modul.

Ha hibakeresési modulok ezt a módszert használja, a modulok futnak az IoT Edge-futtatókörnyezet felett. Az IoT Edge-eszköz és a Visual Studio Code ugyanarra a gépre, vagy több általában a fejlesztési számítógépén a Visual Studio Code és az IoT Edge-futtatókörnyezet és a modulok egy másik fizikai számítógépen futnak. Annak érdekében, hogy hibakeresése a Visual Studio Code-ból, tegye a következőket:

- Állítsa be az IoT Edge-eszközt, és hozzon létre az IoT Edge szerkezettel rendelkező a **.debug** docker-fájlt, majd telepítenie kell az IoT Edge-eszköz.
- Tegye elérhetővé az IP és port, a modul úgy, hogy a hibakeresőt csatolható.
- Frissítés a `launch.json` úgy, hogy a Visual Studio Code csatlakoztathat a folyamat a tárolóban, a távoli számítógépen. Ez a fájl a `.vscode` mappát a munkaterületet, és a frissítések minden alkalommal, amikor hozzáadhat egy új modul, amely támogatja a hibakeresés.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Buildelése és üzembe helyezése a modul az IoT Edge-eszköz

1. A Visual Studio Code-ban nyissa meg a `deployment.debug.template.json` fájl, amely tartalmazza a hibakeresési verzió, a modul képek a megfelelő `createOptions` beállított értékeket.

1. Ha a modul Python nyelven fejleszt, kövesse az alábbi lépéseket a folytatás előtt:
   - Nyissa meg a fájlt `main.py` , és adja hozzá ezt a kódot az importálási szakasznak után:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - A következő egyetlen sor kód hozzáadása a visszahívás, amelyen hibakeresést végez:

      ```python
      ptvsd.break_into_debugger()
      ```

     Ha amelyen hibakeresést végez, például a `receive_message_callback` metódus, lenne beszúrása a sor kódot alább látható módon:

      ```python
      def receive_message_callback(message, hubManager):
          ptvsd.break_into_debugger()
          global RECEIVE_CALLBACKS
          message_buffer = message.get_bytearray()
          size = len(message_buffer)
          print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode ('utf-8'), size) )
          map_properties = message.properties()
          key_value_pair = map_properties.get_internals()
          print ( "    Properties: %s" % key_value_pair )
          RECEIVE_CALLBACKS += 1
          print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
          hubManager.forward_event_to_output("output1", message, 0)
          return IoTHubMessageDispositionResult.ACCEPTED
      ```

1. A Visual Studio Code parancskatalógus:
   1. Futtassa a parancsot **Azure IoT Edge: A build és a leküldéses IoT Edge megoldás**.

   1. Válassza ki a `deployment.debug.template.json` fájlt a megoldáshoz.

1. Az a **Azure IoT Hub-eszközök** szakasz a Visual Studio Code Explorerben nézet:
   1. Kattintson a jobb gombbal egy IoT Edge-eszköz azonosítója, majd **központi telepítés létrehozása egyetlen eszköz**.

      > [!TIP]
      > Győződjön meg arról, hogy úgy döntött, az eszköz IoT Edge-eszköz, válassza azt, bontsa ki a modulok listájához, és meglétének ellenőrzése **$edgeHub** és **$edgeAgent**. Minden IoT Edge-eszköz az alábbi két modulokat tartalmaz.

   1. Keresse meg a megoldás **config** mappáját, válassza ki a `deployment.debug.amd64.json` fájlt, és válassza ki **kiválasztása peremhálózati üzembe helyezési Manifest**.

Látni fogja az üzembe helyezés sikeresen létrehozott egy központi telepítési azonosítót, az integrált terminálon.

A tároló állapotának ellenőrzéséhez futtassa a `docker ps` parancsot a terminálon. Ha ugyanazon a számítógépen futtatja a Visual Studio Code és az IoT Edge-futtatókörnyezet, az állapota a Visual Studio Code Docker nézetben is ellenőrizheti.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Tegye elérhetővé az IP és port, a modul a hibakereső

Kihagyhatja, ez a szakasz Ha ugyanarra a gépre, a Visual Studio Code-ban futtatja a modulok localhost csatlakoztatása a tárolóhoz, és már rendelkezik a megfelelő port-beállításokat használja a **.debug** docker-fájlban, a modul a tároló `createOptions` beállításait, és `launch.json` fájlt. Ha a modulok és a Visual Studio Code és külön gépeken futnak, kövesse a fejlesztői nyelvek esetében.

- **C#, beleértve az Azure Functions**

  [Konfigurálja az SSH-csatorna a fejlesztési számítógép és az IoT Edge-eszköz](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) majd szerkesztése `launch.json` fájlt csatolni.

- **Node.js**

  - Győződjön meg arról, hogy a modul indítja el a gépen futó és készen áll a ladicí programy csatolni, és hogy portot 9229 elérhető külsőleg. Ezt ellenőrizheti megnyitásával `http://<target-machine-IP>:9229/json` a hibakeresőt gépen. Az URL-címet meg kell jelennie indítja el a Node.js-modullal kapcsolatos információt.
  
  - A fejlesztői gépén, nyissa meg a Visual Studio Code-ot, és szerkessze `launch.json` úgy, hogy a cím értékét a  ***&lt;a modulnév&gt;* távoli hibakeresés (Node.js)** profil (vagy  ***&lt;a modulnév&gt;* távoli hibakeresés (Node.js Windows-tárolóban)** profilt, ha a modul egy Windows-tárolót futtató) az IP-címe van a a gép replikaszámát.

- **Java**

  - A gépre történő futtatásával hibakereséséhez SSH-alagutat hozhat létre `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`.
  
  - A fejlesztői gépén, nyissa meg a Visual Studio Code-ot, és módosítsa a  ***&lt;a modulnév&gt;* távoli hibakeresés (Java)** profilban, az `launch.json` csatlakoztathat, hogy a a célszámítógépen. További szerkesztésével `launch.json` és hibakeresése a Visual Studio Code-ban a Java ismertető szakaszban [konfigurálása a hibakeresőt](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Győződjön meg arról, hogy a gépen történő hibakereséséhez 5678 port nyitott és elérhető-e.

  - A kód `ptvsd.enable_attach(('0.0.0.0', 5678))` korábban illesztett `main.py`, módosítsa **0.0.0.0** indítja el a gép IP-címet. Hozhat létre, küldje le és telepítse újra az IoT Edge-modul.

  - A fejlesztői gépén, nyissa meg a Visual Studio Code-ot, és szerkessze `launch.json` úgy, hogy a `host` értékét a  ***&lt;a modulnév&gt;* távoli hibakeresés (Python)** profil használ az IP-cím helyett a célként megadott gép `localhost`.

### <a name="debug-your-module"></a>A modul hibakeresése

1. A Visual Studio Code Debug nézetben válassza ki a hibakeresési konfigurációs fájlt a modul. A hibakeresési beállítás neve legyen hasonló  ***&lt;a modulnév&gt;* távoli hibakeresése**

1. Nyissa meg a kívánt modulfájlt fejlesztési nyelvű, és adjon hozzá egy töréspontot:

   - **Azure-függvény (C#)**: Adja hozzá a fájlhoz a töréspont `<your module name>.cs`.
   - **C#**: Adja hozzá a fájlhoz a töréspont `Program.cs`.
   - **Node.js**: Adja hozzá a fájlhoz a töréspont `app.js`.
   - **Java**: Adja hozzá a fájlhoz a töréspont `App.java`.
   - **Python**: Adja hozzá a fájlhoz a töréspont `main.py`a visszahívási metódus, amelyikhez hozzáadta a `ptvsd.break_into_debugger()` sor.
   - **C**: Adja hozzá a fájlhoz a töréspont `main.c`.

1. Válassza ki **Start Debugging** , vagy válasszon **F5**. Jelölje be a csatlakoztatni kívánt folyamatot.

1. A Visual Studio Code Debug nézetben láthatja a változókat a bal oldali panelen.

> [!NOTE]
> Az előző példa bemutatja, hogyan lehet hibákat keresni a tárolók IoT Edge-modulok. Ez a modul tárolóhoz adni elérhetővé tett port `createOptions` beállításait. Miután elvégezte a modulok hibakeresés, javasoljuk, hogy eltávolítja ezeket a közzétett éles használatra kész IoT Edge-modulok portokat.

## <a name="build-and-debug-a-module-remotely"></a>Hozhat létre, és a egy modul távoli hibakeresés

Az SSH-kapcsolatok támogatásához a Docker és a Moby motorok végrehajtott legutóbbi módosításoknak, és a egy új beállítás található, amely lehetővé teszi a környezeti beállítások a Visual Studio Code parancskatalógus, és az Azure IoT Edge terminálok injektálása Azure IoT-eszközök mostantól hozhat létre és hibakeresés modulok távoli eszközökön.

Ez [IoT fejlesztői blogbejegyzés](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) további információk és részletes útmutatást.

## <a name="next-steps"></a>További lépések

A modul létrehozása, után megtudhatja, hogyan [üzembe helyezése a Visual Studio Code-ból az Azure IoT Edge-modulok](how-to-deploy-modules-vscode.md).

Az IoT Edge-eszközökön, a modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).
