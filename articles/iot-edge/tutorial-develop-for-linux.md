---
title: Linux-eszközök – Azure IoT Edge modul fejlesztése |} A Microsoft Docs
description: Ez az oktatóanyag végigvezeti a fejlesztési számítógép és a felhőalapú erőforrások beállítása a Linux-tárolók használata Linux rendszerű eszközök IoT Edge-modulok fejlesztése
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4ee3c0bf344520afee6b3a00eb49759ae1432949
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239772"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Oktatóanyag: A Linux rendszerű eszközök IoT Edge-modulok fejlesztése

Visual Studio Code használatával fejlesztése és kód üzembe helyezése IoT Edge futó Linux rendszerű eszközökre. 

A rövid útmutató cikkek létrehozott egy Linux virtuális gépek használatával IoT Edge-eszköz, és üzembe helyezett egy előre elkészített modul az Azure Marketplace-ről. Ez az oktatóanyag végigvezeti a Mi szükséges fejlesztése és üzembe helyezése IoT Edge-eszköz saját kódot. Ebben az oktatóanyagban hasznos előfeltétele a ismertető többi, amely a részletes információkat olvashat a konkrét programozási nyelvet, vagy az Azure-szolgáltatásokkal. 

Ebben az oktatóanyagban üzembe helyezése a példa egy **C modul egy Linux rendszerű eszközre**. Ebben a példában választotta, a legkevesebb előfeltételeket, mert így megismerheti a Fejlesztőeszközök anélkül, hogy van-e telepítve a megfelelő kódtárak foglalkoznia. Miután megismerkedett a fejlesztői fogalmak, majd kiválaszthatja a választott nyelven vagy az Azure-szolgáltatás a részletek áttekintése. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a fejlesztői gépére.
> * Az IoT Edge-eszközök a Visual Studio Code használatával hozzon létre egy új projektet.
> * Tárolójaként a projekt buildjének elkészítéséhez, és a egy Azure container registry tárolja.
> * A kód üzembe helyezése IoT Edge-eszköz. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Fő fogalmak

Ez az oktatóanyag végigvezeti egy IoT Edge-modul fejlesztését. Egy *IoT Edge-modul*, vagy közvetlenül az egyes esetekben *modul* röviden, van egy tároló, amely végrehajtható kódot tartalmaz. Telepíthet egy vagy több modulja az IoT Edge-eszköz. Modulok az érzékelők, a data analytics vagy adatokat tisztítási műveleteket, illetve üzenetek küldése az IoT hub mint adatok feldolgozására feladatok végrehajtására. További információkért lásd: [megismerheti az Azure IoT Edge-modulok](iot-edge-modules.md).

IoT Edge-modulok fejlesztésekor fontos a fejlesztői gépen, és a cél, ahol a modul végül telepíti az IoT Edge-eszköz közötti különbségek megértése. A tároló, amely hoz létre, amely tárolja a modul kódja meg kell egyeznie az operációs rendszer (OS), a *céleszköz*. Ha például a leggyakoribb eset olyan személy, szándékos volt, amelyekre az IoT Edge-es Linux rendszerű eszközök a Windows számítógépen modul fejlesztése. Ebben az esetben az operációs rendszer lenne Linux. Ez az oktatóanyag lépéseinek követése közben tartsa szem előtt a különbség a *fejlesztői gépen az operációs rendszer* és a *tároló OS*.

Ez az oktatóanyag az IoT Edge futtató Linux rendszerű eszközök célozza. Használhatja az előnyben részesített fejlesztési gép operációs rendszerének mindaddig, amíg a fejlesztői gépén Linux-tárolókat futtathat. Azt javasoljuk, hogy az milyen ebben az oktatóanyagban használja a Linux rendszerű eszközök esetében fejleszthet a Visual Studio Code használatával. Használhatja a Visual Studio 2017, bár a támogatás a két eszköz közötti különbségek vannak.

A következő táblázat felsorolja a támogatott fejlesztési forgatókönyvek **Linux-tárolók** a Visual Studio Code és a Visual Studio 2017-ben.

