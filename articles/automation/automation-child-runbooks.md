---
title: Gyermek runbookok Azure Automation
description: Ismerteti a különböző módszereit, amelyekkel elindíthat egy runbook a Azure Automation egy másik runbook, és megoszthatja egymás között az adatokat.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 5527b96ddf6ccebb60ca8130e48f6aae87a3f715
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246552"
---
# <a name="child-runbooks-in-azure-automation"></a>Gyermek runbookok Azure Automation

Ajánlott eljárás a Azure Automation az újrafelhasználható, moduláris runbookok és más runbookok által hívott diszkrét függvények írására. A szülő runbook gyakran egy vagy több alárendelt runbookok hív meg a szükséges funkciók elvégzéséhez. A gyermek runbook kétféleképpen hívható meg, és eltérő különbségek vannak, amelyeket érdemes megállapítani, hogy melyik a legmegfelelőbb a forgatókönyvekhez.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Alárendelt runbook meghívása beágyazott végrehajtás használatával

Ha egy runbook egy másik runbook szeretne meghívni, használja a runbook nevét, és adja meg a paramétereinek értékeit, ugyanúgy, mint egy tevékenységet vagy egy parancsmagot. Az azonos Automation-fiók összes runbookok elérhetővé válik minden más számára, hogy ily módon lehessen használni. A szülő runbook megvárja a gyermek runbook befejeződését, mielőtt továbblép a következő sorra, és minden kimenet közvetlenül a szülőhöz tér vissza.

Egy beágyazott runbookot indít el, ha ugyanazt a feladatot, mint a szülő runbook futtatja. A gyermek runbook feladatainak előzményei nem szerepelnek a feladatokban. A gyermek runbook származó kivételek és stream-kimenetek a szülőhöz vannak társítva. Ez a viselkedés kevesebb feladatot eredményez, és megkönnyíti a nyomon követést és a hibakeresést.

Runbook közzétételekor az általa megjelenő összes gyermek runbookok már közzé kell tenni. Ennek az az oka, hogy Azure Automation összeállítja az összes gyermek runbookok való társítást, amikor lefordít egy runbook. Ha a gyermek runbookok még nem tették közzé, úgy tűnik, hogy a fölérendelt runbook megfelelően közzé van téve, de kivételt hoz létre a indításakor. Ha ez történik, újra közzéteheti a szülő runbook, hogy megfelelően hivatkozzon a gyermek runbookok. A fölérendelt runbook nem kell újból közzétennie, ha a gyermek runbook módosul, mert a társítás már létrejött.

