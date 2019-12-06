---
title: Gyermek runbookok Azure Automation
description: Ismerteti a különböző módszereit, amelyekkel elindíthat egy runbook a Azure Automation egy másik runbook, és megoszthatja egymás között az adatokat.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e7341a8c270d16497430a70c2a1b21354a775787
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850448"
---
# <a name="child-runbooks-in-azure-automation"></a>Gyermek runbookok Azure Automation

Ajánlott eljárás a Azure Automation az újrafelhasználható, moduláris runbookok és más runbookok által használt diszkrét függvények írására. A szülő runbook gyakran egy vagy több alárendelt runbookok hív meg a szükséges funkciók elvégzéséhez. A gyermek runbook kétféleképpen hívható meg, és mindegyik különböző eltéréseket tartalmaz, amelyeket meg kell értenie, hogy melyik a legmegfelelőbb a különböző forgatókönyvekhez.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Alárendelt runbook meghívása beágyazott végrehajtás használatával

Runbook másik runbookból történő beágyazott indításához használja a runbook nevét, és adja meg a paraméterek értékeit ugyanúgy, mintha egy tevékenységet vagy egy parancsmagot használna.  Az azonos Automation-fiók összes runbookok elérhetővé válik minden más számára, hogy ily módon lehessen használni. Mielőtt a szülőrunbook a következő sorra lépne, megvárja, hogy a gyermekrunbook befejeződjön, és a rendszer minden kimenetet közvetlenül a szülőnek ad vissza.

A beágyazottan indított runbook ugyanazt a feladatot futtatja, mint a szülőrunbook. Nincs jelezve az általa futtatott gyermek runbook tartozó feladatok előzményei. A gyermek runbook származó kivételek és stream-kimenetek a szülőhöz vannak társítva. Ez a viselkedés kevesebb feladatot eredményez, és megkönnyíti a nyomon követést és a hibakeresést, mivel a gyermek runbook és annak bármely stream-kimenete által okozott kivételek a fölérendelt feladattal vannak társítva.

Runbook közzétételekor az általa megjelenő összes gyermek runbookok már közzé kell tenni. Ennek az az oka, hogy Azure Automation összeállítja az összes gyermek runbookok való társítást egy runbook lefordításakor. Ha nem, akkor a fölérendelt runbook úgy tűnik, hogy megfelelően közzéteszi a közzétételt, de kivételt fog előkészíteni. Ha ez történik, újra közzéteheti a szülő runbook, hogy megfelelően hivatkozzon a gyermek runbookok. Nem kell újból közzétennie a szülő runbook, ha a gyermek runbookok bármelyike megváltozik, mert a társítás már létrejött.

