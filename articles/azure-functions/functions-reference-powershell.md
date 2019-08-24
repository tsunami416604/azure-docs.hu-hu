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
ms.openlocfilehash: 8c6f13f85b692d2405928fe06605d8b2ac0ec8e7
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012716"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell fejlesztői útmutató

Ez a cikk részletesen ismerteti, hogyan írhat Azure Functions a PowerShell használatával.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

A PowerShell Azure-függvény (Function) egy PowerShell-parancsfájlként jelenik meg, amely az aktiváláskor fut. Mindegyik függvény parancsfájlhoz tartozik egy `function.json` kapcsolódó fájl, amely meghatározza, hogy a függvény hogyan viselkedik, például hogyan aktiválódik, valamint a bemeneti és kimeneti paramétereket. További információt az [Eseményindítók és](functions-triggers-bindings.md)a kötések című cikkben talál. 

Más típusú függvényekhez hasonlóan a PowerShell-parancsfájlok is a `function.json` fájlban meghatározott összes bemeneti kötés nevével egyező paramétereket fogadnak el. Egy `TriggerMetadata` paraméter is át lett adva, amely további információkat tartalmaz a függvényt elindító triggerről.

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

A projekt gyökerében található egy megosztott [`host.json`](functions-host-json.md) fájl, amely a Function alkalmazás konfigurálására használható. Minden függvényhez tartozik egy mappa, amely a saját kódjával (. ps1) és a kötési`function.json`konfigurációs fájllal () rendelkezik. A function. JSON fájljának szülő könyvtárának neve mindig a függvény neve.

