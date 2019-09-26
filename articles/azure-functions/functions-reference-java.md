---
title: Java fejlesztői segédlet a Azure Functionshoz | Microsoft Docs
description: Ismerje meg, hogyan fejlesztheti a függvényeket a Javával.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, functions, Event Processing, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra, Java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: e3ab825fbf5b5dba74b67eaa894a38c74ed0b62a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299393"
---
# <a name="azure-functions-java-developer-guide"></a>A Java fejlesztői útmutató Azure Functions

A Azure Functions futtatókörnyezet támogatja a [Java SE 8 LTS (Zulu 8.31.0.2-JRE 8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/)használatát. Ez az útmutató a Java-Azure Functions írásának bonyolult adatait tartalmazza.

Ahogy más nyelveken is előfordul, egy függvényalkalmazás lehet, hogy egy vagy több függvényt tartalmaz. A Java-függvények `public` a `@FunctionName`jegyzetekkel díszített metódusok. Ez a metódus definiálja a Java-függvények bejegyzését, és egyedinek kell lennie egy adott csomagban. A Java-ban írt függvényalkalmazás több olyan osztállyal rendelkezhet, amelyekben több nyilvános metódus `@FunctionName`is szerepel.

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői referenciát](functions-reference.md). Az első függvény létrehozásához a [Visual Studio Code](functions-create-first-function-vs-code.md) vagy a [Maven](functions-create-first-java-maven.md)használatával is végre kell hajtania a functions rövid útmutatót.

## <a name="programming-model"></a>A programozási modell 

Az [Eseményindítók és kötések](functions-triggers-bindings.md) fogalmai alapvető fontosságúak a Azure functions. Elindítja a kód végrehajtását. A kötések lehetővé teszik a függvények adatainak átadását és az adatok visszaküldését anélkül, hogy egyéni adatelérési kódot kellene írnia.

## <a name="project-scaffolding"></a>Projekt – Állványzat

Egy Java-alapú Azure Function-projekt bevezető legegyszerűbb módja az archetípusok `Apache Maven` használata. A Project generálási varázslók a Visual Studio Code-ban, valamint az Eclipse-hez és a IntelliJ készült Azure-eszközkészleteket is megtalálják.

Jelenleg két Azure Functions archetípus a Maven számára:

### <a name="java-archetype"></a>Java-archetípus

Ez a archetípus a következő groupId és artifactId ( [com. microsoft. Azure) címen érhető el: Azure-functions-archetípus](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/).

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="kotlin-archetype-preview"></a>Kotlin archetípus (előzetes verzió)

Ez a archetípus a következő groupId és artifactId ( [com. microsoft. Azure) címen érhető el: Azure-functions-Kotlin-archetípus](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/).

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

