---
title: Fejlesztés és a C# modul hibakeresése a Visual Studio 2017 Azure IoT Edge |} A Microsoft Docs
description: A Visual Studio 2017 használatával fejlesztése, és C# modul hibakeresése az Azure IoT Edge-hez
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 86f0867389ce6ee1c83190e03ba17362db29d6f7
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975873"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Fejlesztés és az Azure IoT Edge (előzetes verzió) C# modul hibakeresése a Visual Studio 2017 használatával

Az Azure IoT Edge kapcsolhatja az üzleti logikára modulokat. Ez a cikk bemutatja, hogyan a fő eszköz fejlesztéséhez és a C# modul hibakeresése a Visual Studio 2017 adatokként.

Az Azure IoT Edge Tools for Visual Studio az alábbi előnyöket nyújtja:

- Létrehozás, Szerkesztés, létrehozása, futtatása és Azure IoT Edge-megoldások és a modulok a helyi fejlesztési számítógép hibakeresési.
- Az Azure IoT Edge-megoldás üzembe helyezése az Azure IoT Hub-n keresztül az Azure IoT Edge-eszköz.
- Az Azure IoT-modulok a C#-kód során a Visual Studio fejlesztési előnyeit a teljes.
- Kezelheti az Azure IoT Edge-eszközök és a modulok felhasználói felületen keresztül. 

Ez a cikk bemutatja, hogyan fejleszthet a C# IoT Edge-modulok az Azure IoT Edge Tools for Visual Studio 2017 használatával. Azt is megtudhatja, hogyan a projekt telepítése az Azure IoT Edge-eszköz.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy a Windows rendszerű fejlesztői gépen, virtuális gép használja. Az IoT Edge-eszközt egy másik fizikai eszköz lehet.

Mivel ez a cikk a Visual Studio 2017, a fő fejlesztési eszközt használ, telepítse a Visual Studióban. Győződjön meg arról is, és **Azure development workload munkafolyamatának** a Visual Studio 2017 telepítése. Is [módosítása a Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) , és adja hozzá az Azure-fejlesztési számítási feladatot.

Miután készen áll a Visual Studio 2017, akkor is szüksége lesz:

- Töltse le és telepítse [Azure IoT Edge bővítmény](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) IoT Edge létrehozása a Visual Studio marketplace-ről projektre a Visual Studio 2017-ben.
- [A docker Community Edition](https://docs.docker.com/install/) a fejlesztői gépen való létrehozásához és futtatásához a modul rendszerképeit. A Docker CE Linux tároló vagy Windows-tároló üzemmódban futó megfelelően be kell.
- A hibakeresés, futtatása és az IoT Edge-megoldás teszteléséhez helyi fejlesztési környezet beállításával kapcsolatban kell [Azure IoT EdgeHub fejlesztőeszközt](https://pypi.org/project/iotedgehubdev/). Telepítés [Python (2.7-es és 3.6) és a Pip](https://www.python.org/). Ezután telepítse **iotedgehubdev** futtassa az alábbi parancsot a terminálon. Ellenőrizze, hogy az Azure IoT EdgeHub fejlesztési eszköz verziószáma nagyobb, mint 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   - Egy helyi Docker-beállításjegyzék prototípus és a egy felhőbeli beállításjegyzék helyett tesztelési célokra használható. 

- A modul teszteléséhez szüksége active IoT hub létrehozott legalább egy IoT Edge Eszközazonosítót használó. Ha az IoT Edge biztonsági démon a fejlesztői gépen futtatja, szüksége lehet EdgeHub és EdgeAgent állítsa le a Visual Studióban fejlesztés megkezdése előtt. 

### <a name="check-your-tools-version"></a>Az eszközök verziójának ellenőrzéséhez

1. Az a **eszközök** menüben válassza a **bővítmények és frissítések**. Bontsa ki a **telepített > eszközök** és annak **Azure IoT Edge** és **Cloud Explorer**.

2. Megjegyzés: a telepített verzió. Ez a verzió a legújabb verzióra a Visual Studio-piactér összehasonlíthatja ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge))

