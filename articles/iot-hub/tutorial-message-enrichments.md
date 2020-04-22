---
title: Oktatóanyag – Az Azure IoT Hub üzenetgazdagításának használata
description: Oktatóanyag az Azure IoT Hub-üzenetek üzenetgazdagításának használatáról
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 78aee7829e58feede3360f30f10260903713c52f
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770086"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Oktatóanyag: Az Azure IoT Hub üzenetbővítéseinek használata

*Az üzenetek gazdagítása* azt ismerteti, hogy az Azure IoT Hub képes-e további adatokkal *lebélyegezni* az üzeneteket, mielőtt az üzeneteket a kijelölt végpontra küldené. Az üzenetek bővítésének egyik oka az, hogy olyan adatokat is tartalmaz, amelyek egyszerűsíthetik az alsóbb rétegbeli feldolgozást. Például az eszköz telemetriai üzenetek egy eszköz ikercímke csökkentheti a terhelést az ügyfelek számára, hogy az eszköz iker API-hívások ezt az információt. További információt az [Üzenetgazdagítások áttekintése](iot-hub-message-enrichments-overview.md)című témakörben talál.

Ebben az oktatóanyagban két módon hozhat létre és konfigurálhatja az okat, amelyek az IoT-központ üzenetbővítéseinek teszteléséhez szükségesek. Az erőforrások közé tartozik egy tárfiók két tárolótárolók. Az egyik tároló tartalmazza a bővített üzeneteket, egy másik pedig az eredeti üzeneteket. Is szerepel egy IoT hub az üzenetek fogadására, és továbbítja azokat a megfelelő tárolótároló alapján, hogy azok gazdagítva vannak-e vagy sem.