Ezen archetípusok forráskódját az [Azure Maven archetípusok GitHub-tárházában](https://github.com/microsoft/azure-maven-archetypes)találja.

## <a name="folder-structure"></a>Mappa szerkezete

Itt látható egy Azure Functions Java-projekthez tartozó mappa szerkezete:

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

_* A Kotlin-projekt nagyon hasonlónak tűnik, mivel még Maven_

A Function alkalmazást a Shared [Host. JSON](functions-host-json.md) fájl használatával konfigurálhatja. Mindegyik függvényhez saját kódlap (. Java) és kötési konfigurációs fájl (function. JSON) tartozik.

Egy projektben több függvény is elhelyezhető. Kerülje a függvények különálló tégelybe való elhelyezését. A `FunctionApp` cél könyvtárban az Azure-beli Function alkalmazás üzembe helyezése történik.

## <a name="triggers-and-annotations"></a>Eseményindítók és jegyzetek

 A függvényeket egy eseményindító hívja meg, például egy HTTP-kérelem, egy időzítő vagy egy frissítés az adatokhoz. A függvénynek fel kell dolgoznia az triggert és minden más bemenetet, hogy egy vagy több kimenetet hozzon létre.

Használja a [com. microsoft. Azure. functions. Megjegyzés. *](/java/api/com.microsoft.azure.functions.annotation) csomagban található Java-megjegyzéseket a bemenetek és kimenetek a metódusokhoz való kötéséhez. További információ: [Java-referenciák dokumentációja](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Konfigurálnia kell egy Azure Storage-fiókot a [Local. Settings. JSON](/azure/azure-functions/functions-run-local#local-settings-file) fájlban az Azure Blob Storage, az Azure üzenetsor-tároló vagy az Azure Table Storage-eseményindítók helyi futtatásához.

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

Az `function.json` [Azure-functions-Maven-beépülő modul](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)által generált adatok:

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

## <a name="jdk-runtime-availability-and-support"></a>A JDK futtatókörnyezet rendelkezésre állása és támogatása 

A Java functions-alkalmazások helyi fejlesztéséhez töltse le és használja a Azul [Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK az [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)-től. A Azure Functions az Azul Java 8 JDK futtatókörnyezetet használja a Function apps Felhőbeli üzembe helyezése során.

Az [Azure-támogatás](https://azure.microsoft.com/support/) a JDK és a Function alkalmazással kapcsolatos problémákhoz egy [minősített támogatási csomaggal](https://azure.microsoft.com/support/plans/)érhető el.

## <a name="customize-jvm"></a>JVM testreszabása

A functions lehetővé teszi a Java-függvények futtatásához használt Java virtuális gép (JVM) testreszabását. Alapértelmezés szerint a [következő JVM-beállításokat](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) használja a rendszer:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

További argumentumokat is megadhat egy nevű `JAVA_OPTS`alkalmazás-beállításban. Az Azure-ban üzembe helyezett Function-alkalmazáshoz az Azure Portal vagy az Azure CLI-ben adhat hozzá Alkalmazásbeállítások.

### <a name="azure-portal"></a>Azure Portal

A `JAVA_OPTS` [Azure Portal](https://portal.azure.com)a beállítás hozzáadásához használja az [Alkalmazásbeállítások lapot](functions-how-to-use-azure-function-app-settings.md#settings) .

### <a name="azure-cli"></a>Azure CLI

Az az [functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) paranccsal állíthatja be `JAVA_OPTS`a következő példát, ahogy az az alábbi példában is látható:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Ez a példa a fej nélküli üzemmódot teszi lehetővé. Cserélje `<APP_NAME>` le a nevet a Function alkalmazás nevére, és `<RESOURCE_GROUP>` az erőforráscsoport elemre.

> [!WARNING]  
> A használati [tervben](functions-scale.md#consumption-plan)a `WEBSITE_USE_PLACEHOLDER` beállítást a értékkel `0`kell kiegészíteni.  
Ezzel a beállítással növelhető a Java-függvények hideg indítási ideje.

## <a name="third-party-libraries"></a>Harmadik féltől származó kódtárak 

A Azure Functions támogatja a külső gyártótól származó kódtárak használatát. Alapértelmezés szerint a rendszer a projektfájl `pom.xml` összes függőségét automatikusan becsomagolja a [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cél alatt. A `pom.xml` fájlban függőségként nem megadott könyvtárak esetében helyezze azokat `lib` a függvény gyökérkönyvtárában található könyvtárba. A `lib` címtárban elhelyezett függőségek a rendszerosztály-betöltőhöz lesznek hozzáadva futásidőben.

A `com.microsoft.azure.functions:azure-functions-java-library` függőség a osztályútvonal alapértelmezés szerint van megadva, és nem kell szerepelnie a `lib` címtárban. Az [Azure-functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) az [itt](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) felsorolt függőségeket is hozzáadja a osztályútvonal.

## <a name="data-type-support"></a>Adattípusok támogatása

Használhat egyszerű, régi Java-objektumokat (szerializálói), definiált `azure-functions-java-library`típusokat vagy primitív adattípusokat, például karakterláncot és egész számot a bemeneti vagy kimeneti kötésekhez való kötéshez.

### <a name="pojos"></a>Szerializálói

A bemeneti adatok POJO alakításához az [Azure-functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) a [gson](https://github.com/google/gson) könyvtárat használja. A függvények bemenetként használt POJO-típusok legyenek `public`.

### <a name="binary-data"></a>Bináris adatok

A bináris bemenetek és kimenetek `byte[]` `dataType` kötése a következőhöz `binary`:

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

Ha a rendszer null értékeket vár, `Optional<T>`használja a értéket.

## <a name="bindings"></a>Kötések

A bemeneti és kimeneti kötések deklaratív módszert biztosítanak a kódban lévő adatokhoz való kapcsolódáshoz. A függvények több bemeneti és kimeneti kötéssel is rendelkezhetnek.

### <a name="input-binding-example"></a>Bemeneti kötési példa

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

Ezt a függvényt egy HTTP-kérelemmel hívja meg. 
- Az argumentumhoz `inputReq`a HTTP-kérelem `String` adattartalmát adja át a rendszer.
- A rendszer beolvas egy bejegyzést a Table Storage-ból, és `TestInputData` az argumentumként `inputData`adja át.

Bemeneti kötegek fogadásához a következőhöz köthető `String[]` `POJO[]` `List<String>`:,, vagy `List<POJO>`.

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

Ez a függvény akkor aktiválódik, amikor új adat van a konfigurált Event hub-ban. Mivel a `cardinality` `MANY`értéke, a függvény egy köteg üzenetet fogad az Event hub-ból. `EventData`az Event hub-ból a `TestEventData` függvény végrehajtásához konvertálva lesz.

### <a name="output-binding-example"></a>Kimeneti kötési példa

A paranccsal `$return`kötést adhat hozzá a visszatérési értékhez. 

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

Ha több kimeneti kötés is van, akkor a visszatérési értéket csak az egyikre használja.

Több kimeneti érték küldéséhez használja `OutputBinding<T>` a `azure-functions-java-library` csomagban definiált értéket. 

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

Ezt a függvényt egy HttpRequest hívja meg. Több értéket ír a várólista-tárolóba.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage és HttpResponseMessage

 Ezek a ben `azure-functions-java-library`vannak meghatározva. A HttpTrigger functions szolgáltatással használható segítő típusok.

| Speciális típus      |       Target        | Jellemző használat                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-trigger     | Metódusok, fejlécek vagy lekérdezések beolvasása |
| `HttpResponseMessage` | HTTP kimeneti kötés | A 200-tól eltérő állapotot ad vissza.   |

## <a name="metadata"></a>Metaadatok

Néhány eseményindító elküldi az [eseményindító-metaadatokat](/azure/azure-functions/functions-triggers-bindings) a bemeneti adatokkal együtt. A megjegyzésekkel `@BindingName` kötést használhat a trigger metaadataihoz.


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
Az előző példában a `queryValue` a HTTP-kérelem URL-címében `http://{example.host}/api/metadata?name=test`a `name` lekérdezési karakterlánc paraméterhez van kötve. Íme egy másik példa, amely bemutatja, hogyan lehet `Id` kötést létrehozni a várólista-trigger metaadatainak használatával.

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
> A jegyzetben megadott névnek egyeznie kell a metadata tulajdonsággal.

## <a name="execution-context"></a>Végrehajtási környezet

`ExecutionContext`a `azure-functions-java-library`ben definiált, segítő metódusokat tartalmaz a functions futtatókörnyezettel folytatott kommunikációhoz.

### <a name="logger"></a>Naplózó

A `getLogger`-ben `ExecutionContext`definiált, a naplókat a függvény kódjából való írásához használhatja.

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

## <a name="view-logs-and-trace"></a>Naplók és nyomkövetés megtekintése

Az Azure CLI-vel a Java stdout és a stderr naplózását, valamint más alkalmazások naplózását is elvégezheti. 

Az alábbi módon konfigurálhatja a Function alkalmazást az alkalmazások naplózásának az Azure CLI használatával történő írásához:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Ha az Azure CLI használatával szeretné továbbítani a függvény alkalmazásának naplózási kimenetét, nyisson meg egy új parancssort, bash-t vagy terminál-munkamenetet, és írja be a következő parancsot:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Az az [WebApp log farok](/cli/azure/webapp/log) paranccsal a `--provider` lehetőség használatával szűrheti a kimenetet. 

Ha az Azure CLI használatával egyetlen ZIP-fájlként szeretné letölteni a naplófájlokat, nyisson meg egy új parancssort, bash-t vagy terminál-munkamenetet, és írja be a következő parancsot:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

A parancs futtatása előtt engedélyeznie kell a fájlrendszer naplózását a Azure Portal vagy az Azure CLI-ben.

## <a name="environment-variables"></a>Környezeti változók

A függvények, az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai a végrehajtás során környezeti változókként jelennek meg. Ezeket a beállításokat a ( `System.getenv("AzureWebJobsStorage")`) segítségével érheti el.

A következő példa beolvassa az [alkalmazás beállítását](functions-how-to-use-azure-function-app-settings.md#settings)a nevű `myAppSetting`kulccsal:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>További lépések

Az Azure Functions Java-fejlesztéssel kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* Helyi fejlesztés és hibakeresés a [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), a [IntelliJ](functions-create-maven-intellij.md)és az [Eclipse](functions-create-maven-eclipse.md) használatával
* [Java Azure Functions távoli hibakeresése a Visual Studio Code-ban](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven beépülő modul a Azure Functionshoz](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Egyszerűsítse a függvények létrehozását a `azure-functions:add` cél segítségével, és készítse elő a [zip-fájl üzembe helyezésének](deployment-zip-push.md)előkészítési könyvtárát.
