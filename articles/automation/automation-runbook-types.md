---
title: Azure Automation-Runbook típusok
description: 'Ismerteti a runbookok, amelyek az Azure Automation és kapcsolatos szempontokat, akkor figyelembe kell vennie annak meghatározása, amelyek használatához írja be. '
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 800380c68e1bb1314021ab60f92ccd3b392dbe32
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-runbook típusok
Azure Automation szolgáltatásbeli számos különböző típusú, amely röviden ismerteti a runbookok támogatja a következő táblázatban.  Az alábbi szakaszokban minden típusát, melynek során vegye figyelembe az egyes esetekben a további információt.

| Típus | Leírás |
|:--- |:--- |
| [Grafikus](#graphical-runbooks) |A Windows PowerShell és az Azure-portálon létrehozott és szerkesztett teljesen a grafikus szerkesztő alapján. |
| [Grafikus PowerShell-munkafolyamat](#graphical-runbooks) |A Windows PowerShell munkafolyamat alapú és létrehozott és szerkesztett teljesen a grafikus szerkesztőben Azure-portálon. |
| [PowerShell](#powershell-runbooks) |A Windows PowerShell-parancsfájl alapján szöveg runbook. |
| [PowerShell-munkafolyamat](#powershell-workflow-runbooks) |A Windows PowerShell munkafolyamat alapú szöveg runbook. |
| [Python](#python-runbooks) |Szöveg runbook Python alapján. |

## <a name="graphical-runbooks"></a>Grafikus forgatókönyvek
[Grafikus](automation-runbook-types.md#graphical-runbooks) és grafikus PowerShell-munkafolyamati forgatókönyvek létrehozott és szerkesztett a grafikus szerkesztővel az Azure portálon.  Egy fájlba exportálhatja, és majd importálja azokat egy másik automation-fiók, de nem készít vagy szerkeszt őket egy másik eszközzel.  Grafikus forgatókönyvek létrehozása a PowerShell-kódot, de közvetlenül nem tekinthetők meg és módosíthatja a kódot. Grafikus forgatókönyvek nem alakítható át egyik a [szöveges formátumokból](automation-runbook-types.md), sem szöveg runbook konvertálható grafikus formátumban. Grafikus forgatókönyvek konvertálható grafikus PowerShell-munkafolyamati forgatókönyvek során importálása és fordítva.

### <a name="advantages"></a>Előnyei
* Visual szerzői modell insert-kapcsolat konfigurálása  
* Hogyan adatáramlás a folyamatot, összpontosítani  
* Vizuálisan képviselik a felügyeleti folyamatok  
* Közé tartozik más runbookok gyermek runbookként magas szintű munkafolyamatok létrehozásához  
* Könnyen létrejöhetnek moduláris programozás  


### <a name="limitations"></a>Korlátozások
* Nem lehet szerkeszteni a runbookot Azure-portálon kívül.
* A PowerShell-kódjába komplex logikai végrehajtásához tartalmazó tevékenységeket lehet szükség.
* Nem tekinthetők meg és a PowerShell-kódot, amely a grafikus munkafolyamat által létrehozott közvetlen szerkesztése. Vegye figyelembe, hogy látható kód tevékenységeket hoz létre a kódot.

## <a name="powershell-runbooks"></a>A PowerShell-forgatókönyvek
A PowerShell-forgatókönyvek a Windows PowerShell alapulnak.  Közvetlenül szerkesztheti a kódot a runbook a szövegszerkesztő segítségével az Azure portálon.  Is használhatja a kapcsolat nélküli szövegszerkesztőben és [a forgatókönyv importálása](http://msdn.microsoft.com/library/azure/dn643637.aspx) az Azure Automation.

### <a name="advantages"></a>Előnyei
* PowerShell-kód nélkül a további összetett szolgáltatásokkal, PowerShell-munkafolyamat minden komplex logikai megvalósításához. 
* Runbook gyorsabb, mint a PowerShell-munkafolyamati forgatókönyvek kezdődik, mivel fordítandó futtatása előtt nem kell azt.

### <a name="limitations"></a>Korlátozások
* PowerShell parancsfájl-kezelési ismernie kell.
* Nem használható [párhuzamos feldolgozást végző](automation-powershell-workflow.md#parallel-processing) párhuzamosan több műveletek elvégzéséhez.
* Nem használható [ellenőrzőpontokat](automation-powershell-workflow.md#checkpoints) folytatja a runbook hiba esetén.
* PowerShell-munkafolyamati forgatókönyvek és grafikus forgatókönyvek csak szerepelhetnek gyermek runbookként a Start-AzureAutomationRunbook parancsmaggal létrehoz egy új feladatot.

### <a name="known-issues"></a>Ismert problémák
Az alábbiakban a PowerShell-forgatókönyvek jelenlegi ismert problémái.

* A PowerShell-forgatókönyvek nem tud beolvasni egy titkosítatlan [változóeszköz](automation-variables.md) null értékű.
* A PowerShell-forgatókönyvek nem tud beolvasni egy [változóeszköz](automation-variables.md) a *~* a neve.
* Get-Process egy PowerShell ismétlődő runbook körülbelül 80 közelítő összeomolhat. 
* A PowerShell-forgatókönyv sikertelen lehet, ha nagyon nagy mennyiségű adatot írni a kimeneti adatfolyamba egyszerre megkísérli.   Általában oldható meg a probléma által szerint kiírta volna csak a nagyméretű objektumok az használatakor szükséges információkat.  Például helyett szerint kiírta volna hasonlót *Get-Process*, csak a kötelező mezőket a kimenetre küldheti *Get-Process |} Válassza ki a Folyamatnév, CPU*.

## <a name="powershell-workflow-runbooks"></a>PowerShell-munkafolyamati forgatókönyvek
PowerShell-munkafolyamati forgatókönyvek alapján szöveg runbookok olyan [Windows PowerShell munkafolyamat](automation-powershell-workflow.md).  Közvetlenül szerkesztheti a kódot a runbook a szövegszerkesztő segítségével az Azure portálon.  Is használhatja a kapcsolat nélküli szövegszerkesztőben és [a forgatókönyv importálása](http://msdn.microsoft.com/library/azure/dn643637.aspx) az Azure Automation.

### <a name="advantages"></a>Előnyei
* PowerShell-munkafolyamati kód minden komplex logikai megvalósításához.
* Használjon [ellenőrzőpontokat](automation-powershell-workflow.md#checkpoints) folytatja a runbook hiba esetén.
* Használjon [párhuzamos feldolgozást végző](automation-powershell-workflow.md#parallel-processing) párhuzamosan több műveletek elvégzéséhez.
* Grafikus forgatókönyvek és lehetnek PowerShell munkafolyamat runbookok gyermek runbookként magas szintű munkafolyamatok létrehozásához.

### <a name="limitations"></a>Korlátozások
* PowerShell-munkafolyamati ismernie kell a szerző.
* A Runbook például a PowerShell-munkafolyamati további összetettsége kell foglalkozik [objektumok deszerializálni](automation-powershell-workflow.md#code-changes).
* A Runbook elindításához mint a PowerShell-forgatókönyvek, mivel futtatása előtt kell lefordítani hosszabb időt vesz igénybe.
* A PowerShell-forgatókönyvek csak szerepelhetnek gyermek runbookként a Start-AzureAutomationRunbook parancsmaggal létrehoz egy új feladatot.

## <a name="python-runbooks"></a>Python-forgatókönyvek
Python runbookok összeállításához Python 2.  Közvetlenül szerkesztheti a kódot a runbook a szövegszerkesztő segítségével az Azure portálon, vagy használhatja a kapcsolat nélküli szövegszerkesztőben és [a forgatókönyv importálása](http://msdn.microsoft.com/library/azure/dn643637.aspx) az Azure Automation.

### <a name="advantages"></a>Előnyei
* A Python robusztus szabványos függvénytár használják.

### <a name="limitations"></a>Korlátozások
* Python-parancsfájl ismernie kell.
* Csak Python 2 támogatott időpontjában, ami azt jelenti, Python 3 adott funkciókhoz sikertelen lesz.

### <a name="known-issues"></a>Ismert problémák
Az alábbiakban Python runbookok jelenlegi ismert problémái.

* Ahhoz, hogy a külső könyvtárak használatára, a runbookot futtatni kell egy [Windows hibrid forgatókönyv-feldolgozó](https://docs.microsoft.com/azure/automation/automation-windows-hrw-install) vagy [Linux hibrid forgatókönyv-feldolgozó](https://docs.microsoft.com/azure/automation/automation-linux-hrw-install) előtt a számítógépen már telepítve könyvtárakkal a runbook indítását.

## <a name="considerations"></a>Megfontolandó szempontok
Akkor figyelembe kell vennie a következő további szempontok számának meghatározásakor az egyes runbookok használandó típust.

* A grafikus forgatókönyvek nem konvertálható szöveges típusra, vagy fordítva.
* Különböző típusú runbookok használja, a gyermek runbook korlátozások is.  Lásd: [az Azure Automation runbookjai gyermek](automation-child-runbooks.md) további információt.

## <a name="next-steps"></a>További lépések
* További információk a létrehozásról grafikus forgatókönyvnek, [grafikus készítése az Azure Automationben](automation-graphical-authoring-intro.md)
* PowerShell és a PowerShell közötti különbségek megismeréséhez munkafolyamatok runbookok, lásd: [tanulási Windows PowerShell munkafolyamat](automation-powershell-workflow.md)
* Hozzon létre vagy Runbook importálása további információkért lásd: [létrehozása vagy egy Runbook importálása](automation-creating-importing-runbook.md)