Bizonyos kötések egy `extensions.csproj` fájl jelenlétét igénylik. A függvények futtatókörnyezet [2. x verziójában](functions-versions.md) szükséges kötési kiterjesztések a `extensions.csproj` fájlban vannak definiálva, a `bin` mappában lévő tényleges függvénytár-fájlokkal. Helyi fejlesztés esetén regisztrálnia kell a [kötési bővítményeket](functions-bindings-register.md#extension-bundles). A Azure Portal funkcióinak fejlesztésekor ez a regisztráció történik.

A PowerShell-függvény alkalmazásaiban megadhatja, hogy `profile.ps1` a rendszer mikor fusson, amikor egy Function-alkalmazás elindul (más néven a *[hidegindító kezdete](#cold-start)* ). További információ: PowerShell- [profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell-parancsfájl definiálása függvényként

Alapértelmezés szerint a functions Runtime a függvényét keresi a `run.ps1`alkalmazásban `run.ps1` , ahol ugyanazokat a szülő könyvtárat osztja `function.json`meg a megfelelővel.

A szkript több argumentumot adott meg a végrehajtás során. A paraméterek kezeléséhez adjon hozzá egy `param` blokkot a szkript elejéhez, ahogy az a következő példában látható:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata paraméter

A `TriggerMetadata` paraméter használatával további információkat adhat meg az aktiválásról. A további metaadatok a kötéstől a kötéstől függenek, de `sys` mindegyikük tartalmaz egy tulajdonságot, amely a következő adatokat tartalmazza:

```powershell
$TriggerMetadata.sys
```

| Tulajdonság   | Description                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Ha UTC-ben a függvény aktiválva lett        | DateTime |
| Metódus neve | Az aktivált függvény neve     | Karakterlánc   |
| RandGuid   | a függvény végrehajtásának egyedi GUID azonosítója | Karakterlánc   |

Minden trigger típushoz eltérő metaadatok vannak megadva. `$TriggerMetadata` A for például a `InsertionTime`, `QueueTrigger` `Id`,, többek között a következőt tartalmazza:. `DequeueCount` Az üzenetsor-eseményindító metaadataival kapcsolatos további információkért nyissa meg a [várólista-eseményindítók hivatalos dokumentációját](functions-bindings-storage-queue.md#trigger---message-metadata). Tekintse meg a dokumentációt azon [Eseményindítók](functions-triggers-bindings.md) használatával kapcsolatban, amelyekkel megtudhatja, mi történik az eseményindító metaadatainak között.

## <a name="bindings"></a>Kötések

A PowerShellben [](functions-triggers-bindings.md) a kötések konfigurálva és definiálva vannak egy függvény function. JSON fájljában. A függvények számos módon működnek együtt a kötésekkel.

### <a name="reading-trigger-and-input-data"></a>Trigger-és bemeneti adatok olvasása

Az trigger és a bemeneti kötések a függvénynek átadott paraméterekként lesznek beolvasva. A bemeneti kötések a `direction` function. `in` JSON fájlban vannak beállítva. A `name` `param` ben `function.json` definiált tulajdonság a paraméter neve, a blokkban. Mivel a PowerShell nevesített paramétereket használ a kötéshez, a paraméterek sorrendje nem számít. Azonban az ajánlott eljárás az, hogy kövesse a ben `function.json`definiált kötések sorrendjét.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Kimeneti adatokat írunk

A functions szolgáltatásban a kimeneti kötés `direction` a Function `out` . JSON fájlban van beállítva. Egy kimeneti kötésre a `Push-OutputBinding` parancsmag használatával írhat, amely a functions futtatókörnyezet számára érhető el. Minden esetben `name` a kötésnek a `Push-OutputBinding` parancsmag `Name` paraméterében `function.json` megadott tulajdonsága megfelel a parancsmagnak.

A következő bemutatja, hogyan hívhat `Push-OutputBinding` meg a függvény parancsfájljában:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

A folyamaton keresztül egy adott kötés értékét is átadhatja.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`a a következőhöz `-Name`megadott érték alapján viselkedik:

* Ha a megadott név nem oldható fel érvényes kimeneti kötésre, akkor hiba történik.

* Ha a kimeneti kötés fogadja az értékek gyűjteményét, többször is `Push-OutputBinding` meghívhatja a több érték leküldését.

* Ha a kimeneti kötés csak egyszeres értéket fogad el, `Push-OutputBinding` akkor a második alkalommal hibát jelez.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`Szintaxis

A következő érvényes paraméterek használhatók a `Push-OutputBinding`híváshoz:

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

Egy HTTP-trigger a nevű `response`kimeneti kötés használatával ad vissza egy választ. A következő példában a kimeneti kötés `response` értéke "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Mivel a kimenet a http, amely csak egy egyedi értéket fogad el, akkor a `Push-OutputBinding` rendszer a második időpontjának meghívásakor hibát jelez.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Az olyan kimenetek esetében, amelyek csak egyszeres értékeket fogadnak `-Clobber` el, a paraméter használatával felülbírálhatja a régi értéket, és nem kell hozzáadnia a gyűjteményhez. Az alábbi példa azt feltételezi, hogy már hozzáadott egy értéket. A használatával `-Clobber`a következő példa válasza felülbírálja a meglévő értéket, hogy a "output #3" értéket adja vissza:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Leküldéses OutputBinding – példa: Üzenetsor kimeneti kötése

`Push-OutputBinding`az adatokat kimeneti kötésekre küldi, például egy [Azure üzenetsor-tároló kimeneti kötését](functions-bindings-storage-queue.md#output). A következő példában a várólistára írt üzenet "output #1" értékkel rendelkezik:

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

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`parancsmag

`Get-OutputBinding` A parancsmag segítségével beolvashatja a kimeneti kötésekhez jelenleg beállított értékeket. Ez a parancsmag egy olyan szórótábla kérdez le, amely tartalmazza a kimeneti kötések nevét a megfelelő értékekkel. 

Az alábbi példa a használatával `Get-OutputBinding` az aktuális kötési értékek visszaküldését szemlélteti:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`egy nevű `-Name`paramétert is tartalmaz, amely a visszaadott kötés szűrésére használható, ahogy az alábbi példában is látható:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

A-ben a `Get-OutputBinding`helyettesítő karakterek (*) támogatottak.

## <a name="logging"></a>Naplózás

A PowerShell-függvények naplózása ugyanúgy működik, mint a rendszeres PowerShell-naplózás. A naplózási parancsmagok segítségével írhat az egyes kimeneti adatfolyamokhoz. Minden parancsmag a függvények által használt naplózási szintre mutat.

| Függvények naplózási szintje | Naplózási parancsmag |
| ------------- | -------------- |
| Hiba | **`Write-Error`** |
| Figyelmeztetés | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Az adatszint naplózásának megírása. |
| Hibakeresés | **`Write-Debug`** |
| Híváslánc | **`Write-Progress`** <br /> **`Write-Verbose`** |

Ezen parancsmagok mellett a folyamatba írt összes adat át lesz irányítva a `Information` naplózási szintre, és az alapértelmezett PowerShell-formázással jelenik meg.

> [!IMPORTANT]
> A vagy `Write-Verbose` `Write-Debug` a parancsmag használata nem elegendő a részletes és hibakeresési szintű naplózás megjelenítéséhez. A naplózási szint küszöbértékét is be kell állítania, amely kijelenti, hogy milyen szintű naplókra van szüksége. További információ: [az alkalmazás naplózási szintjének konfigurálása](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>A Function app naplózási szintjének konfigurálása

Azure Functions segítségével meghatározhatja a küszöbértéket, hogy könnyen szabályozható legyen a függvények írási módja a naplókba. A konzolra írt összes nyomkövetés küszöbértékének megadásához használja a `logging.logLevel.default` [Host. JSON] [ `host.json` fájlban]található tulajdonságot. Ez a beállítás a Function alkalmazás összes függvényére érvényes.

A következő példa a küszöbértéket állítja be az összes függvény részletes naplózásának engedélyezéséhez, de beállítja a küszöbértéket, hogy engedélyezze a hibakeresési naplózást egy nevű `MyFunction`függvénynél:

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

Ha a függvényalkalmazást helyileg futtatja fejlesztésre, a a naplófájlok alapértelmezése a fájlrendszer. A naplók a konzolon való megtekintéséhez állítsa a `AZURE_FUNCTIONS_ENVIRONMENT` környezeti `Development` változót a függvényalkalmazás elindítása előtt.

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

A parancsfájlba átadott kérelem-objektum típusa `HttpRequestContext`a következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Description                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Egy objektum, amely tartalmazza a kérelem törzsét. `Body`a a legjobb típusra van szerializálva az adathalmaz alapján. Ha például az adatfájl JSON, a rendszer szórótábla adja át. Ha az érték karakterlánc, akkor karakterláncként adja át a rendszer. | object |
| **`Headers`** | A kérések fejléceit tartalmazó szótár.                | Szótár < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Method`** | A kérelem HTTP-metódusa.                                | Karakterlánc                    |
| **`Params`**  | Egy objektum, amely a kérelem útválasztási paramétereit tartalmazza. | Szótár < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Query`** | Egy objektum, amely tartalmazza a lekérdezési paramétereket.                  | Szótár < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Url`** | A kérelem URL-címe.                                        | Karakterlánc                    |

<sup>*</sup>Az `Dictionary<string,string>` összes kulcs megkülönbözteti a kis-és nagybetűket.

#### <a name="response-object"></a>Válaszobjektum

A visszaküldeni kívánt válasz objektum típusa `HttpResponseContext`a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Description                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Egy objektum, amely tartalmazza a válasz törzsét.           | object                    |
| **`ContentType`** | Egy rövid kéz a válasz tartalomtípusának beállításához. | Karakterlánc                    |
| **`Headers`** | Egy objektum, amely tartalmazza a válasz fejléceit.               | Szótár vagy szórótábla   |
| **`StatusCode`**  | A válasz HTTP-állapotkódot.                       | karakterlánc vagy int             |

#### <a name="accessing-the-request-and-response"></a>A kérelem és válasz elérése

Ha HTTP-eseményindítókkal dolgozik, ugyanúgy érheti el a HTTP-kérést, mint bármely más bemeneti kötést. Ez a `param` blokkban található.

`HttpResponseContext` Egy objektummal választ adhat vissza, ahogy az az alábbi képen is látható:

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

Ha például a blob Storage-ból karakterláncként megadott adatokkal szeretne rendelkezni, adja hozzá a következő típust a saját `param` blokkhoz:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profil

A PowerShellben van egy PowerShell-profil fogalma. Ha még nem ismeri a PowerShell-profilokat, tekintse meg a következő témakört: [About Profiles](/powershell/module/microsoft.powershell.core/about/about_profiles).

A PowerShell-függvények esetében a profil parancsfájlja a Function alkalmazás indításakor végrehajtódik. A Function apps az első üzembe helyezéskor kezdődik, és az üresjárat után ([Cold Start](#cold-start)).

Ha eszközöket használó Function-alkalmazást hoz létre, például a Visual Studio Code-ot és a Azure functions Core toolst, a rendszer alapértelmezés szerint létrehoz egy alapértelmezett értéket `profile.ps1` . Az alapértelmezett profil a [GitHub-tárház alapvető eszközein](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) marad, és a következőket tartalmazza:

* Automatikus MSI-hitelesítés az Azure-ba.
* A Azure PowerShell `AzureRM` PowerShell-aliasok bekapcsolásának lehetősége, ha szeretné.

## <a name="powershell-version"></a>PowerShell-verzió

A következő táblázat a függvények futtatókörnyezetének egyes főbb verziói által használt PowerShell-verziót mutatja be:

| Függvények verziója | PowerShell-verzió                             |
|-------------------|------------------------------------------------|
| 1. x               | Windows PowerShell 5,1 (a futtatókörnyezet zárolta) |
| 2. x               | 6\. PowerShell-mag                              |

Az aktuális verziót bármely függvényből kinyomtatva `$PSVersionTable` láthatja.

## <a name="dependency-management"></a>Függőségkezelés

A PowerShell-függvények támogatják az Azure-modulok szolgáltatás általi kezelését. Ha módosítja a Host. JSON fájlt, és a managedDependency enabled tulajdonságot True értékre állítja, a rendszer feldolgozza a követelmények. psd1 fájlját. A rendszer automatikusan letölti és elérhetővé teszi a legújabb Azure-modulokat a függvény számára.

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
}
```

A saját egyéni moduljainak vagy moduljainak kihasználása a [PowerShell-Galéria](https://powershellgallery.com) egy kicsit más, mint a szokásos módon.

Ha telepíti a modult a helyi gépre, az a saját `$env:PSModulePath`globálisan elérhető mappáiba kerül. Mivel a függvény az Azure-ban fut, nem fog hozzáférni a gépen telepített modulokhoz. Ehhez az szükséges, `$env:PSModulePath` hogy a PowerShell `$env:PSModulePath` -függvény alkalmazása eltér a szokásos PowerShell-parancsfájloktól.

A függvények `PSModulePath` között két elérési út található:

* Egy `Modules` mappa, amely a függvényalkalmazás gyökérkönyvtárában található.
* Egy olyan `Modules` mappa elérési útja, amely a PowerShell nyelvi feldolgozón belül él.

### <a name="function-app-level-modules-folder"></a>Függvény alkalmazás-szintű `Modules` mappája

Ha egyéni modulokat vagy PowerShell-modulokat szeretne használni a PowerShell-Galéria, olyan modulokat helyezhet el, amelyeken `Modules` a függvények egy mappától függenek. Ebből a mappából a modulok automatikusan elérhetők a functions Runtime számára. A Function alkalmazás bármely funkciója használhatja ezeket a modulokat.

A szolgáltatás kihasználása érdekében hozzon létre `Modules` egy mappát a Function alkalmazás gyökerében. Mentse a függvényekben használni kívánt modulokat ezen a helyen.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

A `Save-Module` használatával mentheti a függvények által használt összes modult, vagy átmásolhatja a saját egyéni moduljait a `Modules` mappába. A modules mappában a Function alkalmazásnak a következő mappastruktúrát kell megjelennie:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

Ha elindítja a Function alkalmazást, a PowerShell nyelvi feldolgozó hozzáadja ezt `Modules` a mappát a `$env:PSModulePath` modulhoz, így a modulok automatikus betöltését ugyanúgy használhatja, mint egy normál PowerShell-parancsfájlban.

### <a name="language-worker-level-modules-folder"></a>Nyelvi feldolgozói szint `Modules` mappája

A PowerShell nyelvi feldolgozója általában számos modult használ. Ezek a modulok a utolsó helyén `PSModulePath`vannak meghatározva. 

A modulok aktuális listája a következő:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): az archívumok, `.zip`például, `.nupkg`és mások használatához használt modul.
* **ThreadJob**: A PowerShell-feladatok API-k szálon alapuló implementálása.

A modulok legújabb verzióját a függvények használják. A modulok adott verziójának használatához az adott verziót `Modules` a Function alkalmazás mappájába helyezheti el.

## <a name="environment-variables"></a>Környezeti változók

A függvények, az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai a végrehajtás során környezeti változókként jelennek meg. Ezeket a beállításokat a használatával `$env:NAME_OF_ENV_VAR`érheti el, ahogy az az alábbi példában is látható:

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

Alapértelmezés szerint a PowerShell egyszálas parancsfájlkezelési nyelv. A párhuzamosság azonban több PowerShell-futási terek is hozzáadható ugyanabban a folyamatban. Ez a funkció azt ismerteti, hogyan működik a Azure Functions PowerShell-futtatókörnyezet.

Ennek a megközelítésnek van néhány hátránya.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>A Egyidejűség csak olyan jól működik, mint a gép, amelyen fut

Ha a Function alkalmazás olyan [app Service](functions-scale.md#app-service-plan) -csomagon fut, amely csak egyetlen mag használatát támogatja, akkor a Egyidejűség nem fog sokat segíteni. Ennek oka, hogy nincsenek további magok a terhelés kiegyensúlyozásához. Ebben az esetben a teljesítmény akkor változhat, ha az önálló mag a futási terek közötti kontextusban van.

A használati [terv](functions-scale.md#consumption-plan) csak egy mag használatával fut, ezért nem használhatja a párhuzamosságot. Ha teljes mértékben ki szeretné használni a párhuzamosságot, helyette helyezze üzembe a függvényeket egy dedikált App Service-csomagon futó Function alkalmazásban, amely elegendő magot használ.

#### <a name="azure-powershell-state"></a>Azure PowerShell állapota

A Azure PowerShell egyes _folyamat-szintű_ kontextusokat és állapotot használ, hogy segítsen a felesleges gépelésben. Ha azonban a függvény alkalmazásban bekapcsolja a párhuzamosságot, és meghívja az állapotot megváltoztató műveleteket, akkor a verseny feltételei is megváltozhatnak. Ezek a versenyhelyzet nehéz hibakeresést végezni, mert egy hívás egy bizonyos állapotra támaszkodik, és a másik meghívás megváltoztatta az állapotot.

Az Azure PowerShell-ben óriási érték van, mivel néhány művelet jelentős időt vehet igénybe. Azonban körültekintően kell eljárnia. Ha azt gyanítja, hogy a verseny feltételét tapasztalja, állítsa a PSWorkerInProcConcurrencyUpperBound `1` az alkalmazásra, és Ehelyett használja a [nyelvi feldolgozói folyamat szintjének elkülönítését](functions-app-settings.md#functions_worker_process_count) a párhuzamossághoz.

## <a name="configure-function-scriptfile"></a>Függvény konfigurálása`scriptFile`

Alapértelmezés szerint a rendszer egy PowerShell-függvényt `run.ps1`hajt végre, egy olyan fájlt, amely ugyanazt a szülő könyvtárat `function.json`osztja meg a megfelelővel.

A (z) `function.json` tulajdonságaakövetkezőpéldáhozhasonlómappa-struktúrabeszerzésérehasználható:`scriptFile`

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Ebben az esetben a `function.json` for `myFunction` kifejezés egy olyan `scriptFile` tulajdonságot tartalmaz, amely a fájlra hivatkozik, és az exportált függvényt futtatja.

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
A függvényeket azonban PowerShell-modulokban is felveheti. A modulban a function. JSON konfigurációs fájljának `scriptFile` és `entryPoint` mezőinek használatával hivatkozhat az adott függvény kódjára.

Ebben az esetben `entryPoint` a a PowerShell-modulban `scriptFile`hivatkozott függvény vagy parancsmag neve.

Vegye figyelembe a következő mappastruktúrát:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Ahol `PSFunction.psm1` a a következőket tartalmazza:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Ebben a példában a konfiguráció `myFunction` egy olyan `scriptFile` tulajdonságot tartalmaz, amely `PSFunction.psm1`hivatkozik rá, amely egy másik mappában található PowerShell-modul.  A `entryPoint` tulajdonság a `Invoke-PSTestFunc` függvényre hivatkozik, amely a modul belépési pontja.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Ezzel a konfigurációval a `Invoke-PSTestFunc` beolvasás pontosan `run.ps1` az lesz.

## <a name="considerations-for-powershell-functions"></a>A PowerShell-függvények szempontjai

A PowerShell-függvények használatakor vegye figyelembe az alábbi részekben ismertetett szempontokat.

### <a name="cold-start"></a>Hidegindító

Azure Functions a [kiszolgáló nélküli üzemeltetési modellben](functions-scale.md#consumption-plan)való fejlesztésekor a hideg indítás a valóság. A *hűtőházi kezdés* azt az időtartamot jelenti, ameddig a Function alkalmazásnak futnia kell a kérelem feldolgozásához. A hidegindító folyamat gyakrabban fordul elő a fogyasztási tervben, mert a Function alkalmazás leáll az inaktivitási időszakok során.

### <a name="bundle-modules-instead-of-using-install-module"></a>Köteg modulok használata helyett`Install-Module`

A szkript minden meghívásnál fut. Kerülje a `Install-Module` használatát a parancsfájlban. Ehelyett használja `Save-Module` a közzététel előtt, hogy a függvénynek ne kelljen időt pazarolnia a modul letöltésével. Ha a ritkán használt funkciók hatással vannak a függvényekre, érdemes lehet a Function alkalmazást egy olyan [app Service-csomagra](functions-scale.md#app-service-plan) telepíteni, amely a [prémium](functions-scale.md#premium-plan)szintű csomagra van beállítva.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)

[a Host. JSON dokumentációja]: functions-host-json.md
