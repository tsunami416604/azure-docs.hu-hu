---
title: Azure IoT Hub (.NET) útvonalterveket üzenet megtekintése |} A Microsoft Docs
description: Azure IoT Hub üzenet-útválasztási eredmények megtekintése
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 7f6baec8fee2b046949e2d88b5fff7bb5db9b40b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662414"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Oktatóanyag: 2. rész – az irányított üzenet megtekintése

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

## <a name="rules-for-routing-the-messages"></a>Az üzenetek útválasztási szabályokat

Ezek a szabályok az Útválasztás üzenet; ezek is állítsa be az 1. rész a jelen oktatóanyag, és megjelenik a második rész működjenek.

|érték |Eredmény|
|------|------|
|level="storage" |Írás az Azure Storage-ba.|
|level="critical" |Írás a Service Bus-üzenetsorba. Egy logikai alkalmazás lekéri az üzenetet a sorból, és az Office 365 használatával e-mailben küldi el az üzenetet.|
|alapértelmezett |Jelenítse meg az adatokat a Power BI használatával.|

Most hozzon létre az erőforrások, amelyhez az üzenetek lesznek irányítva, futtathat egy alkalmazást, amely üzeneteket küldjön a hubnak, és tekintse meg az útválasztást a műveletet.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása  

A Service Bus-üzenetsor a kritikusként megjelölt üzenetek fogadására használható. Állítson be egy logikai alkalmazást a Service Bus-üzenetsor monitorozására, illetve arra, hogy e-mailt küldjön, amikor a rendszer hozzáad egy üzenetet az üzenetsorhoz.

