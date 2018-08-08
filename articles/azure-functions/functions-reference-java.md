---
title: Java fejlesztői segédanyagok az Azure Functions |} A Microsoft Docs
description: Megismerheti, hogyan hozhat létre Java-függvényeket.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: az Azure functions, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra, a java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 65964372cf2a0aa42be967f7c93749c58a9f56dd
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621769"
---
# <a name="azure-functions-java-developer-guide"></a>Az Azure Functions Java fejlesztői útmutatója

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>A programozási modell 

Az Azure-függvény egy állapot nélküli osztály módszer, amelynek dolgozza fel a bemenetet és kimenetet kell lennie. Engedélyezett metódusok írhat, bár a funkció nem függhet az osztály minden szolgáltatáspéldány-mezők. Az összes funkció módszer rendelkeznie kell egy `public` elérhetősége.

## <a name="triggers-and-annotations"></a>Eseményindítók és jegyzetek

Általában egy Azure-függvény meghívása külső eseményindító miatt. A függvénynek szüksége van, az eseményindító és a kapcsolódó bemeneteit feldolgozásához, és előállít egy vagy több kimenetek.

Java-jegyzetek szerepelnek a `azure-functions-java-core` csomag bemeneti és kimeneti kötést létrehozni a módszereket. A támogatott bemeneti eseményindítók és a kimeneti kötését a jegyzetek a következő táblázat tartalmazza:

Kötés | Megjegyzés
---|---
CosmosDB | –
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | –
Notification Hubs | –
Storage Blob | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Tárolási üzenetsor | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Storage-táblából | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Időzítő | <ul><li>`TimerTrigger`</li></ul>
Twilio | –

