---
title: Modulok fejlesztése és hibakeresése a Visual Studióban – Azure IoT Edge
description: A Visual Studio és az Azure IoT Tools segítségével fejleszthet egy C vagy C# IoT Edge-modult, és lelökheti azt az IoT Hubról egy IoT-eszközre, egy központi telepítési jegyzékben konfigurálva.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384857"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>A Visual Studio 2019 segítségével fejleszthet és debugolhatozik az Azure IoT Edge moduljainak fejlesztéséhez és hibakereséséhez

Az üzleti logikát az Azure IoT Edge moduljaivá alakíthatja. Ez a cikk bemutatja, hogyan használhatja a Visual Studio 2019-et a modulok fejlesztésének és hibakeresésének fő eszközeként.

Az Azure IoT Edge Tools for Visual Studio a következő előnyöket nyújtja:

- Azure IoT Edge-megoldások at és modulokat hozhat létre, szerkeszt, hozhat létre, futtatést és hibakeresést a helyi fejlesztői számítógépen.
- Azure IoT Edge-es megoldását az Azure IoT Edge-eszközön az Azure IoT Hubon keresztül üzembe helyezheti.
- Az Azure IoT-modulokat C vagy C# nyelven kódolhatja, miközben a Visual Studio-fejlesztés minden előnyét élvezi.
- Azure IoT Edge-eszközök és -modulok kezelése a felhasználói felülettel.

