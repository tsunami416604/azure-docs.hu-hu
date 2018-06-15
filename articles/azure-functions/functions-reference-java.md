---
title: Java fejlesztői dokumentáció az Azure Functions |} Microsoft Docs
description: Megtudhatja, hogyan működik együtt a Java fejlesztéséhez.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, Funkciók, Eseményfeldolgozási, webhookokkal, a dinamikus számítási, a kiszolgáló nélküli architektúra, a java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 3f63cb5a16b74458f9b53fddaea13a61ec1196a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31514019"
---
# <a name="azure-functions-java-developer-guide"></a>Az Azure Functions Java fejlesztői útmutató

## <a name="programming-model"></a>A programozási modell 

Az Azure-függvény dolgozza fel a bemeneti és a kimenetet állapotmentes osztály metódus kell lennie. Bár a metódusok írni engedélyezettek, a függvény nem függhet osztály minden példánya mezőt. Minden függvény módszerek rendelkeznie kell egy `public` access módosítóval.

## <a name="triggers-and-annotations"></a>Eseményindítók és jegyzetek

Egy Azure függvény általában egy külső eseményindító miatt hívják. A függvény kell, hogy az eseményindító és társított bemenet feldolgozása, majd előállítanak egy vagy több kimenetek.

Java jegyzetek szerepelnek a `azure-functions-java-core` csomag bemeneti és kimeneti kötése a metódusok. A támogatott bemeneti eseményindítók és a kimeneti jegyzetek kötés a következő táblázat tartalmazza:

Kötelező | Megjegyzés
---|---
CosmosDB | –
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | –
Notification Hubs | –
Storage Blob | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Tárolási üzenetsor | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Tárolási tábla | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Időzítő | <ul><li>`TimerTrigger`</li></ul>
Twilio | –

