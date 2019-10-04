---
title: Runbookok kezelése Azure Automation
description: Ez a cikk bemutatja, hogyan kezelheti a runbookok a Azure Automationban.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a477811e46d97375d4dce4d83072dda60ca797c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717220"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbookok kezelése Azure Automation

Hozzáadhat egy runbook a Azure Automationhoz, vagy létrehozhat [egy újat](#create-a-runbook) , vagy importálhat egy meglévő runbook egy fájlból vagy a [runbook](automation-runbook-gallery.md)-gyűjteményből. Ez a cikk a runbookok fájlokból való létrehozásával és importálásával kapcsolatos információkat tartalmaz.  A [Runbook-ben és a Azure Automation](automation-runbook-gallery.md)-modulban elérhető közösségi runbookok és moduljaihoz való hozzáférés összes részletét bemutathatja.

## <a name="create-a-runbook"></a>Forgatókönyv létrehozása

Az Azure-portálok vagy a Windows PowerShell használatával létrehozhat egy új runbook Azure Automation. A runbook létrehozása után szerkesztheti azt a [PowerShell](automation-powershell-workflow.md) -munkafolyamatok és [a Azure Automation grafikus](automation-graphical-authoring-intro.md)felhasználói felületének megismerése című témakörben található információk segítségével.

### <a name="create-a-runbook-in-the-azure-portal"></a>Runbook létrehozása a Azure Portalban

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A központban válassza a **runbookok** lehetőséget a runbookok listájának megnyitásához.
3. Kattintson a **Runbook hozzáadása** gombra, és hozzon **létre egy új runbook**.
4. Adja meg a runbook **nevét** , és válassza ki a [típusát](automation-runbook-types.md). A runbook neve egy betűvel kell kezdődnie, és rendelkezhetnek, betűket, számokat, aláhúzásjeleket és kötőjeleket tartalmazhat.
5. A runbook létrehozásához és a szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

### <a name="create-a-runbook-with-powershell"></a>Runbook létrehozása a PowerShell-lel

A [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) parancsmag használatával létrehozhat egy üres [PowerShell-munkafolyamat-runbook](automation-runbook-types.md#powershell-workflow-runbooks). A **Type** paraméter használatával adja meg a négy runbook egyikét.

Az alábbi példák azt mutatják be, hogyan lehet új üres runbook létrehozni.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook importálása

Azure Automation létrehozhat egy új runbook egy PowerShell-parancsfájl vagy egy PowerShell-munkafolyamat (. ps1 kiterjesztés), egy exportált grafikus runbook (. graphrunbook) vagy egy Python 2 parancsfájl (. Másolás kiterjesztés) importálásával.  Meg kell adnia az importálás során létrehozott [runbook típusát](automation-runbook-types.md) , figyelembe véve az alábbi szempontokat.

* Egy `.graphrunbook` fájl csak új [grafikus runbook](automation-runbook-types.md#graphical-runbooks)importálható, és a grafikus runbookok csak `.graphrunbook` fájlból hozhatók létre.
* A PowerShell-munkafolyamatokat tartalmazó [](automation-runbook-types.md#powershell-workflow-runbooks) fájlokcsakPowerShellmunkafolyamat-runbook`.ps1` importálhatók. Ha a fájl több PowerShell-munkafolyamatot tartalmaz, az importálás sikertelen lesz. Minden munkafolyamatot a saját fájljába kell mentenie, és külön kell importálnia azokat.
* Egy PowerShell-munkafolyamatot tartalmazó [](automation-runbook-types.md#powershell-runbooks) fájlnemimportálhatóPowerShell-runbook,mertaPowerShell-parancsfájlmotorjanemismeri`.ps1` fel.
* Egy `.ps1` munkafolyamatot nem tartalmazó fájl nem importálható [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) vagy PowerShell- [munkafolyamat-runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Ha egy PowerShell-munkafolyamati runbook lett importálva, akkor a rendszer egy munkafolyamatba konvertálja, és a megjegyzéseket a runbook tartalmazza, és megadja az elvégzett módosításokat.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Runbook importálása fájlból a Azure Portal

A parancsfájlokat a következő eljárással importálhatja Azure Automationba.

> [!NOTE]
> Vegye figyelembe, hogy a portál használatával csak. ps1 fájlokat importálhat egy PowerShell-munkafolyamati runbook.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A központban válassza a **runbookok** lehetőséget a runbookok listájának megnyitásához.
3. Kattintson a **Runbook hozzáadása** gombra, majd az **Importálás**elemre.
4. Az importálandó fájl kiválasztásához kattintson a **Runbook fájl** elemre.
5. Ha a **név** mező engedélyezve van, akkor lehetősége van módosítani.  A runbook neve egy betűvel kell kezdődnie, és rendelkezhetnek, betűket, számokat, aláhúzásjeleket és kötőjeleket tartalmazhat.
6. A [runbook típusa](automation-runbook-types.md) automatikusan ki van választva, de a megfelelő korlátozások figyelembe vétele után módosíthatja a típust.
7. Az új runbook megjelenik az Automation-fiók runbookok listájában.
8. A Futtatás előtt közzé kell tennie [a runbook](#publish-a-runbook) .

> [!NOTE]
> Grafikus runbook vagy grafikus PowerShell munkafolyamat-runbook importálása után lehetősége van arra, hogy a másik típusra váltson, ha szeretné. Nem lehet konvertálni szöveges runbook.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Runbook importálása parancsfájlból a Windows PowerShell használatával

Az [import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) parancsmag használatával parancsfájlokat importálhat a PowerShell-munkafolyamat runbook. Ha a runbook már létezik, az importálás meghiúsul, ha a *-Force* paramétert használja.

Az alábbi példák bemutatják, hogyan importálhat egy parancsfájlt egy runbook.

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

A runbook tesztelésekor a rendszer végrehajtja a [Piszkozat verzióját](#publish-a-runbook) , és az általa végrehajtott műveletek befejeződtek. A rendszer nem hoz létre munkaelőzményeket, de a [kimenet](automation-runbook-output-and-messages.md#output-stream) és a [Figyelmeztetés és a hiba](automation-runbook-output-and-messages.md#message-streams) streamek a test output (teszt kimenet) ablaktáblán jelennek meg. A [részletes adatfolyamba](automation-runbook-output-and-messages.md#message-streams) küldött üzenetek csak akkor jelennek meg a kimeneti ablaktáblán, ha a [$VerbosePreference változó](automation-runbook-output-and-messages.md#preference-variables) a folytatás értékre van állítva.

Bár a Piszkozat verziója fut, a runbook továbbra is szabályosan hajtja végre, és minden műveletet végrehajt a környezetben lévő erőforrásokon. Emiatt csak a nem éles erőforrások runbookok kell tesztelni.

Az egyes [runbook-típusok](automation-runbook-types.md) tesztelésének eljárása azonos, és nincs különbség a szöveges szerkesztő és a Azure Portal grafikus szerkesztőjé közötti tesztelésben.

1. Nyissa meg a runbook Piszkozat verzióját a [szöveges szerkesztőben](automation-edit-textual-runbook.md) vagy a [grafikus szerkesztőben](automation-graphical-authoring-intro.md).
1. Kattintson a **teszt** gombra a teszt lap megnyitásához.
1. Ha a runbook paraméterekkel rendelkezik, azok a bal oldali panelen jelennek meg, ahol megadhatja a teszthez használni kívánt értékeket.
1. Ha a tesztet [hibrid Runbook](automation-hybrid-runbook-worker.md)-feldolgozón szeretné futtatni, akkor módosítsa a **futtatási beállításokat** **hibrid** feldolgozóra, majd válassza ki a célcsoport nevét.  Ellenkező esetben tartsa meg az alapértelmezett **Azure** -t a teszt felhőben való futtatásához.
1. A teszt elindításához kattintson a **Start** gombra.
1. Ha a runbook [PowerShell-munkafolyamat](automation-runbook-types.md#powershell-workflow-runbooks) vagy [grafikus](automation-runbook-types.md#graphical-runbooks), akkor leállíthatja vagy felfüggesztheti a kimenet panel alatti gombokkal végzett tesztelés során. A runbook felfüggesztésekor előtt felfüggeszti végrehajtja az aktuális tevékenységet. Ha a runbook fel van függesztve, állítsa le, vagy újraindíthatja.
1. Vizsgálja meg a kimenet ablaktáblán látható runbook.

## <a name="publish-a-runbook"></a>Forgatókönyv közzététele

Új runbook létrehozásakor vagy importálásakor a Futtatás előtt közzé kell tennie.  Az Automation minden runbook rendelkezik egy Piszkozatmal és egy közzétett verzióval. Csak a közzétett verziót lehet futtatni, és kizárólag a piszkozat verzió szerkeszthető. A közzétett verzióra nincsenek hatással a piszkozat verzióban végrehajtott módosítások. Ha elérhetővé kell tenni a Piszkozat verzióját, akkor azt közzé kell tennie, amely felülírja a közzétett verziót a Piszkozat verziójával.

### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a runbook a Azure Portalban.
2. Kattintson a **Szerkesztés** gombra.
3. Kattintson a **Közzététel** gombra, majd az **Igen** gombra az ellenőrző üzenethez.

### <a name="powershell"></a>PowerShell

A [publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) parancsmaggal közzétehet egy Runbook a Windows PowerShell használatával. Az alábbi példák azt mutatják be, hogyan tehet közzé egy minta-runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>További lépések

* A Runbook és a PowerShell modul-katalógus előnyeinek megismeréséhez lásd: [Runbook és modul-galériák a Azure Automation](automation-runbook-gallery.md)
* Ha többet szeretne megtudni a PowerShell-és a PowerShell-munkafolyamat runbookok a szöveges szerkesztővel való szerkesztésével kapcsolatban, tekintse meg a [szöveges Runbookok szerkesztése Azure Automation](automation-edit-textual-runbook.md)
* További információ a grafikus runbook létrehozásáról: [grafikus szerzői műveletek Azure Automation](automation-graphical-authoring-intro.md)
