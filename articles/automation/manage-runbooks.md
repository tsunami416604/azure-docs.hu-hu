---
title: Runbookok kezelése az Azure Automationben
description: Ez a cikk ismerteti, hogyan kezelheti a runbookok az Azure Automationben.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: a1229ee389b41625554fb2869089b08a3cb9cb6d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676513"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbookok kezelése az Azure Automationben

Runbookot hozzáadhat az Azure Automationhez úgy, hogy [létrehoz egy újat,](#creating-a-runbook) vagy [importál egy meglévőt](#importing-a-runbook) egy fájlból vagy a [Runbook-galériából.](automation-runbook-gallery.md) Ez a cikk a futtatási könyvek fájlból történő létrehozásáról és importálásáról nyújt tájékoztatást. A közösségi runbookok és modulok elérésének részleteit az [Azure Automation Runbook- és modulgalériáiban kaphatja](automation-runbook-gallery.md)meg.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="creating-a-runbook"></a>Runbook létrehozása

Az Azure Automationben új runbookot hozhat létre az Azure Portalok vagy a Windows PowerShell használatával. A runbook létrehozása után szerkesztheti azt a [Learning PowerShell-munkafolyamat](automation-powershell-workflow.md) és [az Azure Automation grafikus szerzői adatai](automation-graphical-authoring-intro.md)nak felhasználásával.

### <a name="create-a-runbook-in-the-azure-portal"></a>Runbook létrehozása az Azure Portalon

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A hubon válassza a **Runbookok** **a Folyamatautomatizálás** csoportban a runbookok listájának megnyitásához.
3. Kattintson **a Runbook létrehozása gombra.**
4. Adja meg a runbook nevét, és válassza ki annak [típusát.](automation-runbook-types.md) A runbook nevének betűvel kell kezdődnie, és tartalmazhat betűket, számokat, aláhúzásjeleket és kötőjeleket.
5. Kattintson a **Létrehozás gombra** a runbook létrehozásához és a szerkesztő megnyitásához.

### <a name="create-a-runbook-with-powershell"></a>Runbook létrehozása a PowerShell használatával

Az [Új-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) parancsmag segítségével üres [PowerShell-munkafolyamat-runbookot](automation-runbook-types.md#powershell-workflow-runbooks)hozhat létre. A `Type` paraméter segítségével adja meg a számára `New-AzAutomationRunbook`definiált runbook-típusok egyikét.

A következő példa bemutatja, hogyan hozhat létre egy új üres runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook"></a>Runbook importálása

Az Azure Automationben új runbookot hozhat létre Egy PowerShell-parancsfájl vagy PowerShell-munkafolyamat (**.ps1**), egy exportált grafikus runbook (**.graphrunbook**) vagy egy Python2-parancsfájl (**.py**) importálásával.  Meg kell adnia az importálás során létrehozott [runbook típusát,](automation-runbook-types.md) figyelembe véve a következő szempontokat.

* Importálhat egy **.ps1** fájlt, amely nem tartalmaz munkafolyamatot sem [PowerShell-runbookba,](automation-runbook-types.md#powershell-runbooks) sem [PowerShell-munkafolyamat-runbookba.](automation-runbook-types.md#powershell-workflow-runbooks) Ha importálja egy PowerShell-munkafolyamat-runbookba, a rendszer munkafolyamattá konvertálja. Ebben az esetben a megjegyzések szerepelnek a runbook a végrehajtott módosítások leírásához.

* Csak egy PowerShell-munkafolyamatot tartalmazó **.ps1** fájlt importálhat [egy PowerShell-munkafolyamat-runbookba.](automation-runbook-types.md#powershell-workflow-runbooks) Ha a fájl több PowerShell-munkafolyamatot tartalmaz, az importálás sikertelen lesz. Minden munkafolyamatot a saját fájljába kell mentenie, és külön kell importálnia.

* Ne importáljon PowerShell-munkafolyamatot tartalmazó **.ps1** fájlt [egy PowerShell-runbookba,](automation-runbook-types.md#powershell-runbooks)mert a PowerShell parancsfájlmotorja nem ismeri fel.

* **.graphrunbook** fájlt csak új grafikus [runbookba](automation-runbook-types.md#graphical-runbooks)importálhat. Ne feledje, hogy csak **.graphrunbook** fájlból hozhat létre grafikus runbookot.

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Runbook importálása fájlból az Azure Portalon

Az alábbi eljárással importálhat egy parancsfájlt az Azure Automationbe.

> [!NOTE]
> .ps1 fájlt csak a portálhasználatával importálhat egy **.ps1** fájlt egy PowerShell-munkafolyamat-runbookba.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A hubon válassza a **Runbookok** **a Folyamatautomatizálás** csoportban a runbookok listájának megnyitásához.
3. Kattintson **a Runbook importálása gombra.**
4. Kattintson a **Runbook-fájl** elemre, és jelölje ki az importálni kívánt fájlt.
5. Ha a **Név** mező engedélyezve van, lehetősége van a runbook nevének módosítására. A névnek betűvel kell kezdődnie, és tartalmazhat betűket, számokat, aláhúzásjeleket és kötőjeleket.
6. A [runbook típusa](automation-runbook-types.md) automatikusan kiválasztásra kerül, de a vonatkozó korlátozások figyelembevétele után módosíthatja a típust.
7. Kattintson **a Létrehozás gombra.** Az új runbook megjelenik az Automation-fiók runbookok listájában.
8. A [runbook futtatásához](#publishing-a-runbook) közzé kell tennie.

> [!NOTE]
> Miután importált egy grafikus runbookot vagy egy grafikus PowerShell-munkafolyamat-runbookot, átalakíthatja azt egy másik típusra. Azonban nem konvertálhatja az egyik grafikus runbookot szöveges runbookdá.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Runbook importálása parancsfájlból windows PowerShell használatával

Az [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) parancsmag használatával parancsfájlfájlt importálhat a PowerShell-munkafolyamat-runbook vázlataként. Ha a runbook már létezik, az `Force` importálás sikertelen lesz, hacsak nem használja a paramétert a parancsmaggal.

A következő példa bemutatja, hogyan importálhat egy parancsfájlt egy runbookba.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="testing-a-runbook"></a>Runbook tesztelése

Runbook tesztelése során a [piszkozat verziója](#publishing-a-runbook) végrehajtásra kerül, és az általa végrehajtott műveletek befejeződnek. Nem jön létre feladatelőzmények, de a [Kimeneti](automation-runbook-output-and-messages.md#output-stream) és [figyelmeztetési és](automation-runbook-output-and-messages.md#message-streams) hibaadatfolyamok megjelennek a Teszt kimeneti ablaktáblában. A [részletes adatfolyamba](automation-runbook-output-and-messages.md#message-streams) küldött üzenetek csak akkor jelennek meg a Kimenet `Continue`ablaktáblán, ha a [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) változó beállítása .

Annak ellenére, hogy a vázlat verzió futtatása folyamatban van, a runbook továbbra is normálisan hajtható végre, és a környezetben lévő erőforrásokkal szemben hajt végre műveleteket. Ebből az okból csak tesztelje a runbookok nem éles erőforrásokon.

Az egyes [runbook-típusok](automation-runbook-types.md) tesztelésére vonatkozó eljárás megegyezik. Nincs különbség a tesztelés között a szöveges szerkesztő és a grafikus szerkesztő az Azure Portalon.

1. Nyissa meg a runbook Vázlat verzióját a [szöveges szerkesztőben](automation-edit-textual-runbook.md) vagy a [grafikus szerkesztőben.](automation-graphical-authoring-intro.md)
1. Kattintson a **Teszt** gombra a Teszt lap megnyitásához.
1. Ha a runbook paramétereket tartalmaz, azok a bal oldali ablaktáblában jelennek meg, ahol a teszthez használandó értékeket adhat meg.
1. Ha azt szeretné futtatni a tesztet egy [hibrid Runbook-feldolgozó,](automation-hybrid-runbook-worker.md)módosítsa a **Futtatási beállítások** **hibrid feldolgozó,** és válassza ki a célcsoport nevét.  Ellenkező esetben tartsa meg az alapértelmezett **Azure-t** a teszt futtatásához a felhőben.
1. A teszt megkezdéséhez kattintson a **Start** gombra.
1. A Kimenet ablaktábla gombjaival leállíthatja vagy felfüggesztheti a [PowerShell-munkafolyamatot](automation-runbook-types.md#powershell-workflow-runbooks) vagy [grafikus](automation-runbook-types.md#graphical-runbooks) runbookot a tesztelés alatt. A runbook felfüggesztésekor a teszt a felfüggesztés előtt még végrehajtja az aktuális tevékenységet. A felfüggesztés után a runbookot megállíthatja vagy újraindíthatja.
1. Vizsgálja meg a kimenetet a runbook a Kimenet i.

## <a name="publishing-a-runbook"></a>Runbook közzététele

Amikor új runbookot hoz létre vagy importál, közzé kell tennie, mielőtt futtathatja volna. Az Azure Automation minden egyes runbookja rendelkezik egy piszkozatverzióval és egy közzétett verzióval. Kizárólag a Közzétett verziót lehet futtatni, és kizárólag a Piszkozat verzió szerkeszthető. A Közzétett verzióra nincsenek hatással a Piszkozat verzióban végrehajtott módosítások. Amikor a Piszkozat verziót elérhetővé kell tenni, közzé teheti azt, felülírva az aktuális közzétett verziót a Vázlat verzióval.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Runbook közzététele az Azure Portalon

1. Nyissa meg a runbookot az Azure Portalon.
2. Kattintson a **Szerkesztés** gombra.
3. Kattintson **a Közzététel,** majd az **Igen** gombra az ellenőrző üzenetre válaszul.

### <a name="publish-a-runbook-using-powershell"></a>Runbook közzététele a PowerShell használatával

A [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) parancsmag használatával közzétehet egy runbookot a Windows PowerShell használatával. A következő példa bemutatja, hogyan tehet közzé egy minta runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="scheduling-a-runbook-in-the-azure-portal"></a>Runbook ütemezése az Azure Portalon

A runbook közzététele után ütemezheti azt a művelethez.

1. Nyissa meg a runbookot az Azure Portalon.
2. Válassza **az Ütemezések** lehetőséget az **Erőforrások csoportban.**
3. Válassza **az Ütemezés hozzáadása**lehetőséget.
4. A Runbook ütemezése ablaktáblában válassza az **Ütemezés csatolása a runbookhoz**lehetőséget.
5. Válassza **az Új ütemezés létrehozása lehetőséget** az Ütemezés ablaktáblán.
6. Adjon meg egy nevet, leírást és egyéb paramétereket az Új ütemezés ablaktáblán. 
7. Az ütemezés létrehozása után jelölje ki, és kattintson az **OK**gombra. Most kell kapcsolni a runbook.
8. Keressen egy e-mailt a postaládájában, amely értesíti a runbook állapotáról.

## <a name="next-steps"></a>További lépések

* Ha meg szeretné tudni, hogyan élvezheti a Runbook- és A PowerShell-modulgyűjtemény előnyeit, olvassa el [az Azure Automation Runbook- és modulgalériáicímű témakört.](automation-runbook-gallery.md)
* Ha többet szeretne tudni a PowerShell és a PowerShell-munkafolyamat runbookok szöveges szerkesztővel való szerkesztéséről, olvassa el [a Szöveges runbookok szerkesztése az Azure Automationben című témakört.](automation-edit-textual-runbook.md)
* A grafikus runbookok szerkesztéséről a [Grafikus szerzői jog az Azure Automationben](automation-graphical-authoring-intro.md)című témakörben olvashat bővebben.
