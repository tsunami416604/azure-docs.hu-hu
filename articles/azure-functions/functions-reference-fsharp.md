---
title: Az Azure Functions F# – fejlesztői referencia |} A Microsoft Docs
description: Megtudhatja, hogyan fejleszthet az Azure Functions használatával F# parancsfájlt.
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: azure functions, functions, event processing, webhooks, dynamic compute, serverless architecture, F#
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: e7e4e898142d6f9d1a93e91c1f1476ff81fc7d3c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734659"
---
# <a name="azure-functions-f-developer-reference"></a>Az Azure Functions F# – fejlesztői referencia

F#az Azure Functions egy megoldással egyszerűen futtathatók kisebb kódrészletek, más kód vagy a "függvények" a felhőben van. Az adatáramlás a F# függvény függvény argumentumának keresztül. Argument neve meg van határozva a `function.json`, és vannak az előre definiált nevek eléréséhez, többek között a függvény naplózó és a megszakítási tokeneket. 

>[!IMPORTANT]
>F#parancsprogram (.fsx) csak a támogatja [verzió 1.x](functions-versions.md#creating-1x-apps) az Azure Functions Runtime. Ha a használni kívánt F# az a verzió 2.x verziójú futtatókörnyezet kell használnia egy előre lefordított F# hordozhatóosztálytár-projektjének (.fs). Létrehozásához, kezeléséhez és közzétételéhez egy F# hordozhatóosztálytár-projektjének Visual Studio használatával, mint egy [ C# hordozhatóosztálytár-projektjének](functions-dotnet-class-library.md). Functions-verziókkal kapcsolatos további információkért lásd: [Azure Functions runtime verziók áttekintése](functions-versions.md).

Ez a cikk feltételezi, hogy Ön már elolvasta a [Azure Functions fejlesztői segédanyagai](functions-reference.md).

## <a name="how-fsx-works"></a>.Fsx működése
Egy `.fsx` fájl egy F# parancsfájlt. Azt is értelmezhetők, egy F# projekt, amely az egyetlen fájl szerepel. A fájl tartalmazza a kódban a program (az ebben az esetben az Azure-függvény) és a függőségek kezelése irányelveknek.

Ha egy `.fsx` az Azure-függvény, gyakran szükséges a szerelvények olyan automatikusan tartalmazza, így a "bolierplate" helyett a függvény kódját összpontosíthat.

## <a name="folder-structure"></a>gyökérmappa-szerkezetében

A mappastruktúra egy F# parancsfájl projekt a következőhöz hasonló:

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

Van egy megosztott [host.json](functions-host-json.md) fájlt, amely a függvényalkalmazás konfigurálása használható. Minden függvény saját kódfájl (.fsx) és a kötési konfigurációs fájl (function.json) rendelkezik.

