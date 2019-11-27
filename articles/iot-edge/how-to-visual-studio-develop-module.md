---
title: Modulok fejlesztése és hibakeresése a Visual Studióban – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-modulok fejlesztése és hibakeresése a Visual Studio 2019 használatával
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 2f8b0fe83e10beb3b65dca08e18b03f4fc11947e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457110"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Azure IoT Edge-modulok fejlesztése és hibakeresése a Visual Studio 2019 használatával

Az Azure IoT Edge kapcsolhatja az üzleti logikára modulokat. Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio 2019-et a modulok fejlesztésére és hibakeresésére szolgáló fő eszközként.

Az Azure IoT Edge Tools for Visual Studio az alábbi előnyöket nyújtja:

- Azure IoT Edge megoldások és modulok létrehozása, szerkesztése, összeállítása, futtatása és hibakeresése a helyi fejlesztési számítógépen.
- Az Azure IoT Edge-megoldás üzembe helyezése az Azure IoT Hub-n keresztül az Azure IoT Edge-eszköz.
- Az Azure IoT-modulokat a C C# -ben vagy a Visual Studio fejlesztésének összes előnyével elvégezheti.
- Kezelheti az Azure IoT Edge-eszközök és a modulok felhasználói felületen keresztül.

Ez a cikk bemutatja, hogyan használhatja a Visual Studio 2019 Azure IoT Edge-eszközeit a IoT Edge modulok fejlesztéséhez. Azt is megtudhatja, hogyan a projekt telepítése az Azure IoT Edge-eszköz. A Visual Studio 2019 jelenleg a C-ben és C#a-ben írt modulokhoz nyújt támogatást. A támogatott eszközök architektúrái a következők: Windows x64 és Linux x64 vagy ARM32. További információ a támogatott operációs rendszerekről, nyelvekről és architektúráról: [nyelvi és architektúra-támogatás](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy egy számítógép vagy a Windows rendszerű fejlesztői gépen, virtuális gép használja. Windows rendszerű számítógépeken Windows-és Linux-modulokat is fejleszthet. Windows-modulok fejlesztéséhez használjon 1809/Build 17763 vagy újabb verziót futtató Windows-számítógépet. Linux-modulok fejlesztéséhez használjon olyan Windows-számítógépet, amely megfelel a [Docker Desktop követelményeinek](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Mivel ez a cikk a Visual Studio 2019-et használja fő fejlesztői eszközként, telepítse a Visual studiót. Ügyeljen rá, hogy a Visual Studio 2019-es verziójában a számítási **feladatokkal együtt C++**  tartalmazza az **Azure fejlesztési** és asztali fejlesztését. A [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) a szükséges számítási feladatok hozzáadásához is módosítható.

Miután a Visual Studio 2019 elkészült, a következő eszközökre és összetevőkre is szüksége lesz:

- Töltse le és telepítse [Azure IoT Edge eszközöket](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) a Visual Studio Marketplace-ből, és hozzon létre egy IoT Edge projektet a visual Studio 2019-ben.

