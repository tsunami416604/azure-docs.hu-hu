---
title: PowerShell-fejlesztői referencia az Azure Functions
description: Megismerheti, hogyan hozhat létre függvényeket a PowerShell használatával.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: 71ac525e2af7473ca9ce0a8f60268e76eccd1a9a
ms.sourcegitcommit: 111a7b3e19d5515ce7036287cea00a7204ca8b56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64530383"
---
# <a name="azure-functions-powershell-developer-guide"></a>Az Azure Functions PowerShell fejlesztői útmutatója

Ez a cikk ismerteti, hogyan írhat a PowerShell-lel az Azure Functions részleteit.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Egy PowerShell-függvény jelenik meg egy PowerShell-parancsprogram, amely végrehajtja a elindításakor. Minden függvény szkriptre egy kapcsolódó function.json, amely meghatározza a függvény működését, például hogyan aktiválódik és a bemeneti és kimeneti paramétereket. További tudnivalókért tekintse meg a [eseményindítók és kötési cikk](functions-triggers-bindings.md). 

Egyéb típusú olyan funkciók, például a PowerShell-parancsfájlt a paraméterek, amelyek megfelelnek a bemeneti kötések function.json meghatározott nevei vesz igénybe. A `TriggerMetadata` paramétert is kapott, amely az eseményindító függvény elindított további információkat tartalmaz.

Ez a cikk feltételezi, hogy már elolvasta a [Azure Functions fejlesztői segédanyagai](functions-reference.md). Kell is befejezte a [funkciók rövid útmutató a Powershellhez](functions-create-first-function-powershell.md) az első PowerShell-függvény létrehozásához.

## <a name="folder-structure"></a>gyökérmappa-szerkezetében

