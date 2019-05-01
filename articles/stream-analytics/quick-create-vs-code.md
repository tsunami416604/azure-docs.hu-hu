---
title: Az Azure Stream Analytics felhőalapú feladat létrehozása a Visual Studio Code (előzetes verzió)
description: A rövid útmutató bemutatja, hogyan kezdheti el a Stream Analytics-feladat létrehozása, a beállítani a bemeneteket és kimeneteket, és a egy lekérdezés, Visual Studio Code-dal meghatározásával.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: dd6d527020bbf5e2fb510fa9605af408673e89dd
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514235"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Gyors útmutató: Az Azure Stream Analytics felhőalapú feladat létrehozása a Visual Studio Code (előzetes verzió)

Ez a rövid útmutató bemutatja, hogyan hozhat létre és futtathat a Visual Studio Code az Azure Stream Analytics-bővítmény használata a Stream Analytics-feladat. A példa feladat streamelési adatokat olvas be egy IoT Hub-eszköz. Megadhat egy feladatot, amely kiszámítja az átlaghőmérséklet, ha több mint 27° és a létrejövő kimenet egy új fájlba, az események írása a blob storage.

## <a name="before-you-begin"></a>Előzetes teendők

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

* Telepítés [Visual Studio Code](https://code.visualstudio.com/).

* Töltse le a [VS Code-bővítmény az Azure Stream Analytics](https://usqldownload.blob.core.windows.net/ext/asa/vscode-asa-0.0.2.vsix).

## <a name="install-the-azure-stream-analytics-extension"></a>Az Azure Stream Analytics-bővítményének telepítése

Az Azure Stream Analytics-bővítmény telepítése letöltött privát VSIX csomagból.

1. Nyissa meg a Visual Studio Code-ot.

2. A **bővítmények** a bal oldali panelen kattintson a három pontra **(...)**  jobb felső sarokban lévő. Válassza ki **VSIX telepíthessenek**.

   ![Telepítse a Visual Studio Code-ban VSIX](./media/quick-create-vs-code/install-vsix.png)

3. Válassza az előfeltételként letöltötte a bővítményt, majd válasszon **telepítése**.  Ez eltarthat néhány másodpercig.

4. Ha a telepítés sikeresen befejeződött, válassza ki a **újbóli betöltés most** az előugró ablak, ha a rendszer kéri.

5. Ellenőrizze, hogy **Azure Stream Analytics Tools** látható a **engedélyezett bővítmények**.

   ![Az Azure Stream Analytics Tools alatt engedélyezve van a Visual Studio Code-bővítmények](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Aktiválja az Azure Stream Analytics-bővítmény

1. Válassza ki a **Azure** ikonra a tevékenységsávon VS Code. **Stream Analytics** meg fognak jelenni az oldalsó sáv. A **Stream Analytics**válassza **jelentkezzen be Azure**. 

   ![Jelentkezzen be az Azure-bA a Visual Studio Code-ban](./media/quick-create-vs-code/azure-sign-in.png)

2. Ha bejelentkezett, az Azure-fiók neve megjelenik a VS Code-ablak bal alsó sarkában található állapotsoron.

> [!NOTE]
> Az Azure Stream Analytics tools, automatikusan bejelentkezik a következő időpontban, ha nem jelentkezik. Ha a fiókja rendelkezik a kétfaktoros hitelesítés, javasoljuk, hogy használ-e az PIN-kód helyett telefonos hitelesítés.
> Ha problémákba ütközik erőforrások listázása, jelentkezzen ki, és jelentkezzen be újra általában segít. Jelentkezzen ki, adja meg a parancs `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

Mielőtt meghatározná a Stream Analytics-feladat, készítse elő az adatokat, amelyeket később a feladat bemeneti van konfigurálva. A feladat bemeneti adatainak előkészítéséhez, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **IoT Hub** elemet.

3. Az a **az IoT Hub** panelen adja meg a következőket:
   
   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Előfizetés  | \<Az Ön előfizetése\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. |
   |Régió  |  \<Válassza ki a felhasználóihoz legközelebb eső régiót\> | Válasszon egy földrajzi hely, ahol az IoT hubot üzemeltetni. A felhasználókhoz legközelebb eső helyet használja. |
   |Iot Hub-példány neve  | MyASAIoTHub  |   Válassza ki az IoT Hub nevét.   |

   ![IoT Hub létrehozása](./media/quick-create-vs-code/create-iot-hub.png)

4. Válassza ki **tovább: Állítsa be, mérete és méretezése**.

5. Válasszon **tarifacsomag és méretet**. Ez a rövid útmutatóhoz válassza a **F1 – ingyenes** réteg, ha továbbra is elérhető az előfizetésén. Ha az ingyenes szint nem érhető el, válassza ki az elérhető legalacsonyabb szintű. További információkért lásd: [IoT Hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Méretezés és skálázhatja az IoT hubhoz](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget. Az IoT Hub adatait, és kattintson a **létrehozás**. Az IoT Hub létrehozása néhány percet is igénybe vehet. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

7. Az IoT Hub navigációs menüben kattintson a **Hozzáadás** alatt **IoT-eszközök**. Adjon hozzá egy **Eszközazonosító** kattintson **mentése**.

   ![Hozzáad egy eszközt az IoT hubhoz](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Az eszköz létrehozása után nyissa meg az eszköz regisztrációját az **IoT-eszközök** listája. Másolás a **kapcsolati karakterlánc – elsődleges kulcs** , és mentse a Jegyzettömbben későbbi használat céljából.

   ![Az IoT Hub eszköz kapcsolati karakterlánc másolása](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Blob-tároló létrehozása

1. Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Storage** > **Tárfiók** lehetőséget.

2. Az a **storage-fiók létrehozása** panelen adjon meg egy fiók nevét, helyen és erőforráscsoportban csoportjához. Az IoT Hub létrehozott, válassza az ugyanazon a helyen és erőforráscsoportban. Kattintson a **felülvizsgálat + létrehozása** a fiók létrehozásához.

   ![Storage-fiók létrehozása](./media/quick-create-vs-code/create-storage-account.png)

3. A tárfiók létrehozása után válassza ki a **Blobok** csempét a **áttekintése** ablaktáblán.

   ![Tárfiókok áttekintése](./media/quick-create-vs-code/blob-storage.png)

4. Az a **Blob Service** lapon jelölje be **tároló** és adja meg például a tároló nevét *container1*. Hagyja a **nyilvános hozzáférés szintje** , **privát (nincs névtelen hozzáférés)** válassza **OK**.

   ![Blobtároló létrehozása](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Stream Analytics-projekt létrehozása

1. A Visual Studio Code-ban nyomja le a **Ctrl + Shift + P** a parancskatalógus megnyitásához. Írja be **ASA** válassza **ASA: Új projekt létrehozása**.

   ![Új projekt létrehozása](./media/quick-create-vs-code/create-new-project.png)

2. Adjon meg a projekt nevére, például **myASAproj** , és válasszon ki egy mappát a projekthez.

    ![Hozzon létre a projekt neve](./media/quick-create-vs-code/create-project-name.png)

3. Az új projekt a rendszer felveszi a munkaterülethez. Az ASA-projekt tartalmazza a lekérdezési szkriptet **(*.asaql)**, amely egy **JobConfig.json** fájlt, és a egy **asaproj.json** konfigurációs fájlt.

   ![Stream Analytics projektfájlokat a VS Code-ban](./media/quick-create-vs-code/asa-project-files.png)

4. A asaproj.json konfigurációs fájl tartalmazza a bemenetek, kimenetek és a feladat konfigurációs fájl információkat, amelyeket az Azure-bA az ASA-feladat elküldése.

   ![Stream Analytics feladat konfigurációs fájlt a VS Code-ban](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Bemenetek és kimenetek való hozzáadáskor a parancskatalógus, a megfelelő elérési utak be fog bővülni **asaproj.json** automatikusan. Ha hozzáadása vagy eltávolítása a bemeneti vagy kimeneti lemezen közvetlenül, szeretné-e manuálisan adja hozzá, vagy távolítsa el őket a **asaproj.json**. Dönthet úgy, hogy a bemeneti adatok put és a egy kimenetek helyezzük majd hivatkozni tudjon rájuk a különböző feladatok az elérési út megadásával az egyes **asaproj.json**.

## <a name="define-an-input"></a>Egy bemeneti definiálása

1. Válassza ki **Ctrl + Shift + P** a parancskatalógus megnyitásához, és írja be **ASA: Bemenet hozzáadása**.

   ![A VS Code-ban a Stream Analytics bemenet hozzáadása](./media/quick-create-vs-code/add-input.png)

2. Válasszon **az IoT Hub** a bemeneti típus.

   ![Válassza ki az IoT hubot, beállítás megadása](./media/quick-create-vs-code/iot-hub.png)

3. Válassza ki az ASA lekérdezési szkriptet, amely a bemeneti fogja használni. Automatikusan kitölti a fájl elérési útját **myASAproj.asaql**.

   ![Válassza ki az ASA-parancsfájlok a Visual Studio Code-ban](./media/quick-create-vs-code/asa-script.png)

4. Adja meg a bemeneti fájl neve megegyezik **IotHub.json**.

5. Szerkesztés **IoTHub.json** a következő értékeket. Tartsa meg az alábbi nem említett mezők alapértelmezett értékeit. A CodeLens használatával adjon meg egy karakterláncot, vagy válassza ki a legördülő listából.

   |Beállítás|Ajánlott érték|Leírás|
   |-------|---------------|-----------|
   |Name (Név)|Input (Bemenet)|Adja meg a feladat bemenetének azonosító nevét.|
   |IotHubNamespace|MyASAIoTHub|Válassza ki vagy adja meg az IoT Hub nevét. Az IoT Hub nevét a rendszer automatikusan észleli, ha ugyanabban az előfizetésben.|
   |Végpont|Üzenetkezelés| |
   |SharedAccessPolicyName|iothubowner| |

## <a name="define-an-output"></a>Kimenet definiálása

1. Válassza ki **Ctrl + Shift + P** a parancskatalógus megnyitásához. Ezután írja be a **ASA: Adja hozzá a kimenetet**.

   ![Adja hozzá a Stream Analytics-kimenetben a VS Code-ban](./media/quick-create-vs-code/add-output.png)

2. Válasszon **Blob Storage** a fogadó típusa számára.

3. Válassza ki az ASA lekérdezési szkriptet, amely a bemeneti fogja használni.

4. Adja meg a kimeneti fájl nevét, **BlobStorage.json**.

5. Szerkesztés **BlobStorage.json** a következő értékeket. Tartsa meg az alábbi nem említett mezők alapértelmezett értékeit. A CodeLens használatával adjon meg egy karakterláncot, vagy válassza ki a legördülő listából.

   |Beállítás|Ajánlott érték|Leírás|
   |-------|---------------|-----------|
   |Name (Név)|Kimenet| Adja meg a feladat kimenetének azonosító nevét.|
   |Tárfiók|asaquickstartstorage|Válassza ki vagy adja meg a tárfiók nevét. Ha a tárfiókok ugyanahhoz az előfizetéshez tartoznak, a rendszer automatikusan észleli a nevüket.|
   |Tároló|container1|Válassza ki a tárfiókjában létrehozott meglévő tárolót.|
   |Path Pattern (Elérésiút-minta)|output|Adja meg a tárolón belül létrehozni kívánt elérési út nevét.|

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

1. Nyissa meg **myASAproj.asaql** a projektmappa parancssorából.

2. Adja hozzá az alábbi lekérdezést:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="compile-script"></a>Parancsfájl összeállítása

Parancsfájl összeállítása két dolgot eredményez: Ellenőrizze a szintaxist, és hozzon létre az Azure Resource Manager-sablonok, az automatikus telepítéshez.

Parancsprogram-fordítási elindítani két módja van:

1. Válassza ki a parancsprogramot a munkaterületről, és a parancskatalógus fordítási majd indít. 

   ![A VS Code parancskatalógus használata a parancsfájl összeállításához.](./media/quick-create-vs-code/compile-script1.png)

2. A jobb gombbal a parancsfájlt, majd válassza a **ASA: parancsfájl összeállításához**.

    ![Kattintson a jobb gombbal az ASA parancsfájl összeállítása](./media/quick-create-vs-code/compile-script2.png)

3. Lefordítása után annak a két létrehozott Azure Resource Manager-sablonok a **telepítés** mappát a projekt. Két fájlt az automatikus telepítéshez szolgálnak.

## <a name="submit-a-stream-analytics-job-to-azure"></a>Az Azure Stream Analytics-feladat elküldése

1. A Visual Studio Code-ot, a parancsprogram-szerkesztő ablakban válassza ki a **kiválasztása az előfizetések közül**.

   ![Válassza ki a parancsprogram-szerkesztő előfizetések szövegből](./media/quick-create-vs-code/select-subscription.png)

2. A helyi menü listából válassza ki előfizetését.

3. Válassza ki **válasszon ki egy feladatot**. Majd hozzon létre egy új feladatot.

4. Adja meg a feladat nevét **myASAjob** és kövesse az utasításokat követve válassza ki az erőforráscsoportot és helyet.

5. Válassza ki **küldje el az Azure-bA**. A naplók a kimeneti ablakban található. 

6. A feladat létrehozását, láthatja a Stream Analytics Explorerben.

## <a name="run-the-iot-simulator"></a>Az IoT-szimulátor futtatásához

1. Nyissa meg a [Raspberry Pi Azure IoT Online szimulátor](https://azure-samples.github.io/raspberry-pi-web-simulator/) egy új böngészőlapon vagy ablakban.

2. A sor 15 a helyőrzőt cserélje le az Azure IoT Hub eszköz kapcsolati karakterláncát az egyik előző szakaszban mentett.

3. Kattintson a **Run** (Futtatás) parancsra. A kimenet az érzékelő adatokat és az IoT hubnak küldött üzenetek kell megjelennie.

   ![Raspberry Pi az Azure IoT Online szimulátor](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

1. Stream Analytics megnyitása a Visual Studio Code-ban, és keresse meg a feladat**myASAJob**.

2. Kattintson a jobb gombbal a projekt nevére. Ezután válassza ki **Start** a helyi menüből.

3. Válasszon **most** az előugró ablakban indítsa el a feladatot.

4. Megjegyzés: a feladat állapota módosult az **futó**. Kattintson a jobb gombbal a projekt nevére, és válassza a **feladat nézet megnyitása a portál** tekintse meg a bemeneti és kimeneti esemény metrikákat. Ez eltarthat néhány percig.

5. Az eredmények megtekintéséhez nyissa meg a blob storage, a Visual Studio Code-bővítmény vagy az Azure Portalon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az útmutatóban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** menüpontot, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport lapján válassza a **Törlés** elemet, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban üzembe helyezett egy egyszerű Stream Analytics-feladatot a Visual Studio Code használatával. Stream Analytics-feladatok használatával is telepítheti a [az Azure portal](stream-analytics-quick-create-portal.md), [PowerShell](stream-analytics-quick-create-powershell.md), és a Visual Studio (stream-analytics-quick-létrehozása-vs.md). 

További információ az Azure Stream Analytics tools for Visual Studio, folytassa a következő cikket:

> [!div class="nextstepaction"]
> [A Visual Studio használata az Azure Stream Analytics-feladatok megtekintése](stream-analytics-vs-tools.md)