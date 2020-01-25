---
title: Runbook végrehajtás a Azure Automationban
description: Ismerteti, hogy a rendszer hogyan dolgozza fel a runbook a Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c97e10d2785b7dc1a438c95dca9be94fcef82f94
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714849"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook végrehajtás a Azure Automationban

Amikor Azure Automationban indít el egy runbook, a rendszer létrehoz egy feladatot. A feladatok a runbook egyetlen végrehajtási példánya. Az egyes feladatok futtatásához Azure Automation feldolgozót kell rendelni. Míg a feldolgozókat számos Azure-fiók megosztja, a különböző Automation-fiókoktól származó feladatok egymástól el vannak különítve. Nem szabályozhatja, hogy mely munkavégző szolgáltatások a feladatra vonatkozó kérést. Egyetlen runbook egyszerre több feladat is futhat. Előfordulhat, hogy az azonos Automation-fiókból származó feladatok végrehajtási környezete újra felhasználható. Minél több feladatot futtat egyszerre, annál gyakrabban lehet ugyanarra a sandboxra elküldeni. Az ugyanabban a homokozóban futó feladatok hatással lehetnek egymásra, az egyik példa a `Disconnect-AzureRMAccount` parancsmagot futtatja. Ha ezt a parancsmagot futtatja, az összes runbook-feladatot leválaszthatja a megosztott sandbox-folyamat során. Amikor megtekinti a runbookok listáját a Azure Portalban, az felsorolja az egyes runbook elindított összes feladat állapotát. Az egyes runbook tartozó feladatok listáját megtekintheti az egyes műveletek állapotának nyomon követéséhez. A feladatok naplóit legfeljebb 30 napig tároljuk. A feladatok állapotának [különböző állapotainak leírása.](#job-statuses)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Az alábbi ábrán egy runbook-feladatok életciklusa látható a [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks), a [grafikus Runbookok](automation-runbook-types.md#graphical-runbooks) és a [PowerShell-munkafolyamatok runbookok](automation-runbook-types.md#powershell-workflow-runbooks).

![Feladatok állapota – PowerShell-munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

A feladatok az Azure-előfizetéshez való kapcsolódással férhetnek hozzá az Azure-erőforrásokhoz. Csak akkor férhetnek hozzá az adatközpont erőforrásaihoz, ha ezek az erőforrások elérhetők a nyilvános felhőből.

## <a name="where-to-run-your-runbooks"></a>A runbookok futtatása

A Azure Automation runbookok az Azure-ban vagy egy [hibrid Runbook-feldolgozón](automation-hybrid-runbook-worker.md)futtathatók. A homokozó egy olyan megosztott környezet az Azure-ban, amelyet több feladat is használhat. Az azonos Sandboxot használó feladatokat a rendszer a sandbox erőforrás-korlátaihoz köti. A hibrid Runbook-feldolgozók a helyi erőforrások kezeléséhez közvetlenül futtathatják a szerepkört üzemeltető számítógépen és a környezet erőforrásain lévő runbookok. A runbookok tárolása és kezelése Azure Automation, majd egy vagy több hozzárendelt számítógéphez történik. A legtöbb runbookok könnyen futtatható az Azure-beli Sandboxban. Vannak olyan konkrét forgatókönyvek, ahol hibrid Runbook választanak egy Azure-beli homokozóban a Runbook végrehajtásához. Néhány példa a következő táblázatban látható:

|Tevékenység|Legjobb választás|Megjegyzések|
|---|---|---|
|Integráció az Azure-erőforrásokkal|Azure-beli homokozó|Az Azure-ban üzemeltetett hitelesítés egyszerűbb. Ha hibrid Runbook-feldolgozót használ egy Azure-beli virtuális gépen, [felügyelt identitásokat használhat az Azure-erőforrásokhoz](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Optimális teljesítmény az Azure-erőforrások kezeléséhez|Azure-beli homokozó|A parancsfájl ugyanabban a környezetben fut, amely viszont kevesebb késéssel rendelkezik|
|Működési költségek csökkentése|Azure-beli homokozó|Nincs számítási terhelés, nincs szükség virtuális gépre|
|Hosszú ideig futó parancsfájl|Hibrid forgatókönyv-feldolgozó|Az Azure-beli munkaterületek [korlátozásai az erőforrásokon](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Helyi szolgáltatásokkal való kommunikáció|Hibrid forgatókönyv-feldolgozó|Közvetlenül is hozzáférhet a gazdagéphez|
|Harmadik féltől származó szoftverek és végrehajtható fájlok megkövetelése|Hibrid forgatókönyv-feldolgozó|Kezelheti az operációs rendszert, és telepíthet szoftvereket|
|Fájl vagy mappa figyelése runbook|Hibrid forgatókönyv-feldolgozó|[Figyelő tevékenység](automation-watchers-tutorial.md) használata hibrid Runbook-feldolgozón|
|Erőforrás-igényes parancsfájl|Hibrid forgatókönyv-feldolgozó| Az Azure-beli munkaterületek [korlátozásai az erőforrásokon](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Speciális követelményekkel rendelkező modulok használata| Hibrid forgatókönyv-feldolgozó|Néhány példa:</br> **Megnyerő** – függőség a megnyert. exe fájltól </br> **IISAdministration** – engedélyezni kell az IIS-t|
|Telepítőt igénylő modul telepítése|Hibrid forgatókönyv-feldolgozó|A copiable modulnak kell lennie|
|A 4.7.2-től eltérő .NET-keretrendszert igénylő runbookok vagy modulok használata|Hibrid forgatókönyv-feldolgozó|Az Automation-munkaterületeken a .NET-keretrendszer 4.7.2 van, és nincs lehetőség a frissítésre|
|Jogosultságszint-emelést igénylő parancsfájlok|Hibrid forgatókönyv-feldolgozó|A sandboxok nem engedélyezik a jogosultságszint-emelést. Ennek megoldásához használjon hibrid Runbook-feldolgozót, és kapcsolja ki az UAC-t, és használja `Invoke-Command` a jogosultságszint-emelést igénylő parancs futtatásakor.|
|A WMI-hozzáférést igénylő parancsfájlok|Hibrid forgatókönyv-feldolgozó|A Felhőbeli munkaterületeken futó feladatok [nem rendelkeznek hozzáféréssel a WMI-hez](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Runbook viselkedés

A runbookok végrehajtása a bennük definiált logika alapján történik. Ha a runbook megszakad, a runbook az elején indul el. Ez a viselkedés megköveteli, hogy a runbookok olyan módon legyenek írva, hogy a rendszer az újraindítást is lehetővé teszi, ha átmeneti problémák léptek fel.

Az Azure-beli homokozóban futtatott Runbook indított PowerShell-feladatok nem teljes nyelvi módban futnak. További információ a PowerShell nyelvi módjairól: [PowerShell nyelvi módok](/powershell/module/microsoft.powershell.core/about/about_language_modes). További információ a Azure Automation feladatok kezelésével kapcsolatban: a [feladat állapotának beolvasása a PowerShell](#retrieving-job-status-using-powershell) -lel

### <a name="creating-resources"></a>Erőforrások létrehozása

Ha a parancsfájl erőforrásokat hoz létre, ellenőrizze, hogy az erőforrás már létezik-e, mielőtt újra létrehozza azt. Az alábbi példában egy alapszintű példa látható:

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

### <a name="time-dependent-scripts"></a>Időbeli függő parancsfájlok

A runbookok készítésekor körültekintő megfontolást kell végezni. Ahogy korábban említettük, a runbookok úgy kell létrehozni, hogy robusztusak legyenek, és képesek legyenek a runbook újraindítását vagy meghibásodását okozó átmeneti hibák kezelésére. Ha egy runbook meghiúsul, a rendszer újrapróbálkozik. Ha egy runbook általában egy időkorláton belül fut, a végrehajtási idő ellenőrzéséhez a runbook kell végrehajtani a logikát annak érdekében, hogy a műveletek, például az indítás, a Leállítás vagy a vertikális felskálázás csak bizonyos időpontokban fussanak.

> [!NOTE]
> Az Azure-beli sandbox-folyamat helyi ideje UTC-időre van beállítva. A runbookok dátumra és időpontra vonatkozó számításait figyelembe kell venni.

### <a name="tracking-progress"></a>Követési folyamat

Érdemes lehet a runbookok-t a moduláris jelleggel létrehozni. Ez azt jelenti, hogy a logikát úgy kell strukturálni a runbook, hogy az újra felhasználható legyen, és könnyen újra lehessen indítani. A runbook nyomon követése jó módszer annak biztosítására, hogy a runbook logikája megfelelően fusson, ha problémák léptek fel. A runbook előrehaladásának nyomon követésének lehetséges módjai egy külső forrás, például a Storage-fiókok, az adatbázisok vagy a megosztott fájlok használatával. Az állapot külső nyomon követésével létrehozhat logikát a runbook, hogy először ellenőrizzék a runbook vett utolsó művelet állapotát. Ezután az eredmények alapján kihagyhatja vagy folytathatja a runbook meghatározott feladatait.

### <a name="prevent-concurrent-jobs"></a>Egyidejű feladatok megakadályozása

Előfordulhat, hogy néhány runbookok furcsán viselkedik, ha egyszerre több feladaton futnak. Ebben az esetben fontos a logikát megvalósítani annak megállapításához, hogy egy runbook már rendelkezik-e futó feladatokkal. A következő példában egy alapszintű példát láthat arra, hogyan végezheti el ezt a viselkedést:

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

Több előfizetéssel foglalkozó runbookok készítésekor a runbook a [disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) parancsmaggal kell meggyőződnie arról, hogy a hitelesítési környezet nem olvasható be egy másik, ugyanazon a homokozóban futó runbook. Ezután a `-AzureRmContext` paramétert kell használnia a `AzureRM`-parancsmagokon, és át kell adnia a megfelelő környezetet.

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

Parancsfájlok készítésekor fontos, hogy kezelni tudja a kivételeket és a lehetséges időszakos hibákat. Az alábbiakban néhány különböző módon kezelheti a kivételeket vagy a runbookok időszakos problémákat:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

A [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) preferencia változó határozza meg, hogy a PowerShell hogyan válaszol a megszakítást nem okozó hibára. A leállítási hibákat a `$ErrorActionPreference`nem érinti, és mindig leáll. A `$ErrorActionPreference`használatával a `Get-ChildItem` parancsmag által `PathNotFound`tól eltérő, nem megszakítást okozó hiba miatt a rendszer leállítja a runbook. Az alábbi példa a `$ErrorActionPreference`használatát mutatja be. A végleges `Write-Output` sor soha nem lesz végrehajtva, mert a szkript leáll.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Próbálja ki a fogást

[Próbálja ki a Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) eszközt a PowerShell-parancsfájlokban a megszakítási hibák kezeléséhez. A try catch használatával konkrét kivételeket vagy általános kivételeket is megadhat. A Catch utasítás segítségével nyomon követheti a hibákat, vagy felhasználhatja azokat a hibák kezelésére. A következő példa egy nem létező fájl letöltését kísérli meg. A `System.Net.WebException` kivételt, ha másik kivétel történt, az utolsó értéket adja vissza.

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

A [dob](/powershell/module/microsoft.powershell.core/about/about_throw) használatával leállítási hiba hozható fel. Ez akkor lehet hasznos, ha saját logikát határoz meg egy runbook. Ha teljesülnek bizonyos feltételek, amelyeknek le kell állítania a parancsfájlt, a `throw` használatával állíthatja le a parancsfájlt. A következő példa a Machine a `throw`használatával megkövetelt Function paramétert jeleníti meg.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Végrehajtható fájlok vagy hívási folyamatok használata

Az Azure-beli runbookok való futtatás nem támogatja a hívási folyamatokat (például. exe vagy alfolyamat. call). Ennek az az oka, hogy az Azure-beli munkaterületek megosztott folyamatok tárolókban futnak, amelyek nem férhetnek hozzá az összes mögöttes API-hoz. Olyan esetekben, amikor harmadik féltől származó szoftvert vagy alfolyamatokat hív meg, javasoljuk, hogy a runbook egy [hibrid runbook-feldolgozón](automation-hybrid-runbook-worker.md)hajtsa végre.

### <a name="device-and-application-characteristics"></a>Eszköz és alkalmazás jellemzői

Az Azure-beli munkaterületeken futó Runbook-feladatok nem rendelkeznek hozzáféréssel semmilyen eszközhöz vagy alkalmazási tulajdonsághoz. A Windowson a teljesítmény-mérőszámok lekérdezéséhez használt leggyakoribb API a WMI. Ezek közül néhány gyakori mérőszám a memória és a CPU-használat. Azonban nem számít, hogy milyen API-t használ. A felhőben futó feladatok nem férnek hozzá a webalapú Enterprise Management (WBEM) Microsoft általi megvalósításához, amely az eszközök és alkalmazások jellemzőinek meghatározására szolgáló iparági szabványoknak van kialakítva (CIM (CIM).

## <a name="job-statuses"></a>Feladatok állapota

A következő táblázat a feladatokhoz lehetséges különböző állapotokat ismerteti. A PowerShell kétféle hibát, megszakítást és megszakítás nélküli hibát tartalmaz. A leállítási hibák esetén a runbook állapota **sikertelen** , ha azok történnek. A megszakítást nem okozó hibák lehetővé teszik, hogy a parancsfájl még azután is folytatódjon. A megszakítást nem okozó hiba például a `Get-ChildItem` parancsmag használata olyan elérési úttal, amely nem létezik. A PowerShell látja, hogy az elérési út nem létezik, hibát jelez, és folytatja a következő mappába. Ez a hiba nem **tudta** beállítani a runbook állapotát, és **befejezettként**lehet megjelölni. Ha kényszeríteni szeretné, hogy egy runbook leálljon a megszakítást nem okozó hiba esetén, a parancsmagon `-ErrorAction Stop` is használhatja.

| Állapot | Leírás |
|:--- |:--- |
| Befejezve |A feladatot sikerült befejezni. |
| Meghiúsult |A [grafikus és a PowerShell-munkafolyamatok runbookok](automation-runbook-types.md)a runbook nem sikerült lefordítani. A [PowerShell-parancsfájlok runbookok](automation-runbook-types.md)a runbook nem indult el, vagy a feladatokhoz kivétel történt. |
| Sikertelen, várakozás erőforrásokra |A feladatot nem sikerült végrehajtani, mert elérte a [valós megosztási](#fair-share) korlátot háromszor, és ugyanabból az ellenőrzőpontból vagy a runbook elejétől indul el. |
| Aszinkron |A művelet arra vár, hogy az automatizálási feldolgozón lévő erőforrások elérhetővé válnak, hogy el lehessen indítani. |
| Indulás alatt |A feladat hozzá lett rendelve egy feldolgozóhoz, és a rendszer megkezdi. |
| Újrakezd |A rendszer a felfüggesztést követően folytatja a feladat folytatását. |
| Fut |A művelet fut. |
| Futtatás, várakozás erőforrásokra |A feladatot eltávolították a memóriából, mert elérte a [méltányos megosztási](#fair-share) korlátot. Hamarosan folytatja az utolsó ellenőrzőpontot. |
| Leállítva |A felhasználó leállította a feladatot a befejezés előtt. |
| Leállítás |A rendszer leállítja a feladatot. |
| Függesztve |A feladatot a felhasználó, a rendszer vagy a runbook egy parancsa felfüggesztette. Ha egy runbook nem rendelkezik ellenőrzőponttal, az a runbook elejétől kezdődik. Ha ellenőrzőponttal rendelkezik, újra elindíthatja, és folytathatja az utolsó ellenőrzőpontot. A runbook csak akkor felfüggeszti a rendszer, ha kivétel történik. Alapértelmezés szerint a ErrorActionPreference a Continue ( **Folytatás**) értékre van állítva, ami azt jelenti, hogy a művelet hibán fut. Ha ez a preferencia-változó **leállításra**van állítva, akkor a feladattípus hibát jelez. Csak a [grafikus és a PowerShell munkafolyamat-runbookok](automation-runbook-types.md) vonatkozik. |
| Felfüggesztéséről |A rendszer megkísérli a feladat felfüggesztését a felhasználó kérésére. A runbook el kell érnie a következő ellenőrzőpontot a felfüggesztés előtt. Ha már elvégezte az utolsó ellenőrzőpontot, akkor a felfüggesztés előtt befejeződött. Csak a [grafikus és a PowerShell munkafolyamat-runbookok](automation-runbook-types.md) vonatkozik. |

## <a name="viewing-job-status-from-the-azure-portal"></a>A feladatok állapotának megtekintése a Azure Portal

Megtekintheti az összes runbook-feladat összegzett állapotát, vagy részletezheti egy adott runbook-feladat részleteit a Azure Portalban. Az Log Analytics-munkaterülettel való integrációt úgy is konfigurálhatja, hogy továbbítsa a runbook feladatok állapotát és a feladatok folyamait. A Azure Monitor-naplók integrálásával kapcsolatos további információkért lásd: a [feladatok állapotának és a feladatok adatfolyamának továbbítása az automatizálásból a Azure monitor naplókba](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Automation runbook-feladatok összegzése

A kiválasztott Automation-fiók jobb oldalán megtekintheti az összes runbook-feladat összegzését a **feladat statisztikája** csempén.

![Feladatok statisztikájának csempe](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ez a csempe a feladat állapotának számát és grafikus ábrázolását jeleníti meg az összes végrehajtott feladat esetében.

A csempére kattintva megjelenítheti a **feladatok** lapot, amely tartalmazza az összes végrehajtott feladat összegzett listáját. Ezen az oldalon látható az állapot, a kezdési idő és a befejezési idő.

![Automation-fiók feladatok lapja](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

A feladatok listájának szűréséhez válassza a **feladatok szűrése** és a szűrés egy adott runbook, feladat állapota vagy a legördülő listában, valamint a keresendő időtartomány elemet.

![Szűrési feladatok állapota](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Azt is megteheti, hogy megtekinti az adott runbook tartozó feladat összegzésének részleteit úgy, hogy kijelöli a runbook az Automation-fiók **runbookok** lapján, majd kiválasztja a **feladatok** csempét. Ez a művelet megjeleníti a **feladatok** lapot, és innen a feladat rekordra kattintva megtekintheti annak részleteit és kimenetét.

![Automation-fiók feladatok lapja](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Feladat összegzése

Megtekintheti az adott runbook létrehozott összes feladat listáját, valamint a legutóbbi állapotukat is. A listát szűrheti a feladatok állapota és a feladatok utolsó módosításának időtartománya alapján. A részletes információk és a kimenet megtekintéséhez kattintson a feladatok nevére. A feladatok részletes nézete tartalmazza az adott feladatokhoz megadott runbook paraméterek értékeit.

Az alábbi lépésekkel megtekintheti a runbook feladatait.

1. A Azure Portal válassza az **Automation** lehetőséget, majd válassza ki az Automation-fiók nevét.
2. A központból válassza a **runbookok** lehetőséget, majd a **runbookok** lapon válasszon ki egy runbook a listából.
3. A kiválasztott runbook tartozó lapon kattintson a **feladatok** csempére.
4. Kattintson a listában szereplő egyik feladatra, és a runbook feladat részletei lapon megtekintheti annak részleteit és kimenetét.

## <a name="retrieving-job-status-using-powershell"></a>A feladatok állapotának beolvasása a PowerShell használatával

A [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) használatával lekérheti a runbook létrehozott feladatokat és egy adott feladat részleteit. Ha elindítja a runbook a PowerShell használatával a [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)paranccsal, akkor az eredményül kapott feladatot adja vissza. A [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) használatával lekérheti a feladatok kimenetét.

A következő minta-parancsok lekérik az utolsó feladatot egy minta runbook, és megjelenítik az állapotukat, a runbook paraméterekhez megadott értékeket, valamint a feladatokból származó kimenetet.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

A következő minta egy adott feladathoz tartozó kimenetet kérdez le, és visszaadja az egyes rekordokat. Abban az esetben, ha az egyik rekord esetében kivétel történt, a kivételt az érték helyett ki kell írni. Ez a viselkedés akkor lehet hasznos, ha a kivételek további információkat biztosítanak, amelyek a kimenet során általában nem naplózhatók.

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

## <a name="get-details-from-activity-log"></a>Részletek beolvasása a tevékenység naplójából

Az Automation-fiókhoz tartozó tevékenység naplójában további részleteket, például a runbook elindító személyt vagy fiókot lehet beolvasni. A következő PowerShell-példa az utolsó felhasználót kínálja a runbook futtatásához:

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

## <a name="fair-share"></a>Méltányos megosztás

Ha az erőforrásokat a felhő összes runbookok között meg szeretné osztani, Azure Automation ideiglenesen eltávolíthatja vagy leállíthatja a három óránál hosszabb ideig futó feladatokat. A [PowerShell-alapú runbookok](automation-runbook-types.md#powershell-runbooks) és a [Python runbookok](automation-runbook-types.md#python-runbooks) kapcsolatos feladatok leállnak, és nem indulnak újra, és a feladat állapota leáll.

A hosszú ideig futó feladatok esetében javasoljuk, hogy használjon [hibrid Runbook-feldolgozót](automation-hrw-run-runbooks.md#job-behavior). A hibrid Runbook-feldolgozók nem korlátozódnak a méltányos megosztásra, és nincs korlátozás arra vonatkozóan, hogy mennyi ideig lehet végrehajtani a Runbook. A többi [feladattípus az](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) Azure-beli és a hibrid Runbook-feldolgozókra is érvényes. Míg a hibrid Runbook-feldolgozók nem korlátozzák a 3 órás igazságos megosztási korlátot, a runbookok futtatását úgy kell kialakítani, hogy támogassa a váratlan helyi infrastrukturális problémák miatti újraindítási viselkedést.

Egy másik lehetőség, hogy optimalizálja a runbook a gyermek runbookok használatával. Ha a runbook több erőforráson ugyanazt a függvényt futtatja, például egy adatbázis-művelet több adatbázison, akkor áthelyezheti a függvényt egy [alárendelt runbook](automation-child-runbooks.md) , és meghívhatja azt a [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) parancsmaggal. Ezek az alárendelt runbookok külön folyamatokban párhuzamosan hajthatók végre. Ez a viselkedés csökkenti a szülő runbook befejezésének teljes időtartamát. Használhatja a [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) parancsmagot a runbook, hogy ellenőrizze a feladatok állapotát az egyes gyermekekhez, ha vannak olyan műveletek, amelyek a gyermek runbook befejezése után végeznek műveleteket.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a runbook elindításához használható különböző módszerekről Azure Automationban, tekintse meg a [Runbook elindítása Azure Automation](automation-starting-a-runbook.md)
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/powershell/scripting/overview).