A szükséges mappastruktúra PowerShell projekt az alábbihoz hasonló. Ez az alapértelmezett módosítható. További információkért lásd: a [parancsfájl](#configure-function-scriptfile) szakaszt.

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

A projekt gyökerében van egy megosztott [host.json](functions-host-json.md) fájlt, amely a függvényalkalmazás konfigurálása használható. Minden függvény rendelkezik a saját kódfájl (.ps1) és a kötési konfigurációs fájl (function.json) nevű mappa. Nevére `function.json`a könyvtár (szülő) mindig a függvényalkalmazás nevére.

Bizonyos kötések meglétének megkövetelése egy `extensions.csproj`. Kötési bővítményeket, a szükséges [verzió 2.x](functions-versions.md) , a Functions futtatókörnyezete vannak meghatározva a `extensions.csproj` fájlt, a tényleges függvénytárfájlok a `bin` mappát. Ha helyileg fejlesztésével, akkor meg kell [regisztrálja a kötési bővítményeket](functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Amikor fejlesztéséről az Azure Portalon, a regisztrációt, készen áll.

A PowerShell a Függvényalkalmazások, szükség esetén előfordulhat, egy `profile.ps1` amely akkor fut, amikor a függvényalkalmazás futni kezd (más néven ismert egy  *[hidegindítási](#cold-start)*. További információkért lásd: [PowerShell profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Függvényében, egy PowerShell-parancsprogram meghatározása

Alapértelmezés szerint a Functions futtatókörnyezete keres a függvényhez `run.ps1`, ahol `run.ps1` közös szülő könyvtárába annak megfelelő `function.json`.

A parancsfájl végrehajtása a átadott argumentumok számos. Ezek a paraméterek kezelése érdekében adjon hozzá egy `param` tetején a szkript a következő példához hasonlóan a letiltása:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata paraméter

A `TriggerMetadata` paraméter használható az eseményindító további információkat adhat meg. A további metaadatok érték kötés kötést, de az összes bennük egy `sys` tulajdonság, amely tartalmazza a következő adatokat:

```powershell
$TriggerMetadata.sys
```

| Tulajdonság   | Leírás                                     | Típus     |
|------------|-------------------------------------------------|----------|
| utcNow     | Amikor UTC formátumban, a függvény lett elindítva        | DateTime |
| Metódus neve | A függvény, amely aktiválva neve     | string   |
| RandGuid   | Ez a függvény végrehajtása egy egyedi GUID azonosítót | string   |

A metaadatok különböző minden típusú trigger rendelkezik. Ha például a `$TriggerMetadata` a `QueueTrigger` tartalmazza a `InsertionTime`, `Id`, `DequeueCount`, többek között. Az üzenetsor eseményindító metaadatok további információért látogasson el a [eseményindítók hivatalos dokumentációját](functions-bindings-storage-queue.md#trigger---message-metadata). Ellenőrizze a dokumentációban található a [eseményindítók](functions-triggers-bindings.md) megtekintéséhez, mi a lépés a trigger metaadatok belül dolgozik.

## <a name="bindings"></a>Kötések

A PowerShellben [kötések](functions-triggers-bindings.md) konfigurált, és a egy függvény function.json definiálva. Kötések számos módon dolgozhat funkciók.

### <a name="reading-trigger-and-input-data"></a>Olvasási eseményindítót és a bemeneti adatok

Az eseményindító és a bemeneti kötések olvasható a függvénynek átadott paraméterek. A bemeneti kötések rendelkezik egy `direction` beállítása `in` a function.json. A `name` meghatározott tulajdonság `function.json` a paraméter neve szerepel a `param` letiltása. PowerShell-kötés létrehozásához elnevezett paramétereket használja, mivel a paraméterek sorrendje nem számít. Azonban sorrendjét a definiált kötései, kövesse az ajánlott eljárás a `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Kimeneti adatok írása

Funkciók, a kimeneti kötés van egy `direction` beállítása `out` a function.json a. Írható-e egy kimeneti kötés használatával a `Push-OutputBinding` parancsmagot, amely a Functions futtatókörnyezete érhető el. Minden esetben a `name` tulajdonság a kötés, ahogyan az az `function.json` felel meg a `Name` paraméterében a `Push-OutputBinding` parancsmagot.

A következő bemutatja, hogyan hívhat meg `Push-OutputBinding` függvény parancsfájlban:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Is átadhat egy értéket egy adott kötéshez keresztül.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` eltérően a megadott érték alapján működését `-Name`:

* Ha a megadott név nem oldható fel egy érvényes kimeneti kötést, majd egy hiba lépett fel.

* Ha a kimeneti kötés elfogadja a gyűjteményét, meghívhatja `Push-OutputBinding` több érték ismételten elküldeni.

* A kimeneti kötés csak egy egyszeres értéket fogad el, ha hívása `Push-OutputBinding` másodszor hibát jelez.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Szintaxis

Az alábbiakban érvényes paramétereket a hívó `Push-OutputBinding`:

| Name (Név) | Típus | Pozíció | Leírás |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | A kimeneti kötés neve szeretné beállítani. |
| **`-Value`** | Object | 2 | A kimeneti kötés értékét szeretné beállítani, amely láncból a ByValue. |
| **`-Clobber`** | SwitchParameter | nevű | (Nem kötelező) Megadása esetén kényszeríti az értéket a megadott kimeneti kötés állítható be. | 

A következő általános paramétereket is támogatottak: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

További információkért lásd: [kapcsolatos Általánosparaméterek](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Leküldéses-OutputBinding. példa: HTTP-válaszok

HTTP-trigger nevű kimeneti kötés használatával választ ad `response`. A következő példában a kimeneti kötés `response` "kimeneti #1" értékkel rendelkezik:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

A kimenet a HTTP-re, amely csak egy egyszeres értéket fogad el, mert hiba fordul elő amikor `Push-OutputBinding` másodszor nevezzük.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Az egyszeres értékek csak elfogadó kimeneteket, használhatja a `-Clobber` felülbírálhatja a régi értéket adjon hozzá egy gyűjteményhez helyett a paraméter. Az alábbi példa azt feltételezi, hogy már felvett egy értéket. Használatával `-Clobber`, a válasz az alábbi példában felülírja a meglévő értéket ad vissza értéket a "kimeneti #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Leküldéses-OutputBinding. példa: Üzenetsor kimeneti kötése

`Push-OutputBinding` segítségével adatokat küldeni a kimeneti kötések, mint például egy [Azure Queue storage kimeneti kötésének](functions-bindings-storage-queue.md#output). A következő példában az üzenetek várólistára kerülésének értéke "kimeneti #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A tárolási üzenetsor kimeneti kötésének több kimeneti értéket fogad el. Ebben az esetben történt a következő példa a után az üzenetsorba ír az első két elemek listáját: "kimeneti #1" és "kimeneti #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

A következő példa az előző két után meghívásakor ad hozzá a két további értékek a kimeneti gyűjtemény:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Írása az üzenetsorba megtörténik, amikor az üzenet tartalmaz-e az alábbi négy értéket: "kimeneti #1", "#2. kimeneti", "#3 kimeneti" és "kimeneti #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` A parancsmag

Használhatja a `Get-OutputBinding` beolvasásához a kimeneti kötések jelenleg beállított értékeket. Ez a parancsmag kéri le a kimeneti kötések megfelelő értékükön nevét tartalmazza, ez a szórótábla. 

Az alábbiakban egy példát a `Get-OutputBinding` aktuális kötés értékek visszaadásához:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` is tartalmaz egy paraméter `-Name`, amely segítségével szűrheti a visszaadott kötést, a következő példához hasonlóan:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Helyettesítő karaktereket (*) használata támogatott a `Get-OutputBinding`.

## <a name="logging"></a>Naplózás

Bejelentkezés a PowerShell-funkciók rendszeres PowerShell naplózási hasonlóan működik. A naplózási parancsmagok segítségével minden egyes kimeneti adatfolyamba való írása. Minden parancsmagot a Functions által használt naplózási szintet rendeli hozzá.

| Naplózási szint funkciók | Naplózási parancsmaggal |
| ------------- | -------------- |
| Hiba | **`Write-Error`** |
| Figyelmeztetés | **`Write-Warning`**  | 
| Információ | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Információ | Ír _információk_ webhelyszintű naplózás. |
| Hibakeresés | **`Write-Debug`** |
| Nyomkövetés | **`Write-Progress`** <br /> **`Write-Verbose`** |

Ezek a parancsmagok mellett a folyamat számára írt semmit a rendszer átirányítja a `Information` naplózási szint és az alapértelmezett PowerShell formázással jelennek meg.

> [!IMPORTANT]
> Használatával a `Write-Verbose` vagy `Write-Debug` parancsmagok már nem elég részletes és hibakeresési naplózás szint. A napló szintű küszöbérték, amely ténylegesen konkrét naplók milyen szintű deklarálja is konfigurálnia kell. További tudnivalókért lásd: [a függvény alkalmazás naplózási szint konfigurálása](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>A függvény alkalmazás naplózási szint konfigurálása

Functions lehetővé teszi a küszöbértéket, szabályozhatja a módon függvények ír a naplókba megkönnyíti meghatározása. Összes nyomkövetési konzolon küszöbértéke beállításához használja a `logging.logLevel.default` tulajdonságot a [ `host.json` fájl][host.json referencia]. Ez a beállítás minden függvény a függvényalkalmazásban vonatkozik.

Az alábbi példa a függvények részletes naplózás engedélyezése a küszöbértéket, de a küszöbértéket nevű függvény hibakeresési naplózást engedélyező `MyFunction`:

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

További információkért lásd: [host.json referencia].

### <a name="viewing-the-logs"></a>A naplók megtekintése

Ha a Függvényalkalmazás Azure-ban fut, az Application Insights használatával figyelheti. Olvasási [Azure Functions figyelése](functions-monitoring.md) tudhat meg többet a megtekintése, és a függvény naplóinak lekérdezéséhez.

Ha futtatja a Függvényalkalmazás helyileg a fejlesztéshez, naplózza a alapértelmezett a fájlrendszerhez. Tekintse meg a naplókat, a konzolon, állítsa be a `AZURE_FUNCTIONS_ENVIRONMENT` környezeti változót, `Development` a Függvényalkalmazás megkezdése előtt.

## <a name="triggers-and-bindings-types"></a>Eseményindítók és kötések típusok

Nincsenek eseményindítók és kötések számos, a függvényalkalmazás használható. Teljes listája megtalálható az eseményindítók és kötések [itt található](functions-triggers-bindings.md#supported-bindings).

Az összes eseményindítók és kötések-kiszolgálókként vannak a kódban néhány valódi adattípusok:

* Kivonattábla
* string
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Ebben a listában az első öt típus olyan szabványos .NET. Az utolsó két kizárólag a használt a [HttpTrigger eseményindító](#http-triggers-and-bindings).

Az a funkciók egyes kötési paraméter a következő típusok egyikének megfelelő kell lennie.

### <a name="http-triggers-and-bindings"></a>HTTP-eseményindítók és kötések

HTTP és a webhook eseményindítók és a HTTP-kimeneti kötések használata kérések és válaszok objektumok, amelyek a HTTP-üzenetkezelés.

#### <a name="request-object"></a>Támogatásikérelem-objektum

A parancsprogramhoz átadott támogatásikérelem-objektum típusa nem `HttpRequestContext`, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Leírás                                                    | Típus                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Egy objektum, amely tartalmazza a kérelem törzsében. `Body` az adatok alapján a legjobb típus szerializált. Például ha az adatok JSON, azt át egy kivonattáblát. Ha az adatok egy karakterlánc, azt átadott karakterlánc formájában. | objektum |
| **`Headers`** | Egy szótár, amely tartalmazza a kérelem fejlécében.                | Dictionary < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Method`** | A kérelem HTTP-metódus.                                | string                    |
| **`Params`**  | Egy objektum, amely tartalmazza a kérés útválasztási paramétereit. | Dictionary < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Query`** | Egy objektum, amely tartalmazza a lekérdezési paramétereket.                  | Dictionary < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Url`** | A kérelem URL-címe                                        | string                    |

<sup>*</sup> Az összes `Dictionary<string,string>` kulcsok és nagybetűk nincsenek megkülönböztetve.

#### <a name="response-object"></a>Válaszobjektum

Vissza kell küldeni a választ objektum típusa nem `HttpResponseContext`, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás                                                 | Típus                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Egy objektum, amely a válasz törzse tartalmazza.           | objektum                    |
| **`ContentType`** | Egy rövid aktuális állítja a tartalomtípus a válaszhoz. | string                    |
| **`Headers`** | A válaszfejlécek tartalmazó objektum.               | Dictionary, vagy a kivonattábla kulcsa   |
| **`StatusCode`**  | A válasz HTTP-állapotkódot.                       | karakterlánc- vagy int             |

#### <a name="accessing-the-request-and-response"></a>A kérés- és hozzáférés

Ha HTTP-eseményindítók használata során érheti el a HTTP-kérelem a ugyanúgy, mint bármely más bemeneti kötésnek a. Van a `param` letiltása.

Használja az `HttpResponseContext` objektumot adott vissza a választ, az alábbiakban látható módon:

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

Ez a függvény meghívása eredménye a következő lesz:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Az eseményindítók és kötések típusú előrejelzési

Az egyes kötések, például a blob-kötést, Ön meghatározhatja a paraméter típusát.

Például ahhoz, hogy a megadott karakterlánc formájában blobtárolóból származó adatokat, konvertálni a következő típusú hozzá saját `param` letiltása:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profilt

PowerShell-lel van egy PowerShell-profilt a fogalmát. Ha még nem ismeri a PowerShell-profilok, tekintse meg [profilokkal kapcsolatos](/powershell/module/microsoft.powershell.core/about/about_profiles).

PowerShell-funkciók a profil parancsfájl végrehajt a függvényalkalmazás indításakor. Függvényalkalmazások indítsa el első telepítésekor, és folyamatban idled után ([hidegindítási](#cold-start)).

Amikor hoz létre egy függvényalkalmazást, eszközökkel, például a Visual Studio Code és az Azure Functions Core Tools, egy alapértelmezett `profile.ps1` jön létre az Ön számára. Az alapértelmezett profil fenn lesz tartva [Core Tools GitHub-adattárban](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) , és tartalmazza:

* Automatikus MSI-hitelesítés az Azure-bA
* Kapcsolja be az Azure PowerShell lehetővé teszi `AzureRM` PowerShell aliasok, ha szeretné.

## <a name="powershell-version"></a>PowerShell-verzió

Az alábbi táblázat az egyes főverziója a Functions futtatókörnyezete által használt PowerShell-verzió:

| Functions-verzió | PowerShell-verzió                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1-es (futásidejű zárolta) |
| 2.x               | A PowerShell Core 6                              |

A jelenlegi verzió megtekintéséhez nyomtatási `$PSVersionTable` a függvényeket.

## <a name="dependency-management"></a>Függőségkezelés

PowerShell-funkciók támogatása kezelése az Azure-modulokat a szolgáltatás által. A host.json módosítását, és az engedélyezett managedDependency tulajdonság beállítása igaz értékre, a requirements.psd1 fájl lesz feldolgozva. A legújabb Azure-modulok automatikusan a rendszer letölti és elérhetővé téve a függvényt.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

Saját egyéni kihasználva modulokat és a modulok a [PowerShell-galériából](https://powershellgallery.com) kissé eltér attól, hogy miként teheti azt általában.

A modul telepítésekor a helyi gépen lévő globálisan elérhető mappák egyikében kerül a `$env:PSModulePath`. Mivel a függvény futásakor az Azure-ban, nem kell a modulok telepítve van a gépén a hozzáférést. Ez megköveteli, hogy a `$env:PSModulePath` PowerShell függvény alkalmazás eltér `$env:PSModulePath` rendszeres PowerShell parancsfájl.

A függvények `PSModulePath` két elérési útját tartalmazza:

* A `Modules` mappát, amely létezik a Függvényalkalmazás gyökérmappájában.
* Egy elérési útját egy `Modules` lakik belsejében a PowerShell nyelv feldolgozó mappát.

### <a name="function-app-level-modules-folder"></a>A funkció az alkalmazásszintű `Modules` mappa

Egyéni modulok vagy a PowerShell-modulok használata a PowerShell-galériából, helyezze el a függvények függenek, amelyre a modulok egy `Modules` mappát. Ebből a mappából modulok automatikusan elérhetők, a functions-futtatókörnyezetben. Minden függvény a függvényalkalmazásban használhatja ezeket a modulokat.

Ez a funkció kihasználásához, hozzon létre egy `Modules` mappába gyökere a függvényalkalmazást. Mentse az ezen a helyen a függvények használni kívánt modulokat.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Használjon `Save-Module` menti az összes a modulok, az funkciók használata, vagy másolja a saját egyéni modulok a `Modules` mappát. Modulok mappát a függvényalkalmazás a következő mappastruktúra kell rendelkeznie:

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

Amikor a függvényalkalmazás, a PowerShell nyelv feldolgozó hozzáadja ezt `Modules` mappát a `$env:PSModulePath` úgy, hogy ugyanúgy, mint egy normál PowerShell-parancsfájlt a modul autoloading számíthat.

### <a name="language-worker-level-modules-folder"></a>Nyelv feldolgozó szintje `Modules` mappa

Több modulokat a PowerShell nyelv feldolgozó gyakran használja. Ezek a modulok meghatározott utolsó pozíciója `PSModulePath`. 

A modulok aktuális listáját a következőképpen történik:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modul használatához archiválás helyett, például használja `.zip`, `.nupkg`, és másokkal.
* **ThreadJob**: A PowerShell API-k feladat hozzászóláslánc-alapú megvalósítását.

Ezek a modulok a legújabb verziójában funkciókat használják. Ezeket a modulokat egy adott verzióját használja, akkor lehet helyezni a verziót a `Modules` mappában található a függvényalkalmazást.

## <a name="environment-variables"></a>Környezeti változók

A függvények [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatás kapcsolati karakterláncok, ki vannak téve környezeti változókként végrehajtása során. Ezek a beállítások használatával is elérheti `$env:NAME_OF_ENV_VAR`, az alábbi példában látható módon:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Helyi futtatáskor az alkalmazásbeállítások olvassa a rendszer a [local.settings.json](functions-run-local.md#local-settings-file) soubor projektu.

## <a name="concurrency"></a>Egyidejűség

Alapértelmezés szerint az a funkciók PowerShell modul csak képes feldolgozni egyszerre csak egy függvényt egy lett meghívva. A párhuzamosság szintje azonban nem feltétlenül elégségesek az alábbi esetekben:

* Ha szeretne egyszerre nagy számú indítások kezelni.
* Ha rendelkezik az funkciók, amelyek aktiválják az egyéb funkciók ugyanazon függvényalkalmazáson belül.

Ez a viselkedés úgy, hogy egy egész számot a következő környezeti változó módosíthatja:

```
PSWorkerInProcConcurrencyUpperBound
```

Állítsa be ezt a környezeti változót a [Alkalmazásbeállítások](functions-app-settings.md) a függvényalkalmazás.

### <a name="considerations-for-using-concurrency"></a>Egyidejűségi használatának szempontjai

PowerShell- _egyszeres_ programozási nyelv alapértelmezés szerint. Egyidejűségi azonban több PowerShell futási terek használatával ugyanabban a folyamatban is hozzáadhatók. Ez a szolgáltatás el az Azure Functions PowerShell modul működését.

Vannak bizonyos hátrányai, ezt a módszert használja.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>Csak az olyan jól, mint a futtató gép egyidejűség

Ha a függvényalkalmazás fut egy [App Service-csomag](functions-scale.md#app-service-plan) , amely csak egyetlen mag támogatja, akkor egyidejűség nem sokat segít nagy részét. Ennek oka az, a terhelés elosztása érdekében nem processzormaggal vannak. Ebben az esetben a teljesítmény Ha egyetlen alapvető fontosságú környezet-kapcsolóhoz futási terek között változhat.

A [Használatalapú csomag](functions-scale.md#consumption-plan) csak egyetlen mag használatával, így nem tudják hasznosítani a párhuzamosság futtatja. Ha azt szeretné, a párhuzamosság előnyeit, inkább functions üzembe helyezése a egy dedikált App Service-csomag elegendő maggal futó függvényalkalmazáshoz.

#### <a name="azure-powershell-state"></a>Azure PowerShell-állapota

Az Azure PowerShell néhány használ _folyamatszintű_ környezeteket és állapot felesleges írjanak, megtakarítása érdekében. Azonban ha kapcsolja be az egyidejűség a függvényalkalmazást, és hajthatók végre műveletek, amelyek állapotát módosítani, sikerült végül versenyhelyzetek. Ezek olyan versenyhelyzetekben nehezen hibakeresése, mert egy hívás egy adott állapotra hagyatkozik, és a más hívási módosította az állapotot.

Nincs környezetéről érték egyidejűségi az Azure PowerShell-lel, mivel az egyes műveletek a jelentős mennyiségű időt is igénybe vehet. Azonban meg kell legyen óvatos. Ha azt gyanítja, hogy a versenyhelyzet tapasztal, a párhuzamosság állítsa vissza a `1` , és próbálkozzon újra a kérelmet.

## <a name="configure-function-scriptfile"></a>Függvény konfigurálása `scriptFile`

Alapértelmezés szerint egy PowerShell-függvény végrehajtása a `run.ps1`, egy közös szülő könyvtárába annak megfelelő fájl `function.json`.

A `scriptFile` tulajdonságot a `function.json` is használható, amely a következő példához hasonlóan néz ki a mappastruktúrát beolvasásához:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Ebben az esetben a `function.json` a `myFunction` tartalmaz egy `scriptFile` tulajdonságot a fájl futtatása az exportált funkcióval hivatkozik.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Egy belépési pont konfigurálásával PowerShell-modulok használata

Ez a cikk segített a PowerShell-funkciók az alapértelmezett `run.ps1` a sablonok által létrehozott parancsfájlt.
A PowerShell-modulok azonban a függvények is megadhatja. Az adott függvény kódját a modul használatával is lehet hivatkozni a `scriptFile` és `entryPoint` mezőket a function.json "konfigurációs fájl.

Ebben az esetben `entryPoint` egy függvény vagy a hivatkozott PowerShell-modul a parancsmag neve `scriptFile`.

Vegye figyelembe a következő mappastruktúra:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Ahol `PSFunction.psm1` tartalmazza:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Ebben a példában a konfigurációját a `myFunction` tartalmaz egy `scriptFile` tulajdonság által hivatkozott `PSFunction.psm1`, amely egy PowerShell-modul egy másik mappában.  A `entryPoint` tulajdonság hivatkozásokat a `Invoke-PSTestFunc` függvény, amely a modul belépési pontja.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Ezzel a konfigurációval a `Invoke-PSTestFunc` pontosan, hajtsa végre egy `run.ps1` lenne.

## <a name="considerations-for-powershell-functions"></a>Szempontok a PowerShell-funkciók

PowerShell-funkciók használata, a következő szakaszokban ismertetett szempontok figyelembe.

### <a name="cold-start"></a>Hidegindítási

Az Azure Functions fejlesztése során a [kiszolgáló nélküli üzemeltetési modell](functions-scale.md#consumption-plan)ritkán használt, elindul a valóság. *Hidegindítási* hivatkozik időszakban, a függvényalkalmazás a feldolgozott kérések kezdési idő szükséges. Hidegindítási történik az információk gyakran, a Használatalapú: mivel a függvényalkalmazás lekérdezi állítsa le az inaktív időszakokban megtervezése.

### <a name="bundle-modules-instead-of-using-install-module"></a>Csomag modulok használata helyett `Install-Module`

A rendszer a parancsfájlt minden hívás. Kerülje a `Install-Module` a parancsfájlban. Helyette használjon `Save-Module` közzététele, hogy a függvény nem rendelkezik pazarlom az időt a modul letöltése előtt. Ha kiküszöbölik negatív hatással vannak a függvények, érdemes megfontolni a függvényalkalmazást, hogy egy [App Service-csomag](functions-scale.md#app-service-plan) beállítása *folyamatos* vagy egy [prémium szintű csomag](functions-scale.md#premium-plan-public-preview).

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)

[host.json referencia]: functions-host-json.md
