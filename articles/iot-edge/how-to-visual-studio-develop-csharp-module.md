---
title: Fejlesztés és hibakeresés C# modulok a Visual Studio – Azure IoT Edge |} A Microsoft Docs
description: A Visual Studio 2017 használatával fejlesztése, és C# modul hibakeresése az Azure IoT Edge-hez
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9a6c892a71c452a0c6c0dcd43509e345280a810e
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054923"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Fejlesztés és az Azure IoT Edge (előzetes verzió) C# modul hibakeresése a Visual Studio 2017 használatával

Az Azure IoT Edge kapcsolhatja az üzleti logikára modulokat. Ez a cikk bemutatja, hogyan a fő eszköz fejlesztéséhez és a C# modul hibakeresése a Visual Studio 2017 adatokként.

Az Azure IoT Edge Tools for Visual Studio az alábbi előnyöket nyújtja:

- Létrehozása, szerkesztése, létrehozása, futtatása és az Azure IoT Edge-megoldások és a modulok a helyi fejlesztési számítógép hibakeresési.
- Az Azure IoT Edge-megoldás üzembe helyezése az Azure IoT Hub-n keresztül az Azure IoT Edge-eszköz.
- Az Azure IoT-modulok a C#-kód során a Visual Studio fejlesztési előnyeit a teljes.
- Kezelheti az Azure IoT Edge-eszközök és a modulok felhasználói felületen keresztül.

Ez a cikk bemutatja, hogyan fejleszthet a C# IoT Edge-modulok az Azure IoT Edge Tools for Visual Studio 2017 használatával. Azt is megtudhatja, hogyan a projekt telepítése az Azure IoT Edge-eszköz.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy egy számítógép vagy a Windows rendszerű fejlesztői gépen, virtuális gép használja. Az IoT Edge-eszközt egy másik fizikai eszköz lehet.

Mivel ez a cikk a Visual Studio 2017, a fő fejlesztési eszközt használ, telepítse a Visual Studióban. Győződjön meg arról is, és **Azure development workload munkafolyamatának** a Visual Studio 2017 telepítése. Is [módosítása a Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) , és adja hozzá az Azure-fejlesztési számítási feladatot.

Miután készen áll a Visual Studio 2017, emellett kell a következő eszközök és -összetevők:

- Töltse le és telepítse [(előzetes verzió) az Azure IoT Edge bővítmény](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) egy IoT Edge-projekt létrehozása a Visual Studio 2017 a Visual Studio marketplace-ről.

- Töltse le és telepítse [a Docker Community Edition](https://docs.docker.com/install/) a fejlesztői gépen való létrehozásához és futtatásához a modul rendszerképeit. Állítsa be a Docker CE futtatását Linux-tároló mód vagy a Windows tároló módban kell.

- A helyi fejlesztési környezet beállítása hibakeresése, futtatja, és tesztelheti az IoT Edge-megoldás telepítésével a [Azure IoT EdgeHub fejlesztőeszközt](https://pypi.org/project/iotedgehubdev/). Telepítés [Python (2.7-es és 3.6) és a Pip](https://www.python.org/) , majd telepítse a **iotedgehubdev** csomag a következő parancs futtatásával a terminálban. Ellenőrizze, hogy az Azure IoT EdgeHub fejlesztési eszköz verziószáma nagyobb, mint 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Egy helyi Docker-beállításjegyzék prototípus és a egy felhőbeli beállításjegyzék helyett tesztelési célokra használható.

- A modul egy eszközön teszteléséhez kell legalább egy IoT Edge-eszköz active IoT hub. Az IoT Edge-eszközöket a számítógép használja, kövesse a rövid útmutatóban a [Linux](quickstart-linux.md) vagy [Windows](quickstart.md). Ha a fejlesztői gépen futtatja az IoT Edge-démont, szüksége lehet EdgeHub és EdgeAgent állítsa le a Visual Studióban fejlesztés megkezdése előtt.

### <a name="check-your-tools-version"></a>Az eszközök verziójának ellenőrzéséhez

1. Az a **eszközök** menüjében válassza **bővítmények és frissítések**. Bontsa ki a **telepített > eszközök** és annak **Azure IoT Edge-eszközök** és **for Visual Studio Cloud Explorer**.

1. Megjegyzés: a telepített verzió. Ez a verzió a legújabb verzióra a Visual Studio-piactér összehasonlíthatja ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge))

