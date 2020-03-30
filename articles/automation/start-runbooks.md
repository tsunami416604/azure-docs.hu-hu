---
title: Runbook indítása az Azure Automationben
description: Összefoglalja a különböző módszereket, amelyek segítségével elindíthategy runbookot az Azure Automationben, és részletesen ismerteti az Azure Portal és a Windows PowerShell használatával.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 71dd83db02537ed12dc2e711127e32d90603af6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252648"
---
# <a name="start-a-runbook-in-azure-automation"></a>Runbook indítása az Azure Automationben

Az alábbi táblázat segítségével meghatározhatja, hogy az Azure Automationben az adott forgatókönyvnek leginkább megfelelő runbook ot kell elindítani. Ez a cikk az Azure Portalon és a Windows PowerShell használatával való runbook indításának részleteit tartalmazza. A többi módszer rel kapcsolatos részleteket az alábbi linkekről elérhető egyéb dokumentáció tartalmazza.

| **Módszer** | **Jellemzők** |
| --- | --- |
| [Azure-portál](#start-a-runbook-with-the-azure-portal) |<li>Legegyszerűbb módszer interaktív felhasználói felülettel.<br> <li>Űrlap, amely egyszerű paraméterértékeket ad meg.<br> <li>Egyszerűen nyomon követheti a feladat állapotát.<br> <li>Azure-bejelentkezéssel hitelesített hozzáférés. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Hívás a parancssorból a Windows PowerShell-parancsmagokkal.<br> <li>Az automatizált megoldás több lépésből áll.<br> <li>A kérelem hitelesítése tanúsítvány vagy OAuth egyszerű felhasználó / egyszerű szolgáltatás.<br> <li>Adjon meg egyszerű és összetett paraméterértékeket.<br> <li>A feladat állapotának nyomon követése.<br> <li>A PowerShell-parancsmagok támogatásához szükséges ügyfél. |
| [Azure Automation API](/rest/api/automation/) |<li>A legrugalmasabb módszer, de egyben a legösszetettebb is.<br> <li>Hívás bármely egyéni kódból, amely HTTP-kéréseket tud kezdeményezni.<br> <li>A kérelem hitelesített tanúsítvánnyal vagy Oauth egyszerű felhasználó / egyszerű szolgáltatás.<br> <li>Adjon meg egyszerű és összetett paraméterértékeket. *Ha egy Python runbook ot hív meg az API-val, a JSON-tartalom kell szerializált.*<br> <li>A feladat állapotának nyomon követése. |
| [Webhookok](automation-webhooks.md) |<li>Indítsa el a runbookot egyetlen HTTP-kérelemből.<br> <li>Biztonsági jogkivonattal hitelesített az URL-ben.<br> <li>Az ügyfél nem bírálhatja felül a webhook létrehozásakor megadott paraméterértékeket. A Runbook egyetlen paramétert határozhat meg, amely a HTTP-kérelem részleteivel van feltöltve.<br> <li>Nem képes nyomon követni a feladat állapotát a webhook URL-címén keresztül. |
| [Válasz az Azure Alert szolgáltatásra](../log-analytics/log-analytics-alerts.md) |<li>Indítsa el a runbook az Azure-riasztásválaszul.<br> <li>Konfigurálja a webhookot a runbookhoz és a riasztási hivatkozáshoz.<br> <li>Biztonsági jogkivonattal hitelesített az URL-ben. |
| [Ütemezés](automation-schedules.md) |<li>A runbook automatikus indítása óránkénti, napi, heti vagy havi ütemezés szerint.<br> <li>Ütemzés imázsát az Azure Portalon, a PowerShell-parancsmagokon vagy az Azure API-n keresztül módosíthatja.<br> <li>Adja meg az ütemezéssel használandó paraméterértékeket. |
| [Egy másik Runbookról](automation-child-runbooks.md) |<li>Egy runbook használata egy másik runbook tevékenységeként.<br> <li>Több runbook által használt funkciókhoz hasznos.<br> <li>Adja meg a paraméterértékeket a gyermek runbook és a kimenet használata a szülő runbook. |

Az alábbi képen részletes, lépésről-lépésre egy runbook életciklusa részletesen bemutatja. A runbook indítása az Azure Automationben különböző módokon, amelyek a hibrid Runbook-feldolgozó számára szükségesek az Azure Automation runbookok és a különböző összetevők közötti interakciók végrehajtásához szükséges összetevők. Az Automation-runbookok adatközpontban történő végrehajtásáról a [hibrid runbook-dolgozók](automation-hybrid-runbook-worker.md) című dokumentumban olvashat.

![Runbook architektúra](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Runbook indítása az Azure Portalon

1. Az Azure Portalon válassza **az Automatizálás** lehetőséget, majd kattintson egy automatizálási fiók nevére.
2. A Központ menüben válassza a **Runbooks parancsot.**
3. A **Runbooks** lapon jelöljön ki egy runbookot, majd kattintson a **Start**gombra.
4. Ha a runbook paraméterekkel rendelkezik, a rendszer kéri, hogy minden paraméterhez adjon meg értékeket. A paraméterekről további információt a [Runbook parameters című témakörben talál.](#runbook-parameters)
5. A **Feladat** lapon megtekintheti a runbook-feladat állapotát.

## <a name="start-a-runbook-with-powershell"></a>Runbook indítása a PowerShell használatával

A [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) segítségével elindíthat egy runbookot a Windows PowerShell használatával. Az alábbi példakód egy Test-Runbook nevű runbook indítását mutatja be.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

A Start-AzureRmAutomationRunbook egy feladatobjektumot ad vissza, amely a runbook indítása után nyomon követheti az állapotát. Ezután használhatja ezt a feladatobjektumot [a Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) segítségével a feladat állapotának meghatározásához, és a [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) a kimenet leszámításához. Az alábbi példakód elindít egy Test-Runbook nevű runbookot, megvárja, amíg befejeződik, majd megjeleníti annak kimenetét.

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

Ha a runbook paramétereket igényel, akkor meg kell adnia azokat [kivonattable](https://technet.microsoft.com/library/hh847780.aspx)néven. A kivonattábla kulcsának meg kell egyeznie a paraméter nevével, és az érték a paraméter értéke. A következő példa bemutatja egy FirstName és egy LastName sztringparaméterrel, egy RepeatCount nevű egész számmal, valamint egy Show nevű logikai paraméterrel rendelkező runbook futtatását. A paraméterekről az alábbi [Runbook-paraméterek](#runbook-parameters) című témakörben olvashat bővebben.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook-paraméterek

Amikor elindítja a runbook ot az Azure Portalon vagy a Windows PowerShell, az utasítás az Azure Automation webszolgáltatáson keresztül küldi. Ez a szolgáltatás nem támogatja az összetett adattípusokkal rendelkező paramétereket. Ha egy összetett paraméter értékét kell megadnia, akkor egy másik runbookból kell meghívnia, ahogy azt az [Azure Automation gyermekrunbookjai](automation-child-runbooks.md)ismertetik.

Az Azure Automation webszolgáltatás speciális funkciókat biztosít bizonyos adattípusokat használó paraméterekhez a következő szakaszokban leírtak szerint:

### <a name="named-values"></a>Névvel ellátott értékek

Ha a paraméter [object] adattípus, akkor a következő JSON formátummal elküldheti neki az elnevezett értékek listáját: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Ezeknek az értékek csak egyszerű típusok lehetnek. A runbook a paramétert [PSCustomObject objektumként](/dotnet/api/system.management.automation.pscustomobject) kapja, amelynek tulajdonságai megfelelnek az egyes elnevezett értékeknek.

A következő tesztrunbook elfogad egy usernevű paramétert.

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

A user paraméterhez a következő szöveg használható.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Ennek eredménye a következő kimenet:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tömbök

Ha a paraméter egy tömb, például [tömb] vagy [string[]], akkor a következő JSON formátummal elküldheti neki az értékek listáját: *[Value1, Value2, Value3]*. Ezeknek az értékek csak egyszerű típusok lehetnek.

A következő tesztrunbook elfogad egy *user*nevű paramétert.

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

A user paraméterhez a következő szöveg használható.

```input
["Joe","Smith",2,true]
```

Ennek eredménye a következő kimenet:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Hitelesítő adatok

Ha a paraméter **PSCredential**adattípus, akkor megadhategy Azure [Automation-hitelesítő adategység](automation-credentials.md)nevét. A runbook a megadott névvel olvassa be a hitelesítő adatot.

A következő tesztrunbook elfogad egy credentialnevű paramétert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

A következő szöveg használható a felhasználói paraméterhez, feltéve, hogy volt egy *Hitelesítő adattároló*nevű hitelesítő elem.

```input
My Credential
```

Feltételezve, hogy a hitelesítő adatban szereplő felhasználónév *jsmith*volt, az eredmény a következő kimenetet eredményezi:

```output
jsmith
```

## <a name="next-steps"></a>További lépések

* A runbook architektúra az aktuális cikkben nyújt magas szintű áttekintést runbookok erőforrások kezelése az Azure-ban és a helyszíni a hibrid Runbook-feldolgozó. Az Automation-runbookok adatközpontban történő végrehajtásáról a [Hibrid Runbook-feldolgozók](automation-hybrid-runbook-worker.md)című dokumentumban olvashat.
* Ha többet szeretne megtudni arról, hogy létre kell adni a más runbookok által meghatározott vagy gyakori függvényekhez használt moduláris runbookokat, olvassa el a [Gyermek runbookok című cikket.](automation-child-runbooks.md)
* A PowerShellről további információt, beleértve a nyelvi referencia- és tanulási modulokat, olvassa el a [PowerShell-dokumentumok című dokumentumban.](https://docs.microsoft.com/powershell/scripting/overview)
