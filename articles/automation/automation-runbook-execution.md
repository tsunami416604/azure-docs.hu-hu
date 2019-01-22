---
title: Runbook végrehajtása az Azure Automationben
description: Ismerteti, hogyan dolgozza fel az Azure Automation-runbook részletei.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/30/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 859ef4c28b858b00fcc9c7c73a3a706a11225113
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430721"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook végrehajtása az Azure Automationben

Ha az Azure Automationben elindít egy runbookot, létrejön egy feladat. A feladat a runbook egyszeri futtatási példánya. Egy Azure Automation-feldolgozó rendelt minden egyes feladat futtatása. Feldolgozók számos Azure-fiókok által megosztott, miközben másik Automation-fiókok feladatok különítve egymástól. Melyik worker keresztül services, a kérés a feladat nem irányítás. Egyetlen runbook fut egyszerre sok feladat lehet. A feladatok ugyanazt az Automation-fiók végrehajtási környezetének felhasználhatók. Egyidejűleg futtatja a feladatot, a gyakrabban azok elirányíthatók az azonos tesztkörnyezetben. A védőfal ugyanabban a folyamatban futó feladatok hatással lehetnek egymással, például fut-e a `Disconnect-AzureRMAccount` parancsmagot. Futtatja ezt a parancsmagot lenne válassza le a megosztott védőfalfolyamat minden egyes forgatókönyv-feladatot. A runbookok listája az Azure Portalon történő megtekintésekor az összes minden runbook elindított feladatok állapotának sorolja fel. Megtekintheti a feladatok minden runbook minden egyes állapotának nyomon követését. Legfeljebb 30 napig feladatnaplóit tárolódnak. A különböző feladatok állapotai leírását [feladatállapotok](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Az alábbi ábrán látható, a runbook-feladat életciklusa [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) és [PowerShell-munkafolyamati runbookok](automation-runbook-types.md#powershell-workflow-runbooks).

![Feladat állapota – PowerShell-munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

Az alábbi ábrán látható, a runbook-feladat életciklusa [PowerShell-forgatókönyvek](automation-runbook-types.md#powershell-runbooks).

![Feladat állapota – PowerShell-parancsfájl](./media/automation-runbook-execution/job-statuses-script.png)

A feladatok az Azure-erőforrások elérését azáltal, hogy az Azure-előfizetéshez kapcsolódnia kell. Csak rendelkeznek erőforrásokhoz való hozzáférés az adatközpontban, ha ezek az erőforrások elérhetők a nyilvános felhőben.

## <a name="job-statuses"></a>Feladatállapotok

Az alábbi táblázat bemutatja a feladatok különféle lehetséges állapotait. PowerShell kétféle típusú hibák megszakítást nem okozó hibákat tartalmaz. Megszakítást okozó hiba a runbook állapotának beállítása **sikertelen** Ha. Nem okozó még a bekövetkezésük után folytatja a parancsfájl engedélyezi. Egy nem megszakító hiba például használja a `Get-ChildItem` parancsmag és egy elérési út nem létezik. PowerShell látja, hogy az elérési út nem létezik, hibát jelez és továbbra is fennáll, a következő mappát. Ez a hiba nem a runbook állapotának beállítása **sikertelen** és lehetett megjelölni **befejezve**. Használhatja egy runbookot, hogy állítsa le az egy nem megszakító hibát kényszerítéséhez `-ErrorAction Stop` a parancsmagról.

| status | Leírás |
|:--- |:--- |
| Befejezve |A feladat sikeresen befejeződött. |
| Meghiúsult |A [grafikus és a PowerShell-munkafolyamati runbookok](automation-runbook-types.md), a runbook fordítása sikertelen. A [PowerShell-parancsprogram runbookok](automation-runbook-types.md), a runbook nem indult el, vagy a feladat kivétel történt. |
| Sikertelen volt a Várakozás az erőforrásokat, |A feladat sikertelen volt, mert elérte a [igazságos elosztás](#fair-share) háromszor korlátozhatja, és az azonos ellenőrzőpont vagy kezdete a forgatókönyv és minden alkalommal, amikor indított. |
| Várakozási sorba helyezve |A feladat arra vár, hogy egy automatizálási feldolgozó erőforrásai felszabaduljanak, és elindulhasson. |
| Indítás |A feladat hozzá lett rendelve egy feldolgozóhoz, és a rendszer elindítja. |
| Folytatás |A rendszer folytatja a feladat felfüggesztését követően annak. |
| Fut |A feladat fut. |
| Futó erőforrások vár |A feladat lett távolítva a memóriából, mert elérte a [igazságos elosztás](#fair-share) korlátot. Folytatja a hamarosan az utolsó ellenőrzőponttól. |
| Leállítva |A feladatot leállította a felhasználó, mielőtt az befejeződött volna. |
| Leállítás |A rendszer a feladat leállítása folyamatban van. |
| Felfüggesztve |A felhasználó, a rendszer vagy a runbook egy parancsa felfüggesztette a feladatot. Ha egy runbook nem tartalmaz egy ellenőrzőpontot, a runbook elejétől kezdődik. Ha ellenőrzőponttal rendelkezik, ez indítsa el újra, és az utolsó ellenőrzőponttól folytatni. A runbook csak akkor a rendszer által felfüggeszthető, ha kivétel lép fel. Alapértelmezés szerint ErrorActionPreference értéke **Folytatás**, tehát a feladat a hiba továbbra is működik. Ha ezt a preferenciaváltozót **leállítása**, majd a feladat felfüggeszti az hiba. Érvényes [grafikus és a PowerShell-munkafolyamati runbookok](automation-runbook-types.md) csak. |
| Felfüggesztés |A rendszer megpróbálja a feladat felfüggesztését a felhasználó a kérelmet. A runbook felfüggesztés előtt el kell érnie a következő ellenőrzőpontot. Ha már elhagyta az utolsó ellenőrzőpontot, majd a felfüggesztés előtt befejeződött. Érvényes [grafikus és a PowerShell-munkafolyamati runbookok](automation-runbook-types.md) csak. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Az Azure Portalról a feladat állapotának megtekintése

Megtekintheti az összes runbook-feladatok összesített állapotát, vagy egy adott runbook-feladat az Azure Portalon részletek feltárásához. A runbook állapota és a feladat feladatstreamek továbbítják a Log Analytics-munkaterület is konfigurálása integrációs is. A Log Analytics integrálásával kapcsolatos további információkért lásd: [feladat állapotát és a feladatstreamek automatizálást továbbítása a Log Analyticsbe](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Automation-runbook feladatok összegzése

Jobb oldalán a kijelölt Automation-fiókot, láthatja az összes, a runbook-feladatok alatt **feladatstatisztika** csempére.

![Feladatstatisztika csempe](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ez a csempe és az összes feladat végrehajtása a feladat állapotának grafikus ábrázolását jeleníti meg.

Megadja a csempére kattintva a **feladatok** oldal, amely tartalmazza az összes feladat végrehajtása összefoglaló listája. Ezen a lapon látható az állapot, a kezdési idejének és a befejezési idők.

![Automation-fiók feladatok oldalon](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Kiválasztásával szűrheti a feladatok **feladatok szűrése** és szűrő egy adott runbookot, a feladat állapotát, vagy a legördülő listából válassza ki, és az időtartományt, amelyben keresni kíván.

![Szűrési feladat állapota](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Azt is megteheti, megtekintheti a feladat összegző részleteit egy adott runbook a runbook kiválasztásával a **Runbookok** lapon az Automation-fiókban, és válassza ki a **feladatok** csempére. Ez a művelet megadja a **feladatok** lapot, és onnan kattintva megtekintheti azok részleteit és kimenetét a feladat rekord.

![Automation-fiók feladatok oldalon](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Feladat összegzése

Megtekintheti az összes feladat egy adott runbookhoz és azok állapotával létrehozott listáját. Szűrhet az ebben a listában, a feladat állapotát és a feladat utolsó módosításának időtartománya. A részletes információkat és a kimenet megtekintéséhez kattintson a feladat nevére. A feladat részletes nézetében megtalálhatók azon runbook-paraméterek értékei, amelyek meg lettek adva a feladathoz.

Az egy adott runbookhoz tartozó feladatok a következő lépésekkel tekinthetők meg.

1. Az Azure Portalon válassza ki a **Automation** , és válassza ki az Automation-fiók nevére.
2. Válassza ki a hub **Runbookok** majd a a **Runbookok** lapon válasszon ki egy runbookot a listából.
3. A kiválasztott runbook lapon kattintson a **feladatok** csempére.
4. Kattintson az egyik a feladatok a listában, és a runbook-feladat részletei lapon megtekintheti azok részleteit és kimenetét.

## <a name="retrieving-job-status-using-windows-powershell"></a>Windows PowerShell-lel feladatállapot lekérése

Használhatja a [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) lekérheti feladatokat, létrehozott egy runbookot és a egy adott feladat részleteit. Ha elindít egy runbookot a Windows PowerShell [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), majd visszaadja az eredményezett feladatot. Használat [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) beolvasni a feladat kimenetének.

Az alábbi Példaparancsok lekérni a minta-runbookhoz tartozó utolsó feladatot, és az állapotot, a forgatókönyv-paramétereket, és a feladat kimenetét a megadott értékek megjelenítése.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Az alábbi példa lekéri egy adott feladat kimenetét, és minden rekordot ad vissza. Abban az esetben egyet a rekordok kivétel történt a kivétel íródik érték helyett. Ez a viselkedés akkor hasznos, mint a kivételek biztosíthat további adatokat, amelyek esetleg nem lesz naplózva, általában kimeneti során.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>A tevékenységnaplóról beolvasása – részletek

Egyéb részleteket, például a személy vagy a fiókot, amelyet a runbook lekérhetők a tevékenységnapló esetében az automation-fiókot. A következő PowerShell-példa az utolsó felhasználó futtatja a runbookot, az adott biztosítja:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>Igazságos elosztás

Annak érdekében, hogy megoszthatják az erőforrásokat az összes runbook a felhőben, Azure Automation fog átmenetileg el vagy több mint három óra alatt futó összes feladat leállítása. A feladatok [PowerShell-alapú runbookok](automation-runbook-types.md#powershell-runbooks) és [Python runbookok](automation-runbook-types.md#python-runbooks) le lesznek állítva és nem indítják újra, és a feladat állapota leállítva.

A hosszan futó feladatok esetén ajánlott [hibrid runbook-feldolgozót](automation-hrw-run-runbooks.md#job-behavior) használni. Hibrid Runbook-feldolgozók igazságos elosztás nincs korlátozva, és a egy korlátozás nem rendelkezik a mennyi runbookot hajthat végre. A többi feladat [korlátok](../azure-subscription-service-limits.md#automation-limits) Azure próbakörnyezetbe lefordítja a és a hibrid Runbook-feldolgozók egyaránt érvényesek. Hibrid Runbook-feldolgozók a hozzáférésüket nem korlátozza a 3 óra igazságos elosztás korlátot, miközben runbookok futtatta rajtuk továbbra is támogatja a helyi infrastruktúra váratlan problémák újraindítási viselkedés kell kidolgozni.

Egy másik lehetőség, hogy a runbook optimalizálása gyermek runbookok használatával. Ha a runbook végighalad az erőforrások, például a több adatbázis, adatbázis-művelet számos ugyanannak a függvénynek helyezheti át a függvényt egy [gyermekrunbook](automation-child-runbooks.md) és adja neki a a [ Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) parancsmagot. Ezen gyermek runbookok mindegyike különálló folyamatban, egymással párhuzamosan fut, így csökkentik a szülő runbook futtatásának befejezéséhez szükséges teljes időt. Használhatja a [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) parancsmagot a runbook a feladat állapotának ellenőrzése minden gyermek, ha a művelet, amely a gyermekrunbook befejezése után kell elvégezni.

## <a name="next-steps"></a>További lépések

* A runbook indítása az Azure Automation segítségével különböző módszerekkel kapcsolatos további tudnivalókért lásd: [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)