Egy beágyazott alárendelt runbook paraméterei bármilyen adattípus lehetnek, beleértve az összetett objektumokat is. A runbook a Azure Portal vagy a Start-AzureRmAutomationRunbook parancsmaggal történő indításakor nem létezik [JSON-szerializálás](start-runbooks.md#runbook-parameters) .

### <a name="runbook-types"></a>Forgatókönyvtípusok

Mely típusok hívhatják egymást:

* A [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) és a [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) meghívhatják egymásba a beágyazott (mindkettő PowerShell-alapú).
* Egy [PowerShell-munkafolyamat runbook](automation-runbook-types.md#powershell-workflow-runbooks) és grafikus PowerShell-munkafolyamati runbookok meghívhat egymásba (mindkettő PowerShell-munkafolyamaton alapuló)
* A PowerShell-típusok és a PowerShell-munkafolyamatok típusai nem hívhatják egymást, és a Start-AzureRmAutomationRunbook parancsot kell használniuk.

Mikor teszi közzé a sorrendet:

* A runbookok közzétételi sorrendje csak a PowerShell-munkafolyamatok és a grafikus PowerShell-munkafolyamat runbookok esetében számít.

Ha a grafikus vagy a PowerShell-alapú munkafolyamat alárendelt runbook a beágyazott végrehajtás használatával hívja meg, használja a runbook nevét.  A PowerShell gyermek runbook meghívásakor el kell indítania a nevét a következővel: *.\\* annak megadásához, hogy a parancsfájl a helyi könyvtárban található.

### <a name="example"></a>Példa

A következő példa egy teszt gyermek runbook indít el, amely három paramétert fogad el: egy összetett objektumot, egy egész számot és egy logikai értéket. A rendszer egy változóhoz társítja a gyermekrunbook kimenetét.  Ebben az esetben a gyermek runbook egy PowerShell-munkafolyamat runbook.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

A következő ugyanaz a példa egy PowerShell-runbook gyermekként való használatára.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Gyermek runbook indítása parancsmag használatával

> [!IMPORTANT]
> Ha a `Start-AzureRmAutomationRunbook` parancsmaggal a `-Wait` kapcsolóval és a gyermek runbook eredményével rendelkező gyermek runbook hivatkozik, akkor hibák jelentkezhetnek. A hiba megoldásához tekintse meg a [gyermek runbookok az objektum kimenetével](troubleshoot/runbooks.md#child-runbook-object) című témakört, amelyből megtudhatja, hogyan valósítja meg az eredmények lekérdezéséhez szükséges logikát, és hogyan használhatja a [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

A [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) parancsmaggal elindíthat egy runbook a következő témakörben leírtak szerint: [Runbook elindítása a Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell)használatával. Ehhez a parancsmaghoz két mód használható.  Egy módban a parancsmag a feladattípust adja vissza, ha az alárendelt feladatot a gyermek runbook hozza létre.  Más módban, amelyet a **-WAIT** paraméter megadásával engedélyez, a parancsmag addig vár, amíg a gyermek feladata be nem fejeződik, és visszaadja a gyermek runbook kimenetét.

A-parancsmaggal elindított runbook-feladatok egy külön feladatokban futnak a szülő runbook. Ez a viselkedés több feladatot eredményez, mint a runbook inline elindítása, és nehezebbé teszi a nyomon követést. A szülő több alárendelt runbook is indíthat aszinkron módon anélkül, hogy az egyes műveletek befejezésére kellene várnia. Ahhoz, hogy egyazon párhuzamos végrehajtás meghívja a gyermek runbookok, a szülő runbook a [Parallel kulcsszót](automation-powershell-workflow.md#parallel-processing)kell használnia.

A gyermek runbookok kimenete nem kerül vissza a szülő runbook megbízhatóan az időzítés miatt. Emellett bizonyos változók, például a $VerbosePreference, a $WarningPreference és mások nem lesznek propagálva a gyermek runbookok. A probléma elkerülése érdekében a `Start-AzureRmAutomationRunbook` parancsmaggal elindíthatja a gyermek runbookok különálló Automation-feladatként a `-Wait` kapcsolóval. Ez megakadályozza a szülő runbook, amíg a gyermek runbook be nem fejeződik.

Ha nem szeretné, hogy a szülő runbook le legyen tiltva a várakozás során, a `Start-AzureRmAutomationRunbook` parancsmaggal elindíthatja a gyermek runbook a `-Wait` kapcsoló nélkül. Ezután `Get-AzureRmAutomationJob` kell várnia a feladatok befejezésére, és `Get-AzureRmAutomationJobOutput` és `Get-AzureRmAutomationJobOutputRecord` az eredmények lekéréséhez.

Egy parancsmaggal elindított alárendelt runbook paramétereinek megadása szórótábla az [runbook-paraméterekben](start-runbooks.md#runbook-parameters)leírtak szerint. Csak az egyszerű adattípusokat lehet használni. Ha a runbook rendelkezik összetett adattípusú paraméterrel, beágyazottan kell meghívni.

Előfordulhat, hogy az előfizetési környezet elvész, amikor elindítják a gyermek runbookok különálló feladatként. Ahhoz, hogy az alárendelt runbook Azure RM-parancsmagokat hajtson végre egy adott Azure-előfizetésen belül, a gyermek runbook a szülő runbook függetlenül kell hitelesítenie ezt az előfizetést.

Ha ugyanabban az Automation-fiókban lévő feladatok egynél több előfizetéssel működnek, az egyik feladatban szereplő előfizetés kiválasztásával megváltoztathatja a jelenleg kijelölt előfizetési környezetet más feladatokhoz. A probléma elkerüléséhez használja a `Disable-AzureRmContextAutosave –Scope Processsave` az egyes runbook elején. Ez a művelet csak a környezetet menti a runbook végrehajtásához.

### <a name="example"></a>Példa

A következő példa egy gyermek runbook indít el paraméterekkel, majd megvárja, amíg a Start-AzureRmAutomationRunbook-WAIT paraméterrel be nem fejeződik. Ha elkészült, a rendszer a kimenetét a gyermek runbook gyűjti. `Start-AzureRmAutomationRunbook`használatához hitelesítenie kell magát az Azure-előfizetésében.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Gyermek runbook meghívására szolgáló metódusok összehasonlítása

Az alábbi táblázat a runbookok másik runbookból történő meghívására szolgáló két módszer eltéréseit foglalja össze.

|  | Beágyazott | Parancsmag |
|:--- |:--- |:--- |
| Feladat |A gyermekrunbookok a szülővel azonos feladatban futnak. |A rendszer külön feladatot hoz létre a gyermekrunbookhoz. |
| Végrehajtás |A folytatás előtt a szülőrunbook megvárja, hogy a gyermekrunbook befejeződjön. |A szülő runbook közvetlenül a gyermek runbook elindítása után folytatódik, *vagy* a szülő runbook megvárja, amíg befejeződik a gyermek feladata. |
| Kimenet |A szülőrunbook közvetlenül lekérheti a gyermekrunbook kimenetét. |A szülő runbook le kell kérnie a gyermek runbook-feladatokból származó kimenetet *, vagy* a szülő runbook közvetlenül a gyermek runbook származó kimenetet kaphat. |
| Paraméterek |A gyermekrunbook paramétereinek értékeit külön kell meghatározni, és bármilyen adattípus használható. |A gyermek runbook paramétereinek értékeit egyetlen szórótábla kell egyesíteni. Ez a szórótábla csak olyan egyszerű, tömb és objektum típusú adattípusokat tartalmazhat, amelyek JSON-szerializálást használnak. |
| Automation-fiók |A szülő runbook csak azonos Automation-fiókban használhatja a gyermek runbook. |A szülő runbookok bármely Automation-fiókból gyermek runbook használhat ugyanabból az Azure-előfizetésből, és akár egy másik előfizetést is, amelyhez kapcsolódik. |
| Közzététel |A gyermekrunbookot a szülőrunbook közzététele előtt kell közzétenni. |A gyermek runbook bármikor közzé kell tenni a szülő runbook elindítása előtt. |

## <a name="next-steps"></a>Következő lépések

* [Runbook indítása Azure Automation](start-runbooks.md)
* [Runbook kimenete és üzenetei Azure Automation](automation-runbook-output-and-messages.md)

