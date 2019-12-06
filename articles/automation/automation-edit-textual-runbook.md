---
title: Szöveges runbookok szerkesztése Azure Automationban
description: Ez a cikk különböző eljárásokat tartalmaz a PowerShell és a PowerShell munkafolyamat-runbookok használatáról a Azure Automation a szöveges szerkesztő használatával.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a7eec0a7650f9c8e04a8d1062d32b6feb7d1d99
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850856"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Szöveges runbookok szerkesztése Azure Automationban

A Azure Automationban található szöveges szerkesztő használható a PowerShell- [runbookok](automation-runbook-types.md#powershell-runbooks) és a [PowerShell-munkafolyamat runbookok](automation-runbook-types.md#powershell-workflow-runbooks)szerkesztésére. Ez a más kód-szerkesztők, például az IntelliSense és a színkódolás jellemző funkcióit tartalmazza, és további speciális funkciókkal segíti a runbookok-hez hasonló erőforrások elérését. Ez a cikk részletesen ismerteti a különböző függvények a szerkesztővel való elvégzésének lépéseit.

A szöveges szerkesztő tartalmaz egy funkciót, amely a parancsmagok, az eszközök és a gyermek runbookok kódját szúrja be egy runbook. A kód beírása helyett választhat az elérhető erőforrások listájából, és beillesztheti a megfelelő kódot a runbook.

Azure Automation minden runbook két verziója, piszkozata és közzététele van. Szerkessze a runbook Piszkozat verzióját, és tegye közzé azt, hogy végrehajtható legyen. A Közzétett verzió nem szerkeszthető. További információ: [Runbook közzététele](manage-runbooks.md#publish-a-runbook).

A [grafikus runbookok](automation-runbook-types.md#graphical-runbooks)való munkavégzéshez lásd: [grafikus létrehozás Azure Automationban](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Runbook szerkesztése a Azure Portal

A következő eljárással megnyithatja a runbook szerkesztésre a szöveges szerkesztőben.

1. A Azure Portal válassza ki az Automation-fiókját.
2. A **folyamat automatizálása**területen válassza a **runbookok** lehetőséget a runbookok listájának megnyitásához.
3. Válassza ki a szerkeszteni kívánt runbook, majd kattintson a **Szerkesztés** gombra.
4. Szerkessze a runbook.
5. A Szerkesztés befejezéséhez kattintson a **Mentés** gombra.
6. Kattintson a közzététel elemre, ha azt szeretné, hogy a rendszer a runbook legújabb vázlatos verzióját **tegye** közzé.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Parancsmag beszúrása egy runbook

1. A szöveges szerkesztő Vászonján vigye a kurzort arra a helyre, ahová a parancsmagot helyezni kívánja.
2. Bontsa ki a **parancsmagok** csomópontot a könyvtár vezérlőben.
3. Bontsa ki a használni kívánt parancsmagot tartalmazó modult.
4. Kattintson a jobb gombbal a beszúrandó parancsmagra, majd válassza a **Hozzáadás a vászonhoz**lehetőséget. Ha a parancsmag egynél több paramétert tartalmaz, a rendszer az alapértelmezett készletet adja hozzá. A parancsmag kibontásával másik paramétert is kijelölhet.
5. A rendszer beszúrja a parancsmag kódját a paraméterek teljes listájába.
6. Adja meg a megfelelő értéket a kapcsos zárójelek között, < > a szükséges paraméterekhez. Távolítsa el a nem szükséges paramétereket.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>A gyermek runbook tartozó kód beszúrása runbook

1. A szöveges szerkesztő Vászonján vigye a kurzort arra a helyre, ahová a [gyermek runbook](automation-child-runbooks.md)kódot kívánja helyezni.
2. Bontsa ki a **runbookok** csomópontot a könyvtár vezérlőben.
3. Kattintson a jobb gombbal a beszúrandó runbook, és válassza a **Hozzáadás a vászonhoz**lehetőséget.
4. A gyermek runbook kódja minden runbook-paraméterhez be van beszúrva.
5. Cserélje le a helyőrzőket az egyes paraméterek megfelelő értékeivel.

### <a name="to-insert-an-asset-into-a-runbook"></a>Eszköz beszúrása runbook

1. A szöveges szerkesztő Vászonján vigye a kurzort arra a helyre, ahová a gyermek runbook kódot kívánja helyezni.
2. Bontsa ki az **eszközök** csomópontot a könyvtár vezérlőben.
3. Bontsa ki a csomópontot a kívánt eszköz típusához.
4. Kattintson a jobb gombbal a beszúrandó eszközre, majd válassza a **Hozzáadás a vászonhoz**lehetőséget. [Változó eszközök](automation-variables.md)esetén válassza a **"változó beolvasása" lehetőséget a vászonhoz** , vagy **adja hozzá a "változó beállítása" kifejezést a vászonhoz** , attól függően, hogy a változót szeretné-e beolvasni vagy beállítani.
5. Az eszköz kódját a rendszer beszúrja a runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Azure Automation runbook szerkesztése a Windows PowerShell használatával

Ha a Windows PowerShell segítségével szeretne szerkeszteni egy runbook, használja az Ön által választott szerkesztőt, és mentse azt egy `.ps1` fájlba. Az [export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) parancsmaggal lekérheti a runbook tartalmát, majd az [import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) parancsmaggal lecserélheti a meglévő runbook-tervezetet a módosítottra.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának lekérése a Windows PowerShell használatával

A következő mintaparancsok bemutatják, hogyan kérhető le egy runbook parancsfájlja és menthető egy parancsfájlba. Ez a példa a vázlatverzió lekérését mutatja be. A runbook közzétett verzióját is le lehet kérni, bár ez a verzió nem módosítható.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának módosítása a Windows PowerShell használatával

Az alábbi példaparancsok szemléltetik egy Runbook létező tartalmának felülírását egy parancsprogram tartalmával. Ez ugyanaz a mintavételi eljárás, mint [egy Runbook Windows PowerShell-lel való importálása parancsfájlból](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Runbookok kezelése Azure Automation](manage-runbooks.md)
* [A PowerShell-munkafolyamat megismerése](automation-powershell-workflow.md)
* [Grafikus szerzői műveletek Azure Automation](automation-graphical-authoring-intro.md)
* [Tanúsítványok](automation-certificates.md)
* [Kapcsolatok](automation-connections.md)
* [Hitelesítő adatok](automation-credentials.md)
* [Ütemezések](automation-schedules.md)
* [Változók](automation-variables.md)

