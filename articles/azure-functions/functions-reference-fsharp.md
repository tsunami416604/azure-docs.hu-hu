---
title: "Az Azure Functions F # fejlesztői leírás |} Microsoft Docs"
description: "Megtudhatja, hogyan fejleszthet Azure Functions használatával F #."
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, webhookokkal, a dinamikus számítási, a kiszolgáló nélküli architektúra, F #"
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: functions
ms.devlang: fsharp
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/09/2016
ms.author: syclebsc
ms.openlocfilehash: 314f528a1fcef2c7afb0eedba012023f3bc9502b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-f-developer-reference"></a>Az Azure Functions F # fejlesztői leírás
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

F # az Azure Functions egy megoldással egyszerűen futtathatók kisebb kódrészletek, más néven "függvények" a felhőben. Az F # függvénynek keresztül Függvényargumentumok adatáramlás. Argumentum neve meg van határozva a `function.json`, és nincsenek előre definiált nevek többek között a funkció naplózó és a megszakítási jogkivonatok eléréséhez.

Ez a cikk feltételezi, hogy Ön már elolvasta a [Azure Functions fejlesztői segédanyagai](functions-reference.md).

## <a name="how-fsx-works"></a>.Fsx működése
Egy `.fsx` fájl az F #-parancsfájlok. Akkor tekinthető az F # projekt egyetlen fájlban található. A fájl tartalmazza a kódban a program (ebben az esetben, az Azure-függvény) és irányelveket függőségek kezelése.

Ha egy `.fsx` egy Azure-függvény, általában a szükséges szerelvényeket automatikusan érhetők el, ezáltal jobban összpontosíthat az "bolierplate" helyett a függvény a kódban.

## <a name="binding-to-arguments"></a>Argumentum kötése
Minden egyes kötés támogatja-e bizonyos argumentumok, ahogy az az készletét a [Azure Functions eseményindítók és kötések fejlesztői segédanyagai](functions-triggers-bindings.md). Például egy blob eseményindító támogatja argumentum kötéseket egyik egy POCO, amelyek használatával az F # rekord jelöl. Példa:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Az F # Azure-függvény lépnek egy vagy több argumentum. Amikor döntésről bővebben az Azure Functions argumentumok, hivatkozunk *bemeneti* argumentumok és *kimeneti* argumentumok. Egy bemeneti argumentum pontosan mit úgy tűnik, hogy például: az F # Azure-függvény a bemeneti. Egy *kimeneti* argumentum változtatható adatok vagy egy `byref<>` argumentumot, amelynek vissza az adatok úgy funkcionál *kimenő* a függvény.

A példában `blob` egy bemeneti argumentum, és `output` kimeneti argumentum van. Figyelje meg, hogy használtuk `byref<>` a `output` (nincs szükség hozzáadása a `[<Out>]` jegyzet). Használja a `byref<>` típus lehetővé teszi, hogy a függvény melyik rekord vagy argumentum a hivatkozott objektum módosítása.

