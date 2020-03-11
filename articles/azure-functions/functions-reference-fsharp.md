---
title: Azure Functions F# fejlesztői dokumentáció
description: Megtudhatja, hogyan fejlesztheti Azure Functions parancsfájl használatával F# .
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358186"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F# fejlesztői dokumentáció

F#a Azure Functions egy olyan megoldás, amellyel könnyedén futtathatja a felhőben a kis-és a "functions" kódokat. Az adatfolyamatok F# függvény argumentumai használatával áramlanak a függvénybe. Az argumentumok neve `function.json`ban van megadva, és előre definiált nevek vannak az olyan dolgokhoz való hozzáféréshez, mint a Function Logger és a lemondási tokenek. 

>[!IMPORTANT]
>F#a parancsfájlt (. fsx) csak az Azure Functions futtatókörnyezet [1. x verziója](functions-versions.md#creating-1x-apps) támogatja. Ha a futtatókörnyezet 2. F# x vagy újabb verzióját kívánja használni, akkor előre lefordított F# osztály-függvénytári projektet (. FS) kell használnia. A Visual Studióval hozhat létre, kezelhet F# és tehet közzé egy osztály-függvénytár-projektet, ahogy azt egy [ C# Class Library-projekt](functions-dotnet-class-library.md)is tenné. További információ a functions-verziókról: [Azure functions Runtime Versions – áttekintés](functions-versions.md).

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői referenciát](functions-reference.md).

## <a name="how-fsx-works"></a>A. fsx működése
Egy `.fsx` fájl egy F# parancsfájl. Azt is megteheti, hogy F# egy olyan projekt, amely egyetlen fájlban található. A fájl tartalmazza a program kódját (ebben az esetben az Azure-függvényt) és a függőségek kezelésére vonatkozó irányelveket.

Ha egy Azure-függvényhez `.fsx` használ, a rendszer automatikusan felveszi a szükséges szerelvényeket, így a függvényre koncentrálhat, nem pedig a "nem szabványos" kódot.

## <a name="folder-structure"></a>Mappa szerkezete

A F# parancsfájl-projekthez tartozó mappastruktúrát a következőhöz hasonlóan néz ki:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Létezik egy megosztott [Host. JSON](functions-host-json.md) fájl, amely a Function alkalmazás konfigurálására használható. Mindegyik függvényhez saját kódlap (. fsx) és kötési konfigurációs fájl (function. JSON) tartozik.

