---
title: Gyermek runbookok az Azure Automationben
description: Az Azure Automation runbook indítása másik runbookból történő, és közöttük megosztására szolgáló különböző módszereket ismertet.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/14/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 037c2714d146bd59b30573df874794342d743e03
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782232"
---
# <a name="child-runbooks-in-azure-automation"></a>Gyermek runbookok az Azure Automationben

Az Azure Automationben egy más runbookok által használható önálló funkcióval újrafelhasználható, moduláris runbookok írása a legjobb legyen. A szülő runbook szülőrunbookok gyakran hívnak meg egy vagy több gyermekrunbookot a kívánt funkció végrehajtásához. Gyermekrunbookok meghívása kétféleképpen, és meg kell értenie közötti különbségeket, hogy meghatározhatja, amely a különböző helyzetekhez ajánlott lesz.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Beágyazott Futtatás használatával gyermekrunbook indítása

Egy másik runbookból történő beágyazott runbookot meghívni, a runbook nevét használja, és adja meg a paraméterek értékeit ugyanúgy, egy tevékenységet vagy egy parancsmagot használna.  Az azonos Automation-fiókot az összes runbook minden más ily módon használható érhetők el. A szülőrunbook megvárja a gyermekrunbook befejeződjön, mielőtt a következő sorra lép, és minden kimenetet közvetlenül a szülő visszaadja.

Egy beágyazott runbookot indít el, ha ugyanazt a feladatot, mint a szülő runbook futtatja. A feladatelőzmények nem jelenik meg annak feladatelőzményeiben a gyermekrunbook lesz. Kivételeket és adatfolyam-kimeneteket a gyermekrunbooktól érkező lesz a szülőhöz társítja. Ez kevesebb feladatot eredményez, és megkönnyíti a nyomon követésére, és elháríthatja a gyermekrunbook összes kivételét óta, és a stream kimenetétől bármelyike a szülőfeladat társítva.

Amikor egy runbook közzététele runbookok általuk meghívott gyermekrunbookoknak már közzé kell tenni. Ennek az oka az Azure Automation runbookok társítást hoz létre, amikor egy runbook fordítását. Ellenkező esetben a szülőrunbook közzététele sikeres fog megjelenni, de kivételt hoz létre, amikor elindul. Ha ez történik, a szülő runbook újbóli megfelelő hivatkozást a gyermekrunbookokra. Nem kell a szülő runbook ismételt közzététele, ha a runbookok bármelyikét változnak, mivel a társítás már létrejön.

