---
title: Az Azure IoT Hub üzenetútválasztási eredményeinek megtekintése (.NET) | Microsoft dokumentumok
description: Miután az oktatóanyag 1.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bfee4e64070e5f37eaa3d63280409f00c0ed8672
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890394"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Oktatóanyag: 2. rész - Az irányított üzenetek megtekintése

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Az üzenetek továbbítására vonatkozó szabályok

Ezek az üzenet-útválasztás szabályai; ezek hoztak létre az 1.

|Érték |Eredmény|
|------|------|
|level="storage" |Írás az Azure Storage-ba.|
|level="critical" |Írás a Service Bus-üzenetsorba. Egy logikai alkalmazás lekéri az üzenetet a sorból, és az Office 365 használatával e-mailben küldi el az üzenetet.|
|alapértelmezett |Jelenítse meg az adatokat a Power BI használatával.|

Most hozza létre az erőforrásokat, amelyekhez az üzenetek et továbbítják, futtasson egy alkalmazást, hogy üzeneteket küldjön a központnak, és nézze meg az útválasztást működés közben.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása  

A Service Bus-üzenetsor a kritikusként megjelölt üzenetek fogadására használható. Állítson be egy logikai alkalmazást a Service Bus-üzenetsor monitorozására, illetve arra, hogy e-mailt küldjön, amikor a rendszer hozzáad egy üzenetet az üzenetsorhoz.

