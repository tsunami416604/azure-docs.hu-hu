---
title: Oktatóanyag – Modul fejlesztése Windows-eszközökhöz az Azure IoT Edge használatával
description: Ez az oktatóanyag bemutatja a fejlesztői és a felhőbeli erőforrások beállítását az IoT Edge-modulok fejlesztéséhez Windows-eszközökhöz készült Windows-tárolók használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 58a63c9e11cf86318f0e9f051d034cbbaf7c40a9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772248"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Oktatóanyag: IoT Edge-modulok fejlesztése Windows-eszközökhöz

A Visual Studio segítségével kódot fejleszthet és helyezhet üzembe az IoT Edge-et futtató Windows-eszközökre.

A rövid útmutatóban létrehozott egy IoT Edge-eszközt egy Windows virtuális gép használatával, és telepített egy előre elkészített modult az Azure Piactérről. Ez az oktatóanyag bemutatja, hogy mi kell a saját kód fejlesztéséhez és üzembe helyezéséhez egy IoT Edge-eszközön. Ez az oktatóanyag hasznos előfeltétele a többi oktatóanyagnak, amelyek részletesebben ismertetik az egyes programozási nyelveket vagy az Azure-szolgáltatásokat.

Ez az oktatóanyag egy **C# modul Windows-eszközre**való telepítésének példáját használja. Ez a példa azért lett kiválasztva, mert ez a leggyakoribb fejlesztési forgatókönyv. Ha más nyelven szeretne fejleszteni, vagy tervezi az Azure-szolgáltatások modulokként való üzembe helyezését, ez az oktatóanyag továbbra is hasznos lehet a fejlesztői eszközök megismeréséhez. Miután megértette a fejlesztési fogalmakat, majd kiválaszthatja a kívánt nyelvet vagy az Azure-szolgáltatást a részletek bemerüléséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Állítsa be a fejlesztői gépet.
> * A Visual Studio IoT Edge eszközeivel új projektet hozhat létre.
> * Készítse el a projektet tárolóként, és tárolja egy Azure-tároló beállításjegyzékében.
> * Telepítse a kódot egy IoT Edge-eszközre.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Fő fogalmak

Ez az oktatóanyag egy IoT Edge-modul fejlesztésén vezet be. Az *IoT Edge-modul*, vagy néha csak *modul* röviden, egy tároló, amely végrehajtható kódot tartalmaz. Egy vagy több modult telepíthet egy IoT Edge-eszközre. A modulok olyan konkrét feladatokat hajtanak végre, mint az érzékelőkből származó adatok betöltése, adatelemzés vagy adattisztítási műveletek végrehajtása, vagy üzenetek küldése egy IoT-központba. További információ: [Az Azure IoT Edge-modulok megismerése.](iot-edge-modules.md)

Az IoT Edge-modulok fejlesztése során fontos megérteni a fejlesztési gép és a cél IoT Edge-eszköz közötti különbséget, ahol a modul végül üzembe kerül. A modulkód tárolására épített tárolónak meg kell egyeznie a céleszköz operációs rendszerével .The container that you build to hold your module code must match the operating system (OS) of the *target device.* A Windows-tárolók fejlesztése esetén ez a koncepció egyszerűbb, mivel a Windows-tárolók csak Windows operációs rendszereken futnak. De például használhatja a Windows fejlesztői gépét a Linux IoT Edge-eszközök moduljainak létrehozásához. Ebben a forgatókönyvben meg kell győződnie arról, hogy a fejlesztői gép Linux-tárolókat futtat. Ahogy megy keresztül ez a bemutató, ne feledje, a különbség a *fejlesztőgép operációs rendszer* és a *konténer operációs rendszer*.

Ez az oktatóanyag az IoT Edge-et futtató Windows-eszközöket célozza meg. A Windows IoT Edge-eszközök Windows-tárolókat használnak. Azt javasoljuk, hogy a Visual Studio segítségével fejlessze windowsos eszközökre, ezért ez az oktatóanyag is ezt fogja használni. A Visual Studio-kód is használható, bár a két eszköz között különbségek vannak a támogatásban.

