---
title: Azure Stream Analytics Cloud-feladatok létrehozása a Visual Studio Code-ban (előzetes verzió)
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el az első lépéseket egy Stream Analytics feladatok létrehozásával, a bemenetek, a kimenetek konfigurálásával és a lekérdezés a Visual Studio Code használatával történő definiálásával.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 3301be3a067982cb90e663fe3782319eb0b90ba0
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673132"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Gyors útmutató: Azure Stream Analytics Cloud-feladatok létrehozása a Visual Studio Code-ban (előzetes verzió)

Ez a rövid útmutató bemutatja, hogyan hozhat létre és futtathat egy Stream Analytics feladatot a Visual Studio Code-hoz készült Azure Stream Analytics bővítménnyel. A példában szereplő művelet beolvassa az adatfolyam-adatokat egy IoT Hub eszközről. Olyan feladatot határozhat meg, amely több mint 27 °-ban kiszámítja az átlagos hőmérsékletet, és az eredményül kapott kimeneti eseményeket egy új fájlba írja a blob Storage-ban.

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

* Telepítse a [Visual Studio Code](https://code.visualstudio.com/)-ot.

## <a name="install-the-azure-stream-analytics-extension"></a>Az Azure Stream Analytics-bővítmény telepítése

1. Nyissa meg a Visual Studio Code-ot.

2. A bal oldali ablaktáblán található **bővítmények** területen keressen rá **stream Analytics** , majd válassza a **telepítés** lehetőséget a **Azure stream Analytics** bővítményben.

3. Miután telepítette a bővítményt, ellenőrizze, hogy a **Azure stream Analytics eszközök** láthatóak-e az **engedélyezett bővítményekben**.

   ![A Visual Studio Code-ban elérhető bővítmények Azure Stream Analytics eszközei](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>A Azure Stream Analytics-bővítmény aktiválása

1. Válassza ki az **Azure** ikont a vs Code tevékenység sávján. **Stream Analytics** látható lesz az oldalsó sávban. A **stream Analytics**területen válassza a **Bejelentkezés az Azure-** ba lehetőséget. 

   ![Bejelentkezés az Azure-ba a Visual Studio Code-ban](./media/quick-create-vs-code/azure-sign-in.png)

2. Amikor bejelentkezett, az Azure-fiók neve megjelenik az állapotsorban a VS Code ablak bal alsó sarkában.

> [!NOTE]
> Azure Stream Analytics eszközök a következő alkalommal automatikusan bejelentkeznek, ha nem kijelentkezik. Ha a fiókja kétfaktoros hitelesítést használ, javasoljuk, hogy PIN-kód helyett telefonos hitelesítést használjon.
> Ha problémák merülnek fel az erőforrások listázása során, a kijelentkezés és az újbóli bejelentkezés általában segít. A kijelentkezéshez írja be a következő parancsot: `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics-feladatnak a meghatározása előtt elő kell készítenie az adatokat, amelyeket később a feladatként megadott bemenetként kell konfigurálni. A feladathoz szükséges bemeneti adatok előkészítéséhez végezze el a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **IoT Hub** elemet.

3. A **IoT hub** ablaktáblán adja meg a következő adatokat:
   
   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Subscription  | \<Az Ön előfizetése\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Resource group   |   asaquickstart-resourcegroup  |   Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. |
   |Régió  |  \<Válassza ki a felhasználóihoz legközelebb eső régiót\> | Válassza ki azt a földrajzi helyet, ahol a IoT Hub üzemeltetheti. Használja a felhasználókhoz legközelebb eső helyet. |
   |Iot Hub-példány neve  | MyASAIoTHub  |   Válassza ki a IoT Hub nevét.   |

   ![IoT Hub létrehozása](./media/quick-create-vs-code/create-iot-hub.png)

4. Válassza **a Next (tovább) lehetőséget: A méret és a skála beállítása @ no__t-0.

5. Válasszon **tarifacsomag és méretet**. Ebben a rövid útmutatóban válassza az **F1 – ingyenes** szintet, ha az előfizetése továbbra is elérhető. Ha az ingyenes szint nem érhető el, válassza ki az elérhető legalacsonyabb szintet. További információ: [IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![A IoT Hub mérete és méretezése](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget. Tekintse át IoT Hub adatait, és kattintson a **Létrehozás**gombra. A IoT Hub létrehozása néhány percet is igénybe vehet. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

7. A IoT Hub navigációs menüjében kattintson a **Hozzáadás** elemre a **IoT eszközök**alatt. Adja meg az **eszköz azonosítóját** , és kattintson a **Mentés**gombra.

   ![Eszköz hozzáadása a IoT Hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Az eszköz létrehozása után nyissa meg az eszközt a **IoT-eszközök** listából. Másolja a **kapcsolódási sztringet – az elsődleges kulcsot** , és mentse egy Jegyzettömbbe, hogy később használhassa.

   ![IoT Hub eszköz-kapcsolatok karakterláncának másolása](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>BLOB Storage létrehozása

1. Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Storage** > **Tárfiók** lehetőséget.

2. A **Storage-fiók létrehozása** panelen adja meg a Storage-fiók nevét, helyét és az erőforráscsoportot. Válassza ki ugyanazt a helyet és erőforráscsoportot, mint a létrehozott IoT Hub. Ezután kattintson a **felülvizsgálat + létrehozás** gombra a fiók létrehozásához.

   ![Storage-fiók létrehozása](./media/quick-create-vs-code/create-storage-account.png)

3. Miután létrehozta a Storage-fiókot, válassza a **Blobok** csempét az **Áttekintés** ablaktáblán.

   ![Tárfiókok áttekintése](./media/quick-create-vs-code/blob-storage.png)

4. A **blob szolgáltatás** lapon válassza a **tároló** lehetőséget, és adja meg a tároló nevét (például *container1*). Hagyja meg a **nyilvános hozzáférési szintet** **magánjellegűként (névtelen hozzáférés nélkül)** , majd kattintson **az OK gombra**.

   ![Blobtároló létrehozása](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Stream Analytics-projekt létrehozása

1. A Visual Studio Code-ban nyomja le a **CTRL + SHIFT + P** billentyűkombinációt a parancs paletta megnyitásához. Ezután írja be az **ASA** értéket, és válassza a **ASA: Hozzon létre egy új projektet @ no__t-0.

   ![Új projekt létrehozása](./media/quick-create-vs-code/create-new-project.png)

2. Adja meg a projekt nevét, például **myASAproj** , és válasszon egy mappát a projekt számára.

    ![Projekt nevének létrehozása](./media/quick-create-vs-code/create-project-name.png)

3. Az új projekt hozzá lesz adva a munkaterülethez. Egy ASA-projekt a lekérdezési parancsfájlból **(*. asaql)** , egy **JobConfig. JSON** fájlból és egy **asaproj. JSON** konfigurációs fájlból áll.

   ![Projektfájlok Stream Analytics a VS Code-ban](./media/quick-create-vs-code/asa-project-files.png)

4. A **asaproj. JSON** konfigurációs fájl tartalmazza a bemeneteket, a kimeneteket és a feladatok konfigurációs fájljának információit, amelyek a stream Analytics feladatnak az Azure-ba való küldéséhez szükségesek.

   ![Stream Analytics a feladatok konfigurációs fájlját a VS Code-ban](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Bemenetek és kimenetek a parancssorból való hozzáadásakor a rendszer automatikusan hozzáadja a megfelelő elérési utakat a **asaproj. JSON** fájlhoz. Ha közvetlenül ad hozzá vagy távolít el bemeneteket vagy kimeneteket a lemezen, manuálisan kell hozzáadnia vagy eltávolítania azokat a **asaproj. JSON**fájlból. Dönthet úgy is, hogy a bemeneteket és kimeneteket egy helyen helyezi el, majd a különböző feladatokban hivatkozik rájuk, ha megadja az elérési utakat az egyes **asaproj. JSON**fájlban.

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

1. Nyissa meg a **myASAproj. asaql** mappát a projekt mappájából.

2. Adja hozzá az alábbi lekérdezést:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```
## <a name="test-with-sample-data"></a>Tesztelés mintaadatokkal
A lekérdezés a felhőben való futtatása előtt tesztelheti a lekérdezést a helyi mintaadatok használatával a lekérdezési logika ellenőrzéséhez.

További részletekért kövesse a [tesztelés a mintaadatok](vscode-local-run.md) alapján című szakasz utasításait. 

 ![Mintaadatok tesztelése a VS Code-ban](./media/quick-create-vs-code/vscode-localrun.gif)

## <a name="define-an-input"></a>Egy bemeneti definiálása

1. Válassza a **CTRL + SHIFT + P** billentyűkombinációt a parancssor megnyitásához, majd írja be a **ASA: Adja hozzá a bemenet @ no__t-0 értéket.

   ![Stream Analytics bemenet hozzáadása a VS Code-ban](./media/quick-create-vs-code/add-input.png)

2. A bemeneti típushoz válassza a **IoT hub** lehetőséget.

   ![IoT Hub kiválasztása bemeneti beállításként](./media/quick-create-vs-code/iot-hub.png)

3. Válassza ki azt az ASA-lekérdezési parancsfájlt, amely a bemenetet fogja használni. Automatikusan feltölti a fájlt a **myASAproj. asaql**fájl elérési útjával.

   ![ASA-szkript kiválasztása a Visual Studio Code-ban](./media/quick-create-vs-code/asa-script.png)

4. Adja meg a bemeneti fájl nevét **IotHub**néven.

5. Szerkessze a **IoTHub. JSON** fájlt a következő értékekkel. Tartsa meg az alább nem említett mezők alapértelmezett értékeit. A Codelensben segítségével karakterláncot adhat meg, kiválaszthatja a legördülő listából, vagy megváltoztathatja a szöveget közvetlenül a fájlban.

   |Beállítás|Ajánlott érték|Leírás|
   |-------|---------------|-----------|
   |Name (Név)|Input (Bemenet)|Adja meg a feladat bemenetének azonosító nevét.|
   |IotHubNamespace|MyASAIoTHub|Válassza ki vagy adja meg a IoT Hub nevét. A rendszer automatikusan észleli a IoT Hub neveket, ha azok ugyanabban az előfizetésben jönnek létre.|
   |SharedAccessPolicyName|iothubowner| |

   ![A bemenet konfigurálása a Visual Studio Code-ban](./media/quick-create-vs-code/configure-input.png)



## <a name="define-an-output"></a>Kimenet definiálása

1. A parancs paletta megnyitásához kattintson a **CTRL + SHIFT + P** billentyűkombinációra. Ezután írja be a **ASA: Adja hozzá a kimenetet @ no__t-0.

   ![Stream Analytics kimenet hozzáadása a VS Code-ban](./media/quick-create-vs-code/add-output.png)

2. A fogadó típushoz válassza a **blob Storage** lehetőséget.

3. Válassza ki azt az ASA-lekérdezési parancsfájlt, amely ezt a bemenetet fogja használni.

4. Adja meg a kimeneti fájl nevét **BlobStorage**néven.

5. Szerkessze a **BlobStorage** a következő értékekkel. Tartsa meg az alább nem említett mezők alapértelmezett értékeit. A Codelensben segítségével kiválaszthatja a legördülő listából, vagy megadhat egy karakterláncot. 

   |Beállítás|Ajánlott érték|Leírás|
   |-------|---------------|-----------|
   |Name (Név)|Output| Adjon meg egy nevet a feladatok kimenetének azonosításához.|
   |Tárfiók|asaquickstartstorage|Válassza ki vagy adja meg a Storage-fiók nevét. Ha a tárfiókok ugyanahhoz az előfizetéshez tartoznak, a rendszer automatikusan észleli a nevüket.|
   |Tároló|container1|Válassza ki a tárfiókjában létrehozott meglévő tárolót.|
   |Path Pattern (Elérésiút-minta)|kimenet|Adja meg a tárolón belül létrehozni kívánt elérési út nevét.|

 ![Kimenet konfigurálása a Visual Studio Code-ban](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>A szkript fordítása

A szkriptek fordítása két dolgot tesz: a szintaxis ellenõrzése és a Azure Resource Manager-sablonok létrehozása az automatikusan üzembe helyezéshez.

A szkriptek fordítását kétféleképpen lehet elindítani:

1. Válassza ki a munkaterületen a parancsfájlt, majd aktiválja a fordítást a parancs palettáján. 

   ![A VS Code parancs-paletta használata a szkript fordításához](./media/quick-create-vs-code/compile-script1.png)

2. Kattintson a jobb gombbal a parancsfájlra, és válassza az **ASA: fordítás szkriptet**.

    ![Kattintson a jobb gombbal az ASA-szkriptre a fordításhoz](./media/quick-create-vs-code/compile-script2.png)

3. A fordítást követően a projekt **üzembe helyezési** mappájában található két létrehozott Azure Resource Manager-sablon. A rendszer ezt a két fájlt használja az üzembe helyezéshez.

    ![Központi telepítési sablonok Stream Analytics a Fájlkezelőben](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Stream Analytics-feladatok elküldése az Azure-ba

1. A Visual Studio Code parancsfájl-szerkesztő ablakában válassza az **előfizetések**közül a kiválasztás lehetőséget.

   ![Válassza ki az előfizetések szövegét a parancsfájl-szerkesztőben](./media/quick-create-vs-code/select-subscription.png)

2. Válassza ki az előfizetést az előugró listából.

3. Válasszon ki egy feladatot * *. Ezután válassza az új feladatok létrehozása lehetőséget.

4. Adja meg a feladat nevét, a **myASAjob** , majd kövesse az utasításokat az erőforráscsoport és a hely kiválasztásához.

5. Válassza **az elküldés az Azure-** ba lehetőséget. A naplók a kimenet ablakban találhatók. 

6. A feladatok létrehozásakor a **stream Analytics Explorerben**látható.

![Feladatok listázása Stream Analytics Explorerben](./media/quick-create-vs-code/list-job.png)


## <a name="run-the-iot-simulator"></a>A IoT Simulator futtatása

1. Nyissa meg a [málna PI Azure IoT online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) lapot egy új böngészőablakban vagy ablakban.

2. A 15. sorban található helyőrzőt cserélje le az Azure IoT Hub Device-kapcsolatok karakterláncára, amelyet egy korábbi szakaszban mentett.

3. Kattintson a **Run** (Futtatás) parancsra. A kimenetnek meg kell jelenítenie az érzékelő adatait és a IoT Hub küldött üzeneteket.

   ![Málna PI Azure IoT online szimulátor](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

1. Nyissa meg **stream Analytics Explorert** a Visual Studio Code-ban, és keresse meg a **myASAJob**feladatot.

2. Kattintson a jobb gombbal a feladatokra. Ezután válassza a **Start** lehetőséget a helyi menüben.

   ![Stream Analytics-feladatok elindítása a VS Code-ban](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. A felugró ablakban válassza a **most** lehetőséget a feladatok elindításához.

4. Figyelje meg, hogy a feladatok állapota **fut**értékre változott. Kattintson a jobb gombbal a feladatra, majd válassza a **feladatok nézet megnyitása a portálon** lehetőséget a bemeneti és a kimeneti esemény metrikáinak megtekintéséhez. Ez a művelet néhány percet is igénybe vehet.

5. Az eredmények megtekintéséhez nyissa meg a blob Storage-t a Visual Studio Code-bővítményben vagy a Azure Portal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az útmutatóban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** menüpontot, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport lapján válassza a **Törlés** elemet, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics feladatot telepített a Visual Studio Code használatával. Stream Analytics feladatokat a [Azure Portal](stream-analytics-quick-create-portal.md), a [PowerShell](stream-analytics-quick-create-powershell.md)és a Visual Studio (stream-Analytics-Quick-Create-vs.MD) használatával is üzembe helyezhet. 

A Visual studióhoz készült Azure Stream Analytics Tools megismeréséhez folytassa a következő cikkel:

> [!div class="nextstepaction"]
> [A Visual Studio használata az Azure Stream Analytics-feladatok megtekintése](stream-analytics-vs-tools.md)