Trigger bemeneteinek és kimeneteinek is lehet definiálni az [function.json](/azure/azure-functions/functions-reference#function-code) az alkalmazáshoz.

> [!IMPORTANT] 
> Konfigurálnia kell az Azure Storage-fiókot a [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) helyi futtatását az Azure Storage-Blob, üzenetsor vagy tábla eseményindítókat.

Jegyzetek használatával. példa:

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

Ugyanannak a függvénynek írt jegyzetek nélkül:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

az ehhez tartozó `function.json`:

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

Szabadon javában adattípusok használata a bemeneti és kimeneti adatait, beleértve a natív típusai; a Java és a meghatározott specializált Azure típusainak testre szabott `azure-functions-java-core` csomagot. Az Azure Functions runtime megkísérli a kód által kért típus érkezett a bemeneti típusra konvertál.

### <a name="strings"></a>Sztringek

Fog leadott függvény átadott értékek karakterláncok, ha a megfelelő bemeneti paraméter típusa, a függvény típusú `String`. 

### <a name="plain-old-java-objects-pojos"></a>Egyszerű régi Java-objektumok (POJOs)

Ha a függvény metódus a bemeneti vár, hogy a Java-típus JSON-formátumú karakterláncot a Java-típusok adja le. Az átalakítás JSON-bemenet átadandó a függvények és a Java-típusok a kód a saját programkódban implementálni az átalakítás nélkül teszi lehetővé.

A függvények bemenetei között meg kell azonos módon pojo-vá típusok `public` elérhetősége, a függvény módszernek a van használva. Nem kell deklarálnia pojo-vá osztály mezők `public`. Ha például egy JSON-karakterlánc `{ "x": 3 }` a következő típusú pojo-vá alakítandó képes:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Bináris adatok

A bináris adatok jelenik meg egy `byte[]` az Azure functions-kódjában. Bináris bemeneti vagy kimeneti kötés az funkciók beállításával a `dataType` mezőbe, a function.json `binary`:

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

Ezután használhatja a függvény kódját:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Használat `OutputBinding<byte[]>` egy bináris kimeneti kötés típusa.


## <a name="function-method-overloading"></a>Függvény metódus terhelve

Jogosult a túlterhelési függvény módszerek ugyanazzal a névvel, de különböző típusú. Például rendelkezhet mindkét `String echo(String s)` és `String echo(MyType s)` az osztályt, és az Azure Functions runtime úgy dönt, hogy melyiket kell meghívásához, vizsgálja meg a tényleges bemeneti típus (HTTP-bemenet, MIME-típus esetében `text/plain` vezet `String` közben `application/json` jelöli `MyType`).

## <a name="inputs"></a>Bemenetek

Bemeneti az Azure Functions két kategóriába oszthatók: egy a trigger bemenete a másik pedig a további adatokat. Bár ezek eltérnek a `function.json`, a használat értéke megegyezik a Java-kódban. Vegyük a következő kódrészlet például:

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

A `@BindingName` jegyzet fogad egy `String` tulajdonság, amely a kötés eseményindító nevét megadva a `function.json`:

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

Ezért ha ez a függvény meghívása a HTTP-kérések forgalma átadja egy nem kötelező `String` argumentumként `in` és az Azure Table Storage `MyObject` típus átadott argumentum `obj`. Használja a `Optional<T>` típusú bemenetek kezelni az a Funkciók, amelyek null értékű.

## <a name="outputs"></a>Kimenetek

Kimenetek mind a visszaadott érték, vagy a kimeneti paraméterek jelöl. Ha csak egyetlen kimeneti, a visszaadott értékének használata ajánlott. Több kimeneteket meg kell használni a kimeneti paraméterek.

Visszatérési értéke legegyszerűbb formája a kimeneti, csak a bármilyen típusú értéket adja vissza, és az Azure Functions runtime megpróbálja átrendezésére, térjen vissza a tényleges típus (például egy HTTP-választ). A `functions.json`, használhat `$return` a kimeneti kötés neveként.

Termék, a több kimeneti értéket használja `OutputBinding<T>` típusa a `azure-functions-java-core` csomagot. Győződjön meg arról, HTTP-választ és a egy üzenetet le egy várólistába is kell, ha írási hasonló:

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

érdemes definiálni, amely a kimeneti kötés a `function.json`:

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
## <a name="specialized-types"></a>Speciális típusok

Néha egy függvény részletes bemenetek és kimenetek felett. Speciális típusa az `azure-functions-java-core` csomag áll rendelkezésre, hogy a kérelem kezelésére, és testre szabni a HTTP-eseményindító visszatérési állapota:

| Speciális típusa      |       Cél        | Tipikus használati                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-eseményindító     | Első módszer, fejlécek és lekérdezések |
| `HttpResponseMessage<T>` | HTTP-kimeneti kötése | 200-as eltérő állapotot adott vissza   |

> [!NOTE] 
> Is `@BindingName` jegyzet HTTP-fejlécek és lekérdezéseket. Ha például `@BindingName("name") String query` módszert használ, a HTTP-kérelmek fejléceinek és a lekérdezéseket, és adja át ezt az értéket a metódus. Ha például `query` lesz `"test"` Ha a kérelem URL-cím `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metaadatok

Metaadatok származik a különböző források, például HTTP-fejléceket, HTTP-lekérdezések és [metaadat-trigger](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Használja a `@BindingName` jegyzet együtt a metaadatok nevének értékét.

Ha például a `queryValue` a következő kódot a kódrészlet lesz `"test"` Ha a kért URL-címe `http://{example.host}/api/metadata?name=test`.

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

## <a name="functions-execution-context"></a>Functions végrehajtási környezet

Az Azure Functions végrehajtási környezetben keresztül kezelheti a `ExecutionContext` meghatározott objektum a `azure-functions-java-core` csomagot. Használja a `ExecutionContext` objektum használata a hívási adatokat, és a functions runtime adatait a kódban.

### <a name="logging"></a>Naplózás

A Functions runtime naplózó hozzáférést keresztül érhető el a `ExecutionContext` objektum. A naplózó vannak kötve, az Azure monitor, és lehetővé teszi, hogy jelző figyelmeztetéseket és hibákat észlelt függvény végrehajtása során.

Az alábbi példakód figyelmeztető üzenetet rögzít, amikor a fogadott kérelem törzse üres.

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

Gyakran kívánatos ki a biztonsági okokból kódból származzanak titkos információk kinyeréséhez. Ez lehetővé teszi a kód forrás kódtárházaktól közzétételének véletlenül más fejlesztők számára a hitelesítő adatok megadása nélkül. Ez megvalósítható használatával környezeti változókat, az Azure Functions helyi futtatás során, mind az Azure-ban a függvények telepítésekor.

Egyszerűen beállítani a környezeti változók, amikor az Azure Functions futtatása helyileg, dönthet úgy adja hozzá ezeket a változókat a local.settings.json fájlhoz. Ha egy nem szerepel a Functions-projekt gyökérkönyvtárát, nyugodtan hozzon létre egyet. Íme, mi a fájl hasonlóan kell kinéznie:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Minden egyes kulcs / érték-hozzárendelését a `values` térkép lesz elérhető futásidőben környezeti változóban, elérhető meghívásával `System.getenv("<keyname>")`, például `System.getenv("AzureWebJobsStorage")`. Hozzáadásával további kulcs / érték párok elfogadta és ajánlott eljárás.

> [!NOTE]
> Ha ez a módszer van, kell arra kell figyelembe venni, hogy hozzáadása a local.settings.json fájlt a tárház figyelmen kívül fájlt, így azt nem véglegesített.

Most ezeket a környezeti változókat függően a kóddal bejelentkezhet ugyanazzal a kulccsal beállítása / érték párok, a függvény alkalmazás beállításai az Azure Portalra, hogy a kód év működjön telepítésekor tesztelés helyileg és amikor az Azure-bA.

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* [Távoli hibakeresés Java az Azure Functions Visual Studio Code-dal](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
