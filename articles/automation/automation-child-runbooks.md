---
title: Azure Automation runbookjai gyermek |} Microsoft Docs
description: "Az Azure Automationben runbook indítása másik runbookból és a közöttük információk megosztása a különböző módszereket írja le."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 919887b9-43e2-4c16-883c-f81807fe37db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 617e18f5435c7eacb7751ccca6ac2f3814745f04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="child-runbooks-in-azure-automation"></a>Azure Automation runbookjai gyermek
Az Azure Automationben egy más runbookok által használható önálló funkcióval újrafelhasználható, moduláris runbookok írása a legjobb. Gyakran a szülő runbook fel fogja hívni egy vagy több gyermek runbook végrehajtani a kért funkciót. A gyermek runbook hívásához két módja van, és mindegyik rendelkezik-e különböző módszer közötti különbségeket tisztában kell lennie, hogy megállapíthassa, amely a különböző forgatókönyvek esetén ajánlott lesz.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Beágyazott Futtatás használatával gyermek runbook meghívása
A beágyazottan indított runbook másik runbookból meghívni, a runbook nevét használja, és adjon meg értékeket a paraméterek ugyanúgy egy tevékenységet vagy egy parancsmagot használhatja.  Automation-fiók minden forgatókönyve érhetők el minden más ily módon használható. A szülőrunbook megvárja a gyermekrunbook befejeződjön, mielőtt továbblép a következő sorra, és minden kimenetet közvetlenül a szülő visszaadja.

A beágyazottan indított runbook meghívásakor ugyanazt a feladatot, mint a szülőrunbook futtatja. Nincs a gyermekrunbook annak feladatelőzményeiben a feladatelőzmények feltüntetése lesz. Kivételeket és adatfolyam-a gyermekrunbook kimenetét a szülő társítva lesz. Ez kevesebb feladatot eredményez, és megkönnyíti a nyomon követését és elhárításához, mivel a szülő feladatukhoz társított és az adatfolyam-kimenetét bármelyikét a gyermekrunbook összes kivételét.

Egy runbook közzétételekor runbookok gyermekrunbookoknak már közzé kell tenni. Ennek oka az Azure Automation runbookok társítást hoz létre, amikor gyermekrunbookkal. Ellenkező esetben a szülőrunbook közzététele sikeres megjelenik, de kivételt hoz létre, amikor elindul. Ez akkor fordul elő, ha a szülő runbook újbóli ahhoz, hogy a megfelelő hivatkozást a gyermekrunbookokra. Nem kell a szülő runbook ismételt közzététele, ha a gyermekrunbookokra bármelyikét van módosítani, mert a társítás fog létre lett hozva.

