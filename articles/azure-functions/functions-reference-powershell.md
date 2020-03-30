---
title: PowerShell fejlesztői útmutató az Azure Functionshez
description: Ismerje meg, hogyan fejleszthet funkciókat a PowerShell használatával.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276737"
---
# <a name="azure-functions-powershell-developer-guide"></a>Az Azure Functions PowerShell fejlesztői útmutatója

Ez a cikk részletesen ismerteti, hogyan írja az Azure Functions a PowerShell használatával.

A PowerShell Azure-függvény (függvény) egy PowerShell-parancsfájlként jelenik meg, amely az aktiváláskor hajt végre. Minden függvényparancsfájl rendelkezik egy kapcsolódó `function.json` fájllal, amely meghatározza a függvény működését, például a működés módját, valamint a bemeneti és kimeneti paramétereket. További információ: [Triggers and binding article](functions-triggers-bindings.md). 

Más típusú függvényekhez hasonlóan a PowerShell parancsfájlfüggvények olyan paramétereket vesznek fel, `function.json` amelyek megfelelnek a fájlban definiált összes bemeneti kötés nevének. Egy `TriggerMetadata` paraméter is át, amely további információkat tartalmaz az eseményindító, amely elindította a függvényt.

Ez a cikk feltételezi, hogy már elolvasta az [Azure Functions fejlesztői hivatkozását.](functions-reference.md) Az első PowerShell-függvény létrehozásához el kellett végeznie a [PowerShell functionsi rövid útmutatóját](functions-create-first-function-powershell.md) is.

## <a name="folder-structure"></a>Mappastruktúra

