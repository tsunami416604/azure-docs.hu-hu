---
title: Szöveges runbookok szerkesztése az Azure Automationben
description: Ez a cikk különböző eljárásokat tartalmaz a PowerShell és a PowerShell-munkafolyamat runbookok az Azure Automation ben a szöveges szerkesztő használatával.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ef7db244057bc8b3b2e4d938b9f3bdd11c7940a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406028"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Szöveges runbookok szerkesztése az Azure Automationben

Az Azure Automation tankönyvszerkesztője a [PowerShell runbookok](automation-runbook-types.md#powershell-runbooks) és a [PowerShell-munkafolyamat runbookok](automation-runbook-types.md#powershell-workflow-runbooks)szerkesztésére használható. Ez a szerkesztő a tipikus funkciók más kódszerkesztők, mint például az IntelliSense. Azt is színkódolás további speciális funkciókkal, hogy segítsen a futáskönyvek közös erőforrások elérésében. 

A szövegszerkesztő tartalmaz egy olyan funkciót, amely kód beszúrására parancsmagok, eszközök és a gyermek runbookok egy runbook. Ahelyett, hogy saját maga írja be a kódot, választhat a rendelkezésre álló erőforrások listájából, és a szerkesztő beilleszti a megfelelő kódot a runbookba.

Az Azure Automation minden egyes runbookja két verzióval rendelkezik: vázlat és közzétett. Szerkesztheti a Runbook Piszkot, majd közzéteheti, hogy végrehajtható legyen. A Közzétett verzió nem szerkeszthető. További információt a Runbook közzététele című [témakörben talál.](manage-runbooks.md#publishing-a-runbook)

Ez a cikk részletes lépéseket tartalmaz a különböző funkciók végrehajtásához ezzel a szerkesztővel. Ezek nem vonatkoznak a [grafikus runbookok](automation-runbook-types.md#graphical-runbooks). A runbookokkal való munkáról az Azure Automation grafikus szerzői című [témakörben](automation-graphical-authoring-intro.md)található.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="editing-a-runbook-with-the-azure-portal"></a>Runbook szerkesztése az Azure Portalon

Az alábbi eljárással megnyithat egy runbookot szerkesztésre a szöveges szerkesztőben.

1. Az Azure Portalon válassza ki az Automation-fiókot.
2. A **FOLYAMATAUTOMATIZÁLÁS csoportban**válassza a **Runbookok** lehetőséget a runbookok listájának megnyitásához.
3. Válassza ki a szerkesztni kívánt runbookot, majd kattintson **a Szerkesztés gombra.**
4. A runbook szerkesztése.
5. Kattintson a **Mentés gombra,** ha a módosítások befejeződtek.
6. Kattintson **a Közzététel** gombra, ha közzé szeretné tenni a runbook legújabb vázlatverzióját.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Parancsmag beszúrása runbookba

1. A szövegszerkesztő vásznán helyezze a kurzort oda, ahová a parancsmamot el szeretné helyezni.
2. Bontsa ki a **Parancsmagok** csomópontot a Könyvtár vezérlőben.
3. Bontsa ki a használni a parancsmamot tartalmazó modult.
4. Kattintson a jobb gombbal a parancsmag nevére a beszúráshoz, és válassza **a Hozzáadás a vászonhoz parancsot.** Ha a parancsmaghoz egynél több paraméter van beállítva, a szerkesztő hozzáadja az alapértelmezett készletet. A parancsmag kibontásával másik paraméterkészletet is választhat.
5. Ne feledje, hogy a parancsmag kódja a paraméterek teljes listájával együtt kerül beillesztésre.
6. Adjon meg megfelelő értéket a szögtartókkal (<>) körülvett érték helyett a szükséges paraméterekhez. Távolítsa el azokat a paramétereket, amelyekre nincs szüksége.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Gyermek runbook kódjának beszúrása runbookba

1. A szövegszerkesztő vásznán helyezze a kurzort oda, ahová a [gyermek runbook](automation-child-runbooks.md)kódját el szeretné helyezni.
2. Bontsa ki a **Runbook-csomópontot** a Könyvtár vezérlőben.
3. Kattintson a jobb gombbal a runbookra a beszúráshoz, és válassza **a Hozzáadás a vászonhoz parancsot.**
4. A gyermek runbook kódja bármely runbook-paraméter helyőrzőivel bekerül.
5. Cserélje le a helyőrzőket az egyes paraméterekmegfelelő értékeire.

### <a name="insert-an-asset-into-a-runbook"></a>Eszköz beszúrása runbookba

1. A vászon vezérlőben a szöveges szerkesztő, helyezze a kurzort, ahol el szeretné helyezni a kódot a gyermek runbook.
2. Bontsa ki az **Eszközök** csomópontot a Könyvtár vezérlőben.
3. Bontsa ki a kívánt eszköztípus csomópontja.
4. Kattintson a jobb gombbal a beszúráshoz az eszköz nevére, és válassza **a Hozzáadás a vászonhoz parancsot.** Változó [eszközök](automation-variables.md)esetén válassza a **"Változó hozzáadása" lehetőséget a vászonra** vagy **a "Változó hozzáadása" lehetőséget a vászonhoz,** attól függően, hogy be szeretné-e szerezni vagy be szeretné állítani a változót.
5. Vegye figyelembe, hogy az eszköz kódja bekerül a runbookba.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Azure Automation-runbook szerkesztése a Windows PowerShell használatával

Runbook windows PowerShell használatával történő szerkesztéséhez használja a választott szerkesztőt, és mentse a runbookot egy **.ps1** fájlba. Az [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) parancsmag segítségével lekérheti a runbook tartalmát. Az [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) parancsmag segítségével lecserélheti a meglévő vázlat runbookot a módosítottra.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának lekérése a Windows PowerShell használatával

A következő mintaparancsok bemutatják, hogyan kérhető le egy runbook parancsfájlja és menthető egy parancsfájlba. Ez a példa a vázlatverzió lekérését mutatja be. A runbook közzétett verziója is lehívható, bár ez a verzió nem módosítható.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának módosítása a Windows PowerShell használatával

A következő mintaparancsok bemutatják, hogyan cserélheti le a runbookok meglévő tartalmát egy parancsfájl tartalmára. Ez ugyanaz a mintaeljárás, mint a [Runbook windowsPowerShell rendszerű parancsfájlból történő importálásához.](manage-runbooks.md#importing-a-runbook)

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Runbookok kezelése az Azure Automationben](manage-runbooks.md)
* [A PowerShell-munkafolyamat tanulása](automation-powershell-workflow.md)
* [Grafikus szerzői jog az Azure Automationben](automation-graphical-authoring-intro.md)
* [Tanúsítványok](automation-certificates.md)
* [Kapcsolatok](automation-connections.md)
* [Hitelesítő adatok](automation-credentials.md)
* [Ütemezések](automation-schedules.md)
* [Változók](automation-variables.md)
* [A PowerShell-parancsmagok leírása](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
