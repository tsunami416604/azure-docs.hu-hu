---
title: Runbook kimenete és üzenetei Azure Automation
description: Ismerteti, hogyan lehet kimeneti és hibaüzeneteket létrehozni és beolvasni a runbookok Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: af199439fedddaef5b1bd3b219a60db697fb25ab
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849649"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook kimenete és üzenetei Azure Automation

A legtöbb Azure Automation runbookok valamilyen típusú kimenettel rendelkezik. Ez a kimenet hibaüzenetet jelenthet a felhasználónak vagy egy másik runbook használni kívánt összetett objektumnak. A Windows PowerShell [több streamet](/powershell/module/microsoft.powershell.core/about/about_redirection) biztosít a kimenetek parancsfájlokból vagy munkafolyamatokból való küldéséhez. A Azure Automation az egyes streamek esetében különbözőképpen működik. A runbook létrehozásakor ajánlott eljárásokat kell követnie.

Az alábbi táblázat az egyes adatfolyamok rövid leírását, valamint azok viselkedését ismerteti Azure Portal a közzétett runbookok és a [runbook tesztelésekor](automation-testing-runbook.md). Az egyes streamekről további részleteket a későbbi részekben talál.

| Stream | Leírás | Közzétéve | Tesztelés |
|:--- |:--- |:--- |:--- |
| Kimenet |Másik runbookok számára készült objektum. |Szerepel a feladatelőzményekben. |Fel van tüntetve a Tesztkimenet ablaktáblán. |
| Figyelmeztetés |A felhasználónak szóló figyelmeztető üzenet. |Szerepel a feladatelőzményekben. |Fel van tüntetve a Tesztkimenet ablaktáblán. |
| Hiba |A felhasználónak szóló hibaüzenet. A runbook alapértelmezés szerint tovább fut egy hibaüzenet megjelenésekor (nem úgy, mint a kivételek esetében). |Szerepel a feladatelőzményekben. |Fel van tüntetve a Tesztkimenet ablaktáblán. |
| Részletezés |Általános vagy hibakeresési adatokat tartalmazó üzenetek. |Csak akkor szerepel a feladatelőzményekben, ha a runbookban is engedélyezve van a részletes naplózás. |Csak akkor van feltüntetve a Tesztkimenet ablaktáblán, ha a runbook $VerbosePreference beállítása Folytatás értékű. |
| Állapot |A runbook összes tevékenysége előtt és után automatikusan létrejövő rekordok. A runbook nem próbálja meg létrehozni a saját előrehaladási rekordokat, mivel azok interaktív felhasználók számára készültek. |Csak akkor szerepel a feladatelőzményekben, ha a runbookban is engedélyezve van az állapot naplózása. |Nincs feltüntetve a Tesztkimenet ablaktáblán. |
| Hibakeresés |Interaktív felhasználó számára készült üzenetek. Nem használható a runbookok. |Nem szerepel a feladatelőzményekben. |Nincs feltüntetve a Tesztkimenet ablaktáblán. |

