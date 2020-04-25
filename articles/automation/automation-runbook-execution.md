---
title: Runbook végrehajtása az Azure Automationben
description: Ismerteti, hogy a rendszer hogyan dolgozza fel a runbook a Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 09122581a3ade4e741a29996b7202ce0f96d074b
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145532"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook végrehajtása az Azure Automationben

A Azure Automation folyamatok automatizálása lehetővé teszi a PowerShell, a PowerShell-munkafolyamatok és a grafikus runbookok létrehozását és kezelését. Részletekért lásd: [Azure Automation runbookok](automation-runbook-types.md). 

Az Automation a bennük definiált logikán alapuló runbookok hajtja végre. Ha egy runbook megszakad, a rendszer az elején újraindítja. Ennek a viselkedésnek a működéséhez olyan runbookok kell írnia, amelyeken a támogatás átmeneti problémák esetén újraindul.

Egy runbook elindítása Azure Automation létrehoz egy feladatot, amely a runbook egyetlen végrehajtási példánya. Minden egyes feladatokhoz hozzáfér az Azure-erőforrásokhoz az Azure-előfizetéshez való kapcsolódással. A feladatok csak akkor férhetnek hozzá az adatközpont erőforrásaihoz, ha ezek az erőforrások elérhetők a nyilvános felhőből.

Azure Automation hozzárendel egy feldolgozót az egyes feladatok futtatásához a runbook végrehajtása során. Míg a feldolgozókat számos Azure-fiók megosztja, a különböző Automation-fiókoktól származó feladatok egymástól el vannak különítve. Nem szabályozhatja, hogy mely munkavégző szolgáltatások számára kéri a feladatokat.

Amikor megtekinti a Azure Portal runbookok listáját, az egyes runbook elindított feladatok állapotát jeleníti meg. A Azure Automation a feladatok naplóit legfeljebb 30 napig tárolja.

