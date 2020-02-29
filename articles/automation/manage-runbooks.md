---
title: Runbookok kezelése Azure Automation
description: Ez a cikk bemutatja, hogyan kezelheti a runbookok a Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 4531e5d483508ca99722182f97c6a7aa0e0b68f3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191112"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbookok kezelése Azure Automation

Hozzáadhat egy runbook a Azure Automationhoz, vagy létrehozhat [egy újat](#create-a-runbook) , vagy [importálhat egy meglévőt](#import-a-runbook) egy fájlból vagy a [runbook-gyűjteményből](automation-runbook-gallery.md). Ez a cikk a runbookok fájlokból való létrehozásával és importálásával kapcsolatos információkat tartalmaz. A Azure Automation a közösségi runbookok és moduljaihoz való hozzáférés összes részletét megtekintheti a [Runbook és modul-galériákban](automation-runbook-gallery.md).

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Runbook létrehozása

Az Azure-portálok vagy a Windows PowerShell használatával létrehozhat egy új runbook Azure Automation. A runbook létrehozása után szerkesztheti azt a [PowerShell-munkafolyamatok](automation-powershell-workflow.md) és [a Azure Automation grafikus](automation-graphical-authoring-intro.md)felhasználói felületének megismerése című témakörben található információk segítségével.

### <a name="create-a-runbook-in-the-azure-portal"></a>Runbook létrehozása a Azure Portalban

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A runbookok listájának megnyitásához a központból válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
3. Kattintson **a Runbook létrehozása**elemre.
4. Adja meg a runbook nevét, és válassza ki a [típusát](automation-runbook-types.md). A runbook nevének betűvel kell kezdődnie, és csak betűket, számokat, aláhúzásokat és kötőjeleket tartalmazhat.
5. A runbook létrehozásához és a szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

### <a name="create-a-runbook-with-powershell"></a>Runbook létrehozása a PowerShell-lel

A [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) parancsmag használatával létrehozhat egy üres [PowerShell-munkafolyamat-runbook](automation-runbook-types.md#powershell-workflow-runbooks). A *Type* paraméterrel megadhatja a **New-AzAutomationRunbook**definiált runbook-típusok egyikét.

Az alábbi példa bemutatja, hogyan hozhat létre új üres runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook importálása

Azure Automation létrehozhat egy új runbook egy PowerShell-parancsfájl vagy egy PowerShell-munkafolyamat ( **. ps1**), egy exportált grafikus runbook ( **. graphrunbook**) vagy egy Python 2-parancsfájl ( **.** másolás) importálásával.  Meg kell adnia az importálás során létrehozott [runbook típusát](automation-runbook-types.md) , figyelembe véve az alábbi szempontokat.

* Egy munkafolyamatot nem tartalmazó **. ps1** fájl importálható egy [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) vagy egy PowerShell- [munkafolyamat runbook](automation-runbook-types.md#powershell-workflow-runbooks). Ha egy PowerShell munkafolyamat-runbook importálja, azt egy munkafolyamatba konvertálja a rendszer. Ebben az esetben a megjegyzéseket a runbook tartalmazza az elvégzett módosítások leírásához.

* Egy PowerShell-munkafolyamatot tartalmazó **. ps1** fájl csak [PowerShell munkafolyamat-runbook](automation-runbook-types.md#powershell-workflow-runbooks)importálható. Ha a fájl több PowerShell-munkafolyamatot tartalmaz, az importálás sikertelen lesz. Minden munkafolyamatot a saját fájljába kell mentenie, és külön kell importálnia azokat.

* Egy PowerShell-munkafolyamatot tartalmazó **. ps1** fájl nem importálható [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks), mert a PowerShell-parancsfájl motorja nem ismeri fel.

* A **. graphrunbook** -fájlok csak új [grafikus runbook](automation-runbook-types.md#graphical-runbooks)importálhatók. Vegye figyelembe, hogy egy **. graphrunbook** fájlból csak grafikus runbook hozhat létre.

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Runbook importálása fájlból a Azure Portal

A parancsfájlokat a következő eljárással importálhatja Azure Automationba.

> [!NOTE]
> Egy **. ps1** fájlt csak a portál használatával lehet egy PowerShell-munkafolyamati runbook importálni.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A runbookok listájának megnyitásához a központból válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
3. Kattintson **a Runbook importálása**elemre.
4. Kattintson a **Runbook fájl** elemre, és válassza ki az importálni kívánt fájlt.
5. Ha a **név** mező engedélyezve van, lehetősége van módosítani a runbook nevét. A névnek betűvel kell kezdődnie, és csak betűket, számokat, aláhúzásokat és kötőjeleket tartalmazhat.
6. A [runbook típusa](automation-runbook-types.md) automatikusan ki van választva, de a megfelelő korlátozások figyelembe vétele után módosíthatja a típust.
7. Kattintson a **Létrehozás** gombra. Az új runbook megjelenik az Automation-fiók runbookok listájában.
8. A Futtatás előtt közzé kell tennie [a runbook](#publish-a-runbook) .

> [!NOTE]
> Grafikus runbook vagy grafikus PowerShell munkafolyamat-runbook importálása után átalakíthatja azt más típusra. A grafikus runbookok egyikét azonban nem konvertálhatja szöveges runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Runbook importálása parancsfájlból a Windows PowerShell-lel

Az [import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) parancsmag használatával egy parancsfájlt importálhat a PowerShell-munkafolyamat runbook. Ha a runbook már létezik, az importálás meghiúsul, ha a *Force* paramétert használja a parancsmaggal.

Az alábbi példa bemutatja, hogyan importálhat egy parancsfájlt egy runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Runbook tesztelése

A runbook tesztelésekor a rendszer végrehajtja a [Piszkozat verzióját](#publish-a-runbook) , és az általa végrehajtott műveletek befejeződtek. A rendszer nem hoz létre munkaelőzményeket, de a [kimenet](automation-runbook-output-and-messages.md#output-stream) és a [Figyelmeztetés és a hiba](automation-runbook-output-and-messages.md#message-streams) streamek a test output (teszt kimenet) ablaktáblán jelennek meg. A [részletes adatfolyamba](automation-runbook-output-and-messages.md#message-streams) küldött üzenetek csak akkor jelennek meg a kimeneti ablaktáblán, ha a [$VerbosePreference változó](automation-runbook-output-and-messages.md#preference-variables) a **Folytatás**értékre van állítva.

Bár a Piszkozat verziója fut, a runbook továbbra is szabályosan hajtja végre, és minden műveletet végrehajt a környezetben lévő erőforrásokon. Emiatt csak a nem éles erőforrások runbookok kell tesztelni.

Az egyes [runbook-típusok](automation-runbook-types.md) tesztelési eljárása azonos. Nincs különbség a szöveges szerkesztő és a Azure Portal grafikus szerkesztőjé közötti tesztelésben.

1. Nyissa meg a runbook Piszkozat verzióját a [szöveges szerkesztőben](automation-edit-textual-runbook.md) vagy a [grafikus szerkesztőben](automation-graphical-authoring-intro.md).
1. Kattintson a **teszt** gombra a teszt lap megnyitásához.
1. Ha a runbook paraméterekkel rendelkezik, azok megjelennek a bal oldali panelen, ahol megadhatja a teszthez használni kívánt értékeket.
1. Ha a tesztet [hibrid Runbook-feldolgozón](automation-hybrid-runbook-worker.md)szeretné futtatni, módosítsa a **futtatási beállításokat** **hibrid feldolgozóra** , majd válassza ki a célcsoport nevét.  Ellenkező esetben tartsa meg az alapértelmezett **Azure** -t a teszt felhőben való futtatásához.
1. A teszt elindításához kattintson a **Start** gombra.
1. A kimenet ablaktáblán található gombokkal leállíthatja vagy felfüggesztheti a [PowerShell-munkafolyamatokat](automation-runbook-types.md#powershell-workflow-runbooks) vagy [grafikus](automation-runbook-types.md#graphical-runbooks) runbook a tesztelés során. A runbook felfüggesztésekor előtt felfüggeszti végrehajtja az aktuális tevékenységet. Ha a runbook fel van függesztve, állítsa le, vagy újraindíthatja.
1. Vizsgálja meg a kimenet ablaktáblán látható runbook.

## <a name="publish-a-runbook"></a>Runbook közzététele

Új runbook létrehozásakor vagy importálásakor a Futtatás előtt közzé kell tennie. A Azure Automation minden runbook tartalmaz egy Piszkozat-verziót és egy közzétett verziót. Csak a közzétett verziót lehet futtatni, és kizárólag a piszkozat verzió szerkeszthető. A közzétett verzióra nincsenek hatással a piszkozat verzióban végrehajtott módosítások. Ha elérhetővé kell tenni a Piszkozat verzióját, azt közzéteszi, és felülírja a jelenlegi közzétett verziót a Piszkozat verziójával.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Runbook közzététele a Azure Portalban

1. Nyissa meg a runbook a Azure Portalban.
2. Kattintson a **Szerkesztés** gombra.
3. Kattintson a **Közzététel** , majd az **Igen** lehetőségre az ellenőrző üzenetre válaszul.

### <a name="publish-a-runbook-using-powershell"></a>PowerShell-lel runbook közzététele

A [publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) parancsmaggal közzétehet egy Runbook a Windows PowerShell használatával. Az alábbi példa bemutatja, hogyan tehet közzé egy minta-runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan részesülhet a Runbook és a PowerShell modul-katalógusban, tekintse meg a [Azure Automation Runbook és modul-galériáit](automation-runbook-gallery.md).
* Ha többet szeretne megtudni a PowerShell-és a PowerShell-munkafolyamat runbookok a szöveges szerkesztővel való szerkesztésével kapcsolatban, tekintse meg a [szöveges Runbookok szerkesztése Azure Automationban című részt](automation-edit-textual-runbook.md)
* További információ a grafikus runbook létrehozásáról: [grafikus szerzői műveletek Azure Automationban](automation-graphical-authoring-intro.md).