Ez a cikk bemutatja, hogyan használhatja az Azure IoT Edge-eszközöket a Visual Studio 2019-hez az IoT Edge-modulok fejlesztéséhez. Azt is megtudhatja, hogyan telepítheti a projektet az Azure IoT Edge-eszközre. Jelenleg a Visual Studio 2019 támogatja a C és C# nyelven írt modulokat. A támogatott eszközarchitektúrák a Windows X64 és a Linux X64 vagy arm32. A támogatott operációs rendszerekről, nyelvekről és architektúrákról a [Nyelvi és architektúra támogatása](module-development.md#language-and-architecture-support)című témakörben talál további információt.
  
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy fejlesztői gépként Windows rendszert futtató számítógépet vagy virtuális gépet használ. Windows rendszerű számítógépeken Windows vagy Linux modulok at fejleszthet. A Windows-modulok fejlesztéséhez használja a 1809/build 17763-as vagy újabb verziójú Windows számítógépet. Linux-modulok fejlesztéséhez olyan Windows-számítógépet használjon, amely megfelel a [Docker Desktop követelményeinek.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Mivel ez a cikk a Visual Studio 2019-et használja fő fejlesztőeszközként, telepítse a Visual Studio alkalmazást. Győződjön meg arról, hogy a Visual Studio 2019-es telepítésébe beilleszti az **Azure-fejlesztést** és **az asztali fejlesztést C++ számítási feladatokból.** A [Visual Studio 2019 módosításával](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) hozzáadhatja a szükséges munkaterheléseket.

Miután a Visual Studio 2019 készen áll, a következő eszközökre és összetevőkre is szüksége van:

- Töltse le és telepítse az [Azure IoT Edge-eszközöket](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) a Visual Studio piactérről, és hozzon létre egy IoT Edge-projektet a Visual Studio 2019-ben.

> [!TIP]
> Ha a Visual Studio 2017-et használja, töltse le és telepítse az [Azure IoT Edge-eszközöket](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) a VS 2017-hez a Visual Studio piactérről

- Töltse le és telepítse a [Docker Community Edition-t](https://docs.docker.com/install/) a fejlesztőgépre a modullemezképek létrehozásához és futtatásához. Be kell állítania a Docker CE-t linuxos vagy Windows-tároló módban való futtatásra.

- Állítsa be a helyi fejlesztői környezetet az IoT Edge-megoldás hibakereséséhez, futtatásához és teszteléséhez az [Azure IoT EdgeHub fejlesztői eszköz](https://pypi.org/project/iotedgehubdev/)telepítésével. Telepítse a [Pythont (2.7/3.6+) és a Pip-et,](https://www.python.org/) majd telepítse az **iotedgehubdev** csomagot a következő parancs futtatásával a terminálon. Győződjön meg arról, hogy az Azure IoT EdgeHub fejlesztői eszköz verziója nagyobb, mint 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Klónozza a tárházat, és telepítse a Vcpkg könyvtárkezelőt, majd telepítse az **azure-iot-sdk-c csomagot** a Windows rendszerhez.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > A felhőbeállítás-beállításjegyzék helyett használhat egy helyi Docker-beállításjegyzéket prototípus- és tesztelési célokra.

- A modul teszteléséhez egy eszközön, szüksége lesz egy aktív IoT hub legalább egy IoT Edge-eszközzel. A számítógép IoT Edge-eszközként való használatához kövesse a [Linux](quickstart-linux.md) vagy [Windows](quickstart.md)gyorshasználatának lépéseit. Ha IoT Edge démont futtat a fejlesztői gépen, előfordulhat, hogy le kell állítania az EdgeHubot és az EdgeAgentet, mielőtt elkezdené a fejlesztést a Visual Studióban.

### <a name="check-your-tools-version"></a>Az eszközverzió ellenőrzése

1. A **Bővítmények** menüben válassza a **Bővítmények kezelése parancsot.** Bontsa ki **a Telepített > eszközöket,** és megtalálhatja **az Azure IoT Edge tools for Visual Studio** és a Cloud Explorer for Visual Studio **alkalmazást.**

1. Jegyezze fel a telepített verziót. Összehasonlíthatja ezt a verziót a Visual Studio Piactéren[(Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)legújabb verzióval.

1. Ha az Ön verziója régebbi, mint a Visual Studio Piactéren elérhető verzió, frissítse eszközeit a Visual Studióban a következő szakaszban látható módon.

### <a name="update-your-tools"></a>Az eszközök frissítése

1. A Bővítmények kezelése ablakban **bontsa** ki **a Frissítések > visual studio piactér csomópontját,** válassza az Azure **IoT Edge Tools** vagy a Cloud Explorer for Visual Studio alkalmazást, és válassza **a** **Frissítés**lehetőséget.

1. Az eszközök frissítésének letöltése után zárja be a Visual Studio alkalmazást, és indítsa el az eszközfrissítést a VSIX telepítővel.

1. A telepítőben válassza az **OK** gombot az indításhoz, majd a **Módosítás lehetőséget** az eszközök frissítéséhez.

1. A frissítés befejezése után válassza a **Bezárás** és a Visual Studio újraindítása lehetőséget.

### <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge-projekt létrehozása

Az Azure IoT Edge projektsablon a Visual Studio-ban létrehoz egy projektet, amely telepíthető az Azure IoT Edge-eszközök az Azure IoT Hubban. Először hozzon létre egy Azure IoT Edge-megoldást, majd hozza létre az első modult a megoldásban. Minden IoT Edge-megoldás több modult is tartalmazhat.

> [!TIP]
> A Visual Studio által létrehozott IoT Edge projektstruktúra nem ugyanaz, mint a Visual Studio-kódban.

1. A Visual Studio új projektpárbeszédében keressen rá az **Azure IoT Edge projektre,** és kattintson a **Tovább**gombra. A projekt konfigurációja ablakban adja meg a projekt nevét, adja meg a helyet, majd válassza a **Létrehozás lehetőséget.** Az alapértelmezett projektnév **az AzureIoTEdgeApp1.**

   ![Új projekt létrehozása](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Az **IoT Edge alkalmazás és modul hozzáadása** ablakban válassza a **C# modul** vagy a C **modul** lehetőséget, majd adja meg a modul nevét és a modulkép-tárházat. A Visual Studio automatikusan feltölti a modul nevét a **localhost:5000/<modulnevével.\>** Cserélje le a saját rendszerleíró adatbázisadataira. Ha egy helyi Docker-beállításjegyzéket használ a teszteléshez, akkor a **localhost** rendben van. Ha az Azure Container Registry, majd használja a bejelentkezési kiszolgálót a rendszerleíró adatbázis beállításait. A bejelentkezési kiszolgáló a ** _ \<rendszerleíró adatbázis "azurecr.io\>._** Csak a string **localhost:5000** részét cserélje le úgy, hogy a végeredmény a ** \< *rendszerleíró adatbázis nevére*\>(azurecr.io/_\<a modul nevére\>_**. Az alapértelmezett modulnév **IotEdgeModule1**

   ![Alkalmazás és modul hozzáadása](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Válassza **az OK gombot** az Azure IoT Edge-megoldás c# vagy C-t használó modullal való létrehozásához.

Most már rendelkezik egy **AzureIoTEdgeApp1.Linux.Amd64** projekttel vagy egy **AzureIoTEdgeApp1.Windows.Amd64** projekttel, valamint egy **IotEdgeModule1** projekttel a megoldásában. Minden **AzureIoTEdgeApp1** projekt `deployment.template.json` rendelkezik egy fájllal, amely meghatározza az IoT Edge-megoldáshoz létrehozandó és üzembe helyezni kívánt modulokat, valamint meghatározza a modulok közötti útvonalakat. Az alapértelmezett megoldás egy **SimulatedTemperatureSensor** modullal és egy **IotEdgeModule1** modullal rendelkezik. A **SimulatedTemperatureSensor** modul szimulált adatokat hoz létre az **IotEdgeModule1** modulhoz, míg az **IotEdgeModule1** modul alapértelmezett kódja közvetlenül az Azure IoT Hubnak érkező üzeneteket közvetíti.

A szimulált hőmérséklet-érzékelő működésének megtekintéséhez tekintse meg a [SimulatedTemperatureSensor.csproj forráskódot.](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)

Az **IotEdgeModule1** projekt egy .NET Core 2.1 konzolalkalmazás, ha C# modulról van szó. Tartalmazza a szükséges Docker-fájlokat, amelyek szükségesek az IoT Edge-eszköz windowsos vagy Linux-tárolóval futó futtatásához. A `module.json` fájl egy modul metaadatait írja le. A tényleges modulkód, amely az Azure IoT Device SDK-t `main.c` függőségként veszi fel, a vagy a `Program.cs` fájlban található.

## <a name="develop-your-module"></a>A modul fejlesztése

A megoldáshoz tartozó alapértelmezett modulkód az **IotEdgeModule1** > **Program.cs** (C#) vagy **a main.c** (C) elemben található. A modul `deployment.template.json` és a fájl úgy van beállítva, hogy a megoldást elkészítheti, leküldheti a tároló rendszerleíró adatbázisába, és telepítheti egy eszközre, hogy bármilyen kód érintése nélkül elkezdhesse a tesztelést. A modul úgy készült, hogy egy forrásból (ebben az esetben az adatokat szimuláló **SimulatedTemperatureSensor** modulból) és az Azure IoT Hubra való átadására szolgál.

Ha készen áll a modulsablon testreszabására a saját kódjával, az [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) segítségével olyan modulokat hozhat létre, amelyek megfelelnek az IoT-megoldások, például a biztonság, az eszközkezelés és a megbízhatóság kulcsfontosságú igényeinek.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicializálása iotedgehubdev ioT Edge eszköz kapcsolati karakterlánccal

1. Másolja az IoT Edge-eszközök kapcsolati karakterláncát az **elsődleges kapcsolati karakterláncból** a Visual Studio Cloud Explorerben. Ügyeljen arra, hogy ne másolja a kapcsolati karakterlánc egy nem-Edge-eszköz, mivel az IoT Edge-eszköz ikonja eltér a nem Edge-eszköz ikonjától.

   ![Szegélyeszköz-kapcsolati karakterláncának másolása](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Az **Eszközök menüben** válassza az **Azure IoT Edge Tools** > **Setup IoT Edge Simulator parancsot,** illessze be a kapcsolati karakterláncot, majd kattintson az **OK**gombra.

   ![Szegélykapcsolati karakterlánc-készlet ablakának megnyitása](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Írja be a kapcsolati karakterláncot az első lépésből, majd válassza az **OK gombot.**

> [!NOTE]
> Ezeket a lépéseket csak egyszer kell végrehajtania a fejlesztői számítógépen, mivel az eredmények automatikusan érvénybe lépnek az összes későbbi Azure IoT Edge-megoldásra. Ez az eljárás ismét követhető, ha másik kapcsolati karakterláncra szeretne váltani.

## <a name="build-and-debug-single-module"></a>Egyetlen modul létrehozása és hibakeresése

Általában érdemes tesztelni és hibakeresést minden modul futtatása előtt egy teljes megoldás több modult.

1. A **Megoldáskezelőben**kattintson a jobb gombbal az **IotEdgeModule1 elemre,** és válassza a helyi menü **Set as StartUp Project** parancsát.

   ![Indítási projekt beállítása](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Nyomja meg **az F5 gombot,** vagy kattintson az alábbi gombra a modul futtatásához; az első alkalommal&ndash;10 20 másodpercet vehet igénybe.

   ![Modul futtatása](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Ha a modul inicializálása sikeresen megtörtént, meg kell jelennie a .NET Core konzolalkalmazás indításának.

   ![A modul fut](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Ha c#-ban fejlődik ki, `PipeMessage()` állítson be egy töréspontot a **függvényben Program.cs;** ha C-t használ, állítson be töréspontot a `InputQueue1Callback()` **main.c**függvényben. Ezután tesztelheti egy üzenet elküldésével a következő parancsot egy **Git Bash** vagy **WSL Bash** shell. (A `curl` parancs nem futtatható PowerShell ből vagy parancssorból.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Hibakeresés egyetlen modul](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    A töréspontot be kell indítani. A változókat a Visual Studio **Locals** ablakban nézheti meg.

   > [!TIP]
   > [A postai](https://www.getpostman.com/) vagy más API-eszközökkel `curl`is küldhet üzeneteket a helyett.

1. Nyomja **le a Ctrl+ F5** billentyűkombinációt, vagy kattintson a leállítás gombra a hibakeresés leállításához.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>IoT Edge-megoldás létrehozása és hibakeresése több modullal

Miután befejezte az egyetlen modul fejlesztését, érdemes lehet futtatni és hibakeresést végezni egy teljes megoldás több modullal.

1. A **Solution Explorer**ben adjon hozzá egy második modult a megoldáshoz, kattintson a jobb gombbal az **AzureIoTEdgeApp1** elemre, és válassza az**Új IoT Edge-modul** **hozzáadása** > parancsot. A második modul alapértelmezett neve **IotEdgeModule2,** és egy másik csőmodulként fog működni.

1. Nyissa meg `deployment.template.json` a fájlt, és látni **fogja, hogy az IotEdgeModule2** hozzáadva a **modulok** szakaszban. Cserélje le az **útvonalak** szakaszt a következőkre. Ha testre szabta a modulneveket, frissítse ezeket a neveket a megfelelően.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Kattintson a jobb gombbal **az AzureIoTEdgeApp1 elemre,** és válassza a helyi menü **Set as StartUp Project** parancsát.

1. Hozza létre a töréspontokat, majd nyomja **le az F5** billentyűt több modul egyidejű futtatásához és hibakereséséhez. Több .NET Core konzolalkalmazás-ablaknak kell megjelennie, amelyek mindegyike más-más modult jelöl.

   ![Több modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Nyomja **le a Ctrl+ F5** billentyűkombinációt, vagy a hibakeresés leállításához kattintson a stop gombra.

## <a name="build-and-push-images"></a>Képek létrehozása és leküldése

1. Győződjön meg arról, hogy az **AzureIoTEdgeApp1** az induló projekt. Válassza a **Debug** vagy a **Release** lehetőséget a modulképekhez létrehozandó konfigurációként.

    > [!NOTE]
    > A **Hibakeresés kiválasztásakor** `Dockerfile.(amd64|windows-amd64).debug` a Visual Studio docker-lemezképek készítését használja. Ez magában foglalja a .NET Core parancssori hibakereső VSDBG a tároló rendszerkép létrehozása közben is. Éles használatra kész IoT Edge-modulok esetén **Release** azt javasoljuk, `Dockerfile.(amd64|windows-amd64)` hogy használja a Release konfigurációt, amely VSDBG nélkül használható.

1. Ha egy privát beállításjegyzéket használ, például az Azure Container Registry (ACR), a következő Docker-paranccsal jelentkezzen be.  A felhasználónevet és a jelszót a beállításjegyzék **Access keys** lapjáról az Azure Portalon szerezheti be. Ha helyi rendszerleíró adatbázist használ, [futtathat egy helyi rendszerleíró adatbázist.](https://docs.docker.com/registry/deploying/#run-a-local-registry)

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Ha olyan privát rendszerleíró adatbázist használ, mint az Azure Container Registry, hozzá kell adnia `deployment.template.json`a rendszerleíró adatbázis bejelentkezési adatait a fájlban található futásidejű beállításokhoz. Cserélje le a helyőrzőket a tényleges ACR rendszergazdai felhasználónevére, jelszavára és beállításjegyzékére.

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

1. A **Solution Explorer**ben kattintson a jobb gombbal az **AzureIoTEdgeApp1** elemre, és válassza az **IoT Edge-modulok összeállítása és leküldése parancsot** a Docker-rendszerkép létrehozásához és lenyomásához az egyes modulokhoz.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. A modulokat a Visual Studio Felhőkezelőjével is telepítheti. Már rendelkezik egy központi telepítési jegyzékfájl előkészített a forgatókönyv, a `deployment.json` fájl, és mindössze annyit kell tennie, hogy válasszon ki egy eszközt, hogy megkapja a központi telepítést.

1. A **Cloud Explorer** megnyitásához kattintson a**Felhőkezelő** **megtekintése** > gombra. Győződjön meg arról, hogy bejelentkezett a Visual Studio 2019-be.

1. A **Cloud Explorerben**bontsa ki az előfizetését, keresse meg az Azure IoT Hubot és az üzembe helyezni kívánt Azure IoT Edge-eszközt.

1. Kattintson a jobb gombbal az IoT Edge-eszközre egy központi telepítés létrehozásához. Keresse meg a Visual Studio-megoldás **konfigurációs** mappájában található platformhoz `deployment.arm32v7.json`konfigurált telepítési jegyzéket, például .

1. Kattintson a frissítés gombra a **SimulatedTemperatureSensor** modullal és a **$edgeAgent** és **$edgeHub**együtt futó új modulok megtekintéséhez.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Egy adott IoT-Edge-eszköz D2C-üzenetének figyeléséhez jelölje ki az IoT-központban a **Cloud Explorerben,** majd kattintson a **Beépített eseményvégpont indítása** elemre a **Művelet** ablakban.

1. Az adatok figyelésének leállításához válassza a Beépített eseményvégpont leállítása lehetőséget a **Művelet** **ablakban.**

## <a name="next-steps"></a>További lépések

Egyéni modulok fejlesztéséhez az IoT Edge-eszközökhöz, [ismerje meg és használja az Azure IoT Hub SDK-k.](../iot-hub/iot-hub-devguide-sdks.md)