> [!TIP]
> Ha a Visual Studio 2017-at használja, töltse le és telepítse [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for vs 2017 alkalmazást a Visual Studio piactérről

- Töltse le és telepítse a [Docker Community Editiont](https://docs.docker.com/install/) a fejlesztői gépen a modul lemezképének elkészítéséhez és futtatásához. A Docker CE-t úgy kell beállítania, hogy Linux-tároló módban vagy Windows-tároló módban fusson.

- Állítsa be a helyi fejlesztési környezetet a IoT Edge megoldás hibakereséséhez, futtatásához és teszteléséhez az [Azure IoT EdgeHub dev Tool](https://pypi.org/project/iotedgehubdev/)telepítésével. Telepítse a [Pythont (2.7/3.6 +) és a pip](https://www.python.org/) -et, majd telepítse a **iotedgehubdev** csomagot a következő parancs futtatásával a terminálon. Ellenőrizze, hogy az Azure IoT EdgeHub fejlesztési eszköz verziószáma nagyobb, mint 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- A tárház klónozásával telepítse a Vcpkg Library Managert, majd telepítse a Windowshoz készült **Azure-IOT-SDK-c csomagot** .

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Egy helyi Docker-beállításjegyzék prototípus és a egy felhőbeli beállításjegyzék helyett tesztelési célokra használható.

- A modul eszközön való teszteléséhez egy aktív IoT hub szükséges legalább egy IoT Edge eszközzel. Ha a számítógépet IoT Edge eszközként szeretné használni, kövesse a [Linux](quickstart-linux.md) vagy [Windows rendszerhez](quickstart.md)készült gyors útmutató lépéseit. Ha IoT Edge démont futtat a fejlesztői gépen, előfordulhat, hogy le kell állítania a EdgeHub és a EdgeAgent, mielőtt elkezdi a fejlesztést a Visual Studióban.

### <a name="check-your-tools-version"></a>Az eszközök verziójának ellenőrzéséhez

1. A **Tools (eszközök** ) menüben válassza a **bővítmények és frissítések**lehetőséget. Bontsa ki a **telepített > eszközöket** , és megtalálhatja a Visual studióhoz készült **Azure IoT Edge eszközöket** és **a Cloud Explorer programot**.

1. Megjegyzés: a telepített verzió. Ezt a verziót a Visual Studio Marketplace legújabb verziójának használatával hasonlíthatja össze ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Ha a verzió régebbi, mint a Visual Studio Marketplace-en elérhető verziója, frissítse az eszközeit a Visual Studióban, ahogy az a következő szakaszban is látható.

### <a name="update-your-tools"></a>Az eszközök frissítése

1. A **bővítmények és frissítések** párbeszédpanelen bontsa ki a **frissítések > Visual Studio Marketplace**elemet, válassza a **Azure IoT Edge Tools** vagy **a Cloud Explorer for Visual Studio** lehetőséget, és válassza a **frissítés**lehetőséget.

1. Után az eszközök a frissítés letöltését követően zárja be a Visual Studióban az eszközök frissítése a VSIX telepítővel eseményindítóra.

1. A telepítőben kattintson az **OK gombra** a kezdéshez, majd **módosítsa** az eszközök frissítéséhez.

1. A frissítés befejezése után kattintson a **Bezárás gombra** , és indítsa újra a Visual studiót.

### <a name="create-an-azure-iot-edge-project"></a>Az Azure IoT Edge-projekt létrehozása

Az Azure IoT Edge webesprojekt-sablon a Visual Studio létrehoz egy projektet, az Azure IoT Hub az Azure IoT Edge-eszközökre telepíthető. Először hozzon létre egy Azure IoT Edge megoldást, majd hozza létre a megoldás első modulját. Egyes IoT Edge-megoldások tartalmazhatnak egynél több modul.

> [!TIP]
> A Visual Studio által létrehozott IoT Edge projekt szerkezete nem ugyanaz, mint a Visual Studio Code-ban.

1. A Visual Studio új projekt párbeszédpanelen keresse meg és válassza ki **Azure IoT Edge** projektet, majd kattintson a **tovább**gombra. A Project Configuration (projekt konfigurációja) ablakban adja meg a projekt nevét, és adja meg a helyet, majd válassza a **Létrehozás**lehetőséget. Az alapértelmezett projekt neve **AzureIoTEdgeApp1**.

   ![Új projekt létrehozása](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. A **IoT Edge alkalmazás és modul hozzáadása** ablakban válassza a  **C# modul** vagy a **C modul** lehetőséget, majd adja meg a modul nevét és a modul rendszerkép-tárházát. A Visual Studio automatikusan feltölti a modul nevét a **localhost: 5000/< a modul neve\>** . Cserélje le a saját beállításjegyzék-információkat. Ha a teszteléshez helyi Docker-beállításjegyzéket használ, a **localhost** rendben van. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló úgy néz ki, mint  **_\<beállításjegyzék neve\>_ . azurecr.IO**. Csak a karakterlánc következő helyére írja a **localhost: 5000** részét, hogy a végeredmény a **\<*beállításjegyzék neve*\>. azurecr.io/ _\<a modul neve\>_** . Az alapértelmezett modul neve **IotEdgeModule1**

   ![Alkalmazás és modul hozzáadása](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Kattintson **az OK** gombra, ha a Azure IoT Edge megoldást egy C# vagy C betűt használó modullal szeretné létrehozni.

Most már rendelkezik egy **AzureIoTEdgeApp1. Linux. amd64** projekttel vagy egy **AzureIoTEdgeApp1. Windows. Amd64** projekttel, valamint egy **IotEdgeModule1** -projekttel a megoldásban. Mindegyik **AzureIoTEdgeApp1** -projekt rendelkezik egy `deployment.template.json` fájllal, amely meghatározza a IoT Edge-megoldáshoz felépíteni és telepíteni kívánt modulokat, valamint meghatározza a modulok közötti útvonalakat is. Az alapértelmezett megoldás egy **SimulatedTemperatureSensor** modult és egy **IotEdgeModule1** modult tartalmaz. A **SimulatedTemperatureSensor** modul szimulált értékeket hoz létre a **IotEdgeModule1** modulba, míg az **IotEdgeModule1** modul alapértelmezett kódja a közvetlenül az Azure IoT hubba érkező üzeneteket fogad.

A **IotEdgeModule1** -projekt egy .net Core 2,1 Console-alkalmazás, ha C# modul. A Windows-tárolóval vagy a Linux-tárolóval futó IoT Edge eszközhöz szükséges Docker-fájlokat tartalmazza. A `module.json` fájl a modul metaadatait írja le. Az Azure IoT Device SDK-t függőségként tároló tényleges modul-kód a `Program.cs` vagy `main.c` fájlban található.

## <a name="develop-your-module"></a>A modul fejlesztése

A megoldás alapértelmezett moduljának kódja a következő helyen található: **IotEdgeModule1** > **program.cs** ( C#) vagy **Main. c** (c). A modul és a `deployment.template.json` fájl úgy van beállítva, hogy felépítheti a megoldást, leküldheti a tároló-beállításjegyzékbe, és üzembe helyezheti azt egy eszközön anélkül, hogy meg kellene kezdenie a kódot. A modul úgy lett felépítve, hogy bejegyezze a forrás (ebben az esetben az adatokat szimuláló **SimulatedTemperatureSensor** modul) adatait, és átadja az Azure IoT hubnak.

Ha készen áll a modul sablonjának testre szabására a saját kódjával, az [Azure IoT hub SDK](../iot-hub/iot-hub-devguide-sdks.md) -k segítségével olyan modulokat építhet ki, amelyek a IoT-megoldások, például a biztonság, az eszközkezelés és a megbízhatóság terén szükségesek.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Iotedgehubdev inicializálása IoT Edge eszköz-csatlakoztatási karakterlánccal

1. Másolja a Visual Studio Cloud Explorerben a IoT Edge eszközhöz tartozó kapcsolatok karakterláncát az **elsődleges kapcsolatok sztringből** . Ügyeljen arra, hogy ne másolja a nem peremhálózati eszközhöz tartozó kapcsolódási karakterláncot, mert egy IoT Edge eszköz ikonja eltér a nem peremhálózati eszköz ikonjának.

   ![Edge-eszköz kapcsolati karakterlánc másolása](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Nyissa meg az **eszközök** > **Azure IoT Edge eszközök** > a **telepítő IoT Edge szimulátor**, illessze be a kapcsolódási karakterláncot, majd kattintson **az OK**gombra.

   ![Set Edge kapcsolati karakterlánc ablak megnyitása](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Adja meg a kapcsolatok karakterláncát az első lépésnél, majd kattintson **az OK gombra**.

> [!NOTE]
> Ezeket a lépéseket csak egyszer kell követnie a fejlesztői számítógépen, mivel a rendszer automatikusan alkalmazza az eredményeket az összes további Azure IoT Edge megoldásra. Ez az eljárás akkor követhető újra, ha másik kapcsolódási sztringre kell váltania.

## <a name="build-and-debug-single-module"></a>Egyetlen modul létrehozása és hibakeresése

Az egyes modulokat általában tesztelni és hibakeresést végezni, mielőtt egy teljes megoldáson belül futtatja, több modullal.

1. Kattintson a jobb gombbal a **IotEdgeModule1** elemre, és válassza a **beállítás indítási projektként** lehetőséget a helyi menüben.

   ![Indítási projekt beállítása](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Nyomja le az **F5** billentyűt, vagy kattintson az alábbi gombra a modul futtatásához; az első alkalommal 10&ndash;20 másodpercet vehet igénybe.

   ![A modul futtatása](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Ha a modul inicializálása sikeresen megtörtént, megjelenik egy .NET Core Console-alkalmazás.

   ![A modul futtatása](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Ha a- C#ben fejleszti a-t, a **program.cs**`PipeMessage()` függvényében állítson be egy töréspontot. Ha a C-t használja, állítson be egy töréspontot a **Main. C**`InputQueue1Callback()` függvényében. Ezt követően a következő parancs futtatásával tesztelheti azt egy **git bash** -vagy **WSL bash** -rendszerhéjban. (A `curl` parancsot nem futtathatja PowerShellből vagy parancssorból.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Egy modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    A töréspont legyen elindítva. A Visual Studio **Locals** ablakban is megtekintheti a változókat.

   > [!TIP]
   > A [Poster](https://www.getpostman.com/) vagy más API-eszközök használatával `curl`helyett üzeneteket is küldhet.

1. Nyomja le a **CTRL + F5** billentyűkombinációt, vagy kattintson a Leállítás gombra a hibakeresés leállításához.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Fejlesztése és hibakeresése egyszerre több modul IoT Edge-megoldás

Miután befejezte egyetlen modul fejlesztését, érdemes lehet egy teljes megoldást futtatni és hibakeresést végezni több modullal.

1. Vegyen fel egy második modult a megoldásba úgy, hogy a jobb gombbal a **AzureIoTEdgeApp1** elemre kattint, és kiválasztja > **új IoT Edge modul** **hozzáadása** lehetőséget. A második modul alapértelmezett neve **IotEdgeModule2** , és egy másik pipe-modulként fog működni.

1. Nyissa meg `deployment.template.json` fájlt, és látni fogja, hogy a **IotEdgeModule2** hozzá lett adva a **modulok** szakaszhoz. Cserélje le az **útvonalak** szakaszt az alábbira. Ha testre szabta a modulok nevét, ügyeljen rá, hogy a neveket a megfelelő értékre frissítse.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Kattintson a jobb gombbal a **AzureIoTEdgeApp1** elemre, és válassza a **beállítás indítási projektként** lehetőséget a helyi menüben.

1. Hozza létre a töréspontokat, majd nyomja le az **F5** billentyűt egyszerre több modul futtatásához és hibakereséséhez. Látnia kell több .NET Core Console-alkalmazást, amely az egyes ablakokat egy másik modult jelképezi.

   ![Több modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. A hibakeresés leállításához nyomja le a **CTRL + F5** billentyűkombinációt, vagy kattintson a Leállítás gombra.

## <a name="build-and-push-images"></a>És leküldéses képek

1. Győződjön meg arról, hogy a **AzureIoTEdgeApp1** az indítási projekt. Válassza a **hibakeresés** vagy a **kiadás** lehetőséget a modul lemezképei számára felépített konfigurációként.

    > [!NOTE]
    > A **hibakeresés**kiválasztásakor a Visual Studio a `Dockerfile.(amd64|windows-amd64).debug` használatával Docker-rendszerképeket hoz létre. Ez magában foglalja a .NET Core parancssori hibakereső VSDBG a tároló rendszerképének összeállítása során. Az éles használatra kész IoT Edge modulok esetében javasoljuk, hogy a **kiadási** konfigurációt használja, amely VSDBG nélkül használja a `Dockerfile.(amd64|windows-amd64)`t.

1. Ha olyan privát beállításjegyzéket használ, mint a Azure Container Registry, a következő Docker-paranccsal jelentkezzen be. Ha helyi beállításjegyzéket használ, [helyi beállításjegyzéket is futtathat](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Ha olyan privát beállításjegyzéket használ, mint a Azure Container Registry, a beállításjegyzék bejelentkezési adatait a fájlban található futtatókörnyezet-beállításokhoz kell hozzáadnia `deployment.template.json`. Cserélje le a helyőrzőket a tényleges ACR-rendszergazdai felhasználónevére, jelszavára és beállításjegyzék-nevére.

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

1. Kattintson a jobb gombbal a **AzureIoTEdgeApp1** elemre, és válassza a **Build és leküldés Edge-megoldás** lehetőséget az egyes modulok Docker-rendszerképének létrehozásához és leküldése

   ![És leküldéses képek](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. A Cloud Explorer használatával a Visual Studióhoz készült modulok is telepítheti. Már rendelkezik egy, a forgatókönyvhöz előkészített üzembe helyezési jegyzékfájlval, a `deployment.json`-fájlhoz és a szükséges összes művelethez ki kell választania egy eszközt az üzemelő példány fogadásához.

1. Nyissa meg a **Cloud Explorert** a > **Cloud Explorer** **megtekintése** elemre kattintva. Győződjön meg arról, hogy bejelentkezett a Visual Studio 2019-ba.

1. A **Cloud Explorerben**bontsa ki az előfizetését, keresse meg az Azure-IoT hub és a telepíteni kívánt Azure IoT Edge eszközt.

1. A központi telepítés létrehozásához kattintson a jobb gombbal a IoT Edge eszközre, majd a `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`alatt válassza ki a telepítési jegyzékfájlt.

   > [!NOTE]
   > Nem szabad kiválasztani `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Kattintson a refresh (frissítés) gombra, és tekintse meg az új modulokat, amelyek a **SimulatedTemperatureSensor** modullal és a **$edgeAgent** és **$edgeHubval**együtt futnak.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Egy adott eszközhöz tartozó D2C-üzenet figyeléséhez jelölje ki az eszközt a listában, majd kattintson a **beépített esemény-végpont figyelése** elemre a **műveleti** ablakban.

1. A figyelési információk leállításához jelölje ki az eszközt a listában, majd a **művelet** ablakban válassza a **figyelés a beépített esemény végpontjának leállítása** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Egyéni modulok fejlesztése a IoT Edge-eszközökhöz, az [Azure IoT hub SDK-k megismerése és használata](../iot-hub/iot-hub-devguide-sdks.md).
