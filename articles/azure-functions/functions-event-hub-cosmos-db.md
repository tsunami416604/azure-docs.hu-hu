---
title: 'Oktatóanyag: Java-függvények használata Azure Cosmos DB és Event Hubs'
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Event Hubs-ből származó eseményeket, hogy a Java-ban írt függvények használatával Azure Cosmos DB frissítéseket.
author: KarlErickson
manager: barbkess
ms.service: azure-functions
ms.devlang: java
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: 172dda0a79620431d41a5be7e872b6b1507a6c81
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608707"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Oktatóanyag: függvény létrehozása javában Event hub-eseményindítóval és Azure Cosmos DB kimeneti kötéssel

Ez az oktatóanyag bemutatja, hogyan használható a Azure Functions egy olyan Java-függvény létrehozásához, amely folyamatos hőmérséklet-és adatterhelési adatátvitelt elemez. Az érzékelő beolvasását jelképező Event hub-események aktiválja a függvényt. A függvény feldolgozza az eseményre vonatkozó adatokat, majd hozzáadja az állapot-bejegyzéseket egy Azure Cosmos DBhoz.

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Azure-erőforrások létrehozása és konfigurálása az Azure CLI használatával.
> * Az ezekkel az erőforrásokkal kommunikáló Java-függvények létrehozása és tesztelése.
> * A függvények üzembe helyezése az Azure-ban és a Application Insights figyelése.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőket kell telepíteni:

