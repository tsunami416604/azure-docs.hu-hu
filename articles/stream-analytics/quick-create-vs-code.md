---
title: Rövid útmutató – Azure Stream Analytics-feladat létrehozása a Visual Studio-kódban
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a folyamatot egy Stream Analytics-feladat létrehozásával, a bemenetek és kimenetek konfigurálásával, valamint egy lekérdezés Visual Studio-kóddal történő meghatározásával.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: e3e878b4f548da64ab04eb079d51b0846cf35c57
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76313875"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Rövid útmutató: Azure Stream Analytics-feladat létrehozása a Visual Studio-kódban (előzetes verzió)

Ez a rövid útmutató bemutatja, hogyan hozhat létre és futtathatja az Azure Stream Analytics-feladatot az Azure Stream Analytics-eszközök visual studio-kódbővítményhasználatával. A példafeladat beolvassa a streamelési adatokat egy Azure IoT Hub-eszközről. Olyan feladatot határoz meg, amely 27°-nál nagyobb idő esetén kiszámítja az átlaghőmérsékletet, és az eredményül kapott kimeneti eseményeket egy új fájlba írja a blob storage-ban.

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

* Telepítse [a Visual Studio-kódot](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Az Azure Stream Analytics Tools bővítmény telepítése

1. Nyissa meg a Visual Studio Code-ot.

2. A bal oldali ablaktáblában található **bővítményekből** keresse meg a **Stream Analytics** kifejezést, és válassza a **Telepítés** az Azure **Stream Analytics-eszközök** bővítményben lehetőséget.

3. A bővítmény telepítése után ellenőrizze, hogy az **Azure Stream Analytics-eszközök** **láthatók-e**az Engedélyezett bővítmények alkalmazásban.

   ![Azure Stream Analytics-eszközök a Visual Studio-kód engedélyezett bővítményei alatt](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Az Azure Stream Analytics Tools bővítmény aktiválása

1. Válassza ki az **Azure** ikont a Visual Studio Kód tevékenységsávján. Az **oldalsávon** a Stream Analytics csoportban válassza a Bejelentkezés az **Azure-ba**lehetőséget.

   ![Bejelentkezés az Azure-ba a Visual Studio-kódban](./media/quick-create-vs-code/azure-sign-in.png)

2. Amikor be van jelentkezve, az Azure-fiók neve megjelenik a Visual Studio-kód ablak bal alsó sarkában lévő állapotsoron.

> [!NOTE]
> Az Azure Stream Analytics-eszközök bővítmény automatikusan aláírja a következő alkalommal, ha nem jelentkezik ki. Ha fiókjában kétfaktoros hitelesítés van, azt javasoljuk, hogy pin-kód helyett használjon telefonhitelesítést.
> Ha problémái vannak az erőforrások listázásával, a kijelentkezés és az ismételt bejelentkezés általában segít. A kijelentkezéshez `Azure: Sign Out`írja be a parancsot.

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics-feladat definiálása előtt elő kell készítenie azokat az adatokat, amelyek később feladatbemenetként vannak konfigurálva. A feladat által igényelt bemeneti adatok előkészítéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Válassza > **az** >  **Erőforrás-internet**létrehozása**IoT Hub**lehetőséget.

3. Az **IoT Hub** ablaktáblán adja meg a következő adatokat:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Előfizetés  | \<Az Ön előfizetése\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. |
   |Régió  |  \<Válassza ki a felhasználóihoz legközelebb eső régiót\> | Válassza ki azt a földrajzi helyet, ahol az IoT-központot üzemeltetheti. Használja a felhasználókhoz legközelebb eső helyet. |
   |IoT hub neve  | MyASAIoTHub  |   Válassza ki az IoT hub nevét.   |

   ![IoT Hub létrehozása](./media/quick-create-vs-code/create-iot-hub.png)

4. Válassza a **Tovább lehetőséget: Méret és méretezés beállítása**lehetőséget.

5. Válasszon a **Tarifa- és méretezési szinthez.** Ehhez a rövid útmutatóhoz válassza az **F1 – Ingyenes** szintet, ha az még elérhető az előfizetésén. Ha az ingyenes szint nem érhető el, válassza ki a legalacsonyabb elérhető szintet. További információ: [Azure IoT Hub díjszabása.](https://azure.microsoft.com/pricing/details/iot-hub/)

   ![Az IoT-központ méretezése és méretezése](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget. Tekintse át az IoT-központ adatait, és válassza **a Létrehozás lehetőséget.** Az IoT Hub létrehozása eltarthat néhány percig. Az **Értesítések** ablaktáblán figyelemmel kísérheti a folyamatot.

7. Az IoT-központ navigációs menüjében válassza **a Hozzáadás** lehetőséget az **IoT-eszközök**csoportban. Adjon hozzá egy azonosítót **az eszközazonosítóhoz,** és válassza a **Mentés gombot.**

   ![Eszköz hozzáadása az IoT-központhoz](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Az eszköz létrehozása után nyissa meg az eszközt az **IoT-eszközök** listájából. Másolja a karakterláncot a **Kapcsolati karakterláncba (elsődleges kulcs),** és mentse egy jegyzettömbbe, hogy később használhassa.

   ![IoT-központ eszközkapcsolati karakterláncának másolása](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Az IoT-szimulátor futtatása

1. Nyissa meg a [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) t egy új böngészőlapon vagy ablakban.

2. Cserélje le a helyőrzőt a 15-ös sorban az IoT hub eszköz kapcsolati karakterláncára, amelyet korábban mentett.

3. Válassza a **Futtatás** lehetőséget. A kimenetnek meg kell jelennie az IT hubba küldött érzékelő adatok és üzenetek között.

   ![Raspberry Pi Azure IoT Online Simulator kimenettel](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Blob-tárterület létrehozása

1. Az Azure Portal bal felső sarkában válassza az > **erőforrástár-fiók** **Create a resource** > **Storage**létrehozása lehetőséget.

2. A **Tárfiók létrehozása** ablaktáblán adja meg a tárfiók nevét, helyét és erőforráscsoportját. Válassza ki ugyanazt a helyet és erőforráscsoportot, mint a létrehozott IoT hub. Ezután a fiók létrehozásához válassza a **Véleményezés + létrehozás** lehetőséget.

   ![Storage-fiók létrehozása](./media/quick-create-vs-code/create-storage-account.png)

3. A tárfiók létrehozása után válassza ki a **Blobok csempét** az **Áttekintő** ablaktáblán.

   ![Tárfiókok áttekintése](./media/quick-create-vs-code/blob-storage.png)

4. A **Blob Service** lapon válassza a **Tároló** lehetőséget, és adja meg a tároló nevét, például **a container1.** Hagyja **a nyilvános hozzáférési szintet** **privátként (névtelen hozzáférés nélkül),** és válassza **az OK**gombot.

   ![Blobtároló létrehozása](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Stream Analytics-projekt létrehozása

1. A Visual Studio-kódban válassza a **Ctrl+Shift+P** billentyűkombinációt a parancspaletta megnyitásához. Ezután írja be **az ASA** billentyűt, és válassza **az ASA: Új projekt létrehozása**lehetőséget.

   ![Új projekt létrehozása](./media/quick-create-vs-code/create-new-project.png)

2. Írja be a projekt nevét , például **a myASAproj**, és válasszon ki egy mappát a projekthez.

    ![Projektnév létrehozása](./media/quick-create-vs-code/create-project-name.png)

3. Az új projekt hozzáadódik a munkaterülethez. A Stream Analytics projekt három mappából áll: **bemenetek**, **kimenetek**és **függvények**. Azt is a lekérdezés iscript **(*.asaql),** a **JobConfig.json** fájlt, és egy **asaproj.json konfigurációs** fájlt.

    Az **asaproj.json** konfigurációs fájl tartalmazza a bemenetek, kimenetek és a feladat konfigurációs fájl információi a Stream Analytics-feladat azure-ba küldéséhez szükséges információkat.

    ![Stream Analytics-projektfájlok a Visual Studio-kódban](./media/quick-create-vs-code/asa-project-files.png)

> [!Note]
> Amikor bemeneteket és kimeneteket ad hozzá a parancspalettáról, a megfelelő elérési utak automatikusan hozzáadódnak az **asaproj.json hoz.** Ha közvetlenül a lemezen adja hozzá vagy távolítja el a bemeneteket vagy kimeneteket, manuálisan kell hozzáadnia vagy eltávolítania őket **az asaproj.json ból.** Az **asaproj.json** fájlokelérési útjainak megadásával elhelyezheti a bemeneteket és a kimeneteket egy helyre, majd különböző feladatokban hivatkozhat rájuk.

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

1. Nyissa meg **a myASAproj.asaql** fájlt a projekt mappájából.

2. Adja hozzá az alábbi lekérdezést:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="test-the-query-locally-with-sample-data"></a>A lekérdezés helyi tesztelése mintaadatokkal

Mielőtt futtatja a lekérdezést a felhőben, tesztelheti azt helyileg egy helyi mintaadatfájllal vagy az élő bemenetről rögzített adatokkal a lekérdezési logika ellenőrzéséhez.

További részletekért kövesse a [Lekérdezések helyi tesztelése című, helyi mintaadatokkal](visual-studio-code-local-run.md) című útmutatóutasításait.

 ![Tesztelés mintaadatokkal a Visual Studio-kódban](./media/vscode-local-run/localrun-localinput.gif)

## <a name="define-a-live-input"></a>Élő bemenet definiálása

1. Kattintson a jobb gombbal a **Bemenetek** mappára a Stream Analytics projektben. Ezután válassza **az ASA: Add Input** a helyi menüből.

    ![Bemenet hozzáadása a Bemenetek mappából](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

    Vagy válassza **a Ctrl+Shift+P** lehetőséget a parancspaletta megnyitásához és az **ASA: Input hozzáadása**mezőbe.

   ![Stream Analytics-bevitel hozzáadása a Visual Studio-kódban](./media/quick-create-vs-code/add-input.png)

2. Válassza az **IoT Hub** a bemeneti típus.

   ![Az IoT-központ kiválasztása beviteli lehetőségként](./media/quick-create-vs-code/iot-hub.png)

3. Ha hozzáadta a bemenetet a parancspalettáról, válassza ki a Stream Analytics lekérdezési parancsfájlt, amely a bemenetet fogja használni. Meg kell automatikusan kitölteni a fájl elérési útját **myASAproj.asaql**.

   ![Stream Analytics-parancsfájl kiválasztása a Visual Studio-kódban](./media/quick-create-vs-code/asa-script.png)

4. Válassza **a Kijelölés az Azure-előfizetések közül** a legördülő menüből.

    ![Válasszon az előfizetések közül](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Az újonnan létrehozott **IoTHub1.json-fájl szerkesztése** a következő értékekkel. Az itt nem említett mezők alapértelmezett értékeinek megtartása.

   |Beállítás|Ajánlott érték|Leírás|
   |-------|---------------|-----------|
   |Név|Input (Bemenet)|Adjon meg egy nevet a feladat bevitelének azonosításához.|
   |IotHubNamespace|MyASAIoTHub|Válassza ki vagy írja be az IoT hub nevét. Az IoT hub nevek automatikusan észleli, ha ugyanabban az előfizetésben jönnek létre.|
   |SharedAccessPolicyName|iothubowner| |

   A CodeLens szolgáltatással karakterláncot írhat be, legördülő listából választhat, vagy közvetlenül a fájlban módosíthatja a szöveget. Az alábbi képernyőképen példaként **a Kijelölés az Előfizetések közül** látható. A hitelesítő adatok automatikusan szerepelnek, és a helyi hitelesítő adatok kezelőjében tárolódnak.

   ![Bemenet konfigurálása a Visual Studio-kódban](./media/quick-create-vs-code/configure-input.png)

   ![Bemeneti érték konfigurálása a Visual Studio-kódban](./media/quick-create-vs-code/configure-input-value.png)

## <a name="preview-input"></a>Előzetes bemenet

Válassza **az Adatok előnézete** az **IoTHub1.json** ban a felső sorban. Egyes bemeneti adatok beolvasása az IoT hubból lesz behívva, és megjelenik az előnézeti ablakban. Ez a folyamat eltarthat egy ideig.

 ![Élő bevitel előnézete](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-an-output"></a>Kimenet definiálása

1. A parancspaletta megnyitásához válassza a **Ctrl+Shift+P** billentyűkombinációt. Ezután írja be **az ASA: Add Output**( Kimenet hozzáadása ) értéket.

   ![Stream Analytics-kimenet hozzáadása a Visual Studio-kódban](./media/quick-create-vs-code/add-output.png)

2. Válassza a **Blob Storage lehetőséget** a fogadó típusához.

3. Válassza ki a Stream Analytics lekérdezési parancsfájlt, amely ezt a bemenetet fogja használni.

4. Adja meg a kimeneti fájl nevét **BlobStorage**néven.

5. A **BlobStorage** szerkesztése a következő értékek használatával. Az itt nem említett mezők alapértelmezett értékeinek megtartása. A CodeLens funkcióval legördülő listából választhat, vagy karakterláncot írhat be.

   |Beállítás|Ajánlott érték|Leírás|
   |-------|---------------|-----------|
   |Név|Kimenet| Adjon meg egy nevet a feladat kimenetének azonosításához.|
   |Tárfiók|asaquickstartstorage|Válassza ki vagy adja meg a tárfiók nevét. A tárfiókneveket a rendszer automatikusan észleli, ha ugyanabban az előfizetésben jönnek létre.|
   |Tároló|container1|Válassza ki a tárfiókjában létrehozott meglévő tárolót.|
   |Path Pattern (Elérésiút-minta)|output|Adja meg a tárolón belül létrehozni kívánt elérési út nevét.|

   ![Kimenet konfigurálása a Visual Studio-kódban](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>A parancsfájl fordítása

A parancsfájlfordítás ellenőrzi a szintaxist, és létrehozza az Azure Resource Manager-sablonokat az automatikus üzembe helyezéshez.

Kétféleképpen indítható el a parancsfájlok összeállítása:

- Jelölje ki a parancsfájlt a munkaterületről, majd fordítsa le a parancspalettáról.

   ![A parancsfájl fordítása a Visual Studio Code parancspaletta használatával](./media/quick-create-vs-code/compile-script1.png)

- Kattintson a jobb gombbal a parancsfájlra, és válassza **az ASA: Compile Script parancsot.**

    ![A fordításhoz kattintson a jobb gombbal a Stream Analytics parancsfájlra](./media/quick-create-vs-code/compile-script2.png)

A fordítás után a két létrehozott Azure Resource Manager-sablont megtalálhatja a projekt **Üzembe helyezése** mappájában. Ez a két fájl automatikus telepítésre szolgál.

![A Stream Analytics telepítési sablonjai a Fájlkezelőben](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Stream Analytics-feladat küldése az Azure-ba

1. A lekérdezési parancsfájl parancsfájlparancsfájl parancsfájl-szerkesztőablakában válassza a **Küldés az Azure-ba**lehetőséget.

   ![Válasszon az előfizetések szövegéből a parancsfájlszerkesztőben](./media/quick-create-vs-code/submit-job.png)

2. Válassza ki az előfizetést az előugró listából.

3. Válassza **a Feladat kiválasztása**lehetőséget. Ezután válassza **az Új feladat létrehozása**lehetőséget.

4. Adja meg a feladat nevét, **myASAjob**. Ezután kövesse az utasításokat az erőforráscsoport és a hely kiválasztásához.

5. Válassza **a Küldés az Azure-ba**lehetőséget. A naplók a kimeneti ablakban találhatók. 

6. A feladat létrehozásakor láthatja azt a **Stream Analytics Intézőben.**

    ![Listázott feladat a Stream Analytics Intézőben](./media/quick-create-vs-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

1. Nyissa **meg a Stream Analytics Explorert** a Visual Studio-kódban, és keresse meg a **myASAJob állását.**

2. Kattintson a jobb gombbal a feladat nevére. Ezután válassza a helyi menü **Indítás** parancsát.

   ![A Stream Analytics feladat indítása a Visual Studio-kódban](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Válassza a **Most lehetőséget** az előugró ablakban a feladat elindításához.

4. Ne feledje, hogy a feladat állapota **Futás**állapotra változott. Kattintson a jobb gombbal a feladat nevére, és válassza a **Feladatnézet megnyitása a portálon** parancsot a bemeneti és kimeneti eseménymetrikák megtekintéséhez. Ez a művelet eltarthat néhány percig.

5. Az eredmények megtekintéséhez nyissa meg a blob storage a Visual Studio-kód bővítmény ben vagy az Azure Portalon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlése elkerüli a feladat által felhasznált streamelési egységek számlázását. 

Ha a jövőben szeretné használni a feladatot, leállíthatja, és később újraindíthatja. Ha nem fogja újra használni ezt a feladatot, az alábbi lépésekkel törölje az ebben a rövid útmutatóban létrehozott összes erőforrást:

1. Az Azure Portal bal oldali menüjében válassza **az Erőforráscsoportok** lehetőséget, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport oldalán kattintson a **Törlés** parancsra. Írja be a törlendő erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot telepített a Visual Studio-kód használatával. Stream Analytics-feladatokat az [Azure Portal](stream-analytics-quick-create-portal.md), a [PowerShell](stream-analytics-quick-create-powershell.md)és a [Visual Studio](stream-analytics-quick-create-vs.md)használatával is telepíthet.

Ha többet szeretne megtudni az Azure Stream Analytics Eszközök a Visual Studio-kódhoz szolgáltatásról, folytassa az alábbi cikkekkel:

* [Tesztelje az Azure Stream Analytics-feladatokat helyileg az élő bevitellel szemben a Visual Studio-kóddal](visual-studio-code-local-run-live-input.md)

* [Az Azure Stream Analytics-feladatok megtekintése a Visual Studio-kód dal](visual-studio-code-explore-jobs.md)

* [CI/CD-folyamatok beállítása az npm csomag használatával](setup-cicd-vs-code.md)
