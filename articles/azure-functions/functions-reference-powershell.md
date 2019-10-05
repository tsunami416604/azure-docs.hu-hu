---
title: PowerShell fejlesztői referenciája Azure Functions
description: Ismerje meg, hogyan fejlesztheti a függvényeket a PowerShell használatával.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 6cf03d1269cac5dcfa67c2d4778be3fce9ee63aa
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973370"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell fejlesztői útmutató

Ez a cikk részletesen ismerteti, hogyan írhat Azure Functions a PowerShell használatával.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

A PowerShell Azure-függvény (Function) egy PowerShell-parancsfájlként jelenik meg, amely az aktiváláskor fut. Mindegyik függvény parancsfájl egy kapcsolódó `function.json` fájllal rendelkezik, amely meghatározza, hogy a függvény hogyan viselkedik, például hogyan aktiválódik, valamint a bemeneti és kimeneti paramétereket. További információt az [Eseményindítók és a kötések című cikkben](functions-triggers-bindings.md)talál. 

Más típusú függvényekhez hasonlóan a PowerShell-parancsfájlok is a `function.json` fájlban meghatározott összes bemeneti kötés nevével egyező paramétereket fogadnak. Egy `TriggerMetadata` paraméter is át lett adva, amely további információkat tartalmaz a függvényt elindító triggerről.

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői referenciát](functions-reference.md). Az első PowerShell-függvény létrehozásához a [PowerShell](functions-create-first-function-powershell.md) functions rövid útmutatóját is be kell fejeznie.

## <a name="folder-structure"></a>Mappa szerkezete

