---
title: Java fejlesztői útmutató az Azure Functionshez
description: Ismerje meg, hogyan fejleszthet funkciókat a Java segítségével.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4af2a860657f6066112146e1f88d81861d9430ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276750"
---
# <a name="azure-functions-java-developer-guide"></a>Az Azure Functions Java fejlesztői útmutatója

Az Azure Functions futásidejű támogatja a [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Ez az útmutató az Azure Functions Java-val való írásának bonyolultságáról tartalmaz információkat.

Más nyelvekhez, úgy, hogy egy függvényalkalmazásnak egy vagy több funkciója is lehet. A Java függvény `public` egy módszer, díszített `@FunctionName`a jegyzet . Ez a módszer határozza meg egy Java függvény bejegyzését, és egyedinek kell lennie egy adott csomagban. A Java nyelven írt egyik függvényalkalmazás nak több osztálya `@FunctionName`is lehet, több nyilvános metódussal a cikkhez jegyzetekkel.

Ez a cikk feltételezi, hogy már elolvasta az [Azure Functions fejlesztői hivatkozását.](functions-reference.md) A Functions rövid útmutatót is el kell végeznie az első függvény létrehozásához a [Visual Studio Code](functions-create-first-function-vs-code.md) vagy a [Maven](functions-create-first-java-maven.md)használatával.

## <a name="programming-model"></a>A programozási modell 

Az [eseményindítók és kötések fogalmai](functions-triggers-bindings.md) alapvető fontosságúak az Azure Functions számára. Az eseményindítók elindítják a kód végrehajtását. A kötések leteszik az adatok átadását és a függvényből származó adatok visszaadását anélkül, hogy egyéni adatelérési kódot kellene írniuk.

## <a name="create-java-functions"></a>Java függvények létrehozása

A Java-függvények létrehozásának megkönnyítése érdekében vannak Olyan Maven-alapú eszközök és archetípusok, amelyek előre definiált Java-sablonokat használnak, hogy segítsenek projekteket létrehozni egy adott függvényeseményindítóval.    

### <a name="maven-based-tooling"></a>Maven-alapú szerszámozás

A következő fejlesztői környezetek rendelkeznek az Azure Functions eszközzel, amely lehetővé teszi java függvényprojektek létrehozását: 

+ [Visual Studio kód](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

A fenti cikk hivatkozások megmutatják, hogyan lehet létrehozni az első funkciók segítségével ide a választás. 

### <a name="project-scaffolding"></a>Projekt állványzat

Ha a terminálparancssor-fejlesztést részesíti előnyben, a Java-alapú függvényprojektek legegyszerűbb `Apache Maven` módja az archetípusok használata. Jelenleg két Függvény archetípus létezik a Maven-hez:

+ **Java Archetype**: a következő groupId és artifactId [com.microsoft.azure:azure-functions-archetype alatt](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/)közzétéve:

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    Az archetípus használatának első lépéseiről a [Java rövid útmutatója](functions-create-first-java-maven.md)látható. 

+ **Kotlin Archetype (Preview)** a következő groupId és artifactId [com.microsoft.azure:azure-functions-kotlin-archetípus](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/)alatt:

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-kotlin-archetype
    ```

Ezeknek az archetípusoknak a forráskódja megtalálható az [Azure Maven Archetypes GitHub-tárházban.](https://github.com/microsoft/azure-maven-archetypes)


## <a name="folder-structure"></a>Mappastruktúra

Az Azure Functions Java projekt mappastruktúrája:

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

_* A Kotlin projekt nagyon hasonlít, mivel még mindig Maven_

A függvényalkalmazás konfigurálásához használhat megosztott [host.json](functions-host-json.md) fájlt. Minden függvény saját kódfájllal (.java) és kötési konfigurációs fájllal (function.json) rendelkezik.

A projektben több függvény is behelyezhető. Ne helyezze a funkciókat külön üvegekbe. A `FunctionApp` célkönyvtárban, mi lesz telepítve a függvényalkalmazás az Azure-ban.

## <a name="triggers-and-annotations"></a>Eseményindítók és jegyzetek

 A függvényeket egy eseményindító hívja meg, például egy HTTP-kérelem, egy időzítő vagy egy adatfrissítés. A függvénynek fel kell dolgoznia az eseményindítót és minden más bemenetet egy vagy több kimenet létrehozásához.

Használja a [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) csomagban található Java-jegyzeteket a bemeneti és kimeneti műveletek metódusokhoz kötéséhez. További információt a [Java referenciadokumentumaiban talál.](/java/api/com.microsoft.azure.functions.annotation)

> [!IMPORTANT] 
> Konfigurálnia kell egy Azure Storage-fiókot a [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) az Azure Blob storage, az Azure Queue storage vagy az Azure Table storage helyi aktiválások futtatásához.

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

Itt van a `function.json` generált megfelelő az [azure-functions-maven-plugin:](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)

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

## <a name="jdk-runtime-availability-and-support"></a>A JDK futásidejű elérhetősége és támogatása 

A Java függvényalkalmazások helyi fejlesztéséhez töltse le és használja az [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK-kat az [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)rendszerből. Az Azure Functions az Azul Java 8 JDK futásidejű, amikor telepíti a függvényalkalmazásokat a felhőbe.

A JDK-kkal és a függvényalkalmazásokkal kapcsolatos problémák [azure-támogatása](https://azure.microsoft.com/support/) [minősített támogatási csomaggal](https://azure.microsoft.com/support/plans/)érhető el.

## <a name="customize-jvm"></a>JVM testreszabása

A Függvények lehetővé teszi a Java-függvények futtatásához használt Java virtuális gép (JVM) testreszabását. Alapértelmezés szerint a [következő JVM-beállítások](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) at használja a rendszer:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

A megadott alkalmazásbeállításban további argumentumokat adhat meg. `JAVA_OPTS` Az Azure Portalon vagy az Azure CLI-ben üzembe helyezett függvényalkalmazáshoz hozzáadhat alkalmazásbeállításokat.

### <a name="azure-portal"></a>Azure portál

Az [Azure Portalon](https://portal.azure.com)az [Alkalmazás beállításai](functions-how-to-use-azure-function-app-settings.md#settings) `JAVA_OPTS` lapon adja hozzá a beállítást.

### <a name="azure-cli"></a>Azure CLI

Használhatja az [az functionapp config appsettings set parancsot,](/cli/azure/functionapp/config/appsettings) hogy állítsa be `JAVA_OPTS`, mint a következő példában:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Ez a példa lehetővé teszi a fej nélküli módot. Cserélje `<APP_NAME>` le a függvényalkalmazás nevét `<RESOURCE_GROUP>` és az erőforráscsoportot.

> [!WARNING]  
> A [Felhasználás tervben](functions-scale.md#consumption-plan)a `WEBSITE_USE_PLACEHOLDER` értéket a értékkel kell hozzáadnia. `0`  
Ez a beállítás növeli a Java-függvények hidegindítási idejét.

## <a name="third-party-libraries"></a>Külső könyvtárak 

Az Azure Functions támogatja a külső könyvtárak használatát. Alapértelmezés szerint a projektfájlban `pom.xml` megadott összes függőség automatikusan [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) bekerül a programba a cél alatt. A `pom.xml` fájlban függőségként nem megadott tárak esetében `lib` helyezze őket a függvény gyökérkönyvtárának könyvtárába. A `lib` címtárban elhelyezett függőségek futásidőben kerülnek a rendszerosztály betöltőjéhez.

A `com.microsoft.azure.functions:azure-functions-java-library` függőség alapértelmezés szerint a classpath,, és nem kell szerepeltetni a `lib` címtárban. Emellett [az azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) hozzáadja az [itt](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) felsorolt függőségeket az osztályelérési úthoz.

## <a name="data-type-support"></a>Adattípus-támogatás

Használhatja egyszerű régi Java objektumok (POJOs), `azure-functions-java-library`típusú definiált , vagy primitív adattípusok, mint a String és Egész a bemeneti vagy kimeneti kötések.

### <a name="pojos"></a>POJOS

A bemeneti adatok POJO-vá történő konvertálásához [az azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) a [gson-könyvtárat](https://github.com/google/gson) használja. A függvények bemeneteként használt `public`POJO-típusoknak kell lenniük.

### <a name="binary-data"></a>Bináris adatok

Bináris bemenetek vagy kimenetek kötése a alkalmazáshoz úgy, hogy `byte[]`a function.json mezőjét a `dataType` következőre `binary`állítja:

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

Ha null értékeket `Optional<T>`vár, használja a használatát.

## <a name="bindings"></a>Kötések

A bemeneti és kimeneti kötések deklaratív módot biztosítanak a kódon belüli adatokhoz való csatlakozásra. Egy függvény több bemeneti és kimeneti kötéssel is rendelkezhet.

### <a name="input-binding-example"></a>Példa bemeneti kötésre

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

Ezt a függvényt HTTP-kérelemmel hívja meg. 
- A HTTP-kérelem hasznos `String` adata `inputReq`az argumentum a.a.
- Az egyik bejegyzést a table storage-ból `TestInputData` olvassa `inputData`be a rendszer, és az argumentumot illetően a program továbbítja a programot.

A bemenetek kötegének fogadásához a `String[]` `POJO[]`, `List<String>`a `List<POJO>`, a .

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

Ez a függvény akkor aktiválódik, ha új adatok vannak a konfigurált eseményközpontban. Mivel `cardinality` a beállítás `MANY`a beállítás, a függvény üzenetek kötegét fogadja az eseményközpontból. `EventData`az eseményközpontból `TestEventData` a függvény végrehajtásához konvertálódik.

### <a name="output-binding-example"></a>Példa kimenetkötésre

A használatával kimeneti kötést köthet a visszatérési `$return`értékhez. 

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

Ha több kimeneti kötés van, csak az egyikhez használja a visszatérési értéket.

Több kimeneti érték `OutputBinding<T>` küldéséhez használja `azure-functions-java-library` a csomagban definiált értéket. 

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

Ezt a függvényt httprequest-en hívja meg. Több értéket ír a várólista-tárolóba.

## <a name="httprequestmessage-and-httpresponsemessage"></a>httpRequestMessage és HttpResponseMessage

 Ezeket a. `azure-functions-java-library`határozza meg. Ők segítő típusú dolgozni HttpTrigger funkciókat.

| Speciális típus      |       Cél        | Tipikus használat                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-trigger     | Metódus, fejlécek vagy lekérdezések beolvasása |
| `HttpResponseMessage` | HTTP kimenetkötés | 200-tól eltérő állapotot ad eredményül.   |

## <a name="metadata"></a>Metaadatok

Kevés eseményindítók küld [eseményindító metaadatok](/azure/azure-functions/functions-triggers-bindings) mellett bemeneti adatokat. A jegyzet `@BindingName` segítségével kötéssel aktiválhatja a metaadatokat.


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
Az előző példában `queryValue` a http-kérelem `name` URL-címében lévő `http://{example.host}/api/metadata?name=test`lekérdezési karakterlánc paraméterhez van kötve. Íme egy másik példa, amely `Id` bemutatja, hogyan köthető kontegyünk a várólista-aktiválási metaadatokhoz.

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
> A jegyzetben megadott névnek meg kell egyeznie a metaadat-tulajdonsággal.

## <a name="execution-context"></a>Végrehajtási környezet

`ExecutionContext`, a `azure-functions-java-library`ban definiált a , segítő módszereket tartalmaz a függvények futásidejű kommunikációjára.

### <a name="logger"></a>Tuskózó

A `getLogger`, a `ExecutionContext`definiált használatával naplókat írhat a függvénykódból.

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

Az Azure CLI segítségével streamelheti a Java stdout és stderr naplózás, valamint más alkalmazásnaplózás. 

A következőképpen konfigurálhatja a függvényalkalmazást az alkalmazásnaplózás írására az Azure CLI használatával:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Ha az Azure CLI használatával naplózhatja a naplózási kimenetet a függvényalkalmazáshoz, nyisson meg egy új parancssort, a Bash vagy a Terminal munkamenetet, és írja be a következő parancsot:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Az [az webapp log tail](/cli/azure/webapp/log) parancs a `--provider` beállítás használatával szűrheti a kimenetet. 

Ha a naplófájlokat egyetlen ZIP-fájlként szeretné letölteni az Azure CLI használatával, nyisson meg egy új parancssort, a Bash vagy a Terminal munkamenetet, és írja be a következő parancsot:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

A parancs futtatása előtt engedélyeznie kell a fájlrendszer naplózását az Azure Portalon vagy az Azure CLI-ben.

## <a name="environment-variables"></a>Környezeti változók

A Funkciók alkalmazásban [az alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatáskapcsolati karakterláncok környezeti változókként jelennek meg a végrehajtás során. Ezeket a beállításokat `System.getenv("AzureWebJobsStorage")`a használatával érheti el.

A következő példa az [alkalmazás beállítást](functions-how-to-use-azure-function-app-settings.md#settings) `myAppSetting`kapja meg, amelynek neve a kulcs:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>További lépések

Az Azure Functions Java fejlesztéséről az alábbi forrásokban talál további információt:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions aktiválódik és kötéseket köt](functions-triggers-bindings.md)
* Helyi fejlesztés és hibakeresés a [Visual Studio Code,](https://code.visualstudio.com/docs/java/java-azurefunctions) [az IntelliJ](functions-create-maven-intellij.md)és [az Eclipse segítségével](functions-create-maven-eclipse.md)
* [Távoli debug Java Azure-függvények visual studio kóddal](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven beépülő modul az Azure Functionshez](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Egyszerűsítse a `azure-functions:add` függvények létrehozását a célon keresztül, és készítsen elő egy átmeneti könyvtárat a [ZIP-fájlok központi telepítéséhez.](deployment-zip-push.md)
