---
title: A Java-függvény csatlakoztatása az Azure Storage-hoz
description: Ismerje meg, hogyan csatlakoztathat egy HTTP-aktivált Java-függvényt az Azure Storage-hoz egy várólista-tárolási kimeneti kötés használatával.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 8ae69bfa7ed00e310205332e05c071158c5fc9a3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78272810"
---
# <a name="connect-your-java-function-to-azure-storage"></a>A Java-függvény csatlakoztatása az Azure Storage-hoz

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan integrálhatja az [előző rövid útmutató cikkben](functions-create-first-java-maven.md) létrehozott függvényt egy Azure Storage-várólistával. A függvényhez hozzáadott kimeneti kötés http-kérelemből adatokat ír a várólistában lévő üzenetbe.

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a Functions a kötött szolgáltatás eléréséhez használ. A kapcsolat megkönnyítése érdekében használja a storage-fiókot, amelya függvényalkalmazással létrehozott. A fiókhoz való kapcsolat már a neve `AzureWebJobsStorage`s. alkalmazásbeállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk megkezdése előtt hajtsa végre [a Java gyorsútmutató első részének lépéseit.](functions-create-first-java-maven.md)

## <a name="download-the-function-app-settings"></a>A függvényalkalmazás beállításainak letöltése

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Bővítménycsomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Most már hozzáadhatja a Storage kimenetkötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

Java projektben a kötések a függvénymetódus kötési jegyzeteiként vannak definiálva. A *function.json* fájl ezután automatikusan generálódik ezen jegyzetek alapján.

Tallózással keresse meg a függvénykód helyét _az src/main/java_alatt, nyissa meg `run` a *Function.java* projektfájlt, és adja hozzá a következő paramétert a metódus definíciójához:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

A `msg` paraméter [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) egy típus, amely a függvény befejezésekor a kimeneti kötéshez üzenetekként írt karakterláncok gyűjteményét jelöli. Ebben az esetben a kimenet egy `outqueue`tárolási várólista nevű. A storage-fiók kapcsolati karakterláncát `connection` a metódus állítja be. Ahelyett, hogy maga a kapcsolati karakterlánc, adja át az alkalmazás beállítást, amely tartalmazza a storage-fiók kapcsolati karakterlánc.

A `run` metódus definíciójának most a következő példához hasonlóan kell kinéznie:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

Most már használhatja `msg` az új paramétert a kimeneti kötéshez való íráshoz a függvénykódból. Adja hozzá a következő kódsort a sikeres `name` válasz `msg` előtt, hogy hozzáadja a kimeneti kötés értékét.

```java
msg.setValue(name);
```

Kimeneti kötés használataesetén nem kell használnia az Azure Storage SDK-kódot a hitelesítéshez, a várólista-referencia beszerzéséhez vagy az adatok írásához. A Functions futásidejű és a várólista kimeneti kötése ezeket a feladatokat elvégezheti.

A `run` módszernek most a következő példához hasonlóan kell kinéznie:

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>A tesztek frissítése

Mivel az archetípus teszteket is létrehoz, frissítenie kell ezeket a teszteket, hogy kezelni tudja az új `msg` paramétert a `run` metódus aláírásában.  

Tallózással keresse meg a tesztkód helyét _az src/test/java_alatt, nyissa meg `//Invoke` a *Function.java* projektfájlt, és cserélje le a kódsort a következő kódra.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Most már készen áll, hogy helyileg próbálja ki az új kimenetkötést.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A hogy korábban is, a következő paranccsal hozd létre a projektet, és helyileg indítsa el a Functions futásidejűt:

::: zone pivot="java-build-tools-maven"  
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end

> [!NOTE]  
> Mivel engedélyezte a bővítménykötegeket a host.json ban, a [Storage kötésbővítményt](functions-bindings-storage-blob.md#add-to-your-functions-app) az indítás során letöltötte és telepítette, a többi Microsoft-kötési bővítménylel együtt.

Mint korábban, indítsa el a funkciót a parancssorból a cURL használatával egy új terminálablakban:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Ezúttal a kimeneti kötés is `outqueue` létrehoz egy várólistát a storage-fiókban, és hozzáad egy üzenetet ugyanezzel a karakterlánccal.

Ezután az Azure CLI használatával megtekintheti az új várólistát, és ellenőrizze, hogy egy üzenet lett-e hozzáadva. A várólistát a Microsoft [Azure Storage Explorer][Azure Storage Explorer] vagy az [Azure Portal](https://portal.azure.com)használatával is megtekintheti.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>A projekt újratelepítése 

A közzétett alkalmazás frissítéséhez futtassa újra a következő parancsot:  

::: zone pivot="java-build-tools-maven"  
```bash
mvn azure-functions:deploy
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Ismét használhatja a cURL-t az üzembe helyezett függvény teszteléséhez. A hogy korábban `AzureFunctions` is, adja át az értéket a POST-kérelem törzsében az URL-nek, ahogy ebben a példában is:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

A [Storage-várólista-üzenet](#query-the-storage-queue) ismételt vizsgálatával ellenőrizheti, hogy a kimeneti kötés a várt módon új üzenetet hoz-e létre a várólistában.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Frissítette a HTTP-aktivált függvényt, hogy adatokat írjon egy tárolási várólistába. Ha többet szeretne megtudni az Azure Functions Java-val való fejlesztéséről, olvassa el az [Azure Functions Java fejlesztői útmutatóját](functions-reference-java.md) és az [Azure Functions aktiválási és kötési folyamatait.](functions-triggers-bindings.md) A Java-függvényprojektek reprezentatióját a [Java Functions minták című témakörben](/samples/browse/?products=azure-functions&languages=Java)talál. 

Ezután engedélyeznie kell az Application Insights figyelését a függvényalkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
