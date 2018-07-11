---
title: Runbook végrehajtása az Azure Automationben
description: Ismerteti, hogyan dolgozza fel az Azure Automation-runbook részletei.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4b9bfc0df01dd8fc8a6a1b7aed5ade466164a82f
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930052"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook végrehajtása az Azure Automationben

Ha az Azure Automationben elindít egy runbookot, létrejön egy feladat. Egy feladat a runbook egyszeri futtatási példánya. Egy Azure Automation-feldolgozó rendelt minden egyes feladat futtatása. Feldolgozók több Azure-fiókok által megosztott, miközben másik Automation-fiókok feladatok különítve egymástól. Nem irányítás keresztül melyik worker services, a kérés a feladat. Egyetlen runbook fut egyszerre több feladattal rendelkezhet. A feladatok ugyanazt az Automation-fiók végrehajtási környezetének felhasználhatók. A runbookok listája az Azure Portalon történő megtekintésekor az összes minden runbook elindított feladatok állapotának sorolja fel. A feladatok minden runbook megtekintheti annak érdekében, hogy az egyes állapotának nyomon követését. A különböző feladatok állapotai leírását [feladatállapotok](#job-statuses).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

Az alábbi ábrán látható, a runbook-feladat életciklusa [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) és [PowerShell-munkafolyamati runbookok](automation-runbook-types.md#powershell-workflow-runbooks).

![Feladat állapota – PowerShell-munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

Az alábbi ábrán látható, a runbook-feladat életciklusa [PowerShell-forgatókönyvek](automation-runbook-types.md#powershell-runbooks).

![Feladat állapota – PowerShell-parancsfájl](./media/automation-runbook-execution/job-statuses-script.png)

A feladatok az Azure-erőforrások elérését azáltal, hogy az Azure-előfizetéshez kapcsolódnia kell. Csak rendelkeznek erőforrásokhoz való hozzáférés az adatközpontban, ha ezek az erőforrások elérhetők a nyilvános felhőben.

## <a name="job-statuses"></a>Feladatállapotok

A következő táblázat ismerteti a különféle lehetséges állapotait feladat.

| status | Leírás |
|:--- |:--- |
| Befejeződött |A feladat sikeresen befejeződött. |
| Meghiúsult |A [grafikus és a PowerShell-munkafolyamati runbookok](automation-runbook-types.md), a runbook fordítása sikertelen. A [PowerShell-parancsprogram runbookok](automation-runbook-types.md), a runbook nem indult el, vagy a feladat kivételt észlelt. |
| Sikertelen volt a Várakozás az erőforrásokat, |A feladat sikertelen volt, mert elérte a [igazságos elosztás](#fair-share) háromszor korlátozhatja, és az azonos ellenőrzőpont vagy kezdete a forgatókönyv és minden alkalommal, amikor indított. |
| Várakozási sorba helyezve |A feladat felszabaduljanak, amely segítségével elindítható, hogy egy automatizálási feldolgozó erőforrások vár. |
| Indítás |A feladat hozzá lett rendelve egy feldolgozóhoz, és a rendszer az elindítás folyamatban van. |
| Folytatás |A folytatása a feladat felfüggesztését követően annak folyamatban van. |
| Fut |A feladat fut. |
| Futó erőforrások vár |A feladat lett távolítva a memóriából, mert elérte a [igazságos elosztás](#fair-share) korlátot. Folytatja a hamarosan az utolsó ellenőrzőponttól. |
| Leállítva |A feladatot leállította a felhasználó, mielőtt befejeződhetett volna. |
| Leállítás |A rendszer a feladat leállítása folyamatban van. |
| Felfüggesztve |A felhasználó által, a rendszer vagy a runbook egy parancsa felfüggesztette a feladatot. Egy feladatot, amely fel van függesztve, újra lehet indítani, és az utolsó ellenőrzőponttól vagy a runbook elejétől folytatódik, ha nem az ellenőrzőpontok. A runbook csak akkor a rendszer által felfüggeszthető, ha kivétel lép fel. Alapértelmezés szerint ErrorActionPreference értéke **Folytatás**, tehát a feladat a hiba továbbra is működik. Ha ezt a preferenciaváltozót **leállítása**, majd a feladat felfüggeszti az hiba. Érvényes [grafikus és a PowerShell-munkafolyamati runbookok](automation-runbook-types.md) csak. |
| Felfüggesztés |A rendszer megkísérli a feladat felfüggesztését a felhasználó a kérelmet. A runbook felfüggesztés előtt el kell érnie a következő ellenőrzőpontot. Ha már elhagyta az utolsó ellenőrzőpontot, majd a felfüggesztés előtt befejeződött. Érvényes [grafikus és a PowerShell-munkafolyamati runbookok](automation-runbook-types.md) csak. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Az Azure Portalról a feladat állapotának megtekintése

Megtekintheti az összes runbook-feladatok összesített állapotát, vagy egy adott runbook-feladat az Azure Portalon és integráció konfigurálásával a Log Analytics-munkaterület továbbítják a runbook állapota és a feladat feladatstreamek részletek feltárásához. A Log Analytics integrálásával kapcsolatos további információkért lásd: [feladat állapotát és a feladatstreamek automatizálást továbbítása a Log Analyticsbe](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Automation-runbook feladatok összegzése

A kijelölt Automation-fiók a jobb oldalon látható összes a runbook-feladatok számára kijelölt Automation-fiók alatt **feladatstatisztika** csempére.

![Feladatstatisztika csempe](./media/automation-runbook-execution/automation-account-job-status-summary.png).

Ez a csempe és az összes feladat végrehajtása a feladat állapotának grafikus ábrázolását jeleníti meg.

Megadja a csempére kattintva a **feladatok** végrehajtott összes feladat, az állapot, a feladat végrehajtása és a kezdési és befejezési időpontjának összesített listáját tartalmazó panelen.

![Automation-fiók feladatok panelje](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Kiválasztásával szűrheti a feladatok **feladatok szűrése** és szűrő egy adott runbookot, a feladat állapotát, vagy a legördülő listából, a dátum/idő tartomány, amelyben keresni kíván.

![Szűrési feladat állapota](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Azt is megteheti, megtekintheti a feladat összegző részleteit egy adott runbook a runbook kiválasztásával a **Runbookok** panel az Automation-fiókot, és válassza ki a **feladatok** csempére. Ez a **feladatok** panelen, és onnan kattintva megtekintheti azok részleteit és kimenetét a feladat rekord.

![Automation-fiók feladatok panelje](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Feladat összegzése

Megtekintheti az összes feladat egy adott runbookhoz és azok állapotával létrehozott listáját. Szűrhet az ebben a listában, a feladat állapotát és a feladat utolsó módosításának időtartománya. A részletes információkat és a kimenet megtekintéséhez kattintson a feladat nevére. A feladat részletes nézetében a runbook-paraméterek, amelyeket a feladathoz az értékeket tartalmaz.

A következő lépések segítségével a runbook-feladatok megtekintése.

1. Az Azure Portalon válassza ki a **Automation** , és válassza ki az Automation-fiók nevére.
2. Válassza ki a hub **Runbookok** majd a a **Runbookok** panelen válasszon ki egy runbookot a listából.
3. A kiválasztott runbook paneljén kattintson a **feladatok** csempére.
4. Kattintson az egyik a feladatok a listában, és a runbook feladat részletei panelen megtekintheti azok részleteit és kimenetét.

## <a name="retrieving-job-status-using-windows-powershell"></a>Windows PowerShell-lel feladatállapot lekérése

Használhatja a [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) lekérheti feladatokat, létrehozott egy runbookot és a egy adott feladat részleteit. Ha elindít egy runbookot a Windows PowerShell [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), majd visszaadja az eredményezett feladatot. Használat [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)kimeneti beolvasni a feladat kimenetének.

Az alábbi Példaparancsok lekérni a minta-runbookhoz tartozó utolsó feladatot, és az állapotot, a forgatókönyv-paramétereket, és a feladat kimenetét a megadott értékek megjelenítése.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Az alábbi példa lekéri egy adott feladat kimenetét, és minden rekordot ad vissza. Abban az esetben egyet a rekordok kivétel történt a kivétel íródik érték helyett. Ez akkor hasznos, mint a kivételek az további információkkal, amelyek esetleg nem lesz naplózva, általában kimeneti során.

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

Annak érdekében, hogy megoszthatják az erőforrásokat az összes runbook a felhőben, Azure Automation átmenetileg el lesz minden olyan feladat után három óráig futott. Ebben az időszakban feladatai [PowerShell-alapú runbookok](automation-runbook-types.md#powershell-runbooks) le lesz állítva, és a rendszer nem indítható újra. A feladat állapota látható **leállítva**. Az ilyen típusú runbook mindig újraindul az elejéről, mivel azok nem támogatják az ellenőrzőpontokat.

[PowerShell-munkafolyamat-alapú runbookok](automation-runbook-types.md#powershell-workflow-runbooks) újrakezdődnek az elmúlt [ellenőrzőpont](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints). Után három órán keresztül fut, a runbook-feladat fel van függesztve, a szolgáltatást és annak állapotát jeleníti meg **fut, a Várakozás az erőforrások**. Amikor elérhetővé válik egy tesztkörnyezet, a runbook az Automation szolgáltatást, és folytatja az utolsó ellenőrzőponttól automatikusan újraindul. Ez a normál PowerShell-munkafolyamat felfüggesztése/restart viselkedését. Ha a runbook újra nagyobb, mint három óra futtatókörnyezet, a folyamat ismétlődik, legfeljebb három alkalommal. A harmadik újraindításkor, ha a runbook még nem fejeződött be három óra, akkor a runbook-feladat nem sikerült, és a feladatok állapotát mutatja **sikertelen, Várakozás az erőforrások**. Ebben az esetben az a hiba a következő kivételt kap.

*A feladat fut, mert azt a ismételten fürtből az azonos ellenőrzőpont nem folytatható. Ellenőrizze, hogy a Runbook nem hajt végre műveletek végzésekor állapotában megőrzése nélkül.*

Ez a szolgáltatás szembeni nélkül, határozatlan ideig futó runbookok, mivel azok nem tudja, hogy a következő ellenőrzőpontot, anélkül, hogy újra eltávolítása folyamatban.

Ha a runbook rendelkezik nincsenek ellenőrzőpontok, vagy a feladat nem érte el a az első ellenőrzőpont folyamatban előtt, majd újraindul az elejétől.

A sokáig futó feladatokat, javasoljuk, hogy használjon egy [hibrid Runbook-feldolgozó](automation-hrw-run-runbooks.md#job-behavior). Hibrid Runbook-feldolgozók igazságos elosztás nincs korlátozva, és a egy korlátozás nem rendelkezik a mennyi runbookot hajthat végre.

Az Azure-ban, egy PowerShell-munkafolyamati forgatókönyv használ, amikor a runbook létrehozása, ha biztosítania kell, hogy a két ellenőrzőpontok közötti tevékenységeket futási időnek nem haladja meg a három óra. Szükség lehet az ellenőrzőpontokat hozzáadni a runbookhoz, győződjön meg arról, hogy azt nem eléri ezt a korlátot három óra vagy feloszthatja a hosszú futó műveletek. Például a runbook egy reindex előfordulhat, hogy végre nagy SQL-adatbázisba. Ha a egyetlen művelet nem fejeződik az igazságos elosztás időkorláton belül, majd a feladat a memóriából és indítani az elejétől. Ebben az esetben kell részekre a reindex művelet, például újraindexeléshez egy olyan táblát, egyszerre több lépésekre, és helyezze ellenőrzőpont után minden művelet, úgy, hogy a feladat sikerült folytatni a legutóbbi művelet befejezését követően.

## <a name="next-steps"></a>További lépések

* A runbook indítása az Azure Automation segítségével különböző módszerekkel kapcsolatos további tudnivalókért lásd: [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)
