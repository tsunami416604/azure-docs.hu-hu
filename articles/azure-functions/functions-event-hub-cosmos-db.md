---
title: 'Oktatóanyag: Java-függvények használata az Azure Cosmos DB-vel és az Event Hubs-szal'
description: Ez az oktatóanyag bemutatja, hogyan használhatja fel az Események hubok eseményeket az Azure Cosmos DB-ben java nyelven írt függvény használatával.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: b6d7b2c60e777266b1cab578b8970c1fa1c6bc50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77425323"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Oktatóanyag: Hozzon létre egy függvényt Java-ban egy Event Hub-eseményindítóval és egy Azure Cosmos DB kimeneti kötéssel

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Java-függvényt az Azure Functions használatával, amely a hőmérséklet- és nyomásadatok folyamatos adatfolyamát elemzi. Az érzékelőleolvasásokat jelző eseményközpont-események aktiválják a funkciót. A függvény feldolgozza az eseményadatokat, majd állapotbejegyzéseket ad hozzá egy Azure Cosmos DB-hez.

Ebben az oktatóanyagban a következőkre lehet:

> [!div class="checklist"]
> * Azure-erőforrások létrehozása és konfigurálása az Azure CLI használatával.
> * Hozzon létre és teszteljen java függvényeket, amelyek ezekkel az erőforrásokkal kommunikálnak.
> * Telepítse a funkciókat az Azure-ba, és figyelje őket az Application Insights segítségével.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következőket kell telepítenie:

* A [Java Developer Kit](https://aka.ms/azure-jdks) 8-as verziója
* Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója
* [Azure CLI,](/cli/azure/install-azure-cli) ha nem szeretné használni a Cloud Shellt
* [Az Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) 2.6.666-os vagy újabb verziója

> [!IMPORTANT]
> Az `JAVA_HOME` oktatóanyag befejezéséhez a környezeti változót a JDK telepítési helyére kell állítani.

Ha közvetlenül szeretné használni az oktatóanyag kódját, tekintse meg a [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) mintatárt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Ebben az oktatóanyagban a következő forrásokra lesz szüksége:

* A többi erőforrást tartalmazó erőforráscsoport
* Eseményközpontok névtere, eseményközpontja és engedélyezési szabálya
* Cosmos DB-fiók, adatbázis és gyűjtemény
* Egy függvényalkalmazás és egy tárfiók a gazdaüzemeltetéséhez

A következő szakaszok bemutatják, hogyan hozhat létre ezeket az erőforrásokat az Azure CLI használatával.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha nem használja a Cloud Shellt, az Azure CLI-t helyileg kell használnia a fiók eléréséhez. Használja `az login` a bash parancs parancs átad-ból egy böngésző-alapú bejelentkezési élményt. Ha egynél több Azure-előfizetéshez fér hozzá, állítsa be az alapértelmezett et, `az account set --subscription` majd az előfizetés-azonosítót.

### <a name="set-environment-variables"></a>Környezeti változók beállítása

Ezután hozzon létre néhány környezeti változót a létrehozni kívánt erőforrások nevéhez és helyéhez. Használja a következő parancsokat, és cserélje le a `<value>` helyőrzőket az Ön által választott értékekre. Az értékeknek meg kell felelniük az [Azure-erőforrások elnevezési szabályainak és korlátozásainak.](/azure/architecture/best-practices/resource-naming) A `LOCATION` változóhoz használja a `az functionapp list-consumption-locations` parancs által létrehozott értékek egyikét.

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

Az oktatóanyag többi része ezeket a változókat használja. Ne feledje, hogy ezek a változók csak a jelenlegi Azure CLI- vagy Cloud Shell-munkamenet időtartama alatt maradnak meg. Ezeket a parancsokat újra kell futtatnia, ha egy másik helyi terminálablakot használ, vagy a Cloud Shell munkamenet-időkifutási idő.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure erőforráscsoportok at használ a fiókjában lévő összes kapcsolódó erőforrás összegyűjtéséhez. Így megtekintheti őket egységként, és egyetlen paranccsal törölheti őket, ha végzett velük.

Erőforráscsoport létrehozásához használja a következő parancsot:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ezután hozzon létre egy Azure Event Hubs névteret, eseményközpontot és engedélyezési szabályt a következő parancsokkal:

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

Az Event Hubs névtér tartalmazza a tényleges eseményközpontot és annak engedélyezési szabályát. Az engedélyezési szabály lehetővé teszi, hogy a függvények üzeneteket küldjenek a hubra, és figyeljék a megfelelő eseményeket. Az egyik függvény telemetriai adatokat jelölő üzeneteket küld. Egy másik függvény figyeli az eseményeket, elemzi az eseményadatokat, és tárolja az eredményeket az Azure Cosmos DB-ben.

### <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB létrehozása

Ezután hozzon létre egy Azure Cosmos DB-fiókot, adatbázist és gyűjteményt a következő parancsokkal:

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

Az `partition-key-path` érték az egyes elemek `temperatureStatus` értéke alapján particionálja az adatokat. A partíciókulcs lehetővé teszi, hogy a Cosmos DB növelje a teljesítményt azáltal, hogy az adatokat különálló részhalmazokba osztja, amelyeket egymástól függetlenül érhet el.

### <a name="create-a-storage-account-and-function-app"></a>Tárfiók és függvényalkalmazás létrehozása

Ezután hozzon létre egy Azure Storage-fiókot, amely az Azure Functions által igényelt, majd hozza létre a függvényalkalmazást. Az alábbi parancsokat használja:

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

Amikor `az functionapp create` a parancs létrehozza a függvényalkalmazást, egy Azonos nevű Application Insights-erőforrást is létrehoz. A függvényalkalmazás automatikusan konfigurálva `APPINSIGHTS_INSTRUMENTATIONKEY` van egy olyan nevű beállítással, amely összeköti azt az Application Insights-mal. Megtekintheti az alkalmazástelemetriai adatokat, miután üzembe helyezte a függvényeket az Azure-ban, az oktatóanyag későbbi részében leírtak szerint.

## <a name="configure-your-function-app"></a>A függvényalkalmazás konfigurálása

A függvényalkalmazásnak hozzá kell férnie a többi erőforráshoz a megfelelő működéshez. A következő szakaszok bemutatják, hogyan konfigurálhatja a függvényalkalmazást úgy, hogy az a helyi számítógépen futhasson.

### <a name="retrieve-resource-connection-strings"></a>Erőforrás-kapcsolati karakterláncok beolvasása

A következő parancsokkal lekérheti a tároló- és eseményközpontot és a Cosmos DB kapcsolati karakterláncokat, és mentheti őket a környezeti változókban:

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

Ezek a változók az Azure CLI-parancsokból beolvasott értékekre vannak beállítva. Mindegyik parancs Egy JMESPath-lekérdezést használ a visszaadott JSON-tartalom kapcsolati karakterláncának kinyeréséhez. A kapcsolati karakterláncok `echo` is megjelennek a használatával, így ellenőrizheti, hogy sikeresen lelett-e beolvasva.

### <a name="update-your-function-app-settings"></a>A függvényalkalmazás beállításainak frissítése

Ezután a következő paranccsal vigye át a kapcsolati karakterlánc értékeit az Azure Functions-fiók alkalmazásbeállításaiba:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Az Azure-erőforrások létrehozása és konfigurálása megfelelően működik együtt.

## <a name="create-and-test-your-functions"></a>A függvények létrehozása és tesztelése

Ezután hozzon létre egy projektet a helyi számítógépen, adjon hozzá Java-kódot, és tesztelje azt. A Maven Azure Functions beépülő moduljával és az Azure Functions core eszközökkel használható parancsokat fog használni. A függvények helyileg fognak futni, de a létrehozott felhőalapú erőforrásokat használják. Miután a függvények helyileg működnek, a Maven segítségével üzembe helyezheti őket a felhőben, és megtekintheti az adatok és elemzések felhalmozódását.

Ha a Cloud Shell használatával hozza létre az erőforrásokat, akkor nem csatlakozik helyileg az Azure-hoz. Ebben az esetben `az login` használja a parancsot a böngészőalapú bejelentkezési folyamat elindításához. Ezután szükség esetén állítsa `az account set --subscription` be az alapértelmezett előfizetést, amelyet az előfizetés-azonosító követ. Végül futtassa a következő parancsokat, hogy újra hozzon létre néhány környezeti változót a helyi számítógépen. Cserélje `<value>` le a helyőrzőket a korábban használt értékekre.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Helyi függvényprojekt létrehozása

A következő Maven paranccsal hozzon létre egy függvényprojektet, és adja hozzá a szükséges függőségeket.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Ez a parancs több `telemetry-functions` fájlt hoz létre egy mappában:

* A `pom.xml` Maven-hez használható fájl
* Alkalmazásbeállítások `local.settings.json` at tartó fájl a helyi teszteléshez
* Az `host.json` Azure Functions Extension Bundle-t lehetővé tő> lehetővé tévő fájl, amely a Cosmos DB kimeneti kötéséhez szükséges az adatelemzési függvényben
* Alapértelmezett `Function.java` függvényimplementációt tartalmazó fájl
* Néhány tesztfájl, amelyet ez az oktatóanyag nem igényel

A fordítási hibák elkerülése érdekében törölnie kell a tesztfájlokat. Futtassa a következő parancsokat az új projektmappába való navigáláshoz és a tesztmappa törléséhez:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>A függvényalkalmazás beállításainak beolvasása helyi használatra

A helyi teszteléshez a függvényprojektnek szüksége lesz a kapcsolati karakterláncok, amelyek az Azure-ban az oktatóanyag korábbi az Azure-ban hozzáadott. Használja a következő Azure Functions Core Tools parancsot, amely lekéri a felhőben `local.settings.json` tárolt összes függvényalkalmazás-beállítást, és hozzáadja őket a fájlhoz:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Java-kód hozzáadása

Ezután nyissa `Function.java` meg a fájlt, és cserélje le a tartalmát a következő kódra.

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

Amint láthatja, ez a `generateSensorData` fájl `processSensorData`két függvényt tartalmaz, és . A `generateSensorData` függvény egy érzékelőt szimulál, amely hőmérséklet- és nyomásértékeket küld az eseményközpontba. Egy időzítő eseményindító fut a függvény 10 másodpercenként, és egy eseményközpont kimeneti kötés elküldi a visszatérési értéket az eseményközpontba.

Amikor az eseményközpont megkapja az üzenetet, létrehoz egy eseményt. A `processSensorData` függvény fut, amikor megkapja az eseményt. Ezután feldolgozza az eseményadatokat, és egy Azure Cosmos DB kimeneti kötés t használ az eredmények et az Azure Cosmos DB-nek.

Az ezek a függvények által használt `TelemetryItem`adatok tárolása egy osztály nevű , amelyet meg kell valósítani. Hozzon létre `TelemetryItem.java` egy új fájlt, amelyet ugyanazon a helyen hívnak meg, `Function.java` és adja hozzá a következő kódot:

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

Most már hozhat létre, és a függvények helyileg, és az adatok megjelennek az Azure Cosmos DB.

A függvények létrehozásához és futtatásához használja a következő Maven parancsokat:

```bash
mvn clean package
mvn azure-functions:run
```

Néhány létrehozási és indítási üzenet után a függvények futtatásakor a következő példához hasonló kimenet jelenik meg:

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

Ezután nyissa meg az [Azure Portalon,](https://portal.azure.com) és keresse meg az Azure Cosmos DB-fiókját. Válassza **az Adatkezelő**, bontsa ki a **TelemetryInfo**, majd válassza **az elemek** az adatok megtekintéséhez, amikor megérkezik.

![Cosmos DB adatkezelő](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Üzembe helyezés az Azure-ban és az alkalmazástelemetria megtekintése

Végül telepítheti az alkalmazást az Azure-ba, és ellenőrizheti, hogy továbbra is ugyanúgy működik-e, mint helyileg.

Telepítse a projektet az Azure-ba a következő paranccsal:

```bash
mvn azure-functions:deploy
```

Your functions now run in Azure, and continue to accumulate data in your Azure Cosmos DB. Megtekintheti az üzembe helyezett függvényalkalmazást az Azure Portalon, és megtekintheti az alkalmazástelemetriai adatokat a csatlakoztatott Application Insights-erőforráson keresztül, ahogy az a következő képernyőképeken látható:

**Élő mérőszámok streamje:**

![Az Application Insights élő mérőszámok streamje](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Teljesítmény:**

![Az Application Insights teljesítménypanelje](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az oktatóanyagban létrehozott Azure-erőforrásokkal, az alábbi parancs használatával törölheti őket:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy Azure-függvényt, amely kezeli az Event Hub-eseményeket, és frissíti a Cosmos DB-t. További információt az [Azure Functions Java fejlesztői útmutatójában talál.](/azure/azure-functions/functions-reference-java) A használt jegyzetekről a [com.microsoft.azure.functions.annotation](/java/api/com.microsoft.azure.functions.annotation) hivatkozásban talál további információt.

Ez az oktatóanyag környezeti változókat és alkalmazásbeállításokat használt a titkos kulcsok, például a kapcsolati karakterláncok tárolására. A titkos kulcsok Azure Key Vaultban való tárolásáról a [Key Vault-hivatkozások használata az App Service-hez és az Azure Functionshez](/azure/app-service/app-service-key-vault-references)című témakörben talál tájékoztatást.

Ezután megtudhatja, hogyan használhatja az Azure Pipelines CI/CD-t az automatikus telepítéshez:

> [!div class="nextstepaction"]
> [Java létrehozása és üzembe helyezése az Azure Functions ben](/azure/devops/pipelines/ecosystems/java-function)