1. Az [Azure Portalon](https://portal.azure.com)válassza a **+ Erőforrás létrehozása**lehetőséget. A keresőmezőbe írja a **logikai alkalmazás** kifejezést, és nyomja le az Enter billentyűt. A megjelenített keresési eredmények között válassza a Logikai alkalmazás lehetőséget, majd a **Létrehozás** lehetőséget a **Logikai alkalmazás létrehozása** ablaktáblához való folytatáshoz. Töltse ki a mezőket.

   **Név**: Ez a mező a logikai alkalmazás neve. Ez az oktatóanyag a **ContosoLogicApp** nevet használja.

   **Előfizetés**: Válassza ki az Azure-előfizetést.

   **Erőforráscsoport**: Válassza **a Meglévő használata lehetőséget,** és válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

   **Hely**: Használja a saját helyzetét. Ez az oktatóanyag az **USA nyugati régióját** használja.

   **Log Analytics**: Ezt a kapcsolót ki kell kapcsolni.

   ![A Logikai alkalmazás létrehozása képernyő](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Kattintson a **Létrehozás** gombra. Az alkalmazás üzembe helyezése eltarthat néhány percig.

2. Lépjen a logikai alkalmazáshoz. A logic app legegyszerűbb módja az **Erőforráscsoportok**kiválasztása , válassza ki az erőforráscsoportot (ez az oktatóanyag **ContosoResources-t**használ), majd válassza ki a Logikai alkalmazást az erőforrások listájából. 

    Megjelenik a Logic Apps Designer lap (előfordulhat, hogy jobbra kell görgetnie, hogy megjelenjen a teljes lap). A Logic Apps Designer lapon görgessen lefelé, amíg meg nem jelenik a **Üres logikai alkalmazás +** csempéje, és jelölje ki. Az alapértelmezett lap a "For You". Ha ez az ablaktábla üres, válassza az **Összes** lehetőséget az összes elérhető összekötő és eseményindító megtekintéséhez.

3. Válassza ki a **Service Bus** elemet az összekötők listájából.

   ![Az összekötők listája](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Megjelenik az eseményindítók listája. Jelölje be **az Üzenet érkezésekor jelölőnégyzetet (automatikus kiegészítés) / Service Bus**.

   ![A Service Bus eseményindítóinak listája](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. A következő képernyőn töltse ki a Kapcsolat neve mezőt. Ez az oktatóanyag a **ContosoConnection** nevet használja.

   ![A Szolgáltatásbusz-várólista kapcsolatának beállítása](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Válassza ki a Service Bus névteret. Ez az oktatóanyag a **ContosoSBNamespace** névteret használja. A névtér kiválasztásakor a Portal lekérdezi a Service Bus-névteret a kulcsok lekéréséhez. Válassza **a RootManageSharedAccessKey,** majd **a Create (Létrehozás) lehetőséget.**

   ![A kapcsolat beállításának befejezése](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. A következő képernyőn a legördülő listából válassza ki az üzenetsor nevét (ebben az oktatóanyagban ez **contososbqueue**). A többi mező esetében használhatja az alapértelmezett értékeket.

   ![A várólista beállításai](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Állítsa be a műveletet, hogy e-mailt küldjön, amikor egy üzenet érkezik az üzenetsorba. A Logic Apps Designer, válassza **a + Új lépés** egy lépés hozzáadásához, majd válassza az **Összes** az összes rendelkezésre álló lehetőségek megtekintéséhez. A **Művelet kiválasztása** ablaktáblában keresse meg és válassza az **Office 365 Outlook**lehetőséget. A Műveletek képernyőn válassza **az E-mail küldése / Office 365 Outlook**lehetőséget.  

   ![Az Office365 beállításai](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Jelentkezzen be Office 365-fiókjába a kapcsolat beállításához. Ha ez az idő ki, csak próbáld újra. Adja meg az e-mailek címzettjeinek e-mail-címeit. Adja meg a tárgyat is, és írja be a szövegtörzsben megjeleníteni kívánt üzenetet. Teszteléshez adja meg a saját e-mail-címét címzettként.

   Válassza **a Dinamikus tartalom hozzáadása** lehetőséget a felvehető üzenet tartalmának megjelenítéséhez. Válassza a **Tartalom** lehetőséget, amellyel belefoglalhatja az üzenetet az e-mailbe.

   ![A logikai alkalmazás e-mail beállításai](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Kattintson a **Mentés** gombra. Ezután zárja be a Logic App Designert.

## <a name="set-up-azure-stream-analytics"></a>Az Azure Stream Analytics beállítása

A Power BI-vizualizáció adatainak megtekintéséhez először állítson be egy Stream Analytics-feladatot az adatok lekérésére. Ne feledje, hogy a rendszer csak azokat az üzeneteket küldi az alapértelmezett végpontra, amelyek **level** tulajdonsága **normal** értékű, és a Stream Analytics-feladat csak ezeket kéri le a Power BI-vizualizációhoz.

### <a name="create-the-stream-analytics-job"></a>A Stream Analytics-feladat létrehozása

1. Az [Azure Portalon](https://portal.azure.com)válassza az > **Erőforrás-internet-adatfolyam-elemzési** > **feladat** **létrehozása**lehetőséget.

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie. Ez az oktatóanyag a **contosoJob** nevet használja.

   **Előfizetés**: Az oktatóanyaghoz használt Azure-előfizetés.

   **Erőforráscsoport**: Használja az IoT Hubja által használt erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

   **Hely**: Használja a beállítási szkriptnél is használt helyet. Ez az oktatóanyag az **USA nyugati régióját** használja.

   ![Az adatfolyam-elemzési feladat létrehozása](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. A feladat létrehozásához válassza a **Létrehozás** gombot. A telepítés eltarthat néhány percig.

    A feladathoz való visszakereséshez válassza az **Erőforráscsoportok**lehetőséget. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. Jelölje ki az erőforráscsoportot, majd válassza ki a Stream Analytics-feladatot az erőforrások listájában.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia csoportban**válassza **a Bemenetek**lehetőséget.

2. A **Bemenetek** ablaktáblán válassza az **Adatfolyam-bevitel hozzáadása** és az IoT-központ lehetőséget. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Bemeneti áljel**: Ez az oktatóanyag a **contosoinputs** áljelet használja.

   **Válassza az IoT Hub ot az előfizetésből:** Válassza ezt a választógombot.

   **Előfizetés**: Válassza ki az oktatóanyaghoz használt Azure-előfizetést.

   **IoT Hub:** Válassza ki az IoT hub. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja.

   **Végpont**: Válassza az **Üzenetkezelés** lehetőséget. (Ha a Műveletek monitorozása lehetőséget választja, akkor az IoT Hub telemetriaadatait kapja meg, nem pedig az átküldött adatokat.) 

   **Megosztott hozzáférésű házirend neve**: Válassza a **szolgáltatást.** A Portal automatikusan kitölti a megosztott elérési házirend kulcsát.

   **Fogyasztói csoport**: Válassza ki az oktatóanyag 1. Ez az oktatóanyag a **contosoconsumers** csoportot használja.
   
   A többi mezőnél fogadja el az alapértelmezett beállításokat. 

   ![Az adatfolyam-elemzési feladat bemeneteinek beállítása](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia**csoportban válassza **a Kimenetek**lehetőséget.

2. A **Kimenetek** ablaktáblán válassza a **Hozzáadás**lehetőséget, majd a **Power BI**lehetőséget. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Kimeneti áljel**: A kimenet egyedi áljele. Ez az oktatóanyag a **contosooutputs** áljelet használja. 

   **Adatkészlet neve**: A Power BI-ban használni kívánt adatkészletnév. Ez az oktatóanyag a **contosodataset** nevet használja. 

   **Tábla neve**: A Power BI-ban használni kívánt táblanév. Ez az oktatóanyag a **contosotable** nevet használja.

   A többi mezőnél fogadja el az alapértelmezett beállításokat.

3. Válassza **az Engedélyezés**lehetőséget, és jelentkezzen be a Power BI-fiókjába. (Ez több próbálkozást is igénybe vehet).

   ![A kimenetek beállítása az adatfolyam-elemzési feladathoz](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen válassza a **Lekérdezés** lehetőséget.

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére. Ez az oktatóanyag a **contosoinputs** bemeneti áljelet használja.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére. Ez az oktatóanyag a **contosooutputs** áljelet használja.

   ![A lekérdezés beállítása az adatfolyam-elemzési feladathoz](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Kattintson a **Mentés** gombra.

5. Zárja be a Lekérdezés panelt. Visszatér az erőforráscsoport erőforrásainak nézetéhez. Válassza ki a Stream Analytics feladatot. Ebben az oktatóanyagban a neve: **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics-feladatban válassza a Start Now Start **(Indítsa** > **el)** > **lehetőséget.** Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

A Power BI-jelentés beállításához adatokra van szükség, ezért a Power BI-t az eszköz létrehozása és az eszközszimulációs alkalmazás futtatása után fogja beállítani.

## <a name="run-simulated-device-app"></a>Szimulált eszközalkalmazás futtatása

Az oktatóanyag 1. Ebben a szakaszban letölti a .NET konzolalkalmazást, amely szimulálja, hogy az eszköz küld eszköz-felhő üzeneteket egy IoT hub (feltételezve, hogy még nem töltötte le az alkalmazást és az erőforrásokat az 1. részben).

Ez az alkalmazás üzeneteket küld az egyes különböző üzenet-útválasztási módszerek. Van is egy mappa a letöltés, amely tartalmazza a teljes Azure Resource Manager-sablon és paraméterek fájl, valamint az Azure CLI és a PowerShell-parancsfájlok.

Ha nem töltötte le a fájlokat a tárházból az oktatóanyag [1.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) A hivatkozás kiválasztása letölti a több alkalmazással rendelkező adattárat; A keresett megoldás az iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Kattintson duplán a megoldásfájlra (IoT_SimulatedDevice.sln) a kód Visual Studio-ban való megnyitásához, majd nyissa meg a Program.cs. Az `{your hub name}` értéket cserélje le az IoT Hub gazdagépnevére. Az IoT Hub gazdagépnevének formátuma: **{iot-hub-name}.azure-devices.net**. Ebben az oktatóanyagban a központ gazdagépneve: **ContosoTestHub.azure-devices.net**. Ezután a `{your device key}` értéket cserélje le az eszközkulcsra, amelyet korábban, a szimulált eszköz beállítása során mentett. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Futtatás és tesztelés

Futtassa a konzolalkalmazást. Várjon néhány percet. Az alkalmazás konzolképernyőjén láthatja az elküldött üzeneteket.

Az alkalmazás másodpercenként elküld egy új üzenetet az eszközről a felhőbe az IoT Hubnak. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával, a hőmérséklettel, a páratartalommal és az üzenet szintjével, amely alapértelmezés szerint `normal`. Az alkalmazás véletlenszerűen rendeli hozzá a `critical` vagy a `storage` szintet, így az üzenet a tárfiókhoz vagy a Service Bus-üzenetsorhoz lesz irányítva (a logikai alkalmazás pedig ennek hatására egy e-mailt küld). Az alapértelmezett olvasás (`normal`) megjelenik a BI-jelentésben, amelyet a következő lépésben fog beállítani.

Ha minden helyesen lett beállítva, a következő eredményeket fogja látni:

1. E-maileket kap a kritikus üzenetekről.

   ![Az így kapott e-mailek](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Ez az eredmény azt jelenti, hogy a következő utasítások igazak. 

   * A Service Bus-üzenetsorra történő irányítás megfelelően működik.
   * Az üzenetet a Service Bus-üzenetsorból lekérő logikai alkalmazás megfelelően működik.
   * A logikai alkalmazás Outlook-összekötője megfelelően működik. 

2. Az [Azure Portalon](https://portal.azure.com)válassza **az Erőforráscsoportok** lehetőséget, és válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. 

    Jelölje ki a tárfiókot, válassza **a Tárolók**lehetőséget, majd a Tárolót. Ez az oktatóanyag a **contosoresults** tárolót használja. Megjelenik egy mappa, amelyben lefúrhat a könyvtárakba, amíg egy vagy több fájlt nem lát. Nyissa meg az egyik fájlt. A fájlok a tárfiókhoz irányított bejegyzéseket tartalmazzák. 

   ![Az eredményfájlok a tárolóban](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Ez az eredmény azt jelenti, hogy a következő utasítás igaz.

   * A tárfiókhoz történő útválasztás megfelelően működik.

Most, hogy az alkalmazás még fut, állítsa be a Power BI-vizualizációt az alapértelmezett útválasztáson keresztül érkező üzenetek megtekintéséhez.

## <a name="set-up-the-power-bi-visualizations"></a>A Power BI-vizualizációk beállítása

1. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába.

2. Lépjen a **Munkaterületek** részhez, és válassza ki azt a munkaterületet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor állított be. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. 

3. Válassza **az Adatkészletek**lehetőséget . Ha nem rendelkezik adatkészletekkel, várjon néhány percet, és ellenőrizze újra.

   Megjelenik az adatkészlet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor adott meg. Ez az oktatóanyag a **contosodataset** nevet használja. (Akár 5-10 percet is igénybe vehet, mire az adatkészlet először megjelenik.)

4. A **MŰVELETEK csoportban**válassza ki az első ikont a jelentés létrehozásához.

   ![Power BI-munkaterület kiemelt Műveletek és jelentésikonnal](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   * A jelentés létrehozása lapon vegyen fel egy vonaldiagramot a vonaldiagram ikonjának kiválasztásával.

     ![A vizualizációk és mezők](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát. Ez az oktatóanyag a **contosotable** nevet használja.

   * Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.

   * Húzza a **hőmérséklet** elemet az **Értékek** helyre.

   Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

6. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. A második diagram beállításához kövesse ugyanezt a folyamatot az első diagramnál, és helyezze az **EventEnqueuedUtcTime-ot** az x tengelyre (**tengely**) és a **páratartalmat** az y tengelyre (**Értékek**).

   ![A Power BI végleges jelentése a két diagrammal](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. A **Mentés gombra** a jelentés mentéséhez válassza a nevet, ha a program kéri.

Az adatoknak mindkét diagramon meg kell jelenniük. Ez az eredmény azt jelenti, hogy a következő állítások igazak:

   * Az alapértelmezett végpontra történő irányítás megfelelően működik.
   * Az Azure Stream Analytics-feladat streamelése megfelelően működik.
   * A Power BI-vizualizáció megfelelően van beállítva.

A power bi ablak tetején található Frissítés gombbal frissítheti a diagramokat, hogy megtekinthesse a legfrissebb adatokat. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha el szeretné távolítani az összes Azure-erőforrást, amelyet az oktatóanyag mindkét részén keresztül létrehozott, törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT Hubot, a Service Bus-névteret és -üzenetsort, a logikai alkalmazást, a tárfiókot és magát az erőforráscsoportot is. Eltávolíthatja a Power BI-erőforrásokat is, és törölheti az oktatóanyag során küldött e-maileket.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Erőforrások eltávolítása a Power BI-vizualizációban

Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába. Lépjen a munkaterülethez. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. A Power BI-vizualizáció eltávolításához nyissa meg a DataSets elemet, és jelölje ki a kukáskont az adatkészlet törléséhez. Ez az oktatóanyag a **contosodataset** nevet használja. Az adatkészlet eltávolításakor a jelentés is törlődik.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Az Azure CLI használatával megtisztítja az erőforrásokat

Az erőforráscsoport az [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítható el. `$resourceGroup`az oktatóanyag elején **contosoResources** lesz beállítva.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Erőforrások karbantartása a PowerShell használatával

Az erőforráscsoport eltávolításához használja az [Eltávolítás-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) parancsot. `$resourceGroup`az oktatóanyag elején **contosoResources** lesz beállítva.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Teszte-mailek karbantartása

Előfordulhat, hogy törölni szeretné a beérkezett üzenetek között a Logic App on keresztül az eszközalkalmazás futása közben létrehozott e-mailek mennyiségét is.

## <a name="next-steps"></a>További lépések

Ebben a két részes oktatóanyagban megtanulta, hogyan használhatja az üzenet-útválasztást az IoT Hub-üzenetek különböző célhelyekre történő továbbításához a következő feladatok végrehajtásával.  

**I. rész: Erőforrások létrehozása, üzenet-útválasztás beállítása**
> [!div class="checklist"]
> * Hozza létre az erőforrásokat – egy IoT hubot, egy tárfiókot, egy Service Bus-várólistát és egy szimulált eszközt.
> * Konfigurálja a végpontokat és az üzenetútvonalakat az IoT Hubban a tárfiókhoz és a Service Bus-várólistához.

**II. rész: Üzenetek küldése a központba, útválasztásos eredmények megtekintése**
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
> [Metrikák és diagnosztika beállítása és használata Az IoT Hub segítségével](tutorial-use-metrics-and-diags.md)
