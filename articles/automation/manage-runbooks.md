---
title: Az Azure Automation runbookjai kezelése
description: Ez a cikk az Azure Automation runbookjai kezelését ismerteti.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5bb52e0547ed9bc18d67370ffb9db35942212aab
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887590"
---
# <a name="manage-runbooks-in-azure-automation"></a>Az Azure Automation runbookjai kezelése

Hozzáadhat egy runbook Azure Automation által [újat hozna létre](#create-a-runbook) vagy egy létező runbookot importál egy fájlból vagy a [forgatókönyv-katalógusában](automation-runbook-gallery.md). Ez a cikk információt nyújt a létrehozása, és a runbookok importálása egy fájlból.  Megtekintheti az összes részletes adat a Közösség forgatókönyveit és moduljait az elérése [Azure Automation forgatókönyv- és galériák](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Runbook létrehozása

Az Azure Automationben az Azure Portalon vagy a Windows PowerShell segítségével hozhat létre egy új runbookot. A runbook létrehozása után szerkesztheti témakörben található információk alapján [PowerShell-munkafolyamat megismerése](automation-powershell-workflow.md) és [grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Runbook létrehozása az Azure Portalon

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza ki a Hub **Runbookok** forgatókönyvek listájának megnyitásához.
3. Kattintson a **forgatókönyv hozzáadása** gombra, majd **hozzon létre egy új runbookot**.
4. Adjon meg egy **neve** a runbookhoz, és válassza ki a [típus](automation-runbook-types.md). A runbook neve egy betűvel kell kezdődnie, és rendelkezhetnek, betűket, számokat, aláhúzásjeleket és kötőjeleket tartalmazhat.
5. Kattintson a **létrehozás** hozza létre a runbookot, és a szerkesztő megnyitásához.

### <a name="create-a-runbook-with-powershell"></a>Runbook létrehozása a PowerShell-lel

Használhatja a [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) parancsmaggal hozzon létre egy üres [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks). Használja a **típus** paraméterrel adja meg a négy runbook-típusok közül.

A következő mintaparancsok bemutatják, hogyan hozhat létre egy új üres runbookot.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook importálása

Létrehozhat egy új runbookot az Azure Automationben egy PowerShell-parancsfájlt vagy a PowerShell-munkafolyamat (.ps1 kiterjesztéssel), egy exportált grafikus forgatókönyvnek (.graphrunbook) vagy a Python 2 parancsfájl (.py kiterjesztéssel) importálásával.  Meg kell adnia a [típusú forgatókönyvet](automation-runbook-types.md) az alábbi szempontokat figyelembe véve, az importálás során létrehozott.

* A `.graphrunbook` fájlt egy új, csak importálhatók [grafikus forgatókönyv](automation-runbook-types.md#graphical-runbooks), és a grafikus runbookok csak hozható létre egy `.graphrunbook` fájlt.
* A `.ps1` egy PowerShell-munkafolyamat tartalmazó fájl csak importálhatók, egy [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks).  Ha a fájl több PowerShell-munkafolyamatok tartalmaz, akkor az importálás sikertelen lesz. Mentse a saját minden egyes munkafolyamat és importálhatja minden egyes külön-külön kell.
* A `.ps1` fájlt, amely nem tartalmazza a munkafolyamat vagy importálhatók egy [PowerShell-forgatókönyv](automation-runbook-types.md#powershell-runbooks) vagy egy [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks).  Egy PowerShell-munkafolyamati forgatókönyv importálva elromlik, majd azt a munkafolyamat konvertálja és megjegyzéseit adja meg az elvégzett módosítások a runbook szerepelnek.

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
8. Meg kell [közzétenni a runbookot](#publish-a-runbook) előtt is futtatható legyen.

> [!NOTE]
> Grafikus forgatókönyv vagy a grafikus PowerShell-munkafolyamati forgatókönyv importálása után lehetősége van más típusra konvertálni, ha a szeretett volna. Nem alakítható át szöveges runbookok.

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

## <a name="test-a-runbook"></a>Runbook tesztelése

Egy runbook tesztelésekor a [vázlatként megjelölt verziót](#publish-a-runbook) hajtja végre, és minden olyan műveleteket, amelyeket végrehajt a végezhető el. Az előzményeket nem feladat jön létre, de a [kimeneti](automation-runbook-output-and-messages.md#output-stream) és [figyelmeztetési és](automation-runbook-output-and-messages.md#message-streams) adatfolyamok megjelennek a teszt panel kimenete. Az üzenetek a [részletes Stream](automation-runbook-output-and-messages.md#message-streams) megjelennek a Tesztkimenet ablaktáblán csak akkor, ha a [$VerbosePreference változó](automation-runbook-output-and-messages.md#preference-variables) Folytatás értékre van állítva.

Annak ellenére, hogy a vázlatként megjelölt verziót fut, a runbook továbbra is megfelelően hajt végre, és minden műveletet erőforrásokon elvégez a környezetben. Ezért soha ne tesztelje a runbookok nem termelési erőforrásokon.

Az eljárást minden egyes teszteléséhez [típusú forgatókönyvet](automation-runbook-types.md) azonos, és hogy a tesztelés a szöveges szerkesztő és az Azure Portalon a grafikus szerkesztő között nincs különbség.  

1. Nyissa meg a runbook vázlatverzióját vagy a [szöveges szerkesztő](automation-edit-textual-runbook.md) vagy [grafikus szerkesztő](automation-graphical-authoring-intro.md).
1. Kattintson a **teszt** gombra kattintva nyissa meg a tesztelési lapot.
1. Ha a runbook paraméterekkel rendelkezik, akkor lesz jelenik meg a bal oldali panelen, ahol megadhat értékeket a teszt használható.
1. Ha a vizsgálat futtatása egy [hibrid Runbook-feldolgozó](automation-hybrid-runbook-worker.md), majd módosítsa **beállításai** való **hibrid feldolgozó** válassza ki a nevét, a célcsoport.  Ellenkező esetben hagyja meg az alapértelmezett **Azure** a teszt futtatása a felhőben.
1. Kattintson a **Start** gombra kattintva indítsa el a vizsgálatot.
1. Ha a runbook [PowerShell-munkafolyamat](automation-runbook-types.md#powershell-workflow-runbooks) vagy [grafikus](automation-runbook-types.md#graphical-runbooks), akkor állítsa le, vagy felfüggesztésére, amíg a kimeneti ablaktábla alatti gombokkal tesztelését. A runbook felfüggesztésekor előtt felfüggeszti végrehajtja az aktuális tevékenységet. Ha a runbook fel van függesztve, állítsa le, vagy újraindíthatja.
1. Tekintse meg a kimenetet, a runbook a tesztkimenet ablaktáblán.

## <a name="publish-a-runbook"></a>Forgatókönyv közzététele

Hoz létre, vagy importálja egy új runbookot, ha közzé kell tennie, mielőtt is futtatható legyen.  Minden runbook Automation rendelkezik, egy Vázlat és egy közzétett verziója. Csak a közzétett verziót lehet futtatni, és kizárólag a piszkozat verzió szerkeszthető. A közzétett verzióra nincsenek hatással a piszkozat verzióban végrehajtott módosítások. Ha a vázlatként megjelölt verziót elérhetővé kell tenni, majd közzéteheti azt, amely felülírja a közzétett verziót a piszkozattal.

### <a name="azure-portal"></a>Azure Portal

1. Az Azure Portalon nyissa meg a runbookot.
2. Kattintson a **szerkesztése** gombra.
3. Kattintson a **közzététel** gombra, majd **Igen** , az ellenőrző üzenetet.

### <a name="powershell"></a>PowerShell

Használhatja a [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) közzé egy runbookot, a Windows PowerShell-parancsmagot. A következő mintaparancsok bemutatják, hogyan tehet közzé egy runbookot.

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
