---
title: Windows-eszközök – Azure IoT Edge modul fejlesztése |} A Microsoft Docs
description: Ez az oktatóanyag végigvezeti a fejlesztési számítógép és a felhőalapú erőforrások beállítása a Windows-tárolók használatával a Windows-eszközök IoT Edge-modulok fejlesztése
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/20/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1f2e9bc93b8bea70a58f2e6a544e2088505935a9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239761"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Oktatóanyag: Fejlesztés az IoT Edge-modulok Windows-eszközökhöz

Visual Studio 2017 használatával fejlesztése és kód üzembe helyezése IoT Edge rendszerű Windows-eszközökhöz.

A rövid útmutatóban létrehozott Windows virtuális gépek használatával IoT Edge-eszköz és a egy előre elkészített modul az Azure Marketplace-ről üzembe helyezett. Ez az oktatóanyag végigvezeti a Mi szükséges fejlesztése és üzembe helyezése IoT Edge-eszköz saját kódot. Ebben az oktatóanyagban hasznos előfeltétele a ismertető többi, amely a részletes információkat olvashat a konkrét programozási nyelvet, vagy az Azure-szolgáltatásokkal. 

Ebben az oktatóanyagban üzembe helyezése a példa egy **C modult egy Windows eszköz**. Ebben a példában az egyszerűség kedvéért lett választva, így megismerheti a Fejlesztőeszközök anélkül, hogy van-e telepítve a megfelelő kódtárak foglalkoznia. Miután megismerkedett a fejlesztői fogalmak, majd kiválaszthatja a választott nyelven vagy az Azure-szolgáltatás a részletek áttekintése. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a fejlesztői gépére.
> * Az IoT Edge-eszközök Visual Studio 2017 használatával hozzon létre egy új projektet.
> * Tárolójaként a projekt buildjének elkészítéséhez, és a egy Azure container registry tárolja.
> * A kód üzembe helyezése IoT Edge-eszköz. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Fő fogalmak

Ez az oktatóanyag végigvezeti egy IoT Edge-modul fejlesztését. Egy *IoT Edge-modul*, vagy közvetlenül az egyes esetekben *modul* röviden, van egy tároló, amely végrehajtható kódot tartalmaz. Telepíthet egy vagy több modulja az IoT Edge-eszköz. Modulok az érzékelők, a data analytics vagy adatokat tisztítási műveleteket, illetve üzenetek küldése az IoT hub mint adatok feldolgozására feladatok végrehajtására. További információkért lásd: [megismerheti az Azure IoT Edge-modulok](iot-edge-modules.md).

IoT Edge-modulok fejlesztésekor fontos a fejlesztői gépen, és a cél, ahol a modul végül telepíti az IoT Edge-eszköz közötti különbségek megértése. A tároló, amely hoz létre, amely tárolja a modul kódja meg kell egyeznie az operációs rendszer (OS), a *céleszköz*. Windows-tároló fejlesztéshez a fogalom azért egyszerűbb Windows-tárolók csak a Windows operációs rendszereken futtatható. Azonban, például használhatja a Windows fejlesztői gépére hozhat létre Linux IoT Edge-eszközök modulokat. Ebben az esetben azt kellene győződjön meg arról, hogy a fejlesztői gépén futott-e a Linux-tárolókat. Ez az oktatóanyag lépéseinek követése közben tartsa szem előtt a különbség a között *fejlesztői gépen az operációs rendszer* és a *tároló OS*.

Ez az oktatóanyag az IoT Edge futtató Windows-eszközök célozza. Windows IoT Edge-eszközök a Windows-tárolók használata. Azt javasoljuk, hogy az milyen ebben az oktatóanyagban használja a Windows-eszközök esetén fejleszthet a Visual Studio 2017 használatával. Használhatja a Visual Studio Code, bár a támogatás a két eszköz közötti különbségek vannak.

A következő táblázat felsorolja a támogatott fejlesztési forgatókönyvek **Windows-tárolók** a Visual Studio Code és a Visual Studio 2017-ben.

|   | Visual Studio Code | Visual Studio 2017 |
| - | ------------------ | ------------------ |
| **Azure-szolgáltatások** | Azure Functions <br> Azure Stream Analytics |   |
| **Nyelvek** | C#(hibakeresés nem támogatott) | C <br> C# |
| **További információ** | [A Visual Studio Code az Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) |

