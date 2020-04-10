---
title: Azure Automation-runbook-típusok
description: Ismerteti a különböző típusú runbookok, amelyek az Azure Automation-ben használható, és szempontokat, amelyeket figyelembe kell venni annak meghatározásakor, hogy milyen típusú használni.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010171"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-runbook-típusok

Az Azure Automation folyamatautomatizálási szolgáltatás a runbookok többtípusát támogatja. A típusokat az alábbi táblázat röviden meghatározza, és az alábbi szakaszokban részletesebben ismerteti. A folyamatautomatizálási környezetről a [Runbook-végrehajtás az Azure Automationben](automation-runbook-execution.md)című témakörben olvashat.

| Típus | Leírás |
|:--- |:--- |
| [Grafikus](#graphical-runbooks)|A Windows PowerShell en alapuló grafikus runbook, amelyet az Azure Portal grafikus szerkesztőjében hozott létre és szerkesztett. |
| [Grafikus PowerShell-munkafolyamat](#graphical-runbooks)|A Windows PowerShell-munkafolyamaton alapuló grafikus runbook, amelyet az Azure Portal grafikus szerkesztőjében hozott létre és szerkesztett. |
| [PowerShell](#powershell-runbooks) |A Windows PowerShell-parancsfájlok on alapuló szöveges runbook. |
| [PowerShell-munkafolyamat](#powershell-workflow-runbooks)|A Windows PowerShell-munkafolyamat parancsfájljain alapuló szöveges runbook. |
| [Python](#python-runbooks) |Python-parancsfájlok alapján futókönyv. |

## <a name="graphical-runbooks"></a>Grafikus runbookok

Grafikus és grafikus PowerShell-munkafolyamat-runbookokat hozhat létre és szerkeszthet az Azure Portalon a grafikus szerkesztővel. Az ilyen típusú runbookok azonban nem hozhathatók létre és nem szerkeszthetők egy másik eszközzel.

A grafikus runbook a következő főbb jellemzőkkel rendelkezik:

* Exportálható egy fájlba az Automation-fiókban, majd importálható egy másik Automation-fiókba. 
* PowerShell-kódot hoz létre. 
* Az importálás során grafikus PowerShell-munkafolyamat-runbookra konvertálható. 

### <a name="advantages"></a>Előnyök

* Vizuális beszúrási-kapcsolat-konfigurálású szerzői modellt használ.
* Arra összpontosít, hogy az adatok hogyan haladnak át a folyamaton.
* Vizuálisan a felügyeleti folyamatokat jelöli.
* Más runbookokat tartalmaz gyermek runbookként a magas szintű munkafolyamatok létrehozásához.
* Ösztönzi a moduláris programozást.

### <a name="limitations"></a>Korlátozások

* Nem hozható létre és nem szerkeszthető az Azure Portalon kívül.
* Előfordulhat, hogy egy PowerShell-kódot tartalmazó kódtevékenységet igényel az összetett logika végrehajtásához.
* A [szöveges formátumok](automation-runbook-types.md)egyike nem konvertálható, és a szöveges runbook sem konvertálható grafikus formátumra. 
* Nem teszi lehetővé a grafikus munkafolyamat által létrehozott PowerShell-kód megtekintését vagy közvetlen szerkesztését. A kódtevékenységekben létrehozott kódot megtekintheti.
* Nem fut a Linux hibrid Runbook feldolgozó. Lásd: [Erőforrások automatizálása az adatközpontban vagy a felhőben a hibrid Runbook-feldolgozó használatával.](automation-hybrid-runbook-worker.md)

## <a name="powershell-runbooks"></a>PowerShell-runbookok

A PowerShell-runbookok a Windows PowerShell-alapúak. Közvetlenül szerkesztheti a runbook kódját az Azure Portalon található szövegszerkesztő használatával.  Bármely offline szövegszerkesztőt használhat, és [importálhatja a runbookot az](manage-runbooks.md) Azure Automationbe.

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
* Nem futtatható a Linux hibrid Runbook-feldolgozó.

## <a name="python-runbooks"></a>Python runbookok

Python runbookok fordítása python 2 alatt. Közvetlenül szerkesztheti a runbook kódját az Azure Portalon található szövegszerkesztővel vagy egy offline szövegszerkesztővel, és [importálhatja a runbookot az](manage-runbooks.md) Azure Automationbe.

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