3. Ha a verzió régebbi, frissítse az eszközök a Visual Studióban, a következő szakaszban látható módon.

### <a name="update-your-tools"></a>Az eszközök frissítése

1. Az a **bővítmények és frissítések** párbeszédpanelen bontsa ki a **frissítések > Visual Studio-piactér**, válassza a **Azure IoT Edge** vagy **Cloud Explorer**válassza **frissítés**.

2. Után az eszközök a frissítés letöltését követően zárja be a Visual Studióban az eszközök frissítése a VSIX telepítővel eseményindítóra.

3. A telepítő válassza **OK** elindításához, és módosítsa az eszközök frissítése.

4. A frissítés befejezése után kattintson a Bezárás gombra, és indítsa újra a Visual Studióban.

### <a name="create-an-azure-iot-edge-project"></a>Az Azure IoT Edge-projekt létrehozása

Az Azure IoT Edge webesprojekt-sablon a Visual Studio létrehoz egy projektet, az Azure IoT Hub az Azure IoT Edge-eszközökre telepíthető. Először hozzon létre egy Azure IoT Edge-megoldás, és ezután az első C# modul létrehozása ebben a megoldásban. Egyes IoT Edge-megoldások tartalmazhatnak egynél több modul. 

1. A Visual Studio **Fájl** menüjében válassza az **Új** > **Projekt** lehetőséget.

