---
title: Java fejlesztői segédanyagok az Azure Functions |} A Microsoft Docs
description: Megismerheti, hogyan hozhat létre Java-függvényeket.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: az Azure functions, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra, a java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: acd873cd19cafb785f968fd3d8671640bcfafed8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163706"
---
# <a name="azure-functions-java-developer-guide"></a>Az Azure Functions Java fejlesztői útmutatója

Az Azure Functions runtime támogatja [Java használata 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Ez az útmutató az Azure Functions és a Java együttes írása jainak részleteivel kellene információt tartalmaz.

Egy Java-függvény egy `public` módot, az a jegyzet kitüntetett `@FunctionName`. Ez a módszer a Java-függvények a bejegyzés határozza meg, és a egy adott csomag egyedinek kell lennie. 

Ez a cikk feltételezi, hogy már elolvasta a [Azure Functions fejlesztői segédanyagai](functions-reference.md). A függvények gyors útmutató: az első függvény létrehozása használatával is ki [Visual Studio Code](functions-create-first-function-vs-code.md) vagy [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>A programozási modell 

A fogalmait [eseményindítók és kötések](functions-triggers-bindings.md) alapvető fontosságú, az Azure Functionsben. Eseményindítók egy kód végrehajtásának indítása. Kötések lehetővé teszik, adja át az adatokat, és a egy függvényt, az adatokat egyéni adat-hozzáférési kód írása nélkül adja vissza.

## <a name="folder-structure"></a>gyökérmappa-szerkezetében

A mappastruktúra az Azure Functions Java-projekt itt látható:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Használhat egy megosztott [host.json](functions-host-json.md) fájlt a függvényalkalmazás konfigurálása. Minden függvény saját kódfájl (.java) és a kötési konfigurációs fájl (function.json) rendelkezik.

Egynél több függvényt helyezheti a projektben. Kerülje a functions üzembe a különálló JAR-fájlok kivételével. A `FunctionApp` a célzott könyvtárban, mire megtörténik a függvényalkalmazáshoz az Azure-ban.

## <a name="triggers-and-annotations"></a>Eseményindítók és jegyzetek

 Funkciók, például HTTP-kérést, egy időzítő vagy egy frissítést a data-triggerek által kerül meghívásra. A függvény kell feldolgoznia az eseményindító és a többi bemenet, egy vagy több kimeneti előállításához.

Használja a Java-jegyzetek szerepel a [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) csomag bemeneti és kimeneti kötést létrehozni a módszereket. További információkért lásd: a [Java referenciadokumentumok](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Konfigurálnia kell az Azure Storage-fiókot a [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) Azure Blob storage, Azure Queue storage vagy az Azure Table storage eseményindítók helyi futtatásához.

Példa:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Itt látható a létrehozott megfelelő `function.json` által a [azure-functions-maven-bővítménnyel](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>JDK futásidejű rendelkezésre állásával és támogatásával 

Helyi fejlesztés Java függvényalkalmazások, letöltheti és használhatja a [Azul Zulu Enterprise Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 segítségével a [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Az Azure Functions az Azul Java 8 JDK-modul használ, a függvényalkalmazásokat a felhőbe való központi telepítésekor.

[Az Azure-támogatás](https://azure.microsoft.com/support/) a segítségével és a függvény problémáinak alkalmazások érhető el egy [minősített támogatási csomag](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>JVM testreszabása

Functions segítségével testre szabhatja a Java virtuális gép (JVM) segítségével a Java-függvények futtatása. A [alábbi JVM-beállításokat](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) alapértelmezés szerint használt:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Megadhat egy alkalmazásban nevű beállításhoz további argumentumok `JAVA_OPTS`. A függvényalkalmazás üzembe helyezett Azure-bA az Azure portal vagy az Azure CLI adhat hozzá alkalmazásbeállításokat.

### <a name="azure-portal"></a>Azure Portal

Az a [az Azure portal](https://portal.azure.com), használja a [Alkalmazásbeállítások lapot](functions-how-to-use-azure-function-app-settings.md#settings) hozzáadása a `JAVA_OPTS` beállítás.

### <a name="azure-cli"></a>Azure CLI

Használhatja a [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) parancsot az `JAVA_OPTS`, ahogy az alábbi példában:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Ebben a példában a távfelügyeleti üzemmód lehetővé teszi. Cserélje le `<APP_NAME>` a függvényalkalmazás nevére és `<RESOURCE_GROUP>` az erőforráscsoport.

> [!WARNING]  
> Az a [Használatalapú csomag](functions-scale.md#consumption-plan), hozzá kell adnia a `WEBSITE_USE_PLACEHOLDER` beállítás értékét a `0`.  
Ez a beállítás növelje a ritkán használt kezdő időpontjának a Java-függvényeket.

## <a name="third-party-libraries"></a>Külső gyártó kódtárait 

Az Azure Functions támogatja a külső gyártótól származó kódtárakat. Alapértelmezés szerint az összes függősége a projekt megadott `pom.xml` fájl automatikusan alatt vannak csoportosítva a [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cél. Nincs megadva függőségeként-tárak a `pom.xml` fájlt, helyezze el őket egy `lib` könyvtárat a gyökérkönyvtár a függvény. Függőségeket helyezi el a `lib` directory hozzáadódnak a rendszer osztálybetöltő futásidőben.

A `com.microsoft.azure.functions:azure-functions-java-library` függőségi alapértelmezés szerint az osztályútvonal lemezén, és nem kell szerepelnie a `lib` könyvtár. Ezenkívül [azure-functions-java-feldolgozó](https://github.com/Azure/azure-functions-java-worker) hozzáadja a felsorolt függőségei [Itt](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) , az osztályútvonal.

## <a name="data-type-support"></a>Adattípus-támogatás

Egyszerű régi Java-objektumok (POJOs) is használhatja, meghatározott típusú `azure-functions-java-library`, vagy például karakterlánc és egész kötést létrehozni a bemeneti vagy kimeneti kötés primitív adattípusokat.

### <a name="pojos"></a>POJOs

Az bemeneti adatok átalakításához pojo-vá, [azure-functions-java-feldolgozó](https://github.com/Azure/azure-functions-java-worker) használja a [gson](https://github.com/google/gson) könyvtár. A függvények bemenetei között meg kell használni pojo-vá típusok `public`.

### <a name="binary-data"></a>Bináris adatok

Bináris bemeneti vagy kimeneti való kötés `byte[]`, beállításával a `dataType` mezőbe, a function.json `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Ha várhatóan null értékeket, `Optional<T>`.

## <a name="bindings"></a>Kötések

Bemeneti és kimeneti kötései deklaratív módszert a kódon belül az adatokhoz való csatlakozáshoz adja meg. Egy függvény több bemeneti és a kimeneti kötés.

### <a name="input-binding-example"></a>Bemeneti kötés példa

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Ez a funkció a HTTP-kérést indít el. 
- HTTP-kérések forgalma átadott a `String` argumentuma `inputReq`.
- Egy bejegyzést a Table storage-ból beolvasott, és mint átadott `TestInputData` a argumentum `inputData`.

Bemenetek kötegelt fogadásához kell kötni `String[]`, `POJO[]`, `List<String>`, vagy `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Ez a függvény minden alkalommal, amikor nincs új adat a konfigurált eseményközpont lekérdezi aktiválódik. Mivel a `cardinality` értékre van állítva `MANY`, a függvény kap egy üzenetköteget az eseményközpontból. `EventData` az event hub alakulnak át `TestEventData` függvény végrehajtására.

### <a name="output-binding-example"></a>Kimeneti kötés példa

Egy kimeneti kötést kell kötni a visszaadott érték használatával `$return`. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Ha több kimeneti kötést, a visszaadott értékének használata csak az egyik.

Több kimeneti érték küldeni, használja a `OutputBinding<T>` meghatározott a `azure-functions-java-library` csomagot. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Ez a függvény egy Törzsparaméterei a indít el. A Queue storage több értéket ír.

## <a name="httprequestmessage-and-httpresponsemessage"></a>A HttpRequestMessage és HttpResponseMessage

 Ezek meghatározása a `azure-functions-java-library`. Ezek olyan segítő HttpTrigger függvények.

| Speciális típusa      |       Target        | Tipikus használati                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-trigger     | Módszer, fejlécek és lekérdezések beolvasása |
| `HttpResponseMessage` | HTTP-kimeneti kötése | 200-as eltérő állapotának visszaadása   |

## <a name="metadata"></a>Metaadatok

Néhány eseményindítók küldése [metaadat-trigger](/azure/azure-functions/functions-triggers-bindings) bemeneti adatokkal együtt. Használhatja a jegyzet `@BindingName` metaadat-trigger kötést létrehozni.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
Az előző példában a `queryValue` van kötve, a lekérdezési karakterlánc paramétereként `name` http kérés URL-CÍMBEN szereplő `http://{example.host}/api/metadata?name=test`. Egy másik példa: hogyan kell kötni `Id` üzenetsor eseményindító metaadataiból.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> A jegyzet megadott névvel kell egyeznie a metaadat-tulajdonságot.

## <a name="execution-context"></a>Végrehajtási környezet

`ExecutionContext`, meghatározott a `azure-functions-java-library`, a functions futtatókörnyezete kommunikálni segítő metódust tartalmaz.

### <a name="logger"></a>naplózó

Használat `getLogger`meghatározva `ExecutionContext`, naplók írni a függvénykódot.

Példa:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Naplók megtekintése és nyomkövetés

Az Azure CLI-stream Java stdout és stderr naplózás, valamint egyéb alkalmazásnaplózás is használhatja. 

A következő alkalmazásnaplózás írni az Azure CLI-vel a függvényalkalmazás konfigurálása:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

A naplózás kimeneti a függvényalkalmazás streamelése az Azure CLI-vel, nyisson meg egy új parancssort, a Bash vagy a terminál-munkamenetet, és adja meg a következő parancsot:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
A [az webapp log tail](/cli/azure/webapp/log) parancsnak beállításai használatával kimeneti szűrése a `--provider` lehetőséget. 

Töltse le a naplófájlok egyetlen ZIP-fájlban, az Azure CLI-vel, nyisson meg egy új parancssort, a Bash vagy a terminál-munkamenetet, és adja meg a következő parancsot:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Fájlrendszer naplózása az Azure Portalon vagy az Azure CLI a parancs futtatása előtt van engedélyezve.

## <a name="environment-variables"></a>Környezeti változók

A függvények [Alkalmazásbeállítások](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), például a szolgáltatás kapcsolati karakterláncok, ki vannak téve környezeti változókként végrehajtása során. Ezek a beállítások használatával, férnek `System.getenv("AzureWebJobsStorage")`.

Például hozzáadhat [Alkalmazásbeállítás](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings), nevű `testAppSetting` és az érték `testAppSettingValue`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>További lépések

Azure Functions-Java-fejlesztésekhez kapcsolatos további információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* Helyi fejlesztés és a hibakeresési [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), és [eclipse-ben](functions-create-maven-eclipse.md)
* [Távoli hibakeresés Java az Azure Functions Visual Studio Code-dal](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven beépülő modul az Azure Functions szolgáltatáshoz](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Függvény létrehozása révén egyszerűsíthetők a `azure-functions:add` célja, és készítse elő az átmeneti könyvtár [ZIP fájl központi telepítési](deployment-zip-push.md).
