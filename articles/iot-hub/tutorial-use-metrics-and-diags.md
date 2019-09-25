---
title: Metrikák és diagnosztikai naplók beállítása és használata az Azure IoT hub használatával | Microsoft Docs
description: Metrikák és diagnosztikai naplók beállítása és használata Azure IoT hub használatával
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 7349287945a56bb7674e364f515d0b763015ed59
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262320"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Oktatóanyag: Metrikák és diagnosztikai naplók beállítása és használata IoT hub használatával

Ha éles környezetben futó IoT Hub-megoldással rendelkezik, állítson be néhány mérőszámot, és engedélyezze a diagnosztikai naplókat. Ha probléma merül fel, a megjelenő információkkal megtekintheti, hogy a probléma diagnosztizálása és gyorsabb javítása érdekében. Ebből a cikkből megtudhatja, hogyan engedélyezheti a diagnosztikai naplókat, és hogyan végezheti el a hibák ellenőrzését. Emellett néhány mérőszámot is beállíthat a figyeléshez, és riasztást küld, ha a mérőszámok egy bizonyos határt érintenek. Előfordulhat például, hogy egy e-mailt küld Önnek, ha az elküldött telemetria-üzenetek száma meghaladja az adott határt, vagy ha a felhasznált üzenetek száma a IoT Hub naponta engedélyezett üzenetek kvótája mellett van. 

Egy példa a használati esetre egy olyan benzinkútnál, ahol a szivattyúk olyan IoT, amelyek az IoT hub használatával kommunikálnak. A rendszer ellenőrzi a hitelkártyákat, és a végső tranzakciót egy adattárba írja. Ha a IoT-eszközök nem csatlakoznak a központhoz, és üzeneteket küldenek, sokkal nehezebb kijavítani, ha nem láthatók a mi folyik.

Ez az oktatóanyag az Azure-mintát használja az [IoT hub Routing](tutorial-routing.md) -ből, hogy üzeneteket küldjön az IoT hubhoz.

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure CLI használatával hozzon létre egy IoT hubot, egy szimulált eszközt és egy Storage-fiókot.  
> * Engedélyezze a diagnosztikai naplókat.
> * Metrikák engedélyezése
> * Riasztások beállítása ezekhez a metrikához. 
> * Töltsön le és futtasson egy alkalmazást, amely egy IoT-eszközt szimulál, amely üzeneteket küld az elosztónak. 
> * Futtassa az alkalmazást, amíg a riasztások elkezdenek tüzet. 
> * Tekintse meg a metrikák eredményeit, és tekintse meg a diagnosztikai naplókat. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

- A [Visual Studio](https://www.visualstudio.com/) telepítése. 

- E-mail fiók, amely képes e-mailek fogadására.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Erőforrások beállítása

Ebben az oktatóanyagban szüksége lesz egy IoT hub-ra, egy Storage-fiókra és egy szimulált IoT-eszközre. Ezek az erőforrások létrehozhatók az Azure CLI vagy az Azure PowerShell használatával. Ugyanazt az erőforráscsoportot és helyet használja minden erőforráshoz. Így mindent egyetlen lépésben távolíthat el az erőforráscsoport törlésével.

Ezek a szükséges lépések.

1. Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md). 

2. Hozzon létre egy IoT hubot.

3. Hozzon létre egy standard szintű V1-tárfiókot Standard_LRS-replikációval.

4. Hozzon létre egy eszközidentitást ahhoz a szimulált eszközhöz, amely üzeneteket küld a központnak. Mentse a kulcsot a tesztelési fázishoz.

### <a name="set-up-resources-using-azure-cli"></a>Erőforrások beállítása az Azure CLI-vel

Másolja és illessze be az alábbi szkriptet a Cloud Shellbe. A szolgáltatás azt feltételezi, hogy már bejelentkezett, és soronként futtatja a szkriptet. Az új erőforrások az erőforráscsoport ContosoResources jönnek létre.

