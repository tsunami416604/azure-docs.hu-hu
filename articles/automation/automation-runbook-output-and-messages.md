---
title: Runbook-kimenet és üzenetek az Azure Automationben
description: Desribes, hogyan hozhat létre és kimenetet és lekérése az Azure Automation runbookjai az üzeneteket.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82382ecc3adf0d0621f51438a082f7807b031fc9
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431214"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook-kimenet és üzenetek az Azure Automationben
A legtöbb Azure Automation-runbookok rendelkezik valamilyen kimenetet. Ez a kimenet egy hibaüzenet, a felhasználó vagy egy összetett objektumot egy másik runbookból a használni kívánt lehet. Windows PowerShell biztosít [több adatfolyam](/powershell/module/microsoft.powershell.core/about/about_redirection) , elküldheti a kimenetet egy parancsfájl vagy a munkafolyamat. Az Azure Automation eltérően működik az egyes ezekbe az adatfolyamokba. Ajánlott eljárások használata minden egyes runbook létrehozásakor kövesse.

Az alábbi táblázatban az egyes a Streamek és a közzétett forgatókönyvek esetében az Azure Portalon viselkedésük rövid leírását és mikor [runbook tesztelése](automation-testing-runbook.md). Egyes adatfolyamokkal kapcsolatos további részletekért lásd a későbbi szakaszokban találhatók.

| Stream | Leírás | Közzétett | Teszt |
|:--- |:--- |:--- |:--- |
| Kimenet |Másik runbookok számára készült objektum. |A feladatelőzményekbe írt. |Megjelennek a Tesztkimenet ablaktáblán. |
| Figyelmeztetés |A felhasználónak szóló figyelmeztető üzenetet. |A feladatelőzményekbe írt. |Megjelennek a Tesztkimenet ablaktáblán. |
| Hiba |A felhasználónak szóló hibaüzenet. A kivételek a runbook továbbra is egy hibaüzenet alapértelmezés szerint. |A feladatelőzményekbe írt. |Megjelennek a Tesztkimenet ablaktáblán. |
| Részletes |Általános vagy hibakeresési információkat üzenetek. |Csak akkor, ha a runbook be van kapcsolva a részletes naplózást, szerepel a feladatelőzményekben. |Csak akkor, ha a runbook Folytatás értékre van állítva a $VerbosePreference megjelennek a Tesztkimenet ablaktáblán. |
| Előrehaladás |A rekordok automatikusan generált előtt és után a runbook egyes tevékenységeinek. A runbook ne próbálja meg létrehozni a saját állapotrekordjait, mert arra szolgálnak, interaktív felhasználó számára. |Szerepel a feladatelőzményekben, csak ha az állapot naplózása be van kapcsolva a runbookhoz. |Nem jelennek meg a Tesztkimenet ablaktáblán. |
| Hibakeresés |Egy interaktív felhasználó számára készült üzenetek. Nem runbookok általi használatra. |Nem szerepel a feladatelőzményekben. |Nem szerepel a Tesztkimenet ablaktáblán. |

