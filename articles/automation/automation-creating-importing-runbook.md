---
title: "Létrehozása vagy egy Azure Automation forgatókönyv importálása"
description: "Ez a cikk ismerteti, hogyan hozzon létre egy új runbookot az Azure Automationben vagy importálása egy fájlból."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 24414362-b690-4474-8ca7-df18e30fc31d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: f2e34e6a4d3d2f29fe6320d805e38e6fccbb74de
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Létrehozása vagy egy Azure Automation forgatókönyv importálása
Hozzáadhat egy runbook az Azure Automation által [újat hoz létre](#creating-a-new-runbook) vagy egy létező runbookot importál egy fájlból, vagy a a [forgatókönyvek](automation-runbook-gallery.md). Ez a cikk bemutatja, létrehozása és runbookok importálása fájlból.  Összes közösségi runbookok és a modulok használata a részletes kaphat [az Azure Automation forgatókönyv- és gyűjtemények](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Új runbook létrehozása
Azure Automation egyike az Azure portálon vagy a Windows PowerShell segítségével is létrehozhat egy új runbookot. A runbook létrehozása után szerkesztheti témakörben található információk alapján [tanulási PowerShell munkafolyamat](automation-powershell-workflow.md) és [grafikus készítése az Azure Automationben](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Egy új Azure Automation-runbook létrehozása a klasszikus Azure portálon
Csak dolgozhat [PowerShell munkafolyamat-forgatókönyvekről](automation-runbook-types.md#powershell-workflow-runbooks) az Azure portálon.

1. A klasszikus Azure portálon kattintson, **új**, **alkalmazásszolgáltatások**, **Automation**, **Runbook**, **Gyorslétrehozás**.
2. Adja meg a szükséges adatokat, és kattintson **létrehozása**. A runbook nevének betűvel kell kezdődnie, és lehet, betűket, számokat, aláhúzásjeleket és kötőjeleket.
3. Ha módosítani szeretné a runbook most, majd kattintson **Runbook szerkesztése**. Ellenkező esetben kattintson a **OK**.
4. Az új runbook megjelenik a **Runbookok** fülre.

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Egy új Azure Automation-runbook létrehozása az Azure portállal
1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza ki a központ **Runbookok** runbookok listájának megnyitásához.
3. Kattintson a **hozzáadása egy runbook** gombra, majd **hozzon létre egy új runbookot**.
4. Adjon meg egy **neve** a runbookhoz, és válassza ki a [típus](automation-runbook-types.md). A runbook nevének betűvel kell kezdődnie, és lehet, betűket, számokat, aláhúzásjeleket és kötőjeleket.
5. Kattintson a **létrehozása** hozza létre a runbookot, és nyissa meg a szerkesztőt.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Egy új Azure Automation-runbook létrehozása a Windows PowerShell használatával
Használhatja a [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) parancsmag segítségével hozzon létre egy üres [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks). Megadhatja a **neve** paraméterrel hozhat létre egy üres runbookot, később módosíthatja, vagy megadhatja a **elérési** paraméter runbook fájl importálásához. A **típus** paraméter is kell adnia egy runbook négyféle benne kell lennie.

A következő mintaparancsok bemutatják, hogyan hozzon létre egy új üres runbookot.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Runbook Azure Automation-fájlból való importálása
Létrehozhat egy új runbookot az Azure Automation a PowerShell vagy a PowerShell-munkafolyamati (.ps1 kiterjesztéssel), az exportált grafikus forgatókönyvnek (.graphrunbook), vagy Python 2 parancsfájl (.py kiterjesztés) importálásával.  Meg kell adnia a [típusú forgatókönyvet](automation-runbook-types.md) az alábbiakat is figyelembe véve, az importálás során létrehozott.

* Egy .graphrunbook fájl csak a olyan új importálhatók [grafikus forgatókönyvnek](automation-runbook-types.md#graphical-runbooks), és csak grafikus forgatókönyvekhez hozhatók létre .graphrunbook fájlból.
* Egy PowerShell-munkafolyamat tartalmazó .ps1 fájl csak olyan importálhatók egy [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks).  Ha a fájl több PowerShell-munkafolyamatok tartalmaz, akkor az importálás sikertelen lesz. Az egyes munkafolyamatokban a saját fájl mentése és importálása egyes külön-külön kell.
* Egy .ps1 fájlt, amely nem tartalmaz egy munkafolyamat vagy importálhatók egy [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) vagy egy [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks).  Ha egy PowerShell-munkafolyamati forgatókönyv lesz importálva, majd alakítja át a munkafolyamat és megjegyzéseit adja meg az elvégzett módosítások a runbook szerepelnek.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Runbook importálása fájlból a klasszikus Azure portálon
Az alábbi eljárás segítségével egy parancsfájl fájlt importálja az Azure Automation.  Vegye figyelembe, hogy csak importálhatja egy .ps1 fájlt egy PowerShell-munkafolyamati forgatókönyv használata ezen a portálon.  Az Azure portál egyéb kell használnia.

1. A klasszikus Azure portálon, válassza ki a **Automation** , és válassza az Automation-fiók.
2. Kattintson az **Importálás** gombra.
3. Kattintson a **fájl** , és keresse meg az importálandó parancsfájl fájl.
4. Ha módosítani szeretné a runbook most, majd kattintson **Runbook szerkesztése**. Ellenkező esetben kattintson az OK gombra.
5. Az új runbook megjelenik a **Runbookok** fülre az Automation-fiókhoz.
6. Meg kell [közzétenni a runbookot](#publishing-a-runbook) előtt is futtathatja.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Runbook importálása egy fájlból, és az Azure portál
Az alábbi eljárás segítségével egy parancsfájl fájlt importálja az Azure Automation.  

> [!NOTE]
> Vegye figyelembe, hogy csak importálhatja egy .ps1 fájlt egy PowerShell-munkafolyamati forgatókönyv, a portál használatával.
> 
> 

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza ki a központ **Runbookok** runbookok listájának megnyitásához.
3. Kattintson a a **hozzáadása egy runbook** gombra, majd **importálási**.
4. Kattintson a **Runbook fájl** válassza ki az importálandó fájlt
5. Ha a **neve** mező engedélyezve van, akkor lehetősége van a módosításhoz.  A runbook nevének betűvel kell kezdődnie, és lehet, betűket, számokat, aláhúzásjeleket és kötőjeleket.
6. A [runbooktípusba](automation-runbook-types.md) automatikusan ki van jelölve, de a megfelelő korlátozások figyelembe vétele után módosíthatja a típusát. 
7. Az új runbook megjelenik forgatókönyvek listája az Automation-fiókhoz.
8. Meg kell [közzétenni a runbookot](#publishing-a-runbook) előtt is futtathatja.

> [!NOTE]
> Egy grafikus forgatókönyvnek vagy egy grafikus PowerShell-munkafolyamati forgatókönyv importálása után a beállítást, ha más típusra konvertálni. Nem konvertálható szöveges forgatókönyvként.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Runbook importálása parancsfájlból a Windows PowerShell használatával
Használhatja a [Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) parancsmag használatával importálhat parancsfájlt a PowerShell-munkafolyamati forgatókönyv vázlatként. Ha a runbook már létezik, az importálás sikertelen, ha nem használ a *-Force* paraméter. 

A következő mintaparancsok bemutatják, hogyan importálhat parancsfájlt runbookba.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Runbook közzététele
Létrehozásakor, vagy egy új forgatókönyv importálása, közzé kell tennie, mielőtt futtatná azt.  Minden runbook Automation rendelkezik, egy Piszkozat és egy közzétett verziója. Csak a közzétett verziót lehet futtatható legyen, és kizárólag a piszkozat verzió szerkeszthető. A közzétett verziója nincs hatással a piszkozat verzióban végrehajtott módosítások. A vázlatként megjelölt verziót elérhetővé kell tenni, ha majd beállíthatja azt is, amely felülírja a közzétett verziót a vázlatként megjelölt verziót.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>A klasszikus Azure portál használatával runbook közzététele
1. Nyissa meg a runbookot a klasszikus Azure portálon.
2. Kattintson a képernyő tetején **Szerző**.
3. A képernyő alján kattintson **közzététel** , majd **Igen** a üzenet számára.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Az Azure portál használatával runbook közzététele
1. Nyissa meg a runbookot az Azure portálon.
2. Kattintson a **szerkesztése** gombra.
3. Kattintson a **közzététel** gombra, majd **Igen** a üzenet számára.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>A Windows PowerShell runbook közzététele
Használhatja a [Publish-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) közzé egy runbookot, a Windows PowerShell-parancsmagot. A következő mintaparancsok bemutatják, hogyan minta runbook közzététele.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Következő lépések
* Hogyan előnyeit úgy használhatja ki a Runbookot és a PowerShell modul gyűjteménye, lásd: [az Azure Automation forgatókönyv- és minták](automation-runbook-gallery.md)
* Szöveges szerkesztővel PowerShell és a PowerShell-munkafolyamati forgatókönyvek szerkesztésével kapcsolatos további tudnivalókért lásd: [szöveges az Azure Automation runbookjai szerkesztése](automation-edit-textual-runbook.md)
* További információk a létrehozásról grafikus forgatókönyvnek, [grafikus készítése az Azure Automationben](automation-graphical-authoring-intro.md)

