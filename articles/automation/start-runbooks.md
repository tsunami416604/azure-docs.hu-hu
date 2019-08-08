---
title: Runbook elindítása Azure Automation
description: Összefoglalja a különböző módszereket, amelyekkel elindíthat egy runbook a Azure Automationban, és részletesen ismerteti a Azure Portal és a Windows PowerShell használatát is.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a42ba0a385b73032e3d4045094bc88613016b849
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850346"
---
# <a name="start-a-runbook-in-azure-automation"></a>Runbook elindítása Azure Automation

A következő táblázat segítségével határozhatja meg, hogy miként indítható el a runbook olyan Azure Automation, amely leginkább megfelel az adott forgatókönyvnek. Ez a cikk részletesen ismerteti, hogyan kell runbook elindítani a Azure Portal és a Windows PowerShell használatával. Az egyéb módszerekkel kapcsolatos további információkat az alábbi hivatkozásokon keresztül elérhető egyéb dokumentációban találja.

| **Metódus** | **Jellemzőit** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>Legegyszerűbb módszer interaktív felhasználói felülettel.<br> <li>Egyszerű paraméter-értékeket biztosító űrlap.<br> <li>A feladatok állapotának egyszerűen nyomon követhető.<br> <li>Az Azure-bejelentkezéssel hitelesített hozzáférés. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Hívás a parancssorból Windows PowerShell-parancsmagokkal.<br> <li>Az automatizált megoldásokban több lépéssel is felvehető.<br> <li>A kérelem hitelesítése tanúsítvány vagy OAuth egyszerű felhasználói tag/szolgáltatásnév alapján történik.<br> <li>Egyszerű és összetett paramétereket adjon meg.<br> <li>Feladatok állapotának nyomon követése<br> <li>A PowerShell-parancsmagok támogatásához szükséges ügyfél. |
| [Azure Automation API](/rest/api/automation/) |<li>A legrugalmasabb módszer, de a legbonyolultabb is.<br> <li>Bármely olyan egyéni kód meghívása, amely HTTP-kéréseket tesz elérhetővé.<br> <li>A tanúsítványsal hitelesített kérelem vagy OAuth-felhasználó/egyszerű szolgáltatásnév.<br> <li>Egyszerű és összetett paramétereket adjon meg. *Ha az API használatával keres Python-runbook, a JSON-adattartalmat szerializálni kell.*<br> <li>Feladatok állapotának nyomon követése |
| [Webhooks](automation-webhooks.md) |<li>Runbook elindítása egyetlen HTTP-kérelemből.<br> <li>Hitelesítve a biztonsági jogkivonattal az URL-címben.<br> <li>Az ügyfél nem bírálhatja felül a webhook létrehozásakor megadott paramétereket. A Runbook egyetlen paramétert is meghatározhat, amely a HTTP-kérelem részleteivel van feltöltve.<br> <li>Nincs lehetőség a feladatok állapotának nyomon követésére webhook URL-címén keresztül. |
| [Válaszadás az Azure-riasztásra](../log-analytics/log-analytics-alerts.md) |<li>Runbook elindítása az Azure-riasztásra adott válaszként.<br> <li>Konfigurálja a webhookot a runbook, és hivatkozzon a riasztásra.<br> <li>Hitelesítve a biztonsági jogkivonattal az URL-címben. |
| [Ütemezés](automation-schedules.md) |<li>Runbook automatikus elindítása óránként, naponta, hetente vagy havonta.<br> <li>Az ütemtervet Azure Portal, PowerShell-parancsmagokkal vagy Azure API-val kezelheti.<br> <li>Adja meg az ütemtervhez használandó paramétereket. |
| [Másik Runbook](automation-child-runbooks.md) |<li>Runbook használata tevékenységként egy másik runbook.<br> <li>Több runbookok által használt funkciók esetében hasznos.<br> <li>Adja meg a gyermekobjektumok runbook, és használja a kimenetet a szülő runbook. |

Az alábbi ábrán részletesen ismertetjük a runbook életciklusában bekövetkezett lépéseket. A runbook különböző módokon indul el Azure Automation, mely összetevők szükségesek a hibrid Runbook-feldolgozók számára Azure Automation runbookok végrehajtásához és a különböző összetevők közötti interakcióhoz. Az automatizálási runbookok az adatközpontban való végrehajtásával kapcsolatban a [hibrid runbook](automation-hybrid-runbook-worker.md) -feldolgozók című témakörben talál további információt.

