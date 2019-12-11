---
title: PowerShell fejlesztői referenciája Azure Functions
description: Ismerje meg, hogyan fejlesztheti a függvényeket a PowerShell használatával.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 2fa510e447d4d9b054a37f7665d010382a5db819
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974240"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell fejlesztői útmutató

Ez a cikk részletesen ismerteti, hogyan írhat Azure Functions a PowerShell használatával.

A PowerShell Azure-függvény (Function) egy PowerShell-parancsfájlként jelenik meg, amely az aktiváláskor fut. Mindegyik függvény parancsfájlhoz tartozik egy kapcsolódó `function.json` fájl, amely meghatározza, hogy a függvény hogyan viselkedik, például hogyan aktiválódik, valamint a bemeneti és kimeneti paramétereket. További információt az [Eseményindítók és a kötések című cikkben](functions-triggers-bindings.md)talál. 

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

A projekt gyökerében található egy megosztott [`host.json`](functions-host-json.md) fájl, amely a Function alkalmazás konfigurálására használható. Minden függvényhez tartozik egy mappa (. ps1) és egy kötési konfigurációs fájl (`function.json`). A function. JSON fájljának szülő könyvtárának neve mindig a függvény neve.

Bizonyos kötések `extensions.csproj` fájl jelenlétét igénylik. A függvények futtatókörnyezetének [2. x vagy újabb](functions-versions.md) verziójában szükséges kötési kiterjesztések a `extensions.csproj` fájlban vannak definiálva, a `bin` mappában található tényleges függvénytár-fájlokkal. Helyi fejlesztés esetén [regisztrálnia kell a kötési bővítményeket](functions-bindings-register.md#extension-bundles). A Azure Portal funkcióinak fejlesztésekor ez a regisztráció történik.

A PowerShell-függvény alkalmazásaiban szükség lehet egy `profile.ps1`ra, amely akkor fut le, amikor egy Function alkalmazás elindul (más néven a *[hidegindító kezdete](#cold-start)* ). További információ: PowerShell- [profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell-parancsfájl definiálása függvényként

Alapértelmezés szerint a functions Runtime a `run.ps1`-ben keresi a függvényt, ahol a `run.ps1` ugyanazt a szülő könyvtárat osztja meg, mint a hozzá tartozó `function.json`.

A szkript több argumentumot adott meg a végrehajtás során. A paraméterek kezeléséhez vegyen fel egy `param` blokkot a parancsfájl elejére, ahogy az alábbi példában látható:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata paraméter

A `TriggerMetadata` paraméterrel további információkat adhat meg az triggerről. A további metaadatok a kötéstől a kötéstől függenek, de mindegyikük tartalmaz egy `sys` tulajdonságot, amely a következő adatokat tartalmazza:

```powershell
$TriggerMetadata.sys
```

| Tulajdonság   | Leírás                                     | Type (Típus)     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Ha UTC-ben a függvény aktiválva lett        | Dátum és idő |
| MethodName | Az aktivált függvény neve     | sztring   |
| RandGuid   | a függvény végrehajtásának egyedi GUID azonosítója | sztring   |

Minden trigger típushoz eltérő metaadatok vannak megadva. A `QueueTrigger` `$TriggerMetadata` például tartalmazza a `InsertionTime`, `Id`, `DequeueCount`, többek között. Az üzenetsor-eseményindító metaadataival kapcsolatos további információkért nyissa meg a [várólista-eseményindítók hivatalos dokumentációját](functions-bindings-storage-queue.md#trigger---message-metadata). Tekintse meg a dokumentációt azon [Eseményindítók](functions-triggers-bindings.md) használatával kapcsolatban, amelyekkel megtudhatja, mi történik az eseményindító metaadatainak között.

## <a name="bindings"></a>Kötések

A PowerShellben a [kötések](functions-triggers-bindings.md) konfigurálva és definiálva vannak egy függvény function. JSON fájljában. A függvények számos módon működnek együtt a kötésekkel.

### <a name="reading-trigger-and-input-data"></a>Trigger-és bemeneti adatok olvasása

Az trigger és a bemeneti kötések a függvénynek átadott paraméterekként lesznek beolvasva. A bemeneti kötések `direction` úgy vannak beállítva, hogy a function. JSON fájlban `in`. A `function.json`ban definiált `name` tulajdonság a `param` blokkban található paraméter neve. Mivel a PowerShell nevesített paramétereket használ a kötéshez, a paraméterek sorrendje nem számít. Az ajánlott eljárás azonban a `function.json`ban meghatározott kötések sorrendjét követi.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Kimeneti adatokat írunk

A functions szolgáltatásban egy kimeneti kötés `direction` van beállítva a function. JSON fájlban `out`. A `Push-OutputBinding` parancsmaggal írhat egy kimeneti kötést, amely a functions Runtime számára érhető el. Minden esetben a kötés `name` tulajdonsága `function.json` a `Push-OutputBinding` parancsmag `Name` paraméterének felel meg.

A következő bemutatja, hogyan hívhat meg `Push-OutputBinding` a függvény parancsfájljában:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

A folyamaton keresztül egy adott kötés értékét is átadhatja.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

a `Push-OutputBinding` a `-Name`megadott érték alapján eltérően viselkedik:

* Ha a megadott név nem oldható fel érvényes kimeneti kötésre, akkor hiba történik.

* Ha a kimeneti kötés fogadja az értékek egy gyűjteményét, többször is meghívhatja `Push-OutputBinding` több érték leküldéséhez.

* Ha a kimeneti kötés csak egy egyszeres értéket fogad el, `Push-OutputBinding` egy második alkalommal hibát jelez.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` szintaxis

A következő `Push-OutputBinding`meghívásához használható paraméterek érvényesek:

| Név | Type (Típus) | Pozíció | Leírás |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Sztring | 1 | A beállítani kívánt kimeneti kötés neve. |
| **`-Value`** | Objektum | 2 | A beállítani kívánt kimeneti kötés értéke, amelyet a rendszer a folyamat ByValue fogad el. |
| **`-Clobber`** | Kapcsolóparaméter | Elemzi | Választható Ha meg van adva, a megadott kimeneti kötéshez beállított értéket kényszeríti ki. | 

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

Egy HTTP-trigger egy `response`nevű kimeneti kötést használó választ ad vissza. A következő példában a `response` kimeneti kötésének értéke "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Mivel a kimenet a HTTP, amely csak egy egyedi értéket fogad el, akkor a rendszer hibát jelez, ha a `Push-OutputBinding`t másodszor is nevezik.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Az olyan kimenetek esetében, amelyek csak egyszeres értékeket fogadnak el, a `-Clobber` paraméterrel felülbírálhatja a régi értéket, és nem kell hozzáadnia a gyűjteményhez. Az alábbi példa azt feltételezi, hogy már hozzáadott egy értéket. `-Clobber`használatával a következő példa válasza felülbírálja a meglévő értéket, hogy a "kimeneti #3" értéket adja vissza:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Leküldéses OutputBinding – példa: várólista kimeneti kötése

`Push-OutputBinding` az adatokat kimeneti kötésekre küldi, például egy [Azure üzenetsor-tároló kimeneti kötését](functions-bindings-storage-queue.md#output). A következő példában a várólistára írt üzenet "output #1" értékkel rendelkezik:

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

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` parancsmag

A `Get-OutputBinding` parancsmaggal kérheti le a kimeneti kötésekhez jelenleg beállított értékeket. Ez a parancsmag egy olyan szórótábla kérdez le, amely tartalmazza a kimeneti kötések nevét a megfelelő értékekkel. 

Az alábbi példa az aktuális kötési értékek visszaküldésére szolgáló `Get-OutputBinding` használatát szemlélteti:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` egy `-Name`nevű paramétert is tartalmaz, amely a visszaadott kötés szűrésére használható, ahogy az alábbi példában is látható:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

A helyettesítő karakterek (*) a `Get-OutputBinding`ben támogatottak.

## <a name="logging"></a>Naplózás

A PowerShell-függvények naplózása ugyanúgy működik, mint a rendszeres PowerShell-naplózás. A naplózási parancsmagok segítségével írhat az egyes kimeneti adatfolyamokhoz. Minden parancsmag a függvények által használt naplózási szintre mutat.

| Függvények naplózási szintje | Naplózási parancsmag |
| ------------- | -------------- |
| Hiba | **`Write-Error`** |
| Figyelmeztetés | **`Write-Warning`**  | 
| Információ | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Információ | _Az_ adatszint naplózásának megírása. |
| Hibakeresés | **`Write-Debug`** |
| Nyomkövetés | **`Write-Progress`** <br /> **`Write-Verbose`** |

Ezen parancsmagok mellett a folyamatba írt összes adat át lesz irányítva a `Information` naplózási szintjére, és az alapértelmezett PowerShell-formázással jelenik meg.

> [!IMPORTANT]
> A `Write-Verbose`-vagy `Write-Debug`-parancsmagok használata nem elegendő a részletes és hibakeresési szintű naplózás megjelenítéséhez. A naplózási szint küszöbértékét is be kell állítania, amely kijelenti, hogy milyen szintű naplókra van szüksége. További információ: [az alkalmazás naplózási szintjének konfigurálása](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>A Function app naplózási szintjének konfigurálása

Azure Functions segítségével meghatározhatja a küszöbértéket, hogy könnyen szabályozható legyen a függvények írási módja a naplókba. A konzolra írt összes nyomkövetés küszöbértékének megadásához használja a [`host.json` file][a host. json dokumentációja]`logging.logLevel.default` tulajdonságát. Ez a beállítás a Function alkalmazás összes függvényére érvényes.

A következő példa beállítja a küszöbértéket az összes függvény részletes naplózásának engedélyezéséhez, de beállítja a küszöbértéket, hogy engedélyezze a hibakeresési naplózást egy `MyFunction`nevű függvénynél:

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

Ha a függvényalkalmazást helyileg futtatja fejlesztésre, a a naplófájlok alapértelmezése a fájlrendszer. A naplóknak a konzolon való megtekintéséhez állítsa a `AZURE_FUNCTIONS_ENVIRONMENT` környezeti változót úgy, hogy `Development` a függvényalkalmazás elindítása előtt.

## <a name="triggers-and-bindings-types"></a>Eseményindítók és kötések típusai

Számos eseményindító és kötés érhető el a Function alkalmazással való használathoz. Az eseményindítók és kötések teljes listája [itt található](functions-triggers-bindings.md#supported-bindings).

Az összes eseményindító és kötés a kódban szerepel néhány valós adattípus formájában:

* Szórótábla
* sztring
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

A parancsfájlba átadott kérelem-objektum `HttpRequestContext`típusú, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Leírás                                                    | Type (Típus)                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Egy objektum, amely tartalmazza a kérelem törzsét. a `Body` a legjobb típusra van szerializálva az adathalmaz alapján. Ha például az adatfájl JSON, a rendszer szórótábla adja át. Ha az érték karakterlánc, akkor karakterláncként adja át a rendszer. | objektum |
| **`Headers`** | A kérések fejléceit tartalmazó szótár.                | Szótár < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Method`** | A kérelem HTTP-metódusa.                                | sztring                    |
| **`Params`**  | Egy objektum, amely a kérelem útválasztási paramétereit tartalmazza. | Szótár < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Query`** | Egy objektum, amely tartalmazza a lekérdezési paramétereket.                  | Szótár < karakterlánc, karakterlánc ><sup>*</sup> |
| **`Url`** | A kérelem URL-címe.                                        | sztring                    |

<sup>*</sup> Az összes `Dictionary<string,string>` kulcs kis-és nagybetűk megkülönböztetése.

#### <a name="response-object"></a>Válaszobjektum

A visszaküldeni kívánt válasz-objektum típusa `HttpResponseContext`, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás                                                 | Type (Típus)                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Egy objektum, amely tartalmazza a válasz törzsét.           | objektum                    |
| **`ContentType`** | Egy rövid kéz a válasz tartalomtípusának beállításához. | sztring                    |
| **`Headers`** | Egy objektum, amely tartalmazza a válasz fejléceit.               | Szótár vagy szórótábla   |
| **`StatusCode`**  | A válasz HTTP-állapotkódot.                       | karakterlánc vagy int             |

#### <a name="accessing-the-request-and-response"></a>A kérelem és válasz elérése

Ha HTTP-eseményindítókkal dolgozik, ugyanúgy érheti el a HTTP-kérést, mint bármely más bemeneti kötést. Ez a `param` blokkban található.

Egy `HttpResponseContext` objektummal választ adhat vissza, ahogy az az alábbi ábrán látható:

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

Ha olyan eszközökkel hoz létre Function alkalmazást, mint például a Visual Studio Code és a Azure Functions Core Tools, akkor a rendszer létrehoz egy alapértelmezett `profile.ps1`. Az alapértelmezett profil a [GitHub-tárház alapvető eszközein](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) marad, és a következőket tartalmazza:

* Automatikus MSI-hitelesítés az Azure-ba.
* A Azure PowerShell `AzureRM` PowerShell-aliasok bekapcsolásának lehetősége, ha szeretné.

## <a name="powershell-version"></a>PowerShell-verzió

A következő táblázat a függvények futtatókörnyezetének egyes főbb verziói által használt PowerShell-verziót mutatja be:

| Függvények verziója | PowerShell-verzió                             |
|-------------------|------------------------------------------------|
| 1. x               | Windows PowerShell 5,1 (a futtatókörnyezet zárolta) |
| 2. x               | 6\. PowerShell-mag                              |

Az aktuális verziót úgy tekintheti meg, hogy kinyomtatja `$PSVersionTable` bármely függvényből.

## <a name="dependency-management"></a>Függőségkezelés

A functions lehetővé teszi a [PowerShell-Galéria](https://www.powershellgallery.com) kihasználása a függőségek kezeléséhez. Ha a függőségek kezelése engedélyezve van, a követelmények. psd1 fájl a szükséges modulok automatikus letöltésére szolgál. Ezt a viselkedést úgy engedélyezheti, ha a `managedDependency` tulajdonságot úgy állítja be, hogy az a [Host. JSON fájl](functions-host-json.md)gyökerében `true` a következő példában látható módon:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Új PowerShell functions-projekt létrehozásakor a függőségek kezelése alapértelmezés szerint engedélyezve van az Azure [`Az` modullal](/powershell/azure/new-azureps-module-az) . A jelenleg támogatott modulok maximális száma 10. A támogatott szintaxis _`MajorNumber`_ `.*` vagy a modul pontos verziója, ahogy az az alábbi követelményekben látható. psd1 példa:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

A követelmények. psd1 fájl frissítésekor a frissített modulok újraindítást követően települnek.

> [!NOTE]
> A felügyelt függőségekhez hozzáférés szükséges a www.powershellgallery.com a modulok letöltéséhez. Helyileg futtatva győződjön meg arról, hogy a futtatókörnyezet hozzáférhet ehhez az URL-címhez a szükséges tűzfalszabályok hozzáadásával. 

A következő Alkalmazásbeállítások segítségével megváltoztathatja a felügyelt függőségek letöltésének és telepítésének módját. Az alkalmazás frissítése `MDMaxBackgroundUpgradePeriod`on belül kezdődik, és a frissítési folyamat körülbelül a `MDNewSnapshotCheckPeriod`on belül fejeződik be.

| függvényalkalmazás beállítás              | Alapértelmezett érték             | Leírás                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 nap)     | Minden PowerShell-munkavégző folyamat kezdeményezi a modul frissítéseinek ellenőrzését a PowerShell-galéria a folyamat indításakor és minden `MDMaxBackgroundUpgradePeriod` után. Ha egy új modul verziója elérhető a PowerShell-galériaban, a rendszer telepíti a fájlrendszerre, és elérhetővé teszi őket a PowerShell-feldolgozók számára. Ennek az értéknek a csökkentése lehetővé teszi, hogy a Function alkalmazás hamarabb lekérje a modul újabb verzióit, de az alkalmazás erőforrás-használatát is növeli (hálózati I/O-, CPU-és tárolási). Az érték növelése csökkenti az alkalmazás erőforrás-használatát, de az új modulok verzióinak az alkalmazásba való kézbesítése is késleltethető. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 óra)       | Miután telepítette az új modul-verziókat a fájlrendszerbe, minden PowerShell-munkavégző folyamatot újra kell indítani. A PowerShell-feldolgozók újraindítása hatással van az alkalmazás rendelkezésre állására, mivel ez megszakíthatja az aktuális függvény végrehajtását. Amíg az összes PowerShell-munkavégző folyamat újra nem indul, a függvény meghívása a régi vagy az új modul verzióját is használhatja. A PowerShell-feldolgozók újraindítása `MDNewSnapshotCheckPeriod`on belül. Az érték növelésével csökken a megszakítások gyakorisága, de az is előfordulhat, hogy a függvény meghívásakor a régi vagy az új modul nem determinisztikus módon verzióját használja. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 nap)     | A gyakori munkavégző újraindítások esetén a modul frissítéseinek figyelése nem történik meg, ha bármelyik feldolgozó már kezdeményezte az utolsó `MDMinBackgroundUpgradePeriod`ellenőrzését. |

A saját egyéni moduljainak kihasználása kicsit eltér a megszokott módon.

A helyi számítógépen a modul a `$env:PSModulePath`egyik globálisan elérhető mappájában lesz telepítve. Ha az Azure-ban fut, nincs hozzáférése a gépen telepített modulokhoz. Ez azt jelenti, hogy egy PowerShell-függvény alkalmazásának `$env:PSModulePath`e eltér a szokásos PowerShell-parancsfájlok `$env:PSModulePath`tól.

A függvények `PSModulePath` két elérési utat tartalmaz:

* `Modules` mappa, amely a Function alkalmazás gyökerében található.
* A PowerShell nyelvi feldolgozója által vezérelt `Modules` mappa elérési útja.

### <a name="function-app-level-modules-folder"></a>A függvény alkalmazás szintű `Modules` mappája

Egyéni modulok használatához olyan modulokat helyezhet el, amelyeken a függvények egy `Modules` mappától függenek. Ebből a mappából a modulok automatikusan elérhetők a functions Runtime számára. A Function alkalmazás bármely funkciója használhatja ezeket a modulokat. 

> [!NOTE]
> A követelmények. psd1 fájlban megadott modulokat a rendszer automatikusan letölti és belefoglalja az elérési útra, így nem kell felvennie őket a modulok mappába. Ezeket a rendszer helyileg tárolja a `$env:LOCALAPPDATA/AzureFunctions` mappában és a `/data/ManagedDependencies` mappában a felhőben való futtatáskor.

Az egyéni modul funkció kihasználása érdekében hozzon létre egy `Modules` mappát a Function alkalmazás gyökerében. Másolja a függvényekben használni kívánt modulokat erre a helyre.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

`Modules` mappában a Function alkalmazásnak a következő mappastruktúrát kell megjelennie:

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

Ha elindítja a Function alkalmazást, a PowerShell nyelvi feldolgozó hozzáadja ezt a `Modules` mappát a `$env:PSModulePath`hoz, így a modul automatikus betöltését ugyanúgy használhatja, mint egy hagyományos PowerShell-parancsfájlban.

### <a name="language-worker-level-modules-folder"></a>Nyelvi feldolgozói szint `Modules` mappája

A PowerShell nyelvi feldolgozója általában számos modult használ. Ezek a modulok `PSModulePath`utolsó helyén vannak meghatározva. 

A modulok aktuális listája a következő:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): az archívumok használatához használt modul, például `.zip`, `.nupkg`és egyebek.
* **ThreadJob**: a PowerShell-feladatok API-k szálon alapuló implementációja.

Alapértelmezés szerint a függvények a modulok legújabb verzióját használják. Egy adott modul verziójának használatához helyezze az adott verziót a Function alkalmazás `Modules` mappájába.

## <a name="environment-variables"></a>Környezeti változók

A függvények, az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai a végrehajtás során környezeti változókként jelennek meg. Ezeket a beállításokat `$env:NAME_OF_ENV_VAR`használatával érheti el, ahogy az az alábbi példában is látható:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Helyileg futtatva az Alkalmazásbeállítások a [Local. Settings. JSON](functions-run-local.md#local-settings-file) Project fájlból lesznek beolvasva.

## <a name="concurrency"></a>Párhuzamosság

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

Az Azure PowerShell-ben óriási érték van, mivel néhány művelet jelentős időt vehet igénybe. Azonban körültekintően kell eljárnia. Ha azt gyanítja, hogy a verseny feltételét tapasztalja, állítsa a PSWorkerInProcConcurrencyUpperBound `1`re, és Ehelyett használja a [nyelvi feldolgozói folyamat szintjének elkülönítését](functions-app-settings.md#functions_worker_process_count) a párhuzamossághoz.

## <a name="configure-function-scriptfile"></a>A függvény konfigurálása `scriptFile`

Alapértelmezés szerint a rendszer egy PowerShell-függvényt hajt végre `run.ps1`ból, egy olyan fájlt, amely ugyanazt a szülő könyvtárat osztja meg a megfelelő `function.json`.

A `function.json` `scriptFile` tulajdonsága a következő példához hasonló mappa-struktúra beszerzésére használható:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Ebben az esetben a `myFunction` `function.json` tartalmaz egy `scriptFile` tulajdonságot, amely a fájlra hivatkozik, és futtatja az exportált függvényt.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>PowerShell-modulok használata BelépésiPont konfigurálásával

Ez a cikk a sablonok által generált alapértelmezett `run.ps1` parancsfájl PowerShell-függvényeit mutatja be.
A függvényeket azonban PowerShell-modulokban is felveheti. A modulban a function. JSON konfigurációs fájl `scriptFile` és `entryPoint` mezőinek használatával hivatkozhat az adott függvény kódjára.

Ebben az esetben `entryPoint` a `scriptFile`ban hivatkozott PowerShell-modul függvényének vagy parancsmagjának a neve.

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

Ebben a példában a `myFunction`hoz tartozó konfiguráció olyan `scriptFile` tulajdonságot tartalmaz, amely egy másik mappában található PowerShell-modulra hivatkozik `PSFunction.psm1`re.  A `entryPoint` tulajdonság a `Invoke-PSTestFunc` függvényre hivatkozik, amely a modul belépési pontja.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Ezzel a konfigurációval a `Invoke-PSTestFunc` pontosan úgy lesz végrehajtva, mint egy `run.ps1`.

## <a name="considerations-for-powershell-functions"></a>A PowerShell-függvények szempontjai

A PowerShell-függvények használatakor vegye figyelembe az alábbi részekben ismertetett szempontokat.

### <a name="cold-start"></a>Hidegindító

Azure Functions a [kiszolgáló nélküli üzemeltetési modellben](functions-scale.md#consumption-plan)való fejlesztésekor a hideg indítás a valóság. A *hűtőházi kezdés* azt az időtartamot jelenti, ameddig a Function alkalmazásnak futnia kell a kérelem feldolgozásához. A hidegindító folyamat gyakrabban fordul elő a fogyasztási tervben, mert a Function alkalmazás leáll az inaktivitási időszakok során.

### <a name="bundle-modules-instead-of-using-install-module"></a>A `Install-Module` használata helyett köteg modulok

A szkript minden meghívásnál fut. Ne használjon `Install-Module` a szkriptben. Ehelyett használja a `Save-Module` a közzététel előtt, hogy a függvénynek ne kelljen időt pazarolnia a modul letöltésével. Ha a ritkán használt funkciók hatással vannak a függvényekre, érdemes lehet a Function alkalmazást egy olyan [app Service-csomagra](functions-scale.md#app-service-plan) telepíteni *, amely* a [prémium szintű csomagra](functions-scale.md#premium-plan)van beállítva.

## <a name="next-steps"></a>Következő lépések

További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)

[a Host. JSON dokumentációja]: functions-host-json.md
