---
title: Az Azure CLI és az Azure portal használata az Azure IoT Hub üzenet útválasztásának konfigurálása |} A Microsoft Docs
description: Az Azure CLI és az Azure portal használata az Azure IoT Hub üzenet útválasztásának konfigurálása
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 2f382c31c6bfb6ab71afd495c4c3f702715633c0
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661888"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Oktatóanyag: Az Azure CLI-vel és az Azure portal segítségével konfigurálhatja az IoT Hub üzenet-útválasztása

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Az alap-erőforrások létrehozása az Azure CLI használatával

Ez az oktatóanyag az Azure CLI használatával az alapvető erőforrások létrehozása, majd használja a [az Azure portal](https://portal.azure.com) megmutatjuk hogyan konfigurálja az útválasztást üzenet, és állítsa be a virtuális eszközt a teszteléshez.

Nincsenek több erőforrás nevét, például az IoT Hub nevét és a tárfiók nevének globálisan egyedinek kell lennie. Az eljárás egyszerűbbé tétele az erőforráscsoport nevére kiegészítve egy véletlenszerű alfanumerikus érték nevű *randomValue*. A randomValue egyszer jön létre, a szkript elején, és hozzáfűzi az erőforrások nevei a parancsfájl egész igény szerint. Ha nem szeretné, hogy a véletlenszerű, beállíthatja azt egy adott értékre vagy üres karakterlánc.

Másolja és illessze be az alábbi parancsfájlt a Cloud shellbe, és nyomja le az Enter billentyűt. A szkript egy sor fut egyszerre. Ekkor létrejön az alapvető erőforrások ebben az oktatóanyagban, beleértve a storage-fiók, az IoT Hub, a Service Bus-Namespace és a Service Bus-üzenetsorba.

Hibakeresés kapcsolatos megjegyzés: Ez a szkript a folytatási szimbólum (fordított perjel `\`) olvashatóbbá a parancsfájlt. Ha a probléma, a szkript futtatása, győződjön meg arról, ne legyenek szóközök után bármely a perjelek szerepeljenek.

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing rules.
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

Most, hogy az alapvető erőforrások vannak beállítva, az üzenet útválasztás konfigurálhatja a [az Azure portal](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Átirányítása a tárfiókba

Most beállíthatja az útválasztást a tárfiókhoz. Lépjen az Üzenetirányítás panelre, majd adjon hozzá egy útvonalat. Az útvonal hozzáadásakor definiáljon egy új végpontot az útvonalhoz. Az Útválasztás be van állítva, miután üzenetekre, hol a **szint** tulajdonsága **tárolási** írt storage-fiók automatikusan. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. Az a [az Azure portal](https://portal.azure.com)válassza **erőforráscsoportok**, majd jelölje ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

2. Válassza ki az IoT hub-erőforrások listája alatt. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja.

3. Válassza ki **üzenet-útválasztása**. Az a **üzenet-útválasztása** ablaktáblán válassza előbb +**Hozzáadás**. Az a **adjon hozzá egy útvonalat** ablaktáblán válassza +**Hozzáadás** mellett a végpont mező megjelenítéséhez a támogatott végpontok, ahogyan a következő képen látható:

   ![Útvonal a végpont hozzáadásának elkezdése](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Válassza a **Blob Storage** lehetőséget. Megjelenik a **egy storage-végpont hozzáadása** ablaktáblán.

   ![A végpont hozzáadása](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Adja meg a végpont nevét. Ebben az oktatóanyagban **ContosoStorageEndpoint**.

6. Válassza ki **tároló kiválasztása**. Ekkor megjelenik a saját tárfiókok listája. Válassza ki azt, amelyiket az előkészítő lépések során állított be. Ez az oktatóanyag a **contosostorage** tárfiókot használja. Ebben az ehhez a tárfiókhoz tartozó tárolókat láthatja. **Válassza ki** a tároló lehet beállítani az előkészítő lépéseket. Ez az oktatóanyag a **contosoresults** tárolót használja. Vissza a **egy storage-végpont hozzáadása** ablaktáblán, és tekintse meg a kiválasztott beállítások.

7. Állítsa be a kódolás AVRO vagy JSON-ként. Ebben az oktatóanyagban a többi mező esetében az alapértelmezett értékeket fogja használni. Ez a mező szürkén jelennek meg, ha a kiválasztott régió nem támogatja a JSON-kódolást.,

   > [!NOTE]
   > A blob nevének formátumát a **Blob fájlnévformátuma** mezőben adhatja meg. A mező alapértelmezett értéke: `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. A formátumnak tetszőleges sorrendben tartalmaznia kell a következőket: {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} és {mm}.
   >
   > Ha például az alapértelmezett blobfájlnév-formátumot használja egy ContosoTestHub nevű központ esetében, és a dátum/idő érték 2018. október 30., 10:56, akkor a blob neve a következő lesz: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > A blobok írása Avro formátumban történik.
   >

8. Válassza ki **létrehozás** hozhat létre a storage-végpont, és adja hozzá az útvonalat. Ekkor visszakerül az **Útvonal hozzáadása** panelre.

9. Töltse ki az útválasztási lekérdezés hiányzó adatait. Ez a lekérdezés megszabja az üzenetek az imént végpontként hozzáadott tárolóhoz való irányításának feltételeit. Töltse ki a képernyőn látható mezőket.

   **Név**: Adjon meg egy nevet az útválasztási lekérdezés. Ebben az oktatóanyagban **ContosoStorageRoute**.

   **Végpont**: Ez látható az imént beállított végpont.

   **Az adatforrás**: Válassza ki **eszköz Telemetriai üzeneteket** a legördülő listából.

   **Engedélyezze az útvonal**: Győződjön meg a mező értéke `enabled`.
   
   **Útválasztási lekérdezés**: Adja meg `level="storage"` a lekérdezési karakterlánc formájában.

   ![A storage-fiókhoz tartozó útválasztási lekérdezés létrehozása](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Kattintson a **Mentés** gombra. A befejezés után a rendszer visszalép az Üzenetirányítás panelre, ahol láthatja a tároló új útválasztási lekérdezését. Zárja be az Útvonalak panelt, ezzel visszatér az Erőforráscsoport oldalra.

### <a name="route-to-a-service-bus-queue"></a>Irányíthatja a Service Bus-üzenetsorba

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. Lépjen az Üzenetirányítás panelre, majd adjon hozzá egy útvonalat. Az útvonal hozzáadásakor definiáljon egy új végpontot az útvonalhoz. Után ez az útvonal be van állítva, üzenetekre, ahol a **szint** tulajdonsága **kritikus** kerüljenek a Service Bus-üzenetsorba, amely elindítja a logikai alkalmazást, amely ezután elküldi az adatokat tartalmazó e-mail küldése.

1. Az erőforráscsoport lapján, válassza ki az IoT hubhoz, majd válassza ki **üzenet-útválasztása**.

2. Az a **üzenet-útválasztása** ablaktáblán válassza előbb +**Hozzáadás**.

3. Az a **adjon hozzá egy útvonalat** ablaktáblán válassza ki az +**Hozzáadás** végpontot mezőtől mellett. Válasszon egy **Service Bus-üzenetsort**. Ekkor megjelenik a **Service Bus-végpont hozzáadása** panel.

   ![A service bus-végpont hozzáadása](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Töltse ki a mezőket:

   **Végpont neve**: Adja meg a végpont nevét. Ebben az oktatóanyagban **ContosoSBQueueEndpoint**.
   
   **Service Bus-Namespace**: A legördülő lista segítségével válassza ki a service bus-névtér az előkészítő lépésben állítsa be. Ez az oktatóanyag a **ContosoSBNamespace** névteret használja.

   **Service Bus-üzenetsorba**: A legördülő lista segítségével válassza ki a Service Bus-üzenetsorba. Ez az oktatóanyag a **contososbqueue** üzenetsort használja.

5. Válassza ki **létrehozás** hozzáadása a Service Bus-üzenetsor végpont. Ekkor visszakerül az **Útvonal hozzáadása** panelre.

6. Töltse ki az útválasztási lekérdezés hiányzó adatait. Ez a lekérdezés megszabja az üzenetek az imént végpontként hozzáadott Service Bus-üzenetsorhoz való irányításának feltételeit. Töltse ki a képernyőn látható mezőket. 

   **Név**: Adjon meg egy nevet az útválasztási lekérdezés. Ebben az oktatóanyagban **ContosoSBQueueRoute**. 

   **Végpont**: Ez látható az imént beállított végpont.

   **Az adatforrás**: Válassza ki **eszköz Telemetriai üzeneteket** a legördülő listából.

   **Útválasztási lekérdezés**: Adja meg `level="critical"` a lekérdezési karakterlánc formájában. 

   ![Hozzon létre egy útválasztási lekérdezést a Service Bus-üzenetsorba](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Kattintson a **Mentés** gombra. Miután visszatért az Útvonalak panelre, láthatja a két új útvonalat, ahogy itt is szerepelnek.

   ![Az imént beállított útvonalak](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Az egyéni végpontok beállítása kiválasztásával megtekintheti a **egyéni végpontok** fülre.

   ![Az imént beállított egyéni végpont](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zárja be az Üzenetirányítás panelt, ezzel visszatér az Erőforráscsoport panelre.

## <a name="create-a-simulated-device"></a>Szimulált eszköz létrehozása

[!INCLUDE [iot-hub-include-create- imulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik az erőforrások beállítása és az üzenetútvonalak konfigurálva, folytassa a következő oktatóanyag üzenetek küldése az IoT hub és azok a másik helyre lesznek irányítva. 

> [!div class="nextstepaction"]
> [2. rész – az üzenet-útválasztási eredmények megtekintése](tutorial-routing-view-message-routing-results.md)