Az alábbi ábrán a [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks), a [PowerShell-munkafolyamatok runbookok](automation-runbook-types.md#powershell-workflow-runbooks)és a [grafikus runbookok](automation-runbook-types.md#graphical-runbooks)tartozó runbook-feladatok életciklusa látható.

![Feladatok állapota – PowerShell-munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>A runbookok futtatása

Azure Automation runbookok Azure-beli vagy [hibrid Runbook-feldolgozón](automation-hybrid-runbook-worker.md)is futtathatók. Ha a runbookok az Azure-ban lévő erőforrásokkal történő hitelesítésre és futtatásra tervezték, akkor egy Azure-beli homokozóban futnak, amely egy megosztott környezet, amelyet több feladat használhat. Az azonos Sandboxot használó feladatokat a rendszer a sandbox erőforrás-korlátaihoz köti.

>[!NOTE]
>Az Azure-beli homokozó környezet nem támogatja az interaktív műveleteket. Emellett a Win32-hívásokat használó runbookok helyi MOF-fájlok használatát is szükségessé teszi.

A hibrid Runbook-feldolgozók használatával közvetlenül a runbookok futtathatók a szerepkört üzemeltető számítógépen és a helyi erőforrásokon a környezetben. Azure Automation tárolja és kezeli a runbookok, majd egy vagy több hozzárendelt számítógéphez továbbítja azokat.

Az alábbi táblázat néhány runbook végrehajtási feladatot sorol fel, amelyek az ajánlott végrehajtási környezettel rendelkeznek.

|Tevékenység|Ajánlás|Megjegyzések|
|---|---|---|
|Integráció az Azure-erőforrásokkal|Azure-beli homokozó|Az Azure-ban üzemeltetett hitelesítés egyszerűbb. Ha hibrid Runbook-feldolgozót használ egy Azure-beli virtuális gépen, [felügyelt identitásokat használhat az Azure-erőforrásokhoz](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Optimális teljesítmény az Azure-erőforrások kezeléséhez|Azure-beli homokozó|A parancsfájl ugyanabban a környezetben fut, amelynek kevesebb a késése.|
|Működési költségek csökkentése|Azure-beli homokozó|Nincs számítási terhelés, és nincs szükség virtuális gépre.|
|Hosszan futó parancsfájl végrehajtása|hibrid runbook-feldolgozó|Az Azure-beli munkaterületeken [erőforrás-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)vannak.|
|Helyi szolgáltatásokkal való kommunikáció|hibrid runbook-feldolgozó|A közvetlenül hozzáférhet a gazdagéphez vagy más felhőalapú környezetekben található erőforrásokhoz, illetve a helyszíni környezetben is. |
|Harmadik féltől származó szoftverek és végrehajtható fájlok megkövetelése|hibrid runbook-feldolgozó|Kezelheti az operációs rendszert, és telepíthet szoftvereket.|
|Fájl vagy mappa figyelése runbook|hibrid runbook-feldolgozó|[Figyelő feladat](automation-watchers-tutorial.md) használata hibrid Runbook-feldolgozón.|
|Erőforrás-igényes parancsfájl futtatása|hibrid runbook-feldolgozó| Az Azure-beli munkaterületeken [erőforrás-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)vannak.|
|Speciális követelményekkel rendelkező modulok használata| hibrid runbook-feldolgozó|Néhány példa:</br> Megnyerő – függőség a megnyert. exe fájltól </br> IIS-felügyelet – az IIS engedélyezésével vagy kezelésével kapcsolatos függőség.|
|Modul telepítése telepítővel|hibrid runbook-feldolgozó|A sandbox moduljainak támogatnia kell a másolást.|
|A 4.7.2-től eltérő .NET-keretrendszer verziót igénylő runbookok vagy modulok használata|hibrid runbook-feldolgozó|Az Automation-munkaterületek támogatják a .NET-keretrendszer 4.7.2, és a másik verzióra való frissítés nem támogatott.|
|Jogosultságszint-emelést igénylő parancsfájlok futtatása|hibrid runbook-feldolgozó|A sandboxok nem engedélyezik a jogosultságszint-emelést. A hibrid Runbook-feldolgozók kikapcsolhatják az UAC-t, és a jogosultságszint-emelést igénylő parancs futtatásakor a [Meghívási parancsot](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) is használhatják.|
|Windows Management Instrumentation (WMI) elérését igénylő parancsfájlok futtatása|hibrid runbook-feldolgozó|A Felhőbeli munkaterületeken futó feladatok nem férnek hozzá a WMI-szolgáltatóhoz. |

## <a name="using-modules-in-your-runbooks"></a>Modulok használata a runbookok

Azure Automation számos alapértelmezett modult támogat, beleértve a AzureRM modulokat (AzureRM. Automation) és egy modult, amely több belső parancsmagot is tartalmaz. A támogatottak a telepíthető modulok is, beleértve az az modulokat (az. Automation), amelyek jelenleg a AzureRM modulok előnyben részesített használatával működnek. A runbookok és a DSC-konfigurációkhoz elérhető modulok részletes ismertetését lásd: [Azure Automation-modulok kezelése](shared-resources/modules.md).

## <a name="creating-resources"></a>Erőforrások létrehozása

Ha a runbook létrehoz egy erőforrást, a parancsfájlnak ellenőriznie kell, hogy az erőforrás már létezik-e, mielőtt megpróbálja létrehozni. Íme egy alapszintű példa.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="supporting-time-dependent-scripts"></a>Időfüggő parancsfájlok támogatása

A runbookok robusztusnak kell lennie, és képesnek kell lennie az olyan átmeneti hibák kezelésére, amelyek újraindítást vagy meghibásodást okozhatnak. Ha egy runbook meghibásodik, Azure Automation újrapróbálkozik.

Ha a runbook általában egy adott időkorláton belül fut, a parancsfájl implementálja a végrehajtási időt a logikával. Ez az ellenőrzés biztosítja az olyan műveletek futtatását, mint az indítás, a Leállítás vagy a Kibővítés csak adott időpontokban.

> [!NOTE]
> Az Azure-beli homokozó folyamat helyi ideje UTC értékre van állítva. A runbookok dátumra és időpontra vonatkozó számításait figyelembe kell venni.

## <a name="tracking-progress"></a>Követési folyamat

Érdemes lehet a runbookok, hogy moduláris jellegűek legyenek, és hogy a logikát könnyen újra felhasználható és újra lehessen indítani. A runbook nyomon követése jó módszer annak biztosítására, hogy a runbook logikája megfelelően fusson, ha problémák merülnek fel. A runbook állapotát egy külső forrás, például egy Storage-fiók, egy adatbázis vagy egy megosztott fájl használatával lehet nyomon követni. A runbook logikát is létrehozhat, hogy először ellenőrizzék az utolsó végrehajtott művelet állapotát. Ezután az ellenőrzés eredménye alapján a logikai művelet kihagyhatja vagy folytathatja a runbook adott feladatait.

## <a name="preventing-concurrent-jobs"></a>Egyidejű feladatok megakadályozása

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

## <a name="working-with-multiple-subscriptions"></a>Több előfizetés használata

Több előfizetés kezeléséhez a runbook a [disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) parancsmagot kell használnia. Ez a parancsmag biztosítja, hogy a hitelesítési környezet ne legyen lekérve egy másik, ugyanazon a homokozóban futó runbook. A runbook az az Module`AzContext` parancsmagok paraméterét is használja, és a megfelelő környezetet adja át.

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

## <a name="handling-exceptions"></a>Kivételek kezelése

Ez a szakasz néhány módszert ismertet a runbookok kivételek vagy időszakos problémák kezelésére. Ilyen például egy WebSocket-kivétel. A kivételek kezelése nem akadályozza meg, hogy az átmeneti hálózati hibák okozzák a runbookok. 

### <a name="erroractionpreference"></a>ErrorActionPreference

A [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) változó határozza meg, hogy a PowerShell hogyan válaszol a megszakítást nem okozó hibára. A hibák megszakítása mindig megszűnik, és a nem `ErrorActionPreference`érinti.

A runbook használatakor `ErrorActionPreference`a szokásos módon megszakítást okozó hiba (például `PathNotFound` a [Get-ChildItem](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) parancsmagból) leállítja a runbook befejezését. A következő példa a használatát mutatja be `ErrorActionPreference`. A végső [írási-kimeneti](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) parancs soha nem hajtható végre, mert a szkript leáll.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Próbálja ki a fogást

[Próbálja meg a Catch végül](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) a PowerShell-szkriptekben használni a leállítási hibák kezelésére. A szkript ezzel a mechanizmussal meghatározott kivételeket vagy általános kivételeket tud kifogni. Az `catch` utasítást a hibák nyomon követésére vagy kipróbálására kell használni. A következő példa egy nem létező fájl letöltését kísérli meg. A `System.Net.WebException` kivételt kigyűjti, és a többi kivétel utolsó értékét adja vissza.

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

### <a name="throw"></a>Throw

A [dob](/powershell/module/microsoft.powershell.core/about/about_throw) használatával leállítási hiba hozható fel. Ez a mechanizmus akkor lehet hasznos, ha saját logikát definiál egy runbook. Ha a parancsfájl megfelel egy olyan feltételnek, amelynek meg kell szüntetnie `throw` , akkor az utasítással leállíthatja. A következő példa ezt az utasítást használja egy kötelező Function paraméter megjelenítéséhez.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="using-executables-or-calling-processes"></a>Végrehajtható fájlok vagy hívási folyamatok használata

Az Azure-beli munkaterületeken futó runbookok nem támogatja a hívási folyamatokat, például a végrehajtható fájlok (**. exe** fájlok) vagy az alfolyamatok használatát. Ennek az az oka, hogy az Azure-beli sandbox egy olyan tárolóban fut, amely esetleg nem fér hozzá az összes mögöttes API-hoz. A harmadik féltől származó szoftvereket vagy az alfolyamatok hívásait igénylő forgatókönyvek esetén runbook kell végrehajtania egy [hibrid runbook-feldolgozón](automation-hybrid-runbook-worker.md).

## <a name="accessing-device-and-application-characteristics"></a>Eszköz-és alkalmazás-jellemzők elérése

Az Azure-beli munkaterületeken futó Runbook feladatok nem férnek hozzá semmilyen eszközhöz vagy alkalmazáshoz. A Windowson a teljesítmény-mérőszámok lekérdezéséhez használt leggyakoribb API a WMI, amely a memóriában és a CPU-használatban előforduló gyakori mérőszámokat is használja. Nem számít azonban, hogy milyen API-t használ, mivel a felhőben futó feladatok nem férnek hozzá a web-alapú Enterprise Management (WBEM) Microsoft-megvalósításához. Ez a platform a CIMre (CIM) épül, amely az eszközök és alkalmazások jellemzőinek meghatározására szolgáló iparági szabványokat biztosítja.

## <a name="handling-errors"></a>Hibák kezelése

A runbookok képesnek kell lennie a hibák kezelésére. A PowerShell két típusú hibát tartalmaz, leáll, és nem leáll. Ha leállítja a hibákat, a runbook végrehajtása leáll. A runbook nem sikerült, mert a feladatok állapota sikertelen.

A nem megszakítást okozó hibák lehetővé teszik a parancsfájlok folytatását még azután is, hogy azok bekövetkeztek. A megszakítást nem okozó hiba egy példa, amely akkor fordul elő, ha egy runbook `Get-ChildItem` a parancsmagot olyan elérési úttal használja, amely nem létezik. A PowerShell látja, hogy az elérési út nem létezik, hibát jelez, és folytatja a következő mappába. A hiba ebben az esetben nem állítja be a runbook-feladatok állapotát Sikertelenre, és a feladatot akár el is lehet végezni. Ha kényszeríteni szeretné, hogy egy runbook megszakítása leálljon egy megszakítást nem okozó hiba esetén, a parancsmagot használhatja `ErrorAction Stop` .

## <a name="handling-jobs"></a>Feladatok feldolgozása

Az azonos Automation-fiókból származó feladatok végrehajtási környezetét újra felhasználhatja. Egyetlen runbook egyszerre több feladat is futhat. Minél több feladatot futtat egyszerre, annál gyakrabban lehet ugyanarra a sandboxra elküldeni.

Az ugyanabban a homokozóban futó feladatok hatással lehetnek egymásra. Az egyik példa a [Disconnect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) parancsmagot futtatja. Ennek a parancsmagnak a végrehajtása megszakítja az egyes runbook-feladatokat a megosztott homokozó folyamat során.

Előfordulhat, hogy az Azure-beli homokozóban futó runbook indított PowerShell-feladatok nem teljes körű [PowerShell nyelvi módban](/powershell/module/microsoft.powershell.core/about/about_language_modes)futnak. A Azure Automation feladataival való interakcióval kapcsolatos további információkért lásd: [feladat állapotának beolvasása a PowerShell](#retrieving-job-status-using-powershell)-lel.

### <a name="job-statuses"></a>Feladatok állapota

A következő táblázat a feladatokhoz lehetséges állapotokat ismerteti.

| status | Leírás |
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
| Felfüggesztve |Csak a [grafikus és a PowerShell munkafolyamat-runbookok](automation-runbook-types.md) vonatkozik. A felhasználó, a rendszer vagy a runbook egy parancsa felfüggesztette a feladatot. Ha egy runbook nem rendelkezik ellenőrzőponttal, az elejétől kezdődik. Ha ellenőrzőponttal rendelkezik, újra elindíthatja, és folytathatja az utolsó ellenőrzőpontot. A rendszer csak akkor felfüggeszti a runbook, ha kivétel történik. Alapértelmezés szerint a `ErrorActionPreference` változó a Continue (folytatás) értékre van állítva, ami azt jelzi, hogy a művelet hibán fut. Ha a preferencia változó leállítás értékre van állítva, a művelet egy hibára felfüggeszti a feladatot.  |
| Felfüggesztés |Csak a [grafikus és a PowerShell munkafolyamat-runbookok](automation-runbook-types.md) vonatkozik. A rendszer megkísérli a feladat felfüggesztését a felhasználó kérésére. A runbooknak el kell érnie a következő ellenőrzőpontot a felfüggesztés előtt. Ha már elvégezte az utolsó ellenőrzőpontot, akkor a felfüggesztés előtt befejeződik. |

### <a name="viewing-job-status-from-the-azure-portal"></a>A feladatok állapotának megtekintése a Azure Portal

Megtekintheti az összes runbook-feladat állapotának összegzését, vagy részletezheti a Azure Portal adott runbook-feladatának részleteit. Az Log Analytics-munkaterülettel való integrációt úgy is konfigurálhatja, hogy továbbítsa a runbook feladatok állapotát és a feladatok folyamait. A Azure Monitor-naplók integrálásával kapcsolatos további információkért lásd: a [feladatok állapotának és a feladatok adatfolyamának továbbítása az automatizálásból a Azure monitor naplókba](automation-manage-send-joblogs-log-analytics.md).

A kiválasztott Automation-fiók jobb oldalán a **feladat statisztikái** csempén látható összes runbook-feladat összegzését láthatja.

![Feladatok statisztikájának csempe](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ez a csempe a feladatok állapotának számát és grafikus ábrázolását jeleníti meg minden végrehajtott feladathoz.

A csempére kattintva megjelenítheti a feladatok lapot, amely tartalmazza az összes végrehajtott feladat összegzett listáját. Ezen a lapon látható az egyes feladatokhoz tartozó állapot, a runbook neve, a kezdési idő és a befejezési idő.

![Automation-fiók feladatok lapja](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

A feladatok listájának szűréséhez kattintson a **feladatok szűrése**lehetőségre. Szűrje egy adott runbook, a feladatok állapotát, vagy válasszon a legördülő listából, és adja meg a keresés időtartományát.

![Szűrési feladatok állapota](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Azt is megteheti, hogy megtekinti az adott runbook tartozó feladat összegzésének részleteit úgy, hogy kijelöli a runbook az Automation-fiók Runbookok lapján, majd kiválasztja a **feladatok** csempét. Ez a művelet a feladatok lapot mutatja be. Innen a feladatra kattintva megtekintheti annak adatait és kimenetét.

![Automation-fiók feladatok lapja](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>A feladatok összegzésének megtekintése

A fent ismertetett feladat-összefoglalás lehetővé teszi, hogy megtekintse az adott runbook létrehozott összes feladat listáját, valamint a legutóbbi állapotukat. A feladatok részletes információinak és kimenetének megtekintéséhez kattintson a nevére a listában. A feladatok részletes nézete tartalmazza az adott feladatokhoz megadott runbook paraméterek értékeit.

Az egy adott runbookhoz tartozó feladatok a következő lépésekkel tekinthetők meg.

1. A Azure Portal válassza az **Automation** lehetőséget, majd válassza ki az Automation-fiók nevét.
2. A központban válassza a **runbookok** lehetőséget a **folyamat automatizálása**alatt.
3. A Runbookok lapon válasszon ki egy runbook a listából.
3. A kiválasztott runbook tartozó lapon kattintson a **feladatok** csempére.
4. Kattintson a listában szereplő egyik feladatra, és tekintse meg a részleteket és a kimenetet a runbook feladat részletei lapon.

### <a name="retrieving-job-status-using-powershell"></a>A feladatok állapotának beolvasása a PowerShell használatával

A [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) parancsmaggal kérheti le a runbook létrehozott feladatokat, valamint egy adott feladat részleteit. Ha runbook indít el a PowerShell használatával `Start-AzAutomationRunbook`, az a létrejövő feladatot adja vissza. A [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) használatával kérheti le a feladatok kimenetét.

A következő példa beolvassa a minta runbook utolsó feltételét, és megjeleníti annak állapotát, a runbook paraméterekhez megadott értékeket, valamint a feladatok kimenetét.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

A következő példa egy adott feladathoz tartozó kimenetet kérdez le, és visszaadja az egyes rekordokat. Ha a rekordok egyikének kivétele van, a parancsfájl az érték helyett a kivételt írja. Ez a viselkedés azért hasznos, mert a kivételek olyan további információkat is biztosíthatnak, amelyek a kimenet során általában nem lesznek naplózva.

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Erőforrások megosztása a runbookok között

Ha az erőforrásokat a felhő összes runbookok között meg szeretné osztani, Azure Automation ideiglenesen eltávolíthatja vagy leállíthatja a három óránál hosszabb ideig futó feladatokat. A [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks) és a [Python-runbookok](automation-runbook-types.md#python-runbooks) kapcsolatos feladatok leállnak, és nem indulnak újra, és a feladat állapota leáll.

A hosszan futó feladatok esetében ajánlott hibrid Runbook-feldolgozót használni. A hibrid Runbook-feldolgozók nem korlátozódnak a méltányos megosztásra, és nincs korlátozás arra vonatkozóan, hogy mennyi ideig lehet végrehajtani a Runbook. A többi [feladattípus az](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) Azure-beli és a hibrid Runbook-feldolgozókra is érvényes. Amíg a hibrid Runbook-feldolgozók nem korlátozzák a 3 órás igazságos megosztási korlátot, akkor olyan runbookok kell fejlesztenie, amelyek támogatják a váratlan helyi infrastruktúra-problémákról való újraindítást.

Egy másik lehetőség, hogy optimalizálja a runbook a gyermek runbookok használatával. Előfordulhat például, hogy a runbook több erőforráson ugyanazt a függvényt, például egy adatbázis-műveletet több adatbázison is át lehet ismételni. Ezt a függvényt áthelyezheti egy [alárendelt runbook](automation-child-runbooks.md) , és a runbook meghívja azt a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)használatával. A gyermek runbookok párhuzamosan hajthatók végre külön folyamatokban.

A gyermek runbookok használata csökkenti a szülő runbook befejezésének teljes időtartamát. A runbook használhatja a [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) parancsmagot a gyermek runbook feladatainak vizsgálatához, ha még több művelet van a gyermek befejeződése után.

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan dolgozhat egy runbook, tekintse meg [a Runbookok kezelése a Azure Automationban](manage-runbooks.md)című témakört.
* Ha többet szeretne megtudni a Azure Automation runbook indításához használható módszerekről, tekintse meg a [runbook Azure Automation-ben](automation-starting-a-runbook.md)való elindításával foglalkozó témakört.
* A PowerShell-lel kapcsolatos további információk, beleértve a nyelvi referenciákat és a tanulási modulokat, lásd: [PowerShell-dokumentumok](https://docs.microsoft.com/powershell/scripting/overview).
* A PowerShell-parancsmagok leírása: [az. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
