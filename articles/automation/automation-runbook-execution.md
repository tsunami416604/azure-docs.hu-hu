---
title: Runbook-végrehajtás az Azure Automationben
description: Az Azure Automation ben egy runbook feldolgozásának részleteit ismerteti.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 1907eb7cde482927ee8e6b0a2522158f05c1808f
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010936"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook-végrehajtás az Azure Automationben

A Runbookok végrehajtása a bennük definiált logika alapján hajtható végre. Ha egy runbook megszakad, a runbook az elején újraindul. Ez a viselkedés megköveteli, hogy írjon runbookok, amelyek támogatják az újraindítást, ha átmeneti problémák fordulnak elő.

Runbook indítása az Azure Automationben létrehoz egy feladatot, amely a runbook egyetlen végrehajtási példányát hozza létre. Minden feladat rendelkezik az Azure-erőforrásokhoz való hozzáféréssel az Azure-előfizetéshez való csatlakozással. A feladat csak akkor rendelkezik az adatközpont erőforrásaihoz, ha ezek az erőforrások elérhetők a nyilvános felhőből.

Az Azure Automation egy dolgozót rendel hozzá az egyes feladatok runbook-végrehajtás során történő futtatásához. Bár a dolgozókat számos Azure-fiók osztja meg, a különböző Automation-fiókokból származó feladatok elkülönülnek egymástól. Nincs szabályozhatja, hogy mely munkavégző szolgáltatások a munkakérelmet.

Amikor az Azure Portalon a runbookok listáját tekinti meg, az egyes runbookokhoz elindított egyes feladatok állapotát jeleníti meg. Az Azure Automation legfeljebb 30 napig tárolja a feladatnaplókat. 

