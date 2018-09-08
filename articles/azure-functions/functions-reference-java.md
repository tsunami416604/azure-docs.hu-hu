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
ms.date: 08/10/2018
ms.author: routlaw
ms.openlocfilehash: f0dc471e8875ad0d738fce10421c3586752148b9
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092309"
---
# <a name="azure-functions-java-developer-guide"></a>Az Azure Functions Java fejlesztői útmutatója

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>A programozási modell 

Az Azure-függvény egy állapot nélküli osztály módszer, amelynek dolgozza fel a bemenetet és kimenetet kell lennie. Bár a metódusok írhat, a függvény nem függhet az osztály minden szolgáltatáspéldány-mezők. Az összes funkció módszer rendelkeznie kell egy `public` elérhetősége.

Egynél több függvényt helyezheti a projektben. Kerülje a functions üzembe a különálló JAR-fájlok kivételével.

## <a name="triggers-and-annotations"></a>Eseményindítók és jegyzetek

 Az Azure functions, például HTTP-kérést, egy időzítő vagy egy frissítést a data-triggerek által kerül meghívásra. A függvénynek szüksége van, az eseményindító és a egy vagy több kimeneti előállításához bemenetet feldolgozni.

Használja a Java-jegyzetek szerepel a [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) csomag bemeneti és kimeneti kötést létrehozni a módszereket. A mintakód a jegyzetek használatával érhető el a [Java referenciadokumentumok](/java/api/com.microsoft.azure.functions.annotation) minden jegyzet és az Azure Functions kötés referencia dokumentációját, például az egyik az [HTTP-eseményindítók](/azure/azure-functions/functions-bindings-http-webhook).

