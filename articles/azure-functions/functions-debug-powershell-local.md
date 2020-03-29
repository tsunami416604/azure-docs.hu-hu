---
title: A PowerShell Azure-függvények helyi hibakeresése
description: Ismerje meg, hogyan fejleszthet funkciókat a PowerShell használatával.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163760"
---
# <a name="debug-powershell-azure-functions-locally"></a>A PowerShell Azure-függvények helyi hibakeresése

Az Azure Functions lehetővé teszi a funkciók Fejlesztése PowerShell-parancsfájlokként.

A PowerShell-funkciók helyi legeltetési hibákat is lehelhet, mint bármely PowerShell-parancsfájlt a következő szabványos fejlesztői eszközök használatával:

* [Visual Studio-kód:](https://code.visualstudio.com/)A Microsoft ingyenes, könnyű és nyílt forráskódú szövegszerkesztője a PowerShell-bővítményt, amely teljes PowerShell-fejlesztési élményt nyújt.
* PowerShell-konzol: Hibakeresés ugyanazokat a parancsokat használja, amelyeket bármely más PowerShell-folyamat hibakereséséhez használna.

[Az Azure Functions Core Tools](functions-run-local.md) támogatja az Azure Functions helyi hibakeresését, beleértve a PowerShell-függvényeket is.

## <a name="example-function-app"></a>Példa függvényalkalmazás

A cikkben használt függvényalkalmazás egyetlen HTTP-aktivált funkcióval rendelkezik, és a következő fájlokkal rendelkezik:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Ez a függvényalkalmazás hasonló ahhoz, amit a [PowerShell-gyorsútmutató](functions-create-first-function-powershell.md)befejezésekor kap.

A függvénykód `run.ps1` a következő parancsfájlhoz hasonlóan néz ki:

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

## <a name="set-the-attach-point"></a>A csatolási pont beállítása

Bármely PowerShell-függvény hibakereséséhez a függvénynek le kell állnia a csatolandó hibakeresőcsatlakoztatáshoz. A `Wait-Debugger` parancsmag leállítja a végrehajtást, és megvárja a hibakeresőt.

Mindössze annyit kell tennie, `Wait-Debugger` hogy adjunk egy `if` hívást a parancsmag fölött a nyilatkozatot, az alábbiak szerint:

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

A hibakeresés az `if` utasításnál kezdődik. 

A `Wait-Debugger` helyben most már hibakeresés a funkciók segítségével vagy visual studio kód vagy egy PowerShell-konzol használatával.

## <a name="debug-in-visual-studio-code"></a>Hibakeresés a Visual Studio-kódban

A PowerShell-függvények Visual Studio-kódban való hibakereséséhez a következő telepítésre van szüksége:

* [PowerShell-bővítmény a Visual Studio-kódhoz](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions-bővítmény a Visual Studio Code-hoz](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 vagy újabb](/powershell/scripting/install/installing-powershell-core-on-windows)

A függőségek telepítése után töltsön be egy meglévő PowerShell Functions projektet, vagy [hozza létre az első PowerShell Functions projektet.](functions-create-first-function-powershell.md)

>[!NOTE]
> Ha a projekt nem rendelkezik a szükséges konfigurációs fájlokkal, a rendszer kéri azok hozzáadását.

### <a name="set-the-powershell-version"></a>A PowerShell-verzió beállítása

A PowerShell Core a Windows PowerShell-lel együtt települ. Állítsa be a PowerShell-core-t PowerShell-verzióként a Visual Studio-kód PowerShell-bővítményével való használatra.

1. Nyomja le az F1 billentyűt a `Session`parancsraklap megjelenítéséhez, majd keresse meg a parancsot.

1. Válassza a **PowerShell: Munkamenet menü megjelenítése lehetőséget.**

1. Ha **a jelenlegi munkamenet** nem **PowerShell Core 6,** válassza **a Váltás a: PowerShell Core 6**lehetőséget.

Ha meg van nyitva egy PowerShell-fájl, az ablak jobb alsó sarkában zöld színnel jelenik meg a verzió. A szöveg kijelölése a munkamenet menüt is megjeleníti. További információ: [A Bővítményhez használandó PowerShell-verzió kiválasztása](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)című témakörben olvashat.

### <a name="start-the-function-app"></a>A függvényalkalmazás indítása

Ellenőrizze, `Wait-Debugger` hogy be van-e állítva abban a függvényben, ahová csatolni szeretné a hibakeresőt.  A `Wait-Debugger` hozzáadott, a funkció alkalmazás hibakeresés a Visual Studio-kód használatával.

Válassza a **Hibakeresés** ablaktáblát, majd **a Csatolás a PowerShell-függvényhez lehetőséget.**

![Hibakereső](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

A hibakeresés megkezdéséhez az F5 billentyű lenyomásával is elindíthatja a gombot.

A hibakeresési művelet a következő feladatokat végzi:

* Fut `func extensions install` a terminálon a függvényalkalmazás által igényelt Azure Functions-bővítmények telepítéséhez.
* Fut `func host start` a terminálon, hogy indítsa el a függvényalkalmazást a Functions host.
* Csatlakoztassa a PowerShell-hibakeresőt a PowerShell-runspace-hez a Functions futásidőben belül.

>[!NOTE]
> Biztosítania kell, hogy a PSWorkerInProcConcurrencyUpperBound 1-re legyen állítva, hogy biztosítsa a megfelelő hibakeresési élményt a Visual Studio-kódban. Ez az alapértelmezett beállítás.

A függvényalkalmazás futása után egy külön PowerShell-konzolra van szükség a HTTP-aktivált függvény hívásához.

Ebben az esetben a PowerShell-konzol az ügyfél. A `Invoke-RestMethod` funkció aktiválására szolgál.

PowerShell-konzolon futtassa a következő parancsot:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Észre fogja venni, hogy a válasz nem azonnal vissza. Ez azért `Wait-Debugger` van, mert csatolta a hibakereső és a PowerShell-végrehajtás ment szünet módban, amint lehetett. Ez azért van, mert a [BreakAll koncepció](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), amely később ismertetjük. Miután megnyomja a `continue` gombot, a hibakereső `Wait-Debugger`a .

Ezen a ponton a hibakereső csatlakoztatva van, és el tudja végezni az összes normál hibakereső műveleteket. A hibakereső Visual Studio-kódban való használatáról [a hivatalos dokumentációban](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)olvashat bővebben.

Miután folytatta, és teljes mértékben meghívta a parancsfájlt, észre fogja venni, hogy:

* A PowerShell-konzol, `Invoke-RestMethod` amely ezt tette, eredményt adott vissza
* A Visual Studio-kód PowerShell-integrált konzolja parancsfájl végrehajtásra vár

Később, amikor meghívja ugyanazt a függvényt, a PowerShell-bővítmény hibakeresője azonnal megszakad a `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Hibakeresés Egy PowerShell-konzolon

>[!NOTE]
> Ez a szakasz feltételezi, hogy elolvasta az Azure Functions `func host start` Core [Tools-dokumentumokat,](functions-run-local.md) és tudja, hogyan használhatja a parancsot a függvényalkalmazás elindításához.

Nyisson meg `cd` egy konzolt a függvényalkalmazás könyvtárába, és futtassa a következő parancsot:

```sh
func host start
```

A függvényalkalmazás futásával `Wait-Debugger` és a helyén, csatolhatja a folyamathoz. További két PowerShell-konzolra van szükség.

Az egyik konzol ügyfélként működik. Ebből, hívja, `Invoke-RestMethod` hogy indítsa el a funkciót. Például futtathatja a következő parancsot:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Észre fogod venni, hogy nem ad vissza választ, `Wait-Debugger`ami a . A PowerShell runspace most várja a hibakereső csatolni kell. Ezt kapcsoljuk.

A másik PowerShell-konzolon futtassa a következő parancsot:

```powershell
Get-PSHostProcessInfo
```

Ez a parancsmag a következő kimenethez hasonló táblázatot adja vissza:

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

Jegyezze fel `ProcessId` a táblázatban szereplő `ProcessName` elem `dotnet`hez a következőt: . Ez a folyamat a függvényalkalmazás.

Ezután futtassa a következő kódrészletet:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Az indítás után a hibakereső eltörik, és a következő kimenethez hasonló tetsző:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Ezen a ponton a [PowerShell hibakeresőjének](/powershell/module/microsoft.powershell.core/about/about_debuggers)egy töréspontjánál áll meg. Innen elvégezheti az összes szokásos hibakeresési műveletet, átléphet, beléphet, folytathatja, kiléphet és mások. A konzolon elérhető hibakeresési parancsok teljes készletének `h` `?` megtekintéséhez futtassa a vagy a parancsokat.

A parancsmaggal ezen a `Set-PSBreakpoint` szinten is beállíthat töréspontokat.

Miután folytatta, és teljes mértékben meghívta a szkriptet, észre fogja venni, hogy:

* A PowerShell-konzol, `Invoke-RestMethod` ahol végrehajtotta, most eredményt adott vissza.
* A PowerShell-konzol, `Debug-Runspace` ahol végrehajtotta, egy parancsfájl végrehajtására vár.

Ugyanazt a függvényt újra `Invoke-RestMethod` meghívhatja (például) és a `Wait-Debugger` hibakereső közvetlenül a parancs után tör ikon rakható be.

## <a name="considerations-for-debugging"></a>A hibakeresés szempontjai

A Függvénykód hibakeresésekénél tartsa szem előtt a következő problémákat.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`a hibakereső váratlan helyre törhet

A PowerShell-bővítmény használja `Debug-Runspace`, amely viszont támaszkodik a PowerShell `BreakAll` funkció. Ez a funkció azt mondja a PowerShell, hogy állítsa le az első parancs, amely végrehajtja. Ez a viselkedés lehetőséget ad arra, hogy töréspontokat állítson be a hibakereső runspace-en belül.

Az Azure Functions futásidejű futtat néhány parancsot, mielőtt ténylegesen hivatkozik a `run.ps1` parancsfájlt, így `Microsoft.Azure.Functions.PowerShellWorker.psm1` `Microsoft.Azure.Functions.PowerShellWorker.psd1`lehetséges, hogy a hibakereső végül törés a vagy .

Ha ez a szünet `continue` `c` történik, futtassa a vagy a parancsot, hogy átugorja ezt a töréspontot. Ezután megáll a várt töréspontnál.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a Funkciók PowerShell használatával történő fejlesztéséről, olvassa el az [Azure Functions PowerShell fejlesztői útmutatóját.](functions-reference-powershell.md)
