---
title: Runbook-kimenet és üzenetek az Azure Automationben
description: Bemutatja, hogyan hozhat létre és kérhet le kimeneti és hibaüzeneteket a runbookokból az Azure Automationben.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 92b6378b00e12f618d07798b5ce789cbd9971544
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535536"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook-kimenet és üzenetek az Azure Automationben

A legtöbb Azure Automation runbookok rendelkeznek valamilyen kimeneti formában. Ez a kimenet lehet egy hibaüzenet a felhasználó vagy egy összetett objektum, amelynek célja, hogy egy másik runbook. A Windows PowerShell [több adatfolyamot](/powershell/module/microsoft.powershell.core/about/about_redirection) biztosít a parancsfájlból vagy munkafolyamatból származó kimenet küldéséhez. Az Azure Automation az egyes adatfolyamokkal eltérően működik. Kövesse az adatfolyamok használatának ajánlott eljárásokat, amikor runbookot hoz létre.

Az alábbi táblázat röviden ismerteti az egyes adatfolyamokat a közzétett runbookok azure-portálján és a [runbooktesztelése](automation-testing-runbook.md)során. A kimeneti adatfolyam a runbookok közötti kommunikáció fő adatfolyama. A többi adatfolyam üzenetfolyamként van besorolva, és az információ felhasználóval való közlésére szolgál. 

| Stream | Leírás | Közzétett | Tesztelés |
|:--- |:--- |:--- |:--- |
| Hiba |A felhasználónak szóló hibaüzenet. A kivétellel ellentétben a runbook alapértelmezés szerint hibaüzenet után folytatódik. |Írásban a munka előzmények |Megjelenik a Teszt kimenet ablaktáblában |
| Hibakeresés |Interaktív felhasználó számára készült üzenetek. Nem használható runbookokban. |Nem írt feladatelőzmények |Nem jelenik meg a Teszt kimenet ablaktáblában |
| Kimenet |Másik runbookok számára készült objektum. |Írásban a munka előzmények |Megjelenik a Teszt kimenet ablaktáblában |
| Előrehaladás |A runbook összes tevékenysége előtt és után automatikusan létrejövő rekordok. A runbook nem próbálja meg létrehozni a saját folyamatrekordokat, mivel azok egy interaktív felhasználó számára. |Csak akkor írva a feladatelőzményekbe, ha a runbook folyamatnaplózása be van kapcsolva |Nem jelenik meg a Teszt kimenet ablaktáblában |
| Részletes |Általános vagy hibakeresési információkat tartalmazó üzenetek. |Csak akkor írva a feladatelőzményekbe, ha a runbook részletes naplózása be van kapcsolva |Csak akkor jelenik `VerbosePreference` meg a Teszt kimenet iatáblában, ha a változó a Runbookban való folytatás ra van beállítva |
| Figyelmeztetés |A felhasználónak szóló figyelmeztető üzenet. |Írásban a munka előzmények |Megjelenik a Teszt kimenet ablaktáblában |

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="output-stream"></a>Kimeneti adatfolyam

A kimeneti adatfolyam a parancsfájl vagy munkafolyamat által létrehozott objektumok kimenetére szolgál, ha az megfelelően fut. Az Azure Automation elsősorban ezt az adatfolyamot használja az [aktuális runbookot](automation-child-runbooks.md)hívó szülő runbookok által felhasznált objektumokhoz. Amikor egy szülő [egy runbook inline-t hív meg,](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)a gyermek adatokat ad vissza a kimeneti adatfolyamból a szülőnek. 

