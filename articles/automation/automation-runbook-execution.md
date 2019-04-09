---
title: Runbook végrehajtása az Azure Automationben
description: Ismerteti, hogyan dolgozza fel az Azure Automation-runbook részletei.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0445643d3aae0e4e072e7fa8e3a73dc8973e84a5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268500"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook végrehajtása az Azure Automationben

Ha az Azure Automationben elindít egy runbookot, létrejön egy feladat. A feladat a runbook egyszeri futtatási példánya. Egy Azure Automation-feldolgozó rendelt minden egyes feladat futtatása. Feldolgozók számos Azure-fiókok által megosztott, miközben másik Automation-fiókok feladatok különítve egymástól. Melyik worker keresztül services, a kérés a feladat nem irányítás. Egyetlen runbook fut egyszerre sok feladat lehet. A feladatok ugyanazt az Automation-fiók végrehajtási környezetének felhasználhatók. Egyidejűleg futtatja a feladatot, a gyakrabban azok elirányíthatók az azonos tesztkörnyezetben. A védőfal ugyanabban a folyamatban futó feladatok hatással lehetnek egymással, például fut-e a `Disconnect-AzureRMAccount` parancsmagot. Futtatja ezt a parancsmagot lenne válassza le a megosztott védőfalfolyamat minden egyes forgatókönyv-feladatot. A runbookok listája az Azure Portalon történő megtekintésekor az összes minden runbook elindított feladatok állapotának sorolja fel. Megtekintheti a feladatok minden runbook minden egyes állapotának nyomon követését. 30 nap legfeljebb feladatnaplóit tárolódnak. A különböző feladatok állapotai leírását [feladatállapotok](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Az alábbi ábrán látható, a runbook-feladat életciklusa [PowerShell-forgatókönyvek](automation-runbook-types.md#powershell-runbooks), [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) és [PowerShell-munkafolyamati runbookok](automation-runbook-types.md#powershell-workflow-runbooks).

![Feladat állapota – PowerShell-munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

A feladatok az Azure-erőforrások elérését azáltal, hogy az Azure-előfizetéshez kapcsolódnia kell. Csak rendelkeznek erőforrásokhoz való hozzáférés az adatközpontban, ha ezek az erőforrások elérhetők a nyilvános felhőben.

## <a name="where-to-run-your-runbooks"></a>A runbookok futtatási helyének

Az Azure Automation Runbookjai futhat bármelyiken védőfal mögött az Azure-ban vagy egy [hibrid Runbook-feldolgozó](automation-hybrid-runbook-worker.md). A tesztkörnyezet-e az Azure-ban, hogy több feladat is lehet megosztott környezetben. Feladatok használata ugyanabban a védőfal által az erőforrás-korlátozások, a védőfal vannak kötve. Hibrid Runbook-feldolgozók futtatható runbookok közvetlenül a számítógépen, amelyen a szerepkör és a helyi erőforrások kezelése a környezetben az erőforrásokon. Runbookok tárolt és kezelt az Azure Automation és egy vagy több hozzárendelt számítógépekre kézbesítését. A legtöbb runbookok egyszerűen futtathat az Azure próbakörnyezetbe lefordítja. Vannak olyan speciális helyzetek, ahol egy hibrid Runbook kiválasztása egy Azure védőfal a runbook végrehajtása során esetlegesen ajánlott. Az alábbi táblázatban néhány példa forgatókönyvet listáját lásd:

|Tevékenység|Legjobb választás|Megjegyzések|
|---|---|---|
|Integrálása az Azure-erőforrások|Azure Sandbox|Azure-ban üzemeltetett, a hitelesítés az egyszerűbb. Ha egy Azure-beli virtuális gépen egy hibrid Runbook-feldolgozó használ, használhatja [felügyelt identitások az Azure-erőforrásokhoz](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Optimális teljesítményét az azure-erőforrások kezeléséhez|Azure Sandbox|A parancsfájlt ugyanabban a környezetben, melynek cserébe megvan kisebb késés|
|Üzemeltetési költségek minimálisra csökkentéséhez|Azure Sandbox|Nincs számítási terhelés, nem kell a virtuális gép van|
|A hosszabb ideig futó parancsfájl|hibrid runbook-feldolgozó|Az Azure próbakörnyezetbe lefordítja rendelkezik [erőforrásokra vonatkozó korlátozás](../azure-subscription-service-limits.md#automation-limits)|
|Együttműködhet a helyi szolgáltatás.|hibrid runbook-feldolgozó|Férhet közvetlenül a gazdagépen|
|3. fél szoftver és végrehajtható fájlok|hibrid runbook-feldolgozó|Az operációs rendszer kezelése, és amelyekkel szoftver telepíthető|
|Egy fájl vagy egy runbook-mappa figyelése|hibrid runbook-feldolgozó|Használja a [figyelőfeladat](automation-watchers-tutorial.md) egy hibrid Runbook-feldolgozón|
|Nagy számításigényű script erőforrás|hibrid runbook-feldolgozó| Az Azure próbakörnyezetbe lefordítja rendelkezik [erőforrásokra vonatkozó korlátozás](../azure-subscription-service-limits.md#automation-limits)|
|Modulok használata az adott követelményekhez| hibrid runbook-feldolgozó|Néhány példa:</br> **WinSCP** -winscp.exe függőség </br> **IISAdministration** -kell az IIS engedélyezve|
|Telepítő igénylő modul telepítése|hibrid runbook-feldolgozó|A védőfal modulok copiable kell lennie.|
|A runbookok vagy a .NET-keretrendszer 4.7.2 eltérő igénylő modulok|hibrid runbook-feldolgozó|Automation próbakörnyezetbe lefordítja a .NET-keretrendszer 4.7.2 rendelkezik, és azt nem lehet|
|Magasabb jogosultsági szintet igénylő parancsfájlok|hibrid runbook-feldolgozó|Próbakörnyezetbe lefordítja a jogosultságszint-emelés nem engedélyezett. Oldja meg ezt, használja a hibrid Runbook-feldolgozók és kikapcsolhatja a felhasználói fiókok felügyelete és használata `Invoke-Command` mikor igényel, amely a következő parancs futtatásával jogosultságszint-emelés|
|WMI-hozzáférést igénylő parancsfájlok|hibrid runbook-feldolgozó|A felhő próbakörnyezetbe lefordítja a futó feladatok [nem rendelkeznek hozzáféréssel a WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>A Runbook viselkedése

Runbookok végrehajtása a bennük lévő definiált logika alapján. Ha egy runbook megszakad, a runbook elején indul újra. Ez a viselkedés szükséges forgatókönyvek, ahol támogatják a újraindul, ha átmeneti problémák fel úgy kell megírni.

Az Azure-ban futtatott PowerShell-feladatok a használatát a Runbook védőfal előfordulhat, hogy a teljes nyelvi módban indul. PowerShell nyelv módokkal kapcsolatos további tudnivalókért lásd: [PowerShell nyelv módok](/powershell/module/microsoft.powershell.core/about/about_language_modes). Hogyan kezelheti az Azure Automation-feladatok a további részletekért lásd: [beolvasása a feladat állapota a PowerShell-lel](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>Erőforrások létrehozása

A parancsfájl erőforrásokat hozott létre, meg kell ellenőrizze, hogy az erőforrás már létezik-e előtt hozza létre újra. Az alábbi példában egy egyszerű példa látható:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Idő függő szkriptek

Alapos megfontolás a runbookok létrehozásakor kell elvégezni. Ahogy korábban említettük, a runbookok hozhatóak létre úgy, hogy azok hatékony kell, és átmeneti hibák, amelyek okozhatnak a runbookot, hogy indítsa újra, vagy nem képes kezelni. Ha egy runbook meghibásodása, újból. Ha egy runbook általában időkorlát belül fut, a logikai ellenőrzése, meg kell valósítani a végrehajtás ideje, hogy a kezdő hasonlóan, a runbook leállítása vagy horizontális felskálázás csak adott idő alatt futnak.

### <a name="tracking-progress"></a>Követési folyamatban

Tanácsos hozhat létre runbookokat kell moduláris jellegűek. Ez azt jelenti, hogy a runbook a logikai strukturálja úgy, hogy azt újból, és könnyedén újraindul. A runbook nyomon követésekor módja a jó győződjön meg arról, hogy a runbook logikája végrehajtja megfelelően Ha is vannak problémák. Néhány lehetséges módszer a runbook állapotának nyomon követéséhez van egy külső forrás, például a storage-fiókok, adatbázis vagy a megosztott fájlok használatával. Külsőleg állapotát nyomon követése, hozhat létre logikai először a runbookban a legutóbbi műveletet, a runbook tartott állapotát. Majd át az eredmények alapján, vagy bizonyos feladatokat is, folytatható a runbook.

### <a name="prevent-concurrent-jobs"></a>Egyidejű feladatok megakadályozása

Bizonyos forgatókönyvek válna működést eredményezhet, ha több feladat között egy időben futnak. Ebben az esetben fontos logikákat implementálhat, ellenőrizze, hogy ha egy runbook már rendelkezik egy futó feladathoz. Előfordulhat, hogy hogyan teheti ezt a viselkedést példáit az alábbi példában látható:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Több előfizetés használata

Az runbookok, amelyek több előfizetést is foglalkozik, amikor a runbook kell használata a [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) parancsmaggal győződjön meg arról, hogy a hitelesítési környezetet nem veszi át egy másik runbookot, amely lehet az azonos tesztkörnyezetben fut. Szeretne használni a `-AzureRmContext` paraméterrel a `AzureRM` parancsmagok és a megfelelő környezetet adja át azt.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Kivételek kezelése

Parancsfájlok készítése, esetén fontos, hogy a kivételek és a lehetséges átmeneti hibák kezelésére képes. Néhány kivétel, vagy időszakos problémák forgatókönyvekkel való kezeléséhez különböző módjai a következők:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

A [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) preferenciaváltozó határozza meg, hogyan PowerShell válaszol egy nem megszakító hibát. Nem érinti a megszakító hibák `$ErrorActionPreference`, mindig leáll. Használatával `$ErrorActionPreference`, mint egy normál esetben nem megszakító hibát `PathNotFound` a a `Get-ChildItem` parancsmag leállítja a runbook befejezését. Az alábbi példa bemutatja, hogy használatával `$ErrorActionPreference`. A végső `Write-Output` sor soha nem hajtja végre, mivel a szkript le fog állni.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Végül próbálja meg Catch

[Próbálja meg Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) segítséget nyújt a megszakító hibáinak kezelése a PowerShell-parancsfájlok szerepel. Próbálja ki a tényleges használatával tényleges adott kivétel, vagy általános kivételek. A "catch" utasítás hibák nyomon követésére használt kell vagy el kezelni a hibát. Az alábbi példa próbálja meg letölteni a fájlt, amely nem létezik. Kivételekkel, az `System.Net.WebException` kivétel, ha egy másik kivétel utolsó értékét adja vissza.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) segítségével egy megszakító hiba jön létre. Ez akkor hasznos, ha a runbook a saját logikai meghatározása. Ha egy bizonyos feltétel teljesül, le kell állnia, a parancsfájl, akkor használhatja `throw` leállítani a parancsfájl. A következő példa bemutatja a gép egy függvény paramétere szükség használatával `throw`.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Végrehajtható fájlok használatával, vagy folyamatok hívása

Runbookok futtatása az Azure próbakörnyezetbe lefordítja a hívó folyamatok (például .exe vagy subprocess.call) nem támogatja. Ennek az oka próbakörnyezetbe lefordítja a az Azure-futtatása tárolók, amely előfordulhat, hogy rendelkezik az alapul szolgáló API-hozzáférés a megosztott folyamat. A forgatókönyvek, ahol a szolgáltatására szükség 3. fél szoftver- vagy alárendelt folyamatok hívása, ajánlott végrehajtja a forgatókönyvet a egy [hibrid Runbook-feldolgozó](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Eszköz- és az jellemzői

Runbook-feladatok futtatása Azure próbakörnyezetbe lefordítja a nincs hozzáférése bármely eszköz vagy alkalmazás jellemzőit. A leggyakrabban használt API-val a lekérdezési teljesítmény-mérőszámokat a Windows WMI. Gyakori metrikák néhány memória és CPU-használat. Azonban nem számít, milyen API-t használja. A felhőben futó feladatok nem rendelkezik megfelelő jogosultsággal a Microsoft általi implementációja webes alapján Enterprise Management (WBEM), amely a Common Information Model (CIM) be van építve, amelyeket az iparági normák, eszközök és az alkalmazások jellemzőinek definiálása.

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
| Felfüggesztve |A felhasználó, a rendszer vagy a runbook egy parancsa felfüggesztette a feladatot. Ha egy runbook nem tartalmaz egy ellenőrzőpontot, a runbook elejétől kezdődik. Ha ellenőrzőponttal rendelkezik, ez indítsa el újra, és az utolsó ellenőrzőponttól folytatni. A runbook csak felfüggesztve, ha kivétel lép fel. Alapértelmezés szerint ErrorActionPreference értéke **Folytatás**, tehát a feladat a hiba továbbra is működik. Ha ezt a preferenciaváltozót **leállítása**, majd a feladat felfüggeszti az hiba. Érvényes [grafikus és a PowerShell-munkafolyamati runbookok](automation-runbook-types.md) csak. |
| Felfüggesztés |A rendszer megpróbálja a feladat felfüggesztését a felhasználó a kérelmet. A runbook felfüggesztés előtt el kell érnie a következő ellenőrzőpontot. Ha már elhagyta az utolsó ellenőrzőpontot, majd a felfüggesztés előtt befejeződött. Érvényes [grafikus és a PowerShell-munkafolyamati runbookok](automation-runbook-types.md) csak. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Az Azure Portalról a feladat állapotának megtekintése

Megtekintheti az összes runbook-feladatok összesített állapotát, vagy egy adott runbook-feladat az Azure Portalon részletek feltárásához. Integráció konfigurálhatja a runbook állapota és a feladat feladatstreamek továbbítják a Log Analytics-munkaterület is. Integrálás az Azure Monitor naplóira kapcsolatos további információkért lásd: [feladat állapotát és a feladatstreamek továbbítja automatizálást az Azure Monitor naplóira](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Automation-runbook feladatok összegzése

A kijelölt Automation-fiók a jobb oldalon látható a runbook-feladatok összefoglalása **feladatstatisztika** csempére.

![Feladatstatisztika csempe](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ez a csempe és az összes feladat végrehajtása a feladat állapotának grafikus ábrázolását jeleníti meg.

Megadja a csempére kattintva a **feladatok** oldal, amely tartalmazza az összes feladat végrehajtása összefoglaló listája. Ezen a lapon látható az állapot, a kezdési idejének és a befejezési idők.

![Automation-fiók feladatok oldalon](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Kiválasztásával szűrheti a feladatok **feladatok szűrése** és szűrő egy adott runbookot, a feladat állapotát, vagy a legördülő listából válassza ki, és az időtartományt, amelyben keresni kíván.

![Szűrési feladat állapota](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Azt is megteheti, megtekintheti a feladat összegző részleteit egy adott runbook a runbook kiválasztásával a **Runbookok** lapon az Automation-fiókban, és válassza ki a **feladatok** csempére. Ez a művelet mutat be a **feladatok** lap, és ott kattintson a feladat rekord megtekintheti azok részleteit és kimenetét.

![Automation-fiók feladatok oldalon](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Feladat összegzése

Megtekintheti egy adott runbookhoz és azok állapotával létrehozott összes feladat listáját. Szűrhet az ebben a listában, a feladat állapotát és a feladat utolsó módosításának időtartománya. Az egyes feladatok nevére kattintva megtekintheti azok részletes információt és kimenetét. A feladat részletes nézetében megtalálhatók azon runbook-paraméterek értékei, amelyek meg lettek adva a feladathoz.

Az egy adott runbookhoz tartozó feladatok a következő lépésekkel tekinthetők meg.

1. Az Azure Portalon válassza ki a **Automation** , és válassza ki az Automation-fiók nevére.
2. Válassza ki a hub **Runbookok** majd a a **Runbookok** lapon válasszon ki egy runbookot a listából.
3. A kiválasztott runbook lapon kattintson a **feladatok** csempére.
4. Kattintson a listában a feladatok egyikét, és a runbook-feladat részletei lapon megtekintheti azok részleteit és kimenetét.

## <a name="retrieving-job-status-using-powershell"></a>PowerShell-lel feladatállapot lekérése

Használhatja a [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) lekérheti feladatokat, létrehozott egy runbookot és a egy adott feladat részleteit. Ha elindít egy runbookot a PowerShell [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), majd visszaadja az eredményezett feladatot. Használat [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) beolvasni a feladat kimenetének.

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
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    { 
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>Igazságos elosztás

Megoszthatják az erőforrásokat az összes runbook a felhőben, az Azure Automation ideiglenesen eltávolítja az adott vagy leállítja az összes feladat végrehajtása több mint három órán keresztül. A feladatok [PowerShell-alapú runbookok](automation-runbook-types.md#powershell-runbooks) és [Python runbookok](automation-runbook-types.md#python-runbooks) le lesznek állítva és nem indítják újra, és a feladat állapota leállítva.

A sokáig futó feladatokat, azt javasoljuk, hogy használjon egy [hibrid Runbook-feldolgozó](automation-hrw-run-runbooks.md#job-behavior). Hibrid Runbook-feldolgozók igazságos elosztás nem korlátozza, és a egy korlátozás nem rendelkezik a mennyi runbookot hajthat végre. A többi feladat [korlátok](../azure-subscription-service-limits.md#automation-limits) Azure próbakörnyezetbe lefordítja a és a hibrid Runbook-feldolgozók egyaránt érvényesek. Hibrid Runbook-feldolgozók nem korlátozza a 3 óra igazságos elosztás korlátot, miközben futtassa őket a runbookok újraindítási viselkedés a helyi infrastruktúra váratlan problémák támogatására kell kidolgozni.

Egy másik lehetőség, hogy a runbook optimalizálása gyermek runbookok használatával. Ha a runbook végighalad a több erőforrás, például az adatbázis-művelet, a számos adatbázis ugyanannak a függvénynek helyezheti át a függvényt egy [gyermekrunbook](automation-child-runbooks.md) és adja neki a a [ Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) parancsmagot. Gyermek runbookok mindegyike külön folyamatokban párhuzamosan hajtja végre. Ez a viselkedés csökkenti a teljes ideje a szülő runbook befejezéséhez. Használhatja a [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) parancsmagot a feladat állapotának ellenőrzéséhez minden gyermek, ha vannak a műveleteket a gyermekrunbook befejezése után a runbookban.

## <a name="next-steps"></a>További lépések

* A runbook indítása az Azure Automation segítségével különböző módszerekkel kapcsolatos további tudnivalókért lásd: [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)

