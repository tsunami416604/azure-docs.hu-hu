---
title: Runbook végrehajtás a Azure Automationban
description: Ismerteti, hogy a rendszer hogyan dolgozza fel a runbook a Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4070b004ee791a433b5aeb9e3e0cdd9662fb0429
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191146"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook végrehajtás a Azure Automationban

A runbookok végrehajtása a bennük definiált logika alapján végezhető el. Ha a runbook megszakad, a runbook az elején indul el. Ennek a viselkedésnek a működéséhez olyan runbookok kell írnia, amelyeken a támogatás átmeneti problémák esetén újraindul.

Egy runbook elindítása Azure Automation egy feladatot hoz létre. A feladatok a runbook egyetlen végrehajtási példánya. Az Azure-erőforrásokhoz való kapcsolódással minden egyes feladatokhoz hozzáfér az Azure-előfizetéshez. A feladatokhoz csak abban az esetben férhet hozzá az adatközpont erőforrásaihoz, ha ezek az erőforrások elérhetők a nyilvános felhőből.

Azure Automation hozzárendel egy feldolgozót az egyes feladatok futtatásához a runbook végrehajtása során. Míg a feldolgozókat számos Azure-fiók megosztja, a különböző Automation-fiókoktól származó feladatok egymástól el vannak különítve. Nem szabályozhatja, hogy a feldolgozói szolgáltatások mely munkavégző szolgáltatásokat kérik.

Amikor megtekinti a Azure Portal runbookok listáját, az egyes runbook elindított feladatok állapotát jeleníti meg. A Azure Automation a feladatok naplóit legfeljebb 30 napig tárolja. 

