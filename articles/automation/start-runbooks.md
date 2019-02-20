---
title: Az Azure Automation forgatókönyv indítása
description: A különböző módszereket, amelyek segítségével az Azure Automation forgatókönyv indítása és az Azure portal és a Windows PowerShell használatával részletesen foglalja össze.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3fd2feb8bbd54e7aefab357b0b2ba4209aa29e09
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418586"
---
# <a name="start-a-runbook-in-azure-automation"></a>Az Azure Automation forgatókönyv indítása

Az alábbi táblázat segítségével meghatározhatja, hogy a metódus elindít egy runbookot, amely a leginkább megfelelő, az adott forgatókönyv az Azure automationben. Ez a cikk a runbook elindítása az Azure Portallal és a Windows PowerShell-lel részleteket tartalmaz. A többi módszer a részletei is szerepelnek az egyéb dokumentáció, amely az alábbi hivatkozásokra kattintva érheti el.

| **Metódus** | **Jellemzők** |
| --- | --- |
| [Azure Portal](#starting-a-runbook-with-the-azure-portal) |<li>Az interaktív felhasználói kezelőfelület legegyszerűbb módja.<br> <li>Adja meg a paraméterértékeket egyszerű képernyőt.<br> <li>Könnyedén nyomon követheti a feladat állapota.<br> <li>Az Azure bejelentkezési hitelesített hozzáférés az. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Hívja meg a Windows PowerShell-parancsmagokkal a parancssorból.<br> <li>Az automatizált megoldás több lépést is részét.<br> <li>Kérelem hitelesítése tanúsítvánnyal vagy OAuth felhasználói, fő / szolgáltatás egyszerű.<br> <li>Adja meg a egyszerű és összetett paraméterértékeket.<br> <li>Feladat állapotának nyomon követése<br> <li>PowerShell-parancsmagok támogatnia kell az ügyfelet. |
| [Az Azure Automation-API](/rest/api/automation/) |<li>Legrugalmasabb módszer, de a legtöbb összetett is.<br> <li>Bármilyen egyéni kód, amely megkönnyíti a HTTP-kérések hívása.<br> <li>A kérelem hitelesítése tanúsítvánnyal vagy Oauth felhasználói, fő / szolgáltatás egyszerű.<br> <li>Adja meg a egyszerű és összetett paraméterértékeket. *Ha az API-val a Python-runbook hívása, a JSON-adattartalom szerializálni kell.*<br> <li>Feladat állapotának nyomon követése |
| [Webhooks](automation-webhooks.md) |<li>Indítsa el a runbook egy HTTP-kérelemből.<br> <li>Hitelesített olyan biztonsági jogkivonat URL-címben.<br> <li>Ügyfél nem bírálhatják felül a webhook létrehozásakor megadott paraméterértékek. Runbook megadhat egyetlen paraméter, amely megjelenik a HTTP-kérés részleteinek megtekintéséhez.<br> <li>Nincs lehetőség keresztül webhook URL-CÍMÉT a feladat állapotának nyomon követéséhez. |
| [Az Azure riasztás válaszolni](../log-analytics/log-analytics-alerts.md) |<li>Runbook indítása Azure-riasztásra adott válaszként.<br> <li>Konfigurálja a runbookhoz, és riasztást hivatkozásra webhookot.<br> <li>Hitelesített olyan biztonsági jogkivonat URL-címben. |
| [Ütemezés](automation-schedules.md) |<li>Óránkénti, napi, heti vagy havi ütemezés szerint automatikusan el a runbookot.<br> <li>Ütemezés – az Azure portal, PowerShell-parancsmagok vagy az Azure API módosítására.<br> <li>Adja meg a ütemezés használandó paraméterértékeket. |
| [Egy másik runbookból](automation-child-runbooks.md) |<li>Egy runbook használja egy másik runbook-tevékenység.<br> <li>Akkor hasznos, ha több runbook által használt szolgáltatásokat.<br> <li>Adja meg a paraméterértékeket a gyermekrunbook, és a szülő runbook kimeneti használja. |

Az alábbi képen egy runbook életciklusának részletes részletes folyamatát mutatja be. Egy runbook elindítja az Azure Automation szolgáltatás különböző módszereket vezetett be, mely a hibrid Runbook-feldolgozó hajtsa végre az Azure Automation-runbookok és a különböző összetevők közötti interakciók szükséges összetevőket. Automation-runbookok végrehajtása a helyi adatközpontban kapcsolatos további információkért tekintse meg [hibrid runbook-feldolgozók](automation-hybrid-runbook-worker.md)

![Runbook-architektúra](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Runbook indítása az Azure portal használatával

1. Az Azure Portalon válassza ki a **Automation** és kattintson az automation-fiók nevére.
2. A központ menüben válassza ki a **Runbookok**.
3. Az a **Runbookok** lapon válasszon ki egy runbookot, és kattintson a **Start**.
4. Ha a runbook paraméterekkel rendelkezik, az egyes paraméterekhez tartozó szövegmező értékeinek megadására kéri. A paraméterek további információkért lásd: [Runbook paraméterek](#Runbook-parameters).
5. Az a **feladat** lapon megtekintheti a runbook-feladat állapotát.

## <a name="start-a-runbook-with-powershell"></a>Runbook indítása a PowerShell-lel

Használhatja a [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) runbook indítása a Windows PowerShell-lel. Az alábbi példakód egy Test-Runbook nevű runbook elindul.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook visszaad egy feladatobjektumot, amely segítségével nyomon követheti az állapotát, a runbook indítását követően. Ezután használhatja a feladatobjektum és a [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) a feladat állapotának megállapításához és [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) lekérheti annak kimenetét. Az alábbi példakód egy Test-Runbook, megvárja, amíg a befejeződött, majd megjeleníti annak kimenetét nevű runbook elindul.

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

Ha a runbookhoz paraméterek szükségesek, akkor meg kell adnia azokat egy [kivonattábla](https://technet.microsoft.com/library/hh847780.aspx). A kivonattábla kulcsa a kulcsot meg kell egyeznie a paraméter nevével, értéke pedig a paraméter értéke. Az alábbi példa bemutatja, hogyan két karakterlánc paraméterei nevű Keresztnév és Vezetéknév, egy RepeatCount nevű egész és egy Show nevű logikai paraméterrel rendelkező runbook indítása. A paraméterek további információkért lásd: [Runbook paraméterek](#Runbook-parameters) alatt.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook-paraméterek

Amikor elindít egy runbookot az Azure Portalon vagy a Windows PowerShell, a utasításait követve az Azure Automation webszolgáltatás keresztül zajlik. Ez a szolgáltatás nem támogatja az összetett adattípusú paramétereket. Ha meg kell adnia egy értéket egy összetett paraméterhez, majd, kell azt beágyazva, egy másik runbookból hívja leírtak szerint [az Azure Automation runbookok](automation-child-runbooks.md).

Az Azure Automation webszolgáltatás a bizonyos adattípusokkal, az alábbiakban leírtak szerint paraméterek speciális funkciókat biztosítja:

### <a name="named-values"></a>Névvel ellátott értékek

Ha a paraméter adattípusa [objektum], akkor használhatja a következő JSON formátummal küldhet neki egy névvel ellátott értékek listája: *{Name1: "Érték1", Név2: "Érték2", név3: "Érték3"}*. Ezek az értékek csak egyszerű típusok lehetnek. A runbook a paramétert, kap egy [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) tulajdonságokkal, minden egyes névvel ellátott értékekkel.

Vegye figyelembe a következő teszt runbook, amely egy felhasználó nevű paramétert fogad el.

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

A következő szöveg használható a user paraméterhez.

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

### <a name="arrays"></a>tömbök

Ha a paraméter egy tömb, például [array] vagy [string []], használhatja a következő JSON formátummal küldhet neki értékekből álló listát: *[érték1, érték2, érték3]*. Ezek az értékek csak egyszerű típusok lehetnek.

Tesztrunbook a következő nevű paraméter elfogadja *felhasználói*.

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

A következő szöveg használható a user paraméterhez.

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

Ha a paraméter adattípusa **PSCredential**, akkor megadhatja, hogy egy Azure Automation neve [hitelesítőeszközt](automation-credentials.md). A runbook lekéri a megadott nevű hitelesítő adat.

Vegye figyelembe a következő teszt runbook, amely elfogadja a hitelesítő adatok nevű paramétert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

A következő szöveg használható a felhasználói paraméter feltéve, hogy létezik egy nevű hitelesítőeszköz *saját hitelesítő adat*.

```input
My Credential
```

Feltéve, hogy a hitelesítő adatokat felhasználónév *jsmith*, ez a következő kimenetet eredményezi:

```output
jsmith
```

## <a name="next-steps"></a>További lépések

* Aktuális a cikkben a runbook-architektúra az Azure és a hibrid Runbook-feldolgozót a helyszíni erőforrások kezelésére runbookok magas szintű áttekintést nyújt. Automation-runbookok végrehajtása a helyi adatközpontban kapcsolatos további információkért tekintse meg [hibrid Runbook-feldolgozók](automation-hybrid-runbook-worker.md).
* A létrehozás moduláris runbookok egyedi vagy közös funkciók más runbookok által használható kapcsolatos további információkért tekintse meg [gyermek Runbookok](automation-child-runbooks.md).