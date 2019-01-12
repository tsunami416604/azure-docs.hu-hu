---
title: Állítsa be, és a metrikák és diagnosztikai naplók használata az Azure IoT hub |} A Microsoft Docs
description: Beállítása és az Azure IoT hub-metrikák és diagnosztikai naplók használata
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/15/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8bcc72cf151b085c7f65b6c600a49642cd330bac
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248827"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Oktatóanyag: Beállítása és IoT hub-metrikák és diagnosztikai naplók használata

Van egy IoT Hub-megoldás az éles környezetben futó, szeretné-e bizonyos metrikák és diagnosztikai naplók engedélyezése. Majd ha probléma merül fel, és tekintse meg segít diagnosztizálni a problémát, és gyorsabban javítást adatokkal rendelkezik. Ebből a cikkből látni fogja a diagnosztikai naplók engedélyezése, és hogyan hibáinak kereséséhez. Is megtekinteni és riasztások, amely értesíti, ha a metrikák nyomja le az egyes határ bizonyos metrikák is ki kell beállítani. Lehet például egy e-mailt küldeni, vagy amikor telemetriai üzenetek száma meghaladja az egy adott határt, vagy ha a használt üzenetek számának beolvasása közeli üzeneteket az IoT Hub engedélyezett napi kvótáját. 

Egy példa használati esetekhez egy benzinkutat, hol találhatók a pedig előre felkészülhet az IoT hub IoT-eszközök által küldött kommunikációhoz. Ellenőrzi a hitelkártyáját, és a egy adattárba írt a végső tranzakció. Ha az IoT-eszközök, állítsa le a hubhoz csatlakozó és küld üzeneteket, ez sokkal több nehéz javításához, ha nincs láthatósága, mi történik.

Ebben az oktatóanyagban az Azure-minta a [IoT Hub útválasztás](tutorial-routing.md) üzeneteket küldeni az IoT hubon.

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Azure CLI-vel, hozzon létre egy IoT hubot, a szimulált eszköz és a egy tárfiókot.  
> * Diagnosztikai naplók engedélyezése.
> * Engedélyezze a mérőszámok.
> * Állítsa be ezeket a metrikákhoz riasztásokat. 
> * Töltse le és futtathat egy alkalmazást, amely szimulálja az üzenetek küldése a hub az IoT-eszközökön. 
> * Futtassa az alkalmazást, amíg a riasztások üzenetszám megkezdése. 
> * Az eredmények megtekintése a metrikákat, és tekintse meg a diagnosztikai naplókat. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

- A [Visual Studio](https://www.visualstudio.com/) telepítése. 

- E-mail-fiók e-mail fogadni képes.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Erőforrások beállítása

Ebben az oktatóanyagban szüksége az IoT hub, a storage-fiók és a szimulált IoT-eszköz. Ezek az erőforrások létrehozhatók az Azure CLI vagy az Azure PowerShell használatával. Ugyanazt az erőforráscsoportot és helyet használja minden erőforráshoz. Így mindent egyetlen lépésben távolíthat el az erőforráscsoport törlésével.

Ezek azok a szükséges lépéseket.

1. Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md). 

2. Hozzon létre egy IoT hubot.

3. Hozzon létre egy standard szintű V1-tárfiókot Standard_LRS-replikációval.

4. Hozzon létre egy eszközidentitást ahhoz a szimulált eszközhöz, amely üzeneteket küld a központnak. Mentse a kulcsot a tesztelési fázishoz.

### <a name="set-up-resources-using-azure-cli"></a>Azure CLI-vel erőforrások beállítása

Másolja és illessze be az alábbi szkriptet a Cloud Shellbe. A szolgáltatás azt feltételezi, hogy már bejelentkezett, és soronként futtatja a szkriptet. Az erőforráscsoport ContosoResources az új erőforrások jönnek létre.

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
>Amikor az eszközidentitást hoz létre, a következő hiba jelenhet meg: *Nincsenek kulcsok az IoT Hub ContosoTestHub házirend iothubowner található*. Ez a hiba javításához az Azure CLI IoT-bővítmény frissítése, majd futtassa a az utolsó két parancsot a szkript újra. 
>
>Ez a parancs a bővítmény frissítése. Futtassa ezt a Cloud Shell-példányában.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>A diagnosztikai naplók engedélyezése 

[Diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-overview.md) alapértelmezés szerint le vannak tiltva, ha létrehoz egy új IoT hubot. Ebben a szakaszban engedélyezze a diagnosztikai naplók a hub.

1. Először, ha még nem tette az hubon a portál, kattintson **erőforráscsoportok** kattintson arra az erőforráscsoportra Contoso-erőforrásokat. Válassza ki a hub-erőforrások jelenik meg a listából. 

