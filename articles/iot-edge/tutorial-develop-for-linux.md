---
title: Oktatóanyag – Modul fejlesztése Linux-eszközökhöz az Azure IoT Edge használatával
description: Ez az oktatóanyag bemutatja a fejlesztői gép és a felhőerőforrások beállítását az IoT Edge-modulok linuxos tárolók linuxos eszközökhasználatával történő fejlesztéséhez
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 478d9c0485125870f8d5ffb4132f46476b4bb4ef
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384364"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Oktatóanyag: IoT Edge-modulok fejlesztése Linux-eszközökhöz

A Visual Studio-kód használatával kódot fejleszthet és helyezhet üzembe az IoT Edge-et futtató Linux-eszközökre.

A rövid útmutatóban létrehozott egy IoT Edge-eszközt egy Linux-virtuális gép használatával, és üzembe helyezett egy modult az Azure Marketplace-ről. Ez az oktatóanyag végigvezeti a saját kód fejlesztését és üzembe helyezését egy IoT Edge-eszközön. Ez a cikk hasznos előfeltétele a többi oktatóanyagnak, amelyek részletesebben ismertetik az egyes programozási nyelveket vagy az Azure-szolgáltatásokat.

Ez az oktatóanyag egy **C# modul Linux-eszközre**való üzembe helyezésének példáját használja. Ezt a példát azért választották, mert ez az IoT Edge-megoldások leggyakoribb fejlesztői forgatókönyve. Még akkor is, ha azt tervezi, hogy egy másik nyelvet, vagy egy Azure-szolgáltatás üzembe helyezése, ez az oktatóanyag továbbra is hasznos a fejlesztési eszközök és fogalmak megismerése. Fejezze be ezt a bevezetést a fejlesztési folyamat, majd válassza ki a kívánt nyelvet vagy az Azure-szolgáltatást a részletekbe merüléshez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Állítsa be a fejlesztői gépet.
> * A Visual Studio-kód IoT Edge eszközeivel új projektet hozhat létre.
> * Készítse el a projektet tárolóként, és tárolja egy Azure-tároló beállításjegyzékében.
> * Telepítse a kódot egy IoT Edge-eszközre.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Fő fogalmak

Ez az oktatóanyag egy IoT Edge-modul fejlesztésén vezet be. Az *IoT Edge-modul*, vagy néha csak *modul* röviden, egy tároló végrehajtható kódot. Egy vagy több modult telepíthet egy IoT Edge-eszközre. A modulok olyan konkrét feladatokat hajtanak végre, mint az érzékelőkből származó adatok betöltése, az adatok tisztítása és elemzése, vagy üzenetek küldése egy IoT hubba. További információ: [Az Azure IoT Edge-modulok megismerése.](iot-edge-modules.md)

Az IoT Edge-modulok fejlesztése során fontos megérteni a fejlesztési gép és a cél IoT Edge-eszköz közötti különbséget, ahol a modul végül üzembe kerül. A modulkód tárolására épített tárolónak meg kell egyeznie a céleszköz operációs rendszerével .The container that you build to hold your module code must match the operating system (OS) of the *target device.* A leggyakoribb forgatókönyv például az, hogy valaki egy Windows-számítógépen fejleszt egy modult, amely ioT Edge-et futtató Linux-eszközt kíván megcélozni. Ebben az esetben a tároló operációs rendszer lenne Linux. Ahogy megy keresztül ez a bemutató, ne feledje, a különbség a *fejlesztőgép operációs rendszer* és a *konténer operációs rendszer*.

Ez az oktatóanyag az IoT Edge-et futtató Linux-eszközöket célozza meg. Használhatja a kívánt operációs rendszert, amíg a fejlesztői gép Linux-tárolókat futtat. Azt javasoljuk, hogy a Visual Studio Code segítségével dolgozzon Linux-eszközökhöz, ezért ez az oktatóanyag ezt fogja használni. A Visual Studio is használható, bár a két eszköz között különbségek vannak a támogatásban.