Ebben az oktatóanyagban a fejlesztési lépések a Visual Studio 2017 azzal foglalkozunk. Ha a Visual Studio Code inkább használna, tekintse meg a következő témakör utasításait [használja a Visual Studio Code fejlesztésről és hibakeresésről modulok az Azure IoT Edge](how-to-vs-code-develop-module.md).

## <a name="prerequisites"></a>Előfeltételek

A fejlesztői gépen:

* A Windows 10-es 1809 frissítéssel vagy újabb.
* Használhatja a saját számítógépén vagy virtuális gépként, attól függően, a fejlesztői beállítások.
* Telepítse a [Git](https://git-scm.com/) szoftvert. 
* Telepítse az Azure IoT C SDK for Windows x64 vcpkg keresztül:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

<!--vcpkg only required for C development-->

Egy Windows Azure IoT Edge-eszközön:

* Azt javasoljuk, hogy nem futnak az IoT Edge, a fejlesztői gépen, de Ehelyett használjon külön eszközt. A fejlesztési számítógép és az IoT Edge-eszköz további megkülönböztetése pontosan egy igaz üzembe helyezési forgatókönyv tükrözi, és segít abban, hogy közvetlenül a különféle fogalmakat.
* Ha nem rendelkezik elérhető egy második eszköz, a rövid útmutató cikk segítségével IoT Edge-eszköz létrehozása az Azure-ban egy [Windows virtuális gép](quickstart.md).

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

## <a name="install-container-engine"></a>Container-motor telepítése

IoT Edge-modulok vannak csomagolva, így egy tároló-motor a fejlesztői gépen való létrehozásához és a tárolók felügyeletéhez szükséges. Azt javasoljuk, hogy asztallal Docker-fejlesztéshez számos funkciók és a egy tároló motorként népszerűsége miatt. Egy Windows-számítógépen a Docker desktoppal válthat Linux-tárolók és a Windows-tárolók közötti, hogy a modulok IoT Edge-eszközök különböző típusú könnyedén fejleszthet. 

A fejlesztői gépen telepítéséhez használja a Docker – dokumentáció: 

* [A Windows Docker Desktop telepítése](https://docs.docker.com/docker-for-windows/install/)

  * Docker asztali Windows telepítésekor kéri, hogy szeretné-e a Linux vagy Windows-tárolók használata. A jelen oktatóanyag esetében használja **Windows-tárolók**. További információkért lásd: [Windows és Linux-tárolók közötti váltás](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>A Visual Studio és az eszközök beállítása

A Visual Studio 2017 IoT-bővítmények használatával IoT Edge-modulok fejlesztését. Ezek a bővítmények projektsablonjai találhatók meg, manifest nasazení automatizálhatja és figyelheti és kezelheti az IoT Edge-eszközök lehetővé teszik. Ebben a szakaszban a Visual Studio és az IoT Edge-bővítmény telepítése, majd állítsa be az Azure-fiókjával, Visual Studión belül az IoT Hub-erőforrások kezeléséhez. 

1. Ha még nem rendelkezik a fejlesztői gépen, a Visual Studio [Visual Studio 2017 telepítése](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017) a következő számítási feladatokkal: 

   * Azure-fejlesztés
   * Asztali fejlesztésC++
   * .NET Core platformfüggetlen fejlesztés

1. Ha már rendelkezik Visual Studio 2017 fejlesztői gépen, győződjön meg arról, hogy a verziószáma 15.7 vagy újabb. Kövesse a [módosítása a Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) hozzáadása a szükséges alkalmazásokat, ha már nincs rájuk.

2. Töltse le és telepítse a [Azure IoT Edge-eszközök](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) bővítmény a Visual Studio 2017-ben. 

3. Ha a telepítés befejeződött, nyissa meg a Visual Studióban.

4. Válassza ki **nézet** > **Cloud Explorer**. 

5. A cloud Explorerben válassza ki a profil ikonjára, és jelentkezzen be az Azure-fiókjával, ha nincs bejelentkezve már. 

6. Miután bejelentkezik, az Azure-előfizetések jelennek meg. Válassza ki az előfizetést, a cloud explorer keresztül férnek hozzá, és válassza ki a kívánt **alkalmaz**. 

7. Bontsa ki az előfizetést, majd **IoT-központok**, majd az IoT hubnak. Az IoT-eszközök listájának kell megjelennie, és ez explorer segítségével felügyelheti azokat. 

   ![A Cloud Explorerben az IoT Hub-erőforrások eléréséhez](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Új modul-projekt létrehozása

Az Azure IoT Edge Tools bővítmény biztosít projektsablonok az összes támogatott IoT Edge modul nyelveken a Visual Studio 2017-ben. Ezek a sablonok rendelkezik a fájlok és a kódot, amely üzembe kell helyeznie egy működő modul tesztelheti az IoT Edge, vagy a saját üzleti logikája a sablon testreszabásához kiindulási pontot biztosítanak. 

1. Futtassa a Visual Studiót rendszergazdaként.

2. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget. 

3. Az új projekt ablakról, válassza ki a **Azure IoT** típus projektre, és válassza ki a **Azure IoT Edge** projekt. Nevezze át a projektre, és megoldást, vagy fogadja el az alapértelmezett **AzureIoTEdgeApp1**. A projekt létrehozásához válassza az **OK** lehetőséget. 

   ![Új Azure IoT Edge-projekt létrehozása](./media/tutorial-develop-for-windows/new-project.png)

4. Az IoT Edge-alkalmazás és a modul ablakban, a projekt konfigurálásához a következő értékeket: 

   | Mező | Érték |
   | ----- | ----- |
   | Alkalmazásplatform | Törölje a jelet **Linux Amd64**, és ellenőrizze **WindowsAmd64**. |
   | Sablonválasztás | Válassza ki **C modul**. | 
   | A modul projekt neve | Fogadja el az alapértelmezett **IoTEdgeModule1**. | 
   | Docker-rendszerkép tárház | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének előre van töltve, a modul projekt neve értékből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/iotedgemodule1. |

   ![A céleszközön, modultípus és tároló-beállításjegyzék-projekt konfigurálása](./media/tutorial-develop-for-windows/add-application-and-module.png)

5. Válassza ki **OK** alkalmazza a módosításokat. 

Ha az új projektet a Visual Studio ablakban betöltött, szánjon egy percet Ismerkedjen meg a létrehozott fájlokat: 

* Az IoT Edge-projektet nevezik **AzureIoTEdgeApp1.Windows.Amd64**.
    * A **modulok** mappa tartalmazza a projekt tartalmazza a modulok mutatókat tartalmaznak. Ebben az esetben csak IoTEdgeModule1 kell lennie. 
    * A **deployment.template.json** fájl egy olyan sablont hozhat létre egy manifest nasazení. A *manifest nasazení* egy fájl, amely pontosan melyik modulokat szeretne központilag telepített az eszközön, hogyan kell konfigurálni, valamint hogyan kommunikálhatnak egymással, és a felhő határozza meg. 
* Az IoT Edge-modul projektet nevezik **IoTEdgeModule1**.
    * A **main.c** fájl, amely együttműködik a projektsablon C modul kódot tartalmaz. Az alapértelmezett modul vesz igénybe a bemeneti forrásból, és átadja mentén az IoT hubnak. 
    * A **module.json** fájl fenntartási részleteit a modult, beleértve a teljes lemezkép tárházat rendszerképet verziót, és mely docker-fájlban használni minden támogatott platformhoz.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Az IoT Edge-ügynök a tárolójegyzék hitelesítő adatainak megadása

Az IoT Edge-futtatókörnyezet kell kérni a tárolórendszerképeket az IoT Edge-eszközön a beállításjegyzék hitelesítő adatait. Adja hozzá ezeket a hitelesítő adatokat a központi telepítési sablont. 

1. Nyissa meg a **deployment.template.json** fájlt.

2. Keresse meg a **registryCredentials** tulajdonság a $edgeAgent a kívánt tulajdonságok. 

3. Frissítse a hitelesítő adatait, ez a formátum a következő tulajdonság: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Review the sample code

The solution template that you created includes sample code for an IoT Edge module. This sample module simply receives messages and then passes them on. The pipeline functionality demonstrates an important concept in IoT Edge, which is how modules communicate with each other.

Each module can have multiple *input* and *output* queues declared in their code. The IoT Edge hub running on the device routes messages from the output of one module into the input of one or more modules. The specific language for declaring inputs and outputs varies between languages, but the concept is the same across all modules. For more information about routing between modules, see [Declare routes](module-composition.md#declare-routes).

1. In the **main.c** file, find the **SetupCallbacksForModule** function.

2. This function sets up an input queue to receive incoming messages. It calls the C SDK module client function [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback). Review this function and see that it initializes an input queue called **input1**. 

   ![Find the input name in the SetInputMessageCallback constructor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Next, find the **InputQueue1Callback** function.

4. This function processes received messages and sets up an output queue to pass them along. It calls the C SDK module client function [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync). Review this function and see that it initializes an output queue called **output1**. 

   ![Find the output name in the SendEventToOutputAsync constructor](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open the **deployment.template.json** file.

6. Find the **modules** property of the $edgeAgent desired properties. 

   There should be two modules listed here. The first is **tempSensor**, which is included in all the templates by default to provide simulated temperature data that you can use to test your modules. The second is the **IotEdgeModule1** module that you created as part of this project.

   This modules property declares which modules should be included in the deployment to your device or devices. 

7. Find the **routes** property of the $edgeHub desired properties. 

   One of the functions if the IoT Edge hub module is to route messages between all the modules in a deployment. Review the values in the routes property. The first route, **IotEdgeModule1ToIoTHub**, uses a wildcard character (**\***) to include any message coming from any output queue in the IoTEdgeModule1 module. These messages go into *$upstream*, which is a reserved name that indicates IoT Hub. The second route, **sensorToIotEdgeModule1**, takes messages coming from the tempSensor module and routes them to the *input1* input queue of the IotEdgeModule1 module. 

   ![Review routes in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## Build and push your solution

You've reviewed the module code and the deployment template to understand some key deployment concepts. Now, you're ready to build the IotEdgeModule1 container image and push it to your container registry. With the IoT tools extension for Visual Studio, this step also generates the deployment manifest based on the information in the template file and the module information from the solution files. 

### Sign in to Docker

Provide your container registry credentials to Docker on your development machine so that it can push your container image to be stored in the registry. 

1. Open PowerShell or a command prompt.

2. Sign in to Docker with the Azure container registry credentials that you saved after creating the registry. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy kap egy biztonsági figyelmeztetés használatát javasolja `--password-stdin`. Adott ajánlott üzemi forgatókönyvek esetén ajánlott, bár ebben az oktatóanyagban hatókörén kívül esik. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) hivatkozást.

### <a name="build-and-push"></a>És leküldéses

A fejlesztői gépén most már hozzáfér a tárolóregisztrációs adatbázisba, és az IoT Edge-eszközök túl lesz. Kapcsolja be a projekt kódot egy tárolórendszerképbe ideje. 

1. Kattintson a jobb gombbal a **AzureIotEdgeApp1.Windows.Amd64** projektmappát, és válassza ki **Build és a leküldéses IoT Edge-modulok**. 

   ![Hozhat létre, és küldje le az IoT Edge-modulok](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   A build és a leküldéses parancs három műveletet indítja el. Először létrehoz egy új mappát a megoldásban nevű **config** , amely társítja a teljes üzembe helyezési jegyzékfájl, beépített ki a központi telepítési sablont információk és egyéb megoldást. Másodszor, futtatásuk `docker build` hozhat létre a tárolórendszerképet a cél-architektúra a megfelelő docker-fájl alapján. Ezt követően futtatja `docker push` paranccsal küldje le a tároló-beállításjegyzék a lemezképtárból. 

   Ez a folyamat eltarthat néhány percig, először azonban gyorsabb legközelebb a parancsokat. 

2. Nyissa meg a **deployment.windows-amd64.json** az újonnan létrehozott konfigurációs fájlban. (A konfigurációs mappát nem szerepelhet a Visual Studio Megoldáskezelőjében. Ha ebben az esetben válassza ki a **minden fájl megjelenítése** a Megoldáskezelőben tálca ikonjára.)

3. Keresse meg a **kép** IotEdgeModule1 szakasz paraméter. Figyelje meg, hogy a rendszerkép tartalmazza a teljes lemezkép-tárunkat, ahol a nevét, verzióját és architektúra címke a module.json fájlból.

4. Nyissa meg a **module.json** IotEdgeModule1 mappában található fájl. 

5. Módosítsa a verziószámot a modul lemezképhez. (A verzió, nem a $schema-verzió.) Például növelni a patch verziószámot **0.0.2** , mintha egy kis javítás kellett tettük a modul kódban. 

   >[!TIP]
   >Modulverziók engedélyezze a verziókezeléshez, és tesztelheti az eszközök egy kis készletét a módosításokat az éles környezetbe frissítések telepítése előtt. Ha a modul verzió létrehozása és leküldése előtt nem nő, majd, felülírja a tárház a tárolóregisztrációs adatbázis. 

6. Mentse a módosításokat a module.json fájlt.

7. Kattintson a jobb gombbal a **AzureIotEdgeApp1.Windows.Amd64** mappa újra projektre, majd válassza ismét **hozhat létre, és küldje le az IoT Edge-modulok**. 

8. Nyissa meg a **deployment.windows-amd64.json** fájlt újra. Figyelje meg, hogy egy új fájlt nem jön létre, amikor a build és a leküldéses parancsot újra futott. Ugyanebben a fájlban, az elvégzett módosításoknak megfelelően megtörtént. A IotEdgeModule1 kép most már a 0.0.2 mutat a tároló verziója. Ez a változtatás manifest nasazení, hogyan állapítható meg, hogy van egy új verziója való lekérésére modulok IoT Edge-eszköz. 

9. Továbbá győződjön meg arról, mi volt a build és a leküldéses parancs, nyissa meg a [az Azure portal](https://portal.azure.com) , és keresse meg a tárolóregisztrációs adatbázisba. 

10. Válassza ki a tároló-beállításjegyzék **Tárházak** majd **iotedgemodule1**. Győződjön meg arról, hogy a lemezképet mindkét verziója lett leküldve a beállításjegyzékbe.

    ![Mindkét képen verziók megtekintése a tárolóregisztrációs adatbázis](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Az eszköz nem tudta a várt módon befejezni a szinkronizálást. A probléma megoldásának módjáról erre az üzenetre kattintva tájékozódhat.

Összeállításakor és a modul rendszerképének leküldése hibákat észlel, ha gyakran rendelkezik, hogy a fejlesztői gépen a Docker-konfigurációját. A következő ellenőrzések segítségével áttekintheti a konfigurációt: 

* Futtatta a `docker login` parancsot a tárolóregisztrációs adatbázis, amelyet másolt hitelesítő adatok használatával? Ezekkel a hitelesítő adatokkal jelentkezzen be az Azure-ban használt rétegében eltérőek. 
* A tároló-beállításjegyzékbe a helyes? Rendelkezik a megfelelő tárolóregisztrációs adatbázis nevét, és a megfelelő modulnév? Nyissa meg a **module.json** fájlban a IotEdgeModule1 ellenőrzéséhez. A tárház értékét így kell kinéznie  **\<beállításjegyzék neve\>.azurecr.io/iotedgemodule1**. 
* Ha egy másik nevet, mint a használt **IotEdgeModule1** a modul van ilyen nevű következetesek a megoldás?
* Fut a gépen ugyanolyan típusú, tárolók, amelyek fejleszt? Ebben az oktatóanyagban a Windows IoT Edge-eszközök esetében van, így a Visual Studio-fájloknak kell rendelkezniük a **windows-amd64** bővítmény és a Docker Desktop kell futnia a Windows-tárolók. 

## <a name="deploy-modules-to-device"></a>Eszköz modulok telepítése

Ellenőrizte, hogy a beépített tárolórendszerképek tárolása a tárolóregisztrációs adatbázisba, hogy legyen idő telepíthetők az eszközökre. Győződjön meg arról, hogy az IoT Edge-eszköz üzembe helyezéséig. 

1. A Cloud Explorer megnyitása a Visual Studióban, és bontsa ki az IoT hub adatait. 

2. Válassza ki a telepíteni kívánt eszköz nevét. Az a **műveletek** listáról válassza ki **hozzon létre Deployment**.

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-develop-for-windows/create-deployment.png)


3. A Fájlkezelőben keresse meg a konfigurációs mappát a projekthez, és válassza ki a **deployment.windows-amd64.json** fájlt. Ez a fájl gyakran található: `C:\Users\<username>\source\repos\AzureIotEdgeApp1\AzureIotEdgeApp1.Windows.Amd64\config\deployment.windows-amd64.json`

   Ne használja a deployment.template.json fájlt, amely nem rendelkezik a modul teljes képértékek azt. 

4. Bontsa ki a modulok az eszközén, a Cloud Explorerben az IoT Edge-eszköz részleteit.

5. Használja a **frissítése** gombra kattintva frissítheti az eszköz állapotát megtekintheti, hogy a tempSensor és IotEdgeModule1 modulok telepítve vannak az eszközt. 


   ![Az IoT Edge-eszközön futó modulok megjelenítése](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Üzenetek megtekintése az eszközről

A IotEdgeModule1 kód keresztül a bemeneti várólista-üzeneteket fogadja, és áthalad mentén őket a kimeneti várólista. Manifest nasazení deklarálva útvonalakat, amely átadott üzenetek a tempSensor IotEdgeModule1, majd IotEdgeModule1 üzeneteket az IoT Hub továbbítja. Az Azure IoT Edge-eszközök for Visual Studio lehetővé teszi üzenetek az IoT Hub, az egyes eszközökről származó beérkezéskor. 

1. A Visual Studio cloud explorer válassza ki az IoT Edge-eszköz központilag nevét. 

2. Az a **műveletek** menüjében válassza **Start beépített esemény végpont**.

3. Tekintse meg a **kimeneti** szakasz az IoT hub érkező üzenetek megtekintéséhez a Visual studióban. 

   Eltarthat néhány percig, mindkét modulok elindításához. Az IoT Edge-futtatókörnyezet kell fogadni az új manifest nasazení kérje le a tároló-futtatókörnyezet modul képeit, majd indítsa el minden egyes új modul. Ha Ön 

   ![A bejövő eszköz a felhőbe irányuló üzenetek megtekintése](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Változások megtekintése az eszközön

Ha meg szeretné tekinteni a magán az eszközön mi történik, használja a parancsokat ebben a szakaszban az IoT Edge-futtatókörnyezet és az eszközön futó modulok vizsgálhatja meg. 

Ez a szakasz parancsai számára az IoT Edge-eszköz, nem a fejlesztői gépére. Ha egy virtuális gépet az IoT Edge-eszközt használ, a csatlakozás most. Az Azure-ban nyissa meg a virtuális gép – áttekintés oldalra, és válassza **Connect** elérni a távoli asztali kapcsolatot. Nyissa meg az eszközön futtatandó parancsot vagy PowerShell-ablakot a `iotedge` parancsokat.

* Az eszközre telepített összes modulban megtekintheti, és ellenőrizze azok állapotát:

   ```cmd
   iotedge list
   ```

   Négy modult kell megjelennie: az IoT Edge-futtatókörnyezet két modulok, tempSensor és IotEdgeModule1. Mind a négy futtatásával kell felsorolni.

* Vizsgálja meg a naplókban az eszközspecifikus modul:

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge-modulok olyan kis-és nagybetűket. 

   A tempSensor és IotEdgeModule1 naplók meg kell jelennie az üzeneteket azok még feldolgozásra. A edgeAgent modul feladata a többi modulnak indítása, így olyan manifest nasazení megvalósításával kapcsolatos információkat, a naplók. Ha bármely modul nem szerepel a listán, vagy nem fut, a edgeAgent naplók valószínűleg a hibákat. A modulok és az IoT Hub közötti kommunikáció a edgeHub modul feladata. A modulok működik, és nem fut, de az üzeneteket az IoT hub érkező, az valószínűleg fog edgeHub naplók van-e a hibákat. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Visual Studio 2017 beállítása a fejlesztői gépen, és abból az első IoT Edge-modul telepítve. Most, hogy már ismeri az alapfogalmakat, próbálja meg funkció hozzáadása egy modult, hogy azt is elemezheti az adatokat, akkor továbbítja. Válassza ki a kívánt nyelvet: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)