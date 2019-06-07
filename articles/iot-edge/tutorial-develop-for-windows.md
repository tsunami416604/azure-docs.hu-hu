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
ms.openlocfilehash: 81d660857eff63e0dfeeda400b168ea424152081
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808601"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Oktatóanyag: IoT Edge-modulok létrehozása Windows-eszközökhöz

A Visual Studio használatával fejlesztése és kód üzembe helyezése IoT Edge rendszerű Windows-eszközökhöz.

A rövid útmutatóban létrehozott Windows virtuális gépek használatával IoT Edge-eszköz és a egy előre elkészített modul az Azure Marketplace-ről üzembe helyezett. Ez az oktatóanyag végigvezeti a Mi szükséges fejlesztése és üzembe helyezése IoT Edge-eszköz saját kódot. Ebben az oktatóanyagban hasznos előfeltétele a ismertető többi, amely a részletes információkat olvashat a konkrét programozási nyelvet, vagy az Azure-szolgáltatásokkal. 

Ebben az oktatóanyagban üzembe helyezése a példa egy **C modult egy Windows eszköz**. Ebben a példában az egyszerűség kedvéért lett választva, így megismerheti a Fejlesztőeszközök anélkül, hogy van-e telepítve a megfelelő kódtárak foglalkoznia. Miután megismerkedett a fejlesztői fogalmak, majd kiválaszthatja a választott nyelven vagy az Azure-szolgáltatás a részletek áttekintése. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a fejlesztői gépére.
> * Az IoT Edge-eszközök a Visual Studio használatával hozzon létre egy új projektet.
> * Tárolójaként a projekt buildjének elkészítéséhez, és a egy Azure container registry tárolja.
> * A kód üzembe helyezése IoT Edge-eszköz. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Fő fogalmak

Ez az oktatóanyag végigvezeti egy IoT Edge-modul fejlesztését. Egy *IoT Edge-modul*, vagy közvetlenül az egyes esetekben *modul* röviden, van egy tároló, amely végrehajtható kódot tartalmaz. Telepíthet egy vagy több modulja az IoT Edge-eszköz. Modulok az érzékelők, a data analytics vagy adatokat tisztítási műveleteket, illetve üzenetek küldése az IoT hub mint adatok feldolgozására feladatok végrehajtására. További információkért lásd: [megismerheti az Azure IoT Edge-modulok](iot-edge-modules.md).

IoT Edge-modulok fejlesztésekor fontos a fejlesztői gépen, és a cél, ahol a modul végül telepíti az IoT Edge-eszköz közötti különbségek megértése. A tároló, amely hoz létre, amely tárolja a modul kódja meg kell egyeznie az operációs rendszer (OS), a *céleszköz*. Windows-tároló fejlesztéshez a fogalom azért egyszerűbb Windows-tárolók csak a Windows operációs rendszereken futtatható. Azonban, például használhatja a Windows fejlesztői gépére hozhat létre Linux IoT Edge-eszközök modulokat. Ebben az esetben azt kellene győződjön meg arról, hogy a fejlesztői gépén futott-e a Linux-tárolókat. Ez az oktatóanyag lépéseinek követése közben tartsa szem előtt a különbség a között *fejlesztői gépen az operációs rendszer* és a *tároló OS*.

Ez az oktatóanyag az IoT Edge futtató Windows-eszközök célozza. Windows IoT Edge-eszközök a Windows-tárolók használata. Azt javasoljuk, hogy az milyen ebben az oktatóanyagban használja a Windows-eszközök esetén fejleszthet a Visual Studio használatával. Használhatja a Visual Studio Code, bár a támogatás a két eszköz közötti különbségek vannak.