1. Az a [az Azure portal](https://portal.azure.com)válassza **+ erőforrás létrehozása**. A keresőmezőbe írja a **logikai alkalmazás** kifejezést, és nyomja le az Enter billentyűt. A keresési eredmények között megjelenik válasszon logikai alkalmazást, majd **létrehozás** továbbra is a **logikai alkalmazás létrehozása** ablaktáblán. Töltse ki a mezőket.

   **Név**: Ebben a mezőben pedig a logikai alkalmazás neve. Ez az oktatóanyag a **ContosoLogicApp** nevet használja.

   **Előfizetés**: Válassza ki az Azure-előfizetését.

   **Erőforráscsoport**: Válassza ki **meglévő** , és jelölje ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

   **Hely**: A helyet használja. Ez az oktatóanyag az **USA nyugati régióját** használja.

   **Log Analytics**: A váltógomb ki kell kapcsolni.

   ![A logikai alkalmazás létrehozása képernyő](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Kattintson a **Létrehozás** gombra.

2. Lépjen a logikai alkalmazáshoz. A legegyszerűbben úgy juthat el a logikai alkalmazást, hogy válassza ki **erőforráscsoportok**, jelölje ki az erőforráscsoportot (ebben az oktatóanyagban **ContosoResources**), majd válassza ki a logikai alkalmazás az erőforrások listájából. Megjelenik a Logic Apps Designer lap (előfordulhat, hogy jobbra kell görgetnie, hogy megjelenjen a teljes lap). A Logic Apps Designerben oldalon görgessen lefelé, amíg meg nem látja a csempe, amely szerint **üres logikai alkalmazás +** , és jelölje ki. Az alapértelmezett lapon az "Az Ön". Ha ez az ablaktábla üres, válassza ki a **összes** , megjelenik az összes összekötő és eseményindítók érhető el.

3. Válassza ki **a Service Bus** összekötők listájából.

   ![Az összekötők listája](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Megjelenik az eseményindítók listája. Válassza ki **üzenet érkezésekor (automatikus lezárás) üzenetsor vagy Service Bus**.

   ![A Service Bus eseményindítók listájában](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. A következő képernyőn töltse ki a Kapcsolat neve mezőt. Ez az oktatóanyag a **ContosoConnection** nevet használja.

   ![A Service Bus-üzenetsorral létesített kapcsolat beállítása](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Válassza ki a Service Bus-névteret. Ez az oktatóanyag a **ContosoSBNamespace** névteret használja. A névtér kiválasztásakor a Portal lekérdezi a Service Bus-névteret a kulcsok lekéréséhez. Válassza ki **RootManageSharedAccessKey** válassza **létrehozás**.

   ![A kapcsolat beállításának befejezése](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. A következő képernyőn a legördülő listából válassza ki az üzenetsor nevét (ebben az oktatóanyagban ez **contososbqueue**). A többi mező esetében használhatja az alapértelmezett értékeket.

   ![Az üzenetsor-beállítások](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Állítsa be a műveletet, hogy e-mailt küldjön, amikor egy üzenet érkezik az üzenetsorba. Válassza ki a Logic Apps Designerben **+ új lépés** lépés hozzáadásához, majd válassza ki **összes** összes rendelkezésre álló beállítások megtekintéséhez. Az a **válasszon ki egy műveletet** ablaktáblán, keresse meg és válassza ki **Office 365 Outlook**. Az eseményindítók képernyőn válassza ki a **e-mail küldése és Office 365 Outlook**.  

   ![Az Office 365-beállítások](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Jelentkezzen be az Office 365-fiókjával a kapcsolat beállításához. Ha ez túllépi az időkorlátot, csak próbálja meg újra. Adja meg az e-mailek címzettjeinek e-mail-címeit. Adja meg a tárgyat is, és írja be a szövegtörzsben megjeleníteni kívánt üzenetet. Teszteléshez adja meg a saját e-mail-címét címzettként.

   Válassza ki **dinamikus tartalom hozzáadása** az üzenetet, amit belefoglalhat a tartalom megjelenítéséhez. Válassza a **Tartalom** lehetőséget, amellyel belefoglalhatja az üzenetet az e-mailbe.

   ![Az e-mail-beállításai a logikai alkalmazás](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Kattintson a **Mentés** gombra. Ezután zárja be a Logic App Designert.

## <a name="set-up-azure-stream-analytics"></a>Az Azure Stream Analytics beállítása

A Power BI-vizualizáció adatainak megtekintéséhez először állítson be egy Stream Analytics-feladatot az adatok lekérésére. Ne feledje, hogy a rendszer csak azokat az üzeneteket küldi az alapértelmezett végpontra, amelyek **level** tulajdonsága **normal** értékű, és a Stream Analytics-feladat csak ezeket kéri le a Power BI-vizualizációhoz.

### <a name="create-the-stream-analytics-job"></a>A Stream Analytics-feladat létrehozása

1. Az a [az Azure portal](https://portal.azure.com)válassza **erőforrás létrehozása** > **IOT-** > **Stream Analytics-feladat**.

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie. Ez az oktatóanyag a **contosoJob** nevet használja.

   **Előfizetés**: Az Azure-előfizetés-t az oktatóanyaghoz használja.

   **Erőforráscsoport**: Az IoT hub által használt ugyanazt az erőforráscsoportot használja. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

   **Hely**: A telepítési parancsprogram használja ugyanazt a helyet használja. Ez az oktatóanyag az **USA nyugati régióját** használja.

   ![A stream analytics-feladat létrehozása](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Válassza ki **létrehozás** létrehozni a feladatot. Válassza ki, hogy a feladat, **erőforráscsoportok**. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. Válassza ki az erőforráscsoportot, majd válassza ki a Stream Analytics-feladat az erőforrások listájában.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

4. A **Feladattopológia**válassza **bemenetek**.

5. Az a **bemenetek** ablaktáblán válassza **streambemenet hozzáadása** , és válassza ki az IoT Hub. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Bemeneti áljel**: Ez az oktatóanyag a **contosoinputs** bemeneti áljelet használja.

   **Válassza ki az IoT hubot az előfizetésből**: A választógomb gomb beállítás.

   **Előfizetés**: Válassza ki az Azure-előfizetés ehhez az oktatóanyaghoz használja.

   **Az IoT Hub**: Válassza ki az IoT hubot. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja.

   **Végpont**: Válassza ki **üzenetkezelési**. (Ha a Műveletek monitorozása lehetőséget választja, akkor az IoT Hub telemetriaadatait kapja meg, nem pedig az átküldött adatokat.) 

   **Megosztott elérési házirend neve**: Válassza ki **iothubowner**. A Portal automatikusan kitölti a megosztott elérési házirend kulcsát.

   **Fogyasztói csoport**: Válassza ki a fogyasztói csoportot, ez az oktatóanyag 1. lépésében beállítása. Ez az oktatóanyag a **contosoconsumers** csoportot használja.
   
   A többi mezőnél fogadja el az alapértelmezett beállításokat. 

   ![Állítsa be a stream analytics-feladat bemenetek](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

6. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia**válassza **kimenetek**.

2. Az a **kimenetek** ablaktáblán válassza **Hozzáadás**, majd válassza ki **Power BI**. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Kimeneti alias**: A kimeneti egyedi aliast. Ez az oktatóanyag a **contosooutputs** áljelet használja. 

   **Adatkészlet neve**: A Power BI-ban használandó adatkészlet nevét. Ez az oktatóanyag a **contosodataset** nevet használja. 

   **Táblanév**: A Power BI-ban használandó tábla neve. Ez az oktatóanyag a **contosotable** nevet használja.

   A többi mezőnél fogadja el az alapértelmezett beállításokat.

3. Válassza ki **engedélyezés**, és jelentkezzen be a Power BI-fiókjába. (Ez eltarthat több próbálkozás).

   ![A kimenetek a stream analytics-feladat beállítása](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen válassza a **Lekérdezés** lehetőséget.

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére. Ez az oktatóanyag a **contosoinputs** bemeneti áljelet használja.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére. Ez az oktatóanyag a **contosooutputs** áljelet használja.

   ![Állítsa be a lekérdezést a stream analytics-feladat](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Kattintson a **Mentés** gombra.

5. Zárja be a Lekérdezés panelt. Vissza a nézet az erőforrások az erőforráscsoportban. Válassza ki a Stream Analytics-feladat. Ebben az oktatóanyagban a neve: **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

Válassza ki a Stream Analytics-feladat **Start** > **most** > **Start**. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

A Power BI-jelentés beállításához adatokra van szükség, ezért a Power BI-t az eszköz létrehozása és az eszközszimulációs alkalmazás futtatása után fogja beállítani.

## <a name="run-simulated-device-app"></a>Szimulált eszközalkalmazás futtatása

Az 1. rész a jelen oktatóanyag, a beállít egy eszközt szimulálása az IoT-eszközt használ. Ebben a szakaszban, töltse le a .NET-konzolalkalmazást, amely szimulálja az eszköz – felhő üzeneteket küld az IoT hub eszköz feltéve, hogy az alkalmazást, és ez az oktatóanyag 1. rész az erőforrások már Ön nem töltődött le.

Ez az alkalmazás üzeneteket küld a különböző üzenet-útválasztási módszerek mindegyikéhez. A letöltés, amely tartalmazza a teljes Azure Resource Manager-sablon és a paramétereket tartalmazó fájlt, valamint az Azure CLI és PowerShell-parancsfájlok is van egy mappát.

Ha ez az oktatóanyag 1. lépésében a tárházból, nem töltődött le a fájlokat, lépjen tovább, és töltse le őket most a [IoT-eszköz szimulálása](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Egy adattár több alkalmazás található; az erre a hivatkozásra kattintva letölti. a keresett megoldást iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Kattintson duplán a megoldásfájlt a Visual studióban nyissa meg a kódot (IoT_SimulatedDevice.sln), majd nyissa meg a program.cs fájlban. Az `{iot hub hostname}` értéket cserélje le az IoT Hub gazdagépnevére. Az IoT Hub gazdagépnevének formátuma: **{iot-hub-name}.azure-devices.net**. Ebben az oktatóanyagban a központ gazdagépneve: **ContosoTestHub.azure-devices.net**. Ezután a `{device key}` értéket cserélje le az eszközkulcsra, amelyet korábban, a szimulált eszköz beállítása során mentett. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Futtatás és tesztelés

Futtassa a konzolalkalmazást. Várjon néhány percet. Az alkalmazás konzolképernyőjén láthatja az elküldött üzeneteket.

Az alkalmazás másodpercenként elküld egy új üzenetet az eszközről a felhőbe az IoT Hubnak. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával, a hőmérséklettel, a páratartalommal és az üzenet szintjével, amely alapértelmezés szerint `normal`. Az alkalmazás véletlenszerűen rendeli hozzá a `critical` vagy a `storage` szintet, így az üzenet a tárfiókhoz vagy a Service Bus-üzenetsorhoz lesz irányítva (a logikai alkalmazás pedig ennek hatására egy e-mailt küld). Az alapértelmezett olvasás (`normal`) megjelenik a BI-jelentésben, amelyet a következő lépésben fog beállítani.

Ha minden helyesen lett beállítva, a következő eredményeket fogja látni:

1. E-maileket kap a kritikus üzenetekről.

   ![Az eredményül kapott e-mailek](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Ez az eredmény azt jelenti, hogy teljesülnek az alábbi utasításokat. 

   * A Service Bus-üzenetsorra történő irányítás megfelelően működik.
   * Az üzenetet a Service Bus-üzenetsorból lekérő logikai alkalmazás megfelelően működik.
   * A logikai alkalmazás Outlook-összekötője megfelelően működik. 

2. Az a [az Azure portal](https://portal.azure.com)válassza **erőforráscsoportok** , és jelölje ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. Válassza ki a tárfiókot, jelölje be **Blobok**, majd válassza ki a tárolót. Ez az oktatóanyag a **contosoresults** tárolót használja. Megjelenik egy mappa, amelyben lefúrhat a könyvtárakba, amíg egy vagy több fájlt nem lát. Nyissa meg az egyik fájlt. A fájlok a tárfiókhoz irányított bejegyzéseket tartalmazzák. 

   ![Az eredményeket tartalmazó fájlokat a storage](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Ez az eredmény azt jelenti, hogy a következő utasítás nastavena nA hodnotu true.

   * A tárfiókhoz történő útválasztás megfelelően működik.

Most, hogy az alkalmazás fut, állítsa be a Power BI-vizualizációt az alapértelmezett útválasztáson keresztül érkező üzenetek megtekintéséhez.

## <a name="set-up-the-power-bi-visualizations"></a>A Power BI-vizualizációk beállítása

1. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába.

2. Lépjen a **Munkaterületek** részhez, és válassza ki azt a munkaterületet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor állított be. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. 

3. Válassza ki **adatkészletek**. Ha nem rendelkezik adatkészletekkel, várjon néhány percet, és ellenőrizze ismét.

   Megjelenik az adatkészlet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor adott meg. Ez az oktatóanyag a **contosodataset** nevet használja. (Akár 5-10 percet is igénybe vehet, mire az adatkészlet először megjelenik.)

4. A **műveletek**, válassza ki az első ikon a jelentés létrehozásához.

   ![A Power BI-munkaterület-műveletek és a jelentés ikonja kiemelve](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   * A jelentés létrehozásának oldalán adja hozzá a vonaldiagram válassza a vonaldiagram ikonját.

     ![A Vizualizációk és a mezők](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát. Ez az oktatóanyag a **contosotable** nevet használja.

   * Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.

   * Húzza a **hőmérséklet** elemet az **Értékek** helyre.

   Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

6. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. Második diagram beállításához kövesse a fenti lépéseket, és az **EventEnqueuedUtcTime** elemet helyezze az X tengelyre, a **páratartalom** értéket pedig az Y tengelyre.

   ![A két diagram a végső Power BI-jelentés](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Válassza ki **mentése** a jelentés mentéséhez.

Az adatoknak mindkét diagramon meg kell jelenniük. Ez az eredmény azt jelenti, hogy teljesülnek az alábbi utasításokat:

   * Az alapértelmezett végpontra történő irányítás megfelelően működik.
   * Az Azure Stream Analytics-feladat streamelése megfelelően működik.
   * A Power BI-vizualizáció megfelelően van beállítva.

A diagramok a Power BI-ablak felső részén a frissítés gomb kiválasztásával a legújabb adatok megjelenítéséhez is frissítheti. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha el kívánja távolítani keresztül mindkét részét ebben az oktatóanyagban létrehozott erőforrásokat, törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT Hubot, a Service Bus-névteret és -üzenetsort, a logikai alkalmazást, a tárfiókot és magát az erőforráscsoportot is. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Erőforrások eltávolítása a Power BI-vizualizációban

Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába. Lépjen a munkaterülethez. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. A Power BI-Vizualizációk eltávolításához nyissa meg az adatkészletek, és válassza a Kuka ikonra az adatkészlet törléséhez is. Ez az oktatóanyag a **contosodataset** nevet használja. Az adatkészlet eltávolításakor a jelentés is törlődik.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Az Azure CLI használatával az erőforrások törlése

Az erőforráscsoport az [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítható el. `$resourceGroup` úgy állították be **ContosoResources** vissza, ez az oktatóanyag elején.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Erőforrások törlése a PowerShell használatával

Az erőforráscsoport a [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal távolítható el. `$resourceGroup` úgy állították be **ContosoResources** vissza, ez az oktatóanyag elején.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

2. rész – ebben az oktatóanyagban megtudhatta, hogyan üzenet-útválasztása használatával irányíthatja az IoT Hub-üzenetek más célhelyekre való a következő feladatok végrehajtásával.  

**I. rész Erőforrások létrehozása, és beállíthatja az üzenet-útválasztása**
> [!div class="checklist"]
> * Az erőforrások – egy IoT hubot, tárfiók, Service Bus-üzenetsorba és szimulált eszköz létrehozása.
> * A végpontok és Üzenetútvonalak konfigurálása az IoT Hub a storage-fiók és a Service Bus-üzenetsorba.

**II. rész: Üzeneteket küldjön a hubnak, irányított eredmények megtekintése**
> [!div class="checklist"]
> * Egy logikai alkalmazás létrehozása, amely egy üzenet Service Bus-üzenetsorba való érkezésekor indul el és küld e-maileket.
> * Egy olyan alkalmazás letöltése és futtatása, amely a Hubhoz üzeneteket küldő IoT-eszközt szimulál a különböző útválasztási lehetőségekhez.
> * Power BI-vizualizáció létrehozása az alapértelmezett végpontra küldött adatokról.
> * Eredmények megtekintése...
> * ... a Service Bus-üzenetsorban és az e-mailekben.
> * ... a tárfiókban.
> * ... a Power BI-vizualizációban.

A következő oktatóanyag az IoT-eszközök állapotának kezelését mutatja be. 

> [!div class="nextstepaction"]
> [Állítsa be, és használja a metrikák és diagnosztikai IoT hub-](tutorial-use-metrics-and-diags.md)
