---
title: Azure Functions F# fejlesztői útmutató
description: Ismerje meg, hogyan fejlesztheti az Azure Functions f# parancsfájl használatával.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276763"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F# Fejlesztői útmutató

Az F# for Azure Functions egy olyan megoldás, amely könnyen futtathat kis kóddarabokat vagy "függvényeket" a felhőben. Az adatok az F# függvénybe áramlanak a függvényargumentumokon keresztül. Az argumentumnevek `function.json`meg vannak adva a alkalmazásban, és előre definiált nevek vannak a függvénynaplózó és a törlési jogkivonatok eléréséhez. 

>[!IMPORTANT]
>F# script (.fsx) csak az Azure Functions futásidejű [verzió](functions-versions.md#creating-1x-apps) támogatja. Ha az F# programot a futásidejű 2.x-es és újabb verzióival szeretné használni, akkor egy előre lefordított F# osztálykönyvtár-projektet (.fs) kell használnia. F# osztálykönyvtár-projektet hozhat létre, kezelhet és tehet közzé a Visual Studio használatával, mint egy [C# osztálykönyvtár-projektet.](functions-dotnet-class-library.md) A Functions-verziókról az [Azure Functions futásidejű verzióinak áttekintése című témakörben olvashat bővebben.](functions-versions.md)

Ez a cikk feltételezi, hogy már elolvasta az [Azure Functions fejlesztői hivatkozását.](functions-reference.md)

## <a name="how-fsx-works"></a>Az .fsx működése
A `.fsx` fájl F# parancsfájl. Úgy is felfogható, mint egy F# projekt, amely egyetlen fájlban található. A fájl tartalmazza a program kódját (ebben az esetben az Azure-függvényt) és a függőségek kezelésére vonatkozó irányelveket is.

Ha egy `.fsx` Azure-függvényhez használ, a rendszer automatikusan tartalmazza a gyakran szükséges szerelvényeket, így a függvényre összpontosíthat a "sablonalapú" kód helyett.

## <a name="folder-structure"></a>Mappastruktúra

Az F# parancsfájl-projekt mappastruktúrája a következőképpen néz ki:

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

Van egy megosztott [host.json](functions-host-json.md) fájl, amely a függvényalkalmazás konfigurálásához használható. Minden függvény saját kódfájllal (.fsx) és kötési konfigurációs fájllal (function.json) rendelkezik.

