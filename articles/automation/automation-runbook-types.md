---
title: Azure Automation Runbook-típusok
description: 'Ismerteti a különböző típusú runbookok, amelyek az Azure Automation-ben használható, és szempontokat, amelyeket figyelembe kell venni annak meghatározásakor, hogy milyen típusú használni. '
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 6346c29210b6390f11c884ff51e0b60af89bbbb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278609"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-runbook-típusok

Az Azure Automation számos típusú runbookot támogat, amelyeket röviden ismertet az alábbi táblázat.  Az alábbi szakaszok további információkat nyújtanak az egyes típusokról, beleértve az egyes típusok használatára vonatkozó szempontokat is.

| Típus | Leírás |
|:--- |:--- |
| [Grafikus](#graphical-runbooks)|A Windows PowerShell alapján, és az Azure Portalon a grafikus szerkesztőben létrehozott és szerkesztett. |
| [Grafikus PowerShell-munkafolyamat](#graphical-runbooks)|A Windows PowerShell-munkafolyamat alapján, és az Azure Portal grafikus szerkesztőjében létrehozott és szerkesztett. |
| [Powershell](#powershell-runbooks) |A Windows PowerShell-parancsfájlon alapuló szöveges runbook. |
| [PowerShell-munkafolyamat](#powershell-workflow-runbooks)|A Windows PowerShell-munkafolyamaton alapuló szöveges runbook. |
| [Python](#python-runbooks) |A Python alapú szöveges runbook. |

## <a name="graphical-runbooks"></a>Grafikus runbookok

[Grafikus](automation-runbook-types.md#graphical-runbooks) és grafikus PowerShell-munkafolyamat runbookok jönnek létre, és az Azure Portalon a grafikus szerkesztővel.  Exportálhatja őket egy fájlba, majd importálhatja őket egy másik automatizálási fiókba. De nem hozhat létre és nem szerkesztheti őket egy másik eszközzel. Grafikus runbookok powershell-kódot generálnak, de közvetlenül nem tekintheti meg vagy módosíthatja a kódot. A grafikus runbookok nem konvertálhatók [szövegformátumokra,](automation-runbook-types.md)és a szöveges runbookok sem konvertálhatók grafikus formátumra. Grafikus runbookok lehet konvertálni grafikus PowerShell-munkafolyamat runbookok importálás közben, és fordítva.

### <a name="advantages"></a>Előnyök

* Visual insert-link-configure szerzői modell
* Fókuszban az adatok folyamatának áramlása
* A felügyeleti folyamatok vizuális megjelenítése
* Más runbookok felvétele gyermekrunbookokközé magas szintű munkafolyamatok létrehozásához
* Ösztönzi a moduláris programozást

### <a name="limitations"></a>Korlátozások

* A runbook nem szerkeszthető az Azure Portalon kívül.
* Összetett logika végrehajtásához szükség lehet egy PowerShell-kódot tartalmazó kódtevékenységre.
* Nem lehet megtekinteni vagy közvetlenül szerkeszteni a grafikus munkafolyamat által létrehozott PowerShell-kódot. A kód bármely tevékenységben megjelenik.
* Nem futtatható linuxos hibrid runbook-feldolgozón

## <a name="powershell-runbooks"></a>PowerShell-runbookok

A PowerShell-runbookok a Windows PowerShell-alapúak.  Közvetlenül szerkesztheti a runbook kódját az Azure Portalon található szövegszerkesztő használatával.  Bármely offline szövegszerkesztőt használhat, és [importálhatja a runbookot az](manage-runbooks.md) Azure Automationbe.

### <a name="advantages"></a>Előnyök

* Valósítsa meg az összes összetett logikát a PowerShell-kóddal a PowerShell-munkafolyamat további összetettsége nélkül.
* Runbook gyorsabban indul el, mint a PowerShell-munkafolyamat runbookok, mivel nem kell lefordítani a futás előtt.
* Futtatható az Azure-ban vagy Linux és Windows hibrid runbook-feldolgozókon is

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-parancsfájlok.
* Nem használható [párhuzamos feldolgozás](automation-powershell-workflow.md#parallel-processing) több művelet párhuzamos végrehajtásához.
* Hiba esetén nem használható [ellenőrzőpontok](automation-powershell-workflow.md#checkpoints) a runbook folytatásához.
* A PowerShell-munkafolyamat runbookok és a grafikus runbookok csak a Start-AzureAutomationRunbook parancsmag használatával, amely létrehoz egy új feladatot gyermek runbookok.

### <a name="known-issues"></a>Ismert problémák

Az alábbiakban a PowerShell runbookokkal kapcsolatos aktuális ismert problémákat.

* A PowerShell runbookjai nem tudják beolvasni a null értékű titkosítatlan [változóeszközt.](automation-variables.md)
* A PowerShell-runbookok nem tudnak lekérni egy [változó eszközt](automation-variables.md) a *~* névben.
* Get-Process egy ciklusban a PowerShell-runbook összeomlás után körülbelül 80 iterációk.
* A PowerShell-runbook sikertelen lehet, ha megkísérli nagy mennyiségű adatot írni a kimeneti adatfolyam egyszerre.   Ezt a problémát általában úgy oldhatja meg, hogy csak azokat az információkat adja ki, amelyeknagy méretű objektumokkal végzett munka során szükséges.  Például, ahelyett, hogy a kimenő valami, mint *a Get-Process*, akkor a kimenet csak a szükséges mezőket *Get-Process | Válassza a Folyamatnév, PROCESSZOR*lehetőséget.

## <a name="powershell-workflow-runbooks"></a>PowerShell-munkafolyamat-runbookok

A PowerShell-munkafolyamat runbookjai [a Windows PowerShell-munkafolyamaton](automation-powershell-workflow.md)alapuló tankönyvek.  Közvetlenül szerkesztheti a runbook kódját az Azure Portalon található szövegszerkesztő használatával.  Bármely offline szövegszerkesztőt használhat, és [importálhatja a runbookot az](manage-runbooks.md) Azure Automationbe.

### <a name="advantages"></a>Előnyök

* Valósítsa meg az összes összetett logikát a PowerShell-munkafolyamat-kóddal.
* Hiba esetén [az ellenőrzőpontok](automation-powershell-workflow.md#checkpoints) segítségével folytathatja a runbookot.
* [Párhuzamos feldolgozással](automation-powershell-workflow.md#parallel-processing) több műveletet hajthat végre párhuzamosan.
* Tartalmazhat más grafikus runbookokat és a PowerShell-munkafolyamat runbookjait gyermek runbookokként a magas szintű munkafolyamatok létrehozásához.

### <a name="limitations"></a>Korlátozások

* A szerzőnek ismernie kell a PowerShell-munkafolyamatot.
* A Runbooknak foglalkoznia kell a PowerShell-munkafolyamat további összetettségével, például [a deszerializált objektumokkal.](automation-powershell-workflow.md#code-changes)
* A Runbook indítása hosszabb időt vesz igénybe, mint a PowerShell runbookok, mivel a futtatás előtt le kell fordítani.
* PowerShell runbookok csak gyermek runbookok a Start-AzureAutomationRunbook parancsmag használatával, amely létrehoz egy új feladatot.
* Nem futtatható linuxos hibrid runbook-feldolgozón

## <a name="python-runbooks"></a>Python runbookok

Python runbookok fordítása python 2 alatt.  Közvetlenül szerkesztheti a runbook kódját az Azure Portalon található szövegszerkesztővel vagy egy offline szövegszerkesztővel, és [importálhatja a runbookot az](manage-runbooks.md) Azure Automationbe.

### <a name="advantages"></a>Előnyök

* Használja a robusztus Python-kódtárakat.
* Futtatható az Azure-ban vagy mindkét Linux hibrid Runbook-feldolgozók. A Windows hibrid Runbook-feldolgozók a [python2.7-tel](https://www.python.org/downloads/release/latest/python2) vannak telepítve.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a Python-parancsfájlok futtatását.
* Jelenleg csak a Python 2 támogatott, ami azt jelenti, hogy a Python 3-specifikus függvények sikertelenek lesznek.
* Harmadik féltől származó tárak használatához [importálnia](python-packages.md) kell a csomagot az Automation-fiókba, hogy használni lehessen.

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következő további szempontokat annak meghatározásakor, hogy melyik típust használja egy adott runbookhoz.

* A runbookok nem konvertálhatók grafikusról szöveges szövegre vagy fordítva.
* Vannak korlátozások használata runbookok különböző típusú, mint egy gyermek runbook. További információ: [Child runbooks in Azure Automation.](automation-child-runbooks.md)

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a grafikus runbook-készítésről, olvassa el [a Grafikus szerzői jog az Azure Automationben című témakört.](automation-graphical-authoring-intro.md)
* A Runbookok PowerShell- és PowerShell-munkafolyamatai közötti különbségeket a [Windows PowerShell-munkafolyamat tanulása című témakörben](automation-powershell-workflow.md) ismeri el.
* A Runbookok létrehozásáról és importálásáról a [Runbook létrehozása és importálása](manage-runbooks.md) című témakörben talál további információt.
* A PowerShellről további információt, beleértve a nyelvi referencia- és tanulási modulokat, olvassa el a [PowerShell-dokumentumok című dokumentumban.](https://docs.microsoft.com/powershell/scripting/overview)
