---
title: Gyermek runbookok az Azure Automationben
description: Az Azure Automation runbook indítása másik runbookból történő, és közöttük megosztására szolgáló különböző módszereket ismertet.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fc9e9cbf3e385a43eee8ce63d2f2fa6b863e95cc
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391354"
---
# <a name="child-runbooks-in-azure-automation"></a>Gyermek runbookok az Azure Automationben

Egy Azure Automation és a egy különálló funkciót, amely más runbookok által újrafelhasználható, moduláris runbookok írása az ajánlott eljárás. A szülő runbook gyakran meghív egy vagy több gyermekrunbookot a kívánt funkció végrehajtásához. Gyermekrunbookok meghívása kétféleképpen, és meg kell értenie közötti különbségeket, hogy a segítségével meghatározhatja, hogy ez az ajánlott a különböző helyzetekhez.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Beágyazott Futtatás használatával gyermekrunbook indítása

Egy másik runbookból történő beágyazott runbookot meghívni, a runbook nevét használja, és adja meg a paraméterek értékeit ugyanúgy, egy tevékenységet vagy egy parancsmagot használna.  Az azonos Automation-fiókot az összes runbook minden más ily módon használható érhetők el. A szülőrunbook megvárja a gyermekrunbook befejeződjön, mielőtt a következő sorra lép, és minden kimenetet közvetlenül a szülő visszaadja.

Egy beágyazott runbookot indít el, ha ugyanazt a feladatot, mint a szülő runbook futtatja. A rendszer nem jelzi a feladatelőzményekben a gyermek runbook meg annak feladatelőzményeiben. A szülő kivételeket és adatfolyam-kimeneteket a gyermekrunbooktól érkező kapcsolódik. Ez a viselkedés kevesebb feladatot eredményez, és megkönnyíti a nyomon követésére, és elháríthatja a gyermekrunbook összes kivételét óta, és a stream kimenetétől bármelyike a szülőfeladat társítva.

Amikor egy runbook közzététele runbookok általuk meghívott gyermekrunbookoknak már közzé kell tenni. Ennek az oka az Azure Automation runbookok társítást hoz létre, amikor egy runbook fordítását. Ellenkező esetben a szülőrunbook közzététele sikeres megjelenik, de kivételt hoz létre, amikor elindul. Ha ez történik, a szülő runbook megfelelő hivatkozást a gyermekrunbookokra újbóli. Nem kell a szülő runbook ismételt közzététele, ha a runbookok bármelyikét változnak, mivel a társítás már létrejött.

