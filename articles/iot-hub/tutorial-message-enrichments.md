---
title: Oktatóanyag – az Azure IoT Hub üzenetek gazdagítása
description: 'Oktatóanyag: az üzenetek dúsításának használata az Azure IoT Hub üzeneteihez'
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 2c115bf0ad21e905e998692fbbc175f5aa52b86d
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014243"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Oktatóanyag: Az Azure IoT Hub üzenet-gazdagítás használata (előzetes verzió)

Az *üzenet* -gazdagítás a IoT hub azon képessége, hogy az üzeneteket a kijelölt végpontnak küldött üzenetek elküldése előtt további információkkal lehessen lepecsételni. Az üzenetek dúsításának használatának egyik oka az, hogy olyan adathalmazt tartalmazzon, amely az alsóbb rétegbeli feldolgozás egyszerűsítésére használható. Például az eszközök telemetria-üzeneteinek az eszközökhöz való bővítésével csökkentheti az ügyfelek terhelését, így az eszközökhöz tartozó Twin API-hívásokat is megteheti az adatokhoz. További információ: az [üzenetek dúsításának áttekintése](iot-hub-message-enrichments-overview.md).

Ebben az oktatóanyagban az Azure CLI használatával állítja be az erőforrásokat, köztük két végpontot, amelyek két különböző tároló-tárolóra mutatnak – **gazdagított** és **eredeti**. Ezután a [Azure Portal](https://portal.azure.com) használatával konfigurálja az üzenetek dúsítását, hogy csak a **dúsított** tárolóval rendelkező végpontnak küldött üzenetekre alkalmazza a rendszer. Üzeneteket küld a IoT Hubnak, amelyek mindkét tárolóba vannak irányítva. Csak a **dúsított** tároló számára a végpontnak küldött üzenetek gazdagítva lesznek.

Az oktatóanyag elvégzéséhez az alábbi feladatokat kell végrehajtani:

**IoT Hub üzenet-gazdagítás használata**
> [!div class="checklist"]
> * Az Azure CLI használatával hozza létre az erőforrásokat – egy IoT hubot, egy két végponttal rendelkező Storage-fiókot és az útválasztási konfigurációt.
> * Az üzenet-gazdagítás konfigurálásához használja a Azure Portal.
> * Futtasson egy alkalmazást, amely egy IoT-eszközt szimulál, amely üzeneteket küld az elosztónak.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenetek gazdagítása a várt módon működik-e.

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* A [Visual Studio](https://www.visualstudio.com/) telepítése.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>A mintakód beolvasása

Töltse le a [IoT-eszköz szimulációját](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) , és csomagolja ki. Ez a tárház több alkalmazással is rendelkezik, beleértve azt is, amelyet az üzenetek küldésére fog használni az IoT hub számára.

Ez a letöltés tartalmazza azt a parancsfájlt is, amely az üzenetek dúsításának teszteléséhez használt erőforrásokat hozza létre. A szkript a/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Egyelőre megtekintheti a szkriptet, és használhatja azt. A szkriptet közvetlenül a cikkből is másolhatja.

Ha készen áll a tesztelés elindítására, az eszköz-szimulációs alkalmazás a letöltés után üzenetet küld az IoT hub-nak.

## <a name="set-up-and-configure-resources"></a>Erőforrások beállítása és konfigurálása

A szükséges erőforrások létrehozása mellett az Azure CLI-szkript a két útvonalat is konfigurálja külön tárolók közötti végpontokra. Az Útválasztás konfigurálásával kapcsolatos további információkért tekintse meg [](tutorial-routing.md)az útválasztással foglalkozó oktatóanyagot. Az erőforrások beállítása után a [Azure Portal](https://portal.azure.com) segítségével konfigurálja az egyes végpontok üzenet-dúsítását, majd folytassa a tesztelési lépéssel.

> [!NOTE]
> Az összes üzenet mindkét végponthoz van irányítva, de csak a végpontra irányuló, konfigurált üzenet-dúsítású üzenetek lesznek bővítve.
>

Használhatja az alábbi parancsfájlt, vagy megnyithatja a parancsfájlt a letöltött adattár/Resources mappájából. A szkript által végrehajtandó lépések a következők:

* IoT Hub létrehozása
* Tárfiók létrehozása.
* Hozzon létre két tárolót a Storage-fiókban – egyet a dúsított üzenetekhez, egyet pedig a nem dúsított üzenetekhez.
* Útválasztás beállítása két különböző Storage-fiókhoz.
    * Hozzon létre egy végpontot minden egyes Storage-fiók tárolóhoz.
    * Hozzon létre egy útvonalat a Storage-fiókok tárolójának összes végpontján.

Több olyan erőforrás neve van, amelynek globálisan egyedinek kell lennie, például a IoT Hub neve és a Storage-fiók neve. A szkript egyszerűbb futtatásához ezeket az erőforrásokat a *randomValue*nevű véletlenszerű alfanumerikus érték fűzi hozzá. A rendszer egyszer hozza létre a randomValue a parancsfájl tetején, és szükség szerint hozzáfűzi az erőforrások nevét a parancsfájlban. Ha nem szeretné, hogy véletlenszerű legyen, beállíthatja egy üres sztringre vagy egy adott értékre.

Ha még nem tette meg, nyisson meg egy [Cloud Shell ablakot a bash számára.](https://shell.azure.com) Nyissa meg a parancsfájlt a kibontott tárházban, majd a CTRL-A billentyűkombinációval jelölje ki az összes, majd a CTRL-C billentyűkombinációt a másoláshoz. Másik lehetőségként átmásolhatja a következő CLI-szkriptet, vagy megnyithatja közvetlenül a Cloud shellben. Illessze be a szkriptet az Azure Cloud Shell ablakába úgy, hogy a jobb gombbal a parancssorra kattint, és kiválasztja a **Beillesztés**elemet. A parancsfájl egyszerre egy utasítást futtat. Ha a parancsfájl futása leáll, válassza az **ENTER billentyűt** , és győződjön meg arról, hogy az utolsó parancsot futtatja. A következő kódrészlet a használt szkriptet mutatja be, a megjegyzésekkel pedig elmagyarázza, hogy mit csinál.

Itt láthatók a szkript által létrehozott erőforrások. A **dúsított** érték azt jelenti, hogy az erőforrás a dúsítással rendelkező üzenetekhez használható. Az **eredeti** érték azt jelenti, hogy az erőforrás a nem dúsított üzenetekhez használható.

| Name (Név) | Value |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| tároló neve | eredeti  |
| tároló neve | sokoldalú és  |
| IoT-eszköz neve | Contoso-test-Device |
| IoT Hub neve | ContosoTestHubMsgEn |
| Storage-fiók neve | contosostorage |
| végpont neve 1 | ContosoStorageEndpointOriginal |
| végpont neve 2 | ContosoStorageEndpointEnriched|
| útvonal neve 1 | ContosoStorageRouteOriginal |
| útvonal neve 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Ezen a ponton az erőforrások mindegyike be van állítva, és az Útválasztás konfigurálva van. Megtekintheti az üzenet-útválasztási konfigurációt a portálon, és beállíthatja az üzenetek dúsítását a **dúsított** tárolóba kerülő üzenetekhez.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Útválasztás megtekintése és az üzenetek dúsításának konfigurálása

1. Lépjen a IoT Hub az **erőforráscsoportok**lehetőség kiválasztásával, majd válassza ki az oktatóanyaghoz beállított erőforráscsoportot (**ContosoResources_MsgEn**). Keresse meg a IoT Hub a listában, és válassza ki. Válassza az IOT hub *üzenet-útválasztás** elemét.

   ![Üzenet-útválasztás kiválasztása](./media/tutorial-message-enrichments/select-iot-hub.png)

   Az üzenet-útválasztási ablaktáblán három lap található: **útvonalak**, **Egyéni végpontok**és **gazdagított üzenetek**. Az első két lapon megtekintheti a parancsfájl által beállított konfigurációt. Az üzenet-gazdagítás hozzáadásához használja a harmadik lapot. Gazdagabbá teheti az üzeneteket a végponthoz a **dúsított**Storage-tárolóhoz. Adja meg a nevet és az értéket, majd válassza ki a végpont **ContosoStorageEndpointEnriched** a legördülő listából. Íme egy példa egy olyan dúsítás beállítására, amely hozzáadja a IoT Hub nevet az üzenethez:

   ![Első alkoholtartalom hozzáadása](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adja hozzá ezeket az értékeket a ContosoStorageEndpointEnriched-végpont listájához.

   | Name (Név) | Value | Végpont (legördülő lista) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin. Tags. location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Vevőkód | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Ha az eszköz nem rendelkezik Twin-vel, az itt megadott érték karakterláncként lesz lepecsételve az üzenet-gazdagított értékhez. Az eszköz Twin adatainak megtekintéséhez nyissa meg a hubot a portálon, majd válassza az **IoT-eszközök**lehetőséget, válassza ki az eszközt, majd válassza a lap tetején található **eszközök Twin** elemet.
   >
   > A két információ szerkesztésével hozzáadhat címkéket (például helyet), és beállíthatja egy adott értékre, ha szeretné. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](iot-hub-devguide-device-twins.md)

3. Ha elkészült, a panelnek a következő képhez hasonlóan kell kinéznie:

   ![Táblázat az összes hozzáadott alkoholtartalommal](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. A módosítások mentéséhez kattintson az **alkalmaz** gombra.

## <a name="send-messages-to-the-iot-hub"></a>Üzenetek küldése a IoT Hub

Most, hogy az üzenet gazdagítása konfigurálva van a végponthoz, futtassa a szimulált eszköz alkalmazást, hogy üzeneteket küldjön a IoT Hub. A hub olyan szabályokkal lett beállítva, amelyek a következőket hajtják végre:

* A tárolási végpont ContosoStorageEndpointOriginal továbbított üzenetek nem lesznek gazdagítva, és a tárolóban `original`lesznek tárolva.

* A tárolási végpont ContosoStorageEndpointEnriched átirányított üzenetek a tárolóban `enriched`lesznek gazdagítva és tárolva.

A szimulált eszköz alkalmazás a kibontott letöltésben szereplő egyik alkalmazás. Az alkalmazás az [útválasztási oktatóanyag](tutorial-routing.md)különböző üzenet-útválasztási módszereire küld üzeneteket. Ez magában foglalja az Azure Storage-t is.

Kattintson duplán a megoldás fájlra (IoT_SimulatedDevice. SLN) a kód megnyitásához a Visual Studióban, majd nyissa meg a Program.cs. Helyettesítse `{your hub name}` be az IoT hub nevét. Az IoT hub-állomásnév formátuma **{a hub neve}. Azure-Devices.net**. Ebben az oktatóanyagban a hub-gazdagép neve **ContosoTestHubMsgEn.Azure-Devices.net**. Ezután cserélje `{device key}` le a korábban mentett eszköz kulcsát, amikor a parancsfájlt az erőforrások létrehozásához futtatja.

Ha nem rendelkezik az eszköz kulcsával, lekérheti azt a portálról. A bejelentkezés után lépjen az **erőforráscsoportok**elemre, válassza ki az erőforráscsoportot, majd válassza ki a IoT hub. Keresse meg a **IoT eszközöket** a tesztelési eszközhöz, és válassza ki az eszközt. A vágólapra másoláshoz kattintson az **elsődleges kulcs** melletti másolás ikonra.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Futtatás és tesztelés

Futtassa a konzolalkalmazást. Várjon néhány percet. Az elküldött üzenetek az alkalmazás konzol képernyőjén jelennek meg.

Az alkalmazás másodpercenként elküld egy új üzenetet az eszközről a felhőbe az IoT Hubnak. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával, a hőmérséklettel, a páratartalommal és az üzenet szintjével, amely alapértelmezés szerint `normal`. Véletlenszerűen rendeli hozzá `critical` a vagy `storage`a-t, így az üzenet a Storage-fiókhoz vagy az alapértelmezett végponthoz lesz irányítva. A Storage-fiókban a **dúsított** tárolóba küldött üzenetek gazdagítva lesznek.

A több Storage-üzenet elküldése után megtekintheti az adatmegjelenítést.

1. Válassza az **erőforráscsoportok**elemet, majd keresse meg az erőforráscsoportot (ContosoResourcesMsgEn), és jelölje ki.

2. Válassza ki a Storage-fiókját (contosostorage). Ezután válassza a **Storage Explorer (előzetes verzió)** lehetőséget a bal oldali kiválasztási ablaktáblán.

   ![A Storage Explorer kiválasztása](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Válassza a **blob** -tárolók lehetőséget a két használható tároló megjelenítéséhez.

   ![A Storage-fiókban található tárolók](./media/tutorial-message-enrichments/show-blob-containers.png)

A **dúsított** tárolóban lévő üzeneteknek az üzenetekben található alkoholtartalom-növeléssel kell rendelkezniük. Az **eredeti** nevű tárolóban lévő üzenetekben a nyers üzenetek nem rendelkeznek alkoholtartalom-növeléssel. Bontsa ki az egyik tárolót, amíg el nem jut az aljára, és megnyithatja a legfrissebb üzenetfájl-t, majd ugyanezt a másik tárolónál is ellenőrizheti, hogy az adott tárolóban lévő üzenetekhez nincsenek-e hozzáadott bővítések.

Ha megtekinti a dúsított üzeneteket, a "saját IoT Hub" nevet kell látnia a hub nevével, valamint a helyet és az ügyfél-azonosítót, például a következőt:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

És itt egy nem dúsított üzenet. A "My IoT Hub", a "devicelocation" és a "Vevőkód" nem jelenik meg itt, mert ez a végpont nem rendelkezik alkoholtartalom-növeléssel.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani az oktatóanyagban létrehozott összes erőforrást, törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT hubot, a Storage-fiókot és magát az erőforráscsoportot.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Erőforrások törlése az Azure CLI használatával

Az erőforráscsoport az [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítható el. `$resourceGroup`az oktatóanyag elején a **ContosoResources** vissza lett állítva.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az alábbi lépéseket követve konfigurálta és tesztelte az üzenet-gazdagítók hozzáadását IoT Hub üzenetekhez:

**IoT Hub üzenet-gazdagítás használata**
> [!div class="checklist"]
> * Az Azure CLI használatával hozza létre az erőforrásokat – egy IoT hubot, egy két enendpoints rendelkező Storage-fiókot és az útválasztási konfigurációt.
> * Az üzenet-gazdagítás konfigurálásához használja a Azure Portal.
> * Futtasson egy alkalmazást, amely egy IoT-eszközt szimulál, amely üzenetet küld az elosztónak.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenetek gazdagítása a várt módon működik-e.

Az üzenetek dúsításával kapcsolatos további információkért tekintse meg az üzenetek bővítésének [áttekintése](iot-hub-message-enrichments-overview.md)című témakört.

Az üzenetek útválasztásával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Eszközről a felhőbe irányuló üzenetek küldése különböző végpontokra IoT Hub üzenet-útválasztás használatával](iot-hub-devguide-messages-d2c.md)

* [Oktatóanyag: IoT Hub Útválasztás](tutorial-routing.md)