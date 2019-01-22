---
title: Létrehozása vagy importálása az Azure Automation-runbook
description: Ez a cikk ismerteti az Azure Automation az új runbook létrehozása vagy importálása egy fájlból.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fdc064ab2b74424ce1e4e163c8843bfebc28bcf4
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435566"
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Létrehozása vagy importálása az Azure Automation-runbook

Hozzáadhat egy runbook Azure Automation által [újat hozna létre](#creating-a-new-runbook) vagy egy létező runbookot importál, vagy a a [forgatókönyv-katalógusában](automation-runbook-gallery.md). Ez a cikk információt nyújt a létrehozása, és a runbookok importálása egy fájlból.  Megtekintheti az összes részletes adat a Közösség forgatókönyveit és moduljait az elérése [Azure Automation forgatókönyv- és galériák](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Új runbook létrehozása

Az Azure Automationben az Azure Portalon vagy a Windows PowerShell segítségével hozhat létre egy új runbookot. A runbook létrehozása után szerkesztheti témakörben található információk alapján [PowerShell-munkafolyamat megismerése](automation-powershell-workflow.md) és [grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Az Azure Portallal egy új Azure Automation-runbook létrehozása

1. Az Azure Portalon nyissa meg az Automation-fiókját.
1. Válassza ki a Hub **Runbookok** forgatókönyvek listájának megnyitásához.
1. Kattintson a **forgatókönyv hozzáadása** gombra, majd **hozzon létre egy új runbookot**.
1. Adjon meg egy **neve** a runbookhoz, és válassza ki a [típus](automation-runbook-types.md). A runbook neve egy betűvel kell kezdődnie, és rendelkezhetnek, betűket, számokat, aláhúzásjeleket és kötőjeleket tartalmazhat.
1. Kattintson a **létrehozás** hozza létre a runbookot, és a szerkesztő megnyitásához.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Egy új Azure Automation-runbook létrehozása a Windows PowerShell használatával
Használhatja a [New-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationrunbook) parancsmaggal hozzon létre egy üres [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks). A **típus** paraméter is kell kiválasztani a négy runbook-típusok közül.

A következő mintaparancsok bemutatják, hogyan hozhat létre egy új üres runbookot.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Runbook importálása egy fájlból az Azure Automationbe

Létrehozhat egy új runbookot az Azure Automationben egy PowerShell-parancsfájlt vagy a PowerShell-munkafolyamat (.ps1 kiterjesztéssel), egy exportált grafikus forgatókönyvnek (.graphrunbook) vagy a Python 2 parancsfájl (.py kiterjesztéssel) importálásával.  Meg kell adnia a [típusú forgatókönyvet](automation-runbook-types.md) az alábbi szempontokat figyelembe véve, az importálás során létrehozott.

* Egy .graphrunbook fájlt egy új, csak importálhatók [grafikus forgatókönyv](automation-runbook-types.md#graphical-runbooks), és a grafikus runbookok csak egy .graphrunbook fájlból hozható létre.
* Egy .ps1 kiterjesztésű fájlba, egy PowerShell-munkafolyamat tartalmazó csak importálhatók, egy [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks).  Ha a fájl több PowerShell-munkafolyamatok tartalmaz, akkor az importálás sikertelen lesz. Mentse a saját minden egyes munkafolyamat és importálhatja minden egyes külön-külön kell.
* Egy .ps1 kiterjesztésű fájlba, amely nem tartalmazza a munkafolyamat vagy importálhatók egy [PowerShell-forgatókönyv](automation-runbook-types.md#powershell-runbooks) vagy egy [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks).  Egy PowerShell-munkafolyamati forgatókönyv importálva elromlik, majd azt a munkafolyamat konvertálja és megjegyzéseit adja meg az elvégzett módosítások a runbook szerepelnek.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Runbook importálása egy fájlból az Azure portal használatával

A következő eljárással importálhat parancsfájlt az Azure Automationbe.  

> [!NOTE]
> Vegye figyelembe, hogy csak egy .ps1 kiterjesztésű fájlba programba importálhatók egy PowerShell-munkafolyamati forgatókönyv a portál használatával.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza ki a Hub **Runbookok** forgatókönyvek listájának megnyitásához.
3. Kattintson a **forgatókönyv hozzáadása** gombra, majd **importálás**.
4. Kattintson a **forgatókönyvfájl** válassza ki az importálandó fájlt
5. Ha a **neve** mező engedélyezve van, akkor lehetősége van módosítani azt.  A runbook neve egy betűvel kell kezdődnie, és rendelkezhetnek, betűket, számokat, aláhúzásjeleket és kötőjeleket tartalmazhat.
6. A [runbook típusa](automation-runbook-types.md) automatikusan ki van jelölve, de módosíthatja a típust a alkalmazni korlátozások figyelembe véve. 
7. Az új runbook megjelenik a listán a runbookok az Automation-fiókhoz.
8. Meg kell [közzétenni a runbookot](#publishing-a-runbook) előtt is futtatható legyen.

> [!NOTE]
> Grafikus forgatókönyv vagy a grafikus PowerShell-munkafolyamati forgatókönyv importálása után lehetősége van más típusra konvertálni, ha a szeretett volna. Nem alakítható át szöveges runbookok.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Runbook importálása parancsfájlból a Windows PowerShell-lel
Használhatja a [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) parancsmag használatával importálhat parancsfájlt a PowerShell-munkafolyamati forgatókönyv piszkozataként. Ha a runbook már létezik, az importálás sikertelen lesz, ha nem használ a *-Force* paraméter. 

Az alábbi mintaparancsok bemutatják, hogyan importálhat parancsfájlt a runbookba.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="publishing-a-runbook"></a>Runbook közzététele

Hoz létre, vagy importálja egy új runbookot, ha közzé kell tennie, mielőtt is futtatható legyen.  Minden runbook Automation rendelkezik, egy Vázlat és egy közzétett verziója. Csak a közzétett verziót lehet futtatni, és kizárólag a piszkozat verzió szerkeszthető. A közzétett verzióra nincsenek hatással a piszkozat verzióban végrehajtott módosítások. Ha a vázlatként megjelölt verziót elérhetővé kell tenni, majd közzéteheti azt, amely felülírja a közzétett verziót a piszkozattal.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Tehet közzé egy runbookot, az Azure portal használatával

1. Az Azure Portalon nyissa meg a runbookot.
1. Kattintson a **szerkesztése** gombra.
1. Kattintson a **közzététel** gombra, majd **Igen** , az ellenőrző üzenetet.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Windows PowerShell-lel runbook közzététele
Használhatja a [Publish-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/publish-azurermautomationrunbook) közzé egy runbookot, a Windows PowerShell-parancsmagot. A következő mintaparancsok bemutatják, hogyan tehet közzé egy runbookot.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>További lépések

* Hogyan élvezheti a Runbook és a PowerShell-modul galériából kapcsolatos további információkért lásd: [Azure Automation forgatókönyv- és katalógusok](automation-runbook-gallery.md)
* Egy szöveges szerkesztő a PowerShell és a PowerShell-munkafolyamati runbookok szerkesztésével kapcsolatos további tudnivalókért lásd: [az Azure Automationben szöveges runbookok szerkesztése](automation-edit-textual-runbook.md)
* Grafikus forgatókönyv-készítési kapcsolatos további információkért lásd: [grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md)
