---
title: Runbook-kimenet és üzenetek az Azure Automationben
description: Hogyan hozhat létre, és kimeneti és a hiba az Azure Automation runbookjai érkező üzenetek Desribes.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b5eaed6c25e3d8ccc4c4577492398ef6cd741b35
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook-kimenet és üzenetek az Azure Automationben
Azure Automation-forgatókönyv a legtöbb van valamilyen kimenetet például egy hibaüzenet a felhasználó számára, vagy egy összetett objektumot egy másik munkafolyamat számára készült. A Windows PowerShell szintén [több adatfolyam](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) kimenetként egy parancsfájl vagy a munkafolyamat számára. Az összes ezekbe az adatfolyamokba eltérő módon működik az Azure Automation, és ajánlott eljárások használata minden egyes runbook létrehozásakor kell.

A következő táblázat röviden az adatfolyamokat, és az Azure-portálon viselkedésük mind a közzétett runbookok futtatásakor, és ha [runbook tesztelése](automation-testing-runbook.md). További részleteket az egyes adatfolyamokkal az ezt követő szakaszok szolgálnak.

| Stream | Leírás | Közzétéve | Tesztelés |
|:--- |:--- |:--- |:--- |
| Kimenet |Másik runbookok számára készült objektum. |A feladatelőzmények írni. |Megjelennek a Tesztkimenet ablaktáblán. |
| Figyelmeztetés |A felhasználónak szóló figyelmeztető üzenetet. |A feladatelőzmények írni. |Megjelennek a Tesztkimenet ablaktáblán. |
| Hiba |A felhasználónak szóló hibaüzenet. A kivételek a runbook továbbra is fut egy hibaüzenet megjelenésekor alapértelmezés szerint. |A feladatelőzmények írni. |Megjelennek a Tesztkimenet ablaktáblán. |
| Részletes |Általános vagy a hibakeresési információk üzeneteket. |Csak akkor, ha a runbook be van kapcsolva a részletes naplózást, szerepel a feladatelőzményekben. |Csak akkor, ha a $VerbosePreference folytatja a runbook értéke megjelenik a Tesztkimenet ablaktáblán. |
| Előrehaladás |Automatikusan létrehozott előtt és után a runbook minden tevékenysége rögzíti. A runbook ne próbáljon létrehozni a saját állapotrekordjait, mert egy interaktív felhasználó szolgálnak. |Csak akkor, ha a runbook be van kapcsolva a folyamatban lévő naplózási szerepel a feladatelőzményekben. |Nem jelennek meg a Tesztkimenet ablaktáblán. |
| Hibakeresés |Egy interaktív felhasználó számára készült üzenetek. Nem használható a runbookok. |Nem szerepel a feladatelőzményekben. |Nem szerepel a Tesztkimenet ablaktáblán. |