Trigger bemeneteinek és kimeneteinek is lehet definiálni az [function.json](/azure/azure-functions/functions-reference#function-code) keresztül jegyzetek helyett a függvény. Használatával `function.json` helyett ezzel a módszerrel jegyzetek nem ajánlott.

> [!IMPORTANT] 
> Konfigurálnia kell az Azure Storage-fiókot a [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) helyi futtatását az Azure Storage-Blob, üzenetsor vagy tábla eseményindítókat.

Jegyzetek használatával. példa:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
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

## <a name="third-party-libraries"></a>Külső gyártó kódtárait 

Az Azure Functions támogatja a külső gyártótól származó kódtárakat. Alapértelmezés szerint az összes függősége a projekt megadott `pom.xml` fájl automatikusan kötegelve fog során a `mvn package` cél. Nincs megadva függőségeként-tárak a `pom.xml` fájlt, helyezze el őket egy `lib` könyvtárat a gyökérkönyvtár a függvény. Függőségeket helyezi el a `lib` directory hozzáadódik a rendszer osztálybetöltő futásidőben.

## <a name="data-type-support"></a>Adattípus-támogatás

A bemeneti és kimeneti adatait, beleértve a natív típusai; is használhat bármilyen típusú adatokat Java nyelven a Java és a meghatározott specializált Azure típusainak testre szabott `azure-functions-java-library` csomagot. Az Azure Functions runtime megkísérli a kód által kért típus érkezett a bemeneti típusra konvertál.

### <a name="strings"></a>Sztringek

Fog leadott függvény átadott értékek karakterláncok, ha a megfelelő bemeneti paraméter típusa, a függvény típusú `String`. 

### <a name="plain-old-java-objects-pojos"></a>Egyszerű régi Java-objektumok (POJOs)

Ha a függvény a bemeneti aláírás vár, hogy a Java-típus JSON-formátumú karakterláncot a Java-típusok adja le. Az átalakításhoz adja át, JSON, és a Java-típusok használata teszi lehetővé.

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

Üres értékeket lehet `null` , a functions argumentumként, de javasolt módja a használandó az üres értékek lehetséges foglalkozik `Optional<T>`.


## <a name="function-method-overloading"></a>Függvény metódus terhelve

Jogosult a túlterhelési függvény módszerek ugyanazzal a névvel, de különböző típusú. Például rendelkezhet mindkét `String echo(String s)` és `String echo(MyType s)` osztályban. Az Azure Functions úgy dönt, hogy melyik módszert követve indíthatók el a bemeneti típus alapján (a HTTP-bemenet, MIME-típus `text/plain` vezet `String` közben `application/json` jelöli `MyType`).

## <a name="inputs"></a>Bemenetek

Bemeneti az Azure Functions két kategóriába oszthatók: egy a trigger bemenete a másik pedig a további adatokat. Bár ezek eltérnek a `function.json`, a használat értéke megegyezik a Java-kódban. Vegyük a következő kódrészlet például:

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

Ez a funkció akkor aktiválódik, ha a HTTP-kérelemre a függvény által átadott `String in`. Egy bejegyzés veszi át az Azure Table Storage URL-útvonal azonosító alapján, és finomhangolásokat rendelkezésre áll-e, `obj` függvény törzsében.

## <a name="outputs"></a>Kimenetek

Kimenetek mind a visszaadott érték, vagy a kimeneti paraméterek jelöl. Ha csak egyetlen kimeneti, a visszaadott értékének használata ajánlott. Több kimeneteket meg kell használni a kimeneti paraméterek.

Visszatérési értéke legegyszerűbb formája a kimeneti, csak a bármilyen típusú értéket adja vissza, és az Azure Functions runtime megpróbálja átrendezésére, térjen vissza a tényleges típus (például egy HTTP-választ).  Alkalmazhat semmilyen kimenet jegyzetek a függvény metódus (a jegyzet neve tulajdonságához azt kell $return) a visszaadott érték kimenet meghatározására.

Termék, a több kimeneti értéket használja `OutputBinding<T>` típusa a `azure-functions-java-library` csomagot. Győződjön meg arról, HTTP-választ és a egy üzenetet le egy várólistába is kell, ha írási hasonló:

Például egy blob tartalma függvény másolása sikerült adható meg az alábbi kódot. `@StorageAccount` jegyzet itt szolgál, hogy mindkét kapcsolati tulajdonság duplikálásához `@BlobTrigger` és `@BlobOutput`.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Használjon `OutputBinding<byte[]`> kimeneti (paraméter); értékének bináris ellenőrizze a visszaadott értékeket használja `byte[]`.

## <a name="specialized-types"></a>Speciális típusok

Néha egy függvény részletes bemenetek és kimenetek felett. Speciális típusa az `azure-functions-java-core` csomag áll rendelkezésre, hogy a kérelem kezelésére, és testre szabni a visszaadott HTTP-trigger állapota:

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
import com.microsoft.azure.functions.annotation.*;


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

## <a name="execution-context"></a>Végrehajtási környezet

Az Azure Functions végrehajtási környezetben keresztül kommunikálhat a `ExecutionContext` meghatározott objektum a `azure-functions-java-library` csomagot. Használja a `ExecutionContext` objektum használata a hívási adatokat, és a functions runtime adatait a kódban.

### <a name="custom-logging"></a>Egyéni naplózás

A Functions runtime naplózó hozzáférést keresztül érhető el a `ExecutionContext` objektum. A naplózó vannak kötve, az Azure monitor, és lehetővé teszi, hogy jelző figyelmeztetéseket és hibákat észlelt függvény végrehajtása során.

Az alábbi példakód figyelmeztető üzenetet rögzít, amikor a fogadott kérelem törzse üres.

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

Az Azure CLI-stream Java-, standard és a hibanaplózás, valamint egyéb alkalmazásnaplózás is használhatja. Először is konfiguráljon függvény alkalmazását az Azure CLI-vel alkalmazásnaplózás írni:

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

Titkos adatok, például a kulcsok vagy tokenek biztonsági okokból a forráskód kívül tartani. Gombok használata és a tokeneket a függvény kódját a környezeti változók elolvasásával.

Környezeti változókat beállítani, amikor az Azure Functions futtatása helyileg, dönthet úgy adja hozzá ezeket a változókat a local.settings.json fájlhoz. Ha egy nem szerepel a Functions-projekt gyökérkönyvtárát, létrehozhat egyet. Íme, mi a fájl hasonlóan kell kinéznie:

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
> Ha ez a módszer van, kell arra a local.settings.json hozzáadása fájlt a tárház figyelmen kívül fájlt, így azt nem véglegesített.

Most ezeket a környezeti változókat függően a kóddal bejelentkezhet ugyanazzal a kulccsal beállítása / érték párok, a függvény alkalmazás beállításai az Azure Portalra, hogy a kód év működjön telepítésekor tesztelés helyileg és amikor az Azure-bA.

## <a name="next-steps"></a>További lépések

Azure-függvény Java-fejlesztésekhez kapcsolatos további információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
- Helyi fejlesztés és a hibakeresési [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), és [Eclipse](functions-create-maven-eclipse.md). 
* [Távoli hibakeresés Java az Azure Functions Visual Studio Code-dal](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven beépülő modul az Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) -függvény létrehozása révén egyszerűsíthetők a `azure-functions:add` cél és a egy átmeneti könyvtárat a előkészítése [ZIP fájl központi telepítési](deployment-zip-push.md).
