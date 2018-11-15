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
ms.openlocfilehash: 5f74ee390ac327a9e697d3dc67da4ea604b64d69
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686892"
---
# <a name="azure-functions-java-developer-guide"></a>Az Azure Functions Java fejlesztői útmutatója

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>A programozási modell 

A fogalmait [eseményindítók és kötések](functions-triggers-bindings.md) alapvető fontosságú, az Azure Functionsben. Eseményindítók egy kód végrehajtásának indítása. Kötések lehetővé teszik, adja át az adatokat, és a egy függvényt, az adatokat egyéni adat-hozzáférési kód írása nélkül adja vissza.

Egy függvény dolgozza fel a bemeneti és kimeneti előállításához állapotmentes metódus kell lennie. A függvény nem szabad függenie az osztály példánya mezőket. A függvény az összes módszer lehet `public` és jegyzet metódust @FunctionName egyedinek kell lennie, mivel a metódus nevét határozza meg a bejegyzést egy függvényhez.

## <a name="folder-structure"></a>gyökérmappa-szerkezetében

A mappastruktúra az Azure-függvény Java-projekt itt látható:

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

Van egy megosztott [host.json](functions-host-json.md) fájlt, amely a függvényalkalmazás konfigurálása használható. Minden függvény saját kódfájl (.java) és a kötési konfigurációs fájl (function.json) rendelkezik.

Egynél több függvényt helyezheti a projektben. Kerülje a functions üzembe a különálló JAR-fájlok kivételével. A FunctionApp a célként megadott könyvtárban, mire megtörténik a függvényalkalmazáshoz az Azure-ban.

## <a name="triggers-and-annotations"></a>Eseményindítók és jegyzetek

 Az Azure functions, például HTTP-kérést, egy időzítő vagy egy frissítést a data-triggerek által kerül meghívásra. A függvénynek szüksége van, az eseményindító és a egy vagy több kimeneti előállításához bemenetet feldolgozni.