Az F # rekord bemeneti típusként használata esetén a rekord definition fel kell tüntetni az `[<CLIMutable>]` annak érdekében, hogy az Azure Functions keretrendszer a mezők megfelelően beállítása előtt a rekord a függvénynek. A technikai részletek alatt `[<CLIMutable>]` állít elő, a rekord tulajdonságok Setter elemek. Példa:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Az F # osztály mindkét és argumentumokat is használható. Egy osztály tulajdonságok általában kell beolvasókat és Setter elemek. Példa:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Naplózás
A kimeneti bejelentkezni a [a folyamatos átviteli naplók](../app-service/web-sites-enable-diagnostic-log.md) F #, a függvény típusú argumentumot kell venniük `TraceWriter`. A konzisztencia, javasoljuk, ez az argumentum neve `log`. Példa:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Aszinkron
A `async` munkafolyamat is használható, de az eredmény vissza kell adnia egy `Task`. Ezt megteheti a `Async.StartAsTask`, például:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token törlése
Ha a függvény leállítási szabályosan kezelni, amelynek megadhatja egy [ `CancellationToken` ](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argumentum. Ez kombinálva `async`, például:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Névterek importálása
Névterek megnyithatók a szokásos módon:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

A következő névterek automatikusan megnyitása:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Külső szerelvények hivatkozik
Ehhez hasonlóan keretrendszer szerelvényhivatkozások hozzáadása is lehetséges a `#r "AssemblyName"` direktívát.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

A következő szerelvények a rendszer automatikusan hozzáadja a üzemeltetési környezet az Azure Functions:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Ezenkívül a következő szerelvényekben különleges cased és simplename is hivatkozhat (pl. `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

A belső szerelvény hivatkoznia kell, ha a szerelvény fájlból feltöltheti egy `bin` mappa viszonyítva az függvény és a hivatkozási (pl. name, a fájl használatával  `#r "MyAssembly.dll"`). A fájlok feltöltéséről a függvény mappába információkért lásd a következő felügyeleti csomag.

## <a name="editor-prelude"></a>Szerkesztő Prelude
Egy szerkesztővel, amely támogatja az F # fordítóprogram szolgáltatások semmit sem éreznek a névterek és-szerelvényt, amely automatikusan felveszi az Azure Functions. Ilyen hasznos lehet egy prelude található használja a szerelvényeket a szerkesztő segítségével, és explicit módon a névterek megnyitásához. Példa:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

A kód az Azure Functions végrehajtásakor feldolgozza az adatforrással, amelynek `COMPILED` definiálva, ezért figyelmen kívül hagyja a szerkesztő prelude.

<a name="package"></a>

## <a name="package-management"></a>Felügyeleti csomag
Az F # függvény NuGet-csomagok használatához vegye fel a `project.json` fájlt a a függvény mappa, a függvény app fájlrendszer. Példa `project.json` hozzáad egy NuGet csomag hivatkozást tartalmazó `Microsoft.ProjectOxford.Face` 1.1.0-ás verzió:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Csak a .NET keretrendszer 4.6-os támogatott, ezért győződjön meg arról, hogy a `project.json` fájl határozza meg `net46` itt látható módon.

Feltöltése egy `project.json` fájl, a futtatókörnyezet lekérdezi a csomagokat, és automatikusan hozzáadja a csomag szerelvények hivatkozik. Nem kell hozzáadnia `#r "AssemblyName"` irányelvek. A szükséges hozzá `open` utasítást, hogy a `.fsx` fájlt.

Kezdésként érdemes lehet helyezni a szerkesztő prelude, F # lefordítani a szolgáltatások a szerkesztő interakcióba javítása érdekében automatikusan hivatkozásokat szerelvényeket.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Hogyan lehet hozzáadni egy `project.json` fájlt az Azure-függvény
1. Először meggyőződött arról, hogy a függvény alkalmazás fut, amely a függvény nyissa meg az Azure portálon teheti. Ez is hozzáférést biztosít a folyamatos átviteli naplók ahol csomag telepítési kimenet jelenik meg.
2. Töltse fel a `project.json` fájlt, az ismertetett módszerek egyikével [függvény alkalmazásfájlok frissítése](functions-reference.md#fileupdate). Ha használ [folyamatos üzembe helyezés az Azure Functions](functions-continuous-deployment.md), adhat hozzá egy `project.json` ahhoz, hogy mielőtt hozzáadná a központi telepítés fiókirodai kísérletezhet az átmeneti ágában fájlt.
3. Miután a `project.json` fájl kerül, látni fogja, a függvény az alábbi példához hasonló kimenetet a streaming naplóban:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Környezeti változók
Egy környezeti változó vagy olyan alkalmazás, beállítás értékét, amelyet `System.Environment.GetEnvironmentVariable`, például:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Újbóli felhasználása .fsx kódot
A többi kódot használhatja `.fsx` a fájlok egy `#load` direktívát. Példa:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Elérési utak biztosít a `#load` helyéhez viszonyított irányelv vannak a `.fsx` fájlt.

* `#load "logger.fsx"`a függvény mappában található fájl betöltése.
* `#load "package\logger.fsx"`található fájl betöltése a `package` mappája a függvény.
* `#load "..\shared\mylogger.fsx"`található fájl betöltése a `shared` mappaként a funkciót, ez azt jelenti, hogy ugyanazon a szinten mappát közvetlenül a `wwwroot`.

A `#load` direktíva csak együttműködve `.fsx` (F # parancsfájl) fájlok, és nem a `.fs` fájlokat.

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket:

* [F # útmutató](/dotnet/articles/fsharp/index)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* [Az Azure Functions tesztelése](functions-test-a-function.md)
* [Az Azure Functions méretezése](functions-scale.md)