## <a name="output-stream"></a>Kimeneti adatfolyam
A kimeneti adatfolyamba, objektumok, amelyek megfelelően olyan parancsfájl vagy a munkafolyamat által létrehozott kimeneti szól. Az Azure Automationben, ez az adatfolyam elsősorban a rendszer által felhasznált objektumok esetében [runbookokat, amelyek az aktuális runbookot hívja meg a szülő](automation-child-runbooks.md). Ha Ön [egy beágyazott runbookot hívja](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) a szülő runbook, adatokat ad vissza a kimeneti adatfolyamból származó a szülő. Csak a kimeneti stream segítségével a felhasználó számára általános információkat továbbítson, ha tudja, hogy a runbook másik runbookból soha nem hívja meg. Ajánlott eljárásként, azonban általában használjon a [részletes Stream](#verbose-stream) , a felhasználó általános információkat továbbítson.

Adatokat írni a kimeneti adatfolyamhoz a [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) vagy az objektum saját sort a runbook helyezésével.

```PowerShell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Függvények kimenete
Ha a függvény, amely tartalmazza a runbook a kimeneti adatfolyamba ír, a kimeneti átadódik a runbook. Ha a runbook hozzárendeli a kimenetet egy változóhoz, majd nem írás a kimeneti adatfolyamba. A megfelelő adatfolyam a runbook ír írnak a bármely más érkező adatfolyamok a függvényen belül.

Vegye figyelembe a következő runbookot:

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

```output
Output inside of function
Output outside of function
```

A runbook feladatának részletes adatfolyama a következő lesz:

```output
Verbose outside of function
Verbose inside of function
```

Miután közzétette a runbookot, és azt megkezdése előtt, akkor a részletes naplózás eredményének részletes adatfolyam a runbook beállításaiban is ki kell kapcsolni.

### <a name="declaring-output-data-type"></a>Jelentést készítő kimeneti adatok típusát
Egy munkafolyamat adhatja meg a kimenethez használt adattípus a [OutputType attribútummal](https://technet.microsoft.com/library/hh847785.aspx). Ez az attribútum nem lesz hatása futásidőben, de befolyásol a runbook szerzője a runbooknak milyen kimenetet a runbook a tervezés során. A runbookok eszközkészlete folyamatosan fejlődnek, fontosság növekszik a tervezés során kimeneti adattípus deklaráló fontosságát. Ennek eredményeképpen Ez a nyilatkozat felvenni az Ön által létrehozott runbookok ajánlott eljárás.

A következő példa listáját kimeneti típusokat:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Az alábbi példában a runbook kimenete egy karakterlánc-objektum, és határozza meg, a kimeneti típus tartalmazza. Ha a runbook kimenete egy tömb bizonyos típusú, majd meg kell továbbra is megadni, nem pedig egy tömb típusú.

```PowerShell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Deklaráljon egy kimeneti típus grafikus vagy a grafikus PowerShell-munkafolyamati runbookok, kiválaszthatja a **bemeneti és kimeneti** menüpontra, majd írja be a kimeneti típus nevét. Javasoljuk, hogy könnyen azonosítható, a szülő runbook való hivatkozáskor használhatja a teljes .NET-osztály neve. Ez mutatja meg, hogy a runbook adatbusz osztály tulajdonságait, és sok rugalmasságot biztosít, amikor feltételes logikához is alkalmazhat, naplózás, és a runbook más tevékenységei számára értékekként hivatkozó használja őket.<br> ![Forgatókönyv-bemenet és kimenet lehetőséget](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

A következő példában két grafikus runbookokban, ez a funkció bemutatásához rendelkezik. A moduláris runbook tervezési modellt alkalmaz, ha egy runbookot, amely rendelkezik a *hitelesítési Runbook sablon* hitelesítés kezelése az Azure-ral a futtató fiók használatával. Második runbookot, amely egy adott forgatókönyv automatizálása fő logikai általában végre, ebben az esetben fogja végrehajtani a *hitelesítési Runbook sablon* , és megjeleníti az eredményeket, és a **teszt** tesztkimenet ablaktáblán. Normál körülmények kell ennek a runbooknak valamit egy erőforráson, kihasználva a gyermekrunbook kimenetét.

Íme az alapszintű logikája a **AuthenticateTo Azure-beli** runbook.<br> ![Runbook Példasablonban hitelesítéséhez](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

A kimeneti típus tartalmaz *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, amely visszaadja a hitelesítési profil tulajdonságai.<br> ![Példa a Runbook-kimenet típusa](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Amíg ez a runbook nagyon egyszerű, van egy adott konfigurációelemet itt hívásához. Legutóbbi tevékenység végrehajtása a **Write-Output** parancsmag és a profil adatokat ír a PowerShell-kifejezéssel $_ változó a **Inputobject** paramétert, amely szükséges a parancsmagot.  

Ebben a példában a második forgatókönyv nevű *Test-ChildOutputType*, egyszerűen csak két tevékenység rendelkezik.<br> ![Példa a gyermeke írja be a Runbook kimenete](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

Az első tevékenység hívások a **AuthenticateTo Azure-beli** runbook és a második tevékenység fut a **Write-Verbose** parancsmagot a **adatforrás** ,  **Tevékenység kimenete** és értékének **mező elérési útja** van **Context.Subscription.SubscriptionName**, amely határozza meg a környezet kimenetét a  **Az Azure-AuthenticateTo** runbook.<br> ![A Write-Verbose parancsmag paraméter adatforrás](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

A kimenet az előfizetés nevét.<br> ![Test-ChildOutputType Runbook Results](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

A kimeneti típusú vezérlőelem a működéssel kapcsolatos megjegyzés. Bemeneti és kimeneti tulajdonságpaneljén kimeneti Type mezőjében írjon be egy értéket, ha rendelkezik, ahhoz, hogy a bejegyzést, hogy a vezérlő beírása után kívül az ellenőrzés gombra.  

## <a name="message-streams"></a>Üzenet-adatfolyamok
A kimeneti adatfolyamokkal ellentétben a üzenet-adatfolyamok kifejezetten a felhasználó felé történő információtovábbítást. Különböző információtípusoknak saját üzenet-adatfolyamuk, és minden egyes Azure Automation a eltérően kell kezelni.

### <a name="warning-and-error-streams"></a>Figyelmeztető és hibastreamek
A figyelmeztetési és hibaadatfolyamok a runbookban előforduló problémákat naplózzák. Írás a feladatelőzményekben, ha egy runbook végrehajtásakor, és az Azure Portalon Tesztkimenet ablaktábláján szerepelnek, amikor tesztelnek egy runbookot. Alapértelmezés szerint a runbook szakítják figyelmeztetés vagy hiba. Megadhatja, hogy a runbook kell futtatása fel legyen függesztve figyelmeztető vagy hibaüzenet beállításával egy [preferenciaváltozó](#preference-variables) a runbookban az üzenet létrehozása előtt. Például, hogy egy runbook felfüggesztése hiba, mintha a kivétel, állítsa **$ErrorActionPreference** állítja.

Hozzon létre egy figyelmeztető vagy hibaüzeneteket a [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) vagy [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) parancsmagot. A tevékenységek is írhatnak adatokat ezekbe az adatfolyamokba.

```PowerShell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Részletes adatfolyam
A részletes üzenet-adatfolyam a runbook-művelettel kapcsolatos általános információkat van. Mivel a [hibakeresési Stream](#debug-stream) nem érhető el egy runbookot, részletes üzeneteket kell használni a hibakeresési adatokat. Alapértelmezés szerint a közzétett runbookok részletes üzenetek nem tárolja a feladatelőzményekben. Részletes üzenetek tárolásához, konfigurálja a konfigurálás lapot, az Azure Portalon a runbook közzétett runbookok részletes rekordok naplózása. A legtöbb esetben érdemes megtartani az alapértelmezett beállítás, amely nem naplózza a megfelelő teljesítmény biztosítása érdekében a runbook részletes rekordjait. Kapcsolja be ezt a beállítást csak hibakeresési egy runbook vagy hibák elhárítása.

Amikor [runbook tesztelése](automation-testing-runbook.md), nem a részletes üzenetek akkor jelennek meg, akkor is, ha a runbook a részletes rekordok naplózására van beállítva. Miközben részletes üzenetek megjelenítése [runbook tesztelése](automation-testing-runbook.md), először be kell állítania a $VerbosePreference változó tovább. A változó beállítása részletes üzenetek megjelennek a Tesztkimenet ablaktáblán az Azure Portal.

Részletes üzenetet létrehozni a [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) parancsmagot.

```PowerShell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Hibakeresési adatfolyam
A hibakeresési adatfolyam interaktív felhasználóknak szól, és nem használható a forgatókönyvekben.

## <a name="progress-records"></a>Haladási rekordok
Ha folyamatban van a bejelentkezni egy runbook rögzíti (a az Azure Portalon a runbook konfigurálása lapon), majd egy rekord lesz írva a feladatelőzményekbe előtt és után a tevékenység futtatása. A legtöbb esetben érdemes megtartani az alapértelmezett beállítás a nem naplózza a runbook teljesítmény maximalizálása érdekében. Kapcsolja be ezt a beállítást csak hibakeresési egy runbook vagy hibák elhárítása. A runbook tesztelésekor folyamatüzeneteket nem jelennek meg, akkor is, ha a runbook az állapotrekordok naplózására van van beállítva.

A [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) parancsmag érvénytelen, nem a runbookokban, mivel ez a parancsmag használata interaktív felhasználóknak szól.

## <a name="preference-variables"></a>Preferenciaváltozók
Használja a Windows PowerShell [preferenciaváltozók](https://technet.microsoft.com/library/hh847796.aspx) határozni, hogy miként reagál a különböző kimeneti adatfolyamokba küldött adatokra. Ezek a változók egy runbook vezérelheti, hogyan reagáljon a különböző adatfolyamokba küldött adatok állíthatja be.

Az alábbi táblázat a preferenciaváltozók használható a runbookok, valamint azok érvényes és alapértelmezett értékeit. Ez a táblázat csak a runbookokban érvényes értékeket tartalmazza. Az Azure Automation-en kívül a Windows PowerShell használatakor a preferenciaváltozók más érvényes értéket is.

| Változó | Alapértelmezett érték | Érvényes értékek |
|:--- |:--- |:--- |
| WarningPreference |Továbbra is |Leállítás<br>Továbbra is<br>Folytatáscsendben |
| ErrorActionPreference |Továbbra is |Leállítás<br>Továbbra is<br>Folytatáscsendben |
| VerbosePreference |Folytatáscsendben |Leállítás<br>Továbbra is<br>Folytatáscsendben |

A következő táblázat felsorolja a runbookokban érvényes szabályozó változó értéke a viselkedés.

| Érték | Viselkedés |
|:--- |:--- |
| Továbbra is |Naplózza az üzenetet, és folytatja a runbook futtatását. |
| Folytatáscsendben |Továbbra is fennáll, az üzenet naplózása nélkül a runbook futtatását. Ez az érték a hatása, figyelmen kívül hagyja az üzenetet. |
| Leállítás |Naplózza az üzenetet, és felfüggeszti a runbook futtatását. |

## <a name="runbook-output"></a>Runbook-kimenet és üzenetek lekérése
### <a name="azure-portal"></a>Azure Portal
A runbook-feladat részleteit megtekintheti a feladatok lapon, a runbookok az Azure Portalon. A feladat összegzését jeleníti meg a bemeneti paraméterek, és a [kimeneti Stream](#output-stream) a feladat és a kivételek, ha azok kapcsolatos általános adatok mellett. Az előzmények üzeneteit tartalmazza a [kimeneti Stream](#output-stream) és [figyelmeztető és Hibaadatfolyamok](#warning-and-error-streams) , valamint a [részletes Stream](#verbose-stream) és [Állapotrekordokat](#progress-records) , ha a runbook részletes és állapotrekordokat van beállítva.

### <a name="windows-powershell"></a>Windows PowerShell
A Windows PowerShellben, lekérheti-kimenet és üzenetek runbookok a [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) parancsmagot. A parancsmag megköveteli a feladat Azonosítóját, és a egy paraméter neve adhatja meg melyik adatfolyamot adja vissza a Stream. Megadhat **bármely** a feladathoz tartozó összes adatfolyamot visszaadja.

A következő példában elindul a runbook, és megvárja, amíg az befejeződik. Ha befejeződött, a kimeneti adatfolyamot a feladatból gyűjti.

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

### <a name="graphical-authoring"></a>Grafikus létrehozásról
Grafikus runbookok esetében, az extra naplózás érhető el tevékenységszintű nyomon követés formájában. Nincsenek nyomkövetési két szintet: Alapszintű, és részletes. Alapszintű nyomon követését, láthatja a kezdési és befejezési idő, az egyes tevékenységek a runbookot, és minden tevékenység-újrapróbálkozások kapcsolatos adatokat. Néhány példa, a kísérletek számát, és a tevékenység kezdési időpontja. A részletes nyomon követését, kap az alapszintű nyomkövetés plusz bemeneti és kimeneti adatokat minden egyes tevékenységhez. Jelenleg a nyomkövetési rekordok használatával írt részletes adatfolyama így engedélyeznie kell a részletes naplózást, ha engedélyezi a nyomkövetési. A grafikus runbookok esetében a nyomkövetés engedélyezve van nincs állapotrekordok naplózására van szükség. Alapszintű nyomkövetés ugyanazt a célt szolgálja, és több információs célokat szolgál.

![Grafikus szerzői feladat adatfolyamok megtekintése](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Megjelenik az az előző képernyőképen látható, hogy ha engedélyezi a részletes naplózás és nyomkövetés a grafikus runbookokban, sokkal több információ érhető el a termelési Feladatstreamek megtekintése. Lehet, hogy a további információk a runbook üzemi kapcsolatos problémák alapvető, és ezért csak akkor engedélyezze, erre a célra, nem pedig egy általános gyakorlat. Lehet, hogy a nyomkövetési rekordok különösen számos. Grafikus forgatókönyv nyomon követését, a kettő vagy négy rekordok száma attól függően, hogy konfigurálta az alap vagy részletes nyomkövetési tevékenység kérheti le. Ha nem kell ezt az információt a hibaelhárítási forgatókönyv előrehaladását úgy követheti nyomon, érdemes folyamatosan nyomon követése ki van kapcsolva.

**Tevékenységszintű nyomon követés engedélyezéséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **Folyamatautomatizálás**válassza **Runbookok** forgatókönyvek listájának megnyitásához.
3. A forgatókönyvek oldalon kattintással jelölje ki a grafikus runbookok forgatókönyvek listájából.
4. A **beállítások**, kattintson a **naplózás és nyomkövetés**.
5. Kattintson a naplózás és nyomkövetés oldalon, a részletes rekordok naplózása **a** engedélyezi a részletes naplózást, és a tevékenységszintű nyomon követés, módosítsa a nyomkövetési szintet **alapszintű** vagy **részletes** szükséges nyomkövetési szint alapján.<br>
   
   ![Grafikus szerzői műveletek naplózás és nyomkövetés lap](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-log-analytics"></a>A Microsoft Azure Naplóelemzés
Automation küldhet a runbook állapota és a feladat feladatstreamek a Log Analytics-munkaterületre. A Log Analytics használatával is,

* Ismerkedjen meg az Automation-feladatokkal 
* Az eseményindító egy e-mailben vagy a riasztások alapján a forgatókönyv-feladat állapota (például felfüggesztett vagy sikertelen) 
* Speciális lekérdezések írása a feladatstreamek között 
* Feladatok összekapcsolása az Automation-fiókok 
* A feladatelőzmények megjelenítése időbeli alakulása    

Történő integráció konfigurálása a Log Analytics összegyűjtését, összekapcsolását és a feladat az adatokkal műveleteket végezni, további információkért lásd: [feladat állapotát és a feladatstreamek automatizálást továbbítása a Log Analyticsbe](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>További lépések
* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza
* Megtudhatja, hogyan tervezhet és gyermek runbookok, lásd: [gyermek runbookok az Azure Automationben](automation-child-runbooks.md)