A runbook a kimeneti adatfolyam ot használja az általános információk kommunikálására az ügyféllel csak akkor, ha azt soha nem hívta meg egy másik runbook. Ajánlott eljárásként azonban a runbookok általában a [részletes adatfolyamot](#verbose-stream) kell használniuk az általános információk közléséhez a felhasználóval.

A runbook adatokírási adatokat a kimeneti adatfolyam [write-output](https://technet.microsoft.com/library/hh849921.aspx)használatával. Azt is megteheti, hogy az objektumot a saját sorára helyezi a parancsfájlban.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Kimenet kezelése egy függvényből

Amikor egy runbook-függvény a kimeneti streambe ír, a kimenet visszakerül a runbookba. Ha a runbook hozzárendeli, hogy a kimenet egy változóhoz, a kimenet nem íródik a kimeneti adatfolyamba. Írás bármely más stream a függvényen belül írja a megfelelő stream a runbook. Fontolja meg a következő minta PowerShell-munkafolyamat runbook.

```powershell
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

A runbook-feladat kimeneti adatfolyama a következő:

```output
Output inside of function
Output outside of function
```

A részletes stream a runbook feladat:

```output
Verbose outside of function
Verbose inside of function
```

Miután közzétette a runbookot, és mielőtt elindítané, be kell kapcsolnia a részletes naplózást a runbook-beállításokban a részletes adatfolyam-kimenet lefoglalásához.

### <a name="declaring-output-data-type"></a>Kimeneti adattípus deklarálása

Az alábbi példák a kimeneti adattípusokra mutatnak be:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Kimeneti adattípus deklarálása munkafolyamatban

A munkafolyamat a [Kimenettípusa attribútum](https://technet.microsoft.com/library/hh847785.aspx)mal adja meg a kimenet adattípusát. Ez az attribútum nincs hatása futásidőben, de ez jelzi a tervezési időben a runbook várható kimenetét. A runbookok eszközkészletének előrehaladtával a kimeneti adattípusok tervezési időpontban történő deklarálásának fontossága nő. Ezért ajánlott ezt a deklarációt a létrehozott runbookokba foglalni.

Az alábbi példában a runbook kimenete egy sztring típusú objektum, és megadja az elvárt kimenettípust. Amennyiben a runbook kimenete egy bizonyos típusú tömb, akkor is egy típust érdemes megadni, nem pedig a típusok egy tömbjét.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Kimeneti adattípus deklarálása grafikus runbookban

Ha egy kimeneti típust deklarálni szeretne egy grafikus vagy grafikus PowerShell-munkafolyamat-runbookban, **kiválaszthatja** a Bemenet i és kimenet menüpontot, és megadhatja a kimenet típusát. Javasoljuk, hogy a teljes .NET osztálynevet használja a típus könnyen azonosíthatóvá tenni, amikor egy szülő runbook hivatkozik rá. A teljes név használatával az osztály összes tulajdonsága elérhetővé teszi a runbookban lévő databus számára, és növeli a rugalmasságot, ha a tulajdonságokfeltételes logika, naplózás és más runbook-tevékenységek értékeiként vannak használva.<br> ![Runbook bemeneti és kimeneti beállítás](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Miután beírt egy értéket a Bemeneti és kimeneti tulajdonságok ablaktábla **Kimenettípusa** mezőjébe, mindenképpen kattintson a vezérlőn kívülre, hogy az felismerje a bejegyzést.

A következő példa két grafikus runbookot mutat be a bemeneti és kimeneti funkció bemutatására. A moduláris runbook-tervezési modell alkalmazása, egy runbook, mint a Hitelesített Runbook sablon hitelesítése az Azure-ral a Futtatás másként fiók használatával. A második runbook, amely általában egy adott forgatókönyv automatizálásához alapvető logikát hajt végre, ebben az esetben végrehajtja a Runbook hitelesítése sablont. Megjeleníti az eredményeket a teszt kimeneti ablaktáblán. Normál körülmények között, akkor ez a runbook tenni valamit egy erőforrás kihasználva a kimenetet a gyermek runbook.

Íme a **AuthenticateTo-Azure** runbook alapvető logikája.<br> ![Runbook-sablon](media/automation-runbook-output-and-messages/runbook-authentication-template.png)hitelesítése – példa .

A runbook tartalmazza `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`a kimeneti típust, amely a hitelesítési profil tulajdonságait adja vissza.<br> ![Példa a Runbook kimeneti típusra](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Bár ez a runbook egyszerű, van egy konfigurációs elem, amelyet itt ki kell hívni. Az utolsó tevékenység `Write-Output` végrehajtja a parancsmaparancsot, hogy profiladatokat `Inputobject` írjon egy változóba egy PowerShell-kifejezés használatával a paraméterhez. Ez a paraméter `Write-Output`szükséges a hoz.

A második runbook ebben a példában, **nevű Test-ChildOutputType,** egyszerűen két tevékenységet határoz meg.<br> ![Példa gyermekkimeneti típusú runbookra](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Az első tevékenység meghívja a **AuthenticateTo-Azure** runbook. A második tevékenység `Write-Verbose` a parancsmast futtatja, **amelynek adatforrás-beállítása** **Tevékenység kimenet.** Emellett **a mező elérési útja** **context.Subscription.SubscriptionName**, a **AuthenticateTo-Azure** runbook környezetkimenete.<br> ![Részletes parancsmag paraméteradatforrása](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Az eredményül kapott kimenet az előfizetés neve.<br> ![Test-ChildOutputType Runbook eredmények](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Üzenetfolyamok

A kimeneti adatfolyamtól eltérően az üzenetfolyamok adatokat továbbítanak a felhasználónak. A különböző típusú információkhoz több üzenetadat létezik, és az Azure Automation az egyes adatfolyamokat eltérően kezeli.

### <a name="warning-and-error-streams"></a>Figyelmeztetési és hibaadatfolyamok

A figyelmeztetés és hiba streameli a runbookban előforduló hibákat. Az Azure Automation ezeket az adatfolyamokat a feladatelőzményekbe írja egy runbook végrehajtásakor. Az automatizálás az Azure Portalon a Teszt kimeneti ablaktáblában lévő adatfolyamokat tartalmazza, ha egy runbookot tesztelnek. 

Alapértelmezés szerint a runbook továbbra is végrehajtja egy figyelmeztetés vagy hiba után. Megadhatja, hogy a runbook fel kell függeszteni egy figyelmeztetés vagy hiba, ha a runbook beállít egy [beállítási változó](#preference-variables) az üzenet létrehozása előtt. Ha például a runbook ot egy hiba miatt függeszti `ErrorActionPreference` fel, ahogy azt egy kivételesetében teszi, állítsa a változót Leállításra.

Hozzon létre egy figyelmeztető vagy hibakérő üzenetet az [Írás-figyelmeztetés](https://technet.microsoft.com/library/hh849931.aspx) vagy [írási hiba](https://technet.microsoft.com/library/hh849962.aspx) parancsmag használatával. A tevékenységek a Figyelmeztetés és hiba adatfolyamokba is írhatnak.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Hibakeresési adatfolyam

Az Azure Automation a Hibakeresési üzenetfolyamot használja az interaktív felhasználók számára. Nem használható runbookokban.

### <a name="verbose-stream"></a>Részletes adatfolyam

A részletes üzenetfolyam támogatja a Runbook-művelet általános adatait. Mivel a hibakeresési adatfolyam nem érhető el egy runbookhoz, a runbooknak részletes üzeneteket kell használnia a hibakeresési információkhoz. 

Alapértelmezés szerint a feladatelőzmények teljesítménybeli okokból nem tárolnak részletes üzeneteket a közzétett runbookokról. Részletes üzenetek tárolásához használja az Azure Portal **Konfigurálás** lapját a **Napló részletes rekordok** beállítással a közzétett runbookok részletes üzenetek naplózására történő konfigurálásához. Ezt a beállítást csak akkor érdemes bekapcsolni, ha hibát keres a runbookban, vagy ha hibaelhárításra van szükség. A legtöbb esetben meg kell tartani a részletes rekordok naplózásának elnem e-műveletek naplózásának alapértelmezett beállítását.

[Runbook tesztelése](automation-testing-runbook.md)során a részletes üzenetek nem jelennek meg, még akkor sem, ha a runbook részletes rekordok naplózására van konfigurálva. Ha részletes üzeneteket szeretne megjeleníteni [a runbook tesztelése](automation-testing-runbook.md)közben, a `VerbosePreference` változót continue (Folytatás) kell beállítania. Ezzel a változókészlettel részletes üzenetek jelennek meg az Azure Portal teszt kimeneti ablaktáblájában.

A következő kód részletes üzenetet hoz létre a [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) parancsmag használatával.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Folyamatrekordok

Az Azure Portal **Konfigurálás** lapján konfigurálhat egy runbookot a folyamatrekordok naplózásához. Az alapértelmezett beállítás az, hogy nem naplózza a rekordokat, hogy maximalizálja a teljesítményt. A legtöbb esetben meg kell tartani az alapértelmezett beállítást. Ezt a beállítást csak akkor érdemes bekapcsolni, ha hibát keres a runbookban, vagy ha hibaelhárításra van szükség. 

Ha engedélyezi a folyamatrekord-naplózást, a runbook minden tevékenység futtatása előtt és után egy rekordot ír a feladatelőzményekbe. A runbook tesztelése nem jeleníti meg a folyamatjelző üzeneteket, még akkor sem, ha a runbook a végrehajtási rekordok naplózására van konfigurálva.

>[!NOTE]
>A [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) parancsmag nem érvényes a runbookokban, mivel ez a parancsmag interaktív felhasználókszámára készült.

## <a name="preference-variables"></a>Beállítási változók

Beállíthatja, hogy bizonyos Windows [PowerShell-beállítási változók](https://technet.microsoft.com/library/hh847796.aspx) a runbookok, hogy szabályozhassa a különböző kimeneti adatfolyamok küldött válaszokat. Az alábbi táblázat a runbookokban használható beállítási változókat sorolja fel az alapértelmezett és érvényes értékekkel. További értékek érhetők el a beállítási változók, ha az Azure Automationen kívül a Windows PowerShell ben használja.

| Változó | Alapértelmezett érték | Érvényes értékek |
|:--- |:--- |:--- |
| `WarningPreference` |Folytatás |Leállítás<br>Folytatás<br>FolytatásCsendben |
| `ErrorActionPreference` |Folytatás |Leállítás<br>Folytatás<br>FolytatásCsendben |
| `VerbosePreference` |FolytatásCsendben |Leállítás<br>Folytatás<br>FolytatásCsendben |

A következő táblázat a runbookokban érvényes beállítási változóértékek viselkedését sorolja fel.

| Érték | Viselkedés |
|:--- |:--- |
| Folytatás |Naplózza az üzenetet, és folytatja a runbook futtatását. |
| FolytatásCsendben |Az üzenet naplózása nélkül folytatja a runbook futtatását. Ennek az értéknek az a hatása, hogy figyelmen kívül hagyja az üzenetet. |
| Leállítás |Naplózza az üzenetet, és felfüggeszti a runbook futtatását. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Runbook kimenetének és üzenetének beolvasása

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Runbook-kimenet és üzenetek lekérése az Azure Portalon

Megtekintheti a runbook-feladat részleteit az Azure Portalon a **Runbook Feladatok** lapján. A feladat összegzése megjeleníti a bemeneti paramétereket és a [Kimeneti adatfolyamot,](#output-stream)a feladatra vonatkozó általános információkon és a felmerült kivételeken kívül. A feladatelőzmények a Kimeneti adatfolyamból, valamint a [Figyelmeztetési és Hibafolyamokból származó üzeneteket tartalmazzák.](#warning-and-error-streams) A [részletes adatfolyam-](#verbose-stream) és [folyamatrekordokból](#progress-records) származó üzeneteket is tartalmazza, ha a runbook részletes és végrehajtási rekordok naplózására van konfigurálva.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Runbook-kimenet és üzenetek beolvasása a Windows PowerShellben

A Windows PowerShell ben a [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) parancsmag használatával lekérheti a kimeneti és üzeneteket egy runbookból. Ez a parancsmag a feladat azonosítóját igényli, `Stream` és rendelkezik egy nevű paraméterrel, amelyben meg adható a beolvasni kívánt adatfolyam. Megadhat egy Any értéket ehhez a paraméterhez, hogy a feladathoz az összes adatfolyamot lekérje.

A következő példában elindul a runbook, majd a rendszer megvárja, amíg annak végrehajtása befejeződik. Miután a runbook végrehajtása befejeződött, a parancsfájl összegyűjti a runbook kimeneti stream a feladatból.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Runbook-kimenet és üzenetek beolvasása grafikus runbookokban

Grafikus runbookok esetén a kimenetek és az üzenetek extra naplózása tevékenységszintű nyomkövetés formájában érhető el. A nyomon követésnek két szintje van: Alap szintű és részletes. Az alapszintű nyomkövetés megjeleníti a runbook egyes tevékenységének kezdési és befejezési idejét, valamint a tevékenység-újrapróbálkozásokkal kapcsolatos információkat. Néhány példa a kísérletek száma és a tevékenység kezdési időpontja. A részletes nyomkövetés alapvető nyomkövetési funkciókat, valamint az egyes tevékenységek bemeneti és kimeneti adatainak naplózását tartalmazza. 

Jelenleg a tevékenységszintű nyomkövetés a részletes adatfolyam használatával írja a rekordokat. Ezért a nyomkövetés engedélyezésekor engedélyeznie kell a részletes naplózást. A grafikus runbookok nyomkövetés engedélyezve van, nincs szükség a folyamatrekordok naplózására. Az alapvető nyomon követés ugyanazt a célt szolgálja, és informatívabb.

![Grafikus szerzői feladatadatfolyamok nézete](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

A képből láthatja, hogy a részletes naplózás és a grafikus runbookok nyomon követése sokkal több információt tesz elérhetővé az éles **feladatstreamek** nézetben. Ez a további információ alapvető fontosságú lehet a runbookokkal kapcsolatos éles problémák elhárításához. 

Azonban, ha nem kell ezt az információt a runbook hibaelhárítási folyamatának nyomon követéséhez, érdemes lehet a nyomkövetést általános gyakorlatként kikapcsolni. A nyomkövetési adatok különösen nagyok lehetnek. A grafikus runbook-nyomkövetés lelhető fel, tevékenységenként két-négy rekordot kaphat, az alapszintű vagy részletes nyomkövetés konfigurációjától függően.

**A tevékenységszintű nyomkövetés engedélyezése:**

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza **a Runbookok** **a Folyamatautomatizálás** csoportban a runbookok listájának megnyitásához.
3. A Runbookok lapon válasszon ki egy grafikus runbookot a runbookok listájából.
4. A **Beállítások csoportban**kattintson **a Naplózás és nyomkövetés gombra.**
5. A Naplózás és követés lap **Részletes rekordok naplózása**csoportban kattintson **a Be** gombra a részletes naplózás engedélyezéséhez.
6. A **Tevékenységszintű nyomkövetés**csoportban módosítsa a nyomkövetési szintet **Alap szintű** vagy **Részletes**szintre a szükséges nyomkövetési szint alapján.<br>

   ![Grafikus szerzői naplózás és nyomkövetés lap](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Runbook-kimenet és üzenetek beolvasása a Microsoft Azure Figyelő naplóiban

Az Azure Automation runbook-feladatállapotot és feladatstreameket küldhet a Log Analytics-munkaterületre. Az Azure Monitor támogatja a naplókat, amelyek lehetővé teszik, hogy:

* Betekintést nyerhet az Automatizálási feladatokba.
* E-mailt vagy riasztást aktiválanak a runbook-feladat állapota alapján, például sikertelen vagy felfüggesztett.
* Speciális lekérdezések írása a feladatadatfolyamok között.
* Feladatok korrelálása az Automation-fiókok között.
* A feladatelőzmények megjelenítése.

Az Azure Monitor-naplókkal való integráció konfigurálásáról a feladatadatok gyűjtésére, korrelációjával és az azokra való cselekvésre vonatkozótovábbi információért olvassa el a [Feladat állapotának továbbítása és feladatadatfolyamok továbbítása az Automationből az Azure Monitor-naplókba című témakört.](automation-manage-send-joblogs-log-analytics.md)

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a runbook-végrehajtásról, a Runbook-feladatok figyeléséről és egyéb technikai részletekről, olvassa el [a Runbook-feladat nyomon követése című témakört.](automation-runbook-execution.md)
* A gyermekrunbookok tervezésének és használatának megértéséhez olvassa el [a Gyermek runbookok az Azure Automationben.](automation-child-runbooks.md)
* A PowerShellről további információt, beleértve a nyelvi referencia- és tanulási modulokat, tekintse meg a [PowerShell-dokumentumok című témakört.](/powershell/scripting/overview)