1. Ha a verzió régebbi, mint mi érhető el a Visual Studio-piactér, frissítse az eszközök a Visual Studióban, a következő szakaszban látható módon.

### <a name="update-your-tools"></a>Az eszközök frissítése

1. Az a **bővítmények és frissítések** párbeszédpanelen bontsa ki a **frissítések > Visual Studio-piactér**, jelölje be **Azure IoT Edge-eszközök** vagy **Cloud Explorer vizualizációhoz Studio** válassza **frissítés**.

1. Után az eszközök a frissítés letöltését követően zárja be a Visual Studióban az eszközök frissítése a VSIX telepítővel eseményindítóra.

1. Válassza ki a telepítő **OK** elindítani, majd **módosítás** az eszközök frissítése.

1. A frissítés befejezése után jelölje ki a **Bezárás** , és indítsa újra a Visual Studióban.

### <a name="create-an-azure-iot-edge-project"></a>Az Azure IoT Edge-projekt létrehozása

Az Azure IoT Edge webesprojekt-sablon a Visual Studio létrehoz egy projektet, az Azure IoT Hub az Azure IoT Edge-eszközökre telepíthető. Először hozzon létre egy Azure IoT Edge-megoldás, és ezután az első C# modul létrehozása ebben a megoldásban. Egyes IoT Edge-megoldások tartalmazhatnak egynél több modul.

1. A Visual Studio **Fájl** menüjében válassza az **Új** > **Projekt** lehetőséget.