A gyermekrunbook beágyazottan meghívott paraméterei bármilyen adattípusúak, többek között összetett objektumok lehet, és nincs nincs [JSON-szerializálás](automation-starting-a-runbook.md#runbook-parameters) , ha a runbook az Azure felügyeleti portál használatával, vagy a a Start-AzureRmAutomationRunbook parancsmag.

### <a name="runbook-types"></a>Runbook-típusok
Milyen típusú hívhatják meg egymással:

* A [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) és [grafikus forgatókönyvek](automation-runbook-types.md#graphical-runbooks) hívhatják meg minden egyéb beágyazott (mindkettő PowerShell-alapú).
* A [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks) és grafikus PowerShell-munkafolyamati forgatókönyvek hívhatják meg minden egyéb beágyazott (mindkettő PowerShell munkafolyamat alapú)
* A PowerShell és a PowerShell-munkafolyamati típusainak nem hívható meg egymással beágyazott, és a Start-AzureRmAutomationRunbook.

Ha sorrendben függetlenül attól, hogy közzététele:

* Csak runbookok Közzététel sorrendje számít, a PowerShell munkafolyamatok és a grafikus PowerShell-munkafolyamati forgatókönyvek.

Meghívja a beágyazott Futtatás használatával grafikus vagy PowerShell-munkafolyamati gyermekrunbook, csak a runbook nevét használja.  A PowerShell gyermek runbook hívásakor a neve előtt kell *.\\*  adhatja meg, hogy a parancsfájl a helyi könyvtárban található. 

### <a name="example"></a>Példa
A következő példa egy teszt gyermekrunbook, amely három paramétert, egy összetett objektum, egy egész számot és egy logikai érték fogad hív meg. A gyermek runbook kimeneti hozzá van rendelve egy változót.  Ebben az esetben a gyermekrunbook egy PowerShell-munkafolyamati forgatókönyv

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Az alábbiakban az ugyanebben a példában a gyermek egy PowerShell-forgatókönyv használatával.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true


## <a name="starting-a-child-runbook-using-cmdlet"></a>Parancsmag használatával gyermekrunbook indítása
Használhatja a [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) runbook indítása, a parancsmag [runbook indítása a Windows PowerShell-lel](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Ez a parancsmag használati két módja van.  Az egyik mód a parancsmag adja vissza a feladatazonosítót, amint a gyermekrunbook a gyermek feladat jön létre.  A más módban, amely megadásával engedélyezi a **-Várjon, amíg** paraméter, a parancsmag megvárja, amíg a gyermek feladat befejeződik, és a kimeneti visszatér a gyermek runbook.

A parancsmaggal indított gyermekrunbook feladatot a szülő runbook tart egy külön feladat. Ez több feladatot eredményez, mint a runbook soron belüli meghívása, és így nehezebb. A szülő több gyermekrunbookot aszinkron módon nélkül indíthatják befejezéséhez mindegyik esetében megvárná a. Az, hogy azonos típusú párhuzamos futtatáshoz, hívja meg a gyermekrunbookokat, a szülő runbook kell alkalmaznia a [parallel kulcsszót](automation-powershell-workflow.md#parallel-processing).

A parancsmaggal indított gyermekrunbook paramétereinek megadott egy kivonattáblát a [Runbook-paraméterek](automation-starting-a-runbook.md#runbook-parameters). Csak egyszerű adattípusok használhatók. Ha a runbook rendelkezik összetett adattípusú paraméterrel, majd, beágyazottan kell meghívni.

### <a name="example"></a>Példa
Az alábbi példa paraméterekkel rendelkező gyermekrunbook elindul, és majd megvárja, amíg befejeződik a Start-AzureRmAutomationRunbook használatával – várakozási paraméter. Ezt követően a gyermek runbook begyűjti a kimenetet.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResourceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>A gyermekrunbookok meghívására szolgáló módszerek összehasonlítása
A következő táblázat összefoglalja, a két módszer egy runbook másik runbookból hívja.

|  | Beágyazott | Parancsmag |
|:--- |:--- |:--- |
| Feladat |Gyermekrunbookok a szülővel azonos feladatban fut. |A gyermekrunbook egy külön feladat jön létre. |
| Végrehajtás |Szülőrunbook megvárja a gyermekrunbook befejeződjön, mielőtt továbblépne. |Szülő runbook továbbra is fennáll, a gyermekrunbook indítását követően azonnal *vagy* szülőrunbook megvárja, a gyermek feladat befejeződésére. |
| Kimenet |Szülőrunbook közvetlenül lekérheti kimeneti gyermekrunbook. |Szülő runbook kell lekérnie a kimenetet gyermek runbook-feladat *vagy* szülőrunbook közvetlenül lekérheti kimeneti gyermekrunbook. |
| Paraméterek |A gyermekrunbook paramétereinek értékeit külön kell meghatározni, és bármilyen adattípus használható. |Értékek a gyermekrunbook paramétereinek egyesíthetők kell egyetlen kivonattáblába, és csak egyszerű, a tömb és az objektum adattípusok, hogy használja ki a JSON-szerializálást. |
| Automation-fiók |Szülő runbook használja a gyermek runbook ugyanazt az automation-fiókban. |Szülő runbook bármely azonos Azure-előfizetést, és még egy másik előfizetésben található automation-fiók a gyermekrunbook is használhat, ha a hozzá való csatlakozást. |
| Közzététel |Gyermekrunbook közzé kell tenni, mielőtt a szülőrunbook közzététele. |Gyermekrunbook közzé kell tenni a szülőrunbook indítása előtt. |

## <a name="next-steps"></a>Következő lépések
* [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)
* [Runbook-kimenet és üzenetek az Azure Automationben](automation-runbook-output-and-messages.md)