Az alábbi táblázat a Visual Studio-kódban és a Visual Studio-ban **található Linux-tárolók** támogatott fejlesztési forgatókönyveit sorolja fel.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux-eszközarchitektúra** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-szolgáltatások** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Nyelvek** | C# <br> C# <br> Java <br> Node.js <br> Python | C# <br> C# |
| **További információk** | [Azure IoT Edge visual studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-eszközök a Visual Studio 2017-hez](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge-eszközök a Visual Studio 2019-hez](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>A Linux ARM64-eszközök támogatása [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)érhető el. További információ: [ARM64 IoT Edge modulok fejlesztése és hibakeresése a Visual Studio Code programban (előzetes verzió)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Ez az oktatóanyag a Visual Studio-kód fejlesztési lépéseit ismerteti. Ha inkább a Visual Studio-t szeretné használni, olvassa el a Visual Studio 2019 használata című útmutatót az [Azure IoT Edge moduljainak fejlesztéséhez és hibakereséséhez.](how-to-visual-studio-develop-module.md)

## <a name="prerequisites"></a>Előfeltételek

Fejlesztőgép:

* Használhatja a saját számítógép vagy egy virtuális gép, attól függően, hogy a fejlesztési beállításokat.
  * Győződjön meg arról, hogy a fejlesztői gép támogatja a beágyazott virtualizációt. Ez a képesség egy tárolómotor futtatásához szükséges, amelyet a következő szakaszban telepít.
* A tárolómotor futtatására alkalmas operációs rendszerek többsége használható IoT Edge-modulok linuxos eszközökhöz történő fejlesztéséhez. Ez az oktatóanyag Windows-számítógépet használ, de rámutat az ismert különbségekre MacOS vagy Linux rendszeren.
* Telepítse [a Git](https://git-scm.com/)alkalmazást , hogy az oktatóanyag későbbi részében lehúzza a modulsabloncsomagokat.  
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Azure IoT Edge-eszköz Linuxon:

* Azt javasoljuk, hogy ne futtassa az IoT Edge-et a fejlesztői gépen, hanem használjon egy külön eszközt. Ez a különbségtétel a fejlesztői gép és az IoT Edge-eszköz pontosabban tükrözi a valódi üzembe helyezési forgatókönyv, és segít megőrizni a különböző fogalmak egyenes.
* Ha nem rendelkezik egy második eszköz áll rendelkezésre, a rövid útmutató cikk segítségével hozzon létre egy IoT Edge-eszköz az Azure-ban egy [Linux virtuális gép.](quickstart-linux.md)

Felhőerőforrások:

* Ingyenes vagy standard szintű [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

## <a name="install-container-engine"></a>Konténermotor telepítése

Az IoT Edge-modulok tárolóként vannak csomagolva, ezért a fejlesztői gépen egy tárolómotorra van szükség azok létrehozásához és kezeléséhez. A Docker Desktop-ot a szolgáltatás támogatása és népszerűsége miatt javasoljuk a Docker Desktop számára. A Windows-on futó Docker Desktop lehetővé teszi a Linux-tárolók és a Windows-tárolók közötti váltást, így könnyedén fejleszthet modulokat a különböző típusú IoT Edge-eszközökhöz.

A Docker dokumentációjával telepítheti a fejlesztői gépre:

* [A Windows rendszerhez készült Docker asztali verziójának telepítése](https://docs.docker.com/docker-for-windows/install/)

  * A Docker Desktop for Windows telepítésekor a rendszer megkérdezi, hogy Linux vagy Windows tárolókat kíván-e használni. Ez a döntés bármikor megváltoztatható egy egyszerű kapcsolóval. Ebben az oktatóanyagban Linux-tárolókat használunk, mert a moduljaink Linux-eszközöket céloznak meg. További információt a [Váltás Windows és Linux-tárolók között című témakörben talál.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

* [A Docker Desktop for Mac telepítése](https://docs.docker.com/docker-for-mac/install/)

* A Több Linux platformon a telepítéssel kapcsolatos információkért olvassa el a [Docker CE-ről](https://docs.docker.com/install/) című információt.
  * A Linux windowsos alrendszer (WSL) esetén telepítse a Docker Desktop for Windows rendszert.

## <a name="set-up-vs-code-and-tools"></a>Vs-kód és eszközök beállítása

A Visual Studio-kód IoT-bővítményei segítségével ioT edge-modulokat fejleszthet. Ezek a bővítmények projektsablonokat biztosítanak, automatizálják a központi telepítési jegyzékfájl létrehozását, és lehetővé teszik az IoT Edge-eszközök figyelését és kezelését. Ebben a szakaszban telepíti a Visual Studio-kódot és az IoT-bővítményt, majd beállítja az Azure-fiókot az IoT Hub-erőforrások kezelésére a Visual Studio-kódból.

1. Telepítse a [Visual Studio-kódot](https://code.visualstudio.com/) a fejlesztőgépre.

2. A telepítés befejezése után válassza a**Bővítmények** **megtekintése** > lehetőséget.

3. Keressen **az Azure IoT-eszközök**, amely valójában bővítmények gyűjteménye, amely segít az IoT Hub és az IoT-eszközök, valamint az IoT Edge-modulok fejlesztése.

4. Válassza az **Install** (Telepítés) lehetőséget. Minden mellékelt kiterjesztés telepítése külön-külön.

5. Ha a bővítmények telepítése befejeződött, nyissa meg a parancspalettát a**Parancspaletta** **megtekintése** > lehetőséget választva.

6. A parancspalettán keresse meg és válassza az **Azure: Bejelentkezés**lehetőséget. Az utasításokat követve jelentkezzen be Azure-fiókjába.

7. A parancspalettán ismét keresse meg és válassza az **Azure IoT Hub: Select IoT Hub.** Kövesse az utasításokat az Azure-előfizetés és az IoT hub kiválasztásához.

8. Nyissa meg a Visual Studio-kód intézőszakaszát úgy, hogy vagy a bal oldali tevékenységsávon az ikont, vagy az**Intéző** **megtekintése** > lehetőséget választja.

9. Az explorer szakasz alján bontsa ki az összecsukott **Azure IoT Hub-eszközök menüt.** A parancspalettán keresztül kiválasztott IoT hubhoz társított eszközöknek és IoT Edge-eszközöknek kell megtekintenie.

   ![Eszközök megtekintése az IoT hubban](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Új modulprojekt létrehozása

Az Azure IoT Tools bővítmény projektsablonokat biztosít a Visual Studio-kód ban az összes támogatott IoT Edge-modulnyelvhez. Ezek a sablonok rendelkeznek az összes olyan fájllal és kóddal, amelyegy működő modul üzembe helyezéséhez szükséges az IoT Edge teszteléséhez, vagy kiindulópontot biztosít a sablon saját üzleti logikával történő testreszabásához.

Ebben az oktatóanyagban a C# modulsablont használjuk, mert ez a leggyakrabban használt sablon.

### <a name="create-a-project-template"></a>Projektsablon létrehozása

A Visual Studio Code parancspalettán keresse meg és válassza az **Azure IoT Edge: Új IoT Edge-megoldás lehetőséget.** Kövesse az utasításokat, és használja az alábbi értékeket a megoldás létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldásnak, vagy fogadja el az alapértelmezett **EdgeSolution nevet.** |
   | Select module template (Modulsablon kiválasztása) | Válassza a **C# modul lehetőséget.** |
   | Provide a module name (Modulnév megadása) | Fogadja el az alapértelmezett **SampleModule modult.** |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre ki van töltve az utolsó lépésben megadott névből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> A végső képtár \<háztár\>néz ki, mint a rendszerleíró adatbázis neve .azurecr.io/samplemodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-develop-for-linux/image-repository.png)

Miután az új megoldás betöltődik a Visual Studio Kód ablakába, szánjon egy kis lehetőséget arra, hogy megismerkedjen az általa létrehozott fájlokkal:

* A **.vscode** mappa egy **launch.json**nevű fájlt tartalmaz, amely a modulok hibakeresésére szolgál.
* A **modulok mappája** a megoldás minden moduljához tartalmaz egy mappát. Most, hogy csak **SampleModule**, vagy bármilyen nevet adott a modul. A SampleModule mappa tartalmazza a fő programkódot, a modul metaadatait és számos Docker-fájlt.
* Az **.env** fájl tárolja a hitelesítő adatokat a tároló rendszerleíró adatbázisában. Ezek a hitelesítő adatok meg vannak osztva az IoT Edge-eszközzel, így hozzáféréssel rendelkezik a tárolórendszerképek lekérése.
* A **deployment.debug.template.json** fájl és **a deployment.template.json** fájl olyan sablonok, amelyek segítenek a központi telepítési jegyzék létrehozásában. A *központi telepítési jegyzékfájl* egy olyan fájl, amely pontosan meghatározza, hogy mely modulokat szeretné telepíteni egy eszközön, hogyan kell konfigurálni őket, és hogyan kommunikálhatnak egymással és a felhővel. A sablonfájlok bizonyos értékekhez mutatót használnak. Amikor a sablont valódi központi telepítési jegyzékfájllá alakítja, a mutatókat a mutatómás megoldásfájlokból vett értékek helyettesítik. Keresse meg a két általános helyőrzőt a telepítési sablonban:

  * A rendszerleíró hitelesítő adatok szakaszban a cím automatikusan kitöltődik a megoldás létrehozásakor megadott adatokból. A felhasználónév és a jelszó azonban az .env fájlban tárolt változókra hivatkozik. Ez a konfiguráció a biztonság, mint a .env fájl git figyelmen kívül hagyja, de a központi telepítési sablon nem.
  * A SampleModule szakaszban a tárolórendszerkép nincs kitöltve annak ellenére, hogy a megoldás létrehozásakor biztosította a lemezképtár. Ez a helyőrző a SampleModule mappában lévő **module.json** fájlra mutat. Ha megnyitja a fájlt, látni fogja, hogy a lemezképmező nem tartalmazza a tárházat, hanem egy címke értéket is, amely a verzióból és a tároló platformjából áll. A verziót manuálisan is megismételheti a fejlesztési ciklus részeként, és a tárolóplatformot egy kapcsolósegítségével választhatja ki, amelyet ebben a szakaszban később vezetünk be.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Adja meg a rendszerleíró adatbázis hitelesítő adatait az IoT Edge-ügynöknek

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futásidejű szüksége van ezekre a hitelesítő adatokra a tárolórendszerképek lekérése az IoT Edge-eszközre.

Az IoT Edge-bővítmény megpróbálja lekéri a tároló beállításjegyzék hitelesítő adatait az Azure-ból, és feltölti őket a környezeti fájlban. Ellenőrizze, hogy a hitelesítő adatok már szerepelnek-e benne. Ha nem, adja hozzá őket most:

1. Nyissa meg az **.env** fájlt a modulmegoldásban.
2. Adja hozzá az Azure-tároló beállításjegyzékéből másolt **felhasználónevet** és **jelszóértékeket.**
3. Mentse a módosításokat az .env fájlba.

### <a name="select-your-target-architecture"></a>Válassza ki a célarchitektúrát

Jelenleg a Visual Studio Code C# modulokat fejleszthet Linux AMD64 és ARM32v7 eszközökhöz. Ki kell választania, hogy az egyes megoldásokkal melyik architektúrát célozza meg, mert ez befolyásolja a tároló felépítését és futását. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancspalettát, és keressen az **Azure IoT Edge: Set Default Target Platform for Edge Solution (Alapértelmezett célplatform az edge-megoldáshoz) kifejezésre,** vagy válassza ki az ablak alján lévő oldalsávon található parancsikont.

   ![Architektúra ikon kiválasztása az oldalsávon](./media/tutorial-develop-for-linux/select-architecture.png)

2. A parancspalettán válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu virtuális gépet használunk IoT Edge eszközként, így megtartja az alapértelmezett **amd64-et.**

### <a name="review-the-sample-code"></a>A mintakód áttekintése

A létrehozott megoldássablon egy IoT Edge-modul mintakódját tartalmazza. Ez a mintamodul egyszerűen üzeneteket fogad, majd továbbadja azokat. A folyamat funkció bemutatja az IoT Edge egy fontos koncepcióját, amely a modulok egymással való kommunikációját mutatja be.

Minden modul nak több *bemeneti* és *kimeneti* várólistája is lehet a kódban deklarálva. Az eszközön futó IoT Edge hub egy modul kimenetéről egy vagy több modul bemenetére irányítja az üzeneteket. A bemenetek és kimenetek deklarálásának konkrét nyelve nyelveken ként változik, de a koncepció minden modulban azonos. A modulok közötti útválasztásról az [Útvonalak deklarálása](module-composition.md#declare-routes)című témakörben talál további információt.

A projektsablonhoz tartozó C# mintakód a .NET IoT Hub SDK-ból származó [ModuleClient osztályt](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) használja.

1. Nyissa meg a **Program.cs** fájlt, amely a **modulokban/SampleModule/** mappában található.

2. A program.cs keresse meg a **SetInputMessageHandlerAsync metódust.**

3. A [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) metódus beállít egy bemeneti várólistát a bejövő üzenetek fogadására. Tekintse át ezt a módszert, és nézze meg, hogyan inicializálja a input1 nevű bemeneti **várólistát.**

   ![A bemeneti név megkeresése a SetInputMessageCallback konstruktorban](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Ezután keresse meg a **SendEventAsync metódust.**

5. A [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) metódus feldolgozza a fogadott üzeneteket, és beállít egy kimeneti várólistát, hogy továbbadja őket. Tekintse át ezt a módszert, és nézze meg, hogy inicializálja a **output1**nevű kimeneti várólistát.

   ![A kimeneti név megkeresése a SendEventToOutputAsync fájlban](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Nyissa meg a **deployment.template.json** fájlt.

7. Keresse meg **a** modulok tulajdonsága a $edgeAgent kívánt tulajdonságokat.

   Itt két modulnak kell szerepelnie. Az első a **SimulatedTemperatureSensor**, amely alapértelmezés szerint az összes sablonban szerepel, és szimulált hőmérsékleti adatokat biztosít, amelyek segítségével tesztelheti a modulokat. A második a megoldás részeként létrehozott **SampleModule** modul.

8. A fájl alján keresse meg a **$edgeHub** modul kívánt tulajdonságait.

   Az IoT Edge hub modul egyik funkciója az üzenetek irányítása a központi telepítés összes modulja között. Tekintse át az **útvonalak** tulajdonság értékeit. Az első útvonal, **a SampleModuleToIoTHub**helyettesítő**\*** karaktert ( ) használ a SampleModule modul bármely kimeneti várólistájából érkező üzenetek jelzésére. Ezek az üzenetek *$upstream*kerülnek, amely egy fenntartott név, amely az IoT Hubot jelzi. A második útvonal, a sensorToSampleModule a SimulatedTemperatureSensor modulból érkező üzeneteket a SampleModule kódban inicializált *input1* bemeneti várólistára irányítja.

   ![Útvonalak áttekintése a deployment.template.json ban](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Készítsd el és nyomd meg a megoldást

Áttekintette a modulkódot és a központi telepítési sablont néhány kulcsfontosságú központi telepítési fogalmak megértéséhez. Most már készen áll a SampleModule tárolórendszerkép létrehozása, és a tároló rendszerleíró adatbázisba. A Visual Studio-kód IoT-eszközkiterjesztésével ez a lépés a sablonfájlban lévő információk és a megoldásfájlok modulinformációi alapján is létrehozza a központi telepítési jegyzéket.

### <a name="sign-in-to-docker"></a>Bejelentkezés a Dockerbe

Adja meg a tároló beállításjegyzék-hitelesítő adatait a Dockernek, hogy letudja nyomni a tárolórendszerképet a beállításjegyzékben való tároláshoz.

1. Nyissa meg a Visual Studio Code integrált terminálját a **Terminál megtekintése** > **Terminal**lehetőséget választva.

2. Jelentkezzen be a Dockerbe az Azure container beállításjegyzék-hitelesítő adataival, amelyeket a beállításjegyzék létrehozása után mentett.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   A használatát a használatát `--password-stdin`javasolja. Bár ez az ajánlott eljárás éles forgatókönyvekhez ajánlott, ez kívül esik az oktatóanyag hatókörén. További információkért tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referencia.

### <a name="build-and-push"></a>Építsd meg és nyomd

A Visual Studio Code most már hozzáfér a tároló beállításjegyzékéhez, ezért itt az ideje, hogy a megoldáskódot tárolórendszerképpé alakítsa.

1. A Visual Studio Kódkezelőjében kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza **az IoT Edge-megoldás összeállítása és leküldése parancsot.**

   ![IoT Edge-modulok létrehozása és leküldése](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   A build- és leküldéses parancs három műveletet indít el. Először létrehoz egy új mappát a megoldás nevű **config,** amely tartalmazza a teljes központi telepítési jegyzékfájl, beépített információk a központi telepítési sablon és más megoldásfájlokat. Másodszor, fut `docker build` a tárolórendszerkép létrehozása a célarchitektúrának megfelelő dockerfile alapján. Ezután fut `docker push` a rendszerkép-tárház a tároló rendszerleíró adatbázisba.

   Ez a folyamat első alkalommal több percet is igénybe vehet, de a parancsok következő futtatásakor gyorsabb.

2. Nyissa meg a **deployment.amd64.json** fájlt az újonnan létrehozott konfigurációs mappában. A fájlnév a célarchitektúrát tükrözi, így más lesz, ha másik architektúrát választ.

3. Figyelje meg, hogy a helyőrzők két paramétere a megfelelő értékekkel van kitöltve. A **registryCredentials** szakaszban a rendszerleíró adatbázis felhasználónevét és jelszavát az .env fájlból kell leállítani. A **SampleModule** rendelkezik a module.json fájl nevével, verziójával és architektúrájával rendelkező teljes rendszertárházzal.

4. Nyissa meg a **module.json** fájlt a SampleModule mappában.

5. A modulkép verziószámának módosítása. (A verzió, nem a $schema-verzió.) Például, növekmény a javítás verziószámát **0.0.2,** mintha tettünk egy kis javítást a modul kódot.

   >[!TIP]
   >A modulverziók engedélyezik a verziókövetést, és lehetővé teszik a módosítások tesztelését az eszközök kis készletén, mielőtt frissítéseket telepítene éles környezetbe. Ha nem növekszik a modul verzió létrehozása előtt és lenyomása előtt, majd felülírja a tárház a tároló rendszerleíró adatbázisban.

6. Mentse a módosításokat a module.json fájlba.

7. Kattintson ismét a jobb gombbal a **deployment.template.json** fájlra, majd kattintson ismét **az IoT Edge-megoldás összeállítása és leküldése parancsra.**

8. Nyissa meg újra a **deployment.amd64.json** fájlt. Figyelje meg, hogy nem jött létre új fájl a build- és leküldéses parancs ismételt leküldéses parancsának ismételt leküldéses parancsának ismételt létrehozásakor. Ehelyett ugyanazt a fájlt frissítették, hogy tükrözze a változásokat. A SampleModule-lemezkép most a tároló 0.0.2-es verziójára mutat.

9. A build- és leküldéses parancs további ellenőrzéséhez nyissa meg az [Azure Portalt,](https://portal.azure.com) és keresse meg a tároló beállításjegyzékét.

10. A tároló beállításjegyzékében válassza **az Adattárak,** majd **a samplemodule**lehetőséget. Ellenőrizze, hogy a rendszerkép mindkét verziója a rendszerleíró adatbázisba került-e.

    ![Mindkét lemezképverzió megtekintése a tároló beállításjegyzékében](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Hibaelhárítás

Ha hibákat tapasztal a modulkép létrehozása kor és lenyomásakor, gyakran a fejlesztői gépen lévő Docker-konfigurációval van kapcsolatos. A konfiguráció áttekintéséhez használja az alábbi ellenőrzéseket:

* A parancsot `docker login` a tárolóbeállításjegyzékből másolt hitelesítő adatokkal futtatta? Ezek a hitelesítő adatok eltérnek az Azure-ba való bejelentkezéshez használt hitelesítő adatoktól.
* Helyes a tárolótár? Rendelkezik a megfelelő tárolóbeállítás-bejegyzés nevével és a megfelelő modulnévvel? Nyissa meg az ellenőrizve kívánt **Module.json** fájlt a SampleModule mappában. A tárház értékének a ** \<rendszerleíró adatbázis "azurecr.io/samplemodule .\>**
* Ha a modulhoz a **SampleModule-tól** eltérő nevet használt, akkor ez a név konzisztens a megoldásban?
* A gépe ugyanolyan típusú konténereket üzemeltet, mint amit épít? Ez az oktatóanyag Linux IoT Edge-eszközökhöz való, ezért a Visual Studio Code-nak **amd64-et** vagy **arm32v7-et** kell mondania az oldalsó sávban, és a Docker Desktopnak Linux-tárolókat kell futtatnia.  

## <a name="deploy-modules-to-device"></a>Modulok telepítése az eszközre

Ellenőrizte, hogy a beépített tárolórendszerképek a tároló beállításjegyzékében tárolódnak-e, ezért itt az ideje, hogy telepítse őket egy eszközre. Győződjön meg arról, hogy az IoT Edge-eszköz működik.

1. A Visual Studio Kódkezelőben bontsa ki az Azure IoT Hub-eszközök szakaszt.

2. Kattintson a jobb gombbal arra az IoT Edge-eszközre, amelybe telepíteni szeretné, majd válassza **a Központi telepítés létrehozása egyetlen eszközre parancsot.**

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-develop-for-linux/create-deployment.png)

3. A fájlkezelőben keresse meg a **konfigurációs** mappát, majd jelölje ki a **deployment.amd64.json** fájlt.

   Ne használja a deployment.template.json fájlt, amely nem rendelkezik a tároló rendszerleíró hitelesítő adataival vagy modulképértékeivel. Ha egy Linux ARM32-eszközt céloz meg, a központi telepítési jegyzékfájl neve deployment.arm32v7.json lesz.

4. Bontsa ki az IoT Edge-eszköz adatait, majd **bontsa** ki az eszköz modulok listáját.

5. A frissítés gombbal frissítheti az eszköznézetet, amíg meg nem jelenik az eszközön futó SimulatedTemperatureSensor és SampleModule modulok.

   Mindkét modul indítása eltarthat néhány percig. Az IoT Edge-futásidejű kell kapnia az új központi telepítési jegyzékfájl, húzza le a modullemezképeket a tároló futásidejű, majd indítsa el az egyes új modul.

   ![IoT Edge-eszközön futó modulok megtekintése](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Üzenetek megtekintése az eszközről

A SampleModule kód fogadja az üzeneteket a bemeneti várólistán keresztül, és továbbítja azokat a kimeneti várólistán keresztül. A központi telepítési jegyzékfájl deklarált útvonalak, amelyek üzeneteket továbbítottak samplemodule a SimulatetTemperatureSensor, majd továbbította az üzeneteket SampleModule az IoT Hub. Az Azure IoT-eszközök a Visual Studio-kód lehetővé teszi, hogy az üzenetek et az egyes eszközökről az IoT Hubhoz érkezve láthatja.

1. A Visual Studio Kódkezelőjében kattintson a jobb gombbal a figyelni kívánt IoT Edge-eszközre, majd válassza **a Beépített eseményvégpont figyelésének megkezdése parancsot.**

2. Tekintse meg a Visual Studio-kód kimeneti ablakát az IoT-központba érkező üzenetek megtekintéséhez.

   ![Bejövő eszköz megtekintése felhőalapú üzenetekbe](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Változások megtekintése az eszközön

Ha szeretné látni, mi történik az eszközön is, használja az ebben a szakaszban található parancsokat az IoT Edge futtató- és az eszközön futó modulok vizsgálatához.

Ebben a szakaszban a parancsok az IoT Edge-eszköz, nem a fejlesztői gép. Ha egy virtuális gépet használ az IoT Edge-eszközhöz, csatlakozzon hozzá most. Az Azure-ban lépjen a virtuális gép áttekintő oldalára, és válassza a **Csatlakozás** lehetőséget a biztonságos rendszerhéj-kapcsolat eléréséhez.

* Az eszközre telepített összes modul megtekintése és állapotuk ellenőrzése:

   ```bash
   iotedge list
   ```

   Négy modulnak kell megjelennie: a két IoT Edge futásidejű modul, a SimulatedTemperatureSensor és a SampleModule. Mind a négyet futóként kell felsorolni.

* Vizsgálja meg a naplókat egy adott modul:

   ```bash
   iotedge logs <module name>
   ```

   Az IoT Edge-modulok kis- és nagybetűket megkülönböztetők.

   A SimulatedTemperatureSensor és samplemodule naplóknak meg kell jelenniük a feldolgozásuk általuk feldolgozott üzeneteket. Az edgeAgent modul felelős a többi modul elindításáért, így a naplók információkat tartalmaznak a központi telepítési jegyzék megvalósításáról. Ha valamelyik modul nem szerepel a listában, vagy nem fut, az edgeAgent naplók valószínűleg a hibákat. Az edgeHub modul felelős a modulok és az IoT Hub közötti kommunikációért. Ha a modulok futnak és futnak, de az üzenetek nem érkeznek meg az IoT hub, az edgeHub naplók valószínűleg a hibákat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a fejlesztői gépen beállította a Visual Studio-kódot, és üzembe helyezte az első IoT Edge-modult. Most, hogy már ismeri az alapfogalmakat, próbálja meg hozzá a modul továbbfejlesztett funkcióit, hogy elemezhesse az adatokat. Válassza ki a kívánt nyelvet:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java-csomópont.js](tutorial-node-module.md)
> [Java](tutorial-java-module.md)
> [Python](tutorial-python-module.md)