A inline nevű alárendelt runbook paramétereinek bármilyen adattípusa lehet, beleértve az összetett objektumokat is. Nincs JSON- [szerializálás](start-runbooks.md#runbook-parameters), mert a Azure Portal vagy a [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) parancsmaggal indítja el a runbook.

### <a name="runbook-types"></a>Runbook-típusok

Mely runbook-típusok hívhatják egymást?

* Egy [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) és egy [grafikus runbook](automation-runbook-types.md#graphical-runbooks) is meghívhat egymásba, mivel mindkettő PowerShell-alapú.
* A [PowerShell-munkafolyamatok runbook](automation-runbook-types.md#powershell-workflow-runbooks) és a grafikus PowerShell-munkafolyamat runbook meghívhatják egymást, mivel mindkettő PowerShell-munkafolyamat-alapú.
* A PowerShell-típusok és a PowerShell-munkafolyamatok típusai nem hívhatják egymást, és a **Start-AzAutomationRunbook**parancsot kell használniuk.

Mikor teszi közzé a sorrendet?

A runbookok közzétételi sorrendje csak a PowerShell-munkafolyamatok és a grafikus PowerShell-munkafolyamat runbookok esetében számít.

Ha a runbook a beágyazott végrehajtás használatával meghívja a grafikus vagy a PowerShell-munkafolyamatok alárendelt runbook, a a runbook nevét használja. A névnek a következővel kell kezdődnie: **.\\** annak megadásához, hogy a parancsfájl a helyi könyvtárban található.

### <a name="example"></a>Példa

A következő példa egy teszt gyermek runbook indít el, amely egy összetett objektumot, egy egész értéket és egy logikai értéket fogad el. A gyermekrunbook kimenetét egy változóban van hozzárendelve. Ebben az esetben a gyermek runbook egy PowerShell-munkafolyamat runbook.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Ez ugyanaz a példa, amely egy PowerShell-runbook használja a gyermekként.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Gyermek runbook indítása parancsmag használatával

> [!IMPORTANT]
> Ha a runbook meghív egy gyermek runbook a **Start-AzAutomationRunbook** parancsmaggal a *WAIT* paraméterrel, és a gyermek runbook egy objektum eredményét eredményezi, a művelet hibába ütközhet. A hiba megoldásához tekintse meg a [gyermek runbookok az objektum kimenetével](troubleshoot/runbooks.md#child-runbook-object) című témakört, amelyből megtudhatja, hogyan implementálhatja a logikát az eredmények lekérdezésére a [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) parancsmag használatával.

A **Start-AzAutomationRunbook** használatával elindíthat egy runbook a következő témakörben leírtak szerint: [Runbook elindítása Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell)-lel. Ehhez a parancsmaghoz két mód használható. Egy módban a parancsmag a feladattípust adja vissza, ha a rendszer létrehozza a feladatot a gyermek runbook. A másik mód, amelyet a parancsfájl a *WAIT* paraméter megadásával engedélyez, a parancsmag addig vár, amíg a gyermek feladata be nem fejeződik, és visszaadja a gyermek runbook kimenetét.

A-parancsmaggal elindított runbook-feladatok a szülő runbook-feladatoktól függetlenül futnak. Ez a viselkedés több feladatot eredményez, mint a runbook inline elindítása, és nehezebbé teszi a feladatok nyomon követését. A szülő több alárendelt runbook is indíthat aszinkron módon anélkül, hogy az egyes műveletek befejezésére kellene várnia. Ahhoz, hogy ez a párhuzamos végrehajtás meghívja a alárendelt runbookok, a szülő runbook a [Parallel kulcsszót](automation-powershell-workflow.md#parallel-processing)kell használnia.

A gyermek runbook kimenete az időzítés miatt nem megbízhatóan tér vissza a szülő runbook. Emellett előfordulhat, hogy a változók (például *$VerbosePreference*, *$WarningPreference*és mások) nem lesznek propagálva a gyermek runbookok. Ezen problémák elkerülése érdekében a **Start-AzAutomationRunbook** és a *WAIT* paraméter használatával elindíthatja a gyermek runbookok külön Automation-feladatként. Ez a módszer megakadályozza a szülő runbook, amíg a gyermek runbook be nem fejeződik.

Ha nem szeretné, hogy a szülő runbook le legyen tiltva a várakozási sorban, a **Start-AzAutomationRunbook** paranccsal elindíthatja a gyermek Runbook a *WAIT* paraméter nélkül. Ebben az esetben a runbook a [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) használatával kell várnia a feladatok befejezését. A [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) és a [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) használatával is le kell kérni az eredményeket.

Egy parancsmaggal elindított alárendelt runbook paramétereinek megadása szórótábla, a [runbook paraméterben](start-runbooks.md#runbook-parameters)leírtak szerint. Csak az egyszerű adattípusokat lehet használni. Ha a runbook rendelkezik összetett adattípusú paraméterrel, majd, beágyazottan kell meghívni.

Előfordulhat, hogy az előfizetési környezet elvész, amikor elindítják a gyermek runbookok különálló feladatként. Ahhoz, hogy a gyermek runbook az az Module-parancsmagok egy adott Azure-előfizetéssel való végrehajtásához, a gyermeknek a szülő runbook függetlenül hitelesítenie kell ezt az előfizetést.

Ha ugyanabban az Automation-fiókban lévő feladatok egynél több előfizetéssel működnek, az egyik feladatban szereplő előfizetés kiválasztásával megváltoztathatja a jelenleg kijelölt előfizetési környezetet más feladatokhoz. Ha el szeretné kerülni ezt a helyzetet, használja a `Disable-AzContextAutosave –Scope Process`t az egyes runbook elején. Ez a művelet csak a környezetet menti a runbook végrehajtásához.

### <a name="example"></a>Példa

A következő példa egy gyermek runbook indít el paraméterekkel, majd megvárja, amíg a **Start-AzAutomationRunbook** parancsmagot a *WAIT* paraméterrel elvégezte. Ha elkészült, a példa a gyermek runbook származó parancsmag kimenetét gyűjti. A **Start-AzAutomationRunbook**használatához a szkriptnek hitelesítenie kell magát az Azure-előfizetésében.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Gyermek runbook meghívására szolgáló metódusok összehasonlítása

A következő táblázat összefoglalja a két módszer közötti különbséget a runbook másik runbook való meghívásához.

|  | Beágyazott | Parancsmag |
|:--- |:--- |:--- |
| Feladat |Gyermek runbookok a a szülővel azonos feladatban futnak. |A gyermek runbook egy külön feladat jön létre. |
| Futtatási |Szülőrunbook megvárja, amíg a gyermekrunbook befejeződjön a folytatás előtt. |A szülő runbook közvetlenül a gyermek runbook elindítása után folytatódik, *vagy* a szülő runbook megvárja, amíg befejeződik a gyermek feladata. |
| Kimenet |Szülőrunbook közvetlenül lekérheti a kimeneti gyermekrunbook. |A szülő runbook le kell kérnie a gyermek runbook-feladatokból származó kimenetet *, vagy* a szülő runbook közvetlenül a gyermek runbook származó kimenetet kaphat. |
| Paraméterek |A gyermekrunbook paramétereinek értékeit külön kell meghatározni, és bármilyen adattípus használható. |A gyermek runbook paramétereinek értékeit egyetlen szórótábla kell egyesíteni. Ez a szórótábla csak olyan egyszerű, tömb és objektum típusú adattípusokat tartalmazhat, amelyek JSON-szerializálást használnak. |
| Automation-fiók |A szülő runbook csak azonos Automation-fiókban használhatja a gyermek runbook. |A szülő runbookok bármely Automation-fiókból, ugyanabból az Azure-előfizetésből, illetve egy másik előfizetésből származó gyermek runbook is használhat, amelyhez kapcsolódik. |
| Közzététel |Gyermek runbook közzé kell tenni, mielőtt a szülőrunbook közzététele. |A gyermek runbook a szülő runbook elindítása előtt bármikor közzé lesz téve. |

## <a name="next-steps"></a>Következő lépések

* [Runbook indítása Azure Automation](start-runbooks.md)
* [Runbook kimenete és üzenetei Azure Automation](automation-runbook-output-and-messages.md)