Egy PowerShell-projekthez szükséges mappastruktúrát a következőhöz hasonlóan néz ki. Ez az alapértelmezett érték módosítható. További információért lásd az alábbi [scriptFile](#configure-function-scriptfile) szakaszt.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

A projekt gyökerében található egy megosztott [`host.json`](functions-host-json.md) fájl, amely a Function alkalmazás konfigurálására használható. Minden függvényhez tartozik egy mappa, amely a saját kódjával (. ps1) és a kötési konfigurációs fájllal (`function.json`) rendelkezik. A function. JSON fájljának szülő könyvtárának neve mindig a függvény neve.

Bizonyos kötések `extensions.csproj` fájl jelenlétét igénylik. A functions futtatókörnyezet [2. x verziójában](functions-versions.md) szükséges kötési kiterjesztések a `extensions.csproj` fájlban vannak definiálva, a `bin` mappában található tényleges függvénytár-fájlokkal. Helyi fejlesztés esetén [regisztrálnia kell a kötési bővítményeket](functions-bindings-register.md#extension-bundles). A Azure Portal funkcióinak fejlesztésekor ez a regisztráció történik.

A PowerShell-függvény alkalmazásaiban lehet, hogy `profile.ps1`, amely akkor fut le, amikor egy Function alkalmazás elindul (más néven a *[hidegindító kezdete](#cold-start)* ). További információ: PowerShell- [profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell-parancsfájl definiálása függvényként

Alapértelmezés szerint a functions futtatókörnyezet `run.ps1` értékben keresi a függvényt, ahol a `run.ps1` a hozzá tartozó `function.json`.

A szkript több argumentumot adott meg a végrehajtás során. A paraméterek kezeléséhez adjon hozzá egy `param` blokkot a parancsfájl elejéhez, ahogy az alábbi példában látható:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata paraméter

A `TriggerMetadata` paraméterrel további információkat adhat meg a triggerről. A további metaadatok a kötéstől függenek, de mindegyikük tartalmaz egy `sys` tulajdonságot, amely a következő adatokat tartalmazza:

```powershell
$TriggerMetadata.sys
```

| Tulajdonság   | Description                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Ha UTC-ben a függvény aktiválva lett        | DateTime |
| MethodName | Az aktivált függvény neve     | Karakterlánc   |
| RandGuid   | a függvény végrehajtásának egyedi GUID azonosítója | Karakterlánc   |

Minden trigger típushoz eltérő metaadatok vannak megadva. Például a `QueueTrigger` `$TriggerMetadata` a következőt tartalmazza: `InsertionTime`, `Id`, `DequeueCount`, többek között. Az üzenetsor-eseményindító metaadataival kapcsolatos további információkért nyissa meg a [várólista-eseményindítók hivatalos dokumentációját](functions-bindings-storage-queue.md#trigger---message-metadata). Tekintse meg a dokumentációt azon [Eseményindítók](functions-triggers-bindings.md) használatával kapcsolatban, amelyekkel megtudhatja, mi történik az eseményindító metaadatainak között.

## <a name="bindings"></a>Kötések

A PowerShellben a [kötések](functions-triggers-bindings.md) konfigurálva és definiálva vannak egy függvény function. JSON fájljában. A függvények számos módon működnek együtt a kötésekkel.

### <a name="reading-trigger-and-input-data"></a>Trigger-és bemeneti adatok olvasása

Az trigger és a bemeneti kötések a függvénynek átadott paraméterekként lesznek beolvasva. A bemeneti kötések `direction` készlettel rendelkeznek a function. JSON fájlban `in` értékre. Az `function.json` értékben definiált `name` tulajdonság a paraméter neve, a `param` blokkban. Mivel a PowerShell nevesített paramétereket használ a kötéshez, a paraméterek sorrendje nem számít. Az ajánlott eljárás azonban a `function.json` által meghatározott kötések sorrendjét követi.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Kimeneti adatokat írunk

A functions-ben egy kimeneti kötés `direction` értékkel rendelkezik, amely a function. JSON fájlban `out`. A `Push-OutputBinding` parancsmaggal írhat egy kimeneti kötést, amely a functions Runtime számára érhető el. A kötés `name` tulajdonsága minden esetben a `function.json` paraméterben meghatározottak szerint megfelel a `Push-OutputBinding` parancsmag `Name` paraméterének.

A következő bemutatja, hogyan hívható meg `Push-OutputBinding` a függvény parancsfájljában:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

A folyamaton keresztül egy adott kötés értékét is átadhatja.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

a `Push-OutputBinding` a (z) `-Name` számára megadott érték alapján eltérően viselkedik:

* Ha a megadott név nem oldható fel érvényes kimeneti kötésre, akkor hiba történik.

* Ha a kimeneti kötés fogadja az értékek gyűjteményét, többször is meghívhatja a `Push-OutputBinding` értéket a több érték leküldéséhez.

* Ha a kimeneti kötés csak egyszeres értéket fogad el, a `Push-OutputBinding` második alkalommal meghívja a hibát.

#### <a name="push-outputbinding-syntax"></a>@no__t – 0 szintaxis

A következő érvényes paraméterek a `Push-OutputBinding` hívásához:

| Name (Név) | Type | Pozíció | Leírás |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Sztring | 1 | A beállítani kívánt kimeneti kötés neve. |
| **`-Value`** | Object | 2 | A beállítani kívánt kimeneti kötés értéke, amelyet a rendszer a folyamat ByValue fogad el. |
| **`-Clobber`** | SwitchParameter | Elemzi | Választható Ha meg van adva, a megadott kimeneti kötéshez beállított értéket kényszeríti ki. | 

A következő közös paraméterek is támogatottak: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

További információ: [About általánosparaméterek](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Leküldéses OutputBinding – példa: HTTP-válaszok

Egy HTTP-trigger egy `response` nevű kimeneti kötést használó választ ad vissza. A következő példában a `response` kimeneti kötésének értéke "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Mivel a kimenet a HTTP, amely csak egy egyedi értéket fogad el, a rendszer hibát jelez, ha a `Push-OutputBinding` neve második alkalommal történik.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Az olyan kimenetek esetében, amelyek csak egyszeres értékeket fogadnak el, a `-Clobber` paraméterrel felülbírálhatja a régi értéket, ahelyett, hogy hozzá szeretne adni egy gyűjteményhez. Az alábbi példa azt feltételezi, hogy már hozzáadott egy értéket. @No__t-0 használatával a következő példa válasza felülbírálja a meglévő értéket, hogy a "kimeneti #3" értéket adja vissza:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Leküldéses OutputBinding – példa: Üzenetsor kimeneti kötése

a `Push-OutputBinding` az adatokat kimeneti kötésekre küldi, például egy [Azure üzenetsor-tároló kimeneti kötését](functions-bindings-storage-queue.md#output). A következő példában a várólistára írt üzenet "output #1" értékkel rendelkezik:

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A tárolási várólista kimeneti kötése több kimeneti értéket is elfogad. Ebben az esetben a következő példát hívja meg a várólistára való első írás után, amely két elemből áll: "output #1" és "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

A következő példa, amikor az előző kettő után meghívja, két további értéket ad hozzá a kimeneti gyűjteményhez:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

A várólistába íráskor az üzenet a következő négy értéket tartalmazza: "output #1," output #2 "," output #3 "és" output #4 ".

#### <a name="get-outputbinding-cmdlet"></a>@no__t – 0 parancsmag

A `Get-OutputBinding` parancsmaggal kérheti le a kimeneti kötésekhez jelenleg beállított értékeket. Ez a parancsmag egy olyan szórótábla kérdez le, amely tartalmazza a kimeneti kötések nevét a megfelelő értékekkel. 

Az alábbi példa a `Get-OutputBinding` használatával ad vissza aktuális kötési értékeket:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

a `Get-OutputBinding` egy `-Name` nevű paramétert is tartalmaz, amely a visszaadott kötés szűrésére használható, ahogy az alábbi példában is látható:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

A helyettesítő karakterek (*) a `Get-OutputBinding` esetében támogatottak.

## <a name="logging"></a>Naplózás

A PowerShell-függvények naplózása ugyanúgy működik, mint a rendszeres PowerShell-naplózás. A naplózási parancsmagok segítségével írhat az egyes kimeneti adatfolyamokhoz. Minden parancsmag a függvények által használt naplózási szintre mutat.

| Függvények naplózási szintje | Naplózási parancsmag |
| ------------- | -------------- |
| Hiba | **`Write-Error`** |
| Figyelmeztetés | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | _Az_ adatszint naplózásának megírása. |
| Hibakeresés | **`Write-Debug`** |
| Híváslánc | **`Write-Progress`** <br /> **`Write-Verbose`** |

Ezen parancsmagok mellett a folyamatba írt összes adat át lesz irányítva a `Information` naplózási szintre, és az alapértelmezett PowerShell-formázással jelenik meg.

> [!IMPORTANT]
> A `Write-Verbose` vagy `Write-Debug` parancsmagok használata nem elegendő a részletes és hibakeresési szintű naplózás megjelenítéséhez. A naplózási szint küszöbértékét is be kell állítania, amely kijelenti, hogy milyen szintű naplókra van szüksége. További információ: [az alkalmazás naplózási szintjének konfigurálása](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>A Function app naplózási szintjének konfigurálása

Azure Functions segítségével meghatározhatja a küszöbértéket, hogy könnyen szabályozható legyen a függvények írási módja a naplókba. A konzolra írt összes nyomkövetés küszöbértékének megadásához használja a `logging.logLevel.default` tulajdonságot a [`host.json` file][a host. json dokumentációja]. Ez a beállítás a Function alkalmazás összes függvényére érvényes.

A következő példa beállítja a küszöbértéket az összes függvény részletes naplózásának engedélyezéséhez, de a küszöbértéket úgy állítja be, hogy engedélyezze a hibakeresési naplózást egy `MyFunction` nevű függvénynél:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

További információ: [a Host. JSON dokumentációja].

### <a name="viewing-the-logs"></a>A naplók megtekintése

Ha az függvényalkalmazás az Azure-ban fut, akkor a Application Insights használatával figyelheti. Olvassa el a [figyelési Azure functions](functions-monitoring.md) , ha többet szeretne megtudni a függvények naplóinak megtekintéséről és lekérdezéséről.

Ha a függvényalkalmazást helyileg futtatja fejlesztésre, a a naplófájlok alapértelmezése a fájlrendszer. A naplók a konzolon való megjelenítéséhez állítsa a `AZURE_FUNCTIONS_ENVIRONMENT` környezeti változót `Development` értékre a függvényalkalmazás elindítása előtt.

## <a name="triggers-and-bindings-types"></a>Eseményindítók és kötések típusai

Számos eseményindító és kötés érhető el a Function alkalmazással való használathoz. Az eseményindítók és kötések teljes listája [itt található](functions-triggers-bindings.md#supported-bindings).

Az összes eseményindító és kötés a kódban szerepel néhány valós adattípus formájában:

* Szórótábla
* Karakterlánc
* bájt []
* int
* double
* HttpRequestContext
* HttpResponseContext

A lista első öt típusa a standard .NET-típusok. Az utolsó kettőt csak a [HttpTrigger trigger](#http-triggers-and-bindings)használja.

A függvények minden kötési paraméterének a következő típusok egyikének kell lennie.

### <a name="http-triggers-and-bindings"></a>HTTP-eseményindítók és-kötések

A HTTP-és webhook-eseményindítók és a HTTP-kimeneti kötések a HTTP-üzenetküldést a kérelmek és válaszok objektumok használatával jelölik.

#### <a name="request-object"></a>Kérelem objektum

A parancsfájlba átadott kérelem-objektum `HttpRequestContext` típusú, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Description                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Egy objektum, amely tartalmazza a kérelem törzsét. a `Body` a legjobb típusra van szerializálva az adathalmaz alapján. Ha például az adatfájl JSON, a rendszer szórótábla adja át. Ha az érték karakterlánc, akkor karakterláncként adja át a rendszer. | object |
| **`Headers`** | A kérések fejléceit tartalmazó szótár.                | Szótár < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Method`** | A kérelem HTTP-metódusa.                                | Karakterlánc                    |
| **`Params`**  | Egy objektum, amely a kérelem útválasztási paramétereit tartalmazza. | Szótár < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Query`** | Egy objektum, amely tartalmazza a lekérdezési paramétereket.                  | Szótár < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Url`** | A kérelem URL-címe.                                        | Karakterlánc                    |

<sup>@no__t – 1</sup> Az összes `Dictionary<string,string>` kulcs kis-és nagybetűk megkülönböztetése.

#### <a name="response-object"></a>Válaszobjektum

A visszaküldeni kívánt válasz objektum `HttpResponseContext` típusú, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Description                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Egy objektum, amely tartalmazza a válasz törzsét.           | object                    |
| **`ContentType`** | Egy rövid kéz a válasz tartalomtípusának beállításához. | Karakterlánc                    |
| **`Headers`** | Egy objektum, amely tartalmazza a válasz fejléceit.               | Szótár vagy szórótábla   |
| **`StatusCode`**  | A válasz HTTP-állapotkódot.                       | karakterlánc vagy int             |

#### <a name="accessing-the-request-and-response"></a>A kérelem és válasz elérése

Ha HTTP-eseményindítókkal dolgozik, ugyanúgy érheti el a HTTP-kérést, mint bármely más bemeneti kötést. A `param` blokkban van.

Egy `HttpResponseContext` objektummal választ adhat vissza, ahogy az az alábbi képen is látható:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

A függvény meghívásának eredménye a következő:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Triggerek és kötések típusa – öntés

Bizonyos kötésekhez, például a blob-kötésekhez, meg tudja adni a paraméter típusát.

Ha például a blob Storage-ból karakterláncként megadott adatokkal szeretne rendelkezni, adja hozzá a következő típust a `param` blokkhoz:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profil

A PowerShellben van egy PowerShell-profil fogalma. Ha még nem ismeri a PowerShell-profilokat, tekintse meg a következő témakört: [About Profiles](/powershell/module/microsoft.powershell.core/about/about_profiles).

A PowerShell-függvények esetében a profil parancsfájlja a Function alkalmazás indításakor végrehajtódik. A Function apps az első üzembe helyezéskor kezdődik, és az üresjárat után ([Cold Start](#cold-start)).

Ha olyan eszközökkel hoz létre Function alkalmazást, mint például a Visual Studio Code és a Azure Functions Core Tools, akkor a rendszer az alapértelmezett `profile.ps1` értéket hozza létre. Az alapértelmezett profil a [GitHub-tárház alapvető eszközein](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) marad, és a következőket tartalmazza:

* Automatikus MSI-hitelesítés az Azure-ba.
* Ha szeretné, bekapcsolhatja a Azure PowerShell `AzureRM` PowerShell-aliast.

## <a name="powershell-version"></a>PowerShell-verzió

A következő táblázat a függvények futtatókörnyezetének egyes főbb verziói által használt PowerShell-verziót mutatja be:

| Függvények verziója | PowerShell-verzió                             |
|-------------------|------------------------------------------------|
| 1. x               | Windows PowerShell 5,1 (a futtatókörnyezet zárolta) |
| 2. x               | 6\. PowerShell-mag                              |

Az aktuális verziót úgy tekintheti meg, hogy bármely függvényből kinyomtatja a `$PSVersionTable` értéket.

## <a name="dependency-management"></a>Függőségkezelés

A PowerShell-függvények támogatják a szolgáltatásban a [PowerShell](https://www.powershellgallery.com) -katalógus moduljainak letöltését és felügyeletét. Ha módosítja a Host. JSON fájlt, és a managedDependency enabled tulajdonságot True értékre állítja, a rendszer feldolgozza a követelmények. psd1 fájlját. A rendszer automatikusan letölti és elérhetővé teszi a megadott modulokat a függvény számára. 

A jelenleg támogatott modulok maximális száma 10. A támogatott szintaxis a MajorNumber. * vagy a modul pontos verziója, ahogy az alább látható. Az Azure az modul alapértelmezés szerint az új PowerShell-függvény alkalmazás létrehozásakor szerepel.

A nyelvi feldolgozó a frissített modulokat is felveszi az újraindításra.

host.json
```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

követelmények. psd1

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

A következő beállítások állnak rendelkezésre a felügyelt függőségek letöltésének és telepítésének megváltoztatásához. Az alkalmazás frissítése a MDMaxBackgroundUpgradePeriod-on belül indul el, és a frissítési folyamat körülbelül MDNewSnapshotCheckPeriod belül elvégezhető.

| függvényalkalmazás beállítás              | Alapértelmezett érték             | Leírás                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| MDMaxBackgroundUpgradePeriod      | "7.00:00:00" (7 nap)     | Minden PS-feldolgozó kezdeményezi a modul frissítésének ellenőrzését a PS-galériában a munkavégző folyamat indításakor és minden MDMaxBackgroundUpgradePeriod után. Ha a PS-katalógusban új modul-verziók érhetők el, akkor a rendszer a PS-feldolgozók számára elérhető fájlrendszerbe telepíti őket. Ennek az értéknek a csökkentése lehetővé teszi, hogy a függvény alkalmazása hamarabb lekérje a modul újabb verzióit, de az alkalmazás erőforrás-használatát is megnöveli (hálózati I/O-, CPU-és tárolási). Ennek az értéknek a növelésével csökken az alkalmazás erőforrásainak használata, de az új modul-verziók az alkalmazásba való kézbesítése is késleltethető.      | 
| MDNewSnapshotCheckPeriod          | "01:00:00" (1 óra)       | Miután telepítette az új modul verzióit a fájlrendszerre, minden PS-feldolgozót újra kell indítani. A PS-feldolgozók újraindítása hatással lehet az alkalmazás rendelkezésre állására, mert az az aktuális függvény hívásait megszakítja. Amíg az összes PS-feldolgozó újraindul, a függvény meghívása a régi vagy az új modul verzióját is használhatja. Az összes PS-feldolgozó újraindítása a MDNewSnapshotCheckPeriod-en belül fejeződik be. Ennek az értéknek a növelésével csökken a megszakítások gyakorisága, de az is előfordulhat, hogy a függvény meghívásakor a régi vagy az új modul nem determinisztikus módon-verzióit használja. |
| MDMinBackgroundUpgradePeriod      | "1.00:00:00" (1 nap)     | A gyakori munkavégző újraindítások esetén a modulok frissítésének ellenőrzése nem történik meg, ha bármelyik feldolgozó már kezdeményezte az utolsó MDMinBackgroundUpgradePeriod belül. |

A saját egyéni moduljainak kihasználása kicsit eltér a megszokott módon.

Ha a modult a helyi gépen telepíti, az a `$env:PSModulePath` globálisan elérhető mappáiba kerül. Mivel a függvény az Azure-ban fut, nem fog hozzáférni a gépen telepített modulokhoz. Ehhez az szükséges, hogy a PowerShell-függvényhez tartozó `$env:PSModulePath` a szokásos PowerShell-parancsfájlban `$env:PSModulePath` értéktől eltérő legyen.

A functions-ben a `PSModulePath` két elérési utat tartalmaz:

* @No__t-0 mappa, amely a függvényalkalmazás gyökerében található.
* Egy `Modules` mappa elérési útja, amely a PowerShell nyelvi feldolgozón belül él.

### <a name="function-app-level-modules-folder"></a>Függvény alkalmazás-szintű `Modules` mappa

Egyéni modulok használatához olyan modulokat helyezhet el, amelyeken a függvények egy `Modules` mappától függenek. Ebből a mappából a modulok automatikusan elérhetők a functions Runtime számára. A Function alkalmazás bármely funkciója használhatja ezeket a modulokat. 

> [!NOTE]
> A követelmények. psd1 fájlban megadott modulokat a rendszer automatikusan letölti és belefoglalja az elérési útra, így nem kell felvennie őket a modulok mappába. Ezeket a rendszer helyileg tárolja a $env: LOCALAPPDATA/AzureFunctions mappában és a/data/ManagedDependencies mappában a felhőben való futtatáskor.

Az egyéni modul funkció kihasználása érdekében hozzon létre egy `Modules` mappát a Function alkalmazás gyökerében. Másolja a függvényekben használni kívánt modulokat erre a helyre.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

A modules mappában a Function alkalmazásnak a következő mappastruktúrát kell megjelennie:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

A Function app indításakor a PowerShell nyelvi feldolgozó hozzáadja ezt a `Modules` mappát a `$env:PSModulePath`-hez, így a modul automatikus betöltését ugyanúgy használhatja, mint egy normál PowerShell-parancsfájlban.

### <a name="language-worker-level-modules-folder"></a>Nyelvi feldolgozói szint @no__t – 0 mappa

A PowerShell nyelvi feldolgozója általában számos modult használ. Ezek a modulok a `PSModulePath` utolsó helyén vannak meghatározva. 

A modulok aktuális listája a következő:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): az archívumok kezeléséhez használt modul, például `.zip`, `.nupkg` és egyebek.
* **ThreadJob**: A PowerShell-feladatok API-k szálon alapuló implementálása.

A modulok legújabb verzióját a függvények használják. Ezen modulok adott verziójának használatához az adott verziót a Function app `Modules` mappájába helyezheti el.

## <a name="environment-variables"></a>Környezeti változók

A függvények, az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai a végrehajtás során környezeti változókként jelennek meg. Ezeket a beállításokat `$env:NAME_OF_ENV_VAR` használatával érheti el, ahogy az az alábbi példában is látható:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Helyileg futtatva az Alkalmazásbeállítások a [Local. Settings. JSON](functions-run-local.md#local-settings-file) Project fájlból lesznek beolvasva.

## <a name="concurrency"></a>Egyidejűség

Alapértelmezés szerint a függvények PowerShell-futtatókörnyezete egyszerre csak egy függvény hívását képes feldolgozni. Előfordulhat azonban, hogy ez a párhuzamossági szint nem elegendő a következő helyzetekben:

* Ha nagy számú hívást próbál meg egyszerre kezelni.
* Ha olyan függvényekkel rendelkezik, amelyek más függvényeket hívnak meg ugyanazon a Function alkalmazáson belül.

Ezt a viselkedést úgy módosíthatja, ha a következő környezeti változót egész értékre állítja:

```
PSWorkerInProcConcurrencyUpperBound
```

Ezt a környezeti változót a függvényalkalmazás [alkalmazás beállításaiban](functions-app-settings.md) állíthatja be.

### <a name="considerations-for-using-concurrency"></a>A párhuzamosság használatának szempontjai

Alapértelmezés szerint a PowerShell _egyszálas_ parancsfájlkezelési nyelv. A párhuzamosság azonban több PowerShell-futási terek is hozzáadható ugyanabban a folyamatban. A létrehozott futási terek mérete megegyezik a PSWorkerInProcConcurrencyUpperBound alkalmazás beállításával. Az átviteli sebességet a kiválasztott csomagban elérhető CPU és memória mennyisége befolyásolja.

A Azure PowerShell egyes _folyamat-szintű_ kontextusokat és állapotot használ, hogy segítsen a felesleges gépelésben. Ha azonban a függvény alkalmazásban bekapcsolja a párhuzamosságot, és meghívja az állapotot megváltoztató műveleteket, akkor a verseny feltételei is megváltozhatnak. Ezek a versenyhelyzet nehéz hibakeresést végezni, mert egy hívás egy bizonyos állapotra támaszkodik, és a másik meghívás megváltoztatta az állapotot.

Az Azure PowerShell-ben óriási érték van, mivel néhány művelet jelentős időt vehet igénybe. Azonban körültekintően kell eljárnia. Ha azt gyanítja, hogy a verseny feltételét tapasztalja, állítsa a PSWorkerInProcConcurrencyUpperBound alkalmazás beállítását `1` értékre, és Ehelyett használja a [nyelv munkavégző folyamat szintjének elkülönítését](functions-app-settings.md#functions_worker_process_count) a párhuzamossághoz.

## <a name="configure-function-scriptfile"></a>A függvény konfigurálása @no__t – 0

Alapértelmezés szerint a rendszer a PowerShell-függvényt `run.ps1` fájlból hajtja végre, amely ugyanazokat a szülő könyvtárat osztja meg, mint a megfelelő `function.json`.

A `function.json` `scriptFile` tulajdonsága a következő példához hasonló mappa-struktúra beszerzésére használható:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Ebben az esetben a `myFunction` `function.json` tartalmaz egy `scriptFile` tulajdonságot, amely a fájlra hivatkozik, és az exportált függvénnyel fut.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>PowerShell-modulok használata BelépésiPont konfigurálásával

Ez a cikk a sablonok által generált alapértelmezett `run.ps1` parancsfájl PowerShell-függvényeit mutatja.
A függvényeket azonban PowerShell-modulokban is felveheti. A modulban a function. JSON konfigurációs fájl `scriptFile` és `entryPoint` mezőinek használatával hivatkozhat az adott függvény kódjára.

Ebben az esetben a `entryPoint` a `scriptFile`-ben hivatkozott PowerShell-modul függvényének vagy parancsmagjának a neve.

Vegye figyelembe a következő mappastruktúrát:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Ahol a `PSFunction.psm1` a következőket tartalmazza:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Ebben a példában a `myFunction` konfigurációjában szerepel egy `scriptFile` tulajdonság, amely `PSFunction.psm1` értékre hivatkozik, amely egy másik mappában található PowerShell-modul.  A `entryPoint` tulajdonság a `Invoke-PSTestFunc` függvényre hivatkozik, amely a modul belépési pontja.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Ezzel a konfigurációval a `Invoke-PSTestFunc` a `run.ps1` értéknek megfelelően lesz végrehajtva.

## <a name="considerations-for-powershell-functions"></a>A PowerShell-függvények szempontjai

A PowerShell-függvények használatakor vegye figyelembe az alábbi részekben ismertetett szempontokat.

### <a name="cold-start"></a>Hidegindító

Azure Functions a [kiszolgáló nélküli üzemeltetési modellben](functions-scale.md#consumption-plan)való fejlesztésekor a hideg indítás a valóság. A *hűtőházi kezdés* azt az időtartamot jelenti, ameddig a Function alkalmazásnak futnia kell a kérelem feldolgozásához. A hidegindító folyamat gyakrabban fordul elő a fogyasztási tervben, mert a Function alkalmazás leáll az inaktivitási időszakok során.

### <a name="bundle-modules-instead-of-using-install-module"></a>Köteg modulok a `Install-Module` használata helyett

A szkript minden meghívásnál fut. Kerülje a `Install-Module` használatát a parancsfájlban. Ehelyett használja a `Save-Module` értéket a közzététel előtt, így a függvénynek nem kell időt pazarolnia a modul letöltésével. Ha a ritkán használt funkciók hatással vannak a függvényekre, érdemes lehet a Function alkalmazást egy olyan [app Service-csomagra](functions-scale.md#app-service-plan) telepíteni *, amely* a [prémium szintű csomagra](functions-scale.md#premium-plan)van beállítva.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)

[a Host. JSON dokumentációja]: functions-host-json.md