1. Az a **új projekt** párbeszédpanelen válassza **telepített**, bontsa ki a **Visual C# > felhő**, jelölje be **Azure IoT Edge**, adjon meg egy nevet a projektre, és adja meg a helyet, és válassza ki **OK**. Az alapértelmezett projekt neve **AzureIoTEdgeApp1**.

   ![Új projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. Az a **IoT Edge-modul konfigurációjának** ablakban válassza  **C# modul** , és adja meg a modul nevét és a modul lemezképtárban. A Visual Studio autopopulates a modul neve a **localhost:5000 / < a modulnév\>**. Cserélje le a saját beállításjegyzék-információkat. Ha használja a helyi Docker-beállításjegyzék tesztelési, majd **localhost** nem okoz gondot. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló néz ***\<beállításjegyzék neve\>*. azurecr.io**. Csak cserélje le a **localhost:5000** része a karakterláncot, hogy néz ki a végső eredményt **\<* beállításjegyzék neve*\>.azurecr.io/* \<a modulnév\>x. Az alapértelmezett modul neve **IoTEdgeModule1**

1. Válassza ki **OK** , az Azure IoT Edge-projektet hoz létre egy C# modul.

Most már rendelkezik egy **AzureIoTEdgeApp1** projekt és a egy **IoTEdgeModule1** projektet a megoldásban. A **AzureIoTEdgeApp1** projekt rendelkezik egy **deployment.template.json** fájlt. Ez a fájl határozza meg a kívánt létrehozása és üzembe helyezése az IoT Edge-megoldás modulok, és a modulok közötti útvonalak is meghatározza. A alapértelmezett megoldáshoz tartozik egy **tempSensor** modul és a egy **IoTEdgeModule1** modul. A **tempSensor** modul a szimulált adatokat állít elő a **IoTEdgeModule1** modult, miközben az alapértelmezett kód a a **IoTEdgeModule1** modul közvetlenül kapott csövek Azure IoT Hub üzeneteket.

A **IoTEdgeModule1** projekt egy 2.1 a .NET Core-konzolalkalmazást. Szüksége lesz az IoT Edge-eszköz és a egy Windows-tároló vagy a Linux-tárolóban fut szükséges Docker-fájlokat tartalmazza. A **module.json** fájl írja le a modulok metaadatai. A **program.cs** a tényleges modul kódja, amely az Azure IoT eszközoldali SDK függőségként.

## <a name="develop-your-module"></a>A modul fejlesztése

Az alapértelmezett C# modul kód, amely a megoldás a következő helyen található **IoTEdgeModule1** > **Program.cs**. A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul be van építve igénybe vehet a bemeneti forrásból (ebben az esetben a **tempSensor** modul, amely szimulálja az adatokat), és átadhatja azt az Azure IoT hubra.

Ha készen áll a C#-sablont a saját kód testreszabása, használja a [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) hozhat létre a modulok a kulcsot kell IoT-megoldások, például a biztonság, a kezelés és a megbízhatóság címmel.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Az IoT Edge-eszköz kapcsolati karakterláncának iotedgehubdev inicializálása

1. Másolja a kapcsolati karakterláncot, a bármely IoT Edge-eszköz **elsődleges kapcsolati karakterlánc** a a Visual Studio Cloud Explorer. Mindenképpen nem másolja a kapcsolati karakterláncot nem peremhálózati eszköz, mivel az ikonra az IoT Edge-eszköz eltér attól az ikontól, nem Edge-eszköz.

   ![Edge-eszköz kapcsolati karakterlánc másolása](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Kattintson a jobb gombbal a **AzureIoTEdgeApp1** projektre, majd kattintson **beállítása peremhálózati eszköz kapcsolati karakterláncának** az Azure IoT Edge-telepítő ablak megnyitásához.

   ![Set Edge kapcsolati karakterlánc ablak megnyitása](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Az első lépésben adja meg a kapcsolati karakterláncot, majd **OK**.

> [!NOTE]
> Kövesse az alábbi lépéseket csak akkor, ha a fejlesztési számítógépen, az eredményeket automatikusan érvénybe lépnek minden ezt követő Azure IoT Edge-megoldások kell. Ez az eljárás újra végrehajthatók, ha egy másik kapcsolati karakterláncot, módosítani kell.

## <a name="build-and-debug-single-c-module"></a>Hozhat létre, és a egy C# modul hibakeresése

Általában érdemes tesztelése és hibakeresése az egyes modulok belül egyszerre több modul teljes megoldás futtatásához.

1. Kattintson a jobb gombbal **IoTEdgeModule1** válassza **Set as StartUp Project** a helyi menüből.

   ![Indítási projekt beállítása](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Nyomja meg **F5** vagy a modul futtassa az alábbi gombra kattintva; 10 is igénybe vehet&ndash;20 másodperc először törli.

   ![A modul futtatása](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Indítsa el, ha a modul inicializálása sikerült egy .NET Core-konzolalkalmazást kell megjelennie.

   ![A modul futtatása](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Állítson be egy töréspontot a `PipeMessage()` függvényével **Program.cs** és tesztelje a következő parancs futtatásával egy üzenet küldésével egy **a Git Bash** vagy **WSL Bash** rendszerhéj . (Nem lehet futtatni a `curl` parancsot a PowerShell vagy az parancssor.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Egy modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    A töréspont legyen elindítva. Megtekintheti a Visual Studio változók **Hívásiverem** ablak.

   > [!TIP]
   > Is [PostMan](https://www.getpostman.com/) vagy más API-eszközök helyett üzenetek küldéséhez `curl`.

1. Nyomja meg **Ctrl + F5** , vagy kattintson a Leállítás gomb a hibakeresés leállításához.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Fejlesztése és hibakeresése egyszerre több modul IoT Edge-megoldás

Miután elkészült egy modul fejlesztésével, érdemes futtatni, és egyszerre több modul teljes megoldás hibakereséséhez.

1. Vegyen fel egy második C# modult, kattintson a jobb gombbal a megoldás **AzureIoTEdgeApp1** és kiválasztásával **Hozzáadás** > **új IoT Edge-modul**. Az alapértelmezett név a második modul **IoTEdgeModule2** és a egy másik cső modul fog működni.

1. Nyissa meg a fájlt `deployment.template.json` és látni fogja **IoTEdgeModule2** hozzá lett adva a **modulok** szakaszban. Cserélje le a **útvonalak** a következő szakaszt. Ha testreszabta a nevét, ellenőrizze, hogy ezeket a neveket megfelelően frissíti.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Kattintson a jobb gombbal **AzureIoTEdgeApp1** válassza **Set as StartUp Project** a helyi menüből.

1. A töréspontok létrehozása, és nyomja le az **F5** , futtatása és hibáik keresése egyszerre több modul. Megjelenik a konzol több .NET Core-alkalmazás windows, mely minden jelölő egy másik ablak C# modul.

   ![Több modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Nyomja meg **Ctrl + F5** , vagy válassza a Leállítás gomb a hibakeresés leállításához.

## <a name="build-and-push-images"></a>És leküldéses képek

1. Győződjön meg arról, hogy **AzureIoTEdgeApp1** indítási projekt. Válassza **Debug** vagy **kiadási** , a konfiguráció a modul képeket hozhat létre.

    > [!NOTE]
    > Amikor kiválasztja, **Debug**, használja a Visual Studio `Dockerfile.debug` Docker-rendszerképeket hozhat létre. Ez magában foglalja a .NET Core parancssori hibakereső VSDBG a tároló rendszerképének összeállítása során. Az éles használatra kész IoT Edge-modulok, azt javasoljuk, hogy használja a **kiadási** -beállítást `Dockerfile` VSDBG nélkül.

1. Privát regisztrációs adatbázis például az Azure Container Registry használata, a következő parancsot a Docker használatával bejelentkezni. Ha a helyi beállításjegyzékben használja, [futtassa a helyi beállításjegyzék](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Privát regisztrációs adatbázis például az Azure Container Registry használata, a beállításjegyzék bejelentkezési adatok hozzáadása a futásidő beállításait, a fájlban kell `deployment.template.json`. Cserélje le a helyőrzőket a tényleges ACR rendszergazdai felhasználónév, jelszó és a beállításjegyzék nevére.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Kattintson a jobb gombbal **AzureIoTEdgeApp1** válassza **Build és a leküldéses peremhálózati megoldás** összeállítása és leküldése a Docker-rendszerképet, az egyes modulok.

   ![És leküldéses képek](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. A Cloud Explorer használatával a Visual Studióhoz készült modulok is telepítheti. Már rendelkezik egy manifest nasazení előkészítve a forgatókönyvnek a `deployment.json` fájl- és mást nem kell tennie a válasszon ki egy eszközt megkapná a központi telepítést.

1. Nyissa meg **Cloud Explorer** kattintva **nézet** > **Cloud Explorer**. Győződjön meg arról, hogy bejelentkezett a Visual Studio 2017.

1. A **Cloud Explorer**, bontsa ki az előfizetést, keresse meg az Azure IoT Hub és az Azure IoT Edge-eszköz számára telepíteni kívánja.

1. Kattintson a jobb gombbal a központi telepítés létrehozásához, IoT Edge-eszközön, ki kell választania a központi telepítési jegyzékfájl alatt a `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Nem kell választania `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. A frissítés gombra kattintva tekintse meg az új modulokat fut a **TempSensor** modul és **$edgeAgent** és **$edgeHub**.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Az egy adott eszközhöz tartozó D2C üzenet monitorozásához válassza ki az eszközt a listából, és kattintson a **figyelési D2C-Messages Start** a a **művelet** ablak.

1. Az adatok monitorozásának leállításához válassza ki az eszközt a listából, majd **figyelési D2C-Messages leállítása** a a **művelet** ablak.

## <a name="next-steps"></a>További lépések

Az IoT Edge-eszközök egyéni modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).