A functions futtatókörnyezet [2. x vagy újabb](functions-versions.md) verziójában szükséges kötési kiterjesztések a `extensions.csproj` fájlban vannak meghatározva, a `bin` mappában lévő fájlok tényleges fájljaival együtt. Helyi fejlesztés esetén [regisztrálnia kell a kötési bővítményeket](./functions-bindings-register.md#extension-bundles). A Azure Portal funkcióinak fejlesztésekor ez a regisztráció történik.

## <a name="binding-to-arguments"></a>Argumentumok kötése
Minden kötés támogatja az argumentumok bizonyos készletét, ahogy az a [Azure functions eseményindítók és a kötések fejlesztői referenciájában](functions-triggers-bindings.md)szerepel. Például a blob triggert kötő argumentumok egyike egy POCO, amely F# rekord használatával fejezhető ki. Például:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Az F# Azure-függvény egy vagy több argumentumot fog elkészíteni. Ha Azure Functions argumentumokról beszélünk, a *bemeneti* argumentumok és a *kimeneti* argumentumok szerepelnek. A bemeneti argumentum pontosan úgy hangzik, mint: bemenet az F# Azure-függvényhez. A *kimeneti* argumentum változó adat, vagy egy `byref<>` argumentum, amely lehetővé teszik, hogy az adatokat visszaadja *a* függvényből.

A fenti példában `blob` egy bemeneti argumentum, és a `output` egy kimeneti argumentum. Figyelje meg, hogy a `output` `byref<>` használták (a `[<Out>]` jegyzetet nem kell hozzáadnia). `byref<>` típus használata lehetővé teszi, hogy a függvény módosítsa az argumentum által hivatkozott rekordot vagy objektumot.

Ha egy F# rekord bemeneti típusként van használatban, a rekord definícióját meg kell jelölni `[<CLIMutable>]`, hogy a Azure functions keretrendszer megfelelően állítsa be a mezőket, mielőtt a rekordot a függvénynek átadná. A motorháztető alatt `[<CLIMutable>]` állít elő a rekord tulajdonságaihoz. Például:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Az F# osztályok a és a kimenő argumentumok esetében is használhatók. Egy osztály esetében a tulajdonságok általában a képolvasók és a beállítók számára szükségesek. Például:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Naplózás
Ha a kimenetét be szeretné jelentkezni a F# [folyamatos átviteli naplókba](../app-service/troubleshoot-diagnostic-logs.md) , a függvénynek [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)típusú argumentumot kell használnia. A konzisztencia érdekében javasoljuk, hogy az argumentum neve `log`legyen. Például:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Aszinkron
A `async` munkafolyamatot lehet használni, de az eredménynek `Task`kell visszaadnia. Ezt a `Async.StartAsTask`lehet elvégezni, például:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Lemondási token
Ha a függvénynek szabályosan kell kezelnie a leállítást, [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argumentumot adhat meg. Ezt a `async`kombinálhatja, például:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Névterek importálása
A névtereket a szokásos módon lehet megnyitni:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

A rendszer automatikusan megnyitja a következő névtereket:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Külső szerelvények hivatkozása
Ehhez hasonlóan a keretrendszer szerelvény-referenciái is felvehetők a `#r "AssemblyName"` direktívával.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

A Azure Functions üzemeltetési környezet automatikusan hozzáadja a következő szerelvényeket:

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

Emellett a következő szerelvények speciális betokozással is rendelkezhetnek, és a simplename hivatkozhatnak (például `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Ha privát szerelvényre kell hivatkoznia, feltöltheti az összeállítási fájlt egy `bin` mappába a függvényhez képest, és hivatkozhat rá a fájlnév használatával (például  `#r "MyAssembly.dll"`). A fájlok a függvény mappájába való feltöltésével kapcsolatos információkért tekintse meg a következő szakaszt a csomagkezelő című részben.

## <a name="editor-prelude"></a>Szerkesztői bevezetés
A fordítói szolgáltatásokat F# támogató szerkesztő nem veszi figyelembe azokat a névtereket és szerelvényeket, amelyeket az Azure functions automatikusan tartalmaz. Ezért hasznos lehet olyan bevezetés, amely segít a szerkesztőnek megkeresni a használt szerelvényeket, és explicit módon megnyitni a névtereket. Például:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Amikor Azure Functions végrehajtja a kódot, feldolgozza a forrást `COMPILED` definiált módon, így a szerkesztő Prelude figyelmen kívül lesz hagyva.

<a name="package"></a>

## <a name="package-management"></a>Csomagkezelés
Ha NuGet-csomagokat szeretne használni F# egy függvényben, adjon hozzá egy `project.json` fájlt a függvény mappájához a Function alkalmazás fájlrendszerében. Íme egy példa `project.json` fájl, amely egy NuGet-csomagra mutató hivatkozást hoz létre a `Microsoft.ProjectOxford.Face` 1.1.0-es verzióra:

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

Csak a .NET-keretrendszer 4,6 támogatott, ezért győződjön meg arról, hogy a `project.json` fájl `net46` az itt látható módon.

`project.json`-fájl feltöltésekor a futtatókörnyezet megkapja a csomagokat, és automatikusan hozzáadja a csomag szerelvényekre mutató hivatkozásokat. Nincs szükség `#r "AssemblyName"` irányelvek hozzáadására. Csak adja hozzá a szükséges `open` utasításokat a `.fsx`-fájlhoz.

Érdemes lehet automatikusan hivatkozni a szerkesztőben lévő szerelvényekre, hogy javítsa a szerkesztő interakcióját a F# fordítási szolgáltatásokkal.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>`project.json`-fájl hozzáadása az Azure-függvényhez
1. Először is győződjön meg arról, hogy a Function alkalmazás fut, amelyet a függvény megnyitásával tehet meg a Azure Portalban. Emellett hozzáférést biztosít a folyamatos átviteli naplókhoz, ahol megjelenik a csomag telepítési kimenete.
2. `project.json`-fájl feltöltéséhez használja a [Function apps-fájlok frissítése](functions-reference.md#fileupdate)című témakörben leírt módszerek egyikét. Ha a [Azure functions folyamatos üzembe helyezését](functions-continuous-deployment.md)használja, hozzáadhat egy `project.json`-fájlt az előkészítési ágra az üzembe helyezéshez, mielőtt hozzáadja azt a telepítési ágra.
3. A `project.json` fájl hozzáadása után az alábbi példához hasonló kimenet jelenik meg a függvény folyamatos átviteli naplójában:

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
Környezeti változó vagy Alkalmazásbeállítások értékének beszerzéséhez használja a `System.Environment.GetEnvironmentVariable`, például:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>A. fsx kód újrafelhasználása
`#load` direktívával más `.fsx` fájlokból is használhat programkódot. Például:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Az elérési utak a `#load` direktívához képest a `.fsx` fájl helyéhez viszonyítva jelennek meg.

* `#load "logger.fsx"` betölti a Function mappában található fájlt.
* `#load "package\logger.fsx"` betölti a `package` mappában található fájlt a függvény mappájába.
* `#load "..\shared\mylogger.fsx"` betölti a `shared` mappában található fájlt a Function mappával megegyező szinten, közvetlenül a `wwwroot`alatt.

Az `#load` direktíva csak `.fsx` (F# parancsfájl) fájlokkal működik, és nem `.fs` fájlokkal.

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket:

* [F#Útmutató](/dotnet/articles/fsharp/index)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* [Tesztelés Azure Functions](functions-test-a-function.md)
* [Azure Functions skálázás](functions-scale.md)

