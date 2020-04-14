---
title: Azure Automation-runbook-típusok
description: Ismerteti a különböző típusú runbookok, amelyek az Azure Automation-ben használható, és a használathoz használt típus meghatározásának szempontjai.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 4e8a5d2d168b8f60b7a32a8af358c6097003de60
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261295"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-runbook-típusok

Az Azure Automation folyamatautomatizálási szolgáltatás a következő táblázatban meghatározott runbookok többtípusát támogatja. A folyamatautomatizálási környezetről a [Runbook-végrehajtás az Azure Automationben](automation-runbook-execution.md)című témakörben olvashat.

| Típus | Leírás |
|:--- |:--- |
| [Grafikus](#graphical-runbooks)|A Windows PowerShell en alapuló grafikus runbook, amelyet az Azure Portal grafikus szerkesztőjében hozott létre és szerkesztett. |
| [Grafikus PowerShell-munkafolyamat](#graphical-runbooks)|A Windows PowerShell-munkafolyamaton alapuló grafikus runbook, amelyet az Azure Portal grafikus szerkesztőjében hozott létre és szerkesztett. |
| [PowerShell](#powershell-runbooks) |A Windows PowerShell-parancsfájlok on alapuló szöveges runbook. |
| [PowerShell-munkafolyamat](#powershell-workflow-runbooks)|A Windows PowerShell-munkafolyamat parancsfájljain alapuló szöveges runbook. |
| [Python](#python-runbooks) |Python-parancsfájlok alapján futókönyv. |

Vegye figyelembe a következő szempontokat annak meghatározásakor, hogy melyik típust használja egy adott runbookhoz.

* A runbookok nem konvertálhatók grafikusról szövegtípusra, illetve fordítva.
* A különböző típusú runbookok gyermek runbookok használatakor vannak korlátozások. További információ: [Child runbooks in Azure Automation.](automation-child-runbooks.md)

## <a name="graphical-runbooks"></a>Grafikus runbookok

Grafikus és grafikus PowerShell-munkafolyamat-runbookokat hozhat létre és szerkeszthet az Azure Portalon a grafikus szerkesztővel. Az ilyen típusú runbookok azonban nem hozhathatók létre és nem szerkeszthetők egy másik eszközzel. A grafikus runbookok főbb jellemzői:

* Exportálható az Automation-fiókfájljaiba, majd importálható egy másik Automation-fiókba. 
* PowerShell-kód létrehozása. 
* Grafikus PowerShell-munkafolyamat-runbookokba konvertálható az importálás során. 

### <a name="advantages"></a>Előnyök

* Használjon vizuális beszúrási-kapcsolat-konfigurálású szerzői modellt.
* Összpontosítson arra, hogy az adatok hogyan haladnak át a folyamaton.
* Vizuálisan képviseli a felügyeleti folyamatokat.
* Más runbookok közé tartozik a gyermek runbookok magas szintű munkafolyamatok létrehozásához.
* Ösztönözze a moduláris programozást.

### <a name="limitations"></a>Korlátozások

* Nem lehet létrehozni vagy szerkeszteni az Azure Portalon kívül.
* Előfordulhat, hogy egy PowerShell-kódot tartalmazó kódtevékenységet igényel az összetett logika végrehajtásához.
* Nem lehet [átkonvertálni](automation-runbook-types.md)a szövegformátumok egyikére, és a szöveges runbookot sem lehet grafikus formátumba konvertálni. 
* Nem lehet megtekinteni vagy közvetlenül szerkeszteni a grafikus munkafolyamat által létrehozott PowerShell-kódot. A kódtevékenységekben létrehozott kódot megtekintheti.
* Nem futtathatók runbookok linuxos hibrid runbook-feldolgozón. Lásd: [Erőforrások automatizálása az adatközpontban vagy a felhőben a hibrid Runbook-feldolgozó használatával.](automation-hybrid-runbook-worker.md)

## <a name="powershell-runbooks"></a>PowerShell-runbookok

A PowerShell-runbookok a Windows PowerShell-alapúak. Közvetlenül szerkesztheti a runbook kódját az Azure Portalon található szövegszerkesztő használatával.  Bármely offline szövegszerkesztőt használhat, és [importálhatja a runbookot az](manage-runbooks.md) Azure Automationbe.

### <a name="advantages"></a>Előnyök

* Valósítsa meg az összes összetett logikát a PowerShell-kóddal a PowerShell-munkafolyamat további összetettsége nélkül.
* Indítsa el gyorsabban, mint a PowerShell-munkafolyamat runbookok, mivel nem kell lefordítani a futás előtt.
* Futtassa az Azure-ban és a hibrid Runbook-feldolgozók windowsos és Linuxos rendszeren.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-parancsfájlok.
* A Runbookok nem [használhatják párhuzamos feldolgozással](automation-powershell-workflow.md#parallel-processing) több művelet párhuzamos végrehajtásához.
* A Runbookok nem [használhatják az ellenőrzőpontokat](automation-powershell-workflow.md#checkpoints) a runbook folytatásához, ha hiba történt.
* A [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) parancsmag használatával csak a PowerShell-munkafolyamat runbookok és grafikus runbookok lehetnek gyermek runbookok, amely új feladatot hoz létre.

### <a name="known-issues"></a>Ismert problémák

Az alábbiakban a PowerShell runbookokkal kapcsolatos aktuális ismert problémák at ismertek:

* A PowerShell-runbookok nem tudnak lekérni egy titkosítatlan [változóeszközt](automation-variables.md) null értékkel.
* A PowerShell-runbookok nem tudnak lekérni egy változó eszközt a `*~*` névben.
* A [PowerShell-runbook](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) ban egy körmenetben végzett get-process művelet körülbelül 80 ismétlés után összeomlhat.
* A PowerShell-runbook sikertelen lehet, ha egyszerre nagy mennyiségű adatot próbál írni a kimeneti adatfolyamba. Ezt a problémát általában úgy oldhatja meg, hogy a runbook-kimenet csak a nagy objektumokhoz szükséges információkat adja ki. Például ahelyett, `Get-Process` hogy korlátozás nélkül használná, a parancsmag kimenete csak `Get-Process | Select ProcessName, CPU`a szükséges paramétereket használhatja, mint például a .

## <a name="powershell-workflow-runbooks"></a>PowerShell-munkafolyamat-runbookok

A PowerShell-munkafolyamat runbookjai [a Windows PowerShell-munkafolyamaton](automation-powershell-workflow.md)alapuló tankönyvek. Közvetlenül szerkesztheti a runbook kódját az Azure Portalon található szövegszerkesztő használatával. Bármely offline szövegszerkesztőt használhat, és [importálhatja a runbookot az](manage-runbooks.md) Azure Automationbe.

### <a name="advantages"></a>Előnyök

* Valósítsa meg az összes összetett logikát a PowerShell-munkafolyamat-kóddal.
* Hiba esetén [az ellenőrzőpontok](automation-powershell-workflow.md#checkpoints) segítségével folytathatja a műveletet.
* [Párhuzamos feldolgozással](automation-powershell-workflow.md#parallel-processing) több műveletet is végrehajtanak párhuzamosan.
* Tartalmazhat más grafikus runbookokat és a PowerShell-munkafolyamat runbookjait gyermek runbookokként a magas szintű munkafolyamatok létrehozásához.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-munkafolyamat.
* A Runbookoknak foglalkozniuk kell a PowerShell-munkafolyamat további összetettségével, például [a deszerializált objektumokkal.](automation-powershell-workflow.md#code-changes)
* A Runbookok indítása hosszabb időt vesz igénybe, mint a PowerShell runbookok, mivel azokat le kell fordítani a futtatás előtt.
* A PowerShell-runbookok csak a parancsmag `Start-AzAutomationRunbook` használatával lehet felvenni a PowerShell-runbookok gyermek runbookok.
* A Runbookok nem futtathatók linuxos hibrid runbook-feldolgozón.

## <a name="python-runbooks"></a>Python runbookok

Python runbookok fordítása python 2 alatt. Közvetlenül szerkesztheti a runbook kódját az Azure Portalon található szövegszerkesztő használatával. Offline szövegszerkesztőt is használhat, és [importálhatja a runbookot az](manage-runbooks.md) Azure Automationbe.

### <a name="advantages"></a>Előnyök

* Használja a robusztus Python-kódtárakat.
* Futtatható az Azure-ban vagy a Linux hibrid Runbook-feldolgozók. A Windows hibrid Runbook-feldolgozók a [python2.7-tel](https://www.python.org/downloads/release/latest/python2) vannak telepítve.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a Python-parancsfájlok futtatását.
* Jelenleg csak a Python 2 támogatott. A Python 3-specifikus függvények sikertelenek.
* A külső könyvtárak használatához [importálnia](python-packages.md) kell a csomagokat az Automation-fiókba.

## <a name="next-steps"></a>További lépések

* A grafikus runbookok szerkesztéséről a [Grafikus szerzői jog az Azure Automationben](automation-graphical-authoring-intro.md)című témakörben olvashat bővebben.
* A Runbookok PowerShell- és PowerShell-munkafolyamatai közötti különbségeket a [Windows PowerShell-munkafolyamat tanulása című témakörben ismeri el.](automation-powershell-workflow.md)
* A runbookok létrehozásáról és importálásáról a [Runbookok kezelése az Azure Automationben című témakörben](manage-runbooks.md)talál további információt.
* Ha többet szeretne megtudni a PowerShellről, beleértve a nyelvi referencia- és tanulási modulokat, olvassa el a [PowerShell-dokumentumok című dokumentumait.](https://docs.microsoft.com/powershell/scripting/overview)
