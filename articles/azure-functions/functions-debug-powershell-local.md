---
title: PowerShell az Azure Functions helyi hibakeresése
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
ms.openlocfilehash: 554b7b7f401ec7cdb1ae08839550b81d797764f2
ms.sourcegitcommit: 111a7b3e19d5515ce7036287cea00a7204ca8b56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64530331"
---
# <a name="debug-powershell-azure-functions-locally"></a>PowerShell az Azure Functions helyi hibakeresése

Az Azure Functions lehetővé teszi a függvények, PowerShell-parancsfájlok fejlesztése.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

A PowerShell-funkciók is hibakeresése helyileg, minden olyan PowerShell-parancsfájlok a következő szabványos fejlesztői eszközökkel, mint:

* [A Visual Studio Code](https://code.visualstudio.com/): A Microsoft ingyenes, könnyen használható és nyílt forráskódú szövegszerkesztő a PowerShell-bővítmény, amely egy teljes PowerShell fejlesztői felületet kínál.
* A PowerShell-konzolt: A hibakeresési használja ugyanazokat a parancsokat minden más PowerShell-folyamattal hibakeresése szeretné használni.

[Az Azure Functions Core Tools](functions-run-local.md) helyi Hibakeresés az Azure Functions, beleértve a PowerShell-funkciókat is támogatja.

## <a name="example-function-app"></a>Példa függvényalkalmazás

A cikk ezt használja a függvényalkalmazást egy HTTP által aktivált funkcióval rendelkezik, és a következő fájlokat:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

A függvényalkalmazás a hasonló kap, amikor befejeződik a [PowerShell rövid](functions-create-first-function-powershell.md).

A függvény kódját a `run.ps1` tűnik, az alábbi parancsfájlt:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>A csatolás pont beállítása

Hibakeresés a PowerShell-függvényeket, a függvény kell csatolni a hibakereső leállítása. A `Wait-Debugger` parancsmag leállítja a végrehajtást, és megvárja, amíg a hibakereső funkcióját.

Ehhez mindössze, adja hozzá a hívást a `Wait-Debugger` parancsmag feladatállapotában a `if` utasítás, az alábbiak szerint:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Hibakeresés kezdődik a `if` utasítást. 

A `Wait-Debugger` helyen, hibakeresés most már az a Funkciók, a Visual Studio Code-ot vagy a PowerShell-konzolt.

## <a name="debug-in-visual-studio-code"></a>Hibakeresés a Visual Studio Code-ban

Hibakeresés a Visual Studio Code-ban a PowerShell-függvények, a Visual Studio Code a következő kiterjesztések kell rendelkeznie:

* [PowerShell](/powershell/scripting/components/Visual Studio Code/using-Visual Studio Code)
* [Azure Functions](functions-create-first-function-vs-code.md)

Telepítése után a PowerShell és az Azure Functions bővítmények betöltése egy meglévő függvényalkalmazás projektjét. Emellett [-Functions-projekt létrehozása](functions-create-first-function-vs-code.md).

>[!NOTE]
> A projekt nem kell a szükséges konfigurációs fájlokat, kéri, adja hozzá őket.

### <a name="start-the-function-app"></a>Indítsa el a függvényalkalmazás

Ellenőrizze, hogy `Wait-Debugger` állítja be a függvény ahol társítsa a hibakeresőt szeretné.  A `Wait-Debugger` hozzáadott, a függvényalkalmazás a Visual Studio Code is hibakereséséhez.

Válassza ki a **Debug** ablaktáblán, majd **PowerShell-függvény csatlakoztatása**.

![Ladicí program](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Nyomja le a az F5 billentyűt, a hibakeresés is.

A művelet hibakeresés indítása a következő feladatokat hajtja végre:

* Futtatások `func extensions install` által az függvényalkalmazás bármely Azure Functions-bővítmény telepítéséhez a terminálon.
* Futtatások `func host start` indítsa el a függvényalkalmazás a függvények fogadó parancsot a terminálon.
* A PowerShell-Hibakereső csatlakoztatása a PowerShell futási térben a Functions futtatókörnyezete belül.

A függvényalkalmazás fut, a HTTP által aktivált függvény egy külön PowerShell-konzolt kell.

Ebben az esetben a PowerShell-konzol az ügyfél. A `Invoke-RestMethod` szolgál, hogy elindítsa a függvényt.

A PowerShell-konzol futtassa a következő parancsot:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Láthatja, hogy a nem azonnal adott vissza választ. Ez azért van, `Wait-Debugger` csatlakoztatva van a hibakeresőt és a PowerShell végrehajtási történt break üzemmódra vált, amint ez sikerült. Mert ez az a [BreakAll fogalom](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), amelyek később kifejtett. Ezt követően a `continue` gombra, a hibakereső most működésképtelenné válik a sor jobb után `Wait-Debugger`.

Ezen a ponton a hibakereső van csatolva, és megteheti a normál hibakereső műveletek. A hibakereső használatával a Visual Studio Code további információkért lásd: [a hivatalos dokumentáció](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Miután továbbra is, és a parancsfájl teljes meghívása, láthatja, hogy:

* A PowerShell-konzolon korábban a `Invoke-RestMethod` eredményt adott vissza
* A beépített PowerShell-konzolt, a Visual Studio Code egy szkriptet futtatandó vár

Ezt követő alkalommal, amikor hívhat meg ugyanezt a funkciót, a PowerShell-bővítmény jobb után működésképtelenné válik a hibakeresőt a `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Hibakeresés a PowerShell-konzol

>[!NOTE]
> Ez a szakasz azt feltételezi, hogy elolvasta a [Azure Functions Core Tools docs](functions-run-local.md) , és tudja, hogyan használható a `func host start` parancsot a függvényalkalmazást.

Nyisson meg egy konzol `cd` a könyvtárba, a függvényalkalmazást, és futtassa a következő parancsot:

```sh
func host start
```

A futó függvényalkalmazást és a `Wait-Debugger` teljesülnek, a folyamat is csatolhat. Két további PowerShell-konzolt kell.

Az ügyfél a konzolok egyik funkcionál. Ennek a hívás `Invoke-RestMethod` , aktiválja a függvényt. Ha például is futtassa a következő parancsot:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Láthatja, hogy azt nem ad vissza a választ, amely egy eredményt a rendszer, a `Wait-Debugger`. A PowerShell futási térben már vár egy hibakereső van csatlakoztatva. Folytassuk, amely csatolva.

A többi PowerShell konzolon futtassa a következő parancsot:

```powershell
Get-PSHostProcessInfo
```

Ez a parancsmag adja vissza egy táblát, amely a következő példához hasonlóan néz ki:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Jegyezze fel a `ProcessId` a cikk a táblában a `ProcessName` , `dotnet`. Ez a folyamat a függvényalkalmazás lesz.

Ezután futtassa a következő kódrészletet:

```powershell
# This enters into the the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Megkezdése után a hibakeresőt működésképtelenné válik, és a például a következő kimenet látható:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Ezen a ponton még egy töréspontot a következő leállt a [PowerShell hibakereső](/powershell/module/microsoft.powershell.core/about/about_debuggers). Itt is ezt a a szokásos hibakeresési műveletek, ugorja át, lépjen be, továbbra is, lépjen ki, és másokkal. A konzolon elérhető hibakeresési parancsok teljes készletének megtekintéséhez futtassa a `h` vagy `?` parancsokat.

Töréspontokat állíthat be az ezen a szinten a `Set-PSBreakpoint` parancsmagot.

Továbbra is, és a parancsfájl teljes meghívása után láthatja, hogy:

* A PowerShell-konzol, ahol hajtja végre `Invoke-RestMethod` mostantól adott vissza eredményt.
* A PowerShell-konzol, ahol hajtja végre `Debug-Runspace` várakozik egy parancsfájl hajtható végre.

Újra hívhatók meg ugyanezt a funkciót (használatával `Invoke-RestMethod` például) és a hibakeresőt után jobb működésképtelenné válik a `Wait-Debugger` parancsot.

## <a name="considerations-for-debugging"></a>Hibakeresés szempontjai

Vegye figyelembe a következő problémák a Functions-kódhoz történő hibakeresése során.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` a hibakereső váratlan helyen hibájához vezethet

A PowerShell-bővítmény által használt `Debug-Runspace`, amely támaszkodik a PowerShell `BreakAll` funkció. Ez a funkció arra utasítja a PowerShell használatával az első parancs végrehajtott állni. Ez a viselkedés lehetővé teszi, hogy a állítson be töréspontokat a hibakeresésen futási térben belül.

Az Azure Functions runtime néhány parancsot futtatja ténylegesen meghívása előtt a `run.ps1` szkriptet, így lehetséges, hogy a hibakeresőt említi használhatatlanná tévő belül a `Microsoft.Azure.Functions.PowerShellWorker.psm1` vagy `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Történjen, ez a megszakítás, futtassa a `continue` vagy `c` kihagyni a töréspont parancsot. Ezután állni a várt töréspontig.

## <a name="next-steps"></a>További lépések

Függvények PowerShell-lel kapcsolatos további információkért lásd: [fejlesztői útmutató az Azure Functions PowerShell](functions-reference-powershell.md).