|   | Visual Studio Code | Visual Studio 2017 |
| - | ------------------ | ------------------ |
| **Linux rendszerű eszköz architektúra** | Linux-AMD64 <br> Linux ARM32 | Linux-AMD64 <br> Linux ARM32 |
| **Azure-szolgáltatások** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Nyelvek** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **További információ** | [A Visual Studio Code az Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) |

Ebben az oktatóanyagban a fejlesztési lépések a Visual Studio Code azzal foglalkozunk. Ha szeretné inkább használja a Visual Studio 2017, tekintse meg a következő témakör utasításait [használja a Visual Studio 2017 fejlesztésről és hibakeresésről modulok az Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Előfeltételek

A fejlesztői gépen:

* Használhatja a saját számítógépén vagy virtuális gépként, attól függően, a fejlesztői beállítások.
* A legtöbb operációs rendszer futtatható egy tároló-motor használható Linux rendszerű eszközök az IoT Edge-modulok fejlesztését. Ebben az oktatóanyagban egy Windows-számítógépet használ, de rámutat, ismert különbségek MacOS vagy Linux rendszeren. 
* Telepítés [Git](https://git-scm.com/), sablon csomagok modul lekérni az oktatóanyag későbbi részében.  

Az Azure IoT Edge-eszköz Linux rendszeren:

* Azt javasoljuk, hogy nem futnak az IoT Edge, a fejlesztői gépen, de Ehelyett használjon külön eszközt. A fejlesztési számítógép és az IoT Edge-eszköz további megkülönböztetése pontosan egy igaz üzembe helyezési forgatókönyv tükrözi, és segít abban, hogy közvetlenül a különféle fogalmakat.
* Ha nem rendelkezik elérhető egy második eszköz, a rövid útmutató cikk segítségével IoT Edge-eszköz létrehozása az Azure-ban egy [Linux rendszerű virtuális gép](quickstart-linux.md).

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

## <a name="install-container-engine"></a>Container-motor telepítése

IoT Edge-modulok vannak csomagolva, így egy tároló-motor a fejlesztői gépen való létrehozásához és a tárolók felügyeletéhez szükséges. Azt javasoljuk, hogy asztallal Docker-fejlesztéshez számos funkciók és a egy tároló motorként népszerűsége miatt. Egy Windows-eszközön Docker desktoppal válthat Linux-tárolók és a Windows-tárolók közötti, hogy a modulok IoT Edge-eszközök különböző típusú könnyedén fejleszthet. 

A fejlesztői gépen telepítéséhez használja a Docker – dokumentáció: 

* [A Windows Docker Desktop telepítése](https://docs.docker.com/docker-for-windows/install/)

  * Docker asztali Windows telepítésekor kéri, hogy szeretné-e a Linux vagy Windows-tárolók használata. Ezt a döntést egy egyszerű kapcsoló használatával bármikor módosítható. Ebben az oktatóanyagban használ Linux-tárolók, mert a modulok céloz meg Linux rendszerű eszközök. További információkért lásd: [Windows és Linux-tárolók közötti váltás](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Docker Desktop for Mac telepítése](https://docs.docker.com/docker-for-mac/install/)

* Olvasási [Docker CE](https://docs.docker.com/install/) telepítési információk számos Linux-platformokon.

## <a name="set-up-vs-code-and-tools"></a>A VS Code és eszközök beállítása

Az IoT-bővítmények a Visual Studio Code használata IoT Edge-modulok fejlesztéséhez. Ezek a bővítmények projektsablonjai találhatók meg, manifest nasazení automatizálhatja és figyelheti és kezelheti az IoT Edge-eszközök lehetővé teszik. Ebben a szakaszban a Visual Studio Code és az IoT-bővítmény telepítése, majd állítsa be az Azure-fiókjával a Visual Studio Code belül az IoT Hub-erőforrások kezeléséhez. 

1. Telepítés [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen. 

2. Miután a telepítés befejeződött, válassza ki a **nézet** > **bővítmények**. 

3. Keresse meg **Azure IoT-eszközök**, amelynek van ténylegesen gyűjteménye, amelyek segítségével bővítmények, használatba az IoT Hub és IoT-eszközök, valamint az IoT Edge-modulok fejlesztését. 

4. Válassza az **Install** (Telepítés) lehetőséget. Minden egyes belefoglalt bővítmény telepítését külön-külön. 

5. A bővítmények végrehajtása után telepíti, nyissa meg a parancskatalógust kiválasztásával **nézet** > **Parancskatalógus**. 

6. A parancskatalógus keresése és kiválasztása **Azure: Jelentkezzen be a**. Az utasításokat követve jelentkezzen be Azure-fiókjába. 

7. A parancskatalógus újra, keresése és kiválasztása **Azure IoT hubbal: Válassza ki az IoT Hub**. Kövesse az utasításokat követve válassza ki az Azure-előfizetés és az IoT hub. 

7. Nyissa meg a Visual Studio Code Explorerben szakaszában, vagy a ikon kiválasztásával a tevékenységsávon a bal oldali, vagy kiválasztásával **nézet** > **Explorer**. 

8. Bontsa ki a explorer szakasz alján a összecsukott **Azure IoT Hub-eszközök** menü. Az eszközök és az IoT Edge-eszközökön az IoT hubon keresztül a parancskatalógus kiválasztott társított kell megjelennie. 

   ![Az IoT hub eszközök megtekintése](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Új modul-projekt létrehozása

Az Azure IoT Tools bővítmény biztosít projektsablonok az összes támogatott IoT Edge modul nyelveken a Visual Studio Code-ban. Ezek a sablonok rendelkezik a fájlok és a kódot, amely üzembe kell helyeznie egy működő modul tesztelheti az IoT Edge, vagy a saját üzleti logikája a sablon testreszabásához kiindulási pontot biztosítanak. 

Ebben az oktatóanyagban használ a C modul sablon telepítéséhez szükséges legkevesebb előfeltételeket, mert. 

### <a name="create-a-project-template"></a>Hozzon létre egy projektet sablont

A Visual Studio Code parancskatalógus keresése és kiválasztása **Azure IoT Edge: Új IoT Edge-megoldás**. Kövesse az utasításokat, és a megoldás létrehozásához a következő értékeket használja: 

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **C modul**. |
   | Provide a module name (Modulnév megadása) | Fogadja el az alapértelmezett **SampleModule**. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének a neve, az előző lépésben megadott van töltve. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/samplemodule. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-develop-for-linux/image-repository.png)

Miután az új megoldásokat a Visual Studio Code-ablak betölti, szánjon egy percet Ismerkedjen meg a létrehozott fájlokat: 

* A **.vscode** mappa tartalmaz egy nevű fájlt **launch.json**, használt modulok hibakeresés.
* A **modulok** mappa tartalmaz egy mappát az egyes modulok, a megoldás. Már zajlik is, amely csak akkor **SampleModule**, vagy a modul nevét bármilyen, biztosította. A SampleModule mappa tartalmazza a fő program kódot, a modul metaadatai és több Docker-fájlt. 
* A **.env** fájl tárolja a hitelesítő adatokat a tárolóregisztrációs adatbázisba. Ezeket a hitelesítő adatokat az IoT Edge-eszköz megosztott módon, hogy hozzáférést kérni a tárolórendszerképeket. 
* A **deployment.debug.template.json** fájl és **deployment.template.json** fájlban vannak olyan sablonok, amelyek segítségével üzembe helyezési jegyzék létrehozásához. A *manifest nasazení* egy fájl, amely pontosan melyik modulokat szeretne központilag telepített az eszközön, hogyan kell konfigurálni, valamint hogyan kommunikálhatnak egymással, és a felhő határozza meg. A sablonfájlokat mutatók néhány értéket használja. A sablon ismeretekké igaz telepítési jegyzékfájl, amikor a mutatók felülírják más megoldásfájlok származó értékekkel. Keresse meg a két gyakori helyőrzőt a központi telepítési sablont: 

  * A beállításjegyzék hitelesítő adatait a szakaszban a címe autofilled a a megoldás létrehozásakor megadott információk. A felhasználónév és jelszó hivatkozhat a változók egy .env fájlban tárolja. Ez a lehetőség a biztonság érdekében a .env fájlból figyelmen kívül hagyja a git, de nem áll a központi telepítési sablont. 
  * A SampleModule szakaszban a tároló rendszerképét nem kitöltött annak ellenére, hogy a megoldás létrehozásakor megadott a lemezképtárból. A helyőrző mutat a **module.json** fájlt a SampleModule mappán belül. Ha ezt a fájlt, láthatja, hogy a lemezkép mezőt tartalmaz, a tárház, hanem a címke értéke, amely a verzió és a platform, a tároló áll. A verzió manuálisan is iterálni, a fejlesztési ciklus részeként, és a container platform, amely bevezettünk egy váltó később használata ebben a szakaszban választja. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Az IoT Edge-ügynök a tárolójegyzék hitelesítő adatainak megadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezet kell kérni a tárolórendszerképeket az IoT Edge-eszközön való ezeket a hitelesítő adatokat. 

Az IoT Edge bővítmény megpróbálja a tároló az Azure-ból a tárolójegyzék hitelesítő adatainak lekérése, és töltse ki őket a környezetben fájlban. Ellenőrizze, hogy ha a hitelesítő adatok már szerepelnek. Ha nem, most már hozzáadhatja őket:

1. Nyissa meg a **.env** fájlt a modul megoldásában. 
2. Adja hozzá a **felhasználónév** és **jelszó** értékeket, amelyeket az Azure container registry fájlból kimásolt.
3. Mentse a módosításokat a .env fájlt. 

### <a name="select-your-target-architecture"></a>Válassza ki a cél-architektúra

A Visual Studio Code jelenleg C modulok Linux AMD64- és Linux ARM32v7 eszközökhöz is fejleszthet. Kell választania a melyik architektúrát, az egyes megoldások célozza meg, mert, amely hatással van, hogyan épül fel a tárolót, és futtatja. Az alapértelmezett érték a Linux-AMD64. 

1. A parancskatalógus megnyitásához, és keressen rá a **Azure IoT Edge: Állítsa be alapértelmezett célplatform peremhálózati megoldás**, vagy válassza ki a parancsikont a oldalsó sáv az ablak alján. 

   ![Az oldalsó sáv architektúra ikonnal](./media/tutorial-develop-for-linux/select-architecture.png)

2. A parancskatalógus válassza ki a cél-architektúra a lehetőségek listájából. Ebben az oktatóanyagban használjuk egy Ubuntu virtuális gép legyen az IoT Edge-eszköz, így megtartja az alapértelmezett **amd64**. 

### <a name="review-the-sample-code"></a>Tekintse át a mintakódot

A megoldássablon, létrehozott egy IoT Edge-modul a mintakód tartalmazza. Ez a minta modul egyszerűen fogadja az üzeneteket, és továbbítja őket. Adatfolyamat-funkciókat mutatja be, hogy egyik fontos vonása, az IoT Edge segítségével, amely hogyan modulok kommunikálnak egymással.

Minden modul rendelkezhet több *bemeneti* és *kimeneti* üzenetsorok deklarálva a kódra összpontosítsanak. Az IoT Edge hub az eszközön futó üzenetirányítást végez egy modul kimenetéből származó be egy vagy több modul bemenetével. Az adott nyelvhez tartozó bemenetek és kimenetek deklaráló nyelvek közé esik, de a koncepció azonos több összes modulban. További információ a modulok közötti útválasztás: [útvonalak deklarálja](module-composition.md#declare-routes).

1. Nyissa meg a **main.c** fájlt, amely a tartalmát a **modulok/SampleModules/** mappát. 

2. Az IoT Hub C SDK-t használ, a függvény [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback) modul bemeneti üzenetsorok inicializálása. Keresés adott funkcióval main.c fájlon belül.

3. Tekintse át a SetInputMessageCallback függvény konstruktort, és tekintse meg, hogy egy bemeneti várólista neve **input1** inicializálva van a kódban. 

   ![Keresse meg a bemeneti névnek SetInputMessageCallback konstruktor](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Modul kimeneti üzenetsorokhoz hasonló módon inicializálása. Keresse meg a [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync) függvény a main.c fájlban. 

5. Tekintse át a SendEventToOutputAsync függvény konstruktort, és tekintse meg, hogy egy kimeneti várólista neve **output1** inicializálva van a kódban. 

   ![A kimeneti név található SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Nyissa meg a **deployment.template.json** fájlt.

7. Keresse meg a **modulok** a $edgeAgent tulajdonságát kívánt tulajdonságot. 

   Az itt felsorolt két modult kell lennie. Az első **tempSensor**, amely az összes sablon, amellyel tesztelheti a modulok szimulált hőmérsékleti adatokat alapértelmezés szerint tartalmazza. A második pedig a **SampleModule** modul, amely a megoldás részeként létrehozott.

7. A fájl alján keresse meg a kívánt tulajdonságainak a **$edgeHub** modul. 

   A functions az IoT Edge hubot modul egyik, a központi telepítés összes moduljai közötti üzenetek továbbítását. Tekintse át az értékeket a **útvonalak** tulajdonság. Az első útvonal **SampleModuleToIoTHub**, használja a helyettesítő karaktert ( **\*** ) a SampleModule modul kimeneti üzenetsorokkal részéről érkező üzenetáradat jelzi. Ezeket az üzeneteket lépnek *felső $* , amely, amely azt jelzi, hogy az IoT Hub foglalt név. A második útvonal sensorToSampleModule, vesz igénybe a tempSensor modulból érkező üzeneteket, és továbbítja őket a *input1* látott inicializálása a SampleModule kódot a bemeneti várólistát. 

   ![Tekintse át a deployment.template.json útvonalak](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>És a megoldás leküldéses

Áttekintette a modul kódot és a központi telepítési sablon üzembe helyezés fő fogalmak megértéséhez. Most már készen áll a SampleModule tárolólemezkép létrehozásához, hogy a tárolóregisztrációs adatbázisba. Az IoT tools bővítmény a Visual Studio Code, az ebben a lépésben is nyújtanak a sablonfájlt, valamint a megoldásfájlok modul információi alapján manifest nasazení állít elő. 

### <a name="sign-in-to-docker"></a>Jelentkezzen be a

Adja a tároló Docker registry hitelesítő adatokat, hogy, leküldheti a tárolórendszerképet a beállításjegyzék tárolja. 

1. Nyissa meg a Visual Studio Code integrált terminálon kiválasztásával **nézet** > **terminál**.

2. Jelentkezzen be a Docker mentette a beállításjegyzék létrehozását követően az Azure container registry hitelesítő adataival. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy kap egy biztonsági figyelmeztetés használatát javasolja `--password-stdin`. Adott ajánlott üzemi forgatókönyvek esetén ajánlott, bár ebben az oktatóanyagban hatókörén kívül esik. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) hivatkozást.

### <a name="build-and-push"></a>És leküldéses 

A Visual Studio Code most már hozzáfér a tárolóregisztrációs adatbázisba, hogy ideje bekapcsolni a megoldás kódot egy tárolórendszerképbe. 

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlt, és válassza ki **Build és a leküldéses IoT Edge-megoldás**. 

   ![Hozhat létre, és küldje le az IoT Edge-modulok](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   A build és a leküldéses parancs három műveletet indítja el. Először létrehoz egy új mappát a megoldásban nevű **config** , amely társítja a teljes üzembe helyezési jegyzékfájl, beépített ki a központi telepítési sablont információk és egyéb megoldást. Másodszor, futtatásuk `docker build` hozhat létre a tárolórendszerképet a cél-architektúra a megfelelő docker-fájl alapján. Ezt követően futtatja `docker push` paranccsal küldje le a tároló-beállításjegyzék a lemezképtárból. 

   Ez a folyamat eltarthat néhány percig, először azonban gyorsabb legközelebb a parancsokat. 

2. Nyissa meg a **deployment.amd64.json** újonnan létrehozott konfigurációs fájlban. A fájlnév a cél architektúra tükrözi, így különböző jinou architekturu választásakor.

3. Figyelje meg, hogy a két paramétert, akinek a helyőrzők most már megjelenik a rendszer megfelelő értékekre. A **registryCredentials** szakasz azt a beállításjegyzék-felhasználónevét és jelszavát a .env fájlból beolvasott. A **SampleModule** a teljes lemezkép-tárház a nevét, verzióját és architektúra címkével rendelkezik a module.json fájlból. 

4. Nyissa meg a **module.json** SampleModule mappában található fájl. 

5. Módosítsa a verziószámot a modul lemezképhez. (A verzió, nem a $schema-verzió.) Például növelni a patch verziószámot **0.0.2** , mintha egy kis javítás kellett tettük a modul kódban. 

   >[!TIP]
   >Modulverziók engedélyezze a verziókezeléshez, és tesztelheti az eszközök egy kis készletét a módosításokat az éles környezetbe frissítések telepítése előtt. Ha a modul verzió létrehozása és leküldése előtt nem nő, majd, felülírja a tárház a tárolóregisztrációs adatbázis. 

6. Mentse a módosításokat a module.json fájlt.

7. Kattintson a jobb gombbal a **deployment.template.json** újra a fájlt, majd válassza ismét **Build és a leküldéses IoT Edge-megoldás**.

8. Nyissa meg a **deployment.amd64.json** fájlt újra. Figyelje meg, hogy egy új fájlt nem jön létre, amikor a build és a leküldéses parancsot újra futott. Ugyanebben a fájlban, az elvégzett módosításoknak megfelelően megtörtént. A SampleModule kép most már a 0.0.2 mutat a tároló verziója. 

9. Továbbá győződjön meg arról, mi volt a build és a leküldéses parancs, nyissa meg a [az Azure portal](https://portal.azure.com) , és keresse meg a tárolóregisztrációs adatbázisba. 

10. Válassza ki a tároló-beállításjegyzék **Tárházak** majd **samplemodule**. Győződjön meg arról, hogy a lemezképet mindkét verziója lett leküldve a beállításjegyzékbe.

   ![Mindkét képen verziók megtekintése a tárolóregisztrációs adatbázis](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Az eszköz nem tudta a várt módon befejezni a szinkronizálást. A probléma megoldásának módjáról erre az üzenetre kattintva tájékozódhat.

Összeállításakor és a modul rendszerképének leküldése hibákat észlel, ha gyakran rendelkezik, hogy a fejlesztői gépen a Docker-konfigurációját. A következő ellenőrzések segítségével áttekintheti a konfigurációt: 

* Futtatta a `docker login` parancsot a tárolóregisztrációs adatbázis, amelyet másolt hitelesítő adatok használatával? Ezekkel a hitelesítő adatokkal jelentkezzen be az Azure-ban használt rétegében eltérőek. 
* A tároló-beállításjegyzékbe a helyes? Rendelkezik a megfelelő tárolóregisztrációs adatbázis nevét, és a megfelelő modulnév? Nyissa meg a **module.json** fájlban a SampleModule ellenőrzéséhez. A tárház értékét így kell kinéznie  **\<beállításjegyzék neve\>.azurecr.io/samplemodule**. 
* Ha egy másik nevet, mint a használt **SampleModule** a modul van ilyen nevű következetesek a megoldás?
* Fut a gépen ugyanolyan típusú, tárolók, amelyek fejleszt? Ez az oktatóanyag olyan Linux IoT Edge-eszközök esetén, így üzenetnek kell megjelennie a Visual Studio Code **amd64** vagy **arm32v7** oldalán az eszköztáron és a Linux-tárolók Docker asztali kell futtatnia. A Visual Studio Code C modulok nem támogatja a Windows-tárolók. 

## <a name="deploy-modules-to-device"></a>Eszköz modulok telepítése

Ellenőrizte, hogy a beépített tárolórendszerképek tárolása a tárolóregisztrációs adatbázisba, hogy legyen idő telepíthetők az eszközökre. Győződjön meg arról, hogy az IoT Edge-eszköz üzembe helyezéséig. 

1. A Visual Studio Code Explorerben bontsa ki az Azure IoT Hub-eszközök szakaszban. 

2. Kattintson a jobb gombbal az IoT Edge-eszköz üzembe helyezése, majd válassza a kívánt **központi telepítés létrehozása egyetlen eszköz**. 

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-develop-for-linux/create-deployment.png)

3. A Fájlkezelőben keresse meg a **config** mappát, majd válassza a **deployment.amd64.json** fájlt. 

   Ne használja a deployment.template.json fájlt, amely nem rendelkezik a container registry hitelesítő adatokat vagy a modul képértékek azt. Linux ARM32 eszköz célozza meg, ha a központi telepítési jegyzékfájl neve lesz deployment.arm32v7.json. 

4. Bontsa ki az IoT Edge-eszköz adatait, majd bontsa ki a **modulok** az eszköz listáját. 

5. A frissítés gomb használatával frissíti az eszköz nézetet, amíg meg nem látja a tempSensor és az eszközön futó SampleModule modulok. 

   Eltarthat néhány percig, mindkét modulok elindításához. Az IoT Edge-futtatókörnyezet kell fogadni az új manifest nasazení kérje le a tároló-futtatókörnyezet modul képeit, majd indítsa el minden egyes új modul. 

   ![Az IoT Edge-eszközön futó modulok megjelenítése](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Üzenetek megtekintése az eszközről

A SampleModule kód keresztül a bemeneti várólista-üzeneteket fogadja, és áthalad mentén őket a kimeneti várólista. Manifest nasazení deklarálva útvonalakat, amely átadott üzenetek SampleModule tempSensor származó, majd SampleModule üzeneteket az IoT Hub továbbítja. Az Azure IoT-eszközök a Visual Studio Code-lehetővé teszi üzenetek az IoT Hub, az egyes eszközökről származó beérkezéskor. 

1. A Visual Studio Code Explorerben kattintson a jobb gombbal az IoT Edge-eszköz figyelése, majd válassza a kívánt **Start beépített esemény végpont**. 

2. Tekintse meg a kimeneti ablak a Visual Studio Code az IoT hub érkező üzenetek megtekintéséhez. 

   ![A bejövő eszköz a felhőbe irányuló üzenetek megtekintése](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Változások megtekintése az eszközön

Ha meg szeretné tekinteni a magán az eszközön mi történik, használja a parancsokat ebben a szakaszban az IoT Edge-futtatókörnyezet és az eszközön futó modulok vizsgálhatja meg. 

Ez a szakasz parancsai számára az IoT Edge-eszköz, nem a fejlesztői gépére. Ha egy virtuális gépet az IoT Edge-eszközt használ, a csatlakozás most. Az Azure-ban nyissa meg a virtuális gép – áttekintés oldalra, és válassza **Connect** eléréséhez a secure shell-kapcsolatot. 

* Az eszközre telepített összes modulban megtekintheti, és ellenőrizze azok állapotát:

   ```bash
   iotedge list
   ```

   Négy modult kell megjelennie: az IoT Edge-futtatókörnyezet két modulok, tempSensor és SampleModule. Mind a négy futtatásával kell felsorolni.

* Vizsgálja meg a naplókban az eszközspecifikus modul:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge-modulok olyan kis-és nagybetűket. 

   A tempSensor és SamplModule naplók meg kell jelennie az üzeneteket azok még feldolgozásra. A edgeAgent modul feladata a többi modulnak indítása, így olyan manifest nasazení megvalósításával kapcsolatos információkat, a naplók. Ha bármely modul nem szerepel a listán, vagy nem fut, a edgeAgent naplók valószínűleg a hibákat. A modulok és az IoT Hub közötti kommunikáció a edgeHub modul feladata. A modulok működik, és nem fut, de az üzeneteket az IoT hub érkező, az valószínűleg fog edgeHub naplók van-e a hibákat. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Visual Studio Code beállítása a fejlesztői gépen, és abból az első IoT Edge-modul telepítve. Most, hogy már ismeri az alapfogalmakat, próbálja meg funkció hozzáadása egy modult, hogy azt is elemezheti az adatokat, akkor továbbítja. Válassza ki a kívánt nyelvet: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)