Azokhoz a változókhoz, amelyeknek globálisan egyedinek kell lenniük, a következő van hozzáfűzve: `$RANDOM`. Ha a szkript fut, és a változók be vannak állítva, a rendszer létrehoz egy véletlenszerű numerikus sztringet, és hozzáfűzi a rögzített sztring végéhez, hogy a sztring egyedi legyen.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

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
>Az eszköz identitásának létrehozásakor a következő hibaüzenetet kaphatja: *Nem található kulcs a IoT hub ContosoTestHub házirend iothubowner*. A hiba elhárításához frissítse az Azure CLI IoT bővítményét, majd futtassa újra a parancsfájl utolsó két parancsát. 
>
>Itt látható a bővítmény frissítésére szolgáló parancs. Futtassa ezt a Cloud Shell-példányban.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése 

Új IoT hub létrehozásakor a [diagnosztikai naplók](../azure-monitor/platform/resource-logs-overview.md) alapértelmezés szerint le vannak tiltva. Ebben a szakaszban engedélyezheti a központ diagnosztikai naplóit.

1. Először is, ha a portálon még nem szerepel a központban, kattintson az **erőforráscsoportok** elemre, és kattintson az erőforráscsoport contoso – erőforrások elemre. Válassza ki a hubot a megjelenő erőforrások listájából. 