* Az első módszer az Azure CLI használatával hozza létre az erőforrásokat, és konfigurálja az üzenet-útválasztást. Ezután manuálisan definiálja a dúsításokat az [Azure Portal](https://portal.azure.com)használatával.

* A második módszer egy Azure Resource Manager-sablon használatával hozza létre az erőforrásokat és az üzenet-útválasztást *és* az üzenetbővítéseket.

Miután az üzenet-útválasztás és az üzenetbővítés ikonfigurációi befejeződtek, egy alkalmazás használatával küldhet üzeneteket az IoT hubnak. A hub ezután mindkét tárolótárolóba irányítja őket. Csak a **dúsított** tároló végpontjára küldött üzenetek dúsítva vannak.

Az oktatóanyag befejezéséhez az alábbiakat hajthatja végre:

**Az IoT Hub-üzenetek bővítésének használata**
> [!div class="checklist"]
> * Első módszer: Erőforrások létrehozása és az üzenetek útválasztásának konfigurálása az Azure CLI használatával. Az Azure [Portal](https://portal.azure.com)használatával manuálisan konfigurálja az üzenetbővítéseket.
> * Második módszer: Erőforrások létrehozása és az üzenet-útválasztás és az üzenetbővítés konfigurálása erőforrás-kezelő sablon használatával. 
> * Futtasson egy olyan alkalmazást, amely szimulálja az IoT-eszközt, amely üzeneteket küld a központnak.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenetbővítés a várt módon működik-e.

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
* A [Visual Studio](https://www.visualstudio.com/) telepítése.

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. Az oktatóanyagban szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Az IoT C# mintatárház lekérése

Töltse le az [IoT C# mintákat](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) a GitHubról, és csomagolja ki őket. Ez a tárház több alkalmazással, parancsfájllal és Erőforrás-kezelő sablonnal rendelkezik. Az oktatóanyaghoz a következők:

* A manuális módszer, van egy CLI-parancsfájl, amely az erőforrások létrehozásához használt. Ez a parancsfájl az /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli nyelven található. Ez a parancsfájl létrehozza az erőforrásokat, és konfigurálja az üzenet-útválasztást. A parancsfájl futtatása után manuálisan hozza létre az üzenetbővítéseket az [Azure Portal](https://portal.azure.com)használatával.
* Az automatikus módszer hez tartozik egy Azure Resource Manager-sablon. A sablon az /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json nyelven található. Ez a sablon létrehozza az erőforrásokat, konfigurálja az üzenet-útválasztást, majd konfigurálja az üzenetbővítéseket.
* A harmadik alkalmazás használata az Eszközszimuláció alkalmazás, amellyel üzeneteket küldhet az IoT hubnak, és tesztelheti az üzenetbővítéseket.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Manuális beállítás és konfigurálás az Azure CLI használatával

A szükséges erőforrások létrehozása mellett az Azure CLI-parancsfájl is konfigurálja a két útvonalat a végpontok, amelyek külön tárolótárolók. Az üzenetútválasztás konfigurálásáról az [Útválasztás oktatóanyagban](tutorial-routing.md)talál további információt. Az erőforrások beállítása után az [Azure Portal](https://portal.azure.com) használatával konfigurálja az egyes végpontok üzenetbővítési. Ezután folytassa a tesztelési lépéssel.

> [!NOTE]
> Az összes üzenet mindkét végpontra lesz irányítva, de csak a végpontra, konfigurált üzenetbővítéssel rendelkező üzenetek lesznek gazdagítva.
>

Használhatja a következő parancsfájlt, vagy megnyithatja a parancsfájlt a letöltött tárház /resources mappájában. A szkript a következő lépéseket hajtja végre:

* Hozzon létre egy IoT hubot.
* Tárfiók létrehozása.
* Hozzon létre két tárolót a tárfiókban. Az egyik tároló a bővített üzenetekhez, a másik pedig a nem bővített üzenetekhez.
* Műveletterv beállítása a két különböző tárfiókhoz:
    * Hozzon létre egy végpontot minden tárfiók-tárolóhoz.
    * Hozzon létre egy útvonalat az egyes tárfiók tárolóvégpontok.

Számos erőforrásnév, amely globálisan egyedi, például az IoT hub nevét és a tárfiók nevét. A parancsfájl futtatásának megkönnyítése érdekében ezek az erőforrásnevek egy random alfanumerikus értékkel, a *randomValue-értékkel*vannak elfűzve. A véletlenszerű érték egyszer jön létre a parancsfájl tetején. Szükség szerint hozzáfűzi az erőforrásneveket a parancsfájlban. Ha nem szeretné, hogy az érték véletlenszerű legyen, beállíthatja azt egy üres karakterláncra vagy egy adott értékre.

Ha még nem tette meg, nyisson meg egy Azure [Cloud Shell-ablakot,](https://shell.azure.com) és győződjön meg arról, hogy bash-re van állítva. Nyissa meg a parancsfájlt a kibontott tárházban, az összes kijelöléséhez válassza a Ctrl+A billentyűkombinációt, majd másolja a Ctrl+C billentyűkombinációt. Másik lehetőségként másolhatja a következő CLI-parancsfájlt, vagy közvetlenül a Cloud Shellben is megnyithatja. Illessze be a parancsfájlt a Cloud Shell ablakába úgy, hogy a jobb gombbal a parancssorra kattint, és a Beillesztés parancsot **választja.** A parancsfájl egyszerre csak egy utasítást futtat. Miután a parancsfájl futása leáll, válassza az **Enter** lehetőséget, hogy biztosan futjon az utolsó parancs. A következő kódblokk a használt parancsfájlt mutatja, megjegyzésekkel, amelyek elmagyarázzák, hogy mit csinál.

Itt vannak a parancsfájl által létrehozott erőforrások. *A bővített* azt jelenti, hogy az erőforrás gazdagítású üzenetekhez tartozik. *Az eredeti* azt jelenti, hogy az erőforrás nem bővített üzenetekhez tartozik.

| Name (Név) | Érték |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| tároló neve | Eredeti  |
| tároló neve | Dúsított  |
| IoT-eszköz neve | Contoso-teszt-eszköz |
| IoT Hub neve | ContosoTestHubMsgEn |
| tárfiók neve | contosostorage (contosostorage) |
| végpont neve 1 | ContosoStorageEndpointOriginal |
| végpont neve 2 | ContosoStorageEndpointEnriched|
| útvonal neve 1 | ContosoStorageRouteOriginal |
| útvonal neve 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

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
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

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

# This is the endpoint for the first container, for endpoint messages that are not enriched.
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

# This is the endpoint for the second container, for endpoint messages that are enriched.
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

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Ezen a ponton az erőforrások be vannak állítva, és az üzenet-útválasztás is be van állítva. Megtekintheti az üzenet-útválasztási konfigurációt a portálon, és beállíthatja a **bővített** tárolóba kerülő üzenetek üzenetgazdagítását.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Az üzenetbővítésmanuális konfigurálása az Azure Portal használatával

1. Nyissa meg az IoT-központot **az Erőforráscsoportok**kiválasztásával. Ezután válassza ki az oktatóanyaghoz beállított erőforráscsoportot (**ContosoResourcesMsgEn**). Keresse meg az IoT hubot a listában, és jelölje ki. Válassza **az Üzenet-útválasztás** az IoT hub üzenetküldés e lehetőséget.

   ![Üzenet-útválasztás kiválasztása](./media/tutorial-message-enrichments/select-iot-hub.png)

   Az üzenettovábbítás ablaktáblán **három, Útvonalak**, **Egyéni végpontok**és **Üzenetek gazdagítása feliratú**lap található. Tallózzon az első két lapon a parancsfájl által beállított konfiguráció megtekintéséhez. A harmadik lapon üzenetbővítéseket adhat hozzá. Gazdagítsuk az üzeneteket a **bővített**tároló végpontjára irányuló üzenetekben. Töltse ki a nevet és az értéket, majd válassza ki a **ContosoStorageEndpointEnriched** végpontot a legördülő listából. Íme egy példa arra, hogyan állíthat be egy olyan dúsítást, amely hozzáadja az IoT hub nevét az üzenethez:

   ![Első dúsítás hozzáadása](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adja hozzá ezeket az értékeket a ContosoStorageEndpointEnriched végpont listájához.

   | Kulcs | Érték | Végpont (legördülő lista) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Eszközelhelyezkedése | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Vevőkód | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Ha az eszköz nem rendelkezik ikertestvérrel, az itt behelyezett érték az üzenet gazdagítási értékének karakterláncaként lesz lepecsételve. Az ikereszköz-adatok megtekintéséhez nyissa meg a hubot a portálon, és válassza az **IoT-eszközök**lehetőséget. Válassza ki az eszközt, majd a lap tetején válassza az **Ikereszköz** lehetőséget.
   >
   > Az ikeradatok szerkesztésével címkéket, például helyet adhat hozzá, és beállíthatja őket egy adott értékre. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](iot-hub-devguide-device-twins.md).

3. Ha végzett, az ablaktáblának ehhez a képhez hasonlóan kell kinéznie:

   ![Táblázat az összes hozzáadott dúsítással](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. A módosítások mentéséhez válassza az **Alkalmaz** lehetőséget. Ugrás a [Tesztüzenetek gazdagítása](#test-message-enrichments) szakaszra.

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Létrehozás és konfigurálás Erőforrás-kezelő sablon használatával
Az Erőforrás-kezelő sablonnal létrehozhatja és konfigurálhatja az erőforrásokat, az üzenet-útválasztást és az üzenetbővítéseket.

1. Jelentkezzen be az Azure portálra. Válassza **a + Erőforrás létrehozása lehetőséget** a keresőmező létrehozásához. Írja be *a sablon központi telepítését*, és keresse meg. Az eredmények ablaktáblán válassza **a Sablon központi telepítése (üzembe helyezés egyéni sablon használatával)** lehetőséget.

   ![Sablon üzembe helyezése az Azure Portalon](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Válassza a **Létrehozás gombot** a **Sablon központi telepítésének** ablaktábláján.

1. Az **Egyéni központi telepítés** ablaktáblában válassza **a Saját sablon létrehozása lehetőséget a szerkesztőben.**

1. A **Sablon szerkesztése** ablaktáblán válassza a **Fájl betöltése**lehetőséget. Megjelenik a Windows Intéző. Keresse meg a **template_messageenrichments.json** fájlt a **/iot-hub/Tutorials/Routing/SimulatedDevice/resources fájlban**a kibontott repofájlban. 

   ![Sablon kiválasztása a helyi gépről](./media/tutorial-message-enrichments/template-select.png)

1. Válassza a **Megnyitás** lehetőséget a sablonfájl helyi számítógépről való betöltéséhez. Betöltődik, és megjelenik a szerkesztési ablaktáblán.

   Ez a sablon úgy van beállítva, hogy egy globálisan egyedi IoT hub nevet és tárfiók nevét használja azáltal, hogy véletlenszerű értéket ad hozzá az alapértelmezett nevek végéhez, így a sablont anélkül használhatja, hogy módosítaná.

   Az alábbiakban a sablon betöltésével létrehozott erőforrásokat kell létrehozni. **A bővített** azt jelenti, hogy az erőforrás gazdagítású üzenetekhez tartozik. **Az eredeti** azt jelenti, hogy az erőforrás nem bővített üzenetekhez tartozik. Ezek ugyanazok az Azure CLI-parancsfájlban használt értékek.

   | Name (Név) | Érték |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | tároló neve | Eredeti  |
   | tároló neve | Dúsított  |
   | IoT-eszköz neve | Contoso-teszt-eszköz |
   | IoT Hub neve | ContosoTestHubMsgEn |
   | tárfiók neve | contosostorage (contosostorage) |
   | végpont neve 1 | ContosoStorageEndpointOriginal |
   | végpont neve 2 | ContosoStorageEndpointEnriched|
   | útvonal neve 1 | ContosoStorageRouteOriginal |
   | útvonal neve 2 | ContosoStorageRouteEnriched |

1. Kattintson a **Mentés** gombra. Megjelenik **az Egyéni központi telepítés** ablaktábla, amely a sablon által használt összes paramétert megjeleníti. Az egyetlen mező, amelyet be kell állítania, az **Erőforráscsoport**. Hozzon létre egy újat, vagy válasszon egyet a legördülő listából.

   Az **egyéni központi telepítés** ablaktábla felső fele itt található. Láthatja, hogy hol tölti ki az erőforráscsoportot.

   ![Az Egyéni központi telepítés ablaktábla felső fele](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Itt az egyéni központi **telepítés** ablaktábla alsó fele. Láthatja a többi paramétert és a feltételeket. 

   ![Az Egyéni központi telepítés ablaktábla alsó fele](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Jelölje be a jelölőnégyzetet, ha el szeretné fogadni a feltételeket. Ezután válassza **a Vásárlás** lehetőséget a sablon központi telepítésének folytatásához.

1. Várja meg, amíg a sablon teljes mértékben telepítve lesz. A képernyő tetején található csengő ikonra választva ellenőrizheti a folyamatot. Ha elkészült, folytassa az [Üzenet bővítési szakaszát.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>A tesztüzenetek gazdagítása

Az üzenet bővítési identőelemének megtekintéséhez válassza az **Erőforráscsoportok**lehetőséget. Ezután válassza ki az oktatóanyaghoz használt erőforráscsoportot. Válassza ki az IoT-központot az erőforrások listájából, és nyissa meg az **Üzenetek lehetőséget.** Megjelenik az üzenetútválasztási konfiguráció és a konfigurált dúsítások.

Most, hogy az üzenetbővítési konfigurálása a végponthoz, futtassa a szimulált eszköz alkalmazást üzenetek küldéséhez az IoT hub. A hub a következő feladatokat elvégezhető beállításokkal lett beállítva:

* A ContosoStorageEndpointOriginal tárolóvégpontra átirányított üzenetek nem lesznek gazdagítva, és a `original`tárolótárolóban tárolódnak.

* A ContosoStorageEndpointEnriched tárolóvégpontra átirányított üzenetek gazdagodnak és a `enriched`tárolótárolóban tárolódnak.

A szimulált eszköz alkalmazás az egyik alkalmazás a kibontott letöltés. Az alkalmazás üzeneteket küld az egyes különböző üzenet-útválasztási módszerek az [Útválasztási oktatóanyag](tutorial-routing.md), amely magában foglalja az Azure Storage.

Kattintson duplán a **megoldásfájlra IoT_SimulatedDevice.sln fájlra** a kód Visual Studio programban való megnyitásához, majd nyissa meg **a Program.cs**. Helyettesítse az IoT `{your hub name}`hub nevét a jelölővel. Az IoT hub állomásnevének formátuma **{a hub neve}.azure-devices.net.** Ebben az oktatóanyagban a központi állomás neve ContosoTestHubMsgEn.azure-devices.net. Ezután helyettesítse a parancsfájl futtatásákor korábban mentett eszközkulcsot `{your device key}`a jelölő erőforrásainak létrehozásához.

Ha nem rendelkezik az eszközkulccsal, lekérheti azt a portálról. Miután bejelentkezett, nyissa meg **az Erőforráscsoportok**, válassza ki az erőforráscsoportot, és válassza ki az IoT hub. Keresse meg a teszteszköz **IoT-eszközök alatt,** és válassza ki az eszközt. Az **Elsődleges billentyű** melletti másolásikonra a vágólapra való másoláshoz.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Futtatás és tesztelés

Futtassa a konzolalkalmazást néhány percig. Az elküldött üzenetek az alkalmazás konzolképernyőjén jelennek meg.

Az alkalmazás másodpercenként elküld egy új üzenetet az eszközről a felhőbe az IoT Hubnak. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával, a hőmérséklettel, a páratartalommal és az üzenet szintjével, amely alapértelmezés szerint `normal`. Véletlenszerűen hozzárendeli a `critical` `storage`vagyonszintjét, amely az üzenetet a tárfiókba vagy az alapértelmezett végpontra irányítja. A **tárfiókban** a bővített tárolóba küldött üzenetek dúsítva lesznek.

Több tárolási üzenet elküldése után tekintse meg az adatokat.

1. Válassza az **Erőforráscsoportok** lehetőséget. Keresse meg az erőforráscsoportot, **contosoResourcesMsgEn**, és jelölje ki.

2. Válassza ki a tárfiókot, amely **contosostorage.** Ezután válassza a **Tárolókezelő (előnézet)** lehetőséget a bal oldali ablaktáblában.

   ![Tárolókezelő kiválasztása](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Válassza ki a **BLOB-TÁROLÓK** a két használható tároló megtekintéséhez.

   ![Tekintse meg a tárolókat a tárfiókban](./media/tutorial-message-enrichments/show-blob-containers.png)

A **bővített** tárolóban lévő üzenetek az üzenetekben megtalálhatók az üzenetekben. Az **eredetinek** nevezett tárolóban lévő üzenetek a nyers üzeneteket dúsítás nélkül kapják. Részletezze az egyik tárolót, amíg az aljára nem jut, és nyissa meg a legutóbbi üzenetfájlt. Ezután ugyanezt a másik tárolót annak ellenőrzésére, hogy nincsenek-e a tárolóban lévő üzenetekhez hozzáadott dúsítások.

Ha megnézi a bővített üzeneteket, a központ nevével, helyével és ügyfélazonosítójával együtt az "én IoT Hub" jelenik meg, például:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Itt egy meg nem gazdagított üzenet. Figyelje meg, hogy az "én IoT Hub", "devicelocation" és a "customerID" nem jelenik meg itt, mert ezeket a mezőket a dúsítások adnak hozzá. Ez a végpont nem rendelkezik dúsítások.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagban létrehozott összes erőforrás eltávolításához törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT hubot, a tárfiókot és magát az erőforráscsoportot.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Az Azure CLI használatával megtisztítja az erőforrásokat

Az erőforráscsoport az [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítható el. Visszahívás, `$resourceGroup` hogy volt beállítva **ContosoResourcesMsgEn** elején ez az oktatóanyag.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az alábbi lépésekkel konfigurálta és tesztelte az Üzenetek dúsítását az IoT Hub-üzenetekhez:

**Az IoT Hub-üzenetek bővítésének használata**
> [!div class="checklist"]
> * Első módszer: Erőforrások létrehozása és az üzenetek útválasztásának konfigurálása az Azure CLI használatával. Az Azure [Portal](https://portal.azure.com)használatával manuálisan konfigurálja az üzenetbővítéseket.
> * Második módszer: Erőforrások létrehozása és az üzenetek útválasztásának és üzenetbővítéseinek konfigurálása egy Azure Resource Manager-sablon használatával.
> * Futtasson egy olyan alkalmazást, amely szimulálja az IoT-eszközt, amely üzeneteket küld a központnak.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenetbővítés a várt módon működik-e.

Az üzenetek gazdagításáról az [Üzenetgazdagítások áttekintése című](iot-hub-message-enrichments-overview.md)témakörben olvashat bővebben.

Az üzenetek útválasztásáról az alábbi cikkekben talál további információt:

* [Az IoT Hub üzenet-útválasztásának használata eszközről felhőbe irányuló üzenetek küldésére különböző végpontokra](iot-hub-devguide-messages-d2c.md)
* [Oktatóanyag: IoT Hub útválasztás](tutorial-routing.md)
