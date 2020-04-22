---
title: Metrikák és diagnosztikai naplók beállítása és használata Azure IoT-központtal
description: Ismerje meg, hogyan állíthat be és használhat metrikákat és diagnosztikai naplókat egy Azure IoT-központtal. Ez adatokat biztosít az elemzéshez, hogy segítsen diagnosztizálni a hub problémáit.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 3eda4cd8dc10bd9128186b2ff4f8d6ac0254fe5d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770597"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Oktatóanyag: Metrikák és diagnosztikai naplók beállítása és használata IoT-központtal

Ha éles környezetben futó IoT Hub-megoldással rendelkezik, szeretne beállítani néhány metrikát, és engedélyezni e diagnosztikai naplókat. Ezután, ha probléma merül fel, olyan adatokat kell megvizsgálnia, amelyek segítenek a probléma diagnosztizálásában és gyorsabb megoldásában. Ebben a cikkben megtudhatja, hogyan engedélyezheti a diagnosztikai naplókat, és hogyan ellenőrizheti őket a hibákat. Emellett néhány mérőszámot is beállít, és riasztást ad, amely akkor jelenik meg, amikor a metrikák elérik a bizonyos határt. Például lehet, hogy egy e-mailt küldött Önnek, ha a telemetriai üzenetek száma meghaladja egy adott határ, vagy ha a felhasznált üzenetek száma megközelíti az IoT Hub engedélyezett kvótát. 

Egy példa használati eset egy benzinkút, ahol a szivattyúk IoT-eszközök, amelyek egy IoT-központtal kommunikálnak. A hitelkártyák érvényesítése, és a végső tranzakció egy adattárba kerül. Ha az IoT-eszközök nem csatlakoznak a hubhoz, és üzeneteket küldenek, sokkal nehezebb kijavítani, ha nincs rálátása arra, hogy mi történik.

Ez az oktatóanyag az [IoT-központ útválasztása](tutorial-routing.md) az Azure-mintát használja az IoT hubüzenetek küldéséhez.

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure CLI használatával hozzon létre egy IoT-központot, egy szimulált eszközt és egy tárfiókot.  
> * Engedélyezze a diagnosztikai naplókat.
> * Metrikák engedélyezése.
> * Riasztások beállítása a metrikákhoz. 
> * Töltsön le és futtasson egy olyan alkalmazást, amely szimulálja az IoT-eszközt, amely üzeneteket küld a központnak. 
> * Futtassa az alkalmazást, amíg a riasztások el nem kezdenek tüzelni. 
> * Tekintse meg a metrikák eredményeit, és ellenőrizze a diagnosztikai naplók. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- A [Visual Studio](https://www.visualstudio.com/) telepítése. 

- E-mail fiók, amely képes e-mail fogadására.

- Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. Az oktatóanyagban szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Erőforrások beállítása

Ebben az oktatóanyagban egy IoT-hub, egy tárfiók és egy szimulált IoT-eszköz szükséges. Ezek az erőforrások létrehozhatók az Azure CLI vagy az Azure PowerShell használatával. Ugyanazt az erőforráscsoportot és helyet használja minden erőforráshoz. Így mindent egyetlen lépésben távolíthat el az erőforráscsoport törlésével.

Ezek a szükséges lépések.

1. Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/management/overview.md). 

2. Hozzon létre egy IoT hubot.

3. Hozzon létre egy standard szintű V1-tárfiókot Standard_LRS-replikációval.

4. Hozzon létre egy eszközidentitást ahhoz a szimulált eszközhöz, amely üzeneteket küld a központnak. Mentse a kulcsot a tesztelési fázishoz.

### <a name="set-up-resources-using-azure-cli"></a>Erőforrások beállítása az Azure CLI használatával

Másolja és illessze be az alábbi szkriptet a Cloud Shellbe. A szolgáltatás azt feltételezi, hogy már bejelentkezett, és soronként futtatja a szkriptet. Az új erőforrások a ContosoResources erőforráscsoportban jönnek létre.