2. Keresse meg a **figyelés** szakaszt a IoT hub panelen. Kattintson a **Diagnosztikai beállítások** elemre. 

   ![A IoT Hub panel diagnosztikai beállítások részét ábrázoló képernyőkép.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Győződjön meg arról, hogy az előfizetés és az erőforráscsoport helyes. Az **Erőforrás típusa**területen törölje az **összes kijelölése elemet**, majd keresse meg és jelölje be a **IoT hub**. (A jelölőnégyzet *bejelölésével jelölje ki az összes lehetőséget* , csak hagyja figyelmen kívül.) Az **erőforrás**területen válassza ki a hub nevét. A képernyőnek a következő képhez hasonlóan kell kinéznie: 

   ![A IoT Hub panel diagnosztikai beállítások részét ábrázoló képernyőkép.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Most kattintson **a diagnosztika bekapcsolása**elemre. Megjelenik a diagnosztika beállításai panel. Adja meg a diagnosztikai naplók beállításait "diags-hub" néven.

5. Keresse **meg az archívumot egy Storage-fiókban**. 

   ![Képernyőfelvétel: a diagnosztika beállítása a Storage-fiókba való archiválásra.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Kattintson a **Konfigurálás** elemre a **Storage-fiók kiválasztása** képernyő megjelenítéséhez, válassza ki a megfelelőt (*contosostoragemon*), majd kattintson az **OK** gombra a diagnosztikai beállítások panelre való visszatéréshez. 

   ![Képernyőfelvétel: a diagnosztikai naplók beállítása a Storage-fiókba való archiválásra.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. A **napló**területen győződjön meg a **kapcsolatok** és az **eszközök telemetria**, és állítsa be az **adatmegőrzés (nap)** és a 7 nap értékeit. A diagnosztikai beállítások képernyőjének ekkor az alábbi képhez hasonlóan kell kinéznie:

   ![A diagnosztikai napló utolsó beállításait bemutató képernyőkép.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Kattintson a **Mentés** gombra a beállítások mentéséhez. A diagnosztikai beállítások panel bezárásához.

Később, amikor megtekinti a diagnosztikai naplókat, megtekintheti az eszköz csatlakoztatási és leválasztási naplózását. 

## <a name="set-up-metrics"></a>Mérőszámok beállítása 

Most állítson be néhány mérőszámot, amelyből megnézheti, hogy mikor küldik el az üzeneteket az adott hubhoz. 

1. Az IoT hub Beállítások paneljén kattintson a **figyelés** szakaszban található **mérőszámok** lehetőségre.

2. A képernyő felső részén kattintson az **elmúlt 24 óra (automatikus)** elemre. A megjelenő legördülő menüben válassza az **utolsó 4 óra** az időtartományra lehetőséget, és állítsa be az **időbeli részletességet** **1 percre**, helyi időre. A beállítások mentéséhez kattintson az **alkalmaz** gombra. 

   ![A metrikák időbeállítását bemutató képernyőkép.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Alapértelmezés szerint egy metrikai bejegyzés van. Hagyja meg az erőforráscsoportot alapértelmezettként, a metrika névterét. A **metrika** legördülő listában válassza ki az **elküldött telemetria-üzeneteket**. **Összesítés** beállítása a **Sum**értékre.

   ![Az elküldött telemetria-üzenetek metrikájának hozzáadását bemutató képernyőkép.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Most kattintson a **metrika hozzáadása** lehetőségre egy újabb metrika a diagramhoz való hozzáadásához. Válassza ki az erőforráscsoportot (**ContosoTestHub**). A **metrika**területen válassza ki **a felhasznált üzenetek teljes számát**. **Összesítéshez**válassza az **AVG**elemet. 

   Mostantól a képernyőn látható az *elküldött telemetria-üzenetek*kisméretű mérőszáma, valamint a *felhasznált üzenetek teljes számának*új mérőszáma.

   ![Az elküldött telemetria-üzenetek metrikájának hozzáadását bemutató képernyőkép.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Kattintson **a rögzítés az irányítópulton**elemre. A rendszer a Azure Portal irányítópultján rögzíti, hogy újra hozzá lehessen férni. Ha nem rögzíti az irányítópulton, a beállítások nem őrződnek meg.

## <a name="set-up-alerts"></a>Riasztások beállítása

Nyissa meg a hubot a portálon. Kattintson az **erőforráscsoportok**elemre, válassza a *ContosoResources*lehetőséget, majd válassza a IoT hub *ContosoTestHub*elemet. 

A IoT Hub még nem lett áttelepítve a [metrikák Azure monitorra](/azure/azure-monitor/platform/data-collection#metrics) ; [klasszikus riasztásokat](/azure/azure-monitor/platform/alerts-classic.overview)kell használnia.

1. A **figyelés**területen kattintson a riasztások elemre, amely a fő riasztási képernyőt jeleníti meg. 

   ![A klasszikus riasztások keresését bemutató képernyőkép.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. A klasszikus riasztások innen való beszerzéséhez kattintson a **klasszikus riasztások megtekintése**elemre. 

    ![Képernyőfelvétel a klasszikus riasztások képernyőjéről.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Töltse ki a mezőket: 

    **Előfizetés**: Hagyja meg ezt a mezőt a jelenlegi előfizetésében.

    **Forrás**: A mező értékekéntadja meg a metrikákat.

    **Erőforráscsoport**: Állítsa be ezt a mezőt az aktuális erőforráscsoporthoz, a *ContosoResources*. 

    **Erőforrás típusa**: A mező értéke legyen IoT Hub. 

    **Erőforrás**: Válassza ki az IoT hubot, a *ContosoTestHub*-t.

3. Új riasztás beállításához kattintson a **metrikus riasztás hozzáadása (klasszikus)** lehetőségre.

    Töltse ki a mezőket:

    **Név**: Adja meg a riasztási szabály nevét (például *telemetria-üzenetek*).

    **Leírás**: Adja meg a riasztás leírását, például a *riasztást, ha 1000 telemetria üzenet van elküldve*. 

    **Forrás**: Adja meg ezta metrikákat.

    Az előfizetést, az **erőforráscsoportot**és az **erőforrást** a **klasszikus riasztások megjelenítése** képernyőn kiválasztott értékekre kell beállítani. 

    **Metrika** beállítása a *telemetria küldött üzenetekhez*.

    ![Képernyőfelvétel: klasszikus riasztás beállítása a telemetria küldött üzenetekhez.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. A diagram után állítsa be a következő mezőket:

   **Feltétel**: Értéke *nagyobb, mint*.

   **Küszöbérték**: 1000 értékre van állítva.

   **Időszak**: Állítsa az *utolsó 5 percre*.

   **Értesítő e-mailek címzettjei**: Itt helyezheti el az e-mail címét. 

   ![Képernyőfelvétel a riasztások alsó felének megjelenítéséről](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   A riasztás mentéséhez kattintson **az OK** gombra. 

5. Most állítson be egy újabb riasztást a *felhasznált üzenetek teljes számára*vonatkozóan. Ez a metrika akkor lehet hasznos, ha riasztást szeretne küldeni, ha a felhasznált üzenetek száma megközelíti az IoT hub kvótáját – hogy tudd, hogy a központ hamarosan elindítja az üzenetek elutasítását.

   A **klasszikus riasztások megtekintése** képernyőn kattintson a **metrikus riasztás hozzáadása (klasszikus)** elemre, majd töltse ki ezeket a mezőket a **szabály hozzáadása** panelen.

   **Név**: Adja meg a riasztási szabály nevét, például az *üzenetek számát*.

   **Leírás**: Adja meg a riasztás leírását, például a *riasztást a kvóta lezárásakor*.

   **Forrás**: A mező értékekéntadja meg a metrikákat.

    Az előfizetést, az **erőforráscsoportot**és az **erőforrást** a **klasszikus riasztások megjelenítése** képernyőn kiválasztott értékekre kell beállítani. 

    A **metrika** beállítása a *felhasznált üzenetek teljes számára*.

6. A diagram alatt adja meg a következő mezőket:

   **Feltétel**: Értéke *nagyobb, mint*.

   **Küszöbérték**: 1000 értékre van állítva.

   **Időszak**: Adja meg a mezőt *az elmúlt 5 percben*. 

   **Értesítő e-mailek címzettjei**: Itt helyezheti el az e-mail címét. 

   A szabály mentéséhez kattintson **az OK** gombra. 

5. Ekkor két riasztás jelenik meg a klasszikus riasztások ablaktáblán: 

   ![Képernyőfelvétel: klasszikus riasztások képernyő, amely az új riasztási szabályokat tartalmazza.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. A riasztások panel bezárásához. 
    
    Ezekkel a beállításokkal riasztást kap, ha az elküldött üzenetek száma nagyobb, mint 400, és ha a felhasznált üzenetek teljes száma meghaladja a számot.

## <a name="run-simulated-device-app"></a>Szimulálteszköz-alkalmazás futtatása

Korábban, a szkript telepítési szakaszában beállított egy eszközt az IoT-eszköz használatának szimulálására. Ebben a szakaszban egy .NET-konzolalkalmazást tölt le, amely egy, az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál.  

Az [IoT-eszköz szimulációjára](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) szolgáló megoldás letöltése. Ez a hivatkozás egy tárházat tölt le több alkalmazással; a keresett megoldás az IOT-hub/oktatóanyagok/Routing/.

Kattintson duplán a megoldásfájlra (SimulatedDevice.sln), a kód megnyitásához a Visual Studióban, majd nyissa meg a Program.cs fájlt. Az `{iot hub hostname}` értéket cserélje le az IoT Hub gazdagépnevére. Az IoT Hub gazdagépnevének formátuma: **{iot-hub-name}.azure-devices.net**. Ebben az oktatóanyagban a központ gazdagépneve: **ContosoTestHub.azure-devices.net**. Ezután a `{device key}` értéket cserélje le az eszközkulcsra, amelyet korábban, a szimulált eszköz beállítása során mentett. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Futtatás és tesztelés 

A program.cs-ben módosítsa `Task.Delay` a 1000 – 10 értéket, amely csökkenti az üzenetek 1 másodpercről. 01 másodpercre való küldésének időtartamát. A késleltetés lerövidítése növeli az elküldött üzenetek számát.

```csharp
await Task.Delay(10);
```

Futtassa a konzolalkalmazást. Várjon néhány percet (10-15). A szimulált eszközről az alkalmazás konzol képernyőjén a központba küldött üzeneteket láthatja.

### <a name="see-the-metrics-in-the-portal"></a>A mérőszámok megtekintése a portálon

Nyissa meg a metrikákat az irányítópulton. Módosítsa az időértékeket az *elmúlt 30 percben* egy *1 perces*időrészletességgel. Megjeleníti a telemetria küldött üzeneteket, valamint a diagramon használt üzenetek teljes számát a diagram alján található legfrissebb számokkal.

   ![A metrikákat bemutató képernyőkép.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Riasztások megtekintése

Térjen vissza a riasztásokhoz. Kattintson az **erőforráscsoportok**elemre, válassza a *ContosoResources*lehetőséget, majd válassza ki a hub *ContosoTestHub*. A hub számára megjelenített tulajdonságok lapon válassza a **riasztások**, majd a **klasszikus riasztások megtekintése**lehetőséget. 

Ha az elküldött üzenetek száma meghaladja a korlátot, elindítja az értesítő e-maileket. Ha szeretné megtekinteni, hogy vannak-e aktív riasztások, nyissa meg a hubot, és válassza a **riasztások**lehetőséget. Ekkor megjelenik az aktív riasztások, és ha vannak figyelmeztetések. 

   ![A riasztásokat bemutató képernyőkép.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Kattintson a riasztásra a telemetria üzeneteihez. Megjeleníti a metrika eredményét és egy diagramot az eredményekkel. A riasztások égetését elküldő e-mail a következő képhez hasonlóan néz ki:

   ![A riasztásokat bemutató e-mail képernyőképe.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>A diagnosztikai naplók megtekintése

Be kell állítania a diagnosztikai naplókat a blob Storage-ba való exportáláshoz. Nyissa meg az erőforráscsoportot, és válassza ki a Storage-fiók *contosostoragemon*. Válassza a Blobok lehetőséget, majd nyissa meg a tároló-elemzések *-naplók-kapcsolatok*elemet. Nyomja le az aktuális dátumot, és válassza ki a legfrissebb fájlt. 

   ![Képernyőkép a tárolók lefúrásáról a diagnosztikai naplók megtekintéséhez.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Kattintson a **Letöltés** gombra a letöltéshez és a megnyitásához. Ekkor megjelennek az eszközhöz csatlakozó és leválasztott naplófájlok, ahogy üzeneteket küldenek a hubhoz. Példa:

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

Az oktatóanyagban létrehozott összes erőforrás eltávolításához törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT hubot, a Storage-fiókot és magát az erőforráscsoportot. Ha az irányítópulthoz rögzített mérőszámok vannak, akkor azokat manuálisan kell eltávolítania a jobb felső sarokban lévő három pontra, majd az **Eltávolítás**lehetőségre kattintva.

Az erőforráscsoport az [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítható el.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatók a metrikák és a diagnosztikai naplók a következő feladatok végrehajtásával:

> [!div class="checklist"]
> * Az Azure CLI használatával hozzon létre egy IoT hubot, egy szimulált eszközt és egy Storage-fiókot.  
> * Engedélyezze a diagnosztikai naplókat. 
> * Metrikák engedélyezése
> * Riasztások beállítása ezekhez a metrikához. 
> * Töltsön le és futtasson egy alkalmazást, amely egy IoT-eszközt szimulál, amely üzeneteket küld az elosztónak. 
> * Futtassa az alkalmazást, amíg a riasztások elkezdenek tüzet. 
> * Tekintse meg a metrikák eredményeit, és tekintse meg a diagnosztikai naplókat. 

A következő oktatóanyag az IoT-eszközök állapotának kezelését mutatja be. 

> [!div class="nextstepaction"]
> [Eszközök konfigurálása háttérszolgáltatásból](tutorial-device-twins.md)