* [Java Developer Kit](https://aka.ms/azure-jdks), 8-as verzió
* [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió
* Ha inkább nem kívánja használni az [Azure CLI](/cli/azure/install-azure-cli) -t Cloud Shell
* [Azure functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) 2.6.666 vagy újabb verzió

> [!IMPORTANT]
> Az oktatóanyag befejezéséhez a `JAVA_HOME` környezeti változót a JDK telepítési helyére kell beállítani.

Ha közvetlenül az oktatóanyag kódját szeretné használni, tekintse meg a következőt: [Java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) Sample repo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Ebben az oktatóanyagban szüksége lesz ezekre az erőforrásokra:

* A többi erőforrást tartalmazó erőforráscsoport
* Event Hubs névtér, Event hub és engedélyezési szabály
* Egy Cosmos DB fiók, adatbázis és gyűjtemény
* Egy Function-alkalmazás és egy Storage-fiók, amely üzemelteti azt

Az alábbi szakaszban bemutatjuk, hogyan hozhatja létre ezeket az erőforrásokat az Azure CLI használatával.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha nem Cloud Shell használ, az Azure CLI-t helyileg kell használnia a fiók eléréséhez. A bash parancssorában használja a `az login` parancsot a böngészőalapú bejelentkezési élmény elindításához. Ha egynél több Azure-előfizetéshez fér hozzá, akkor az alapértelmezett értéket állítsa `az account set --subscription`, majd az előfizetés-azonosítót.

### <a name="set-environment-variables"></a>Környezeti változók beállítása

Ezután hozzon létre néhány környezeti változót a létrehozni kívánt erőforrások neveihez és helyéhez. Használja az alábbi parancsokat, és cserélje le a `<value>` helyőrzőket a választott értékekre. Az értékeknek meg kell felelniük az [Azure-erőforrások elnevezési szabályainak és korlátozásainak](/azure/architecture/best-practices/resource-naming). A `LOCATION` változó esetében használja a `az functionapp list-consumption-locations` parancs által létrehozott értékek egyikét.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

Az oktatóanyag többi része ezeket a változókat használja. Vegye figyelembe, hogy ezek a változók csak az aktuális Azure CLI-vagy Cloud Shell-munkamenet időtartama alatt maradnak meg. Ezeket a parancsokat újra futtatnia kell, ha más helyi terminált használ, vagy ha a Cloud Shell munkamenet időtúllépést tapasztal.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure erőforráscsoportok használatával gyűjti össze a fiókban lévő összes kapcsolódó erőforrást. Így megtekintheti őket egységként, és egyetlen paranccsal törölheti őket, ha elkészült velük.

Erőforráscsoport létrehozásához használja a következő parancsot:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ezután hozzon létre egy Azure Event Hubs névteret, az Event hub és az engedélyezési szabályt az alábbi parancsokkal:

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

A Event Hubs névtér tartalmazza a tényleges Event hub-t és annak engedélyezési szabályát. Az engedélyezési szabály lehetővé teszi, hogy a függvények üzeneteket küldjenek a hubhoz, és figyeljenek a megfelelő eseményeket. Az egyik függvény telemetria-adatokat jelképező üzeneteket küld. Egy másik függvény figyeli az eseményeket, elemzi az eseményre vonatkozó adatmennyiséget, és az eredményeket Azure Cosmos DB tárolja.

### <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB létrehozása

Ezután hozzon létre egy Azure Cosmos DB fiókot, adatbázist és gyűjteményt a következő parancsokkal:

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

Az `partition-key-path` érték az egyes elemek `temperatureStatus` értéke alapján particionálja az adatait. A partíciós kulcs lehetővé teszi Cosmos DB számára a teljesítmény növelését azáltal, hogy az adatait külön részhalmazokra osztja, amelyeket egymástól függetlenül érhet el.

### <a name="create-a-storage-account-and-function-app"></a>Storage-fiók és-Function-alkalmazás létrehozása

Ezután hozzon létre egy Azure Storage-fiókot, amelyet Azure Functions igényel, majd hozza létre a Function alkalmazást. Használja az alábbi parancsokat:

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

Ha a `az functionapp create` parancs létrehozza a Function alkalmazást, akkor egy azonos nevű Application Insights-erőforrást is létrehoz. A Function alkalmazás automatikusan konfigurálva van egy `APPINSIGHTS_INSTRUMENTATIONKEY` nevű beállítással, amely összekapcsolja azt Application Insightshoz. A függvények Azure-ba történő üzembe helyezését követően megtekintheti az alkalmazás telemetria, az oktatóanyag későbbi részében leírtak szerint.

## <a name="configure-your-function-app"></a>A Function alkalmazás konfigurálása

A Function alkalmazásnak hozzá kell férnie a többi erőforráshoz, hogy megfelelően működjön. A következő részben bemutatjuk, hogyan konfigurálhatja a Function alkalmazást úgy, hogy az a helyi gépen is futtatható legyen.

### <a name="retrieve-resource-connection-strings"></a>Erőforrás-kapcsolatok karakterláncának beolvasása

A következő parancsokkal kérheti le a Storage, az Event hub és a Cosmos DB kapcsolati karakterláncokat, és mentheti azokat környezeti változókba:

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

Ezek a változók az Azure CLI-parancsokból beolvasott értékekre vannak beállítva. Mindegyik parancs egy JMESPath lekérdezést használ a visszaadott JSON-adattartalomból való kinyeréséhez. A kapcsolatok karakterláncai a `echo` használatával is megjelennek, így ellenőrizheti, hogy sikerült-e beolvasni.

### <a name="update-your-function-app-settings"></a>A függvény alkalmazás beállításainak frissítése

Ezután a következő parancs használatával vigye át a kapcsolódási karakterlánc értékeit az Azure Functions-fiókban található alkalmazásbeállításokba:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Az Azure-erőforrások létrehozása és konfigurálása sikeresen megtörtént a megfelelő együttműködés érdekében.

## <a name="create-and-test-your-functions"></a>Függvények létrehozása és tesztelése

Ezután hozzon létre egy projektet a helyi gépen, adja hozzá a Java-kódot, és tesztelje. Olyan parancsokat fog használni, amelyek a Azure Functions és a Azure Functions Core Tools Maven beépülő modullal működnek. A függvények helyileg futnak, de a létrehozott felhőalapú erőforrásokat fogják használni. A függvények helyi működésének megkezdése után a Maven használatával üzembe helyezheti őket a felhőben, és megtekintheti az adatok és az elemzések összegyűjtését.

Ha az erőforrások létrehozásához Cloud Shell használt, akkor nem fog helyileg csatlakozni az Azure-hoz. Ebben az esetben használja a `az login` parancsot a böngészőalapú bejelentkezési folyamat elindításához. Ha szükséges, állítsa az alapértelmezett előfizetést `az account set --subscription`, majd az előfizetés-AZONOSÍTÓval. Végül futtassa a következő parancsokat a környezeti változók újbóli létrehozásához a helyi gépen. Cserélje le a `<value>` helyőrzőket a korábban használt értékekre.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Helyi functions-projekt létrehozása

A következő Maven-paranccsal hozhat létre functions-projektet, és hozzáadhatja a szükséges függőségeket.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Ez a parancs egy `telemetry-functions` mappában több fájlt hoz létre:

* A Maven használatával használható `pom.xml`-fájl
* `local.settings.json`-fájl, amely a helyi teszteléshez szükséges Alkalmazásbeállítások tárolására használható
* `host.json`-fájl, amely lehetővé teszi, hogy az adatelemzési függvény Cosmos DB kimeneti kötéséhez szükséges Azure Functions Extension-csomagot
* Az alapértelmezett függvény implementációját tartalmazó `Function.java`-fájl
* Néhány olyan tesztoldalt, amelyre ez az oktatóanyag nem szükséges

A fordítási hibák elkerülése érdekében törölnie kell a teszt fájlokat. A következő parancsok futtatásával navigáljon az új projekt mappájába, és törölje a teszt mappát:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>A Function alkalmazás beállításainak beolvasása helyi használatra

Helyi teszteléshez a Function projektnek szüksége lesz az Azure-beli Function alkalmazáshoz az oktatóanyag korábbi részében hozzáadott kapcsolódási karakterláncokra. Használja a következő Azure Functions Core Tools parancsot, amely lekéri a felhőben tárolt összes Function app-beállítást, és hozzáadja őket a `local.settings.json` fájlhoz:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Java-kód hozzáadása

Ezután nyissa meg a `Function.java` fájlt, és cserélje le a tartalmát a következő kódra.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Amint látható, ez a fájl két függvényt tartalmaz, `generateSensorData` és `processSensorData`. A `generateSensorData` függvény egy érzékelőt szimulál, amely hőmérséklet-és terhelési beolvasást küld az Event hub-nak. Egy időzítő eseményindító 10 másodpercenként futtatja a függvényt, az Event hub kimeneti kötése pedig visszaküldi a visszatérési értéket az Event hub számára.

Amikor az Event hub megkapja az üzenetet, egy eseményt hoz létre. Az `processSensorData` függvény akkor fut le, amikor megkapja az eseményt. Ezután feldolgozza az esemény-adatokat, és egy Azure Cosmos DB kimeneti kötést használ az eredmények Azure Cosmos DBnak való elküldéséhez.

A függvények által használt adatok tárolása egy `TelemetryItem`nevű osztály használatával történik, amelyet végre kell hajtania. Hozzon létre egy `TelemetryItem.java` nevű új fájlt ugyanazon a helyen, mint `Function.java`, és adja hozzá a következő kódot:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Helyi futtatás

Most már helyileg is létrehozhatja és futtathatja a függvényeket, és láthatja, hogy az adatok megjelennek a Azure Cosmos DB.

A függvények létrehozásához és futtatásához használja a következő Maven-parancsokat:

```bash
mvn clean package
mvn azure-functions:run
```

Néhány felépítési és indítási üzenet után az alábbi példához hasonló kimenet jelenik meg a függvények futtatásakor:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Ezután nyissa meg a [Azure Portal](https://portal.azure.com) , és navigáljon a Azure Cosmos db-fiókjához. Válassza ki **adatkezelő**, bontsa ki a **TelemetryInfo**elemet, majd válassza az **elemek** lehetőséget az adatok megtekintéséhez.

![Cosmos DB Adatkezelő](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Üzembe helyezés az Azure-ban és az alkalmazás telemetria megtekintése

Végül üzembe helyezheti az alkalmazást az Azure-ban, és ellenőrizheti, hogy az továbbra is ugyanúgy működik-e, mint a helyileg.

Telepítse a projektet az Azure-ba a következő paranccsal:

```bash
mvn azure-functions:deploy
```

A függvények mostantól az Azure-ban futnak, és továbbra is felhalmoznak egy adatmennyiséget a Azure Cosmos DB. Megtekintheti a telepített Function alkalmazást a Azure Portalban, és megtekintheti az alkalmazás telemetria a csatlakoztatott Application Insights erőforráson keresztül, ahogy az alábbi képernyőképeken is látható:

**Élő metrikastream:**

![Application Insights Élő metrikastream](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Teljesítmény:**

![Application Insights teljesítmény panel](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az oktatóanyagban létrehozott Azure-erőforrásokkal, a következő parancs használatával törölheti őket:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy Azure-függvényt, amely az Event hub-eseményeket kezeli, és frissíti a Cosmos DB. További információ: [Azure functions Java fejlesztői útmutató](/azure/azure-functions/functions-reference-java). A használt megjegyzésekkel kapcsolatos információkért tekintse meg a következőt: [com. microsoft. Azure. functions. Megjegyzés](/java/api/com.microsoft.azure.functions.annotation) leírása.

Ez az oktatóanyag a környezeti változókat és az alkalmazás beállításait használta a titkok, például a kapcsolódási karakterláncok tárolásához. A titkok Azure Key Vault-ben való tárolásával kapcsolatos információkért lásd: [Key Vault referenciák használata app Service és Azure Functionshoz](/azure/app-service/app-service-key-vault-references).

Következő lépésként megtudhatja, hogyan használhatja az Azure-folyamatok CI/CD-t az automatikus üzembe helyezéshez:

> [!div class="nextstepaction"]
> [Java létrehozása és üzembe helyezése Azure Functions](/azure/devops/pipelines/ecosystems/java-function)