A kötési bővítményeket szükséges [verzió 2.x](functions-versions.md) a Functions runtime vannak meghatározva a `extensions.csproj` fájlt, a tényleges függvénytárfájlok a `bin` mappát. Ha helyileg fejlesztésével, akkor meg kell [regisztrálja a kötési bővítményeket](./functions-bindings-register.md#local-development-azure-functions-core-tools). Amikor fejlesztéséről az Azure Portalon, a regisztrációt, készen áll.

## <a name="binding-to-arguments"></a>Argumentumok kötést
Minden egyes kötés támogatja bizonyos argumentumok leírt módon a [eseményindítók és kötések az Azure Functions fejlesztői segédanyagok](functions-triggers-bindings.md). Ha például egy blob eseményindító támogatja az argumentum kötéseit egyik egy POCO, amelyek használatával kell megadni egy F# rekord. Példa:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

A F# Azure-függvényt egy vagy több argumentumot vesz igénybe. Amikor az Azure Functions-argumentumok az, hogy tekintse meg *bemeneti* argumentumok és *kimeneti* argumentumokat. A bemeneti argumentumok az pontosan mit, megosztásánál: a bemeneti a F# Azure-függvény. Egy *kimeneti* argumentum értéke módosítható adatok, vagy egy `byref<>` argumentum, amely olyan módon, hogy vissza adatokat *ki* a függvény.

A fentiek példában `blob` egy bemeneti argumentum, és `output` egy kimeneti argumentum. Figyelje meg, hogy használtuk `byref<>` a `output` (nem kell hozzáadni a `[<Out>]` jegyzet). Használatával egy `byref<>` típusa lehetővé teszi, hogy a függvény melyik rekord vagy az argumentum a hivatkozott objektum módosítása.

Ha egy F# rekordot egy bemeneti típus használják, fel kell tüntetni az a rekord definíció `[<CLIMutable>]` annak érdekében, hogy az Azure Functions keretrendszer, a mezők helyes beállítása előtt a rekord a függvényt. Technikai részletek `[<CLIMutable>]` rekord tulajdonságaira beállítókat állít elő. Példa:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Egy F# osztályt mind a bejövő és kimenő argumentumokat is használható. Egy osztály tulajdonságait általában kell beolvasókat és beállítókat. Példa:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Naplózás
A kimeneti bejelentkezni a [folyamatos átviteli naplók](../app-service/troubleshoot-diagnostic-logs.md) a F#, a függvény típusú argumentumot vesz igénybe [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). A konzisztencia érdekében javasoljuk, hogy ezt az argumentumot nevű `log`. Példa:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Az aszinkron
A `async` munkafolyamat is használható, de az eredményt kell visszaadnia egy `Task`. Ezt megteheti a `Async.StartAsTask`, például:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token törlése
Ha a függvény leállítási szabályosan kezelésére van szüksége, adhat egy [ `CancellationToken` ](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argumentum. Ez kombinálva `async`, például:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Névterek importálása
Névterek segítségével lehet megnyitni a szokásos módon:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

A következő névterek nyílik meg automatikusan:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Hivatkozó külső szerelvények
Ehhez hasonlóan keretrendszer összeállítási referenciát is hozzáadhatók a a `#r "AssemblyName"` irányelv.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Az alábbi szerelvények a rendszer automatikusan hozzáadja az az Azure Functions üzemeltetési környezet:

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

Emellett a következő szerelvények olyan speciális kisbetűsek és simplename szerint lehet hivatkozni (pl. `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Privátní sestavení hivatkoznia kell, ha fájlt tölthet fel a szerelvény be egy `bin` mappa viszonyítva, a függvény és a hivatkozás, a fájl használatával nevezze (például)  `#r "MyAssembly.dll"`). A fájlok feltöltéséről a függvény mappáját információkért lásd: a következő szakasz a felügyeleti csomag.

## <a name="editor-prelude"></a>Editor Prelude
-Szerkesztő, amely támogatja a F# fordító szolgáltatás nem lesz a névterek és-szerelvényt, amely az Azure Functions automatikusan tartalmazza. Emiatt a hasznos lehet egy prelude, amely segít a szerkesztőben keresse meg a szerelvények használ, és explicit módon a névterek megnyitásához. Példa:

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

Az Azure Functions végrehajtja a kódot, amikor feldolgozza a forráson `COMPILED` meghatározva, hogy a rendszer figyelmen kívül a szerkesztő prelude.

<a name="package"></a>

## <a name="package-management"></a>Csomagkezelés
A NuGet-csomagok használata egy F# működik, adjon hozzá egy `project.json` fájlt a függvényalkalmazás fájlrendszer a függvény mappájába. Íme egy példa `project.json` fájlt, amely hozzáad egy NuGet csomag hivatkozást `Microsoft.ProjectOxford.Face` 1.1.0-s verzió:

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

Csak a .NET Framework 4.6 támogatott, ezért ügyeljen arra, hogy a `project.json` fájlban az `net46` itt látható módon.

Amikor feltölt egy `project.json` fájl, a modul lekérdezi a csomagokat, és automatikusan hozzáadja a csomag szerelvényre hivatkozik. Nem kell hozzá `#r "AssemblyName"` irányelveknek. Adja hozzá a szükséges `open` utasításokat a `.fsx` fájlt.

Kezdésként érdemes lehet helyezni a szerkesztő prelude való interakció a szerkesztő javítása érdekében automatikusan hivatkozásokat szerelvények F# összeállítása szolgáltatások.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Hogyan adhat hozzá egy `project.json` fájlt az Azure-függvény
1. Indítása azáltal, hogy a függvényalkalmazás fut, akkor nyissa meg a függvényt az Azure Portalon teheti. Ez is hozzáférést biztosít a folyamatos átviteli naplók, ahol csomag telepítési kimenet jelenik meg.
2. A feltöltendő egy `project.json` fájlt, a leírt módszerek egyikével [függvény alkalmazásfájlok frissítése](functions-reference.md#fileupdate). Ha használ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md), adhat hozzá egy `project.json` fájlt annak érdekében, hogy az üzembe helyezés ágba való hozzáadása előtt kísérletezhet az átmeneti ágban.
3. Miután a `project.json` fájlt adnak hozzá, látni fogja a függvényben az alábbi példához hasonló kimenetet a streamelési napló:

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
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>.Fsx kód újrafelhasználása
A többi kódot használhatja `.fsx` fájlok használatával olyan `#load` irányelv. Példa:

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

Elérési utak biztosít a `#load` helyéhez viszonyított irányelv vannak a `.fsx` fájlt.

* `#load "logger.fsx"` betölt egy fájlt, a függvény mappában található.
* `#load "package\logger.fsx"` betölt egy fájlt a `package` mappája a függvényt.
* `#load "..\shared\mylogger.fsx"` betölt egy fájlt a `shared` mappa azonos szinten, a függvény mappába, azaz közvetlenül a `wwwroot`.

A `#load` irányelv csak együttműködik `.fsx` (F# parancsfájl) fájlokat, és nem a `.fs` fájlokat.

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [F#Útmutató](/dotnet/articles/fsharp/index)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* [Az Azure Functions tesztelése](functions-test-a-function.md)
* [Az Azure Functions méretezése](functions-scale.md)

