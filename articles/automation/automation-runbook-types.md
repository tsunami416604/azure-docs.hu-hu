---
title: Azure Automation runbook-típusok
description: Ez a cikk ismerteti azokat a runbookok, amelyeket a Azure Automation használhat, és figyelembe kell venni a használandó típus meghatározásának szempontjait.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: c7b036e7adca1fe92425659ebd099feaa3891556
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743828"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation runbook-típusok

A Azure Automation Process Automation szolgáltatás számos runbookok támogat, az alábbi táblázatban meghatározottak szerint. A folyamat-automatizálási környezettel kapcsolatos további tudnivalókért lásd: [Runbook végrehajtása Azure Automationban](automation-runbook-execution.md).

| Típus | Leírás |
|:--- |:--- |
| [Grafikus](#graphical-runbooks)|A Windows PowerShellen alapuló grafikus runbook, és a Azure Portal grafikus szerkesztőjében teljesen elkészült és szerkeszthető. |
| [Grafikus PowerShell-munkafolyamat](#graphical-runbooks)|A grafikus runbook a Windows PowerShell-munkafolyamaton alapul, és a Azure Portal grafikus szerkesztőjében teljesen elkészült és szerkeszthető. |
| [PowerShell](#powershell-runbooks) |A szöveges runbook a Windows PowerShell-parancsfájlok alapján. |
| [PowerShell-munkafolyamat](#powershell-workflow-runbooks)|A szöveges runbook a Windows PowerShell munkafolyamat-parancsfájlok alapján. |
| [Python](#python-runbooks) |A szöveg runbook Python-parancsfájlok alapján. |

Vegye figyelembe az alábbi szempontokat, amikor meghatározza, hogy melyik típust kell használni egy adott runbook.

* A runbookok nem konvertálható grafikus formátumból szöveg típusúra, vagy fordítva.
* A különböző típusú runbookok a gyermek runbookok való használata korlátozásokkal jár. További információ: [gyermek runbookok Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafikus runbookok

Grafikus és grafikus PowerShell-munkafolyamati runbookok hozhat létre és szerkeszthet a Azure Portal grafikus szerkesztőjének használatával. Ilyen típusú runbook azonban nem hozhat létre és nem szerkeszthet más eszközzel. A grafikus runbookok főbb jellemzői:

* Az Automation-fiókban lévő fájlokra exportálható, majd importálható egy másik Automation-fiókba. 
* PowerShell-kód előállítása. 
* Az importálás során grafikus PowerShell-munkafolyamati runbookok konvertálható. 

### <a name="advantages"></a>Előnyök

* A Visual INSERT-link-configure authoring Model használata.
* Koncentráljon arra, hogy az adatforgalom Hogyan zajlik a folyamaton.
* A felügyeleti folyamatok vizuális ábrázolása.
* A magas szintű munkafolyamatok létrehozásához gyermek runbookok más runbookok is tartalmazhat.
* A moduláris programozás ösztönzése.

### <a name="limitations"></a>Korlátozások

* A Azure Portalon kívül nem hozható létre és nem szerkeszthető.
* Az összetett logika végrehajtásához a PowerShell-kódot tartalmazó programkód-tevékenységre lehet szükség.
* Nem lehet konvertálni az egyik [szöveges formátumra](automation-runbook-types.md)sem, és nem alakíthat át szöveges runbook grafikus formátumra. 
* Nem lehet megtekinteni vagy közvetlenül szerkeszteni a grafikus munkafolyamat által létrehozott PowerShell-kódot. Megtekintheti a létrehozott kódot bármely kód tevékenységben.
* A runbookok nem futtatható Linux hibrid Runbook-feldolgozón. [A hibrid Runbook-feldolgozók használatával lásd: erőforrások automatizálása az adatközpontban vagy a felhőben](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-runbookok

A PowerShell-runbookok a Windows PowerShellen alapulnak. Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával.  Használhatja az offline szövegszerkesztőt is, és [importálhatja a runbook](manage-runbooks.md) Azure Automationba.

### <a name="advantages"></a>Előnyök

* Az összes összetett logikát PowerShell-kóddal implementálhatja a PowerShell-munkafolyamat további bonyolultsága nélkül.
* Gyorsabb a PowerShell-munkafolyamatok runbookok, mert a futtatása előtt nem kell lefordítani őket.
* Az Azure-ban és a hibrid Runbook-feldolgozókon is futtatható Windows és Linux rendszereken.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-parancsfájlokat.
* A runbookok nem használhat [párhuzamos feldolgozást](automation-powershell-workflow.md#use-parallel-processing) több művelet párhuzamos végrehajtásához.
* A runbookok nem tud [ellenőrzőpontokat](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) használni a runbook folytatásához, ha hiba történt.
* A [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) parancsmaggal csak a PowerShell-munkafolyamatok runbookok és a grafikus runbookok lehet felvenni, amely új feladatot hoz létre.

### <a name="known-issues"></a>Ismert problémák

A PowerShell-runbookok jelenleg ismert problémák a következők:

* A PowerShell-runbookok nem tud beolvasni egy titkosítatlan [változó](automation-variables.md) objektumot NULL értékkel.
* A PowerShell-runbookok nem tud beolvasni egy változót a `*~*` névben.
* A PowerShell-runbook egy hurokban való [lekéréses](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) művelete a 80-es iteráció után összeomlik.
* A PowerShell-runbook sikertelenek lehetnek, ha nagy mennyiségű adatokat próbálnak írni a kimeneti adatfolyamba. Ezt a problémát általában úgy is megkerülheti, ha a runbook kimenete csak a nagyméretű objektumokkal való működéshez szükséges információ. A korlátozás nélküli használata helyett például `Get-Process` a parancsmag kimenete csak a szükséges paramétereket eredményezi `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>PowerShell-munkafolyamat runbookok

A PowerShell-munkafolyamat runbookok a [Windows PowerShell-munkafolyamaton](automation-powershell-workflow.md)alapuló szöveges runbookok. Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával. Használhatja az offline szövegszerkesztőt is, és [importálhatja a runbook](manage-runbooks.md) Azure Automationba.

### <a name="advantages"></a>Előnyök

* Az összes összetett logika implementálása a PowerShell-munkafolyamat kódjával.
* Ha hiba történt, használja az [ellenőrzőpontokat](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) a művelet folytatásához.
* Párhuzamos [feldolgozás](automation-powershell-workflow.md#use-parallel-processing) használata több művelet párhuzamos végrehajtásához.
* Más grafikus runbookok és PowerShell-munkafolyamatok runbookok is tartalmazhat, mint a magas szintű munkafolyamatok létrehozására szolgáló runbookok.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-munkafolyamatot.
* A runbookok-nek a PowerShell-munkafolyamatok további összetettségét kell megfelelnie, például [deszerializált objektumokat](automation-powershell-workflow.md#deserialized-objects).
* A runbookok hosszabb időt vesz igénybe, mint a PowerShell-runbookok, mivel azokat a Futtatás előtt le kell fordítani.
* A parancsmag használatával csak a PowerShell-runbookok lehet alárendelt runbookok `Start-AzAutomationRunbook` .
* A runbookok nem futtatható Linux hibrid Runbook-feldolgozón.

## <a name="python-runbooks"></a>Python-runbookok

Python-runbookok fordítása a Python 2 alatt. Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával. Használhat egy offline szövegszerkesztőt is, és [importálhatja a runbook](manage-runbooks.md) Azure Automationba.

### <a name="advantages"></a>Előnyök

* Használja a robusztus Python-kódtárakat.
* Futtatható az Azure-ban vagy Linux hibrid Runbook-feldolgozók esetében is. A Windows Hybrid Runbook-feldolgozók a [Python 2.7](https://www.python.org/downloads/release/latest/python2) -es verzióval támogatottak.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a Python-parancsfájlokat.
* Jelenleg csak a Python 2 támogatott. Minden Python 3-specifikus függvény meghiúsul.
* A külső gyártótól származó kódtárak használatához [importálnia kell a csomagokat](python-packages.md) az Automation-fiókba.

## <a name="next-steps"></a>További lépések

* [Runbookok kezelése Azure Automation](manage-runbooks.md)
* [A Azure Automation PowerShell-munkafolyamatának megismerése](automation-powershell-workflow.md)
* [Grafikus runbookok készítése Azure Automation](automation-graphical-authoring-intro.md)
* [PowerShell-dokumentumok](https://docs.microsoft.com/powershell/scripting/overview)