A beágyazottan meghívott gyermekrunbookok paraméterei bármilyen adattípus többek között összetett objektumok is lehet, és nincs nincs [JSON-szerializálás](automation-starting-a-runbook.md#runbook-parameters) , amikor a runbook, az Azure portal használatával, vagy az a Start-AzureRmAutomationRunbook parancsmag.

### <a name="runbook-types"></a>Runbook-típusok

Milyen típusú meghívhatja egymást:

* A [PowerShell-forgatókönyv](automation-runbook-types.md#powershell-runbooks) és [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) meghívhatja a minden más beágyazott (mindkettő PowerShell-alapú).
* A [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks) és a grafikus PowerShell-munkafolyamati runbookok segítségével meghívhatja a minden más beágyazott (mindkettő PowerShell munkafolyamat alapú)
* A PowerShell és a PowerShell-munkafolyamat típusainak egymással beágyazott nelze volat, és a Start-AzureRmAutomationRunbook kell használnia.

Ha a rendelés függetlenül attól, hogy közzététele:

* A runbookok Közzététel sorrendje csak a PowerShell-munkafolyamat és a grafikus PowerShell-munkafolyamati runbookok fontos szempont.

Beágyazott Futtatás használatával grafikus vagy a PowerShell-munkafolyamat gyermekrunbookok meghívása, csak a runbook nevét használja.  Ha a PowerShell gyermekrunbookok meghívása, el kell indítania a neve *.\\*  megadásához, hogy a parancsfájl a helyi könyvtárban található.

### <a name="example"></a>Példa

Az alábbi példa egy teszt gyermekrunbook, amely három paramétert, egy összetett objektumot, egy egész számot és egy logikai hív meg. A gyermekrunbook kimenetét egy változóban van hozzárendelve.  Ebben az esetben a gyermek runbooknak egy PowerShell-munkafolyamati forgatókönyv.

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

Használhatja a [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) elindít egy runbookot, a parancsmag [runbook indítása a Windows PowerShell-lel](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Nincsenek használati parancsmag két módot.  Egy mód a parancsmag ad vissza, a feladat azonosítója, amint a gyermekrunbook a gyermek feladat jön létre.  A más módban, amely engedélyezi a megadásával a **-várjon** paramétert, a parancsmag megvárja a gyermek feladat befejeződik, és a gyermekrunbooktól érkező a hibaüzenettel reagál.

A parancsmaggal indított gyermekrunbook feladat egy külön feladat fut, a szülő runbook. Ez több feladatot eredményez, mint a meghívása a runbook soron belüli és követését teszi őket. A szülő aszinkron módon elindíthat több gyermekrunbookot egyes végrehajtásához várakozás nélkül. Az adott azonos típusú párhuzamos futtatáshoz, hívása a gyermekrunbookokat, a szülő runbook kell alkalmaznia a [parallel kulcsszót](automation-powershell-workflow.md#parallel-processing).

A runbookok kimenetét, a szülő runbook megbízhatóan időzítési miatt nem adja vissza. Is bizonyos változókat, például $VerbosePreference, $WarningPreference, és mások előfordulhat, hogy nem lesznek továbbítva a gyermekrunbookokra. Annak érdekében, hogy ezeket a problémákat, hívhat a runbookok használatával külön automatizálási feladatok, a `Start-AzureRmAutomationRunbook` parancsmagot a `-Wait` váltani. Ez a beállítás megakadályozza a szülő runbook a gyermekrunbook befejezéséig.

Ha nem szeretné, a szülő runbook Várakozás a zárolás, a gyermek runbook használatával hívhat `Start-AzureRmAutomationRunbook` nélkül parancsmag a `-Wait` váltani. Ezután kell használni `Get-AzureRmAutomationJob` való várakozás a feladat befejezésének megvárását, és `Get-AzureRmAutomationJobOutput` és `Get-AzureRmAutomationJobOutputRecord` az eredmények lekéréséhez.

A parancsmaggal indított gyermekrunbook paramétereinek megadott egy kivonattáblát leírtak szerint [Runbook paraméterek](automation-starting-a-runbook.md#runbook-parameters). Csak egyszerű adattípusok használhatók. Ha a runbook rendelkezik összetett adattípusú paraméterrel, majd, beágyazottan kell meghívni.

Gyermek runbookok külön feladatként meghívásakor az előfizetési környezet elveszhetnek. Ahhoz, hogy a gyermekrunbook meghívása az Azure RM-parancsmagok egy kívánt Azure-előfizetésen a gyermek runbook ehhez az előfizetéshez, függetlenül a szülő runbook hitelesíteni kell.

Ha a feladatok ugyanazt az Automation-fiókon belül több előfizetés használata során adott előfizetést kiválasztva egy feladat változhat az aktuálisan kiválasztott előfizetési környezetet, más feladatok is, amelyek normál esetben nem megfelelő. Annak érdekében, hogy a probléma elkerülése érdekében mentse a eredményét a `Select-AzureRmSubscription` parancsmag meghívása, és adja ezt az objektumot a `DefaultProfile` paraméterében minden a későbbi Azure erőforrás-kezelő parancsmagok meghívásához. Ez a minta konzisztens módon kell alkalmazni az Automation-fiókban futó minden runbookot.

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

|  | Beágyazott | Parancsmag |
|:--- |:--- |:--- |
| Feladat |Gyermek runbookok a a szülővel azonos feladatban futnak. |A gyermek runbook egy külön feladat jön létre. |
| Futtatási |Szülőrunbook megvárja, amíg a gyermekrunbook befejeződjön a folytatás előtt. |Szülő runbook továbbra is fennáll, azonnal a gyermekrunbook indítását követően *vagy* szülőrunbook megvárja, amíg a gyermek feladat befejeződésére. |
| Kimenet |Szülőrunbook közvetlenül lekérheti a kimeneti gyermekrunbook. |Szülő runbook kell lekérnie a kimenetet a szülőrunbooknak a gyermekrunbook *vagy* szülőrunbook közvetlenül lekérheti a kimeneti gyermekrunbook. |
| Paraméterek |A gyermekrunbook paramétereinek értékeit külön kell meghatározni, és bármilyen adattípus használható. |Értékek esetében a gyermekrunbook paramétereinek egyesíthetők kell egyetlen kivonattáblába, és csak egyszerű, a tömb és az objektum adattípusok, hogy használja ki a JSON-szerializálást. |
| Automation-fiók |Szülő runbook gyermek runbook csak használhatja ugyanazt az automation-fiókban. |Szülő runbook is használhatja a gyermekrunbook összes automation-fiók az Azure-előfizetéshez, és még egy másik előfizetésben, ha egy kapcsolat van. |
| Közzététel |Gyermek runbook közzé kell tenni, mielőtt a szülőrunbook közzététele. |A gyermekrunbook bármikor a szülőrunbook indítása előtt közzé kell tenni. |

## <a name="next-steps"></a>További lépések

* [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)
* [Runbook-kimenet és üzenetek az Azure Automationben](automation-runbook-output-and-messages.md)