Használja a Java-jegyzetek szerepel a [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) csomag bemeneti és kimeneti kötést létrehozni a módszereket. További információ: [Java referenciadokumentumok](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Konfigurálnia kell az Azure Storage-fiókot a [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) helyi futtatását az Azure Storage-Blob, üzenetsor vagy tábla eseményindítókat.

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

Töltse le és használja a [Azul Zulu Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) segítségével a [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) helyi függvény Java-alkalmazások fejlesztéséhez. Segítségével Windows, Linux és MacOS rendszeren érhetők el. [Az Azure-támogatás](https://support.microsoft.com/en-us/help/4026305/sql-contact-microsoft-azure-support) érhető el egy [minősített támogatási csomag](https://azure.microsoft.com/support/plans/).

## <a name="third-party-libraries"></a>Külső gyártó kódtárait 

Az Azure Functions támogatja a külső gyártótól származó kódtárakat. Alapértelmezés szerint az összes függősége a projekt megadott `pom.xml` fájl automatikusan kötegelve fog során a [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cél. Nincs megadva függőségeként-tárak a `pom.xml` fájlt, helyezze el őket egy `lib` könyvtárat a gyökérkönyvtár a függvény. Függőségeket helyezi el a `lib` directory hozzáadódik a rendszer osztálybetöltő futásidőben.

A `com.microsoft.azure.functions:azure-functions-java-library` függőségi alapértelmezés szerint az osztályútvonal lemezén, és nem kell szerepelnie a `lib` könyvtár. Emellett a függőségek felsorolt [Itt](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) hozzáadódnak az osztályútvonal által [azure-functions-java-feldolgozó](https://github.com/Azure/azure-functions-java-worker).

## <a name="data-type-support"></a>Adattípus-támogatás

Egyszerű régi Java-objektumok (POJOs) is használhatja, meghatározott típusú `azure-functions-java-library` vagy karakterlánc, egész szám, amely a bemeneti/kimeneti kötések kötése például primitív adattípusokat.

### <a name="plain-old-java-objects-pojos"></a>Egyszerű régi Java-objektumok (POJOs)

Az bemeneti adatok átalakításához pojo-vá, [azure-functions-java-feldolgozó](https://github.com/Azure/azure-functions-java-worker) használ [gson](https://github.com/google/gson) könyvtár. A függvények bemenetei között meg kell használni pojo-vá típusok `public`.

### <a name="binary-data"></a>Bináris adatok

Bináris bemeneti vagy kimeneti való kötés `byte[]` beállításával a `dataType` mezőbe, a function.json `binary`:

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

Használat `Optional<T>` Ha null értéket vár

## <a name="bindings"></a>Kötések

Bemeneti és kimeneti kötései deklaratív módszert a kódon belül az adatokhoz való csatlakozáshoz adja meg. Egy függvény több bemeneti és a kimeneti kötés.

### <a name="example-input-binding"></a>Példa bemeneti kötést

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

Ez a függvény meghívása HTTP-kérést. 
- HTTP-kérések forgalma átadott a `String` argumentuma `inputReq`
- Egy bejegyzés az Azure Table Storage-ból beolvasott, és mint átadott `TestInputData` a argumentum `inputData`.

Bemenetek kötegelt fogadásához kell kötni `String[]`, `POJO[]`, `List<String>` vagy `List<POJO>`.

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

Ez a függvény minden alkalommal, amikor nincs új adat a konfigurált eseményközpont lekérdezi aktiválódik. Mint a `cardinality` értékre van állítva `MANY`, függvény kap egy üzenetköteget az event hubs. Az event hubs EventData alakulnak át `TestEventData` függvény végrehajtására.

### <a name="example-output-binding"></a>Példa kimeneti kötése

A visszaadott érték egy kimeneti kötést hozhasson létre `$return` 

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

Egy Törzsparaméterei van meghívott függvény felett, és több érték írja az Azure Queue

## <a name="httprequestmessage-and-httpresponsemessage"></a>A HttpRequestMessage és HttpResponseMessage

 A HttpRequestMessage és HttpResponseMessage típusait `azure-functions-java-library` HttpTrigger függvények segítő típusa

| Speciális típusa      |       Cél        | Tipikus használati                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-trigger     | Első módszer, fejlécek és lekérdezések |
| `HttpResponseMessage` | HTTP-kimeneti kötése | 200-as eltérő állapotot adott vissza   |

## <a name="metadata"></a>Metaadatok

Néhány eseményindítók küldése [metaadat-trigger](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties) bemeneti adatokkal együtt. Használhatja a jegyzet `@BindingName` kötődni a metaadat-trigger


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
A fenti példában a `queryValue` van kötve a lekérdezési sztring paramétereként `name` a HTTP URL-cím kérése `http://{example.host}/api/metadata?name=test`. Az alábbiakban a kötés egy másik példa `Id` üzenetsor eseményindító metaadataiból

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " whith metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> A jegyzet a megadott meg kell felelnie a metaadat-tulajdonságot

## <a name="execution-context"></a>Végrehajtási környezet

`ExecutionContext` meghatározott a `azure-functions-java-library` a functions futtatókörnyezete kommunikálni segítő metódust tartalmaz.

### <a name="logger"></a>Naplózó

Használat `getLogger` meghatározott `ExecutionContext` naplók írni a függvénykódot.

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

Az Azure CLI használatával az alkalmazásnaplózást írási függvény alkalmazását konfigurálja:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Adatfolyam-naplózás kimeneti a függvényalkalmazás az Azure CLI-vel, nyisson meg egy új parancssort, a Bash vagy a terminál-munkamenetet, és adja meg a következő parancsot:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
A [az webapp log tail](/cli/azure/webapp/log) parancsnak szűrése a kimenethez használt beállításai az `--provider` lehetőséget. 

A naplófájlok-ként való letöltéséhez az Azure CLI használatával egyetlen ZIP-fájlban, nyisson meg egy új parancssort, a Bash vagy a terminál-munkamenetet, és írja be a következő parancsot:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Fájlrendszer naplózása az Azure Portal vagy Azure CLI-vel a parancs futtatása előtt van engedélyezve.

## <a name="environment-variables"></a>Környezeti változók

A függvények [Alkalmazásbeállítások](https://docs.microsoft.com/en-us/azure/azure-functions/functions-app-settings), például a szolgáltatás kapcsolati karakterláncok, ki vannak téve környezeti változókként végrehajtása során. Ezeket a beállításokat a, elérheti `System.getenv("AzureWebJobsStorage")`

Példa:

Adjon hozzá [Alkalmazásbeállítás](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings) neve testAppSetting és érték testAppSettingValue az

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>További lépések

Azure-függvény Java-fejlesztésekhez kapcsolatos további információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
- Helyi fejlesztés és a hibakeresési [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), és [Eclipse](functions-create-maven-eclipse.md). 
* [Távoli hibakeresés Java az Azure Functions Visual Studio Code-dal](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven beépülő modul az Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) -függvény létrehozása révén egyszerűsíthetők a `azure-functions:add` cél és a egy átmeneti könyvtárat a előkészítése [ZIP fájl központi telepítési](deployment-zip-push.md).