Azokhoz a változókhoz, amelyeknek globálisan egyedinek kell lenniük, a következő van hozzáfűzve: `$RANDOM`. Ha a szkript fut, és a változók be vannak állítva, a rendszer létrehoz egy véletlenszerű numerikus sztringet, és hozzáfűzi a rögzített sztring végéhez, hogy a sztring egyedi legyen.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Az eszközidentitás létrehozásakor a következő hibaüzenet jelenhet meg: *Nem található kulcs az IoT Hub ContosoTestHub házirend-iothubowner-jéhez.* A hiba megoldásához frissítse az Azure CLI IoT-bővítményt, majd futtassa újra a parancsfájl utolsó két parancsát. 
>
>Itt van a parancs, hogy frissítse a kiterjesztést. Futtassa ezt a Cloud Shell-példányban.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="enable-the-diagnostic-logs"></a>A diagnosztikai naplók engedélyezése 

[A diagnosztikai naplók](../azure-monitor/platform/platform-logs-overview.md) alapértelmezés szerint le vannak tiltva, amikor új IoT-központot hoz létre. Ebben a szakaszban engedélyezze a diagnosztikai naplókat a hubhoz.

1. Először is, ha még nem a központban a portálon, kattintson az **Erőforráscsoportok** és kattintson az erőforráscsoport Contoso-Resources. Válassza ki a központot a megjelenített erőforrások listájából. 