A beágyazottan meghívott gyermekrunbookok paraméterei bármilyen adattípus többek között összetett objektumok lehetnek. Nincs nem [JSON-szerializálás](automation-starting-a-runbook.md#runbook-parameters) , amikor a runbook, az Azure portal használatával, vagy a Start-AzureRmAutomationRunbook parancsmag.

### <a name="runbook-types"></a>Runbook-típusok

Milyen típusú meghívhatja egymást:

* A [PowerShell-forgatókönyv](automation-runbook-types.md#powershell-runbooks) és [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) meghívhatja a minden más beágyazott (mindkettő PowerShell-alapú).
* A [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks) és a grafikus PowerShell-munkafolyamati runbookok segítségével meghívhatja a minden más beágyazott (mindkettő PowerShell munkafolyamat alapú)
* A PowerShell és a PowerShell-munkafolyamat típusainak egymással beágyazott nelze volat, és a Start-AzureRmAutomationRunbook kell használnia.

Ha a rendelés függetlenül attól, hogy közzététele:

* A runbookok Közzététel sorrendje csak a PowerShell-munkafolyamat és a grafikus PowerShell-munkafolyamati runbookok fontos szempont.

Beágyazott Futtatás használatával grafikus vagy a PowerShell-munkafolyamat gyermekrunbookok meghívása, amikor a runbook a nevet használja.  Ha a PowerShell gyermekrunbookok meghívása, el kell indítania a neve *.\\*  megadásához, hogy a parancsfájl a helyi könyvtárban található.

### <a name="example"></a>Példa

A következő példa elindítja egy teszt gyermekrunbook, amely három paramétert, egy összetett objektumot, egy egész számot és egy logikai értéket fogad el. A gyermekrunbook kimenetét egy változóban van hozzárendelve.  Ebben az esetben a gyermek runbooknak egy PowerShell-munkafolyamati forgatókönyv.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Következő ugyanebben a példában egy PowerShell-forgatókönyvet használja, mint a gyermek.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>A parancsmag használatával gyermekrunbook indítása

> [!IMPORTANT]
> Ha egy gyermek runbookot a meghívott a `Start-AzureRmAutomationRunbook` parancsmagot a `-Wait` kapcsoló- és az eredményeket a gyermek runbook egy olyan objektum, hibákba ütközhet. Tekintse meg a hiba elkerüléséhez [gyermek runbookok a kimeneti objektum](troubleshoot/runbooks.md#child-runbook-object) megtudhatja, hogyan lehet lekérdezni az eredményeket, és a logikát alkalmazzák a [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Használhatja a [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) elindít egy runbookot, a parancsmag [runbook indítása a Windows PowerShell-lel](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Nincsenek használati parancsmag két módot.  Egy módot a parancsmag adja vissza a feladatazonosító a gyermek feladat a gyermek runbook létrehozását.  A más módban, amely engedélyezi a megadásával a **-várjon** paramétert, az a parancsmag megvárja, amíg a gyermek feladat befejeződik, és a kimenetet visszaadja a gyermekrunbooktól érkező.

A parancsmaggal indított gyermekrunbook feladat külön feladatban futtatja a szülő runbook. Ez a viselkedés több feladatot eredményez, mint a indítása a runbook soron belüli és követését teszi őket. A szülő aszinkron módon megkezdheti egynél több gyermek runbook egyes végrehajtásához várakozás nélkül. Az adott azonos típusú párhuzamos futtatáshoz, hívása a gyermekrunbookokat, a szülő runbook kell alkalmaznia a [parallel kulcsszót](automation-powershell-workflow.md#parallel-processing).

Gyermek runbookok kimenetét nem küld vissza a szülő runbook megbízhatóan időzítési miatt. Is bizonyos változókat, például $VerbosePreference, $WarningPreference, és mások előfordulhat, hogy nem lesznek továbbítva a gyermekrunbookokra. Ezek a problémák elkerülése érdekében elkezdheti a runbookok használatával külön automatizálási feladatok, a `Start-AzureRmAutomationRunbook` parancsmagot a `-Wait` váltani. Ez a beállítás megakadályozza a szülő runbook a gyermekrunbook befejezéséig.

Ha nem szeretné, a szülő runbook Várakozás a zárolás, megkezdheti a gyermek runbook `Start-AzureRmAutomationRunbook` nélkül parancsmag a `-Wait` váltani. Ezután kell használni `Get-AzureRmAutomationJob` való várakozás a feladat befejezésének megvárását, és `Get-AzureRmAutomationJobOutput` és `Get-AzureRmAutomationJobOutputRecord` az eredmények lekéréséhez.

A parancsmaggal indított gyermekrunbook paramétereinek megadott egy kivonattáblát leírtak szerint [Runbook paraméterek](automation-starting-a-runbook.md#runbook-parameters). Csak egyszerű adattípusok használhatók. Ha a runbook rendelkezik összetett adattípusú paraméterrel, majd, beágyazottan kell meghívni.

Gyermek runbookok külön feladat indításakor az előfizetési környezet elveszhetnek. Ahhoz, hogy a gyermekrunbook hajtsa végre az Azure RM-parancsmagok egy adott Azure előfizetésen a gyermek runbook ezt az előfizetést, függetlenül a szülő runbook hitelesíteni kell.

Ha ugyanazt az Automation-fiókon belül feladatok egynél több előfizetéssel, egy feladat az előfizetés kiválasztásával módosíthatja az aktuálisan kijelölt előfizetési környezetet, más feladatok. Ez a probléma elkerülése érdekében használja `Disable-AzureRmContextAutosave –Scope Processsave` minden runbook elején. Ez a művelet csak menti a környezetet, hogy a runbook végrehajtása.

### <a name="example"></a>Példa

Az alábbi példa egy gyermek runbookot indít paraméterekkel, és megvárja, amíg befejeződik a Start-AzureRmAutomationRunbook használatával-paramétert várja. Ha befejeződött, a kimenetét a gyermekrunbooktól érkező gyűjti. Használandó `Start-AzureRmAutomationRunbook`, hitelesítenie kell az Azure-előfizetéshez.

```azurepowershell-interactive
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
    -DefaultProfile $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>A gyermekrunbookok meghívására szolgáló módszerek összehasonlítása

Az alábbi táblázat a runbook meghívása egy másik runbookból szolgáló két módszer közötti különbségeket foglalja össze.

|  | Beágyazott | A parancsmag |
|:--- |:--- |:--- |
| Feladat |Gyermek runbookok a a szülővel azonos feladatban futnak. |A gyermek runbook egy külön feladat jön létre. |
| Végrehajtás |Szülőrunbook megvárja, amíg a gyermekrunbook befejeződjön a folytatás előtt. |Szülő runbook továbbra is fennáll, azonnal a gyermekrunbook indítását követően *vagy* szülőrunbook megvárja, amíg a gyermek feladat befejeződésére. |
| Kimenet |Szülőrunbook közvetlenül lekérheti a kimeneti gyermekrunbook. |Szülő runbook kell lekérnie a kimenetet a szülőrunbooknak a gyermekrunbook *vagy* szülőrunbook közvetlenül lekérheti a kimeneti gyermekrunbook. |
| Paraméterek |A gyermekrunbook paramétereinek értékeit külön kell meghatározni, és bármilyen adattípus használható. |A gyermekrunbook paramétereinek értékeit kell egyetlen kivonattáblába kombinálni. Ez a szórótábla csak egyszerű, a tömb és objektum JSON-szerializálás használó adattípusok is. |
| Automation-fiók |Szülő runbook gyermek runbook csak használhatja ugyanazt az automation-fiókban. |Szülő runbookok bármely automation-fiók az Azure-előfizetéshez, és még egy másik előfizetésbe való kapcsolattal rendelkező gyermekrunbook használhatja. |
| Közzététel |Gyermek runbook közzé kell tenni, mielőtt a szülőrunbook közzététele. |A gyermekrunbook bármikor előtt a szülő runbook elindult közzé kell tenni. |

## <a name="next-steps"></a>További lépések

* [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)
* [Runbook-kimenet és üzenetek az Azure Automationben](automation-runbook-output-and-messages.md)
