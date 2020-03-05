---
title: A Java-függvény csatlakoztatható az Azure Storage-hoz
description: Megtudhatja, hogyan csatlakoztatható a HTTP-triggert használó Java-függvények az Azure Storage-hoz egy üzenetsor-tároló kimeneti kötésének használatával.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 8ae69bfa7ed00e310205332e05c071158c5fc9a3
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272810"
---
# <a name="connect-your-java-function-to-azure-storage"></a>A Java-függvény csatlakoztatható az Azure Storage-hoz

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan integrálhatja az előző rövid útmutató [cikkében](functions-create-first-java-maven.md) létrehozott függvényt egy Azure Storage-üzenetsor használatával. Az ehhez a függvényhez hozzáadott kimeneti kötés egy HTTP-kérelemből adatokat ír a várólistában lévő üzenetbe.

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A kapcsolódás egyszerűbbé tételéhez használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy `AzureWebJobsStorage`nevű alkalmazás-beállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt végezze el a [Java rövid útmutató 1. részében](functions-create-first-java-maven.md)ismertetett lépéseket.

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Bővítmény-csomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

Egy Java-projektben a kötések kötési megjegyzésekként vannak definiálva a Function metódusban. A *function. JSON* fájl ezután automatikusan létrejön a jegyzetek alapján.

Tallózással keresse meg a függvény kódját a _src/Main/Java_területen, nyissa meg a *function. Java* projektfájlt, és adja hozzá a következő paramétert a `run` metódus definícióhoz:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

A `msg` paraméter egy [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) típus, amely a függvény befejeződése után üzenetként írt karakterláncok gyűjteményét jelöli. Ebben az esetben a kimenet egy `outqueue`nevű tárolási várólista. A Storage-fiókhoz tartozó kapcsolatok karakterláncát a `connection` metódus állítja be. A felhasználói karakterlánc helyett adja meg a Storage-fiók kapcsolatok sztringjét tartalmazó alkalmazásbeállítás értékét.

A `run` metódus definíciójának ekkor az alábbi példához hasonlóan kell kinéznie:  

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

Most az új `msg` paraméter használatával írhat a függvény kódjából a kimeneti kötésbe. Adja hozzá a következő kódrészletet a sikeres válasz előtt, hogy hozzáadja a `name` értékét a `msg` kimeneti kötéshez.

```java
msg.setValue(name);
```

Ha kimeneti kötést használ, nem kell használnia az Azure Storage SDK kódját a hitelesítéshez, a várólista-hivatkozás beszerzéséhez vagy az adatíráshoz. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

A `run` metódusnak most az alábbi példához hasonlóan kell kinéznie:

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

Mivel az archetípus is létrehoz egy tesztet, frissítenie kell ezeket a teszteket az új `msg` paraméternek a `run` metódus aláírásában való kezeléséhez.  

Tallózással keresse meg a kód helyét az _src/test/Java_területen, nyissa meg a *function. Java* projektfájlt, és cserélje le a kód sorát a `//Invoke` elemre a következő kóddal.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Most már készen áll az új kimeneti kötés helyi kipróbálására.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Ahogy korábban is, használja a következő parancsot a projekt felépítéséhez és a functions Runtime helyi elindításához:

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
> Mivel engedélyezte a bővítmények használatát a Host. JSON fájlban, a [Storage kötési bővítmény](functions-bindings-storage-blob.md#add-to-your-functions-app) le lett töltve és telepítve lett az indításakor, valamint a többi Microsoft-kötési bővítménysel együtt.

Ahogy korábban is, aktiválja a függvényt a parancssorból a cURL használatával egy új terminál ablakban:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Ezúttal a kimeneti kötés is létrehoz egy `outqueue` nevű várólistát a Storage-fiókban, és hozzáadja az ugyanezt a karakterláncot tartalmazó üzenetet.

Ezután az Azure CLI használatával megtekintheti az új várólistát, és ellenőrizheti, hogy hozzá lett-e adva üzenet. Az üzenetsor a [Microsoft Azure Storage Explorer][Azure Storage Explorer] vagy a [Azure Portal](https://portal.azure.com)használatával is megtekinthető.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>A projekt újbóli üzembe helyezése 

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

Újra használhatja a cURLot az üzembe helyezett függvény teszteléséhez. Mint korábban, adja át a POST kérelem törzsében `AzureFunctions` értéket az URL-címre, az alábbi példában látható módon:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

[Megvizsgálhatja a tárolási üzenetsor üzenetét](#query-the-storage-queue) annak ellenőrzéséhez, hogy a kimeneti kötés a várt módon létrehoz egy új üzenetet a várólistában.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Következő lépések

Frissítette a HTTP-triggert a függvényt, hogy az adatait egy tárolási várólistába írja. Ha többet szeretne megtudni a Java-Azure Functions fejlesztéséről, tekintse meg a [Java fejlesztői útmutató Azure functions](functions-reference-java.md) és [Azure functions eseményindítók és kötések](functions-triggers-bindings.md)című témakört. A Java-funkciók teljes körű funkciós projektjeivel kapcsolatos példákért tekintse meg a [Java functions mintákat](/samples/browse/?products=azure-functions&languages=Java). 

Ezután engedélyezze Application Insights figyelését a Function alkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
