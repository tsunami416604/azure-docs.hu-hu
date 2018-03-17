---
title: "Runbook elindítása az Azure Automationben"
description: "Összefoglalja a különböző módszereket, amelyek segítségével elindít egy forgatókönyvet az Azure Automation és részleteit az Azure portál és a Windows PowerShell használatával."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 064ba5f73b53681a824b1416243d10ab0e565c44
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="starting-a-runbook-in-azure-automation"></a>Runbook elindítása az Azure Automationben
Az alábbi táblázat segít meghatározni, a runbook indítása az Azure Automationben, amely az adott helyzetnek legmegfelelőbb módszer. Ez a cikk tartalmazza a runbook elindítása és az Azure portál és a Windows PowerShell használatával. Egyéb módszerekkel kapcsolatos részletek szerepelnek a lenti hivatkozásokra kattintva érheti el más dokumentációját.

| **METHOD** | **CHARACTERISTICS** |
| --- | --- |
| [Azure Portal](#starting-a-runbook-with-the-azure-portal) |<li>Az interaktív felhasználói kezelőfelület legegyszerűbb módja.<br> <li>Űrlap egyszerű paraméter értékének megadására.<br> <li>Feladat állapotát nyomon.<br> <li>Az Azure bejelentkezési hitelesített hozzáférést. |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) |<li>A Windows PowerShell-parancsmagokkal a parancssorból hívható.<br> <li>Több lépést automatizált megoldást szerepeljenek.<br> <li>A tanúsítvány vagy egyszerű / szolgáltatás OAuth felhasználói kérelem hitelesítésekor egyszerű.<br> <li>Adja meg egyszerű és összetett paraméter értékét.<br> <li>Feladat-állapotok nyomon követésére.<br> <li>Az ügyfél PowerShell-parancsmagok támogatásához szükséges. |
| [Azure Automation szolgáltatásbeli API](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>Legrugalmasabb módszer, de a legtöbb komplex is.<br> <li>Felelnek meg, és bármilyen egyéni kód használhat, amelyek HTTP-kérelmekre.<br> <li>A kérelem hitelesítése a tanúsítványt, vagy Oauth felhasználó egyszerű / szolgáltatás egyszerű.<br> <li>Adja meg egyszerű és összetett paraméter értékét. *Ha az API-val Python runbookot hív, a JSON-adattartalmat szerializálni kell.*<br> <li>Feladat-állapotok nyomon követésére. |
| [Webhooks](automation-webhooks.md) |<li>Indítsa el a runbook egyetlen HTTP-kérelemből.<br> <li>Felhasználók hitelesítése a biztonsági jogkivonat URL-címben.<br> <li>Ügyfél nem bírálhatja felül a webhook létrehozásakor megadott paraméterértékek. Runbook megadhat egyetlen paramétert, amely a HTTP-kérelem részletes adatait a telepítéskor.<br> <li>Nem képes a webhook URL-CÍMÉT a feladat-állapotok nyomon követésére. |
| [Az Azure riasztás válaszolni](../log-analytics/log-analytics-alerts.md) |<li>Elindít egy forgatókönyvet az Azure riasztás válasz.<br> <li>Konfigurálja webhook runbookhoz és riasztási mutató hivatkozást.<br> <li>Felhasználók hitelesítése a biztonsági jogkivonat URL-címben. |
| [Ütemezés](automation-schedules.md) |<li>Óránkénti, napi, heti vagy havi ütemezés szerint automatikusan elindítja a runbookot.<br> <li>Azure-portálon, a PowerShell-parancsmagok vagy az Azure API ütemezés módosítására.<br> <li>Adja meg az ütemezés használni kívánt paraméterértékeket. |
| [Egy másik runbookból](automation-child-runbooks.md) |<li>Egy runbook használata egy másik runbook egy tevékenységére.<br> <li>Akkor hasznos, ha több runbookok által használt funkciók.<br> <li>Adja meg a gyermekrunbook paraméter értékét, és kimeneti a szülőrunbook. |

Az alábbi képen egy runbook életciklusának részletes részletes folyamatát mutatja be. Ez magában foglalja a különböző módokon egy runbook indítását az Azure Automationben összetevők szükséges a hibrid forgatókönyv-feldolgozó Azure Automation-forgatókönyv és a különböző összetevők közötti interakció végrehajtásához. Automatizálási runbookok végrehajtása az adatközpontban található kapcsolatos információkért tekintse meg [hibrid forgatókönyv-feldolgozók](automation-hybrid-runbook-worker.md)

![Runbook-architektúra](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>Runbook elindítása és az Azure portál
1. Válassza ki az Azure-portálon **Automation** és kattintson az automation-fiók nevét.
2. A központ menüben válassza ki a **Runbookok**.
3. Az a **Runbookok** lapon válasszon ki egy runbookot, és kattintson a **Start**.
4. Ha a runbook paraméterekkel rendelkezik, mindegyik paraméterhez szövegmező értékeinek megadására kéri. Lásd: [Runbook-paraméterek](#Runbook-parameters) alatt kapcsolatos tovább információkért paraméterek.
5. Az a **feladat** lapon megtekintheti a runbook feladatának állapotát.

## <a name="starting-a-runbook-with-windows-powershell"></a>Runbook indítása a Windows PowerShell használatával
Használhatja a [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) runbook indítása a Windows PowerShell használatával. Az alábbi példakód elindít egy Test-Runbook nevű runbookot.

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook adja vissza egy feladatobjektumot, amely segítségével nyomon követheti az állapotát, amennyiben a runbook indítását. Ezután használhatja a feladatobjektum rendelkező [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) a feladat állapotának megállapításához és [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) való lekérheti annak kimenetét. Az alábbi példakód elindít egy Test-Runbook nevű, megvárja, amíg befejeződött, majd megjeleníti annak kimenetét.

```
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

Ha a runbook paramétereket igényel, akkor meg kell adnia azokat egy [hashtable](http://technet.microsoft.com/library/hh847780.aspx) amelyben a kivonattábla kulcsa megegyezik a paraméter neve és értéke a paraméter értéke. A következő példa bemutatja, hogyan két karakterlánc-paraméterrel Utónév és Vezetéknév, egy RepeatCount nevű egész számot és egy Show nevű logikai paraméterrel rendelkező runbook indítása. A paraméterek további információkért lásd: [Runbook-paraméterek](#Runbook-parameters) alatt.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook-paraméterek
Amikor elindít egy forgatókönyvet az Azure-portálon vagy a Windows PowerShell, a parancsfájl az Azure Automation webszolgáltatás keresztül küld el. Ez a szolgáltatás nem támogatja az összetett adattípusú paramétereket. Ha meg kell adnia egy értéket egy összetett paraméterhez, majd meg kell hívnia az beágyazott egy másik runbookból a [az Azure Automation runbookjai gyermek](automation-child-runbooks.md).

Az Azure Automation webszolgáltatás bizonyos adattípusokkal a következő szakaszokban ismertetett módon paraméterek speciális funkciókat biztosítja:

### <a name="named-values"></a>Megnevezett értékek
Ha a paraméter adattípusa [objektum], akkor használhatja a következő JSON formátummal küldhet neki egy névvel ellátott értékek listája: *{Név1: 'Érték1', Name2: 'Érték2', név3: "Érték3"}*. Ezek az értékek csak egyszerű típusok lehetnek. A runbook felkérést kap, a paraméter egy [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) a Tulajdonságok megegyeznek az egyes névvel rendelkező érték.

Tesztrunbook a következő egy felhasználó nevű paramétert fogad.

```
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

A user paraméterhez a következő szöveg használható.

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Ez a következő kimenetet eredményezi:

```
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tömbök
Ha a paraméter egy tömb, például [array] vagy [string []], a következő JSON formátummal küldhet neki értékből álló lista használhatja: *[érték1, érték2, érték3]*. Ezek az értékek csak egyszerű típusok lehetnek.

Tesztrunbook a következő nevű egy paramétert fogad *felhasználói*.

```
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

A user paraméterhez a következő szöveg használható.

```
["Joe","Smith",2,true]
```

Ez a következő kimenetet eredményezi:

```
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Hitelesítő adatok
Ha a paraméter adattípusa **PSCredential**, akkor megadhatja egy Azure Automation neve [hitelesítőadat-eszköz](automation-credentials.md). A runbook lekéri a megadott nevű hitelesítő adat.

Tesztrunbook a következő fogad el egy paraméter, hitelesítő adat.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

A következő szöveg használható a felhasználó paraméter feltéve, hogy létezik egy nevű hitelesítőeszköz *a hitelesítő adatok*.

```
My Credential
```

Feltéve, hogy a hitelesítő adatokat felhasználónév lett *jsmith*, ez a következő kimenetet eredményezi:

```
jsmith
```

## <a name="next-steps"></a>További lépések
* A runbook-architektúra az aktuális cikkben runbookok kezelése az Azure és a hibrid forgatókönyv-feldolgozót a helyszíni erőforrások magas szintű áttekintést nyújt. Automatizálási runbookok végrehajtása az adatközpontban található kapcsolatos információkért tekintse meg [hibrid forgatókönyv-feldolgozók](automation-hybrid-runbook-worker.md).
* Az egyedi vagy közös funkciók más runbookok által használható létrehozása moduláris runbookok kapcsolatos további tudnivalókért lásd [Gyermekforgatókönyvek](automation-child-runbooks.md).