2. Keresse meg a **figyelés** szakasz az IoT Hub panel. Kattintson a **Diagnosztikai beállítások** elemre. 

   ![Képernyőfelvétel: az IoT Hub panel diagnosztikai beállítások részeként.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Ellenőrizze, hogy helyes-e az előfizetésben és erőforráscsoportban. Alatt **erőforrástípus**, törölje a jelet **válassza ki az összes**, majd keresse meg és ellenőrzése **az IoT Hub**. (Mellett helyezi el a pipa jelre *válassza ki az összes* újra, egyszerűen figyelmen kívül hagyáshoz.) A **erőforrás**, válassza ki a hub nevét. A képernyőnek a képhez hasonlóan kell kinéznie: 

   ![Képernyőfelvétel: az IoT Hub panel diagnosztikai beállítások részeként.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Most kattintson **diagnosztika bekapcsolása**. A diagnosztikai beállítások panel jelenik meg. Adja meg a diagnosztikai naplók beállítások neve "diagnosztika-hub".

5. Ellenőrizze **archiválás tárfiókba**. 

   ![Képernyőfelvétel: a diagnosztikai beállítás, a storage-tárfiókba archiválhatja.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Kattintson a **konfigurálása** megtekintéséhez a **válassza ki a tárfiókot** képernyő, válassza ki a megfelelő (*contosostoragemon*), és kattintson a **OK** , térjen vissza a diagnosztikai beállítások panel. 

   ![Képernyőfelvétel: beállítása a diagnosztikai naplók archiválása a tárfiókhoz.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. Alatt **LOG**, ellenőrizze **kapcsolatok** és **eszköz Telemetriai**, és állítsa be a **megőrzése (nap)** 7 nappal minden. A diagnosztikai beállítások képernyő most következő képhez hasonlóan kell kinéznie:

   ![Képernyőfelvétel: a végső diagnosztikai napló beállításokat.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Kattintson a **Mentés** gombra a beállítások mentéséhez. Zárja be a diagnosztikai beállítások panelt.

Később ha megnézi a diagnosztikai naplók, lesz a connect láthat, és válassza le a naplózást az eszközhöz. 

## <a name="set-up-metrics"></a>Állítsa be a metrikák 

Most már készen bizonyos metrikák figyelheti a hubnak küldött üzeneteket. 

1. Az IoT hub a beállítások panelen kattintson a a **metrikák** beállítást a **figyelés** szakaszban.

2. Kattintson a képernyő tetején **(automatikus) az elmúlt 24 órából**. Megjelenő legördülő listában válassza ki a **utolsó 4 óra** a **időtartomány**, és állítsa be **idő részletessége** való **1 perces**, helyi idő. Kattintson a **alkalmaz** ezek a beállítások mentéséhez. 

   ![Képernyőfelvétel: a metrikák beállításai.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Alapértelmezés szerint van egy metrika bejegyzés. Hagyja meg az erőforráscsoport, az alapértelmezett, valamint a metrika névtérben. Az a **metrika** legördülő listában válassza **küldött Telemetriai üzeneteket**. Állítsa be **összesítési** való **Sum**.

   ![Képernyőfelvétel: a telemetriai üzeneteket metrika hozzáadása.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Most kattintson **metrika hozzáadása** , adjon hozzá egy másik metrikát a diagramhoz. Jelölje ki az erőforráscsoportot (**ContosoTestHub**). A **metrika**válassza **használt üzenetek teljes száma**. A **összesítési**válassza **átlagos**. 

   Most a képernyőn látható kis méretben metrikáját *küldött Telemetriai üzeneteket*, plusz az új metrikát *használt üzenetek teljes száma*.

   ![Képernyőfelvétel: a telemetriai üzeneteket metrika hozzáadása.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Kattintson a **rögzítés az irányítópulton**. Ez lesz rögzítheti az az Azure portal irányítópultján újra tud elérni. Nem rögzítés az irányítópulton, ha a beállítások nem maradnak meg.

## <a name="set-up-alerts"></a>Riasztások beállítása

Nyissa meg a hubhoz a portálon. Kattintson a **erőforráscsoportok**válassza *ContosoResources*, majd válassza ki az IoT Hub *ContosoTestHub*. 

Az IoT Hub nem lett migrálva az a [metrikák az Azure monitorban](/azure/azure-monitor/platform/data-collection#metrics) még; kell használnia [klasszikus riasztások](/azure/azure-monitor/platform/alerts-classic.overview).

1. A **figyelés**, kattintson a **riasztások** látható, a fő figyelmeztetési képernyő. 

   ![Képernyőfelvétel: hogyan találhatja meg a klasszikus riasztások.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. A klasszikus riasztások itt, kattintson a hivatkozásra **klasszikus riasztások**. 

    ![Képernyőfelvétel: a klasszikus riasztások képernyő.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Töltse ki a mezőket: 

    **Előfizetés**: Hagyja a mezőt állítsa a jelenlegi előfizetésében.

    **forrás**: Adja meg a mezőben *metrikák*.

    **Erőforráscsoport**: Adja meg a mezőben az aktuális erőforráscsoporton *ContosoResources*. 

    **Erőforrástípus**: Adja meg a mezőben az IoT hubnak. 

    **Erőforrás**: Válassza ki az IoT hub *ContosoTestHub*.

3. Kattintson a **metrikariasztás hozzáadása (klasszikus)** állíthat be egy új riasztás.

    Töltse ki a mezőket:

    **Név**: Adja meg például a riasztási szabály nevét *telemetria-üzenetek*.

    **Leírás**: Adja meg például a riasztás leírása *riasztás, ha 1000 telemetriai üzeneteket küldi*. 

    **forrás**: Állítsa a bestattempt értékre *metrikák*.

    **Előfizetés**, **erőforráscsoport**, és **erőforrás** kiválasztott értékre kell állítani a **klasszikus riasztások megtekintése** képernyő. 

    Állítsa be **metrika** való *küldött Telemetriai üzeneteket*.

    ![Képernyőfelvétel: a telemetriai üzeneteket küldött egy klasszikus riasztás beállítását.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. A diagram után állítsa be a következő mezőket:

   **A feltétel**: Állítsa be *nagyobb, mint*.

   **Küszöbérték**: Állítsa be beállításé pedig 1000.

   **Időszak**: Állítsa be *az utolsó 5 percben*.

   **Értesítés e-mailek címzettjeinek**: Itt adja meg e-mail címét. 

   ![Képernyőfelvétel: alsó riasztások képernyő felét.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Kattintson a **OK** menteni a riasztást. 

5. Most már egy másik riasztás beállítása a *használt üzenetek teljes száma*. Ez a metrika akkor hasznos, ha azt szeretné, küldjön egy riasztást, ha a használt üzenetek száma majdnem elérte a kvótát, az IoT hub – lehetővé teszi, hogy a hubot hamarosan megkezdi üzenetek elutasítása.

   Az a **klasszikus riasztások megtekintése** kattintson **metrikariasztás hozzáadása (klasszikus)**, majd adja meg ezeket a mezőket a a **szabály felvétele** ablaktáblán.

   **Név**: Adja meg például a riasztási szabály nevét *szám,-üzenetek-használt*.

   **Leírás**: Adja meg például a riasztás leírása *riasztás, ha közeli kvóta első*.

   **forrás**: Adja meg a mezőben *metrikák*.

    **Előfizetés**, **erőforráscsoport**, és **erőforrás** kiválasztott értékre kell állítani a **klasszikus riasztások megtekintése** képernyő. 

    Állítsa be **metrika** való *használt üzenetek teljes száma*.

6. Az ábra alatt töltse ki a következő mezőket:

   **A feltétel**: Állítsa be *nagyobb, mint*.

   **Küszöbérték**: Állítsa be beállításé pedig 1000.

   **Időszak**: Adja meg a mezőben *az utolsó 5 percben*. 

   **Értesítés e-mailek címzettjeinek**: Itt adja meg e-mail címét. 

   Kattintson a **OK** a szabály mentéséhez. 

5. Meg kell jelennie a klasszikus riasztások panelen két riasztások: 

   ![Képernyőkép ábrázoló klasszikus riasztások képernyő és az Új riasztási szabályok.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Zárja be a riasztások panelen. 
    
    Ezekkel a beállításokkal riasztást fog kapni, amikor üzenetek száma nagyobb, mint 400-as, és ha a használt üzenetek teljes száma meghaladja.

## <a name="run-simulated-device-app"></a>Szimulálteszköz-alkalmazás futtatása

Korábban, a szkript telepítési szakaszában beállított egy eszközt az IoT-eszköz használatának szimulálására. Ebben a szakaszban egy .NET-konzolalkalmazást tölt le, amely egy, az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál.  

Az [IoT-eszköz szimulációjára](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) szolgáló megoldás letöltése. Ez a hivatkozás letölti olyan adattárat, amelynek a; több alkalmazás a megoldást keres az iot-hub/oktatóprogramok/útválasztás/SimulatedDevice /.

Kattintson duplán a megoldásfájlra (SimulatedDevice.sln), a kód megnyitásához a Visual Studióban, majd nyissa meg a Program.cs fájlt. Az `{iot hub hostname}` értéket cserélje le az IoT Hub gazdagépnevére. Az IoT Hub gazdagépnevének formátuma: **{iot-hub-name}.azure-devices.net**. Ebben az oktatóanyagban a központ gazdagépneve: **ContosoTestHub.azure-devices.net**. Ezután a `{device key}` értéket cserélje le az eszközkulcsra, amelyet korábban, a szimulált eszköz beállítása során mentett. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Futtatás és tesztelés 

A program.cs fájlban módosítsa a `Task.Delay` 10 1000, ami csökkenti az üzenetek küldését 1 másodperc.01 másodperc között eltelt idő. Ez a késleltetés lecsökkentik növeli a küldött üzenetek számát.

```csharp
await Task.Delay(10);
```

Futtassa a konzolalkalmazást. Várjon néhány percet (10 – 15). Láthatja, hogy az a konzol képernyőn az alkalmazás elosztóhoz a szimulált eszközről küldött üzeneteket.

### <a name="see-the-metrics-in-the-portal"></a>A metrikák a portálon

Nyissa meg a metrikákat az irányítópultról. Az idő értékek módosításához *elmúlt 30 percben* idő részletességét a *1 perces*. A telemetriai üzeneteket küldi, és használja a diagramra, a diagram alján a legutóbbi számokkal üzenetek teljes száma látható. 

   ![Képernyőfelvétel: a metrikákat.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>A riasztások megtekintéséhez

Vissza a riasztásokat. Kattintson a **erőforráscsoportok**válassza *ContosoResources*, majd válassza ki a hub *ContosoTestHub*. Megjelenik a hub tulajdonságlapján válassza **riasztások**, majd **klasszikus riasztások**. 

A küldött üzenetek száma meghaladja a korlátot, amikor először e-mail-riasztásokat. Ha vannak aktív riasztások megtekintéséhez nyissa meg az eseményközpont, és válassza **riasztások**. Ebben bemutatjuk, a riasztásokat, amelyek aktív, és nincsenek a figyelmeztetéseket. 

   ![Képernyőfelvétel: a riasztások történt.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Kattintson a riasztásra vonatkozó telemetriai üzeneteket. A metrika eredményt és az eredményeket a diagram mutatja. Figyelmeztet a riasztást kiváltó küldött e-mailben is, ez a rendszerkép hasonlóan néz ki:

   ![Az e-mailt a riasztások történt megjelenítő képernyőkép.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Diagnosztikai naplók

A diagnosztikai naplók beállítása a blob storage-bA exportálva. Nyissa meg az erőforráscsoportot, és válassza ki a tárfiókját *contosostoragemon*. Válassza ki a blobokat, majd nyissa meg a tároló *insights-logs-kapcsolatok*. Részletes elemzést, amíg juthat el az aktuális dátumot, és válassza ki a legutóbbi fájlt. 

   ![Képernyőkép a storage-tárolóba kapott tekintse meg a diagnosztikai naplókat.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Kattintson a **letöltése** töltse le és nyissa meg. A naplók és megszakításának üzeneteket küld a hubon, az eszköz látja. Itt egy példa:

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

Az összes ebben az oktatóanyagban létrehozott erőforrások eltávolításához törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben azt eltávolítja, az IoT hub, a tárfiókot és magát az erőforráscsoportot. Ha metrikák az irányítópultra van rögzítve, akkor távolítsa el manuálisan, az egyes jobb felső sarokban lévő három pontra kattint, és kiválasztja azokat a **eltávolítása**.

Az erőforráscsoport az [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítható el.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan használható a metrikák és diagnosztikai naplók a következő feladatok végrehajtásával:

> [!div class="checklist"]
> * Azure CLI-vel, hozzon létre egy IoT hubot, a szimulált eszköz és a egy tárfiókot.  
> * Diagnosztikai naplók engedélyezése. 
> * Engedélyezze a mérőszámok.
> * Állítsa be ezeket a metrikákhoz riasztásokat. 
> * Töltse le és futtathat egy alkalmazást, amely szimulálja az üzenetek küldése a hub az IoT-eszközökön. 
> * Futtassa az alkalmazást, amíg a riasztások üzenetszám megkezdése. 
> * Az eredmények megtekintése a metrikákat, és tekintse meg a diagnosztikai naplókat. 

A következő oktatóanyag az IoT-eszközök állapotának kezelését mutatja be. 

> [!div class="nextstepaction"]
[Eszközök konfigurálása háttérszolgáltatásból](tutorial-device-twins.md)