2. Az a **új projekt** párbeszédpanelen válassza **telepített**, bontsa ki a **Visual C# > felhő**, jelölje be **Azure IoT Edge**, adjon meg egy  **Név** a projekthez, és adja meg a helyet, és kattintson a **OK**. Az alapértelmezett projekt neve **AzureIoTEdgeApp1**. 

   ![Új projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. Az a **IoT Edge-modul konfigurációjának** ablakban válassza **C# modul** írja be, és adja meg a modul nevét és a modul lemezképtárban.  A modul VS autopopulates név és **localhost:5000**. Cserélje le a saját beállításjegyzék-információkat. Ha tesztelése helyi Docker-tárolójegyzék használja, localhost nem okoz gondot. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló néz  **<registry name>. azurecr.io**. Csak a sztring localhost részét cserélje le, ne törölje a modul nevét. Az alapértelmezett modul neve **IoTEdgeModule1**

4. Kattintson a **OK** az Azure IoT Edge-projekt létrehozása a C# modul.

Most már rendelkezik egy **AzureIoTEdgeApp1** projekt és a egy **IoTEdgeModule1** projektet a megoldásban. A **AzureIoTEdgeApp1** projekt rendelkezik egy **deployment.template.json** fájlt. Ez a fájl határozza meg a kívánt létrehozása és üzembe helyezése az IoT Edge-megoldás modulok, és határozza meg az útvonalakat a modulok között. A alapértelmezett megoldáshoz tartozik egy **tempSensor** modul és a egy **IoTEdgeModule1** modul. A **tempSensor** modul a szimulált adatokat állít elő **IoTEdgeModule1** modult, miközben az alapértelmezett kód a **IoTEdgeModule1** modul egy függőleges üzenet-modult, amely közvetlenül cső fogadott üzeneteket az IoT Hub.

A **IoTEdgeModule1** projekt egy .net Core 2.1-es konzolalkalmazást. Tartalmaz szükséges **docker-fájlok** a tároló Windows vagy Linux-tárolóban futó IoT Edge-eszköz van szüksége. A **module.json** fájl írja le a modulok metaadatai. A **program.cs** a tényleges modul kódja, amely az Azure IoT eszközoldali SDK függőségként.

## <a name="develop-your-module"></a>A modul fejlesztése

Az alapértelmezett C# modul kód, amely a megoldás a következő helyen található **IoTEdgeModule1** > **Program.cs**. A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul csak igénybe vehet a bemeneti forrásból (ami jelen esetben a tempSensor modul, amely szimulálja az adatokat), és átadhatja azt az IoT hubhoz való használatra készült. 

Ha készen áll a C#-sablont a saját kód testreszabása, használja a [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) hozhat létre a modulok a kulcsot kell IoT-megoldások, például a biztonság, a kezelés és a megbízhatóság címmel. 

## <a name="initialize-iotegehubdev-with-iot-edge-device-connection-string"></a>Inicializálása **iotegehubdev** IoT Edge-eszköz kapcsolati karakterlánccal

1. Minden IoT Edge-eszköz kapcsolati karakterláncot kapott, a Cloud Explorer hajtsa végre a Visual Studio 2017-ben is másolhatja az "Elsődleges kapcsolati karakterlánc" értékét. Meg nem másolja a kapcsolati karakterlánc nem peremhálózati eszköz, az ikonra az IoT Edge-eszköz nem Edge-eszköz az egyik eltér.

   ![Edge-eszköz kapcsolati karakterlánc másolása](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

2. Kattintson a jobb gombbal kell **AzureIoTEdgeApp1** projekt helyi menü megnyitásához, majd kattintson a **beállítása peremhálózati eszköz kapcsolati karakterláncának**, jelenik meg az Azure IoT Edge beállítása ablakban.

   ![Set Edge kapcsolati karakterlánc ablak megnyitása](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

3. A telepítési ablakra adjon meg az első lépésben kapott kapcsolati karakterláncot, majd kattintson **OK** gombra.

>[!NOTE]
>Ez egyszeri work, csak kell futtatásakor ebben a lépésben egyszer egy számítógépen, az összes későbbi Azure IoT Edge megoldások fog kapni, ha az ingyenes. Természetesen újra futtathatja ezt a lépést, ha a kapcsolati karakterlánc érvénytelen, vagy egy másik kapcsolati karakterláncot, módosítani kell.

## <a name="build-and-debug-single-c-module"></a>Hozhat létre, és a egy C# modul hibakeresése

Általában szeretnénk teszt/debug minden egyes modul elérhetővé, egyszerre több modul belül teljes megoldás futtatása előtt.

1. Válassza ki **IoTEdgeModule1** az indítási projektként a helyi menüben.

   ![Indítási projekt beállítása](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. Nyomja meg **F5** vagy a modul futtassa az alábbi gombra kattintva, 10 is igénybe vehet ~ idő az első 20 másodperc.

   ![A modul futtatása](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. Megjelenik egy .net Core-konzolalkalmazást használni, ha a modul inicializálása sikerült.

   ![A modul futtatása](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. Most beállíthat egy töréspontot **PipeMessage** a **Program.cs**, majd az üzenet küldése a következő parancs futtatásával a **a Git Bash** vagy **WSL Bash**  (ne futtassa azt a cmd Parancsot vagy a PowerShell használatával) (is találhatja meg ezt a parancsot a VS kimeneti ablak):

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Egy modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    A töréspont legyen elindítva. A Visual Studio helyi változók ablakban változók tekintheti meg.

   > [!TIP]
   > Is [PostMan](https://www.getpostman.com/) vagy más API-eszközök helyett keresztül üzenetek küldéséhez `curl`.

5. Nyomja meg **Ctrl + F5** , vagy kattintson a Leállítás gomb a hibakeresés leállításához.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Fejlesztése és hibakeresése egyszerre több modul IoT Edge-megoldás

Miután befejeződik egy modul fejlesztése mellett, szeretnénk futtatása és hibáik keresése a teljes megoldás egyszerre több modul.

1. A második C# modul hozzáadása a megoldásba. Kattintson a jobb gombbal **AzureIoTEdgeApp1** válassza **Hozzáadás** -> **új IoT Edge-modul**. Az alapértelmezett név a második modul **IoTEdgeModule2** , és továbbra is egy függőleges modul.

2. Navigáljon a **deployment.template.json**. Látni fogja **IoTEdgeModule2** hozzá lett adva **modulok** szakaszban. Cserélje le a **útvonalak** a következő szakaszt. Ha testreszabta a nevét, ellenőrizze, hogy a nevek, a következő cseréje után frissítenie.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. Állítsa be **AzureIoTEdgeApp1** projekt indítási projekt helyi menüjéből.

4. Állítson be töréspontokat, és nyomja le az F5 billentyűt, majd sikerült futtatja, és több modul egyidejű hibakereséséhez. Megtekintheti az több .net Core console app windows, minden egyes ablak azt jelzi, hogy a C# modul. 

   ![Több modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. Nyomja meg **Ctrl + F5** , vagy kattintson a Leállítás gomb a hibakeresés leállításához.

## <a name="build-and-push-images"></a>És leküldéses képek

1. Győződjön meg arról, hogy **AzureIoTEdgeApp1** az indítási projekt. Válassza ki **Debug** vagy **kiadási** konfigurációját a modul rendszerképek létrehozásához.

    > [!NOTE]
    > Amikor kiválasztja, **Debug**, VS fogja használni `Dockerfile.debug` Docker-rendszerképeket hozhat létre. Ez magában foglalja a .NET Core parancssori hibakereső VSDBG a tároló rendszerképének összeállítása során. Javasoljuk, hogy használjon **kiadási** használó konfigurációs `Dockerfile` VSDBG az éles használatra kész IoT Edge-modulok nélkül.

2. Ha például az Azure Container Registry privát regisztrációs adatbázis használ, futtassa a Docker jelentkezzen be a következő parancsot a terminálban. Ha a helyi beállításjegyzékben használja, [futtassa a helyi beállításjegyzék](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Ha privát regisztrációs adatbázis használja például az Azure Container Registry, szeretné-e a beállításjegyzékhez tartozó felhasználónevével és a jelszó `deployment.template.json` a futásidő beállításait, az alábbi tartalommal. Ne felejtse el a helyőrzőt cserélje le tényleges rendszergazdai felhasználónevét és jelszavát.

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

4. Kattintson a jobb gombbal **AzureIoTEdgeApp1** , és válassza a helyi menüpont **Build és a leküldéses peremhálózati megoldás**, hozhat létre, és az egyes modulok docker-rendszerkép leküldése.

   ![És leküldéses képek](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. A Cloud Explorer használatával a Visual Studióhoz készült modulok is telepítheti. Már rendelkezik egy manifest nasazení előkészítve a forgatókönyvnek a `deployment.json` fájlt. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

1. Nyissa meg **Cloud Explorer** kattintva **nézet** > **Cloud Explorer**. Ellenőrizze, hogy a Visual Studio 2017-fiókkal jelentkezett-e.

2. A **Cloud Explorer**, bontsa ki az előfizetést, keresse meg az Azure IoT Hub és az Azure IoT Edge-eszköz számára telepíteni kívánja.

3. IoT Edge-eszköz, a központi telepítés létrehozásához kattintson a jobb gombbal, válassza ki a központi telepítési jegyzékfájl alatt kell létrehoznia a `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

>>[!NOTE]
>>Nem kell választania `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

4. Kattintson a frissítés gombra. Az új modulokat fut kell megjelennie a **TempSensor** modul és a **$edgeAgent** és **$edgeHub**.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. A D2C üzenet egy adott eszközhöz tartozó monitorozásához kattintson arra az eszközre a listában, majd kattintson **figyelési D2C-Messages Start** a **művelet** ablak.

2. Adatok monitorozásának leállításához kattintson az eszközre a listában, és válassza ki **figyelési D2C-Messages leállítása** a **művelet** ablak.

## <a name="next-steps"></a>További lépések

Az IoT Edge-eszközökön, a modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).