Eseményindító bemenetekhez és kimenetekhez is lehet meghatározni a [function.json](/azure/azure-functions/functions-reference#function-code) az alkalmazáshoz.

> [!IMPORTANT] 
> Konfigurálnia kell egy Azure Storage-fiókot a [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) Azure Storage-Blobba, sor vagy tábla eseményindítókat helyi futtatásához.

A példa jegyzetek használatával:

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

A jegyzetek nélkül írt azonos funkciót:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

a megfelelő `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
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

## <a name="data-types"></a>Adattípusok

Szabadon Java nyelven adattípusok használata a bemeneti és kimeneti adatok, beleértve a natív típusai; a testreszabott Java típusok és a speciális Azure típusokkal `azure-functions-java-core` csomag. Az Azure Functions futásidejű kísérletek kérte a kódot típus érkezett a bemeneti konvertálni.

### <a name="strings"></a>Karakterláncok

Rendszer leadott függvénynek átadott értékek karakterláncok, ha a megfelelő bemeneti paraméter típusa, a függvény típusú `String`. 

### <a name="plain-old-java-objects-pojos"></a>TEENDŐ Pojo objektumait (POJOs)

Ha a bemeneti függvény metódus számít, hogy Java JSON formázott karakterláncok Java típusok típuskonverziót végrehajtani. Az átalakításhoz lehetővé teszi a funkciók JSON-bemenet továbbítja, és anélkül, hogy az Ön saját kódját az átalakítás végrehajtásához a kódban Java tárolóhelytípussal működik.

Bemenetek funkciók kell azonos használt pojo-vá típusok `public` hozzáférés-módosító azok használatban lévő függvény módszerként. Nem kell deklarálnia pojo-vá osztály mezők `public`. Például egy JSON-karakterláncban `{ "x": 3 }` képes-e a következő pojo-vá típusúra konvertálni:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Bináris adatok

Bináris adatok egy jelenik meg egy `byte[]` az Azure functions kódban. Bináris bemeneti vagy kimeneti kötése a funkciók úgy, hogy a `dataType` mező mellett a function.json való `binary`:

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Ezután használhatja a funkciókódot:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Használjon `OutputBinding<byte[]>` egy bináris kimeneti a kötés típusa.


## <a name="function-method-overloading"></a>Függvény metódus túl van terhelve

Túlterhelési függvény módszerek ugyanazzal a névvel, de különböző típusú engedélyezett. Például rendelkezhet mindkét `String echo(String s)` és `String echo(MyType s)` egy osztályt, és az Azure Functions futásidejű úgy dönt, hogy melyiket meghívásához vizsgálja meg a tényleges bemeneti típus (HTTP, a bemeneti MIME-típushoz tartozó `text/plain` vezet `String` közben `application/json` jelöli `MyType`).

## <a name="inputs"></a>Bemenetek

Bemeneti az Azure Functions két kategóriába oszthatók: egy eseményindító bemeneti, a másik a további adatok. Bár ezek eltérnek a `function.json`, a használati megegyezik a Java-kódot. Vegyük a következő kódrészlet példa:

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;
import java.util.Optional;

public class MyClass {
    public static String echo(Optional<String> in, @BindingName("item") MyObject obj) {
        return "Hello, " + in.orElse("Azure") + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

A `@BindingName` jegyzet fogad el egy `String` tulajdonság, amely a kötés/eseményindító nevét megadva a `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

Ez a funkció meghívásakor a HTTP-kérések forgalma átadja egy nem kötelező, `String` argumentum `in` és az Azure Table Storage `MyObject` típus átadott argumentum `obj`. Használja a `Optional<T>` bemeneti adatok kezeléséhez a függvényekké, amely lehet null értékű típus.

## <a name="outputs"></a>Kimenetek

Kimenetek lehetnek, mind a visszatérési érték vagy kimeneti paraméterei. Ha csak egy kimeneti, javasoljuk, hogy az eredményül kapott értéket használja. A több kimenet kell használni a kimeneti paraméterek.

Visszatérési értéke, a legegyszerűbb kimeneti, csak bármilyen típusú értéket adja vissza, és Azure Functions futtatókörnyezettel megpróbálja átadásra azt vissza a a tényleges típus (például egy HTTP-válasz). A `functions.json`, használhat `$return` a kimeneti kötés nevével.

Több kimeneti érték létrehozásához használja `OutputBinding<T>` definiált típus a `azure-functions-java-core` csomag. Ha egy HTTP-válasz és a leküldéses üzenet annak a várólistára is van szüksége, megírhatja hasonlót:

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputBinding;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, 
    @QueueOutput(queueName = "messages", connection = "AzureWebJobsStorage", name = "queue") OutputBinding<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

érdemes definiálni, amely a kimeneti kötések `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "queue",
      "direction": "out",
      "queueName": "messages",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>Speciális típusokat

Néha egy függvény részletes ellenőrzése alatt tartja a be- és kimenetekkel. Típusok a kifejezetten a `azure-functions-java-core` csomag célokat szolgálnak, hogy a kérelem adatok kezelésére, és testre szabni a HTTP-eseményindítóval visszaadott állapotát:

| Speciális típusa      |       Cél        | Tipikus használati                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-eseményindítóval     | Módszer, fejlécek vagy lekérdezések beolvasása |
| `HttpResponseMessage<T>` | Kötelező HTTP-kimenet | 200 eltérő visszatérési állapota   |

> [!NOTE] 
> Is `@BindingName` jegyzet HTTP-fejlécek és a lekérdezések. Például `@BindingName("name") String query` megismétli a HTTP-kérelmek fejléceinek és a lekérdezéseket, és adja át ezt az értéket a metódusnak. Például `query` lesz `"test"` Ha a kérelem URL-cím `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metaadatok

Metaadatok származik a különböző forrásokból, például a HTTP-fejléceket, a HTTP-lekérdezések, és [metaadatok indítás](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Használja a `@BindingName` Megjegyzés a metaadat-neve, ahonnan az értéket be kell együtt.

Például a `queryValue` a következő kódot a részlet lesz `"test"` a kért URL-címe `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.serverless.functions.annotation.*;

public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="functions-execution-context"></a>Funkciók végrehajtási környezet

Működik együtt az Azure Functions végrehajtási környezet keresztül a `ExecutionContext` megadott objektumot a `azure-functions-java-core` csomag. Használja a `ExecutionContext` objektum meghívása és futásidejű adatokat funkciók használata a kódban.

### <a name="logging"></a>Naplózás

A funkciók futásidejű naplózó is keresztül elérhető a `ExecutionContext` objektum. A naplózó az Azure-figyelő kötődik, és lehetővé teszi a függvény végrehajtása során kapott jelző figyelmeztetések és hibák.

Ha a kérés törzsében kapott üres, akkor az alábbi példakód figyelmeztető üzenetet rögzít.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="environment-variables"></a>Környezeti változók

Gyakran kívánatos forráskód biztonsági okokból a titkos adatok kibontásához. Ez lehetővé teszi, hogy közzé tenni forrás kód repók véletlenül más fejlesztők számára a hitelesítő adatok megadása nélkül. Ez egyszerűen használatával környezeti változókat, mind az Azure Functions a helyi futtatás során, és a funkciók Azure történő telepítéskor elérhető.

– Könnyedén telepíthető a környezeti változók, ha fut az Azure Functions helyileg, választhatja, hogy ezek a változók hozzáadása a local.settings.json fájlhoz. Ha még nincs jelen, a függvény projekt gyökérkönyvtárában, nyugodtan hozzon létre egyet. Íme, mi a fájl hasonlóan kell kinéznie:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Minden egyes kulcs / érték-hozzárendelését a `values` térkép elérhetik futásidőben, egy környezeti változó, elérhető meghívásával `System.getenv("<keyname>")`, például `System.getenv("AzureWebJobsStorage")`. Hozzáadás további kulcs / érték párok, és lehet ajánlott eljárás.

> [!NOTE]
> Ha ezt a módszert használja, lehet, hogy figyelembe kell venni, hogy a local.settings.json hozzáadása a tárházhoz fájl figyelmen kívül fájl, hogy nincs véglegesítve.

A kód most attól függően, hogy ezek a környezeti változók bejelentkezhet ugyanazzal a kulccsal beállítása / érték párok az függvény alkalmazás beállításaiban, az Azure portálra, hogy a kód év működjön a tesztelés helyileg és amikor központi telepítésekor az Azure-bA.

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* [Távoli hibakeresési Java Azure működik együtt a Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
