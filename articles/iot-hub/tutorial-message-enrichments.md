---
title: Oktatóanyag – Azure IoT Hub üzenet végrehajtott információbeolvasás használatával
description: Oktatóanyag üzenetek végrehajtott információbeolvasás használata az Azure IoT Hub-üzenetek megjelenítése
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259074"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Oktatóanyag: Azure IoT Hub üzenet végrehajtott információbeolvasás (előzetes verzió) használatával

*Üzenet végrehajtott információbeolvasás* rendszer azon képessége, az IoT Hub *stamp* az üzeneteket a kijelölt végponthoz való elküldése előtt további információkat tartalmazó üzenetek. Egy üzenet végrehajtott információbeolvasás használandó oka tartalmazza, amelyek segítségével egyszerűsítheti az alsóbb feldolgozási adatokat. Például eszköz telemetriai üzeneteket, egy eszköz ikereszköz bővítését csökkentheti ügyfeleik számára, hogy az API-khoz ezt az információt ikereszköz terhelését. További információkért lásd: a [üzenet végrehajtott információbeolvasás áttekintése](iot-hub-message-enrichments-overview.md).

Ebben az oktatóanyagban használja az Azure CLI beállítása az erőforrások, például két olyan végpontok, amelyek két különböző storage-tárolók – **bővített** és **eredeti**. Akkor használja a [az Azure portal](https://portal.azure.com) üzenet végrehajtott információbeolvasás, hogy csak a alkalmazni a koncového bodu küldött üzenetek konfigurálása a **bővített** storage-tárolóba. Üzeneteket küld az IoT Hubban, amely mindkét storage-tárolók legyenek átirányítva. Csak a végpont számára küldött üzenetek a **bővített** tárolót fog kell bővített.

Az alábbiakban a feladatokat fogja elvégezni az oktatóanyag elvégzéséhez:

**Az IoT Hub üzenet végrehajtott információbeolvasás használatával**
> [!div class="checklist"]
> * Az Azure CLI használatával hozzon létre az erőforrások – egy IoT hubot, két végpontot egy tárfiókot és az útválasztási konfigurációt.
> * Az Azure portal segítségével üzenet végrehajtott információbeolvasás konfigurálhatja.
> * Futtassa az alkalmazást, amely szimulálja az üzenetek küldése a hub az IoT-eszközökön.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenet végrehajtott információbeolvasás a várt módon működnek.

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* A [Visual Studio](https://www.visualstudio.com/) telepítése.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>A mintakód beolvasása

Töltse le [IoT-eszköz szimulálása](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) és bontsa ki azt. Ez a tárház rendelkezik több alkalmazást, beleértve üzenetek küldése az IoT hub használatával.

Ez a letöltés is tartalmaz a parancsfájl az üzenet végrehajtott információbeolvasás tesztelésére használt erőforrások létrehozásához. A parancsfájl /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli szerepel. Most, tekintse meg a parancsfájl, és azokat használja. Közvetlenül a cikkben szereplő másolhatja a szkriptet is.

Amikor készen áll a tesztelés megkezdése, az Eszközszimuláció alkalmazás letöltéséhez használandó üzenetet küld az IoT hubnak.

## <a name="set-up-and-configure-resources"></a>És erőforrások beállítása

Mellett hoz létre a szükséges erőforrásokat, az Azure CLI-példaszkript is konfigurálja a végpontokat, amelyek különböző storage-tárolók két útvonalakat. Az Útválasztás konfigurálásáról további információkért lásd: a [útválasztási oktatóanyag](tutorial-routing.md). Az erőforrások beállítása után használja a [az Azure portal](https://portal.azure.com) az üzenet végrehajtott információbeolvasás végpontok konfigurálása, és folytassa a tesztelési lépéssel.

> [!NOTE]
> Összes üzenet legyenek átirányítva a mindkét végponton, de csak a végponthoz konfigurált üzenet végrehajtott információbeolvasás az üzeneteket fogja kell bővített.
>

Az alábbi parancsfájlt használhatja, vagy nyissa meg azt a letöltött adattár /resources mappájában. A parancsfájl elvégzi lépései a következők:

* IoT Hub létrehozása
* Tárfiók létrehozása.
* Hozzon létre két tárolót a storage-fiók – egy képi elemekben gazdag üzenetekhez és egy, a rendszer nem bővített üzeneteket.
* Állítsa be a két különböző storage-fiókok útválasztást.
    * Az egyes storage-fiók tárolók egy végpont létrehozását.
    * Hozzon létre egy útvonalat a tárfiókvégpontok tároló minden egyes.

Nincsenek több erőforrás nevét, például az IoT Hub nevét és a tárfiók nevének globálisan egyedinek kell lennie. Ahhoz, hogy könnyebben a szkript futtatása, ezek erőforrásnevek hozzáfűzi a véletlenszerű alfanumerikus érték nevű *randomValue*. A randomValue egyszer jön létre, a szkript elején, és hozzáfűzi az erőforrások nevei a parancsfájl egész igény szerint. Ha nem szeretné, hogy a véletlenszerű, beállíthatja azt egy adott értékre vagy üres karakterlánc.

Ha ezt még nem tette meg, nyisson meg egy [a Bash Cloud Shell ablakába.](https://shell.azure.com). Nyissa meg azt a kicsomagolt tárházban, használja azt az összes kijelölése Ctrl-A, majd másolja azt a Ctrl-C. Azt is megteheti másolja a következő CLI-szkript, vagy nyissa meg a közvetlenül a cloud shellben. Illessze be a szkriptet az Azure cloud shell ablakban kattintson a jobb gombbal a parancssorban, majd válassza **beillesztési**. A szkript futása egy utasítás egyszerre. Miután a parancsfájl futása áll le, válassza ki a **Enter** , hogy az utolsó parancs futása. A következő kódrészletet bemutatja, a parancsfájl, amely elmagyarázza, milyen módon megjegyzésekkel szolgál.

Íme a szkript által létrehozott erőforrásokat. **Bővített** azt jelenti, hogy erőforrás végrehajtott információbeolvasás üzenetekhez. **Eredeti** azt jelenti, hogy erőforrás nem vannak bővített üzenetek esetében.

| Name (Név) | Érték |
|-----|-----|
| Erőforráscsoport | ContosoResourcesMsgEn |
| Tároló neve | Eredeti  |
| Tároló neve | bővített  |
| IoT-eszköz neve | Contoso-Test-Device |
| IoT Hub name | ContosoTestHubMsgEn |
| Tárfiók neve | contosostorage |
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

Ezen a ponton az erőforrások beállítás mentése és az Útválasztás van konfigurálva. Az üzenet-útválasztási konfigurációjának megtekintése a portálon, és állítsa be a üzenet végrehajtott információbeolvasás üzeneteket fogja a **bővített** storage-tárolóba.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Műveletterv megtekintése és az üzenet végrehajtott információbeolvasás konfigurálása

1. Nyissa meg az IoT Hub kiválasztásával **erőforráscsoportok**, majd válassza ki az erőforráscsoportot, a jelen oktatóanyag beállítása (**ContosoResources_MsgEn**). Keresse meg az IoT Hub a listában, és válassza ki azt. Válassza ki *üzenet-útválasztása** az Iot hub.

   ![Válassza ki az üzenet-útválasztása](./media/tutorial-message-enrichments/select-iot-hub.png)

   Az üzenet-útválasztási panelen három lap – rendelkezik **útvonalak**, **egyéni végpontok**, és **üzenetek bővítését**. Megkeresheti az első két lapokat a konfigurációt, a parancsfájl által létrehozott megtekintéséhez. A harmadik lap segítségével üzenet végrehajtott információbeolvasás hozzáadása. Nézzük bővítését az üzeneteket a végpontra a nevű tároló **bővített**. Adja meg a nevét és értékét, és válassza ki a végpont **ContosoStorageEndpointEnriched** a legördülő listából. Íme egy példa-felderítési bővítést, amely az IoT Hub nevét hozzáadja az üzenethez beállítása:

   ![Adja hozzá az első Adatbővítés](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adja hozzá ezeket az értékeket a ContosoStorageEndpointEnriched végpont a listához.

   | Name (Név) | Érték | Végpont (legördülő lista) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Eszközhely | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |CustomerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Ha az eszköz nem rendelkezik egy, az érték, itt adja meg a lesz megjelölve az üzenet végrehajtott információbeolvasás értékéhez karakterláncként. Az eszköz az ikereszköz információkat, nyissa meg a hubhoz a portálon, majd jelölje ki a **IoT-eszközök**, válassza ki az eszközt, és válassza **ikereszköz** az oldal tetején.
   >
   > Az ikereszköz információkat (például a hely) címkéket adhat hozzá, és állítsa egy adott értékre, ha azt szeretné, hogy szerkesztheti. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](iot-hub-devguide-device-twins.md)

3. Ha elkészült, a panelen ábrán láthatóhoz hasonlónak kell kinéznie:

   ![Hozzáadott összes végrehajtott információbeolvasás tartalmazó tábla](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Válassza ki **alkalmaz** menti a módosításokat.

## <a name="send-messages-to-the-iot-hub"></a>Üzenetek küldése az IoT Hub

Most, hogy az üzenet végrehajtott információbeolvasás a végponton, és futtassa a szimulált eszközalkalmazás üzeneteket küldeni az IoT Hub vannak konfigurálva. A hub be lett állítva a szabályokat, amelyek a következőket teszi lehetővé:

* A storage-végpont ContosoStorageEndpointOriginal küldött üzenetek nem lehet bővített, és a storage-tárolóba lesz tárolva `original`.

* A storage-végpont ContosoStorageEndpointEnriched küldött üzenetek rendszer bővített és a storage-tárolóban tárolt `enriched`.

A szimulált eszközalkalmazás az alkalmazások a kicsomagolt letöltés egyike. Az alkalmazás számára a különböző üzenet útválasztási módszer az üzeneteket küld a [útválasztási oktatóanyag](tutorial-routing.md); Ez magában foglalja az Azure Storage.

Kattintson duplán a megoldásfájlt a Visual studióban nyissa meg a kódot (IoT_SimulatedDevice.sln), majd nyissa meg a program.cs fájlban. A helyettesítő `{your hub name}` helyőrzőt az IoT hub nevére. Az IoT hub-állomásnévvel formátuma **{hub név} .azure-devices.net**. Ebben az oktatóanyagban a hub-állomásnévvel van **ContosoTestHubMsgEn.azure-devices.net**. Ezután helyettesítse `{device key}` eszközkulcs mentett korábban futtatásakor a szkript erőforrások létrehozásához.

Ha nem rendelkezik az eszköz kulcsát, kérheti a portálról. Miután bejelentkezett, lépjen **erőforráscsoportok**, jelölje ki az erőforráscsoportot, majd válassza ki az IoT hubnak. Keresse meg a **IoT-eszközök** a vizsgálati eszköz, és válassza ki az eszközt. Válassza ki a másolási ikon melletti **elsődleges kulcs** másolja a vágólapra.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Futtatás és tesztelés

Futtassa a konzolalkalmazást. Várjon néhány percet. A konzol képernyőn az alkalmazás által küldött üzeneteket jelennek meg.

Az alkalmazás másodpercenként elküld egy új üzenetet az eszközről a felhőbe az IoT Hubnak. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával, a hőmérséklettel, a páratartalommal és az üzenet szintjével, amely alapértelmezés szerint `normal`. Véletlenszerűen rendeli hozzá a szintű `critical` vagy `storage`, okozó átirányíthatók tárfiókba, vagy az alapértelmezett végpont az üzenetet. Küldött üzenetek a **bővített** fog kell bővített a storage-fiókban lévő tárolóba.

Több tároló üzenet elküldése után az adatok megtekintéséhez.

1. Válassza ki **erőforráscsoportok**, majd keresse meg az erőforráscsoport (ContosoResourcesMsgEn), és válassza ki azt.

2. Válassza ki a tárfiókot (contosostorage). Válassza ki **Tártallózó (előzetes verzió)** származó a kijelölést a bal oldali panelen.

   ![Válassza ki a storage Explorerben](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Válassza ki **BLOBTÁROLÓK** használható a két tárolók megtekintéséhez.

   ![A storage-fiókban a tárolók megtekintéséhez](./media/tutorial-message-enrichments/show-blob-containers.png)

A tároló található üzenetek nevű **bővített** az üzenetek szerepelnek, az üzenet végrehajtott információbeolvasás rendelkezik. A tároló található üzenetek nevű **eredeti** a nyers üzenetek nem végrehajtott információbeolvasás fog rendelkezni. Részletezhet egy tárolót mindaddig, amíg juthat el az alsó és nyissa meg a legutóbbi üzenet fájlt, majd végezze el ugyanezt a tároló ellenőrizze, hogy nincsenek-e nem végrehajtott információbeolvasás hozzáadott üzenetek a tárolóban.

Ha üzeneteket, amelyek gyarapítása lett, a "saját IoT Hub" a hub nevére, valamint a hely és az ügyfél-Azonosítót, ehhez hasonló kell megjelennie:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

És itt nem üzenet. "" Saját IoT Hub","Eszköz helye"és"customerID"nem jelennek meg itt, mert ez a végpont nem végrehajtott információbeolvasás.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el kívánja távolítani a ebben az oktatóanyagban létrehozott erőforrásokat, törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben azt eltávolítja, az IoT hub, a tárfiókot és magát az erőforráscsoportot.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Az Azure CLI használatával az erőforrások törlése

Az erőforráscsoport az [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítható el. `$resourceGroup` úgy állították be **ContosoResources** vissza, ez az oktatóanyag elején.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban konfigurált, és tesztelt hozzáadása üzenet végrehajtott információbeolvasás, az IoT Hub-üzenetek az alábbi lépéseket követve:

**Az IoT Hub üzenet végrehajtott információbeolvasás használatával**
> [!div class="checklist"]
> * Az Azure CLI használatával hozzon létre az erőforrások – egy IoT hubot, két enendpoints egy tárfiókot és az útválasztási konfigurációt.
> * Az Azure portal segítségével üzenet végrehajtott információbeolvasás konfigurálhatja.
> * Alkalmazás futtatása, amely szimulálja az IoT Device üzenet küldése a hubhoz.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenet végrehajtott információbeolvasás a várt módon működnek.

Üzenet végrehajtott információbeolvasás kapcsolatos további információkért lásd: a [üzenet végrehajtott információbeolvasás áttekintése](iot-hub-message-enrichments-overview.md).

Üzenet-útválasztással kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [Használja az IoT Hub üzenet-útválasztása eszköz – felhő üzeneteket küldeni a különböző végpontok](iot-hub-devguide-messages-d2c.md)

* [Oktatóanyag: Az IoT Hub-Útválasztás](tutorial-routing.md)