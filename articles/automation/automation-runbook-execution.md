---
title: "A Runbook végrehajtása az Azure Automationben |} Microsoft Docs"
description: "Azure Automation forgatókönyv feldolgozásának módja részleteit ismerteti."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: d10c8ce2-2c0b-4ea7-ba3c-d20e09b2c9ca
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: magoedte;bwren
ms.openlocfilehash: a443071aee3e0f845de4387322d2866157a9fe87
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="runbook-execution-in-azure-automation"></a>A Runbook végrehajtása az Azure Automationben
Az Azure Automationben elindít egy runbookot, ha egy feladat jön létre. Egy feladat a runbook egyszeri futtatási példánya. Egy Azure Automation munkavégző rendelt minden feladat futtatása. Munkavállalók több Azure-fiókra által megosztott, amíg feladatokat azok másik Automation-fiók el különítve egymástól. Nem irányítás melyik Worker-szolgáltatások a feladat a kérelmet. Egyetlen runbook fut egyszerre több feladattal rendelkezhet.  Az Automation-fiók a feladatok végrehajtási környezetnek felhasználhatók. Az Azure portálon megtekintett forgatókönyvek listája, amely minden runbook indított összes feladatok állapotának sorolja fel. Megtekintheti a feladatok minden runbook egyes állapotának nyomon követése érdekében. A különböző feladatállapotok leírását [feladatállapotok](#job-statuses).

Az alábbi ábrán látható, a runbook-feladatok életciklusát [grafikus forgatókönyvek](automation-runbook-types.md#graphical-runbooks) és [PowerShell munkafolyamat-forgatókönyvekről](automation-runbook-types.md#powershell-workflow-runbooks).

![Feladatállapotok - PowerShell munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

Az alábbi ábrán látható, a runbook-feladatok életciklusát [PowerShell-forgatókönyvek](automation-runbook-types.md#powershell-runbooks).

![Feladatállapotok - PowerShell-parancsfájl](./media/automation-runbook-execution/job-statuses-script.png)

A feladatok fér hozzá az Azure-erőforrások az Azure-előfizetéssel való kapcsolódás. Csak rendelkeznek erőforrásokhoz való hozzáférés az Adatközpont Ha ezeket az erőforrásokat a nyilvános felhőből érhetők el.

## <a name="job-statuses"></a>Feladatállapotok
A következő táblázat ismerteti a különböző állapotok feladat lehetséges.

| status | Leírás |
|:--- |:--- |
| Befejezve |A feladat sikeresen befejeződött. |
| Sikertelen |A [grafikus és a PowerShell-munkafolyamati forgatókönyvek](automation-runbook-types.md), a runbookot nem sikerült lefordítani.  A [PowerShell-parancsfájl runbookok](automation-runbook-types.md), a runbookot nem sikerült elindítani, vagy a feladat kivételbe ütközött. |
| Nem sikerült, erőforrás Várakozás |A feladat sikertelen volt, mert elérte a [igazságos elosztása révén](#fair-share) háromszor korlátjának növelését, és az azonos ellenőrzőpont vagy a runbook elindítása indított minden alkalommal. |
| Várakozási sorba helyezve |A feladat arra vár erőforrások egy automatizálási feldolgozó elérhető lesz, így indíthatók el. |
| Indulás alatt |A feladat egy feldolgozónak van kiosztva, és az elindítás folyamatban van. |
| Folytatás |A folytatása a feladat felfüggesztését követően annak folyamatban van. |
| Fut |A feladat fut. |
| Rendszert futtató erőforrások vár |A feladat le lett távolítva a memóriából, mert elérte a [igazságos elosztása révén](#fair-share) korlátot. Röviddel a legutóbbi ellenőrzőponttól folytatja. |
| Leállítva |A feladatot a felhasználó leállította, mielőtt befejeződhetett volna. |
| Leállítás |A rendszer a feladat leállítása folyamatban van. |
| Felfüggesztve |A felhasználó által, a rendszer, vagy a runbook egy parancsa felfüggesztette a feladatot. A felfüggesztett feladatokat újra lehet indítani, és azok az utolsó ellenőrzőponttól vagy a runbook elejétől folytatása, ha nincs beállítva a ellenőrzőpontokat. A runbook csak felfüggeszti a rendszer egy kivétel. Alapértelmezés szerint ErrorActionPreference értéke **Folytatás**, tehát a feladat a hiba folyamatosan működik. Ha ezt a preferenciaváltozót **leállítása**, majd a feladat felfüggeszti az hiba.  Érvényes [grafikus és a PowerShell-munkafolyamati forgatókönyvek](automation-runbook-types.md) csak. |
| Felfüggesztés |A rendszer megkísérli a feladat felfüggesztését a felhasználó kérésére. A runbooknak el kell érnie a következő ellenőrzőpontot felfüggesztés előtt. Ha már elhagyta az utolsó ellenőrzőpontot, majd befejezné a felfüggesztés előtt.  Érvényes [grafikus és a PowerShell-munkafolyamati forgatókönyvek](automation-runbook-types.md) csak. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Az Azure-portál a feladat állapotának megtekintése
Megtekintheti az összes runbook-feladatok összesített állapotának vagy elemezze az Azure-portálon vagy az integráció konfigurálása a Microsoft Operations Management Suite (OMS) Naplóelemzési munkaterület továbbítják a runbook-feladat állapotát és a feladat az adott runbook-feladatok részleteit adatfolyamokat.  OMS Naplóelemzési integrálása kapcsolatos további információkért lásd: [továbbítása feladat állapotát és a feladat adatfolyamok Automation való Naplóelemzés (OMS)](automation-manage-send-joblogs-log-analytics.md).  

### <a name="automation-runbook-jobs-summary"></a>Automatizálási runbook-feladatok összegzése
Jobb oldalán a kijelölt Automation-fiók, megtekintheti az összes kijelölt Automation-fiókhoz a runbook-feladatok **Projekt statisztika** csempére.<br><br> ![Projekt statisztika csempe](./media/automation-runbook-execution/automation-account-job-status-summary.png).<br> Ez a csempe egy száma és a grafikus ábrázolása az összes feladat végrehajtása a feladat állapotát jeleníti meg.  

Kattintson a csempére megadja a **feladatok** panel, amelyen az összes feladat végrehajtása, állapot, a feladat végrehajtása és a kezdési és befejezési időpontjai összesített listáját tartalmazza.<br><br> ![Automation-fiók feladatok panelen](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)<br><br>  Szűrheti a feladatok kiválasztásával **szűrve** és szűrő egy adott runbook, a feladat állapotát, vagy a legördülő listából, a dátum/idő tartományon belüli kereséséhez.<br><br> ![Szűrő feladat állapota](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Azt is megteheti, a runbook kiválasztásával megtekintheti feladat összefoglaló információk egy adott runbook a **Runbookok** paneljén az Automation-fiók, és válassza a **feladatok** csempére.  Ez megadja a **feladatok** panelt, és ott rákattinthat a feladat rekord megtekintheti azok részleteit és kimenetét a.<br><br> ![Automation-fiók feladatok panelen](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)<br> 

### <a name="job-summary"></a>Feladat összegzése
Megtekintheti az összes, egy adott runbookhoz és azok állapotával kapcsolatos feladatok listáját. Szűrheti a feladatok állapota a lista és a feladat utolsó módosításának dátumát tartományán. A részletes információkat és a kimeneti megtekintéséhez kattintson a feladat nevét. A feladat részletes nézetében a runbook-paraméterek, amelyeket a feladathoz a értékeket tartalmaz.

Az alábbi lépések segítségével egy runbookhoz tartozó feladatok megtekintéséhez.

1. Válassza ki az Azure-portálon **Automation** , és válassza az Automation-fiók nevét.
2. Válassza ki a központ **Runbookok** majd a a **Runbookok** panelen válassza ki a runbook a listából.
3. A kiválasztott runbookhoz paneljén kattintson a **feladatok** csempére.
4. Kattintson a feladatok listájában, és a runbook feladat részletei panelen megtekintheti azok részleteit és kimenetét.

## <a name="retrieving-job-status-using-windows-powershell"></a>A Windows PowerShell feladatállapot lekérése
Használhatja a [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) egy runbook és egy adott feladat részletes adatait létrehozott feladatok beolvasása. Ha egy runbook indítása a Windows PowerShell használatával [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), akkor adja vissza, az eredményezett feladatot. Használjon [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)kimeneti beolvasni a feladat kimenetét.

Az alábbi Példaparancsok kérje le a minta-runbookhoz tartozó utolsó feladatot, majd megjelenítik annak állapotát, a runbook-paraméterek és a feladat kimenetét megadott értékek.

    $job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
    –RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
    $job.Status
    $job.JobParameters
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output

## <a name="fair-share"></a>Igazságos elosztása révén
Ahhoz, hogy megoszthatják az erőforrásokat a felhőben található összes runbookhoz, Azure Automation ideiglenesen el lesz bármely követően három óráig futott.  Ebben az időszakban, a feladatok [PowerShell-alapú runbookok](automation-runbook-types.md#powershell-runbooks) le van állítva, és nem fog újraindulni.  A feladat állapotának megjelenítése **leállítva**.  Az ilyen típusú runbook mindig újraindítják az elejéről, mivel azok nem támogatják az ellenőrzőpontok.  

[PowerShell Munkafolyamatain alapuló runbookok](automation-runbook-types.md#powershell-workflow-runbooks) folytatódik a legutóbbi [ellenőrzőpont](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints).  Miután három óra, a runbook-feladat fel lesz függesztve a szolgáltatást, és annak állapotát jeleníti meg **fut, az erőforrások Várakozás**.  Amikor egy védőfal elérhetővé válik, a runbook automatikusan újraindul az Automation szolgáltatás és az utolsó ellenőrzőponttól folytatása.  Ez történik akkor normál PowerShell-munkafolyamat felfüggesztése/újraindításhoz.  Ha a runbook újra meghaladja a három óra futtatókörnyezet, a folyamat ismétlődik, legfeljebb három alkalommal.  A harmadik újraindításkor, ha a runbook még nem fejeződött be három óra, akkor a runbook-feladat sikertelen volt, és a feladat állapota: **sikertelen Várakozás erőforrásokra**.  Ebben az esetben a következő kivétel miatt a hibához kap.

*A feladat fut, mert azt a ismételten fürtből a azonos ellenőrzőpont nem folytatható. Győződjön meg arról, hogy a Runbook nem hajt végre műveletek végzésekor állapotában megőrzése nélkül.*

Ez az, hogy megvédje a szolgáltatás határozatlan ideig, befejeződés nélkül futó runbookok még nincsenek újra learningmodule nélkül végezhetnek a következő ellenőrzőpontot.

Ha a runbook nem ellenőrzőpontokkal rendelkezik, vagy a feladat nem érte el a az első ellenőrzőpont learningmodule előtt, majd újraindul az elejétől.  

Ha létrehozta a forgatókönyvet, győződjön meg róla, hogy a tevékenységek között két ellenőrzőpontokat futási időnek nem haladja meg a három óra. Ellenőrzőpontok hozzáadása a runbookhoz, annak érdekében, hogy nem érte el a három óra határértékét vagy feloszthatja a hosszú szeretne műveletek futtatása. Például a runbook egy ismételt indexelése előfordulhat, hogy végre nagy SQL-adatbázis. Ha egyetlen művelet igazságos elosztása révén belül nem fejeződik be, majd a feladat a memóriából és a legelejétől újraindul. Ebben az esetben kell szakítsa meg a több lépést, például egy olyan táblát újraindexelés egyszerre, az ismételt indexelése művelet, és helyezze egy ellenőrzőpontot egyes műveletek után, úgy, hogy a feladat az utolsó művelet befejezését követően sikerült folytatni.

## <a name="next-steps"></a>Következő lépések
* A különböző módszereket, amelyek segítségével elindít egy forgatókönyvet az Azure Automation kapcsolatos további információkért lásd: [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)

