---
title: Java fejlesztői referenciája Azure Functions
description: Ismerje meg, hogyan fejlesztheti a függvényeket a Javával.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4af2a860657f6066112146e1f88d81861d9430ea
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357055"
---
# <a name="azure-functions-java-developer-guide"></a>A Java fejlesztői útmutató Azure Functions

A Azure Functions Runtime támogatja a [Java SE 8 LTS (Zulu 8.31.0.2-JRE 8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Ez az útmutató a Java-Azure Functions írásának bonyolult adatait tartalmazza.

Ahogy más nyelveken is előfordul, egy függvényalkalmazás lehet, hogy egy vagy több függvényt tartalmaz. A Java-függvények `public` metódusok, amelyek a jegyzet `@FunctionName`. Ez a metódus definiálja a Java-függvények bejegyzését, és egyedinek kell lennie egy adott csomagban. A Java-ban írt függvényalkalmazás több olyan osztállyal is rendelkezhet, amelyekben a `@FunctionName`val megjegyzett több nyilvános módszer is szerepel.

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői referenciát](functions-reference.md). Az első függvény létrehozásához a [Visual Studio Code](functions-create-first-function-vs-code.md) vagy a [Maven](functions-create-first-java-maven.md)használatával is végre kell hajtania a functions rövid útmutatót.

## <a name="programming-model"></a>A programozási modell 

Az [Eseményindítók és kötések](functions-triggers-bindings.md) fogalmai alapvető fontosságúak a Azure functions. Elindítja a kód végrehajtását. A kötések lehetővé teszik a függvények adatainak átadását és az adatok visszaküldését anélkül, hogy egyéni adatelérési kódot kellene írnia.

## <a name="create-java-functions"></a>Java-függvények létrehozása

Ahhoz, hogy egyszerűbb legyen a Java-függvények létrehozása, az előre definiált Java-sablonokat használó Maven-alapú eszközök és az olyan archetípusok, amelyek segítségével meghatározott függvény-triggerrel hozhat létre projekteket.    

### <a name="maven-based-tooling"></a>Maven-alapú eszközök

A következő fejlesztői környezetek Azure Functions eszközzel rendelkeznek, amely lehetővé teszi a Java-függvények projektjeinek létrehozását: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

A fenti hivatkozások azt mutatják be, hogyan hozhatja létre az első függvényeit az IDE választása szerint. 

### <a name="project-scaffolding"></a>Projekt – Állványzat

Ha a terminálról szeretne parancssori fejlesztést használni, a Java-alapú függvények beépítésének legegyszerűbb módja a `Apache Maven` archetípusok használata. Jelenleg két függvény archetípusa van a Maven számára:

+ **Java-archetípus**: közzétett GroupID és artifactId [com. microsoft. Azure: Azure-functions-archetípus](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/):

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    A jelen archetípus használatának megkezdéséhez tekintse meg a [Java](functions-create-first-java-maven.md)rövid útmutatóját. 

+ **Kotlin archetípus (előzetes verzió)** , amely a következő GroupID és artifactId [com. microsoft. Azure-ban lett közzétéve: Azure-functions-Kotlin-archetípus](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/):

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

Egy projektben több függvény is elhelyezhető. Kerülje a függvények különálló tégelybe való elhelyezését. Az Azure-beli Function alkalmazás üzembe helyezéséhez a cél könyvtárában lévő `FunctionApp` lesz elérhető.

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

Itt látható az [Azure-functions-Maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)által generált megfelelő `function.json`:

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

További argumentumokat is megadhat egy `JAVA_OPTS`nevű alkalmazás-beállításban. Az Azure-ban üzembe helyezett Function-alkalmazáshoz az Azure Portal vagy az Azure CLI-ben adhat hozzá Alkalmazásbeállítások.

### <a name="azure-portal"></a>Azure Portal

A [Azure Portal](https://portal.azure.com)az [Alkalmazásbeállítások lapon](functions-how-to-use-azure-function-app-settings.md#settings) adja hozzá a `JAVA_OPTS` beállítást.

### <a name="azure-cli"></a>Azure CLI

Az az [functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) paranccsal állíthatja be a `JAVA_OPTS`t, ahogy az alábbi példában is látható:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Ez a példa a fej nélküli üzemmódot teszi lehetővé. Cserélje le a `<APP_NAME>`t a Function alkalmazás nevére, és `<RESOURCE_GROUP>` az erőforráscsoporthoz.

> [!WARNING]  
> A használati [tervben](functions-scale.md#consumption-plan)hozzá kell adnia a `WEBSITE_USE_PLACEHOLDER` beállítást `0`értékkel.  
Ezzel a beállítással növelhető a Java-függvények hideg indítási ideje.

## <a name="third-party-libraries"></a>Harmadik féltől származó kódtárak 

A Azure Functions támogatja a külső gyártótól származó kódtárak használatát. Alapértelmezés szerint a projektben `pom.xml` fájlban megadott összes függőség automatikusan a [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cél alatt lesz csomagolva. A `pom.xml` fájlban függőségként nem megadott könyvtárak esetében helyezze azokat a függvény gyökérkönyvtárában található `lib` könyvtárba. A `lib` könyvtárban elhelyezett függőségek a rendszerosztály-betöltőhöz lesznek hozzáadva futásidőben.

A `com.microsoft.azure.functions:azure-functions-java-library` függőség alapértelmezés szerint a osztályútvonal van megadva, és nem kell szerepelnie a `lib` könyvtárban. Az [Azure-functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) az [itt](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) felsorolt függőségeket is hozzáadja a osztályútvonal.

## <a name="data-type-support"></a>Adattípusok támogatása

Használhatja a régi Java-objektumokat (Szerializálói), a `azure-functions-java-library`ban definiált típusokat, illetve az egyszerű adattípusokat, például a karakterláncot és az egész számot, hogy a bemeneti vagy kimeneti kötésekhez kapcsolódjon.

### <a name="pojos"></a>Szerializálói

A bemeneti adatok POJO alakításához az [Azure-functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) a [gson](https://github.com/google/gson) könyvtárat használja. A függvények bemenetként használt POJO-típusoknak `public`nak kell lenniük.

### <a name="binary-data"></a>Bináris adatok

A bináris bemenetek és kimenetek összekötése `byte[]`a function. JSON fájl `dataType` mezőjének megadásával `binary`:

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

Ha a rendszer null értékeket vár, használja a `Optional<T>`.

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
- A HTTP-kérések adattartalmát a `inputReq`argumentum `String` adja át.
- A rendszer beolvas egy bejegyzést a Table Storage szolgáltatásból, és `TestInputData`ként adja át a `inputData`argumentumnak.

Ha bemeneti köteget szeretne fogadni, `String[]`hoz, `POJO[]`hoz, `List<String>`hoz vagy `List<POJO>`hoz is köthető.

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

Ez a függvény akkor aktiválódik, amikor új adat van a konfigurált Event hub-ban. Mivel a `cardinality` `MANY`re van beállítva, a függvény egy köteg üzenetet fogad az Event hub-ból. az Event hub `EventData` a függvény végrehajtásához `TestEventData` konvertálja a rendszer.

### <a name="output-binding-example"></a>Kimeneti kötési példa

A visszatérési értékhez a `$return`használatával köthető kimeneti kötés. 

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

Több kimeneti érték küldéséhez használja a `azure-functions-java-library` csomagban definiált `OutputBinding<T>`. 

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

 Ezek a `azure-functions-java-library`ben vannak meghatározva. A HttpTrigger functions szolgáltatással használható segítő típusok.

| Speciális típus      |       Cél        | Jellemző használat                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-trigger     | Metódusok, fejlécek vagy lekérdezések beolvasása |
| `HttpResponseMessage` | HTTP kimeneti kötés | A 200-tól eltérő állapotot ad vissza.   |

## <a name="metadata"></a>Metaadatok

Néhány eseményindító elküldi az [eseményindító-metaadatokat](/azure/azure-functions/functions-triggers-bindings) a bemeneti adatokkal együtt. A megjegyzési `@BindingName` segítségével kötést indíthat a metaadatokhoz.


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
Az előző példában a `queryValue` a HTTP-kérelem URL-címe `name` a lekérdezési karakterlánc paraméterhez van kötve, `http://{example.host}/api/metadata?name=test`. Íme egy másik példa, amely bemutatja, hogyan lehet kötést létrehozni az üzenetsor-trigger metaadatainak `Id`.

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

a `azure-functions-java-library`ban definiált `ExecutionContext`segítő metódusokat tartalmaz a functions futtatókörnyezettel való kommunikációhoz.

### <a name="logger"></a>Tuskózó

Használja a `ExecutionContext`ban definiált `getLogger`, hogy naplókat írjon a függvény kódjából.

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
Az az [WebApp log farok](/cli/azure/webapp/log) paranccsal szűrheti a kimenetet a `--provider` kapcsoló használatával. 

Ha az Azure CLI használatával egyetlen ZIP-fájlként szeretné letölteni a naplófájlokat, nyisson meg egy új parancssort, bash-t vagy terminál-munkamenetet, és írja be a következő parancsot:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

A parancs futtatása előtt engedélyeznie kell a fájlrendszer naplózását a Azure Portal vagy az Azure CLI-ben.

## <a name="environment-variables"></a>Környezeti változók

A függvények, az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai a végrehajtás során környezeti változókként jelennek meg. Ezeket a beállításokat a (`System.getenv("AzureWebJobsStorage")`) segítségével érheti el.

A következő példa beolvassa az [Alkalmazásbeállítások értékét](functions-how-to-use-azure-function-app-settings.md#settings)a `myAppSetting`nevű kulccsal:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Következő lépések

Az Azure Functions Java-fejlesztéssel kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* Helyi fejlesztés és hibakeresés a [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), a [IntelliJ](functions-create-maven-intellij.md)és az [Eclipse](functions-create-maven-eclipse.md) használatával
* [Java Azure Functions távoli hibakeresése a Visual Studio Code-ban](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven beépülő modul a Azure Functionshoz](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Egyszerűsítse a függvények létrehozását a `azure-functions:add` céljával, és készítse elő a [zip-fájlok üzembe helyezéséhez](deployment-zip-push.md)szükséges átmeneti könyvtárat.