A következő ábra a [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks), a [grafikus Runbookok](automation-runbook-types.md#graphical-runbooks)és a [PowerShell-munkafolyamatok runbookok](automation-runbook-types.md#powershell-workflow-runbooks)tartozó runbook-feladatok életciklusát mutatja be.

![Feladatok állapota – PowerShell-munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>A runbookok futtatása

Azure Automation runbookok Azure-beli vagy [hibrid Runbook-feldolgozón](automation-hybrid-runbook-worker.md)is futtathatók. A legtöbb runbookok könnyen futtatható egy Azure-beli homokozóban, egy megosztott környezetben, amelyet több feladat is használhat. Az azonos Sandboxot használó feladatokat a rendszer a sandbox erőforrás-korlátaihoz köti.

A hibrid Runbook-feldolgozók használatával közvetlenül a runbookok futtathatók a szerepkört üzemeltető számítógépen és a helyi erőforrásokon a környezetben. Azure Automation tárolja és kezeli a runbookok, majd egy vagy több hozzárendelt számítógéphez továbbítja azokat.

Az alábbi táblázat néhány runbook végrehajtási feladatot sorol fel, amelyek az ajánlott végrehajtási környezettel rendelkeznek.

|Tevékenység|Legjobb választás|Megjegyzések|
|---|---|---|
|Integráció az Azure-erőforrásokkal|Azure-beli homokozó|Az Azure-ban üzemeltetett hitelesítés egyszerűbb. Ha hibrid Runbook-feldolgozót használ egy Azure-beli virtuális gépen, [felügyelt identitásokat használhat az Azure-erőforrásokhoz](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Optimális teljesítmény az Azure-erőforrások kezeléséhez|Azure-beli homokozó|A parancsfájl ugyanabban a környezetben fut, amelynek kevesebb a késése.|
|Működési költségek csökkentése|Azure-beli homokozó|Nincs számítási terhelés, és nincs szükség virtuális gépre.|
|Hosszan futó parancsfájl végrehajtása|hibrid runbook-feldolgozó|Az Azure-beli munkaterületek [korlátai vannak az erőforrásokkal kapcsolatban](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Helyi szolgáltatásokkal való kommunikáció|hibrid runbook-feldolgozó|Közvetlenül is hozzáférhet a gazdagéphez.|
|Harmadik féltől származó szoftverek és végrehajtható fájlok megkövetelése|hibrid runbook-feldolgozó|Kezelheti az operációs rendszert, és telepíthet szoftvereket.|
|Fájl vagy mappa figyelése runbook|hibrid runbook-feldolgozó|[Figyelő feladat](automation-watchers-tutorial.md) használata hibrid Runbook-feldolgozón.|
|Erőforrás-igényes parancsfájl futtatása|hibrid runbook-feldolgozó| Az Azure-beli munkaterületek [korlátai vannak az erőforrásokkal kapcsolatban](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Speciális követelményekkel rendelkező modulok használata| hibrid runbook-feldolgozó|Néhány példa:</br> Megnyerő – függőség a megnyert. exe fájltól </br> IISAdministration – függőség az IIS engedélyezésével.|
|Modul telepítése telepítővel|hibrid runbook-feldolgozó|A sandbox moduljainak támogatnia kell a másolást.|
|A 4.7.2-től eltérő .NET-keretrendszer verziót igénylő runbookok vagy modulok használata|hibrid runbook-feldolgozó|Az Automation-munkaterületeken a .NET-keretrendszer 4.7.2 van, és nincs lehetőség a frissítésre.|
|Jogosultságszint-emelést igénylő parancsfájlok futtatása|hibrid runbook-feldolgozó|A sandboxok nem engedélyezik a jogosultságszint-emelést. A hibrid Runbook-feldolgozók kikapcsolhatják az UAC-t, és a jogosultságszint-emelést igénylő parancs futtatásakor a **Meghívási parancsot** is használhatják.|
|WMI-hozzáférést igénylő parancsfájlok futtatása|hibrid runbook-feldolgozó|A Felhőbeli munkaterületeken futó feladatok nem rendelkeznek hozzáféréssel a WMI-hez. |

## <a name="runbook-behavior"></a>Runbook viselkedés

### <a name="creating-resources"></a>Erőforrások létrehozása

Ha a runbook létrehoz egy erőforrást, a parancsfájlnak ellenőriznie kell, hogy az erőforrás már létezik-e, mielőtt megpróbálja létrehozni. Íme egy alapszintű példa.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Időfüggő parancsfájlok támogatása

A runbookok robusztusnak kell lennie, és képesnek kell lennie az olyan átmeneti hibák kezelésére, amelyek újraindítást vagy meghibásodást okozhatnak. Ha egy runbook meghibásodik, Azure Automation újrapróbálkozik.

Ha a runbook általában egy adott időkorláton belül fut, a parancsfájl implementálja a végrehajtási időt a logikával. Ez az ellenőrzés biztosítja az olyan műveletek futtatását, mint az indítás, a Leállítás vagy a Kibővítés csak adott időpontokban.

> [!NOTE]
> Az Azure-beli homokozó folyamat helyi ideje UTC értékre van állítva. A runbookok dátumra és időpontra vonatkozó számításait figyelembe kell venni.

### <a name="tracking-progress"></a>Követési folyamat

Célszerű a runbookok moduláris jellegűvé tenni, strukturálni a runbook logikát, hogy könnyen felhasználható legyen, és újra lehessen indítani. A runbook nyomon követése jó módszer annak biztosítására, hogy a runbook logikája megfelelően fusson, ha problémák merülnek fel. A runbook állapotát egy külső forrás, például egy Storage-fiók, egy adatbázis vagy egy megosztott fájl használatával lehet nyomon követni. A runbook logikát is létrehozhat, hogy először ellenőrizzék az utolsó végrehajtott művelet állapotát. Ezután az ellenőrzés eredménye alapján a logikai művelet kihagyhatja vagy folytathatja a runbook adott feladatait.

### <a name="preventing-concurrent-jobs"></a>Egyidejű feladatok megakadályozása

Néhány runbookok furcsán viselkedik, ha egyszerre több feladaton futnak. Ebben az esetben fontos, hogy egy runbook logikát alkalmazzon annak megállapításához, hogy van-e már futó feladata. Íme egy alapszintű példa.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
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

Több előfizetés kezeléséhez a runbook a [disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) parancsmagot kell használnia annak biztosításához, hogy a hitelesítési környezet ne legyen lekérve egy másik, ugyanazon a homokozóban futó runbook. A runbook a *AzContext* paramétert is használja az az Module parancsmagokon, és a megfelelő környezetet adja át.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Kivételek kezelése

Ez a szakasz néhány módszert ismertet a runbookok kivételek vagy időszakos problémák kezelésére.

#### <a name="erroractionpreference"></a>$ErrorActionPreference

A [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) változó határozza meg, hogy a PowerShell hogyan válaszol a megszakítást nem okozó hibára. A hibák leállítása mindig megszűnik, és a *$ErrorActionPreference*nem érinti.

Ha a runbook *$ErrorActionPreference*használ, a normál megszakítást nem okozó hiba, például a **Get-ChildItem** parancsmag **PathNotFound** leállítja a runbook befejezését. Az alábbi példa a *$ErrorActionPreference*használatát mutatja be. A végső **írási-kimeneti** parancs soha nem hajtható végre, mert a szkript leáll.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Próbálja ki a fogást

[Próbálja meg a Catch végül](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) a PowerShell-szkriptekben használni a leállítási hibák kezelésére. A szkript ezzel a mechanizmussal meghatározott kivételeket vagy általános kivételeket tud kifogni. A **Catch** utasítás segítségével nyomon követheti vagy megpróbálhatja kezelni a hibákat. A következő példa egy nem létező fájl letöltését kísérli meg. A rendszer a .net. webszolgáltatások kivételét fogja kifogni, és a többi kivétel utolsó értékét adja vissza.

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

A [dob](/powershell/module/microsoft.powershell.core/about/about_throw) használatával leállítási hiba hozható fel. Ez a mechanizmus akkor lehet hasznos, ha saját logikát definiál egy runbook. Ha a parancsfájl egy olyan feltételnek felel meg, amelynek le kell állítania, a **Throw** utasítás leállítására alkalmas. A következő példa ezt az utasítást használja egy kötelező Function paraméter megjelenítéséhez.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Végrehajtható fájlok vagy hívási folyamatok használata

Az Azure-beli munkaterületeken futó runbookok nem támogatja a hívási folyamatokat, például a végrehajtható fájlok ( **. exe** fájlok) vagy az alfolyamatok használatát.  Ennek az az oka, hogy az Azure-beli sandbox egy olyan tárolóban fut, amely nem fér hozzá az összes mögöttes API-hoz. A külső gyártótól származó szoftvereket vagy az alfolyamatok meghívását igénylő forgatókönyvek esetén ajánlott runbook végrehajtani egy [hibrid runbook-feldolgozón](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Eszköz-és alkalmazás-jellemzők elérése

Az Azure-beli munkaterületeken futó Runbook-feladatok nem férhetnek hozzá semmilyen eszközhöz vagy alkalmazási tulajdonsághoz sem. A Windowson a teljesítmény-mérőszámok lekérdezéséhez használt leggyakoribb API a WMI, amely a memóriában és a CPU-használatban előforduló gyakori mérőszámokat is használja. Nem számít azonban, hogy milyen API-t használ, mivel a felhőben futó feladatok nem férnek hozzá a webalapú Enterprise Management (WBEM) Microsoft-megvalósításához. Ez a platform a CIMre (CIM) épül, amely az eszközök és alkalmazások jellemzőinek meghatározására szolgáló iparági szabványokat biztosítja.

## <a name="handling-errors"></a>Kezelési hibák

A runbookok képesnek kell lennie a hibák kezelésére. A PowerShell két típusú hibát tartalmaz, leáll, és nem leáll. Ha leállítja a hibákat, a runbook végrehajtása leáll. A runbook **nem sikerült, mert**a feladatok állapota sikertelen.

A nem megszakítást okozó hibák lehetővé teszik a parancsfájlok folytatását még azután is, hogy azok bekövetkeztek. A megszakítást nem okozó hiba például akkor fordul elő, ha egy runbook a **Get-ChildItem** parancsmagot olyan elérési úttal használja, amely nem létezik. A PowerShell látja, hogy az elérési út nem létezik, hibát jelez, és folytatja a következő mappába. A hiba ebben az esetben nem állítja be a runbook-állapot állapotát **sikertelen**értékre, és előfordulhat, hogy a feladatot még végre kell hajtani. Ha kényszeríteni szeretné, hogy egy runbook leálljon a megszakítást nem okozó hiba esetén, a parancsmagon `-ErrorAction Stop` is használhatja.

## <a name="handling-jobs"></a>Feladatok feldolgozása

Az azonos Automation-fiókból származó feladatok végrehajtási környezetét újra felhasználhatja. Egyetlen runbook egyszerre több feladat is futhat. Minél több feladatot futtat egyszerre, annál gyakrabban lehet ugyanarra a sandboxra elküldeni.

Az ugyanabban a homokozóban futó feladatok hatással lehetnek egymásra. Az egyik példa a **Disconnect-AzAccount** parancsmagot futtatja. Ennek a parancsmagnak a végrehajtása megszakítja az egyes runbook-feladatokat a megosztott homokozó folyamat során.

Előfordulhat, hogy az Azure-beli homokozóban futó runbook indított PowerShell-feladatok nem teljes nyelvi módban futnak. További információ a PowerShell nyelvi módjairól: [PowerShell nyelvi módok](/powershell/module/microsoft.powershell.core/about/about_language_modes). A Azure Automation feladataival való interakcióval kapcsolatos további részletekért lásd: [feladat állapotának beolvasása a PowerShell](#retrieving-job-status-using-powershell)-lel.

### <a name="job-statuses"></a>Feladatok állapota

A következő táblázat a feladatokhoz lehetséges állapotokat ismerteti.

| Állapot | Leírás |
|:--- |:--- |
| Befejezve |A feladat sikeresen befejeződött. |
| Sikertelen |A grafikus vagy a PowerShell-munkafolyamat runbook nem sikerült lefordítani. Egy PowerShell-parancsfájl runbook elindítása sikertelen volt, vagy a feladatokhoz kivétel történt. Lásd: [Azure Automation runbook-típusok](automation-runbook-types.md).|
| Sikertelen, várakozás erőforrásokra |A feladatot nem sikerült végrehajtani, mert elérte a [valós megosztási](#fair-share) korlátot háromszor, és ugyanabból az ellenőrzőpontból vagy a runbook elejétől indul el. |
| Várólistán |A művelet arra vár, hogy az automatizálási feldolgozón lévő erőforrások elérhetővé váljanak, hogy el lehessen indítani. |
| Indítás |A feladat hozzá lett rendelve egy feldolgozóhoz, és a rendszer megkezdi. |
| Folytatás |A rendszer a felfüggesztést követően folytatja a feladat folytatását. |
| Fut |A feladat fut. |
| Futtatás, várakozás erőforrásokra |A feladatot eltávolították a memóriából, mert elérte a méltányos megosztási korlátot. Hamarosan folytatódik az utolsó ellenőrzőponttól. |
| Leállítva |A feladatot leállította a felhasználó, mielőtt az befejeződött volna. |
| Leállítás |A rendszer leállítja a feladatot. |
| Felfüggesztve |Csak a [grafikus és a PowerShell munkafolyamat-runbookok](automation-runbook-types.md) vonatkozik. A felhasználó, a rendszer vagy a runbook egy parancsa felfüggesztette a feladatot. Ha egy runbook nem rendelkezik ellenőrzőponttal, az elejétől kezdődik. Ha ellenőrzőponttal rendelkezik, újra elindíthatja, és folytathatja az utolsó ellenőrzőpontot. A rendszer csak akkor felfüggeszti a runbook, ha kivétel történik. Alapértelmezés szerint a *ErrorActionPreference* változó a **Continue (folytatás**) értékre van állítva, ami azt jelzi, hogy a művelet hibán fut. Ha a preferencia változó **Leállítás**értékre van állítva, a művelet egy hibára felfüggeszti a feladatot.  |
| Felfüggesztés |Csak a [grafikus és a PowerShell munkafolyamat-runbookok](automation-runbook-types.md) vonatkozik. A rendszer megkísérli a feladat felfüggesztését a felhasználó kérésére. A runbook el kell érnie a következő ellenőrzőpontot a felfüggesztés előtt. Ha már elvégezte az utolsó ellenőrzőpontot, akkor a felfüggesztés előtt befejeződik. |

### <a name="viewing-job-status-from-the-azure-portal"></a>A feladatok állapotának megtekintése a Azure Portal

Megtekintheti az összes runbook-feladat összegzett állapotát, vagy részletezheti egy adott runbook-feladat részleteit a Azure Portalban. Az Log Analytics-munkaterülettel való integrációt úgy is konfigurálhatja, hogy továbbítsa a runbook feladatok állapotát és a feladatok folyamait. A Azure Monitor-naplók integrálásával kapcsolatos további információkért lásd: a [feladatok állapotának és a feladatok adatfolyamának továbbítása az automatizálásból a Azure monitor naplókba](automation-manage-send-joblogs-log-analytics.md).

A kiválasztott Automation-fiók jobb oldalán a **feladat statisztikái** csempén látható összes runbook-feladat összegzését láthatja.

![Feladatok statisztikájának csempe](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ez a csempe a feladatok állapotának számát és grafikus ábrázolását jeleníti meg minden végrehajtott feladathoz.

A csempére kattintva megjelenítheti a **feladatok** lapot, amely tartalmazza az összes végrehajtott feladat összegzett listáját. Ezen a lapon látható az egyes feladatokhoz tartozó állapot, a runbook neve, a kezdési idő és a befejezési idő.

![Automation-fiók feladatok lapja](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

A feladatok listájának szűréséhez kattintson a **feladatok szűrése**lehetőségre. Szűrje egy adott runbook, a feladatok állapotát, vagy válasszon a legördülő listából, és adja meg a keresés időtartományát.

![Szűrési feladatok állapota](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Azt is megteheti, hogy megtekinti az adott runbook tartozó feladat összegzésének részleteit úgy, hogy kijelöli a runbook az Automation-fiók **runbookok** lapján, majd kiválasztja a **feladatok** csempét. Ez a művelet a **feladatok** lapot mutatja be. Innen a feladatra kattintva megtekintheti annak adatait és kimenetét.

![Automation-fiók feladatok lapja](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>A feladatok összegzésének megtekintése

A fent leírt feladat-összefoglalás lehetővé teszi, hogy megtekintse az adott runbook létrehozott összes feladat listáját, valamint a legutóbbi állapotukat. A feladatok részletes információinak és kimenetének megtekintéséhez kattintson a nevére a listában. A feladatok részletes nézete tartalmazza az adott feladatokhoz megadott runbook paraméterek értékeit.

Az egy adott runbookhoz tartozó feladatok a következő lépésekkel tekinthetők meg.

1. A Azure Portal válassza az **Automation** lehetőséget, majd válassza ki az Automation-fiók nevét.
2. A központban válassza a **runbookok** lehetőséget a **folyamat automatizálása**alatt.
3. A **runbookok** lapon válasszon ki egy runbook a listából.
3. A kiválasztott runbook tartozó lapon kattintson a **feladatok** csempére.
4. Kattintson a listában szereplő egyik feladatra, és tekintse meg a részleteket és a kimenetet a runbook feladat részletei lapon.

### <a name="retrieving-job-status-using-powershell"></a>A feladatok állapotának beolvasása a PowerShell használatával

A **Get-AzAutomationJob** parancsmaggal kérheti le a runbook létrehozott feladatokat, valamint egy adott feladat részleteit. Ha elindítja a runbook a PowerShell-lel a **Start-AzAutomationRunbook**használatával, az eredményül kapott feladatot adja vissza. A [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) használatával kérheti le a feladatok kimenetét.

A következő példa beolvassa a minta runbook utolsó feltételét, és megjeleníti annak állapotát, a runbook paraméterekhez megadott értékeket, valamint a feladatok kimenetét.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

A következő példa egy adott feladathoz tartozó kimenetet kérdez le, és visszaadja az egyes rekordokat. Ha a rekordok egyike kivételt képez, a parancsfájl az érték helyett a kivételt írja. Ez a viselkedés hasznos lehet, mivel a kivételek olyan további információkat is biztosíthatnak, amelyek a kimenet során általában nem lesznek naplózva.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="getting-details-from-the-activity-log"></a>Részletek beolvasása a tevékenység naplójából

Lekérheti a runbook adatait, például a runbook elindító személyt vagy fiókot az Automation-fiók tevékenységi naplójából. A következő PowerShell-példa az utolsó felhasználót adja meg a megadott runbook futtatásához.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>Erőforrások megosztása a runbookok között

Ha az erőforrásokat a felhő összes runbookok között meg szeretné osztani, Azure Automation ideiglenesen eltávolíthatja vagy leállíthatja a három óránál hosszabb ideig futó feladatokat. A [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks) és a [Python-runbookok](automation-runbook-types.md#python-runbooks) kapcsolatos feladatok leállnak, és nem indulnak újra, és a feladat állapota **leáll**.

A hosszan futó feladatok esetében ajánlott hibrid Runbook-feldolgozót használni. A hibrid Runbook-feldolgozók nem korlátozódnak a méltányos megosztásra, és nincs korlátozás arra vonatkozóan, hogy mennyi ideig lehet végrehajtani a Runbook. A többi [feladattípus az](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) Azure-beli és a hibrid Runbook-feldolgozókra is érvényes. Amíg a hibrid Runbook-feldolgozók nem korlátozzák a 3 órás igazságos megosztási korlátot, akkor olyan runbookok kell fejlesztenie, amelyek támogatják a váratlan helyi infrastruktúra-problémákról való újraindítást.

Egy másik lehetőség, hogy optimalizálja a runbook a gyermek runbookok használatával. Előfordulhat például, hogy a runbook több erőforráson ugyanazt a függvényt, például egy adatbázis-műveletet több adatbázison is át lehet ismételni. Ezt a függvényt áthelyezheti egy [alárendelt runbook](automation-child-runbooks.md) , és a runbook meghívja azt a **Start-AzAutomationRunbook**használatával. A gyermek runbookok párhuzamosan hajthatók végre külön folyamatokban.

A gyermek runbookok használata csökkenti a szülő runbook befejezésének teljes időtartamát. A runbook használhatja a **Get-AzAutomationJob** parancsmagot, hogy ellenőrizze a gyermek runbook tartozó feladatok állapotát, ha még a gyermek befejeződése után is végrehajtja a műveleteket.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a Azure Automation runbook indításához használható módszerekről, tekintse meg a [runbook Azure Automation-ben](automation-starting-a-runbook.md)való elindításával foglalkozó témakört.
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/powershell/scripting/overview).