A Functions futásidejű [2.x-es és újabb verzióiban](functions-versions.md) szükséges `extensions.csproj` kötési kiterjesztések a fájlban vannak definiálva, a mappában lévő `bin` tényleges könyvtárfájlokkal együtt. Helyi fejlesztés esetén kötelező kiterjesztéseket kell [regisztrálnia.](./functions-bindings-register.md#extension-bundles) Amikor funkciókat fejleszt az Azure Portalon, ez a regisztráció az Ön számára történik.

## <a name="binding-to-arguments"></a>Az argumentumokra kötelező
Minden egyes kötés támogat néhány érvkészletet, ahogy azt az [Azure Functions elindítja és kötések fejlesztői referencia.](functions-triggers-bindings.md) Például az egyik argumentum kötések egy blob eseményindító támogatja a POCO, amely kifejezhető egy F # rekord használatával. Példa:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Az F# Azure függvény egy vagy több argumentumot vesz igénybe. Amikor az Azure Functions argumentumairól beszélünk, *a bemeneti* és *kimeneti* argumentumokra hivatkozunk. A bemeneti argumentum pontosan az, aminek hangzik: az F# Azure függvény bemenete. A *kimeneti* argumentum olyan `byref<>` módosítható adat vagy argumentum, amely arra szolgál, hogy az adatokat *visszaadja* a függvényből.

A fenti `blob` példában egy bemeneti `output` argumentum, és egy kimeneti argumentum. Figyeljük meg, hogy használt `byref<>` `output` (nincs szükség, hogy adjunk a `[<Out>]` jegyzet). Egy `byref<>` típus használatával a függvény módosíthatja, hogy az argumentum melyik rekordra vagy objektumra hivatkozik.

Ha egy F# rekordot használ bemeneti típusként, `[<CLIMutable>]` a rekorddefiníciót meg kell jelölni annak érdekében, hogy az Azure Functions keretrendszer megfelelően állítsa be a mezőket, mielőtt a rekordot a függvénybe adná. A motorháztető `[<CLIMutable>]` alatt a rekordtulajdonságok hoz létre beállítókat. Példa:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Az F# osztály argumentumok be- és ki- és kiosztási mint a be- és kiargumentumok hoz is használható. Egy osztály, tulajdonságok általában szükség getters és szetter. Példa:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Naplózás
A kimenet f#-ban való [naplózásához](../app-service/troubleshoot-diagnostic-logs.md) a függvénynek [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)típusú argumentumot kell vennie. A konzisztencia érdekében azt javasoljuk, hogy ez az argumentum neve `log`. Példa:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Aszinkron
A `async` munkafolyamat használható, de az eredménynek `Task`vissza kell adnia egy . Ezt a következőkkel `Async.StartAsTask`teheti meg:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Törlési token
Ha a függvénynek kecsesen kell kezelnie [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) a leállítást, érvelhet. Ez kombinálható `async`például a következővel:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Névterek importálása
A névterek a szokásos módon nyithatók meg:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

A következő névterek automatikusan megnyílnak:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Hivatkozás külső szerelvények
Hasonlóképpen az `#r "AssemblyName"` irányelvvel keretösszeállítási hivatkozások is hozzáadhatók.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Az Azure Functions üzemeltetési környezete automatikusan hozzáadja a következő összeállításokat:

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

Ezenkívül a következő szerelvények különleges tokkal vannak eltestébe, és egyszerű `#r "AssemblyName"`névvel (pl. ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Ha privát szerelvényre szeretne hivatkozni, feltöltheti az `bin` összeállítási fájlt a függvényhez viszonyított mappába, és hivatkozhat rá a fájlnév használatával (pl.  `#r "MyAssembly.dll"`). A fájlok függvénymappába való feltöltésével kapcsolatos további tudnivalókért tekintse meg a csomagkezelésről szóló alábbi szakaszt.

## <a name="editor-prelude"></a>Szerkesztő prelúdium
Az F# Compiler Services-t támogató szerkesztő nem ismeri azOkat a névtereket és szerelvényeket, amelyeket az Azure Functions automatikusan tartalmaz. Ezért hasznos lehet egy olyan előjáték ot megadni, amely segít a szerkesztőnek megtalálni a használt szerelvényeket, és explicit módon megnyitni a névtereket. Példa:

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

Amikor az Azure Functions végrehajtja a kódot, a forrást meghatározottval `COMPILED` dolgozza fel, így a szerkesztő előjátéka figyelmen kívül lesz hagyva.

<a name="package"></a>

## <a name="package-management"></a>Csomagkezelés
Ha A NuGet csomagokat f# függvényben szeretné használni, adjon hozzá egy `project.json` fájlt a függvény mappájához a függvényalkalmazás fájlrendszerében. Íme egy `project.json` példafájl, amely nuget `Microsoft.ProjectOxford.Face` csomaghivatkozást ad hozzá az 1.1.0-s verzióhoz:

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

Csak a .NET Framework 4.6 támogatott, `project.json` ezért győződjön meg arról, hogy a fájl az itt látható módon jelenik `net46` meg.

Amikor feltölt `project.json` egy fájlt, a futásidejű leadja a csomagokat, és automatikusan hivatkozásokat ad hozzá a csomagszerelvényekhez. Nem kell direktívákat hozzáadnia. `#r "AssemblyName"` Csak adja `open` hozzá a `.fsx` szükséges nyilatkozatokat a fájlhoz.

Előfordulhat, hogy automatikusan hivatkozásokat szeretne elhelyezni a szerkesztői előjátékban, hogy javítsa a szerkesztő interakcióját az F# Compile Services szolgáltatással.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Fájl hozzáadása `project.json` az Azure-függvényhez
1. Először győződjön meg arról, hogy a függvényalkalmazás fut, amit megtehet i. a függvény megnyitásával az Azure Portalon. Ez hozzáférést biztosít a streamelési naplókhoz is, ahol a csomag telepítési kimenete megjelenik.
2. Fájl feltöltéséhez `project.json` használja a [függvényalkalmazás-fájlok frissítésében](functions-reference.md#fileupdate)ismertetett módszerek egyikét. Ha folyamatos [üzembe helyezést](functions-continuous-deployment.md)használ az Azure `project.json` Functions hez, hozzáadhat egy fájlt az átmeneti ághoz, hogy kísérletezzen vele, mielőtt hozzáadná a központi telepítési ághoz.
3. A `project.json` fájl hozzáadása után a függvény streamelési naplójában a következő példához hasonló kimenet jelenik meg:

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
Környezeti változó vagy alkalmazásbeállítási érték `System.Environment.GetEnvironmentVariable`bekérése például a következő t használja:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>.fsx-kód újrafelhasználása
Más `.fsx` fájlokból származó kódot direktívával `#load` is használhat. Példa:

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

Az irányelv elérési `#load` útjai a `.fsx` fájl helyéhez viszonyítva találhatók.

* `#load "logger.fsx"`betölt egy fájlt, amely a függvénymappában található.
* `#load "package\logger.fsx"`betölt egy fájlt, amely a `package` függvénymappa mappájában található.
* `#load "..\shared\mylogger.fsx"`betölt egy fájlt, amely a `shared` mappában található, ugyanazon a `wwwroot`szinten, mint a függvénymappa, azaz közvetlenül a alatt.

Az `#load` irányelv csak `.fsx` (F# script) fájlokkal `.fs` működik, fájlokkal nem.

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [F# útmutató](/dotnet/articles/fsharp/index)
* [Gyakorlati tanácsok az Azure Functionshez](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions aktiválódik és kötéseket köt](functions-triggers-bindings.md)
* [Az Azure Functions tesztelése](functions-test-a-function.md)
* [Az Azure Functions méretezése](functions-scale.md)

