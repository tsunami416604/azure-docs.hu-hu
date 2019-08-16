---
title: Modulok fejlesztése és hibakeresése a Visual Studióban – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-modulok fejlesztése és hibakeresése a Visual Studio 2019 használatával
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 892076954535d880f9081a269215cb7e2a0a8dce
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541859"
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

Mivel ez a cikk a Visual Studio 2019-et használja fő fejlesztői eszközként, telepítse a Visual studiót. Ügyeljen rá, hogy a Visual Studio 2019-es verziójában a számítási feladatokkal  **C++ együtt** tartalmazza az **Azure fejlesztési** és asztali fejlesztését. A [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) a szükséges számítási feladatok hozzáadásához is módosítható.

Miután a Visual Studio 2019 elkészült, a következő eszközökre és összetevőkre is szüksége lesz:

- Töltse le és telepítse [Azure IoT Edge eszközöket](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) a Visual Studio Marketplace-ből, és hozzon létre egy IoT Edge projektet a visual Studio 2019-ben.

> [!TIP]
> A Visual Studio 2017-es verziójának használata esetén a plrease letöltheti és telepítheti a VS 2017-es [Azure IoT Edge eszközöket](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) a Visual Studio piactérről

- Töltse le és telepítse a [Docker Community Editiont](https://docs.docker.com/install/) a fejlesztői gépen a modul lemezképének elkészítéséhez és futtatásához. A Docker CE-t úgy kell beállítania, hogy Linux-tároló módban vagy Windows-tároló módban fusson.

- Állítsa be a helyi fejlesztési környezetet a IoT Edge megoldás hibakereséséhez, futtatásához és teszteléséhez az [Azure IoT EdgeHub dev Tool](https://pypi.org/project/iotedgehubdev/)telepítésével. Telepítse a [Pythont (2.7/3.6) és a pip](https://www.python.org/) -et, majd telepítse a **iotedgehubdev** csomagot a következő parancs futtatásával a terminálon. Ellenőrizze, hogy az Azure IoT EdgeHub fejlesztési eszköz verziószáma nagyobb, mint 0.3.0.

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

1. Megjegyzés: a telepített verzió. Ez a verzió a legújabb verzióra a Visual Studio-piactér összehasonlíthatja ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

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

1. A **IoT Edge alkalmazás és modul hozzáadása** ablakban válassza a  **C# modul** vagy a **C modul** lehetőséget, majd adja meg a modul nevét és a modul rendszerkép-tárházát. A Visual Studio automatikusan feltölti a modul nevét a **localhost: 5000/< a modul\>nevével**. Cserélje le a saját beállításjegyzék-információkat. Ha a teszteléshez helyi Docker-beállításjegyzéket használ, a **localhost** rendben van. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló úgy tűnik   **_\<, hogy\>a beállításjegyzék neve_. azurecr.IO**. Csak cserélje le a **localhost: 5000** részét a karakterláncra, hogy a végső eredmény  **\<a *beállításjegyzék neveként*\>hasonlítson. azurecr.IO/ _\<a\>modul nevét_** . Az alapértelmezett modul neve **IotEdgeModule1**

   ![Alkalmazás és modul hozzáadása](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Kattintson **az OK** gombra, ha a Azure IoT Edge megoldást egy C# vagy C betűt használó modullal szeretné létrehozni.

Most már rendelkezik egy **AzureIoTEdgeApp1. Linux. amd64** projekttel vagy egy **AzureIoTEdgeApp1. Windows. Amd64** projekttel, valamint egy **IotEdgeModule1** -projekttel a megoldásban. Minden **AzureIoTEdgeApp1** -projekt tartalmaz `deployment.template.json` egy fájlt, amely meghatározza a IoT Edge megoldásához felépíteni és telepíteni kívánt modulokat, valamint meghatározza a modulok közötti útvonalakat is. Az alapértelmezett megoldás egy **tempSensor** modult és egy **IotEdgeModule1** modult tartalmaz. A **tempSensor** modul szimulált értékeket hoz létre a **IotEdgeModule1** modulba, míg az **IotEdgeModule1** modul alapértelmezett kódja a közvetlenül az Azure IoT hubba érkező üzeneteket fogad.

A **IotEdgeModule1** -projekt egy .net Core 2,1 Console-alkalmazás, ha C# modul. A Windows-tárolóval vagy a Linux-tárolóval futó IoT Edge eszközhöz szükséges Docker-fájlokat tartalmazza. A `module.json` fájl a modul metaadatait írja le. Az Azure IoT Device SDK-t függőségként tároló tényleges modul-kód a vagy `Program.cs` `main.c` a fájlban található.

## <a name="develop-your-module"></a>A modul fejlesztése

A megoldáshoz tartozó alapértelmezett modul-kód a következő helyen található: **IotEdgeModule1** > **program.cs** ( C#for) vagy **Main. c** (c). A rendszer úgy állítja `deployment.template.json` be a modult és a fájlt, hogy felépítse a megoldást, leküldi a tároló-beállításjegyzékbe, és üzembe helyezi azt egy eszközön, hogy bármilyen kód megérintése nélkül el tudja indítani a tesztelést. A modul úgy lett felépítve, hogy bejegyezze a forrás (ebben az esetben az adatokat szimuláló **tempSensor** modul) adatait, és átadja az Azure IoT hubnak.

Ha készen áll a modul sablonjának testre szabására a saját kódjával, az [Azure IoT hub SDK](../iot-hub/iot-hub-devguide-sdks.md) -k segítségével olyan modulokat építhet ki, amelyek a IoT-megoldások, például a biztonság, az eszközkezelés és a megbízhatóság terén szükségesek.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Iotedgehubdev inicializálása IoT Edge eszköz-csatlakoztatási karakterlánccal

1. Másolja a Visual Studio Cloud Explorerben a IoT Edge eszközhöz tartozó kapcsolatok karakterláncát az **elsődleges kapcsolatok sztringből** . Ügyeljen arra, hogy ne másolja a nem peremhálózati eszközhöz tartozó kapcsolódási karakterláncot, mert egy IoT Edge eszköz ikonja eltér a nem peremhálózati eszköz ikonjának.

   ![Edge-eszköz kapcsolati karakterlánc másolása](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Nyissa meg a **Tools** > **Azure IoT Edge Tools** > **Setup IoT Edge Simulator**elemet, illessze be a kapcsolódási karakterláncot, majd kattintson **az OK**gombra.

   ![Set Edge kapcsolati karakterlánc ablak megnyitása](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Adja meg a kapcsolatok karakterláncát az első lépésnél, majd kattintson **az OK gombra**.

> [!NOTE]
> Ezeket a lépéseket csak egyszer kell követnie a fejlesztői számítógépen, mivel a rendszer automatikusan alkalmazza az eredményeket az összes további Azure IoT Edge megoldásra. Ez az eljárás akkor követhető újra, ha másik kapcsolódási sztringre kell váltania.

## <a name="build-and-debug-single-module"></a>Egyetlen modul létrehozása és hibakeresése

Az egyes modulokat általában tesztelni és hibakeresést végezni, mielőtt egy teljes megoldáson belül futtatja, több modullal.

1. Kattintson a jobb gombbal a **IotEdgeModule1** elemre, és válassza a **beállítás indítási projektként** lehetőséget a helyi menüben.

   ![Indítási projekt beállítása](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Nyomja le az **F5** billentyűt, vagy kattintson az alábbi gombra a modul futtatásához; az első alkalommal 10&ndash;20 másodpercig is eltarthat.

   ![A modul futtatása](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Ha a modul inicializálása sikeresen megtörtént, megjelenik egy .NET Core Console-alkalmazás.

   ![A modul futtatása](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Ha a- C#ben fejlődik, állítson be `PipeMessage()` egy töréspontot a függvényben a **program.cs**; ha a C- `InputQueue1Callback()` t használja, állítsa be a töréspontot a függvényben a **Main. C**-ben. Ezt követően a következő parancs futtatásával tesztelheti azt egy **git bash** -vagy **WSL bash** -rendszerhéjban. (A `curl` parancsot nem futtathatja powershellből vagy parancssorból.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Egy modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    A töréspont legyen elindítva. A Visual Studio **Locals** ablakban is megtekintheti a változókat.

   > [!TIP]
   > A Poster vagy [](https://www.getpostman.com/) más API-eszközöket is használhatja az üzenetek küldéséhez a `curl`helyett.

1. Nyomja meg **Ctrl + F5** , vagy kattintson a Leállítás gomb a hibakeresés leállításához.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Fejlesztése és hibakeresése egyszerre több modul IoT Edge-megoldás

Miután befejezte egyetlen modul fejlesztését, érdemes lehet egy teljes megoldást futtatni és hibakeresést végezni több modullal.

1. Vegyen fel egy második modult a megoldáshoz a jobb gombbal a **AzureIoTEdgeApp1** elemre, és válassza az**új IoT Edge modul** **hozzáadása** > lehetőséget. A második modul alapértelmezett neve **IotEdgeModule2** , és egy másik pipe-modulként fog működni.

1. Nyissa meg `deployment.template.json` a fájlt, és látni fogja, hogy a **IotEdgeModule2** hozzá lett adva a **modulok** szakaszhoz. Cserélje le a **útvonalak** a következő szakaszt. Ha testre szabta a modulok nevét, ügyeljen rá, hogy a neveket a megfelelő értékre frissítse.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Kattintson a jobb gombbal a **AzureIoTEdgeApp1** elemre, és válassza a **beállítás indítási projektként** lehetőséget a helyi menüben.

1. Hozza létre a töréspontokat, majd nyomja le az **F5** billentyűt egyszerre több modul futtatásához és hibakereséséhez. Látnia kell több .NET Core Console-alkalmazást, amely az egyes ablakokat egy másik modult jelképezi.

   ![Több modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. A hibakeresés leállításához nyomja le a **CTRL + F5** billentyűkombinációt, vagy kattintson a Leállítás gombra.

## <a name="build-and-push-images"></a>És leküldéses képek

1. Győződjön meg arról, hogy a **AzureIoTEdgeApp1** az indítási projekt. Válassza a **hibakeresés** vagy a **kiadás** lehetőséget a modul lemezképei számára felépített konfigurációként.

    > [!NOTE]
    > A **hibakeresés**kiválasztásakor a Visual `Dockerfile.(amd64|windows-amd64).debug` Studio Docker-lemezképek készítésére használja. Ez magában foglalja a .NET Core parancssori hibakereső VSDBG a tároló rendszerképének összeállítása során. Az éles használatra kész IoT Edge modulok esetében javasoljuk, hogy a kiadási konfigurációt használja, amely `Dockerfile.(amd64|windows-amd64)` VSDBG nélkül használatos.

1. Ha olyan privát beállításjegyzéket használ, mint a Azure Container Registry, a következő Docker-paranccsal jelentkezzen be. Ha helyi beállításjegyzéket használ, [helyi beállításjegyzéket is futtathat](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Ha olyan privát beállításjegyzéket használ, mint például a Azure Container Registry, a beállításjegyzék bejelentkezési adatait fel kell vennie a fájlban `deployment.template.json`található futtatókörnyezeti beállításokba. Cserélje le a helyőrzőket a tényleges ACR-rendszergazdai felhasználónevére, jelszavára és beállításjegyzék-nevére.

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

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. A Cloud Explorer használatával a Visual Studióhoz készült modulok is telepítheti. Már rendelkezik egy, a forgatókönyvhöz előkészített üzembe helyezési jegyzékfájlval, a `deployment.json` fájlnak és az összes szükséges műveletnek ki kell választania egy eszközt az üzemelő példány fogadásához.

1. Nyissa meg **Cloud Explorer** kattintva **nézet** > **Cloud Explorer**. Győződjön meg arról, hogy bejelentkezett a Visual Studio 2019-ba.

1. A **Cloud Explorer**, bontsa ki az előfizetést, keresse meg az Azure IoT Hub és az Azure IoT Edge-eszköz számára telepíteni kívánja.

1. A központi telepítés létrehozásához kattintson a jobb gombbal a IoT Edge eszközre, és válassza ki a központi telepítési jegyzékfájlt `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Nem kell választania `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Kattintson a refresh (frissítés) gombra, és tekintse meg az új modulokat, amelyek a **tempSensor** modullal és a **$edgeAgent** és **$edgeHubval**együtt futnak.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Egy adott eszközhöz tartozó D2C-üzenet figyeléséhez jelölje ki az eszközt a listában, majd kattintson a **beépített esemény-végpont figyelése** elemre a **műveleti** ablakban.

1. A figyelési információk leállításához jelölje ki az eszközt a listában, majd a **művelet** ablakban válassza a **figyelés a beépített esemény végpontjának leállítása** lehetőséget.

## <a name="next-steps"></a>További lépések

Egyéni modulok fejlesztése a IoT Edge-eszközökhöz, az [Azure IoT hub SDK-k megismerése és használata](../iot-hub/iot-hub-devguide-sdks.md).