## <a name="output-stream"></a>Kimeneti adatfolyam
A kimeneti adatfolyamba megfelelően egy parancsfájl vagy a munkafolyamat által létrehozott objektumok kimenetéhez használható. Az Azure Automationben, ez az adatfolyam elsősorban a által felhasznált objektumok esetében [az aktuális runbookot meghívó runbookok szülő](automation-child-runbooks.md). Ha Ön [beágyazottan indított runbook hívja](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) a szülőrunbookból, hogy adatokat ad vissza a kimeneti adatfolyamból a szülő. A kimeneti adatfolyamba való kommunikációhoz az általános információkat továbbítson a felhasználónak, ha tudja, hogy egy másik runbook soha nem hívja a runbook csak használjon. Ajánlott eljárásként, azonban általában használjon a [részletes adatfolyam](#Verbose) hogy általános információkat a felhasználó számára.

Adatokat írni a kimeneti adatfolyamhoz a [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) vagy az objektum saját sort kap a runbookban a helyezésével.

```PowerShell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Függvények kimenete
Ha a kimeneti adatfolyamba, a runbookban szereplő függvényből ír a kimeneti átadódik a runbookot. Ha a runbook hozzárendeli a kimenet egy változóhoz, majd nem írás a kimeneti adatfolyamba. A runbook megfelelő adatfolyamába ír írás bármely másik adatfolyamba a függvényen belül.

Vegye figyelembe az alábbi példában a runbook:

```PowerShell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

A kimeneti adatfolyamba, a runbook-feladat a következő lesz:

    Output inside of function
    Output outside of function

A részletes adatfolyama így a runbook-feladat a következő lesz:

    Verbose outside of function
    Verbose inside of function

A runbook közzététel után, és mielőtt elkezdené, is engedélyezni kell a részletes naplózást a runbook beállításaiban a részletes adatfolyam-kimenetét eléréséhez.

### <a name="declaring-output-data-type"></a>Deklarálási kimeneti adatok típusát
Egy munkafolyamat adhat meg a kimenethez használt adattípusa a [OutputType attribútummal](http://technet.microsoft.com/library/hh847785.aspx). Ez az attribútum nincs hatása futásidőben, de a arra utal, hogy a runbook Szerző a runbook a várt kimeneti tervezési időben. A runbookok eszközkészlete folyamatosan fejleszteni, a fontos tervezési időben elvárt kimeneti adattípus fontossága növekszik. Ennek eredményeképpen a legjobb az Ön által létrehozott runbook ezt a nyilatkozatot felvenni.

Ez egy lista példa kimenet típusa:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Az alábbi példában a runbook kimenete egy karakterlánc-objektum, és megadja az elvárt kimenettípust. Ha a runbook kimenete egy bizonyos típusú tömb, majd, továbbra is adja meg a típus típusú tömb szemben.

```PowerShell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

A forgatókönyvek grafikus vagy grafikus PowerShell-munkafolyamat egy kimeneti típus deklarálja, jelölje ki a **bemeneti és kimeneti** menüpontra, majd a kimeneti típus nevében típusa. Ajánlott a teljes .NET-osztály neve segítségével könnyebben könnyen azonosítható, ha a szülőrunbookból hivatkoznak rá. Ez az adatbuszhoz a runbookban lévő osztály összes tulajdonságainak és sok rugalmasságot nyújt, amikor a használatuk feltételes logikához, a naplózás, és más tevékenységek a runbookban értékként hivatkozik.<br> ![A Runbook bemeneti és kimeneti beállítás](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

A következő példában, hogy ez a funkció bemutatásához két grafikus forgatókönyvek. A moduláris runbook hálózattervezési modell alkalmazza, ha egy runbookot, amely rendelkezik a *hitelesítési Runbook sablon* hitelesítés kezelése az Azure-ban a Futtatás mint fiók. A második runbookot, amely általában hajtaná végre a core logika egy adott forgatókönyv automatizálása, ebben az esetben lesz hajtható végre a *hitelesítési Runbook sablon* , és megjeleníti az eredményeket a **teszt** tesztkimenet ablaktáblán. Normál körülmények között ez a forgatókönyv foglalkozhat ellen, ami a gyermekrunbook kimenetét erőforrás kellene lennie.    

Ez az alapvető logikát a **AuthenticateTo-Azure** runbook.<br> ![Runbook-sablont példa hitelesítéséhez](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

A kimeneti típust tartalmazza *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, ami visszaadja a hitelesítési profiltulajdonságok.<br> ![Runbook-kimenet típusa – példa](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Bár ez a forgatókönyv közvetlen, egy konfigurációs elem van itt hívásához. Az utolsó tevékenység végrehajtása történik a **Write-Output** parancsmag és a profiladatok ír egy $_ változó segítségével egy PowerShell-kifejezést vár a **Inputobject** paraméter, amely pedig szükséges, hogy a parancsmag.  

A második forgatókönyv ebben a példában, nevű *teszt-ChildOutputType*, egyszerűen csak két tevékenység rendelkezik.<br> ![Példa gyermek típusú Runbook kimeneti](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

Az első tevékenység hívások a **AuthenticateTo-Azure** runbook és a második tevékenység fut a **Write-Verbose** parancsmagot a **adatforrás** a **tevékenység kimeneti** és az értéke **mező elérési útja** van **Context.Subscription.SubscriptionName**, amely határozza meg a környezetben kimenetét a a **AuthenticateTo-Azure** runbook.<br> ![Write-Verbose parancsmag paraméter adatforrás](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Ennek kimenete az előfizetés esetén.<br> ![Teszt-ChildOutputType eredmények összesítése](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

A kimeneti típus vezérlő viselkedését egy megjegyzést. Ha a kimeneti mezőben a bemeneti és kimeneti tulajdonságai panelen írjon be egy értéket kell a vezérlőn kívül, akkor ahhoz, hogy a megadott adatot ismeri fel a vezérlő beírása után.  

## <a name="message-streams"></a>Üzenet-adatfolyamok
A kimeneti adatfolyamokkal ellentétben üzenet-adatfolyamok célja, hogy a felhasználónak kommunikációhoz. A különböző információtípusoknak saját üzenet-adatfolyamuk van, és minden Azure Automation a másképpen kezeli.

### <a name="warning-and-error-streams"></a>Figyelmeztetés és hiba adatfolyamok
A figyelmeztetési és hibaadatfolyamok a runbookban előforduló problémákat naplózzák. Írás a feladatelőzményekben a runbook végrehajtása, és az Azure portál Tesztkimenet ablaktábláján szerepel egy runbook tesztelésekor. Alapértelmezés szerint a runbook fog szakítják meg a figyelmeztető vagy hibaüzenetek. Megadhatja, hogy a runbook kell fel legyen függesztve figyelmeztető vagy hibaüzenet úgy, hogy egy [preferenciaváltozót](#PreferenceVariables) a runbookban az üzenet létrehozása előtt. Például, hogy egy runbook felfüggessze a hiba, mintha a kivétel, állítsa **$ErrorActionPreference** állítja.

Hozzon létre egy figyelmeztető és hibaüzeneteket a [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) vagy [Write-Error](http://technet.microsoft.com/library/hh849962.aspx) parancsmag. A tevékenységek is írhatnak adatokat ezekbe az adatfolyamokba.

```PowerShell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Részletes adatfolyam
A részletes üzenet-adatfolyam van a runbook-művelettel kapcsolatos általános információkat. Mivel a [hibakeresési adatfolyam](#Debug) nem érhető el egy runbook részletes üzenetek használja a rendszer hibakeresési információ. Alapértelmezés szerint a közzétett runbookok részletes üzenetei nem tárolódik a feladatelőzményekben. Részletes üzenetek tárolásához, konfigurálja a konfigurálása lapon, az Azure-portálon a runbook közzétett runbookok részletes rekordok naplózása. A legtöbb esetben érdemes megtartani az alapértelmezett beállítás, amely nem naplózza a teljesítményre vonatkozó megfontolásból runbook részletes rekordjait. Kapcsolja be ezt a lehetőséget csak hibaelhárítása vagy a hibakeresési egy runbookot.

Ha [runbook tesztelése](automation-testing-runbook.md), részletes üzenet nem jelenik meg akkor is, ha a runbook a részletes rekordok naplózására van beállítva. Miközben a részletes üzenetek megjelenítése [runbook tesztelése](automation-testing-runbook.md), a $VerbosePreference változó Folytatás kell beállítania. A változó beállítása a részletes üzenetek az Azure portál Tesztkimenet ablaktábláján jelennek meg.

Részletes üzenetet létrehozni a [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) parancsmag.

```PowerShell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Hibakeresési adatfolyam
A hibakeresési adatfolyam interaktív felhasználót számára készült, és nem használható a runbookok.

## <a name="progress-records"></a>Állapotrekordok
Ha konfigurálja a runbook naplózza folyamatban (az Azure-portálon a runbook konfigurálása lapján) rögzíti, majd egy rekord fog szerepelni a feladatelőzményekben, előtt és után a tevékenység futtatása. A legtöbb esetben az alapértelmezett beállítás, amely nem naplózza a runbook az állapotrekordok teljesítmény maximalizálása érdekében érdemes megtartani. Kapcsolja be ezt a lehetőséget csak hibaelhárítása vagy a hibakeresési egy runbookot. A runbook tesztelésekor hibaüzenetei nem jelennek meg, akkor is, ha a runbook az állapotrekordok naplózására van beállítva.

A [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) parancsmag érvénytelen runbookokban, mivel ez egy interaktív felhasználó való használatra készült.

## <a name="preference-variables"></a>Preferenciaváltozók
A Windows PowerShell használ [preferenciaváltozók](http://technet.microsoft.com/library/hh847796.aspx) annak meghatározásához, hogyan reagáljon a különböző kimeneti adatfolyamokba küldött adatokra. Ezek a változók beállíthat egy runbook vezérlésére, hogyan reagáljon a különböző adatfolyamokba küldött adatokra.

Az alábbi táblázat a preferenciaváltozók használható a runbookok, valamint azok érvényes és alapértelmezett értékeit. Ez a táblázat csak a runbookokban érvényes értékeket tartalmazza. A preferenciaváltozók Azure Automation kívül a Windows PowerShell további értékek érvényesek.

| Változó | Alapértelmezett érték | Érvényes értékek |
|:--- |:--- |:--- |
| WarningPreference |Folytatás |Leállítás<br>Folytatás<br>Folytatáscsendben |
| ErrorActionPreference |Folytatás |Leállítás<br>Folytatás<br>Folytatáscsendben |
| VerbosePreference |Folytatáscsendben |Leállítás<br>Folytatás<br>Folytatáscsendben |

A következő táblázat preferencia változóértékei, amelyek érvényesek a runbookok viselkedését.

| Érték | Viselkedés |
|:--- |:--- |
| Folytatás |Naplózza az üzenetet, és folytatja a runbook futtatását. |
| Folytatáscsendben |Továbbra is fennáll, az üzenet naplózása nélkül a runbook futtatását. Ez befolyásolja a rendszer figyelmen kívül hagyja az üzenetet. |
| Leállítás |Naplózza az üzenetet, és felfüggeszti a runbook futtatását. |

## <a name="retrieving-runbook-output-and-messages"></a>Runbook-kimenet és üzenetek lekérése
### <a name="azure-portal"></a>Azure Portal
A runbook-feladatok részletes adatait az Azure portálon a runbookok feladatok lapján tekintheti meg. A feladat összegzését jeleníti meg a bemeneti paraméterek és a [kimeneti adatfolyamba](#Output) mellett a feladatot, és ha sor került ilyenre kivételek kapcsolatos általános információkat. Az előzmények üzeneteit magában foglalja a [kimeneti adatfolyamba](#Output) és [figyelmeztető és Hibaadatfolyamok](#WarningError) kívül a [részletes adatfolyam](#Verbose) és [Állapotrekordokat](#Progress) Ha a runbook naplózza a részletes és állapotrekordokat van beállítva.

### <a name="windows-powershell"></a>Windows PowerShell
A Windows PowerShell parancssorába beolvasható-kimenet és üzenetek powershellel a [Get-AzureAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) parancsmag. Ehhez a parancsmaghoz szükséges a feladat Azonosítóját, és hívta a paraméter adatfolyam, amelyben meg kell határoznia melyik adatfolyamot adja majd vissza. Megadhat **bármely** a feladathoz tartozó összes adatfolyamot visszaadja.

A következő példában elindul a runbook, és majd megvárja, amíg befejeződik. Ezt követően a kimeneti adatfolyamot begyűjti a feladatból.

```PowerShell
$job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>Grafikus készítése
Grafikus forgatókönyvekhez extra naplózás érhető tevékenység szintű nyomkövetés formájában. Nyomkövetés két szint: Basic, és részletesen. Alapvető nyomkövetés, láthatja, hogy a start és a runbook, valamint minden tevékenység-újrapróbálkozások, például a kapcsolatos adatokat minden tevékenység befejezési időpontja kísérletet tett, és a tevékenység kezdési időpontja. Részletes nyomkövetés, a kapott egyszerű nyomkövetést plusz bemeneti és kimeneti minden egyes tevékenységhez. Jelenleg a nyomkövetési rekord segítségével készül a részletes napló, engedélyeznie kell a részletes naplózást, ha engedélyezi a nyomkövetést. Grafikus forgatókönyvekhez a kérelmek nyomon követése engedélyezve van nincs szükség a naplózza az állapotrekordokat, mert az alapvető nyomkövetés ugyanazt a célt szolgálja, és több információs célokat szolgál.

![Grafikus szerzői feladat adatfolyamokat megtekintése](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Az előző képernyőképen látható látható, hogy ha engedélyezi a részletes naplózás és nyomkövetés grafikus forgatókönyvekhez, sokkal több adatot legyen az üzemi feladat adatfolyamok nézet. Lehet, hogy ez további információt a runbook üzemi kapcsolatos problémák alapvető, és ezért csak akkor engedélyezze az erre a célra, nem pedig egy általános gyakorlat. Lehet, hogy a nyomkövetési rekord különösen számos. A grafikus forgatókönyvnek kérelmek nyomon követése, kettő vagy négy rekordok száma attól függően, hogy konfigurálta az alap vagy részletes nyomkövetés tevékenység kérheti le. Ha nem kell ezt az információt a runbook hibaelhárítási előrehaladását úgy követheti nyomon, érdemes nyomkövetés megőrzése ki van kapcsolva.

**Tevékenység-szintű a nyomkövetés engedélyezéséhez, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **folyamat**, jelölje be **Runbookok** runbookok listájának megnyitásához.
3. A Runbookok kattintson a lap, válasszon ki egy grafikus forgatókönyvnek a runbookok a listáról.
4. A **beállítások**, kattintson a **naplózás és nyomkövetés**.
5. A naplózás és nyomkövetés lapon a részletes rekordok naplózása, kattintson a **a** részletes naplózás engedélyezése és a nyomkövetési szint tevékenység szintű nyomkövetés csoportban **alapvető** vagy **részletes** szükséges nyomkövetési szint alapján.<br>
   
   ![Grafikus szerzői naplózás és nyomkövetés panel](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-log-analytics"></a>A Microsoft Azure Naplóelemzés
Automatizálási küldhet runbook feladat állapotát és a feladat adatfolyamok Naplóelemzési munkaterületet. A Naplóelemzési is,

* Az automatizálási feladatok insight letöltése 
* A runbook-feladat állapota (például felfüggesztett vagy sikertelen) alapuló riasztás vagy e-mail eseményindító 
* Speciális lekérdezéseket írhat a feladat adatfolyamok között 
* Feladatok összefüggéseket Automation-fiókok között 
* A feladatelőzmények megjelenítheti az adott idő alatt    

Hogyan gyűjthet, egyeztetéséhez és feladatadatok intézkedjen Naplóelemzési rendszerrel történő integráció konfigurálása További információkért lásd: [feladat állapotát és a feladat adatfolyam továbbítása Automation Naplóelemzési](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>További lépések
* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza
* Tervezése és alárendelt forgatókönyvekkel ismertetése: [gyermek az Azure Automation runbookjai](automation-child-runbooks.md)