Az alábbi táblázat a Visual Studio Code és a Visual Studio **Windows-tárolóinak** támogatott fejlesztési forgatókönyveit sorolja fel.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-szolgáltatások** | Azure Functions <br> Azure Stream Analytics |   |
| **Nyelvek** | C# (a hibakeresés nem támogatott) | C# <br> C# |
| **További információk** | [Azure IoT Edge visual studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-eszközök a Visual Studio 2017-hez](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge-eszközök a Visual Studio 2019-hez](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Előfeltételek

Fejlesztőgép:

* Windows 10 1809-es vagy újabb frissítéssel.
* Használhatja a saját számítógép vagy egy virtuális gép, attól függően, hogy a fejlesztési beállításokat.
  * Győződjön meg arról, hogy a fejlesztői gép támogatja a beágyazott virtualizációt. Ez a képesség egy tárolómotor futtatásához szükséges, amelyet a következő szakaszban telepít.
* Telepítse [a Git](https://git-scm.com/)- alkalmazást.

Azure IoT Edge-eszköz Windowsrendszeren:

* Azt javasoljuk, hogy ne futtassa az IoT Edge-et a fejlesztői gépen, hanem használjon egy külön eszközt. Ez a különbségtétel a fejlesztői gép és az IoT Edge-eszköz pontosabban tükrözi a valódi üzembe helyezési forgatókönyv, és segít megőrizni a különböző fogalmak egyenes.
* Ha nem rendelkezik egy második eszköz áll rendelkezésre, a rövid útmutató cikk segítségével hozzon létre egy IoT Edge-eszköz az Azure-ban egy [Windows virtuális gép.](quickstart.md)

Felhőerőforrások:

* Ingyenes vagy standard szintű [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

## <a name="install-container-engine"></a>Konténermotor telepítése

Az IoT Edge-modulok tárolókként vannak csomagolva, ezért a tárolók létrehozásához és kezeléséhez egy tárolómotorra van szükség a fejlesztői gépen. Javasoljuk, hogy a Docker Desktop-ot fejlesztési célokra használja, mivel számos funkciója és népszerűsége tárolómotorként van. A Windows-számítógépen lévő Docker Desktop segítségével válthat a Linux-tárolók és a Windows-tárolók között, így könnyedén fejleszthet modulokat a különböző típusú IoT Edge-eszközökhöz.

A Docker dokumentációjával telepítheti a fejlesztői gépre:

* [A Windows rendszerhez készült Docker asztali verziójának telepítése](https://docs.docker.com/docker-for-windows/install/)

  * A Docker Desktop for Windows telepítésekor a rendszer megkérdezi, hogy Linux vagy Windows tárolókat kíván-e használni. Ebben az oktatóanyagban használja a **Windows-tárolókat.** További információt a [Váltás Windows és Linux-tárolók között című témakörben talál.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

## <a name="set-up-visual-studio-and-tools"></a>A Visual Studio és az eszközök beállítása

A Visual Studio IoT-bővítményei segítenek az IoT Edge-modulok fejlesztésében. Ezek a bővítmények projektsablonokat biztosítanak, automatizálják a központi telepítési jegyzékfájl létrehozását, és lehetővé teszik az IoT Edge-eszközök figyelését és kezelését. Ebben a szakaszban telepíti a Visual Studio és az IoT Edge-bővítményt, majd beállítja az Azure-fiókját az IoT Hub-erőforrások kezelésére a Visual Studióból.

Ez az oktatóanyag a Visual Studio 2019 fejlesztési lépéseit ismerteti. Ha a Visual Studio 2017-et (15.7-es vagy újabb verzió) használja, a lépések hasonlóak. Ha inkább a Visual Studio-kódot szeretné használni, olvassa el a Visual Studio-kód használata című útmutatóutasításait az [Azure IoT Edge moduljainak fejlesztéséhez és hibakereséséhez.](how-to-vs-code-develop-module.md)

1. Készítse elő a Visual Studio 2019-et fejlesztőgépén.

   * Ha még nem rendelkezik a Visual Studio fejlesztői gépén, telepítse a [Visual Studio 2019-et](https://docs.microsoft.com/visualstudio/install/install-visual-studio) az alábbi munkaterhelésekkel:

      * Azure-fejlesztés
      * Asztali fejlesztés C++ segítségével
      * .NET Core platformfüggetlen fejlesztés

   * Ha már rendelkezik a Visual Studio 2019 fejlesztői gépen, kövesse a [Visual Studio módosítása](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) című részben leírt lépéseket a szükséges számítási feladatok hozzáadásához.

2. Töltse le és telepítse az [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítményt a Visual Studio 2019-hez.

   Ha a Visual Studio 2017-et (15.7-es vagy újabb verzió) használja, töltse le és telepítse az [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)alkalmazást.

3. Ha a telepítések befejeződtek, nyissa meg a Visual Studio 2019-et, és válassza **a Kód nélküli folytatás**lehetőséget.

4. Válassza **a Felhőkezelő megtekintése** > **Cloud Explorer**lehetőséget.

5. Válassza ki a profil ikont a felhőkezelőben, és jelentkezzen be az Azure-fiókjába, ha még nem jelentkezett be.

6. Miután bejelentkezik, az Azure-előfizetések felsorolva. Bontsa ki az IoT-központtal rendelkező előfizetést.

7. Az előfizetése alatt bontsa ki az **IoT Hub-okat,** majd az IoT hubot. Meg kell jelennie az IoT-eszközök listáját, és ezzel a felfedezővel kezelheti őket.

   ![Az IoT Hub erőforrásainak elérése a Cloud Explorerben](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Új modulprojekt létrehozása

Az Azure IoT Edge Tools bővítmény projektsablonokat biztosít a Visual Studio összes támogatott IoT Edge-modulnyelvéhez. Ezek a sablonok rendelkeznek az összes olyan fájllal és kóddal, amelyegy működő modul üzembe helyezéséhez szükséges az IoT Edge teszteléséhez, vagy kiindulópontot biztosít a sablon saját üzleti logikával történő testreszabásához.

1. **Új fájl** > **kiválasztása...** > **Project...**

2. Az új projektablakban keresse meg az **IoT Edge-et,** és válassza az **Azure IoT Edge (Windows amd64)** projektet. Kattintson a **Tovább** gombra.

   ![Új Azure IoT Edge-projekt létrehozása](./media/tutorial-develop-for-windows/new-project.png)

3. A konfigurálása az új projekt ablakban nevezze át a projektet és a megoldást valami leíró, mint **a CSharpTutorialApp**. A projekt létrehozásához kattintson a **Létrehozás** gombra.

   ![Új Azure IoT Edge-projekt konfigurálása](./media/tutorial-develop-for-windows/configure-project.png)

4. A Modul hozzáadása ablakban konfigurálja a projektet a következő értékekkel:

   | Mező | Érték |
   | ----- | ----- |
   | Visual Studio-sablon | Válassza a **C# modul lehetőséget.** |
   | Modulnév | Fogadja el az alapértelmezett **IotEdgeModule1 modult.** |
   | Tárház url-címe | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre ki van töltve a modulprojekt névértékéből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A **bejelentkezési kiszolgáló** értékét az Azure Portalon a tároló beállításjegyzékének **áttekintése lapjáról** kérheti le. <br><br> A végső lemezképtár \<a\>rendszerleíró adatbázis .azurecr.io/iotedgemodule1 nevének tűnik. |

      ![A projekt konfigurálása céleszközhöz, modultípushoz és tárolóbeállítás-beállításjegyzékhez](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. A modul létrehozásához válassza a **Hozzáadás** lehetőséget.

Miután az új projekt betöltődik a Visual Studio ablakába, szánjon egy kis lehetőséget arra, hogy megismerkedjen az általa létrehozott fájlokkal:

* Egy IoT Edge projekt neve **CSharpTutorialApp**.
  * A **Modulok** mappa a projektben szereplő modulokra mutató mutatókat tartalmaz. Ebben az esetben csak az IotEdgeModule1-nek kell lennie.
  * A rejtett **.env** fájl tárolja a tároló beállításjegyzékének hitelesítő adatait. Ezek a hitelesítő adatok meg vannak osztva az IoT Edge-eszközzel, így hozzáféréssel rendelkezik a tárolórendszerképek lekérése.
  * A **deployment.template.json** fájl egy sablon, amely segít a központi telepítési jegyzékfájl létrehozásában. A *központi telepítési jegyzékfájl* egy olyan fájl, amely pontosan meghatározza, hogy mely modulokat szeretné telepíteni egy eszközön, hogyan kell konfigurálni őket, és hogyan kommunikálhatnak egymással és a felhővel.
    > [!TIP]
    > A rendszerleíró hitelesítő adatok szakaszban a cím automatikusan kitöltődik a megoldás létrehozásakor megadott adatokból. Az .env fájlban tárolt felhasználónév- és jelszóhivatkozási változók azonban. Ez a biztonság, mint a .env fájl git figyelmen kívül hagyja, de a központi telepítési sablon nem.
* Egy IoT Edge modulprojekt, az **IotEdgeModule1.**
  * A **program.cs** fájl tartalmazza a projektsablonhoz tartozó alapértelmezett C# modulkódot. Az alapértelmezett modul egy forrásból érkező bemenetet vesz fel, és továbbítja azt az IoT Hubnak.
  * A **module.json** fájl a modul részleteit tartalmazza, beleértve a teljes rendszerkép-tárházat, a rendszerkép-verziót, és hogy melyik Dockerfile-t használja az egyes támogatott platformokhoz.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Adja meg a rendszerleíró adatbázis hitelesítő adatait az IoT Edge-ügynöknek

Az IoT Edge-futásidejű szüksége van a beállításjegyzék hitelesítő adatait a tárolórendszerképek az IoT Edge-eszközre. Az IoT Edge-bővítmény megpróbálja lekéri a tároló beállításjegyzék-adatait az Azure-ból, és feltölti azokat a központi telepítési sablonban.

1. Nyissa meg a **deployment.template.json** fájlt a modulmegoldásában.

1. Keresse meg a **registryCredentials tulajdonságot** a kívánt tulajdonságok $edgeAgent. A beállításjegyzék címét automatikusan ki kell tölteni a projekt létrehozásakor megadott adatokból, majd a felhasználónév és a jelszó mezőknek változóneveket kell tartalmazniuk. Példa:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Nyissa meg az **.env** fájlt a modulmegoldásban. (Alapértelmezés szerint el van rejtve a Megoldáskezelőben, ezért előfordulhat, hogy a megjelenítéshez ki kell **választania** az Összes fájl megjelenítése gombot.)

1. Adja hozzá a **felhasználónév** és **a jelszó** értékeket, amelyeket az Azure-tároló beállításjegyzékből másolt.

1. Mentse a módosításokat az .env fájlba.

### <a name="review-the-sample-code"></a>A mintakód áttekintése

A létrehozott megoldássablon egy IoT Edge-modul mintakódját tartalmazza. Ez a mintamodul egyszerűen üzeneteket fogad, majd továbbadja azokat. A folyamat funkció bemutatja az IoT Edge egy fontos koncepcióját, amely a modulok egymással való kommunikációját mutatja be.

Minden modul nak több *bemeneti* és *kimeneti* várólistája is lehet a kódban deklarálva. Az eszközön futó IoT Edge hub egy modul kimenetéről egy vagy több modul bemenetére irányítja az üzeneteket. A bemenetek és kimenetek deklarálásának konkrét nyelve nyelveken ként változik, de a koncepció minden modulban azonos. A modulok közötti útválasztásról az [Útvonalak deklarálása](module-composition.md#declare-routes)című témakörben talál további információt.

A projektsablonhoz tartozó C# mintakód a .NET IoT Hub SDK-ból származó [ModuleClient osztályt](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) használja.

1. A **program.cs** fájlban keresse meg a **SetInputMessageHandlerAsync metódust.**

2. A [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) metódus beállít egy bemeneti várólistát a bejövő üzenetek fogadására. Tekintse át ezt a módszert, és nézze meg, hogyan inicializálja a input1 nevű bemeneti **várólistát.**

   ![A bemeneti név megkeresése a SetInputMessageHandlserAsync konstruktorban](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Ezután keresse meg a **SendEventAsync metódust.**

4. A [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) metódus feldolgozza a fogadott üzeneteket, és beállít egy kimeneti várólistát, hogy továbbadja őket. Tekintse át ezt a módszert, és nézze meg, hogy inicializálja a **output1**nevű kimeneti várólistát.

   ![A kimeneti név megkeresése a SendEventAsync konstruktorban](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Nyissa meg a **deployment.template.json** fájlt.

6. Keresse meg **a** modulok tulajdonsága a $edgeAgent kívánt tulajdonságokat.

   Itt két modulnak kell szerepelnie. Az első a **SimulatedTemperatureSensor**, amely alapértelmezés szerint az összes sablonban szerepel, és szimulált hőmérsékleti adatokat biztosít, amelyek segítségével tesztelheti a modulokat. A második az **IotEdgeModule1** modul, amelyet a projekt részeként hozott létre.

   Ez a moduletulajdonság deklarálja, hogy mely modulokat kell bevonni az eszközvagy eszközök üzembe helyezésébe.

7. Keresse meg a kívánt tulajdonságok $edgeHub **útvonalak** tulajdonságát.

   Az IoT Edge hub modul egyik funkciója az üzenetek irányítása a központi telepítés összes modulja között. Tekintse át az útvonalak tulajdonság értékeit. Az első útvonal, **az IotEdgeModule1ToIoTHub**helyettesítő**\*** karaktert ( ) használ az IotEdgeModule1 modul bármely kimeneti várólistájából érkező üzenet felvételéhez. Ezek az üzenetek *$upstream*kerülnek, amely egy fenntartott név, amely az IoT Hubot jelzi. A második útvonal, **a sensorToIotEdgeModule1**a SimulatedTemperatureSensor modulból érkező üzeneteket az IotEdgeModule1 modul *input1* bemeneti várólistájára irányítja.

   ![Útvonalak áttekintése a deployment.template.json ban](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Készítsd el és nyomd meg a megoldást

Áttekintette a modulkódot és a központi telepítési sablont néhány kulcsfontosságú központi telepítési fogalmak megértéséhez. Most már készen áll az IotEdgeModule1 tárolórendszerkép létrehozásához, és leküldése a tároló rendszerleíró adatbázisába. A Visual Studio IoT-eszközbővítményével ez a lépés a sablonfájlban lévő információk és a megoldásfájlok modulinformációi alapján is létrehozza a központi telepítési jegyzéket.

### <a name="sign-in-to-docker"></a>Bejelentkezés a Dockerbe

Adja meg a tároló beállításjegyzék-hitelesítő adatait a Docker a fejlesztői gépen, hogy leküldéses a tárolórendszerkép a beállításjegyzékben kell tárolni.

1. Nyissa meg a PowerShellt vagy a parancssort.

2. Jelentkezzen be a Dockerbe az Azure container beállításjegyzék-hitelesítő adataival, amelyeket a beállításjegyzék létrehozása után mentett.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   A használatát a használatát `--password-stdin`javasolja. Bár ez az ajánlott eljárás éles forgatókönyvekhez ajánlott, ez kívül esik az oktatóanyag hatókörén. További információkért tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referencia.

### <a name="build-and-push"></a>Építsd meg és nyomd

A fejlesztői gép most már hozzáfér a tároló beállításjegyzékhez, és az IoT Edge-eszközök is. Itt az ideje, hogy a projektkódot tárolóképpé alakítsa.

1. Kattintson a jobb gombbal a **CSharpTutorialApp** projekt mappájára, és válassza **az IoT Edge-modulok összeállítása és leküldése parancsot.**

   ![IoT Edge-modulok létrehozása és leküldése](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   A build- és leküldéses parancs három műveletet indít el. Először létrehoz egy új mappát a megoldás nevű **config,** amely tartalmazza a teljes központi telepítési jegyzékfájl, beépített információk a központi telepítési sablon és más megoldásfájlokat. Másodszor, fut `docker build` a tárolórendszerkép létrehozása a célarchitektúrának megfelelő dockerfile alapján. Ezután fut `docker push` a rendszerkép-tárház a tároló rendszerleíró adatbázisba.

   Ez a folyamat első alkalommal több percet is igénybe vehet, de a parancsok következő futtatásakor gyorsabb.

2. Nyissa meg a **deployment.windows-amd64.json** fájlt az újonnan létrehozott konfigurációs mappában. (Előfordulhat, hogy a konfigurációs mappa nem jelenik meg a Visual Studio Megoldáskezelőjében. Ebben az esetben válassza az **Összes fájl megjelenítése ikont** a Megoldáskezelő tálcáján.)

3. Keresse **image** meg az IotEdgeModule1 szakasz képparaméterét. Figyelje meg, hogy a lemezkép tartalmazza a teljes képtára a nevét, verzióját és architektúra címke a module.json fájlt.

4. Nyissa meg a **module.json** fájlt az IotEdgeModule1 mappában.

5. A modulkép verziószámának módosítása. (A verzió, nem a $schema-verzió.) Például, növekmény a javítás verziószámát **0.0.2,** mintha tettünk egy kis javítást a modul kódot.

   >[!TIP]
   >A modulverziók engedélyezik a verziókövetést, és lehetővé teszik a módosítások tesztelését az eszközök kis készletén, mielőtt frissítéseket telepítene éles környezetbe. Ha nem növekszik a modul verzió létrehozása előtt és lenyomása előtt, majd felülírja a tárház a tároló rendszerleíró adatbázisban.

6. Mentse a módosításokat a module.json fájlba.

7. Kattintson ismét a jobb gombbal a **CSharpTutorialApp** projektmappára, és válassza ismét **az IoT Edge-modulok összeállítása és leküldése** parancsot.

8. Nyissa meg újra a **deployment.windows-amd64.json** fájlt. Figyelje meg, hogy nem jött létre új fájl a build- és leküldéses parancs ismételt leküldéses parancsának ismételt leküldéses parancsának ismételt létrehozásakor. Ehelyett ugyanazt a fájlt frissítették, hogy tükrözze a változásokat. Az IotEdgeModule1 lemezkép most a tároló 0.0.2-es verziójára mutat. Ez a változás a központi telepítési jegyzékfájlban, hogyan mondja meg az IoT Edge-eszköz, hogy van egy új verziója a modul lekérése.

9. A build- és leküldéses parancs további ellenőrzéséhez nyissa meg az [Azure Portalt,](https://portal.azure.com) és keresse meg a tároló beállításjegyzékét.

10. A tárolóbeállításban válassza **az Adattárak,** majd **az iotedgemodule1 lehetőséget.** Ellenőrizze, hogy a rendszerkép mindkét verziója a rendszerleíró adatbázisba került-e.

    ![Mindkét lemezképverzió megtekintése a tároló beállításjegyzékében](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Hibaelhárítás

Ha hibákat tapasztal a modulkép létrehozása kor és lenyomásakor, gyakran a fejlesztői gépen lévő Docker-konfigurációval van kapcsolatos. A konfiguráció áttekintéséhez használja az alábbi ellenőrzéseket:

* A parancsot `docker login` a tárolóbeállításjegyzékből másolt hitelesítő adatokkal futtatta? Ezek a hitelesítő adatok eltérnek az Azure-ba való bejelentkezéshez használt hitelesítő adatoktól.
* Helyes a tárolótár? Rendelkezik a megfelelő tárolóbeállítás-bejegyzés nevével és a megfelelő modulnévvel? Nyissa meg az ellenőrizendő **Module.json** fájlt az IotEdgeModule1 mappában. A tárház értékének a ** \<rendszerleíró adatbázis "azurecr.io/iotedgemodule1 .\>**
* Ha az **IotEdgeModule1-től** eltérő nevet használt a modulhoz, ez a név konzisztens a megoldásban?
* A gépe ugyanolyan típusú konténereket üzemeltet, mint amit épít? Ez az oktatóanyag Windows IoT Edge-eszközökhöz használható, így a Visual **Studio-fájloknak windows-amd64** kiterjesztéssel kell rendelkezniük, és a Docker Desktopnak Windows-tárolókat kell futtatnia.

## <a name="deploy-modules-to-device"></a>Modulok telepítése az eszközre

Ellenőrizte, hogy a beépített tárolórendszerképek a tároló beállításjegyzékében tárolódnak-e, ezért itt az ideje, hogy telepítse őket egy eszközre. Győződjön meg arról, hogy az IoT Edge-eszköz működik.

1. Nyissa meg a Cloud Explorert a Visual Studióban, és bontsa ki az IoT-központ adatait.

2. Válassza ki annak az eszköznek a nevét, amelynek üzembe helyezését telepíteni szeretné. A **Műveletek** listában válassza a **Telepítés létrehozása lehetőséget.**

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-develop-for-windows/create-deployment.png)

3. A fájlkezelőben keresse meg a projekt konfigurációs mappáját, és válassza ki a **deployment.windows-amd64.json** fájlt. Ez a fájl gyakran található`C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Ne használja a deployment.template.json fájlt, amely nem rendelkezik a teljes modul képértékeit.

4. Bontsa ki az IoT Edge-eszköz adatait a Cloud Explorerben az eszközön lévő modulok megtekintéséhez.

5. A **Frissítés** gombbal frissítheti az eszköz állapotát, és láthatja, hogy a SimulatedTemperatureSensor és az IotEdgeModule1 modulok telepítve vannak-e az eszközön.

   ![IoT Edge-eszközön futó modulok megtekintése](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Üzenetek megtekintése az eszközről

Az IotEdgeModule1 kód fogadja az üzeneteket a bemeneti várólistán keresztül, és továbbítja azokat a kimeneti várólistán keresztül. A központi telepítési jegyzékfájl deklarált útvonalak, amelyek üzeneteket továbbítottak a SimulatetTemperatureSensor az IotEdgeModule1-nek, majd továbbították az üzeneteket az IotEdgeModule1-ről az IoT Hubra. Az Azure IoT Edge-eszközök a Visual Studio lehetővé teszi, hogy az üzenetek et érkezik az IoT Hub az egyes eszközökről.

1. A Visual Studio felhőkezelőjében válassza ki annak az IoT Edge-eszköznek a nevét, amelyet telepített.

2. A **Műveletek** menüben válassza a **Beépített eseményvégpont indítása parancsot.**

3. Tekintse meg a **Kimenet** szakaszt a Visual Studióban az IoT-központba érkező üzenetek megtekintéséhez.

   Mindkét modul indítása eltarthat néhány percig. Az IoT Edge-futásidejű kell kapnia az új központi telepítési jegyzékfájl, húzza le a modullemezképeket a tároló futásidejű, majd indítsa el az egyes új modul.

   ![Bejövő eszköz megtekintése felhőalapú üzenetekbe](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Változások megtekintése az eszközön

Ha szeretné látni, mi történik az eszközön is, használja az ebben a szakaszban található parancsokat az IoT Edge futtató- és az eszközön futó modulok vizsgálatához.

Ebben a szakaszban a parancsok az IoT Edge-eszköz, nem a fejlesztői gép. Ha egy virtuális gépet használ az IoT Edge-eszközhöz, csatlakozzon hozzá most. Az Azure-ban lépjen a virtuális gép áttekintő lapjára, és válassza a **Csatlakozás** lehetőséget a távoli asztali kapcsolat eléréséhez. Az eszközön nyisson meg egy parancsot `iotedge` vagy powershell-ablakot a parancsok futtatásához.

* Az eszközre telepített összes modul megtekintése és állapotuk ellenőrzése:

   ```cmd
   iotedge list
   ```

   Négy modulnak kell megjelennie: a két IoT Edge futásidejű modul, a SimulatedTemperatureSensor és az IotEdgeModule1. Mind a négyet futóként kell felsorolni.

* Vizsgálja meg a naplókat egy adott modul:

   ```cmd
   iotedge logs <module name>
   ```

   Az IoT Edge-modulok kis- és nagybetűket megkülönböztetők.

   A SimulatedTemperatureSensor és az IotEdgeModule1 naplóknak meg kell jelenjenek a feldolgozásuk általuk feldolgozott üzenetek. Az edgeAgent modul felelős a többi modul elindításáért, így a naplók információkat tartalmaznak a központi telepítési jegyzék megvalósításáról. Ha valamelyik modul nem szerepel a listában, vagy nem fut, az edgeAgent naplók valószínűleg a hibákat. Az edgeHub modul felelős a modulok és az IoT Hub közötti kommunikációért. Ha a modulok futnak és futnak, de az üzenetek nem érkeznek meg az IoT hub, az edgeHub naplók valószínűleg a hibákat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Visual Studio 2019-et a fejlesztői gépen állítja be, és üzembe helyezi az első IoT Edge-modult. Most, hogy már ismeri az alapfogalmakat, próbálja meg hozzá a modul továbbfejlesztett funkcióit, hogy elemezhesse az adatokat. Válassza ki a kívánt nyelvet:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C.](tutorial-csharp-module-windows.md)