2. Keresse meg a **figyelési** szakaszt az IoT Hub panelen. Kattintson a **Diagnosztikai beállítások** elemre. 

   ![Képernyőkép az IoT Hub panel diagnosztikai beállításokról készült részéről.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Győződjön meg arról, hogy az előfizetés és az erőforráscsoport helyes. Az **Erőforrástípusa**csoportban törölje a jelet az **Összes kijelölése**jelölőnégyzetből, majd keresse meg és ellenőrizze az **IoT Hub**ot. (Ismét az *Összes kijelölése* jelölőnégyzet melletti pipát helyezi, csak hagyja figyelmen kívül.) Az **Erőforrás csoportban**válassza ki a hub nevét. A képernyőnek így kell kinéznie: 

   ![Képernyőkép az IoT Hub panel diagnosztikai beállításokról készült részéről.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Most kattintson **a Diagnosztika bekapcsolása gombra.** Megjelenik a Diagnosztika beállításai ablaktábla. Adja meg a diagnosztikai naplók beállításainak nevét "diags-hub" néven.

5. Ellenőrizze **az Archívumot egy tárfiókba.** 

   ![Képernyőkép, amelyen a diagnosztika archiválása egy tárfiókba.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Kattintson a **Konfigurálás** gombra a **Tárfiók kiválasztása** képernyő megtekintéséhez, válassza ki a megfelelőt *(contosostoragemon*), majd kattintson az **OK** gombra a Diagnosztikai beállítások ablaktáblához való visszatéréshez. 

   ![Képernyőkép a diagnosztikai naplók tárfiókba archiválására beállításával.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. A **LOG csoportban**jelölje be **a Kapcsolatok** és az **eszköztelemetria jelölőnégyzetet,** és állítsa a **megőrzési (napok)** értékét egyenként 7 napra. A Diagnosztikai beállítások képernyőmost így kell kinéznie:

   ![Képernyőkép a diagnosztikai napló végső beállításairól.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Kattintson a **Mentés** gombra a beállítások mentéséhez. Zárja be a Diagnosztikai beállítások ablaktáblát.

Később, amikor megnézi a diagnosztikai naplókat, láthatja az eszköz csatlakoztatási és kapcsolatbontási naplózását. 

## <a name="set-up-metrics"></a>Mérőszámok beállítása 

Most állítsa be néhány metrikák figyelni, amikor üzeneteket küldenek a hubra. 

1. Az IoT hub beállítások ablaktáblájában kattintson a **Metrikák** lehetőségre a **Figyelés** szakaszban.

2. A képernyő tetején kattintson az **Utolsó 24 óra (Automatikus)** gombra. A megjelenő legördülő menüben válassza az **Elmúlt 4 óra** lehetőséget az **Időtartomány**ban, és állítsa az **Idő részletességét** **1 percre**helyi idő szerint. A beállítások mentéséhez kattintson az **Alkalmaz** gombra. 

   ![Képernyőkép a mérőszámok időbeállításairól.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Alapértelmezés szerint egy metrikabejegyzés van. Hagyja az erőforráscsoportot alapértelmezettként, és a metrika névteret. A **Metrika** legördülő listában válassza az **Elküldött Telemetriai üzenetek lehetőséget.** Az **összesítés** beállítása **Sum**.

   ![Képernyőkép, amelyen az elküldött telemetriai üzenetek metrikája látható.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Most kattintson **a Metrika hozzáadása** gombra, ha egy másik mérőszámot szeretne hozzáadni a diagramhoz. Válassza ki az erőforráscsoportot (**ContosoTestHub).** **A Metrika**csoportban válassza **a Használt üzenetek teljes száma**lehetőséget. Az **Összesítés**területen válassza **az Átlag**lehetőséget. 

   Most a képernyő megjeleníti az *elküldött telemetriai üzenetek*minimális metrikáját, valamint a használt üzenetek teljes számának új metrikáját. *Total number of messages used*

   ![Képernyőkép, amelyen az elküldött telemetriai üzenetek metrikája látható.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Kattintson **a Rögzítés az irányítópultra gombra.** Az Azure Portal irányítópultjára tűzi ki, így újra hozzáférhet. Ha nem tűzi ki az irányítópulton, a beállítások nem maradnak meg.

## <a name="set-up-alerts"></a>Riasztások beállítása

Nyissa meg a portál on hub. Kattintson **az Erőforráscsoportok**elemre, válassza *a ContosoResources*lehetőséget, majd válassza az IoT Hub *ContosoTestHub*lehetőséget. 

Az IoT Hub még nem lett áttelepítve az [Azure Monitor metrikákra;](/azure/azure-monitor/platform/data-collection#metrics) meg kell használni a [klasszikus riasztások](/azure/azure-monitor/platform/alerts-classic.overview).

1. A **Figyelés**csoportban kattintson a **Riasztások:** Ez a fő riasztási képernyő megjelenítése elemre. 

   ![Képernyőkép a klasszikus riasztások megkeresésének módjáról.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Ha innen szeretné tudni a klasszikus riasztásokat, kattintson a **Klasszikus riasztások megtekintése gombra.** 

    ![Képernyőkép a klasszikus riasztások képernyőjéről.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Töltse ki a mezőket: 

    **Előfizetés**: Hagyja ezt a mezőt az aktuális előfizetésére állítva.

    **Forrás**: Állítsa ezt a mezőt *a Metmutatók mezőre.*

    **Erőforráscsoport**: Állítsa be ezt a mezőt az aktuális erőforráscsoportra, *a ContosoResources parancsra.* 

    **Erőforrás típusa**: Állítsa be ezt a mezőt az IoT Hub. 

    **Erőforrás**: Válassza ki az IoT hubot, *contosoTestHub.*

3. Új riasztás beállításához kattintson **a Metrikariasztás hozzáadása (klasszikus)** gombra.

    Töltse ki a mezőket:

    **Név**: Adja meg a riasztási szabály nevét, például *a telemetriai üzeneteket.*

    **Leírás**: Adja meg a riasztás leírását, például *a riasztást, ha 1000 telemetriai üzenetet küld.* 

    **Forrás**: Állítsa ezt *a Mérőszámok beállításra.*

    **Az Előfizetés**, **Az Erőforrás csoport**és az **Erőforrás** értéket a Klasszikus **riasztások megtekintése** képernyőn kiválasztott értékekre kell beállítani. 

    Állítsa **a Metrikát** az *elküldött telemetriai üzenetekre.*

    ![Képernyőkép, amelyen klasszikus riasztás telemetriai üzenetekre vonatkozó beállítása látható.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. A diagram után állítsa be a következő mezőket:

   **Feltétel**: *Nagyobb, mint*.

   **Küszöbérték**: 1000-re van állítva.

   **Időszak**: Az *elmúlt 5 percben*van beállítva .

   **Értesítő e-mail címzettek**: Tegye ide az e-mail címét. 

   ![Képernyőkép a riasztások képernyő alsó felével.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   A riasztás mentéséhez kattintson az **OK** gombra. 

5. Most állítson be egy másik riasztást a *használt üzenetek teljes számára.* Ez a metrika akkor hasznos, ha riasztást szeretne küldeni, ha a használt üzenetek száma megközelíti az IoT hub kvótáját – tudatni a központ hamarosan elkezdi az üzenetek elutasítását.

   A **Klasszikus riasztások megtekintése** képernyőn kattintson a **Metrikariasztás hozzáadása (klasszikus)** elemre, majd töltse ki ezeket a mezőket a **Szabály hozzáadása** ablaktáblán.

   **Név**: Adja meg a riasztási szabály nevét, például *a használt üzenetek számát.*

   **Leírás**: Adja meg a riasztás leírását, például *a figyelmeztetést, amikor a kvóta közelébe kerül.*

   **Forrás**: Állítsa ezt a mezőt *a Metmutatók mezőre.*

    **Az Előfizetés**, **Az Erőforrás csoport**és az **Erőforrás** értéket a Klasszikus **riasztások megtekintése** képernyőn kiválasztott értékekre kell beállítani. 

    Állítsa **a Metrikát** *a felhasznált üzenetek teljes számára.*

6. A diagram alatt töltse ki a következő mezőket:

   **Feltétel**: *Nagyobb, mint*.

   **Küszöbérték**: 1000-re van állítva.

   **Időszak**: Állítsa ezt a mezőt *az elmúlt 5 percben*. 

   **Értesítő e-mail címzettek**: Tegye ide az e-mail címét. 

   A szabály mentéséhez kattintson az **OK** gombra. 

5. Most két riasztást kell látnia a klasszikus riasztások ablaktáblában: 

   ![Képernyőkép a klasszikus riasztások képernyőről az új riasztási szabályokkal.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Zárja be a riasztások ablaktáblát. 
    
    Ezekkel a beállításokkal értesítést kap, ha az elküldött üzenetek száma nagyobb, mint 400, és ha a felhasznált üzenetek száma meghaladja a SZÁMOT.

## <a name="run-simulated-device-app"></a>Szimulálteszköz-alkalmazás futtatása

Korábban, a szkript telepítési szakaszában beállított egy eszközt az IoT-eszköz használatának szimulálására. Ebben a szakaszban egy .NET-konzolalkalmazást tölt le, amely egy, az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál.  

Az [IoT-eszköz szimulációjára](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) szolgáló megoldás letöltése. Ez a hivatkozás letölti a tárhét több alkalmazás sal; a keresett megoldás az iot-hub/Tutorials/Routing/.

Kattintson duplán a megoldásfájlra (SimulatedDevice.sln), a kód megnyitásához a Visual Studióban, majd nyissa meg a Program.cs fájlt. Az `{iot hub hostname}` értéket cserélje le az IoT Hub gazdagépnevére. Az IoT Hub gazdagépnevének formátuma: **{iot-hub-name}.azure-devices.net**. Ebben az oktatóanyagban a központ gazdagépneve: **ContosoTestHub.azure-devices.net**. Ezután a `{device key}` értéket cserélje le az eszközkulcsra, amelyet korábban, a szimulált eszköz beállítása során mentett. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Futtatás és tesztelés 

A Program.cs módosítsa `Task.Delay` az 1000-ről 10-re, ami 1 másodpercről 0,01 másodpercre csökkenti az üzenetek küldése közötti időt. A késleltetés lerövidítése növeli az elküldött üzenetek számát.

```csharp
await Task.Delay(10);
```

Futtassa a konzolalkalmazást. Várjon néhány percet (10-15). Az alkalmazás konzolképernyőjén láthatja a szimulált eszközről a hubra küldött üzeneteket.

### <a name="see-the-metrics-in-the-portal"></a>Tekintse meg a mutatókat a portálon

Nyissa meg a mérőszámokat az irányítópulton. Módosítsa az időértékeket *utolsó 30 percre* 1 *perces*időrészletességgel . Az elküldött telemetriai üzeneteket és a diagramon használt üzenetek teljes számát jeleníti meg, a legutóbbi számok pedig a diagram alján.

   ![A mérőszámokat megjelenítő képernyőkép.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>A riasztások megtekintése

Menj vissza a riasztásokhoz. Kattintson **az Erőforráscsoportok**elemre, válassza a *ContosoResources*elemet, majd jelölje ki a *ContosoTestHub*központot. A hub tulajdonságai lapján válassza a **Riasztások**lehetőséget, majd **a Klasszikus riasztások megtekintése**lehetőséget. 

Ha az elküldött üzenetek száma meghaladja a korlátot, e-mail értesítéseket kap. Ha meg szeretné tudni, hogy vannak-e aktív riasztások, nyissa meg a központot, és válassza a **Riasztások**lehetőséget. Megmutatja az aktív riasztásokat, és ha vannak figyelmeztetések. 

   ![Képernyőkép a riasztások kilövését ábrázoló képernyőkép.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Kattintson a telemetriai üzenetekriasztásra. Ez mutatja a metrika eredménye és a diagram az eredményeket. Is, az e-mailt küldött, hogy figyelmeztesse Önt a riasztás tüzelési néz ki ez a kép:

   ![Képernyőkép a riasztásokat megjelenítő e-mailről.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>A diagnosztikai naplók megtekintése

Állítsa be a diagnosztikai naplók at blob storage exportálni. Nyissa meg az erőforráscsoportot, és válassza ki a tárfiók *contosostoragemon*lehetőséget. Válassza a Blobok lehetőséget, majd nyissa meg a *tárolóelemzési naplókat és a kapcsolatokat.* Részletezze, amíg el nem jut az aktuális dátumig, és ki nem választja a legutóbbi fájlt. 

   ![Képernyőkép a tárolótárolóba való lefúrásról a diagnosztikai naplók megtekintéséhez.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

A **Letöltés gombra** kattintva töltse le és nyissa meg. Láthatja az eszköz csatlakozásának és leválasztásának naplóit, miközben üzeneteket küld a hubnak. Itt egy minta:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Az oktatóanyagban létrehozott összes erőforrás eltávolításához törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT hubot, a tárfiókot és magát az erőforráscsoportot. Ha mérőszámokat rögzített az irányítópultra, akkor ezeket manuálisan kell eltávolítania, ha az egyes sarokban lévő három pontra kattint, és az Eltávolítás lehetőséget **választja.**

Az erőforráscsoport az [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítható el.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő feladatok végrehajtásával megtanulta a metrikák és diagnosztikai naplók használatát:

> [!div class="checklist"]
> * Az Azure CLI használatával hozzon létre egy IoT-központot, egy szimulált eszközt és egy tárfiókot.  
> * Engedélyezze a diagnosztikai naplókat. 
> * Metrikák engedélyezése.
> * Riasztások beállítása a metrikákhoz. 
> * Töltsön le és futtasson egy olyan alkalmazást, amely szimulálja az IoT-eszközt, amely üzeneteket küld a központnak. 
> * Futtassa az alkalmazást, amíg a riasztások el nem kezdenek tüzelni. 
> * Tekintse meg a metrikák eredményeit, és ellenőrizze a diagnosztikai naplók. 

A következő oktatóanyag az IoT-eszközök állapotának kezelését mutatja be. 

> [!div class="nextstepaction"]
> [Eszközök konfigurálása háttérszolgáltatásból](tutorial-device-twins.md)