A PowerShell-projektekhez szükséges mappastruktúra a következőképpen néz ki. Ez az alapértelmezett lehet változtatni. További információt az alábbi [scriptFile](#configure-function-scriptfile) szakaszban talál.

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

A projekt gyökerében van egy [`host.json`](functions-host-json.md) megosztott fájl, amely a függvényalkalmazás konfigurálásához használható. Minden függvénysaját kódfájllal (.ps1) és kötési`function.json`konfigurációs fájllal ( ) rendelkezik. A function.json fájl szülőkönyvtárának neve mindig a függvény neve.

Bizonyos kötések fájl jelenlétét `extensions.csproj` igénylik. A Functions futásidejű [2.x-es és újabb verzióiban](functions-versions.md) szükséges kötésbővítmények a `extensions.csproj` fájlban vannak `bin` definiálva, a mappában lévő tényleges könyvtárfájlokkal együtt. Helyi fejlesztés esetén kötelező kiterjesztéseket kell [regisztrálnia.](functions-bindings-register.md#extension-bundles) Amikor funkciókat fejleszt az Azure Portalon, ez a regisztráció az Ön számára történik.

A PowerShell függvényalkalmazásokban szükség esetén `profile.ps1` előfordulhat, hogy egy függvényalkalmazás futásának megkezdésekor fut (egyébként *[hidegindításként](#cold-start)* ismert. További információ: [PowerShell-profil.](#powershell-profile)

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell-parancsfájl definiálása függvényként

Alapértelmezés szerint a Functions futásidejű megkeresi a függvényt a alkalmazásban, `run.ps1`ahol `run.ps1` ugyanaz a szülőkönyvtár van, mint a megfelelő `function.json`.

A parancsfájl végrehajtása számos argumentumot továbbít. A paraméterek kezeléséhez adjon `param` egy blokkot a parancsfájl tetejére, ahogy az a következő példában található:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata paraméter

A `TriggerMetadata` paraméter az eseményindítóval kapcsolatos további információk szolgáltatására szolgál. A további metaadatok kötéstől kötésig változnak, de mindegyik olyan tulajdonságot `sys` tartalmaz, amely a következő adatokat tartalmazza:

```powershell
$TriggerMetadata.sys
```

| Tulajdonság   | Leírás                                     | Típus     |
|------------|-------------------------------------------------|----------|
| UtcNow között     | Amikor az UTC-ben a funkció        | DateTime |
| Metódusneve | A kiváltott függvény neve     | sztring   |
| RandGuid között   | egy egyedi guid, hogy ez a végrehajtás a funkció | sztring   |

Minden eseményindítótípus nak más a metaadat-készlete. A `$TriggerMetadata` for `QueueTrigger` például `InsertionTime`többek `Id` `DequeueCount`között a , , , . A várólista-eseményindító metaadatairól további információt a [várólista-eseményindítók hivatalos dokumentációjában talál.](functions-bindings-storage-queue-trigger.md#message-metadata) Ellenőrizze a dokumentációt az [eseményindítók,](functions-triggers-bindings.md) amelyekkel dolgozik, hogy mi kerül az eseményindító metaadataiba.

## <a name="bindings"></a>Kötések

A PowerShellben [a kötések](functions-triggers-bindings.md) konfigurálva vannak, és egy függvény function.json. A függvények számos módon kommunikálnak a kötésekkel.

### <a name="reading-trigger-and-input-data"></a>Az eseményindító és a bemeneti adatok olvasása

Az eseményindító és a bemeneti kötések olvasása a függvénynek átadott paraméterek ként lesz olvasható. A bemeneti `direction` kötések `in` készlete a function.json függvényben van. A `name` definiált `function.json` tulajdonság a blokkban lévő `param` paraméter neve. Mivel a PowerShell névvel ellátott paramétereket használ a kötéshez, a paraméterek sorrendje nem számít. Ajánlott azonban követni a kötések sorrendjét, amelyeket a. `function.json`

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Kimeneti adatok írása

A Függvényekben a kimeneti kötés `direction` hez a function.json készlete `out` van. A függvények futásidejű parancsmaggal `Push-OutputBinding` írhat egy kimeneti kötést. A `name` kötés tulajdonsága minden esetben `function.json` megfelel a `Name` `Push-OutputBinding` parancsmag paraméterének.

Az alábbiakban bemutatjuk, hogyan hívhatja meg `Push-OutputBinding` a függvényparancsfájlt:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Egy adott kötés értékét is átadhatja a folyamaton keresztül.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`a következőkben megadott értéknek megfelelően `-Name`eltérően viselkedik:

* Ha a megadott név nem oldható fel érvényes kimeneti kötéssé, a rendszer hibát jelez.

* Ha a kimeneti kötés értékek gyűjteményét `Push-OutputBinding` fogadja el, többször is meghívhat több érték leküldéses.

* Ha a kimeneti kötés csak egy singleton értéket fogad el, a második alkalommal történő hívás `Push-OutputBinding` hibát okoz.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`Szintaxis

A híváshoz érvényes paraméterek `Push-OutputBinding`tartoznak:

| Név | Típus | Pozíció | Leírás |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Sztring | 1 | A beállítani kívánt kimeneti kötés neve. |
| **`-Value`** | Objektum | 2 | A beállítani kívánt kimeneti kötés értéke, amelyet a ByValue folyamatból fogad el. |
| **`-Clobber`** | Kapcsolóparaméter | Nevezett | (Nem kötelező) Ha meg van adva, az értéket egy megadott kimeneti kötéshez kell beállítani. | 

A következő gyakori paraméterek is támogatottak: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

További információ: [About CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Push-OutputBinding példa: HTTP válaszok

A HTTP-eseményindító egy kimeneti `response`kötés nevű válaszát adja vissza. A következő példában a `response` kimeneti kötés értéke "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Mivel a kimenet http-re, amely csak egyton értéket fogad `Push-OutputBinding` el, a rendszer hibát okoz, ha másodszor is meghívják.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Olyan kimenetek esetén, amelyek csak egyszeri értékeket fogadnak el, a `-Clobber` paraméter segítségével felülbírálhatja a régi értéket ahelyett, hogy egy gyűjteményhez próbálna hozzáadni. A következő példa feltételezi, hogy már hozzáadott egy értéket. A `-Clobber`használatával a következő példa válasza felülbírálja a meglévő értéket, hogy "kimeneti #3" értéket adjon vissza:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Leküldéses kimenetkötéspélda: Várólista kimenetkötése

`Push-OutputBinding`adatok küldésére szolgál kimeneti kötések, például egy [Azure Queue storage-kimeneti kötés.](functions-bindings-storage-queue-output.md) A következő példában a várólistába írt üzenet értéke "kimeneti #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A storage-várólista kimeneti kötése több kimeneti értéket fogad el. Ebben az esetben a következő példa hívása az első után ír a várólistába egy listát két elemből: "output #1" és "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

A következő példa, amikor az előző kettő után hívják, két további értéket ad hozzá a kimeneti gyűjteményhez:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

A várólistába íráskor az üzenet a következő négy értéket tartalmazza: "output #1", "output #2", "output #3" és "output #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`Parancsmag

A `Get-OutputBinding` parancsmag segítségével lekérheti a kimeneti kötések jelenleg beállított értékeket. Ez a parancsmag beolvas egy kivonattáblát, amely tartalmazza a kimeneti kötések nevét a megfelelő értékekkel. 

Az alábbi példa az `Get-OutputBinding` aktuális kötési értékek visszaadására használja:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`tartalmaz egy paramétert `-Name`is, amely a visszaadott kötés szűrésére használható, ahogy a következő példában is:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

A helyettesítő karakterek (*) támogatottak a ban. `Get-OutputBinding`

## <a name="logging"></a>Naplózás

A PowerShell-funkciók naplózása úgy működik, mint a normál PowerShell-naplózás. A naplózási parancsmagok segítségével írhat az egyes kimeneti adatfolyamok. Minden parancsmag a Függvények által használt naplószintre van leképezve.

| Funkciók naplózási szintje | Naplózva a parancsmag |
| ------------- | -------------- |
| Hiba | **`Write-Error`** |
| Figyelmeztetés | **`Write-Warning`**  | 
| Információ | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Információ | Az _információs_ szint naplózása. |
| Hibakeresés | **`Write-Debug`** |
| Nyomkövetés | **`Write-Progress`** <br /> **`Write-Verbose`** |

Ezeken a parancsmagokon kívül a folyamatra írt dolgokat a rendszer átirányítja a `Information` napló szintjére, és az alapértelmezett PowerShell-formázással jelenik meg.

> [!IMPORTANT]
> A `Write-Verbose` vagy `Write-Debug` parancsmagok használata nem elegendő a részletes és hibakeresési szint naplózásához. Azt is be kell állítania a naplószint küszöbértéket, amely deklarálja, hogy milyen szintű naplók ténylegesen érdekel. További információ: [A függvény alkalmazásnaplószintjének konfigurálása.](#configure-the-function-app-log-level)

### <a name="configure-the-function-app-log-level"></a>A függvény alkalmazásnaplószintjének konfigurálása

Az Azure Functions segítségével meghatározhatja a küszöbértéket, hogy könnyen szabályozhassa, hogyan írja a Functions a naplókba. A konzolra írt összes nyomkövetés küszöbértékének `logging.logLevel.default` beállításához használja a [ `host.json` ][host.json hivatkozás fájltulajdonságát.] Ez a beállítás a függvényalkalmazás összes függvényére vonatkozik.

A következő példa beállítja a küszöbértéket, hogy engedélyezze a részletes naplózást az `MyFunction`összes függvénynél, de beállítja a küszöbértéket a hibakeresési naplózás engedélyezéséhez egy nevű függvénynél:

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

További információ: [host.json reference].

### <a name="viewing-the-logs"></a>A naplók megtekintése

Ha a függvényalkalmazás fut az Azure-ban, az Application Insights segítségével figyelheti azt. Olvassa el [az Azure Functions figyelését,](functions-monitoring.md) ha többet szeretne megtudni a függvénynaplók megtekintéséről és lekérdezéséről.

Ha a függvényalkalmazást helyileg futtatja fejlesztésre, alapértelmezés szerint a fájlrendszer naplói. A naplók megtekintéséhez a konzolon, állítsa be a `AZURE_FUNCTIONS_ENVIRONMENT` környezeti változót, `Development` mielőtt a Függvény alkalmazás.

## <a name="triggers-and-bindings-types"></a>Eseményindítók és kötések típusai

Számos eseményindítók és kötések állnak rendelkezésre, hogy használja a függvényalkalmazás. Az eseményindítók és kötések teljes listája [itt található.](functions-triggers-bindings.md#supported-bindings)

Az összes eseményindító és kötés néhány valós adattípusként jelenik meg a kódban:

* Kivonatoló
* sztring
* bájt[]
* int
* double
* httpRequestcontext
* httpResponsecontext

A lista első öt típusa szabványos .NET-típus. Az utolsó kettőt csak a [HttpTrigger eseményindító](#http-triggers-and-bindings)használja.

A függvények minden kötési paraméterének az alábbi típusok egyikének kell lennie.

### <a name="http-triggers-and-bindings"></a>HTTP-eseményindítók és kötések

A HTTP- és webhook-eseményindítók és a HTTP-kimeneti kötések kérés- és válaszobjektumokat használnak a HTTP-üzenetküldés hez.

#### <a name="request-object"></a>Kérelem objektum

A parancsfájlba átadott kérelemobjektum típusa `HttpRequestContext`a következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Leírás                                                    | Típus                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | A kérelem törzsét tartalmazó objektum. `Body`az adatok alapján a legjobb típusba kerül. Ha például az adatok JSON, kivonattable-ként adják át. Ha az adat egy karakterlánc, akkor karakterláncként kerül átadásra. | objektum |
| **`Headers`** | A kérelemfejléceket tartalmazó szótár.                | Szótár<karakterlánc,karakterlánc><sup>*</sup> |
| **`Method`** | A kérelem HTTP-metódusa.                                | sztring                    |
| **`Params`**  | A kérelem útválasztási paramétereit tartalmazó objektum. | Szótár<karakterlánc,karakterlánc><sup>*</sup> |
| **`Query`** | A lekérdezési paramétereket tartalmazó objektum.                  | Szótár<karakterlánc,karakterlánc><sup>*</sup> |
| **`Url`** | A kérelem URL-címe.                                        | sztring                    |

<sup>*</sup>Minden `Dictionary<string,string>` kulcs nem szokja a kis- és nagybetűket.

#### <a name="response-object"></a>Válaszobjektum

A visszaküldendő válaszobjektum típusa `HttpResponseContext`a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás                                                 | Típus                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | A válasz törzsét tartalmazó objektum.           | objektum                    |
| **`ContentType`** | Egy rövid kéz a válasz tartalomtípusának beállításához. | sztring                    |
| **`Headers`** | A válaszfejléceket tartalmazó objektum.               | Szótár vagy Hashtable   |
| **`StatusCode`**  | A válasz HTTP-állapotkódja.                       | karakterlánc vagy int             |

#### <a name="accessing-the-request-and-response"></a>A kérés és a válasz elérése

Http-eseményindítókkal való munka során ugyanúgy elérheti a HTTP-kérelmet, mint bármely más bemeneti kötést. A `param` tömbben van.

Használjon `HttpResponseContext` objektumot válasz visszaadásához, ahogy az a következőkben látható:

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

Ennek a funkciónak az eredménye:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Típusöntés a ravaszokhoz és kötésekhez

Bizonyos kötések, például a blob-kötés, megadhatja a paraméter típusát.

Ha például a Blob storage-ból származó adatokat karakterláncként `param` szeretné megadni, adja hozzá a következő típust a blokkomhoz:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profil

A PowerShellben van egy PowerShell-profil fogalma. Ha nem ismeri a PowerShell-profilokat, olvassa el [a Profilok – ismertet című témakört.](/powershell/module/microsoft.powershell.core/about/about_profiles)

A PowerShell-függvények, a profil-parancsfájl végrehajtása a függvényalkalmazás indításakor. A függvényalkalmazások az első üzembe helyezéskor és az alaplegműködő[(hidegindítás)](#cold-start)után indulnak el.

Ha olyan eszközökkel hoz létre függvényalkalmazást, mint például a `profile.ps1` Visual Studio-kód és az Azure Functions Core Tools, egy alapértelmezett alkalmazás jön létre. Az alapértelmezett profil megmarad [a Core Tools GitHub-tárházban,](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) és a következőket tartalmazza:

* Automatikus MSI-hitelesítés az Azure-ba.
* Az Azure PowerShell `AzureRM` PowerShell-aliasok bekapcsolása, ha szeretné.

## <a name="powershell-version"></a>PowerShell-verzió

Az alábbi táblázat a Functions futásidejűek egyes főverziói által használt PowerShell-verziót mutatja be:

| Függvények verziója | PowerShell-verzió                             |
|-------------------|------------------------------------------------|
| 1,x               | Windows PowerShell 5.1 (a futásidő zárolva) |
| 2,x               | PowerShell Core 6                              |

Az aktuális verziót bármely `$PSVersionTable` funkcióból történő nyomtatással láthatja.

## <a name="dependency-management"></a>Függőségkezelés

A Functions segítségével használhatja a [PowerShell-gyűjteményt](https://www.powershellgallery.com) a függőségek kezeléséhez. Ha a függőségkezelés engedélyezve van, a requirements.psd1 fájl a szükséges modulok automatikus letöltésére szolgál. Ezt a viselkedést úgy `managedDependency` engedélyezheti, hogy a tulajdonságot `true` a [host.json fájl](functions-host-json.md)gyökerébe állítja, ahogy az a következő példában is:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Amikor új PowerShell-függvényprojektet hoz létre, a függőségkezelés alapértelmezés szerint engedélyezve van, az [ `Az` Azure-modullal](/powershell/azure/new-azureps-module-az) együtt. A jelenleg támogatott modulok maximális száma 10. A támogatott szintaxis vagy _`MajorNumber`_ `.*` a modul pontos verziója, ahogy az alábbi requirements.psd1 példában látható:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

A requirements.psd1 fájl frissítésekor a frissített modulok az újraindítás után települnek.

> [!NOTE]
> A felügyelt függőségekhez www.powershellgallery.com kell hozzáférni a modulok letöltéséhez. Helyi futtatáskor győződjön meg arról, hogy a futtatójön helyt tud ütközni az URL-cím hez a szükséges tűzfalszabályok hozzáadásával. 

A következő alkalmazásbeállítások segítségével módosíthatja a felügyelt függőségek letöltésének és telepítésének módját. Az alkalmazásfrissítés `MDMaxBackgroundUpgradePeriod`a ( alkalmazásfrissítés ) belül `MDNewSnapshotCheckPeriod`kezdődik, és a frissítési folyamat körülbelül a .

| Függvényalkalmazás beállítása              | Alapértelmezett érték             | Leírás                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`(7 nap)     | Minden PowerShell-munkavégző folyamat kezdeményezi a modulfrissítések ellenőrzését a `MDMaxBackgroundUpgradePeriod` PowerShell-galériában a folyamat indításakor és minden után. Ha egy új modulverzió érhető el a PowerShell-galériában, akkor telepítve van a fájlrendszerre, és elérhetővé válik a PowerShell-dolgozók számára. Ennek az értéknek a csökkentése lehetővé teszi, hogy a függvényalkalmazás hamarabb megkapja az újabb modulverziókat, de növeli az alkalmazás erőforrás-használatát (hálózati I/O, CPU, tároló). Ennek az értéknek a növelésével csökken az alkalmazás erőforrás-felhasználása, de késleltetheti az új modulverziók alkalmazását is. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`(1 óra)       | Miután új modulverziók telepítése a fájlrendszerbe, minden PowerShell munkavégző folyamat újra kell indítani. A PowerShell-dolgozók újraindítása hatással van az alkalmazás rendelkezésre állására, mivel megszakíthatja az aktuális függvényvégrehajtását. Amíg az összes PowerShell-munkavégző folyamat újra nem indul, a függvénymeghívások a régi vagy az új modulverziót használhatják. Az összes PowerShell-dolgozó `MDNewSnapshotCheckPeriod`újraindítása a rendszeren belül fejeződik be. Ennek az értéknek a növelése csökkenti a megszakítások gyakoriságát, de növelheti azt az időtartamot is, amikor a függvénymeghívások nem determinisztikusan használják a régi vagy az új modulverziót. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`(1 nap)     | A gyakori dolgozói újraindítások túlzott modulfrissítésének elkerülése érdekében a rendszer nem ellenőrzi a modulfrissítéseket, ha az utolsó beadást már megindító dolgozó már kezdeményezte a beadást. `MDMinBackgroundUpgradePeriod` |

Kihasználva a saját egyéni modulok egy kicsit más, mint ahogy tennéd rendesen.

A helyi számítógépen a modul a globálisan elérhető mappák egyikébe lesz `$env:PSModulePath`telepítve. Az Azure-ban való futtatáskor nincs hozzáférése a számítógépre telepített modulokhoz. Ez azt `$env:PSModulePath` jelenti, hogy a PowerShell-függvényalkalmazás eltér a normál PowerShell-parancsfájl. `$env:PSModulePath`

A Függvények csoportban két elérési utat `PSModulePath` tartalmaz:

* A `Modules` függvényalkalmazás gyökerében található mappa.
* A PowerShell-nyelvi dolgozó által vezérelt `Modules` mappa elérési útja.

### <a name="function-app-level-modules-folder"></a>Függvényalkalmazás-szintű `Modules` mappa

Az egyéni modulok használatához olyan modulokat helyezhet el, `Modules` amelyektől a függvények függnek egy mappában. Ebből a mappából a modulok automatikusan elérhetők a függvények futásidejű számára. A függvényalkalmazás bármely funkciója használhatja ezeket a modulokat. 

> [!NOTE]
> A requirements.psd1 fájlban megadott modulok automatikusan letöltődnek és bekerülnek az elérési útba, így nem kell őket a modulok mappájába foglalni. Ezek a felhőben való `$env:LOCALAPPDATA/AzureFunctions` futtatáskor `/data/ManagedDependencies` helyileg tárolódnak a mappában és a mappában.

Az egyéni modul funkció előnyeinek `Modules` kihasználásához hozzon létre egy mappát a függvényalkalmazás gyökerében. Másolja a függvényekben használni kívánt modulokat erre a helyre.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Egy `Modules` mappa esetén a függvényalkalmazásnak a következő mappastruktúrával kell rendelkeznie:

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

A függvényalkalmazás indításakor a PowerShell-nyelvi dolgozó hozzáadja ezt `Modules` a mappát a mappához, így ugyanúgy támaszkodhat a `$env:PSModulePath` modul automatikus betöltésére, mint egy normál PowerShell-parancsfájlban.

### <a name="language-worker-level-modules-folder"></a>Nyelvi munkavégző szint `Modules` mappa

A PowerShell nyelvi dolgozója gyakran használ több modult. Ezeket a modulokat a program `PSModulePath`utolsó pozíciójában határozzák meg. 

A modulok aktuális listája a következő:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): az archívumok, `.zip`például a , `.nupkg`és mások használatához használt modul.
* **ThreadJob**: A PowerShell-feladat API-k szálalapú implementációja.

A Functions alapértelmezés szerint a modulok legújabb verzióját használja. Egy adott modulverzió használatához helyezze az `Modules` adott verziót a függvényalkalmazás mappájába.

## <a name="environment-variables"></a>Környezeti változók

A Funkciók alkalmazásban [az alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatáskapcsolati karakterláncok környezeti változókként jelennek meg a végrehajtás során. Ezeket a beállításokat a használatával `$env:NAME_OF_ENV_VAR`érheti el, ahogy az a következő példában látható:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ha helyileg fut, a program a [local.settings.json](functions-run-local.md#local-settings-file) projektfájlból olvassa be az alkalmazásbeállításokat.

## <a name="concurrency"></a>Egyidejűség

Alapértelmezés szerint a Funkciók PowerShell futásidejű csak egy függvény meghívása egy időben. Ez az egyidejűségi szint azonban a következő esetekben nem biztos, hogy elegendő:

* Amikor egyszerre sok idézést próbál kezelni.
* Ha olyan függvényeket, amelyek meghívja más függvények belül ugyanabban a függvényalkalmazásban.

Ezt a viselkedést úgy módosíthatja, hogy a következő környezeti változót egész értékre állítja:

```
PSWorkerInProcConcurrencyUpperBound
```

Ezt a környezeti változót a Függvényalkalmazás [alkalmazásbeállításaiban](functions-app-settings.md) állíthatja be.

### <a name="considerations-for-using-concurrency"></a>Az egyidejűség használatának szempontjai

PowerShell egy _egyetlen szálas_ parancsfájlnyelv alapértelmezés szerint. Azonban egyidejűség adható több PowerShell-runspaces ugyanabban a folyamatban használatával. A létrehozott runspaces mennyisége meg egyezik a PSWorkerInProcConcurrencyUpperBound alkalmazás beállításával. Az átviteli sebességét befolyásolja a kiválasztott tervben rendelkezésre álló processzor és memória mennyisége.

Az Azure PowerShell bizonyos _folyamatszintű_ környezeteket és állapotokat használ a felesleges gépelési műveletek megmentése érdekében. Ha azonban bekapcsolja az egyidejűséget a függvényalkalmazásban, és olyan műveleteket hív meg, amelyek állapotváltozásra hajtanak, a versenyfeltételek et is megkaphatja. Ezeket a versenyfeltételeket nehéz hibakeresésnek kitenni, mert az egyik hívás egy bizonyos állapotra támaszkodik, a másik pedig megváltoztatta az állapotot.

Az Azure PowerShell rel, mivel egyes műveletek jelentős időt vehet igénybe. Azonban óvatosan kell eljárnia. Ha azt gyanítja, hogy egy versenyállapot, állítsa be a PSWorkerInProcConcurrencyUpperBound alkalmazás beállítása, `1` és helyette használja a nyelvi [munkavégző folyamat szintű elkülönítése](functions-app-settings.md#functions_worker_process_count) az egyidejűséghez.

## <a name="configure-function-scriptfile"></a>Konfigurálás i`scriptFile`

Alapértelmezés szerint a PowerShell-függvény `run.ps1`a programból jön ki, amely `function.json`nek ugyanaz a szülőkönyvtára van, mint a megfelelőnek.

A `scriptFile` tulajdonság `function.json` a segítségével a következő példához hasonló mappastruktúra leéséhez használható:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Ebben az esetben `function.json` `myFunction` a `scriptFile` for tartalmaz egy tulajdonságot, amely a fájlt az exportált függvénnyel hivatkozza.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>PowerShell-modulok használata entryPoint konfigurálásával

Ez a cikk a PowerShell-függvényeket a sablonok által létrehozott alapértelmezett `run.ps1` parancsfájlban mutatta be.
Azonban a funkciókat is beillesztheti a PowerShell-modulokba. A modulban lévő adott függvénykódra `scriptFile` hivatkozhat a function.json konfigurációs fájlban lévő és `entryPoint` mezők használatával.

Ebben az `entryPoint` esetben a PowerShell-modulban található függvény vagy parancsmag `scriptFile`neve a.

Vegye figyelembe a következő mappastruktúrát:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Hol `PSFunction.psm1` tartalmaz:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Ebben a példában `myFunction` a `scriptFile` konfiguráció tartalmaz `PSFunction.psm1`egy tulajdonságot, amely hivatkozik , amely egy PowerShell-modul egy másik mappában.  A `entryPoint` tulajdonság a `Invoke-PSTestFunc` függvényre hivatkozik, amely a modul belépési pontja.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Ezzel a konfigurációval a `Invoke-PSTestFunc` gets `run.ps1` végre pontosan úgy, ahogy lenne.

## <a name="considerations-for-powershell-functions"></a>A PowerShell-függvényekkel kapcsolatos szempontok

Amikor a PowerShell-függvények, vegye figyelembe a következő szakaszokban található szempontokat.

### <a name="cold-start"></a>Hidegindítás

Az Azure Functions kiszolgáló [nélküli üzemeltetési modellben](functions-scale.md#consumption-plan)történő fejlesztése során a hideg indítások valóságnak valóra váltanak. *A hidegindítás* azt az időtartamot veszi igénybe, amíg a függvényalkalmazás elindul a kérelem feldolgozásához. A hidegindítás gyakrabban fordul elő a fogyasztási tervben, mert a függvényalkalmazás inaktív időszakokban leáll.

### <a name="bundle-modules-instead-of-using-install-module"></a>Kötegmodulok használata helyett`Install-Module`

A parancsfájl minden híváson fut. Ne `Install-Module` használja a parancsfájlban. Ehelyett `Save-Module` használja a közzététel előtt, hogy a funkció nem kell időt pazarolni a modul letöltésére. Ha a hidegindítások hatással vannak a funkciókra, fontolja meg a függvényalkalmazás üzembe helyezését egy [Olyan App Service-csomagra,](functions-scale.md#app-service-plan) amely mindig egy Prémium *csomagra* vagy [egy Prémium csomagra](functions-scale.md#premium-plan)van beállítva.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions aktiválódik és kötéseket köt](functions-triggers-bindings.md)

[host.json-referencia]: functions-host-json.md