![Runbook architektúra](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Runbook elindítása a Azure Portal

1. A Azure Portal válassza az **Automation** lehetőséget, majd kattintson az Automation-fiók nevére.
2. A központi menüben válassza a **runbookok**lehetőséget.
3. A **runbookok** lapon válasszon ki egy runbook, majd kattintson az **Indítás**gombra.
4. Ha a runbook paraméterekkel rendelkezik, a rendszer felszólítja, hogy adjon meg értékeket az egyes paraméterekhez tartozó szövegmezővel. A paraméterekkel kapcsolatos további információkért lásd: [Runbook paraméterek](#runbook-parameters).
5. A **feladatok** lapon megtekintheti a runbook-feladatok állapotát.

## <a name="start-a-runbook-with-powershell"></a>Runbook elindítása a PowerShell-lel

A [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) használatával elindíthat egy Runbook a Windows PowerShell segítségével. Az alábbi mintakód egy test-Runbook nevű runbook indít el.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

A Start-AzureRmAutomationRunbook egy olyan feladatot ad vissza, amellyel nyomon követheti az állapotát a runbook elindítása után. Ezt követően a [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) használatával meghatározhatja a feladatok állapotát, és lekérheti a [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) a kimenetének lekéréséhez. Az alábbi mintakód elindítja a test-Runbook nevű runbook, megvárja, amíg befejeződik, majd megjeleníti a kimenetét.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Ha a runbook paramétereket igényel, akkor [szórótábla](https://technet.microsoft.com/library/hh847780.aspx)kell megadnia őket. A szórótábla kulcsának meg kell egyeznie a paraméter nevével, és az érték a paraméter értéke. Az alábbi példa bemutatja, hogyan indíthat el egy runbook két, FirstName és LastName nevű karakterlánc-paraméterrel, egy RepeatCount nevű egész számmal, valamint egy show nevű logikai paraméterrel. A paraméterekkel kapcsolatos további információkért lásd az alábbi [Runbook-paramétereket](#runbook-parameters) .

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook-paraméterek

Ha a Azure Portal vagy a Windows PowerShellből indít el egy runbook, a rendszer az utasítást a Azure Automation webszolgáltatáson keresztül továbbítja. Ez a szolgáltatás nem támogatja az összetett adattípusú paramétereket. Ha egy összetett paraméterhez értéket kell megadnia, akkor azt egy másik runbook kell meghívnia a [Azure Automation gyermek runbookok](automation-child-runbooks.md)című témakörben leírtak szerint.

A Azure Automation webszolgáltatás speciális funkciókat biztosít a bizonyos adattípusokat használó paraméterek számára, az alábbi szakaszokban leírtak szerint:

### <a name="named-values"></a>Megnevezett értékek

Ha a paraméter adattípusa [objektum], akkor a következő JSON formátummal küldhet neki egy névvel ellátott értékek listáját: *{Name1: "érték1", Name2: "érték2", név3: "érték3"}* . Ezeknek az értékeknek egyszerű típusúaknak kell lenniük. A runbook az egyes megnevezett értékeknek megfelelő tulajdonságokkal rendelkező [pscustomobject formájában kapja](/dotnet/api/system.management.automation.pscustomobject) fogadja a paramétert.

Vegye figyelembe a következő runbook, amely elfogad egy felhasználó nevű paramétert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

A User paraméterhez a következő szöveg használható.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Ez a következő kimenetet eredményezi:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tömbök

Ha a paraméter egy tömb, például [Array] vagy [string []], akkor a következő JSON formátummal küldheti el az értékek listáját: *[érték1, érték2, érték3]* . Ezeknek az értékeknek egyszerű típusúaknak kell lenniük.

Vegye figyelembe a következő runbook, amely elfogad egy *felhasználó*nevű paramétert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

A User paraméterhez a következő szöveg használható.

```input
["Joe","Smith",2,true]
```

Ez a következő kimenetet eredményezi:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Hitelesítő adatok

Ha a paraméter **PSCredential**adattípusú, akkor megadhatja egy Azure Automation [hitelesítőadat](automation-credentials.md)-objektum nevét. A runbook a megadott névvel kérdezi le a hitelesítő adatokat.

Vegye figyelembe a következő runbook, amely fogadja a hitelesítő adatok nevű paramétert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

A User paraméterhez a következő szöveg használható, feltételezve, hogy a hitelesítő adat " *My hitelesítőadat*" nevű hitelesítő eszköz volt.

```input
My Credential
```

Feltételezve, hogy a hitelesítő adatok felhasználóneve *jsmith*, a következő kimenetet eredményezi:

```output
jsmith
```

## <a name="next-steps"></a>További lépések

* A runbook architektúrája az Azure-ban és a helyszínen, a hibrid Runbook-feldolgozóval átfogó áttekintést nyújt a runbookok. Az automatizálási runbookok az adatközpontban való végrehajtásával kapcsolatos információkért tekintse meg a [hibrid Runbook](automation-hybrid-runbook-worker.md)-feldolgozók című témakört.
* Ha többet szeretne megtudni arról, hogy a moduláris runbookok hogyan használható más runbookok által meghatározott vagy gyakori függvények számára, tekintse meg a [gyermek runbookok](automation-child-runbooks.md).
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/en-us/powershell/scripting/overview).