A következő táblázat felsorolja a támogatott fejlesztési forgatókönyvek **Windows-tárolók** a Visual Studio Code és a Visual Studióban.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-szolgáltatások** | Azure Functions <br> Azure Stream Analytics |   |
| **Nyelvek** | C#(hibakeresés nem támogatott) | C <br> C# |
| **További információ** | [A Visual Studio Code az Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools), [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

Ebben az oktatóanyagban a fejlesztési lépések a Visual Studio 2019 azzal foglalkozunk. Ha a Visual Studio Code inkább használna, tekintse meg a következő témakör utasításait [használja a Visual Studio Code fejlesztésről és hibakeresésről modulok az Azure IoT Edge](how-to-vs-code-develop-module.md). Ha a Visual Studio 2017 (15.7 vagy újabb verzió) használ, plrease töltse le és telepítse [Azure IoT Edge-eszközök Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

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

A Visual Studio 2019 IoT-bővítmények használatával IoT Edge-modulok fejlesztését. Ezek a bővítmények projektsablonjai találhatók meg, manifest nasazení automatizálhatja és figyelheti és kezelheti az IoT Edge-eszközök lehetővé teszik. Ebben a szakaszban a Visual Studio és az IoT Edge-bővítmény telepítése, majd állítsa be az Azure-fiókjával, Visual Studión belül az IoT Hub-erőforrások kezeléséhez. 

1. Ha még nem rendelkezik a fejlesztői gépen, a Visual Studio [telepítse a Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) a következő számítási feladatokkal: 

   * Azure-fejlesztés
   * Asztali fejlesztésC++
   * .NET Core platformfüggetlen fejlesztés

1. Ha már rendelkezik Visual Studio 2019 a fejlesztői gépen. Kövesse a [módosítása a Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) hozzáadása a szükséges alkalmazásokat, ha már nincs rájuk.

2. Töltse le és telepítse a [Azure IoT Edge-eszközök](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítmény a Visual Studio 2019. 

3. Ha a telepítés befejeződött, nyissa meg a Visual Studio 2019 és **kód nélküli folytatás**.

4. Válassza ki **nézet** > **Cloud Explorer**. 

5. A cloud Explorerben válassza ki a profil ikonjára, és jelentkezzen be az Azure-fiókjával, ha nincs bejelentkezve már. 

6. Miután bejelentkezik, az Azure-előfizetések jelennek meg. Válassza ki az előfizetést, a cloud explorer keresztül férnek hozzá, és válassza ki a kívánt **alkalmaz**. 

7. Bontsa ki az előfizetést, majd **IoT-központok**, majd az IoT hubnak. Az IoT-eszközök listájának kell megjelennie, és ez explorer segítségével felügyelheti azokat. 

   ![A Cloud Explorerben az IoT Hub-erőforrások eléréséhez](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Új modul-projekt létrehozása

Az Azure IoT Edge-eszközök bővítményt biztosít projektsablonok az összes támogatott IoT Edge modul nyelveken a Visual Studióban. Ezek a sablonok rendelkezik a fájlok és a kódot, amely üzembe kell helyeznie egy működő modul tesztelheti az IoT Edge, vagy a saját üzleti logikája a sablon testreszabásához kiindulási pontot biztosítanak. 

1. Válassza ki **fájl** > **új** > **projekt...**

2. Az új projekt ablakban, 2. Az új projekt ablakról, keressen **IoT Edge** projektre, és válassza ki a **Azure IoT Edge (Windows-amd64)** projekt. Kattintson a **tovább**. 

   ![Új Azure IoT Edge-projekt létrehozása](./media/tutorial-develop-for-windows/new-project.png)

3. Konfigurálása az új projekt ablakról, nevezze át a projekt és a megoldás leíró valami hasonló **CTutorialApp**. Kattintson a **létrehozás** a projekt létrehozásához.

   ![Egy új Azure IoT Edge-projekt konfigurálása](./media/tutorial-develop-for-windows/configure-project.png)
 

4. Az IoT Edge-alkalmazás és a modul ablakban, a projekt konfigurálásához a következő értékeket: 

   | Mező | Érték |
   | ----- | ----- |

   | Válasszon ki egy sablont |} Válassza ki **C modul**. | | Modulnév projekt |} Fogadja el az alapértelmezett **IoTEdgeModule1**. | | Docker-rendszerkép tárház |} Egy lemezképtárban tartalmazza a tárolóregisztrációs adatbázis nevét és a tároló rendszerképének nevét. A tároló rendszerképének előre van töltve, a modul projekt neve értékből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/iotedgemodule1. |

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
   ```

4. Mentse a deployment.template.json fájlt. 

### <a name="review-the-sample-code"></a>Tekintse át a mintakódot

A megoldássablon, létrehozott egy IoT Edge-modul a mintakód tartalmazza. Ez a minta modul egyszerűen fogadja az üzeneteket, és továbbítja őket. Adatfolyamat-funkciókat mutatja be, hogy egyik fontos vonása, az IoT Edge segítségével, amely hogyan modulok kommunikálnak egymással.

Minden modul rendelkezhet több *bemeneti* és *kimeneti* üzenetsorok deklarálva a kódra összpontosítsanak. Az IoT Edge hub az eszközön futó üzenetirányítást végez egy modul kimenetéből származó be egy vagy több modul bemenetével. Az adott nyelvhez tartozó bemenetek és kimenetek deklaráló nyelvek közé esik, de a koncepció azonos több összes modulban. További információ a modulok közötti útválasztás: [útvonalak deklarálja](module-composition.md#declare-routes).

1. Az a **main.c** fájlt és keresse meg a **SetupCallbacksForModule** függvény.

2. Ez a függvény beállítja egy bemeneti üzenetsor bejövő üzenetek fogadására. Meghívja a C SDK modul ügyfél függvény [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback). Ezt a funkciót, és megnézheti, hogy egy bemeneti várólista nevű inicializálja **input1**. 

   ![A bemeneti név található a SetInputMessageCallback konstruktor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Ezt követően keresse meg a **InputQueue1Callback** függvény.

4. A függvény feldolgozza a kapott üzeneteket, és adja meg azokat a mentén úgy állít be egy kimeneti várólista. Meghívja a C SDK modul ügyfél függvény [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync). Ezt a funkciót, és megnézheti, hogy egy kimeneti várólista nevű inicializálja **output1**. 

   ![Keresse meg a kimeneti név a SendEventToOutputAsync konstruktorban](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Nyissa meg a **deployment.template.json** fájlt.

6. Keresse meg a **modulok** a $edgeAgent tulajdonságát kívánt tulajdonságot. 

   Az itt felsorolt két modult kell lennie. Az első **tempSensor**, amely az összes sablon, amellyel tesztelheti a modulok szimulált hőmérsékleti adatokat alapértelmezés szerint tartalmazza. A második pedig a **IotEdgeModule1** modul, amely a projekt részeként létrehozott.

   A modulok tulajdonság deklarálja, hogy melyik modulokat kell foglalni az üzembe helyezés az eszközre vagy eszközökre. 

7. Keresse meg a **útvonalak** a $edgeHub tulajdonságát kívánt tulajdonságot. 

   Az egyik a functions az IoT Edge hubot modul esetén a központi telepítés összes moduljai közötti üzenetek továbbítását. Tekintse át az útvonalak tulajdonság értékei. Az első útvonal **IotEdgeModule1ToIoTHub**, használja a helyettesítő karaktert ( **\*** ) bármely a IoTEdgeModule1 modul kimeneti várólista-ből érkező bármely üzenet tartalmazza. Ezeket az üzeneteket lépnek *felső $* , amely, amely azt jelzi, hogy az IoT Hub foglalt név. A második útvonal **sensorToIotEdgeModule1**, vesz igénybe a tempSensor modulból érkező üzeneteket, és átirányítja őket a *input1* IotEdgeModule1 modul bemeneti várólistát. 

   ![Tekintse át a deployment.template.json útvonalak](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>És a megoldás leküldéses

Áttekintette a modul kódot és a központi telepítési sablon üzembe helyezés fő fogalmak megértéséhez. Most már készen áll a IotEdgeModule1 tárolólemezkép létrehozásához, hogy a tárolóregisztrációs adatbázisba. A Visual Studio eszközök IoT-bővítmény, az ebben a lépésben is nyújtanak a sablonfájlt, valamint a megoldásfájlok modul információi alapján manifest nasazení állít elő. 

### <a name="sign-in-to-docker"></a>Jelentkezzen be a

Adja meg a tároló a tárolójegyzék hitelesítő adatainak a Docker a fejlesztői gépen, hogy, leküldheti a tárolórendszerképet a beállításjegyzék tárolja. 

1. Nyissa meg a PowerShell vagy az parancssor.

2. Jelentkezzen be a Docker mentette a beállításjegyzék létrehozását követően az Azure container registry hitelesítő adataival. 

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

### <a name="troubleshoot"></a>Hibaelhárítás

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

Ebben az oktatóanyagban a Visual Studio 2019 beállítása a fejlesztői gépen, és abból az első IoT Edge-modul telepítve. Most, hogy már ismeri az alapfogalmakat, próbálja meg funkció hozzáadása egy modult, hogy azt is elemezheti az adatokat, akkor továbbítja. Válassza ki a kívánt nyelvet: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
