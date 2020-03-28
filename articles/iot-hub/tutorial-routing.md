---
title: Üzenet-útválasztás konfigurálása az Azure IoT Hub hoz az Azure CLI használatával
description: Az Azure IoT Hub üzenet-útválasztásának konfigurálása az Azure CLI és az Azure Portal használatával
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 38a40d628b883c0e7ada824d47d3fdf3d29caf93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084384"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Oktatóanyag: Az Azure CLI és az Azure Portal használatával konfigurálhatja az IoT Hub üzenetútválasztást

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Az Azure CLI segítségével hozza létre az alaperőforrásokat

Ez az oktatóanyag az Azure CLI használatával hozza létre az alaperőforrásokat, majd az [Azure Portal](https://portal.azure.com) segítségével bemutatja, hogyan konfigurálhatja az üzenet-útválasztást, és hogyan állíthatja be a virtuális eszközt tesztelésre.

Másolja és illessze be az alábbi parancsfájlt a Cloud Shell be, és nyomja le az Enter billentyűt. A parancsfájlt soronként futtatja. Ez létrehozza az alaperőforrásokat az oktatóanyaghoz, beleértve a tárfiókot, az IoT Hubot, a Service Bus-névtér és a Service Bus-várólistát.

Számos erőforrásnév, amely globálisan egyedi, például az IoT Hub nevét és a tárfiók nevét. A könnyebb ikonra, ezek az erőforrásnevek egy random alfanumerikus értékkel, a *randomValue-értékkel*vannak elfűzve. A randomValue a parancsfájl tetején egyszer jön létre, és szükség szerint hozzáfűzi az erőforrásneveket a parancsfájlban. Ha nem szeretné, hogy véletlenszerű legyen, beállíthatja egy üres karakterláncra vagy egy adott értékre.

> [!TIP]
> Tipp a hibakeresésről: ez a parancsfájl a `\`folytatási szimbólumot (a fordított perjelet) használja a parancsfájl olvashatóbbá teszi. Ha probléma merül fel a parancsfájl futtatásakor, `bash` győződjön meg arról, hogy a Cloud Shell-munkamenet fut, és hogy nincsenek szóközök a fordított perjelek után.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

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
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

```

Most, hogy az alaperőforrások be vannak állítva, konfigurálhatja az üzenet-útválasztást az [Azure Portalon.](https://portal.azure.com)

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Átirányítás tárfiókba

Most beállíthatja az útválasztást a tárfiókhoz. Lépjen az Üzenetirányítás panelre, majd adjon hozzá egy útvonalat. Az útvonal hozzáadásakor definiáljon egy új végpontot az útvonalhoz. Az útválasztás beállítása után a **szinttulajdonságot** **tárolóként** beállító üzeneteket a rendszer automatikusan egy tárfiókba írja. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. Az [Azure Portalon](https://portal.azure.com)válassza az **Erőforráscsoportok**lehetőséget, majd válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

2. Válassza ki az IoT hub ot az erőforrások listája alatt. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja.

3. Válassza **az Üzenetútválasztás**lehetőséget. Az **Üzenettovábbítás** ablaktáblán válassza a +**Hozzáadás**lehetőséget. Az **Útvonal hozzáadása** ablaktáblán válassza a +**Hozzáadás** a Végpont mező mellett jelölőnégyzetet a támogatott végpontok megjelenítéséhez, ahogy az az alábbi képen látható:

   ![Végpont hozzáadásának megkezdése egy útvonalhoz](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Válassza a **Blob Storage** lehetőséget. Ekkor megjelenik a **Tárolóvégpont hozzáadása** ablaktábla.

   ![Végpont hozzáadása](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Adja meg a végpont nevét. Ez az oktatóanyag **a ContosoStorageEndpoint programot**használja.

6. Válassza **a Tároló kiválasztása**lehetőséget. Ekkor megjelenik a saját tárfiókok listája. Válassza ki azt, amelyiket az előkészítő lépések során állított be. Ez az oktatóanyag a **contosostorage** tárfiókot használja. Ebben az ehhez a tárfiókhoz tartozó tárolókat láthatja. **Válassza ki** az előkészítési lépésekben beállított tárolót. Ez az oktatóanyag a **contosoresults** tárolót használja. Visszatér a **Tároló végpont hozzáadása** ablaktáblához, és láthatja a kijelölt beállításokat.

7. Állítsa a kódolást AVRO vagy JSON. Ebben az oktatóanyagban a többi mező esetében az alapértelmezett értékeket fogja használni. Ez a mező szürkén jelenik meg, ha a kiválasztott régió nem támogatja a JSON kódolást.,

   > [!NOTE]
   > A blob nevének formátumát a **Blob fájlnévformátuma** mezőben adhatja meg. A mező alapértelmezett értéke: `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. A formátumnak tetszőleges sorrendben tartalmaznia kell a következőket: {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} és {mm}.
   >
   > Ha például az alapértelmezett blobfájlnév-formátumot használja egy ContosoTestHub nevű központ esetében, és a dátum/idő érték 2018. október 30., 10:56, akkor a blob neve a következő lesz: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > A blobok írása Avro formátumban történik.
   >

8. Válassza a **Létrehozás** lehetőséget a tárolási végpont létrehozásához és az útvonalhoz való hozzáadásához. Ekkor visszakerül az **Útvonal hozzáadása** panelre.

9. Töltse ki az útválasztási lekérdezés hiányzó adatait. Ez a lekérdezés megszabja az üzenetek az imént végpontként hozzáadott tárolóhoz való irányításának feltételeit. Töltse ki a képernyőn látható mezőket.

   **Név**: Adja meg az útválasztási lekérdezés nevét. Ez az oktatóanyag **a ContosoStorageRoute.this tutorial uses ContosoStorageRoute.**

   **Végpont**: Az imént beállított végpontot jeleníti meg.

   **Adatforrás**: A legördülő listából válassza az **Eszköztelemetria-üzenetek** elemet.

   **Útvonal engedélyezése**: Győződjön meg `enabled`arról, hogy ez a mező a.
   
   **Útválasztási lekérdezés**: Lekérdezési sztringként írja be a következőt: `level="storage"`.

   ![Útválasztási lekérdezés létrehozása a tárfiókhoz](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Kattintson a **Mentés** gombra. A befejezés után a rendszer visszalép az Üzenetirányítás panelre, ahol láthatja a tároló új útválasztási lekérdezését. Zárja be az Útvonalak panelt, ezzel visszatér az Erőforráscsoport oldalra.

### <a name="route-to-a-service-bus-queue"></a>Útvonal a Service Bus várólistájára

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. Lépjen az Üzenetirányítás panelre, majd adjon hozzá egy útvonalat. Az útvonal hozzáadásakor definiáljon egy új végpontot az útvonalhoz. Az útvonal beállítása után a **szinttulajdonság** **kritikusra** beállított üzeneteket a Service Bus-várólistába írják, amely elindítja a logikai alkalmazást, amely ezután e-mailt küld az adatokkal.

1. Az Erőforráscsoport lapon jelölje ki az IoT-központot, majd válassza **az Üzenetküldés**lehetőséget.

2. Az **Üzenettovábbítás** ablaktáblán válassza a +**Hozzáadás**lehetőséget.

3. Az **Útvonal hozzáadása** ablaktáblán válassza a +**Hozzáadás** lehetőséget a Végpont mező mellett. Válasszon egy **Service Bus-üzenetsort**. Ekkor megjelenik a **Service Bus-végpont hozzáadása** panel.

   ![Szolgáltatásbusz-végpont hozzáadása](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Töltse ki a mezőket:

   **Végpont neve**: Adja meg a végpont nevét. Ez az oktatóanyag **a ContosoSBQueueEndpoint programot**használja.
   
   **Service Bus Namespace**: A legördülő lista segítségével válassza ki a szolgáltatási busz névterében beállított az előkészítési lépéseket. Ez az oktatóanyag a **ContosoSBNamespace** névteret használja.

   **Service Bus várólista:** A legördülő lista segítségével válassza ki a Service Bus várólistát. Ez az oktatóanyag a **contososbqueue** üzenetsort használja.

5. A Service Bus várólista-végpontjának hozzáadásához válassza a **Létrehozás** lehetőséget. Ekkor visszakerül az **Útvonal hozzáadása** panelre.

6. Töltse ki az útválasztási lekérdezés hiányzó adatait. Ez a lekérdezés megszabja az üzenetek az imént végpontként hozzáadott Service Bus-üzenetsorhoz való irányításának feltételeit. Töltse ki a képernyőn látható mezőket. 

   **Név**: Adja meg az útválasztási lekérdezés nevét. Ez az oktatóanyag **a ContosoSBQueueRoute.this tutorial uses ContosoSBQueueRoute.** 

   **Végpont**: Az imént beállított végpontot jeleníti meg.

   **Adatforrás**: A legördülő listából válassza az **Eszköztelemetria-üzenetek** elemet.

   **Útválasztási lekérdezés**: Lekérdezési sztringként írja be a következőt: `level="critical"`. 

   ![Útválasztási lekérdezés létrehozása a Service Bus várólistához](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Kattintson a **Mentés** gombra. Miután visszatért az Útvonalak panelre, láthatja a két új útvonalat, ahogy itt is szerepelnek.

   ![Az imént beállított útvonalak](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Az egyéni végpontok beállításával megtekintheti a beállított egyéni végpontokat az **Egyéni végpontok** fül kiválasztásával.

   ![Az imént beállított egyéni végpont](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zárja be az Üzenetirányítás panelt, ezzel visszatér az Erőforráscsoport panelre.

## <a name="create-a-simulated-device"></a>Szimulált eszköz létrehozása

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>További lépések

Most, hogy beállította az erőforrásokat, és az üzenetútvonalak konfigurálva, előre a következő oktatóanyag, hogy megtanulják, hogyan küldhet üzeneteket az IoT hub, és látni őket a különböző célhelyekre. 

> [!div class="nextstepaction"]
> [2. rész – Az üzenetút-tervezés eredményeinek megtekintése](tutorial-routing-view-message-routing-results.md)
