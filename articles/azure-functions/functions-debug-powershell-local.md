---
title: PowerShell-Azure Functions helyi hibakeresése
description: Ismerje meg, hogyan fejlesztheti a függvényeket a PowerShell használatával.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 36d15858e1fb535189891303a89b00021027f1e8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227040"
---
# <a name="debug-powershell-azure-functions-locally"></a>PowerShell-Azure Functions helyi hibakeresése

Azure Functions lehetővé teszi a függvények PowerShell-parancsfájlokként való fejlesztését.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

A PowerShell-függvényeket helyileg is felhasználhatja, ahogy bármelyik PowerShell-szkriptet a következő szabványos fejlesztői eszközök használatával:

* [Visual Studio Code](https://code.visualstudio.com/): a Microsoft ingyenes, könnyű és nyílt forráskódú szövegszerkesztője, amely a PowerShell-bővítmény teljes körű fejlesztési élményt nyújt.
* Egy PowerShell-konzol: a hibakereséshez ugyanazokat a parancsokat használja, amelyeket bármilyen más PowerShell-folyamat hibakereséséhez használni kíván.

[Azure functions Core Tools](functions-run-local.md) támogatja a Azure functions helyi hibakeresését, beleértve a PowerShell-függvényeket is.

## <a name="example-function-app"></a>Példa a Function alkalmazásra

A cikkben használt Function alkalmazás egyetlen HTTP által aktivált függvényt tartalmaz, és a következő fájlokat használja:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Ez a Function-alkalmazás a [PowerShell](functions-create-first-function-powershell.md)rövid útmutatójának elvégzéséhez hasonlóan fog megjelenni.

`run.ps1` a következő parancsfájlhoz hasonlóan néz ki:

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

A PowerShell-függvények hibakereséséhez a funkciónak le kell állnia a hibakereső csatolásához. Az `Wait-Debugger` parancsmag leállítja a végrehajtást, és megvárja a hibakeresőt.

Mindössze annyit kell tennie, hogy felveszi a `Wait-Debugger` parancsmaghoz közvetlenül a `if` utasítás fölötti hívást, a következőképpen:

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

A hibakeresés a `if` utasításban kezdődik. 

Ha `Wait-Debugger` van, mostantól a Visual Studio Code vagy a PowerShell-konzol használatával is lehet hibakeresést végezni a függvényekben.

## <a name="debug-in-visual-studio-code"></a>Hibakeresés a Visual Studio Code-ban

A Visual Studio Code-ban a PowerShell-függvények hibakereséséhez a következőkre van szükség:

* [PowerShell-bővítmény a Visual Studio Code-hoz](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions-bővítmény a Visual Studio Code-hoz](functions-create-first-function-vs-code.md)
* [PowerShell Core 6,2 vagy újabb](/powershell/scripting/install/installing-powershell-core-on-windows)

A függőségek telepítése után töltsön be egy meglévő PowerShell functions-projektet, vagy [hozza létre az első PowerShell functions projektet](functions-create-first-function-powershell.md).

>[!NOTE]
> Ha a projekt nem rendelkezik a szükséges konfigurációs fájlokkal, a rendszer kéri, hogy adja hozzá őket.

### <a name="set-the-powershell-version"></a>A PowerShell verziójának beállítása

A PowerShell Core a Windows PowerShell-lel párhuzamosan települ. Állítsa be a PowerShell Core-t PowerShell-verzióként a Visual Studio Code PowerShell-bővítménnyel való használatra.

1. Nyomja meg az F1 billentyűt a parancs raklapjának megjelenítéséhez, majd keressen rá `Session`.

1. Válassza a **PowerShell: munkamenet menü megjelenítése**lehetőséget.

1. Ha az **aktuális munkamenet** nem a **PowerShell Core 6**, válassza **a váltás a következőre: PowerShell Core 6**.

Ha meg van nyitva egy PowerShell-fájl, az ablak jobb alsó sarkában látható zöld színnel jelenik meg. Ha ezt a szöveget választja, a munkamenet menüt is megjeleníti. További információ: a [PowerShell verziójának kiválasztása a bővítménnyel való használatra](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>A Function alkalmazás elindítása

Ellenőrizze, hogy a `Wait-Debugger` be van-e állítva abban a függvényben, amelyhez csatolni szeretné a hibakeresőt.  A `Wait-Debugger` hozzáadva a Function alkalmazást a Visual Studio Code használatával lehet hibakeresésre használni.

Válassza a **hibakeresés** ablaktáblát, majd **csatolja a PowerShell-függvényt**.

![hibakereső](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Az F5 billentyű lenyomásával is elindíthatja a hibakeresést.

A hibakeresés indítása művelet a következő feladatokat hajtja végre:

* `func extensions install` futtat a terminálon a Function alkalmazás által igényelt Azure Functions-bővítmények telepítéséhez.
* A (z) `func host start` futtatja a terminálon a Function alkalmazás a functions gazdagépen való elindításához.
* Csatolja a PowerShell-hibakeresőt a PowerShell-RunSpace a functions futtatókörnyezeten belül.

>[!NOTE]
> Győződjön meg arról, hogy a PSWorkerInProcConcurrencyUpperBound értéke 1, hogy a hibakeresési élmény helyes legyen a Visual Studio Code-ban. Ez az alapértelmezett érték.

Ha a Function alkalmazás fut, külön PowerShell-konzolra van szükség a HTTP-triggert használó függvény meghívásához.

Ebben az esetben a PowerShell-konzol az ügyfél. A `Invoke-RestMethod` a függvény elindítására szolgál.

A PowerShell-konzolon futtassa a következő parancsot:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Megfigyelheti, hogy a rendszer nem küld azonnal visszajelzést. Ennek az az oka, hogy `Wait-Debugger` csatolta a hibakeresőt, és a PowerShell-végrehajtás a lehető leghamarabb megszakította a megszakítási módot. Ennek oka az [BreakAll koncepció](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), amelyet később ismertetünk. A `continue` gomb megnyomása után a Debugger most közvetlenül a `Wait-Debugger`után megszakítja a sort.

Ezen a ponton a hibakereső csatlakoztatva van, és elvégezheti az összes normál hibakereső műveletet. A Debugger a Visual Studio Code-ban való használatával kapcsolatos további információkért tekintse meg [a hivatalos dokumentációt](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Miután folytatta és teljes mértékben meghívta a szkriptet, megfigyelheti, hogy:

* A `Invoke-RestMethod`t eredményező PowerShell-konzol eredményét adta vissza
* A Visual Studio Code-ban a PowerShell integrált konzolja arra vár, hogy végrehajtsa a parancsfájlt.

Később, amikor meghívja ugyanezt a függvényt, a PowerShell-bővítményben lévő hibakereső közvetlenül a `Wait-Debugger`után megszakítja a műveletet.

## <a name="debugging-in-a-powershell-console"></a>Hibakeresés egy PowerShell-konzolon

>[!NOTE]
> Ez a szakasz azt feltételezi, hogy elolvasta a [Azure functions Core Tools dokumentumait](functions-run-local.md) , és tudja, hogyan használhatja a `func host start` parancsot a Function alkalmazás elindításához.

Nyisson meg egy konzolt, `cd` a Function alkalmazás könyvtárába, és futtassa a következő parancsot:

```sh
func host start
```

Ha a Function alkalmazás fut, és a `Wait-Debugger`, a folyamathoz csatolható. Ehhez két PowerShell-konzolra van szükség.

Az egyik konzol ügyfélként működik. Ettől kezdve a `Invoke-RestMethod` meghívásával aktiválja a függvényt. Futtathatja például a következő parancsot:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Megfigyelheti, hogy nem ad vissza választ, ami a `Wait-Debugger`eredménye. A PowerShell-RunSpace most már arra vár, hogy csatolja a hibakeresőt. Lássuk, hogy csatolva van.

A másik PowerShell-konzolon futtassa a következő parancsot:

```powershell
Get-PSHostProcessInfo
```

Ez a parancsmag egy táblázatot ad vissza, amely a következő kimenethez hasonlít:

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

Jegyezze fel a táblázatban szereplő elemek `ProcessId`ét `dotnet`ként a `ProcessName`. Ez a folyamat a Function alkalmazás.

Ezután futtassa a következő kódrészletet:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Az indítás után a hibakereső megszakítja és megjeleníti a következő kimenethez hasonlót:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Ezen a ponton a [PowerShell-hibakereső](/powershell/module/microsoft.powershell.core/about/about_debuggers)töréspontján leállt. Innen elvégezheti az összes szokásos hibakeresési műveletet, a lépéseket, a beléptetést, a folytatást, a kilépést és egyebeket. A konzolon elérhető hibakeresési parancsok teljes készletének megtekintéséhez futtassa a `h` vagy `?` parancsokat.

A töréspontok ezen a szinten is megadhatók az `Set-PSBreakpoint` parancsmaggal.

Ha folytatja, és teljes mértékben meghívja a szkriptet, láthatja, hogy:

* A `Invoke-RestMethod` végrehajtotta a PowerShell-konzolt, amellyel a rendszer sikeresen visszaadott egy eredményt.
* A PowerShell-konzol, amelyen végrehajtotta `Debug-Runspace`, egy parancsfájl végrehajtására vár.

Ugyanezt a függvényt újra meghívhatja (például a `Invoke-RestMethod` használatával), a hibakereső pedig a `Wait-Debugger` parancs után azonnal megszakad.

## <a name="considerations-for-debugging"></a>Hibakeresési megfontolások

A functions-kód hibakeresése során vegye figyelembe a következő problémákat.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` okozhatja, hogy a hibakereső nem várt helyre törik

A PowerShell-bővítmény `Debug-Runspace`használ, ami viszont a PowerShell `BreakAll` szolgáltatására támaszkodik. Ez a funkció azt jelzi, hogy a PowerShell leáll az első parancs végrehajtásakor. Ez a viselkedés lehetővé teszi a töréspontok beállítását a hibakereső RunSpace belül.

A Azure Functions futtatókörnyezet néhány parancsot futtat, mielőtt ténylegesen meghívja a `run.ps1` parancsfájlt, ezért lehetséges, hogy a hibakereső véget ér a `Microsoft.Azure.Functions.PowerShellWorker.psm1` vagy `Microsoft.Azure.Functions.PowerShellWorker.psd1`on belül.

Ha ez a szünet történik, futtassa a `continue` vagy `c` parancsot a Töréspont átugrásához. Ezután leállíthatja a várt töréspontot.

## <a name="next-steps"></a>Következő lépések

További információ a függvények a PowerShell használatával történő fejlesztéséről: [Azure functions PowerShell fejlesztői útmutató](functions-reference-powershell.md).