## <a name="output-stream"></a>Kimeneti adatfolyam
A kimeneti adatfolyam olyan objektumok kimenetére szolgál, amelyeket egy parancsfájl vagy munkafolyamat a megfelelő futtatásakor hoz létre. Azure Automation ez az adatfolyam elsősorban olyan objektumok esetében használatos, amelyeket [az aktuális runbook meghívó szülő-runbookok](automation-child-runbooks.md)kíván felhasználni. Amikor egy fölérendelt runbook [meghívja a runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) , a kimeneti adatfolyamból a szülőnek küldi az adatokat. Ha tudja, hogy a runbook soha nem hívja meg egy másik runbook, csak a kimeneti adatfolyamot használja az általános adatok továbbítására a felhasználónak. Ajánlott eljárásként azonban általában a [részletes adatfolyamot](#verbose-stream) kell használnia az általános információk felhasználóhoz való továbbításához.

Az adatokat [írási kimenettel](https://technet.microsoft.com/library/hh849921.aspx) vagy az objektumnak a runbook saját sorában történő elhelyezésével írhatja be a kimeneti adatfolyamba.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Függvény kimenete

Amikor a runbook található függvényben ír a kimeneti adatfolyamba, a kimenet vissza lesz adva a runbook. Amennyiben a runbook hozzárendeli azt egy változóhoz, a kimenet nem fog szerepelni a kimeneti adatfolyamban. A függvényen belüli bármely más streambe való írás a runbook tartozó megfelelő streambe ír.

Vegye figyelembe a következő minta runbook:

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

A runbook feladatának kimeneti adatfolyama így néz ki:

```output
Output inside of function
Output outside of function
```

A runbook feladatának részletes adatfolyama így néz ki:

```output
Verbose outside of function
Verbose inside of function
```

Miután közzétette a runbook, és megkezdte a megkezdését, be kell kapcsolni a részletes naplózást a runbook-beállításokban a részletes adatfolyam kimenetének lekéréséhez.

### <a name="declaring-output-data-type"></a>A kimeneti adattípus deklarálása

A munkafolyamat a [OutputType attribútum](https://technet.microsoft.com/library/hh847785.aspx)használatával adhatja meg a kimenet adattípusát. Az attribútum semmit sem befolyásol a futtatás során, de a tervezés során jelzi a runbook szerzőjének, hogy a runbooknak milyen kimenetet kell létrehoznia. Mivel a runbookok eszközkészlete folyamatosan fejlődik, fontos, hogy a tervezési időszakban a kimeneti adattípusok bejelentsék a fontosságot. Ennek eredményeképpen az ajánlott eljárás a deklaráció belefoglalása bármely létrehozott runbookok.

Az alábbi lista a kimeneti típusokat tartalmazza:

* System. String
* System. Int32
* System. Collections. szórótábla
* Microsoft. Azure. commands. számítás. models. PSVirtualMachine

Az alábbi példában a runbook kimenete egy sztring típusú objektum, és megadja az elvárt kimenettípust. Amennyiben a runbook kimenete egy bizonyos típusú tömb, akkor is egy típust érdemes megadni, nem pedig a típusok egy tömbjét.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Ha be szeretne jelenteni egy kimeneti típust a grafikus vagy a grafikus PowerShell-munkafolyamat runbookok, válassza a **bemeneti és kimeneti** menü lehetőséget, és írja be a kimeneti típus nevét. Javasoljuk, hogy a teljes .NET-osztály nevét használva könnyen azonosítható legyen, amikor egy szülő runbook hivatkozik rá. Ez az osztály összes tulajdonságát elérhetővé teszi a runbook lévő adatbuszon, és nagy rugalmasságot biztosít, ha feltételes logikát, naplózást és hivatkozásokat használ a runbook más tevékenységeinek értékére.<br> ![Runbook bemeneti és kimeneti beállítása](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

A következő példában két grafikus runbookok van a funkció bemutatásához. Ha a moduláris runbook tervezési modellt alkalmazza, akkor egy runbook rendelkezik, amely az Azure-ban a futtató fiókkal történő hitelesítést kezelő *hitelesítési runbook-sablonként* szolgál. A második runbook, amely normál logikát alkalmaz az adott forgatókönyv automatizálására, ebben az esetben a *hitelesítési runbook sablont* fogja végrehajtani, és megjeleníti az eredményeket a **test** output (kimenet) ablaktáblán. Normális körülmények között ez a runbook a gyermek runbook kimenetét kihasználó erőforráson végezhető el.

Itt látható a AuthenticateTo alapszintű logikája **– Az Azure** runbook.<br> ![hitelesítő Runbook-sablon például](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

Tartalmazza a *Microsoft. Azure. commands. profile. models. PSAzureContext*kimeneti típust, amely a hitelesítési profil tulajdonságait adja vissza.<br> ![Runbook kimeneti típusának példája](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Habár ez a runbook egyenes továbbítva van, egy konfigurációs elem is meghívja Önt. Az utolsó tevékenység hajtja végre a **Write-output** parancsmagot, és a **inputobject elemnél** paraméterhez egy PowerShell-kifejezéssel írja a profil adatokat egy $ _ változóba, amely a parancsmaghoz szükséges.

Ebben a példában a *test-ChildOutputType*nevű második runbook csupán két tevékenységgel rendelkezik.<br> ![példa gyermek kimeneti típus Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Az első tevékenység meghívja a **AuthenticateTo-Azure** runbook, és a második tevékenység a **Write-verbose** parancsmagot futtatja a **tevékenység kimenetének** **adatforrásával** , és a **mező elérési útjának** értéke **Context. Subscription. SubscriptionName**, amely a **AuthenticateTo-Azure** runbook környezeti kimenetét adja meg.<br> ![Write-verbose parancsmag paraméter-adatforrás](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Az eredményül kapott kimenet az előfizetés neve.<br> ![Test-ChildOutputType Runbook eredményei](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

> [!NOTE]
> Miután megadta az értéket a **bemeneti és kimeneti tulajdonságok** ablaktáblán a **kimenet típusa** mezőben, a vezérlőn kívülre kell kattintania, hogy a bejegyzés felismerhető legyen.

## <a name="message-streams"></a>Üzenet streamek

A kimeneti adatfolyamokkal ellentétben az üzenet-adatfolyamok kifejezetten a felhasználó felé történő információtovábbítást szolgálják. Több üzenet-adatfolyam is létezik különböző típusú információkhoz, és mindegyiket a Azure Automation eltérően kezeli.

### <a name="warning-and-error-streams"></a>Figyelmeztetési és hiba-adatfolyamok

A figyelmeztető és hibaadatfolyamok a runbookban előforduló problémákat naplózzák. A runbook végrehajtásakor a rendszer a feladatok előzményeibe írja őket, és a Azure Portal tesztelési kimenet paneljén szerepel a runbook tesztelésekor. Az alapértelmezett beállítások szerint a runbook végrehajtását nem szakítják meg a figyelmeztető vagy hibaüzenetek. Megadhatja, hogy a runbook figyelmeztetés vagy hiba esetén felfüggesztve legyen, ha az üzenet létrehozása előtt beállít egy [preferencia változót](#preference-variables) a runbook. Ha például egy runbook egy hiba miatt felfüggeszti a hibát, mert kivétel lenne, állítsa **$ErrorActionPreference** leállítására.

Figyelmeztetés vagy hibaüzenet létrehozása a [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) vagy a [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) parancsmag használatával. A tevékenységek is írhatnak adatokat ezekbe az adatfolyamokba.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Részletes adatfolyam

A részletes üzenet-adatfolyam a runbook-művelettel kapcsolatos általános információkat tartalmazza. Mivel a [hibakeresési adatfolyam](#debug-stream) nem érhető el egy runbook, a hibakeresési adatokhoz részletes üzeneteket kell használni. Alapértelmezés szerint a közzétett runbookok származó részletes üzeneteket a rendszer nem tárolja a feladatok előzményeiben. A részletes üzenetek tárolásához konfigurálja a közzétett runbookok, hogy naplózza a részletes rekordokat a Azure Portal runbook a configure (Konfigurálás) lapján. A legtöbb esetben a jobb teljesítmény érdekében érdemes megtartani az alapértelmezett beállítást, amely nem naplózza a runbook részletes rekordjait. Ezt a beállítást csak akkor érdemes bekapcsolni, ha hibát keres a runbookban, vagy ha hibaelhárításra van szükség.

[Runbook tesztelésekor](automation-testing-runbook.md)a részletes üzenetek akkor sem jelennek meg, ha a runbook a részletes rekordok naplózására van konfigurálva. A [runbook tesztelésekor](automation-testing-runbook.md)a részletes üzenetek megjelenítéséhez be kell állítania a $VerbosePreference változót a folytatáshoz. Ha ez a változó be van állítva, a részletes üzenetek a Azure Portal teszt kimenet paneljén jelennek meg.

Hozzon létre egy részletes üzenetet a [Write-verbose](https://technet.microsoft.com/library/hh849951.aspx) parancsmag használatával.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Hibakeresési adatfolyam

A hibakeresési adatfolyam interaktív felhasználóknak szól, és nem runbookok általi használatra készült.

## <a name="progress-records"></a>Folyamatjelző rekordok

Ha úgy konfigurálja a runbook, hogy naplózza a folyamatjelző rekordokat (a Azure Portal a runbook konfiguráció lapján), akkor a rendszer az egyes tevékenységek futtatása előtt és után egy rekordot ír a feladatok előzményeibe. A legtöbb esetben a jobb teljesítmény érdekében érdemes megtartani az alapértelmezett beállítást, amely nem naplózza a runbook állapotrekordjait. Ezt a beállítást csak akkor érdemes bekapcsolni, ha hibát keres a runbookban, vagy ha hibaelhárításra van szükség. A runbook tesztelésekor az állapotüzenetek akkor sem jelennek meg, ha a runbook az állapotrekordok naplózására van beállítva.

A [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) parancsmag nem érvényes egy runbook, mert ez a parancsmag interaktív felhasználóval való használatra készült.

## <a name="preference-variables"></a>Preferencia változók

A Windows PowerShell [preferencia-változók](https://technet.microsoft.com/library/hh847796.aspx) használatával határozza meg, hogyan reagáljon a különböző kimeneti adatfolyamoknak küldött adatmennyiségre. Ezeket a változókat beállíthatja egy runbook, hogy megtudja, hogyan reagál a különböző streamekre küldött adatértékekre.

Az alábbi táblázat felsorolja a runbookokban használható preferenciaváltozókat, valamint azok érvényes és alapértelmezett értékeit. Ez a tábla csak a runbook érvényes értékeket tartalmazza. A további értékek érvényesek a prioritási változóknál, ha a Azure Automationon kívüli Windows PowerShellben használják.

| Változó | Alapértelmezett érték | Érvényes értékek |
|:--- |:--- |:--- |
| WarningPreference |Folytatás |Leállítás<br>Folytatás<br>FolytatásCsendben |
| ErrorActionPreference |Folytatás |Leállítás<br>Folytatás<br>FolytatásCsendben |
| VerbosePreference |FolytatásCsendben |Leállítás<br>Folytatás<br>FolytatásCsendben |

A következő táblázat a preferenciaváltozók runbookokban érvényes értékei esetén várható viselkedést írja le.

| Value (Díj) | Viselkedés |
|:--- |:--- |
| Folytatás |Naplózza az üzenetet, és folytatja a runbook futtatását. |
| FolytatásCsendben |Az üzenet naplózása nélkül folytatja a runbook futtatását. Ennek az értéknek az a következménye, hogy figyelmen kívül hagyja az üzenetet. |
| Leállítás |Naplózza az üzenetet, és felfüggeszti a runbook futtatását. |

## <a name="runbook-output"></a>Runbook-kimenet és üzenetek beolvasása

### <a name="azure-portal"></a>Azure Portal

Egy runbook-feladat részleteit a Azure Portal feladatok lapján tekintheti meg a runbook. A feladathoz tartozó összefoglalás megjeleníti a bemeneti paramétereket és a [kimeneti adatfolyamot](#output-stream) a feladattal kapcsolatos általános információk mellett, valamint a kivételeket, ha azok bekövetkeztek. Az előzmények a [kimeneti adatfolyamból](#output-stream) , a [Figyelmeztetési és a hiba-adatfolyamokból](#warning-and-error-streams) , valamint a [részletes adatfolyam](#verbose-stream) -és [folyamatjelző rekordokból](#progress-records) származó üzeneteket tartalmaznak, ha a runbook a részletes és a folyamatjelző rekordok naplózására van konfigurálva.

### <a name="windows-powershell"></a>Windows PowerShell

A Windows PowerShellben a runbook kimenetét és üzeneteit a [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) parancsmag használatával kérheti le. A parancsmag számára meg kell adni a feladat azonosítóját, valamint a Stream paramétert, amely meghatározza, hogy melyik adatfolyamot adja majd vissza. **Bármelyiket** megadhatja a feladatokhoz tartozó összes adatfolyam visszaadásához.

A következő példában elindul a runbook, majd a rendszer megvárja, amíg annak végrehajtása befejeződik. Ezt követően begyűjti a kimeneti adatfolyamot a feladatból.

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

### <a name="graphical-authoring"></a>Grafikus szerzői műveletek

Grafikus runbookok esetében a további naplózás tevékenység szintű nyomkövetés formájában érhető el. A nyomkövetésnek két szintje van: alapszintű és részletes. Az alapszintű nyomkövetésben megtekintheti az egyes tevékenységek kezdési és befejezési időpontját a runbook, valamint a tevékenység újrapróbálkozásával kapcsolatos információkat is. Néhány példa: a kísérletek száma és a tevékenység kezdési időpontja. A részletes nyomkövetés során az alapszintű nyomkövetést, valamint az egyes tevékenységekhez tartozó bemeneti és kimeneti adatokat kapja meg. A nyomkövetési rekordok jelenleg a részletes adatfolyam használatával íródnak, ezért a nyomkövetés engedélyezésekor engedélyeznie kell a részletes naplózást. A nyomkövetést engedélyező grafikus runbookok esetében nem kell naplózni a folyamatjelző rekordokat. Az alapszintű nyomkövetés ugyanazt a célt szolgálja, és több információt nyújt.

![Grafikus szerzői feladatok adatfolyam-nézete](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Az előző képernyőképen látható, hogy amikor engedélyezi a részletes naplózást és a nyomkövetést a grafikus runbookok, sokkal több információ érhető el az üzemi feladatok adatfolyam-nézetében. Ez a további információ elengedhetetlen lehet a runbook kapcsolatos éles problémák elhárításához, ezért ezt kizárólag erre a célra kell engedélyezni, nem pedig általános gyakorlatnak. A nyomkövetési rekordok különösen sokek lehetnek. A grafikus runbook nyomon követésével a tevékenységtől függően két-négy rekord adható meg, attól függően, hogy az alapszintű vagy a részletes nyomkövetést konfigurálta-e. Ha nincs szüksége ezekre az információkra, hogy nyomon követhesse a runbook állapotát a hibaelhárításhoz, érdemes lehet megtartani a nyomkövetést.

**A tevékenység szintű nyomkövetés engedélyezéséhez hajtsa végre a következő lépéseket:**

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **folyamat automatizálása**területen válassza a **runbookok** lehetőséget a runbookok listájának megnyitásához.
3. A Runbookok lapon kattintson a grafikus runbook kiválasztásához a runbookok listájából.
4. A **Beállítások**területen kattintson a **naplózás és nyomkövetés**elemre.
5. A naplózás és nyomkövetés lapon, a részletes naplózási rekordok naplózása területen kattintson **a** be gombra a részletes naplózás engedélyezéséhez és a tevékenység szintjének nyomon követéséhez, módosítsa a nyomkövetési szintet **alapértékre vagy** **részletesre** a szükséges nyomkövetési szint alapján.<br>

   ![Grafikus szerzői műveletek naplózása és nyomkövetése lap](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure figyelési naplók

Az Automation elküldheti a runbook feladatok állapotát és a feladatok streamjét a Log Analytics munkaterületre. Azure Monitor naplók segítségével

* Betekintést nyerhet az automatizálási feladatokba
* E-mailek vagy riasztások indítása a runbook-feladatok állapota alapján (például sikertelen vagy felfüggesztett)
* Speciális lekérdezések írása a feladatok adatfolyamai között
* Feladatok korrelációja az Automation-fiókok között
* A feladatok előzményeinek megjelenítése az idő függvényében

További információ arról, hogyan konfigurálható a Azure Monitor naplókkal való integráció a feladatok adatainak összegyűjtéséhez, korrelálása és működés közbeni működéséhez: a [feladatok állapotának és a feladatok adatfolyamának továbbítása az automatizálásból a Azure monitor naplókba](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Következő lépések

* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza
* A gyermek-runbookok kialakításának és használatának megismeréséhez tekintse meg [a Azure Automation gyermek runbookok](automation-child-runbooks.md)
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](/powershell/scripting/overview).