Az alábbi ábra a [PowerShell-runbookok,](automation-runbook-types.md#powershell-runbooks)a [grafikus runbookok](automation-runbook-types.md#graphical-runbooks)és a [PowerShell-munkafolyamat runbookok](automation-runbook-types.md#powershell-workflow-runbooks)runbook-feladatának életciklusát mutatja be.

![Feladatállapotok – PowerShell-munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="where-to-run-your-runbooks"></a>A runbookok futtatásának helye

Az Azure Automation ben futó könyvek azure-sandboxon vagy [hibrid runbook-feldolgozón](automation-hybrid-runbook-worker.md)futtathatók. A legtöbb runbook könnyen futtatható egy Azure sandbox, egy megosztott környezetben, amely több feladat használható. Az azonos sandboxot használó feladatokat a sandbox erőforrás-korlátai kötik.

A hibrid Runbook-feldolgozó használatával futtassa a runbookok közvetlenül a számítógépen, amely a szerepkört és a környezetben lévő helyi erőforrások ellen fut. Az Azure Automation tárolja és kezeli a runbookokat, majd egy vagy több hozzárendelt számítógéphez kézbesíti azokat.

Az alábbi táblázat néhány runbook-végrehajtási feladatot sorol fel az egyes rendszerű ajánlott végrehajtási környezettel.

|Tevékenység|Legjobb választás|Megjegyzések|
|---|---|---|
|Integrálás az Azure-erőforrásokkal|Azure Sandbox|Az Azure-ban üzemeltetett hitelesítés egyszerűbb. Ha egy azure-beli virtuális gépen hibrid Runbook-feldolgozót használ, felügyelt identitásokat használhat [az Azure-erőforrásokhoz.](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Optimális teljesítmény elérése az Azure-erőforrások kezeléséhez|Azure Sandbox|A parancsfájl ugyanabban a környezetben fut, amely kevésbé késleltetéssel rendelkezik.|
|Minimalizálja a működési költségeket|Azure Sandbox|Nincs számítási terhelés, és nincs szükség a virtuális gép.|
|Hosszú ideig futó parancsfájl végrehajtása|hibrid runbook-feldolgozó|Az Azure sandboxes [korlátozásokat erőforrásokat.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Együttműködés a helyi szolgáltatásokkal|hibrid runbook-feldolgozó|Közvetlenül hozzáférhet a gazdagéphez.|
|Külső gyártótól származó szoftverek és végrehajtható fájlok megkövetelése|hibrid runbook-feldolgozó|Ön kezeli az operációs rendszert, és szoftvereket telepíthet.|
|Runbookot tartalmazó fájl vagy mappa figyelése|hibrid runbook-feldolgozó|[Watcher-feladat](automation-watchers-tutorial.md) használata hibrid runbook-feldolgozón.|
|Erőforrás-igényes parancsfájl futtatása|hibrid runbook-feldolgozó| Az Azure sandboxes [korlátozásokat erőforrásokat.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Speciális követelményekkel rendelkező modulok használata| hibrid runbook-feldolgozó|Néhány példa:</br> WinSCP - függőség winscp.exe </br> IISAdministration – az IIS engedélyezésétől való függés.|
|Modul telepítése telepítővel|hibrid runbook-feldolgozó|A sandbox moduljainak támogatniuk kell a másolást.|
|A 4.7.2-es től eltérő .|hibrid runbook-feldolgozó|Az automatizálási homokozók .NET Framework 4.7.2-es rendszerekkel rendelkeznek, és nem lehet frissíteni.|
|Szintemelést igénylő parancsfájlok futtatása|hibrid runbook-feldolgozó|A sandboxok nem engedélyezik a magasságot. A hibrid Runbook-feldolgozó val kikapcsolhatja az UAC-t, és használhatja az **Invoke-Command** parancsot a jogosultságszint-emelést igénylő parancs futtatásakor.|
|WMI-hez való hozzáférést igénylő parancsfájlok futtatása|hibrid runbook-feldolgozó|A felhőben lévő homokozókban futó feladatok nem férnek hozzá a WMI-hez. |

## <a name="runbook-behavior"></a>Runbook viselkedése

### <a name="creating-resources"></a>Erőforrások létrehozása

Ha a runbook létrehoz egy erőforrást, a parancsfájlnak ellenőriznie kell, hogy az erőforrás már létezik-e, mielőtt megpróbálna létrehozni. Íme egy alapvető példa.

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

A runbookoknak robusztusnak kell lenniük, és képeseknek kell lenniük az olyan átmeneti hibák kezelésére, amelyek újraindításukat vagy sikertelenségüket okozhatják. Ha egy runbook sikertelen, az Azure Automation újrapróbálkozik vele.

Ha a runbook általában egy időkorláton belül fut, a parancsfájl implementálja a logikát a végrehajtási idő ellenőrzéséhez. Ez az ellenőrzés csak meghatározott időszakokban biztosítja az olyan műveletek futtatását, mint az indítás, a leállítás vagy a horizontális felskálázás.

> [!NOTE]
> Az Azure sandbox folyamat helyi ideje UTC. A runbookokban a dátumra és az időre vonatkozó számításoknak ezt a tényt figyelembe kell venniük.

### <a name="tracking-progress"></a>A folyamat nyomon követése

Ez egy jó gyakorlat, hogy a runbookok, hogy moduláris jellegű, strukturálása runbook logika, hogy újra fel lehet használni, és könnyen újraindítható. A runbookok állapotának nyomon követése egy jó módja annak, hogy a Runbook-logika megfelelően hajtsa végre, ha problémák vannak. A runbookok előrehaladását külső forrás, például tárfiók, adatbázis vagy megosztott fájlok használatával lehet nyomon követni. A runbook logikáját a legutóbbi művelet állapotának ellenőrzéséhez először is létrehozhatja. Ezután az ellenőrzés eredménye alapján a logika vagy kihagyja, vagy továbbra is bizonyos feladatokat a runbook.

### <a name="preventing-concurrent-jobs"></a>Az egyidejű feladatok megelőzése

Egyes runbookok furcsán viselkednek, ha egyszerre több feladaton futnak keresztül. Ebben az esetben fontos, hogy egy runbook logikai eszközök bevezetéséhez, hogy megállapítsa, van-e már egy futó feladat. Íme egy alapvető példa.

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

Több előfizetés kezeléséhez a runbooknak a [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) parancsmag használatával kell biztosítania, hogy a hitelesítési környezet ne legyen lehívva egy másik, ugyanabban a rendszerű futó runbookból. A runbook is`AzContext` használja a paramétert az Az modul parancsmagok és átadja azt a megfelelő környezetben.

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

Ez a szakasz néhány, a runbookokban lévő kivételek vagy időszakos problémák kezelésére.

#### <a name="erroractionpreference"></a>ErrorActionPreference

Az [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) változó határozza meg, hogy a PowerShell hogyan reagál egy nem végződő hibára. A megszakítási hibák mindig megszűnnek, és *azerrorActionPreference*nem érinti őket.

Amikor a runbook használja, `ErrorActionPreference`egy általában nem végződő hiba, például **pathNotFound** a `Get-ChildItem` parancsmagból leállítja a runbook befejezését. A következő példa a `ErrorActionPreference`használatát mutatja be. A `Write-Output` végső parancs soha nem hajtható végre, ahogy a parancsfájl leáll.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Próbálja catch végül

[Próbálja catch végül](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) a PowerShell-parancsfájlok a leírási hibák kezelésére. A parancsfájl ezzel a mechanizmussal lekaphat bizonyos kivételeket vagy általános kivételeket. Az `catch` utasítást a hibák nyomon követésére vagy kezelésére kell használni. A következő példa nem létező fájlt próbál letölteni. Elkapja `System.Net.WebException` a kivételt, és az utolsó értéket adja vissza bármely más kivételhez.

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

#### <a name="throw"></a>Dobja

[A dobással](/powershell/module/microsoft.powershell.core/about/about_throw) végződési hibát hozhat létre. Ez a mechanizmus hasznos lehet, ha a saját logikáját definiálja egy runbookban. Ha a parancsfájl megfelel egy feltételnek, amely `throw` nek meg kell állítania, használhatja az utasítást a leállításhoz. A következő példa ezt az utasítást használja a szükséges függvényparaméter megjelenítéséhez.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Végrehajtható fájlok vagy hívó folyamatok használata

Az Azure sandboxokban futó Runbookok nem támogatják a hívási folyamatokat, például a végrehajtható fájlokat (**.exe** fájlok) vagy az alfolyamatokat.  Ennek az az oka, hogy az Azure sandbox egy megosztott folyamat fut egy tárolóban, amely nem fér hozzá az összes alapul szolgáló API-k. Harmadik féltől származó szoftvert vagy alfolyamatokhívásokat igénylő esetekben ajánlott runbookot végrehajtani egy [hibrid Runbook-feldolgozón.](automation-hybrid-runbook-worker.md)

### <a name="accessing-device-and-application-characteristics"></a>Az eszköz és az alkalmazás jellemzőinek elérése

Az Azure sandboxokban futó Runbook-feladatok nem férnek hozzá semmilyen eszköz- vagy alkalmazásjellemzőkhöz. A Windows teljesítménymutatóinak lekérdezésére használt leggyakoribb API a WMI, amelynek néhány gyakori metrikája a memória és a PROCESSZOR-használat. Azonban nem számít, milyen API-t használ, mivel a felhőben futó feladatok nem férnek hozzá a Microsoft webalapú vállalati felügyeleti (WBEM) implementációjához. Ez a platform a közös információs modellre (COMMON Information Model, CIM) épül, amely az eszköz- és alkalmazásjellemzők meghatározására vonatkozó iparági szabványokat biztosítja.

## <a name="handling-errors"></a>Hibák kezelése

A runbookok nak képesnek kell lenniük a hibák kezelésére. A PowerShell kétféle hibát tartalmaz: a leírást és a nem végződést. A hibák leállítása leállítja a runbook végrehajtását, amikor azok bekövetkeznek. A runbook leáll egy feladat állapota Sikertelen.

A nem végződő hibák lehetővé teszik, hogy a parancsfájl oka idát követően is folytatódjon. Egy példa a nem végződő hiba az egyik, amely akkor `Get-ChildItem` fordul elő, amikor egy runbook használja a parancsmag egy elérési utat, amely nem létezik. A PowerShell látja, hogy az elérési út nem létezik, hibát okoz, és folytatja a következő mappát. A hiba ebben az esetben nem állítja be a runbook-feladat állapotát Sikertelen állapotra, és a feladat akár el is fejeződhet. Ha egy runbookot nem végződő hiba esetén le szeretne `-ErrorAction Stop` állítani, használhatja a parancsmagon.

## <a name="handling-jobs"></a>Feladatok kezelése

A végrehajtási környezetet újra felhasználhatja az azonos Automation-fiókból származó feladatokhoz. Egy runbook egyszerre több feladat futtatásával is rendelkezhet. Minél több feladatot futtat egyszerre, annál gyakrabban küldhetők ugyanarra a homokozóba.

Az ugyanabban a sandbox-folyamatban futó feladatok hatással lehetnek egymásra. Erre példa a `Disconnect-AzAccount` parancsmag futtatása. A parancsmag végrehajtása leválasztja az egyes runbook-feladatot a megosztott sandbox-folyamatban.

Előfordulhat, hogy az Azure-sandboxban futó runbookról indított PowerShell-feladatok nem futnak teljes nyelvi módban. Ha többet szeretne megtudni a PowerShell nyelvi módjairól, olvassa el a [PowerShell nyelvi módok .](/powershell/module/microsoft.powershell.core/about/about_language_modes) Az Azure Automation-feladatok használatával kapcsolatos további részletekért olvassa el a [Feladatállapot beolvasása a PowerShell használatával.](#retrieving-job-status-using-powershell)

### <a name="job-statuses"></a>Feladat állapotai

Az alábbi táblázat a feladathoz lehetséges állapotokat ismerteti.

| status | Leírás |
|:--- |:--- |
| Befejezve |A feladat sikeresen befejeződött. |
| Sikertelen |Egy grafikus vagy PowerShell-munkafolyamat-runbook fordítása nem sikerült. Egy PowerShell-parancsfájl runbook nem tudott elindulni, vagy a feladat kivételt kapott. Lásd: [Azure Automation runbook típusok.](automation-runbook-types.md)|
| Sikertelen, erőforrásokra vár |A feladat nem sikerült, mert elérte a [méltányos megosztási](#fair-share) korlátot háromszor, és minden alkalommal ugyanattól az ellenőrzőpontról vagy a runbook kezdetétől indult. |
| Várólistán |A feladat arra vár, hogy egy Automation-dolgozó erőforrásai elérhetővé váljanak, hogy el lehessen indítani. |
| Indítás |A feladat egy dolgozóhoz van rendelve, és a rendszer elindítja azt. |
| Folytatás |A rendszer a felfüggesztés után folytatja a feladatot. |
| Fut |A feladat fut. |
| Futás, erőforrásokra való várakozás |A feladat kilett töltve, mert elérte a méltányos részesedési korlátot. Hamarosan folytatódik az utolsó ellenőrzőpontról. |
| Leállítva |A feladatot leállította a felhasználó, mielőtt az befejeződött volna. |
| Leállítás |A rendszer leállítja a munkát. |
| Felfüggesztve |Csak [a grafikus és a PowerShell-munkafolyamat runbookokra vonatkozik.](automation-runbook-types.md) A felhasználó, a rendszer vagy a runbook egy parancsa felfüggesztette a feladatot. Ha egy runbook nem rendelkezik ellenőrzőponttal, az elejétől kezdődik. Ha rendelkezik ellenőrzőponttal, újraindíthatja, és folytathatja az utolsó ellenőrzőpontról. A rendszer csak akkor függeszti fel a runbookot, ha kivétel történik. Alapértelmezés szerint `ErrorActionPreference` a változó beállítása Continue, jelezve, hogy a feladat továbbra is fut egy hiba. Ha a beállításváltozó Leállítás, a feladat egy hiba miatt felfüggesztésre kerül.  |
| Felfüggesztés |Csak [a grafikus és a PowerShell-munkafolyamat runbookokra vonatkozik.](automation-runbook-types.md) A rendszer a felhasználó kérésére megpróbálja felfüggeszteni a feladatot. A runbooknak el kell érnie a következő ellenőrzőpontot a felfüggesztés előtt. Ha már átment az utolsó ellenőrzőponton, akkor a felfüggesztés előtt befejeződik. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Feladatállapot megtekintése az Azure Portalról

Megtekintheti az összes runbook-feladat összesített állapotát, vagy részletezheti egy adott runbook-feladat részleteit az Azure Portalon. A Log Analytics-munkaterülettel való integrációt a runbook-feladat állapotának és a feladatstreamek továbbításához is konfigurálhatja. Az Azure Monitor naplóival való integrációról a [Feladat állapotának továbbítása és feladatstreamek az Automationből az Azure Monitor-naplókba](automation-manage-send-joblogs-log-analytics.md)című témakörben talál további információt.

A kiválasztott Automation-fiók jobb oldalán az összes runbook-feladat összegzését a Feladatstatisztika csempén **tekintheti** meg.

![Állásstatisztika csempe](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ez a csempe minden egyes végrehajtott feladat feladat állapotának számát és grafikus ábrázolását jeleníti meg.

A csempére kattintva megjelenik a Feladatok lap, amely tartalmazza az összes végrehajtott feladat összesített listáját. Ezen a lapon az állapot, a runbook neve, a kezdési idő és a befejezési idő az egyes feladat.

![Automatizálási fiók feladatok lapja](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

A feladatok listájának szűréséhez válassza **a Feladatok szűrése**lehetőséget. Szűrjön egy adott runbookra, feladatállapotra vagy egy választásra a legördülő listából, és adja meg a keresés időtartományát.

![Feladat állapotának szűrése](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Másik lehetőségként megtekintheti egy adott runbook feladat-összefoglaló részleteit, ha kiválasztja a runbookot az Automation-fiók Runbookok lapjáról, majd a **Feladatok** csempét. Ez a művelet a Feladatok lapot mutatja be. Itt a projektrekordra kattintva megtekintheti annak részleteit és kimenetét.

![Automatizálási fiók feladatok lapja](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>A feladat összegzésének megtekintése

A fenti feladatösszegzés lehetővé teszi, hogy tekintse meg az adott runbookhoz létrehozott összes feladat listáját és azok legutóbbi állapotát. Egy feladat részletes információinak és kimenetének megtekintéséhez kattintson a nevére a listában. A feladat részletes nézete tartalmazza az adott feladathoz megadott Runbook-paraméterek értékeit.

Az egy adott runbookhoz tartozó feladatok a következő lépésekkel tekinthetők meg.

1. Az Azure Portalon válassza **az Automatizálás** lehetőséget, majd válassza ki egy Automation-fiók nevét.
2. A hubon válassza a **Runbookok** lehetőséget a **Folyamatautomatizálás csoportban.**
3. A Runbooks lapon válasszon ki egy runbookot a listából.
3. A kijelölt runbook lapján kattintson a **Feladatok** csempére.
4. Kattintson a listában szereplő feladatok egyikére, és tekintse meg annak részleteit és kimenetét a runbook-feladat részletei lapon.

### <a name="retrieving-job-status-using-powershell"></a>Feladatállapot beolvasása a PowerShell használatával

A `Get-AzAutomationJob` parancsmag segítségével lekérheti a runbookhoz létrehozott feladatokat és egy adott feladat részleteit. Ha egy Runbook powershell `Start-AzAutomationRunbook`használatával indít, az eredményül kapott feladatot adja vissza. A [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) használatával lekérheti a feladat kimenetét.

A következő példa lekéri az utolsó feladat egy minta runbook, és megjeleníti annak állapotát, a runbook-paraméterek megadott értékeket, és a feladat kimeneti.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

A következő példa egy adott feladat kimenetét olvassa be, és minden rekordot visszaad. Ha az egyik rekord kivételt képez, a parancsfájl a kivételt írja az érték helyett. Ez a viselkedés hasznos, mivel a kivételek további információkat nyújthatnak, amelyek nem lehetnek a kimenet során általában naplózva.

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

## <a name="getting-details-from-the-activity-log"></a>Részletek beszerzése a tevékenységnaplóból

A runbook részletei, például a runbookot elvezető személy vagy fiók az Automation-fiók tevékenységnaplójából lekérheti. A következő PowerShell-példa biztosítja az utolsó felhasználó számára a megadott runbook futtatásához.

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Erőforrások megosztása runbookok között

Erőforrások megosztása a felhőben lévő összes runbookok között, az Azure Automation ideiglenesen eltávolítja vagy leállítja a több mint három órán át futó feladat. [PowerShell runbookok](automation-runbook-types.md#powershell-runbooks) és [a Python runbookok](automation-runbook-types.md#python-runbooks) feladatok leállnak, és nem újraindul, és a feladat állapota leáll.

Hosszú ideig futó feladatok hoz ajánlott egy hibrid Runbook-feldolgozó használata. A hibrid Runbook-dolgozókat nem korlátozza a méltányos megosztás, és nincs korlátozás a runbook végrehajtható élettartamára vonatkozóan. A többi [feladatkorlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) az Azure sandboxes és a hibrid Runbook-feldolgozók egyaránt vonatkoznak. Míg a hibrid Runbook-dolgozók nincskorlátozva a 3 órás méltányos megosztási korlát, ki kell dolgoznia runbookok futtatásához a dolgozók, amelyek támogatják a váratlan helyi infrastrukturális problémák újraindul.

Egy másik lehetőség a runbook oka a gyermek runbookok használatával történő optimalizálása. Előfordulhat például, hogy a runbook több erőforráson, például több adatbázison is végighalad ugyanazon a függvényen. Ezt a függvényt áthelyezheti egy [gyermek runbookba,](automation-child-runbooks.md) és a runbookot a használatával `Start-AzAutomationRunbook`hívhatja meg. A gyermek runbookok párhuzamosan, külön folyamatokban hajthatók végre.

Gyermek runbookok használata csökkenti a szülő runbook befejezéséhez szükséges teljes időt. A runbook használhatja a `Get-AzAutomationJob` parancsmag segítségével, hogy ellenőrizze a feladat állapotát egy gyermek runbook, ha továbbra is műveleteket kell végrehajtani a gyermek befejezése után.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a runbook okádékának az Azure Automationben történő indításához használható módszerekről, [olvassa el a Runbook indítása az Azure Automationben című témakört.](automation-starting-a-runbook.md)
* A PowerShellről további információt, beleértve a nyelvi referencia- és tanulási modulokat, olvassa el a [PowerShell-dokumentumok című dokumentumban.](https://docs.microsoft.com/powershell/scripting/overview)
