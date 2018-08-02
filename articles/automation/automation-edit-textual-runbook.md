---
title: Az Azure Automationben szöveges runbookok szerkesztése
description: Ebben a cikkben különböző eljárások az Azure Automation PowerShell és a PowerShell munkafolyamat forgatókönyvekkel való használatához a szöveges szerkesztő használatával.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b17fc82d6e9cbda6ffa94ac2ee5c97835b089a7e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399719"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Az Azure Automationben szöveges runbookok szerkesztése

Az Azure Automationben a szöveges szerkesztő segítségével szerkesztése [PowerShell-forgatókönyvek](automation-runbook-types.md#powershell-runbooks) és [PowerShell-munkafolyamati runbookok](automation-runbook-types.md#powershell-workflow-runbooks). A tipikus szolgáltatásokat az egyéb kód szerkesztők segítséget nyújt a runbookok közös erőforrások eléréséhez, például az intellisense, és további speciális funkciókat Színkódok azt. Ez a cikk részletesen bemutatja a lépéseket a szerkesztő a különböző funkciók végrehajtásához.

A szöveges szerkesztő tartalmaz egy funkciót a parancsmagok, eszközök és gyermek runbookok kód beszúrása a runbookba. Ahelyett, hogy írja be a kódot, saját magának, elérhető erőforrások listájából válassza ki, és a megfelelő kódot szúr be a runbook rendelkezik.

Az Azure Automationben minden runbook két verziója van, vázlat és egy közzétett. A runbook vázlatverzióját szerkesztése, és közzé kell tennie, is végrehajthatók. A közzétett verzió nem szerkeszthető. Lásd: [runbook közzététele](automation-creating-importing-runbook.md#publishing-a-runbook) további információt.

Dolgozunk [grafikus Runbookok](automation-runbook-types.md#graphical-runbooks), lásd: [grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Az Azure Portallal runbook szerkesztése

A következő eljárással egy runbookot, szerkessze az a szöveges szerkesztő megnyitásához.

1. Az Azure Portalon válassza ki az automation-fiók.
2. A **FOLYAMATAUTOMATIZÁLÁS**válassza **Runbookok** forgatókönyvek listájának megnyitásához.
3. Válassza ki a runbookot szeretne szerkessze, majd kattintson a **szerkesztése** gombra.
4. Hajtsa végre a szükséges módosításokat.
5. Kattintson a **mentése** amikor a módosítások befejeződtek.
6. Kattintson a **közzététel** Ha azt szeretné, hogy közzé kell tenni a runbook legfrissebb vázlatverzióját.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>A parancsmag beszúrása a runbookba

1. A szöveges szerkesztő a vásznon vigye a kurzort, ahol szeretné helyezni a parancsmagot.
2. Bontsa ki a **parancsmagok** csomópont a könyvtár vezérlőben.
3. Bontsa ki a modult, amely tartalmazza a használni kívánt parancsmag.
4. Kattintson a jobb gombbal a Beszúrás, és válassza ki a parancsmag **adja hozzá a vászonhoz**. Ha a parancsmag több paramétert, majd az alapértelmezett készlet fog bővülni. A parancsmagot, amely egy másik paraméterkészletet válassza ki is bővítheti.
5. A parancsmag a kódját a paraméterek teljes listája a egészül ki.
6. Adjon meg egy megfelelő értéket a szükséges paraméterek a zárójelek között <> foglalt adattípus helyett. Távolítsa el a nem szükséges paramétereket.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Gyermek runbook kód beszúrása a runbookba

1. A szöveges szerkesztő a vásznon, vigye a kurzort hol érdemes a kódját a [gyermekrunbook](automation-child-runbooks.md).
2. Bontsa ki a **Runbookok** csomópont a könyvtár vezérlőben.
3. Kattintson a jobb gombbal a Beszúrás, és válassza ki a runbook **adja hozzá a vászonhoz**.
4. A kód a gyermek runbook a runbook-paraméterek bármely helyőrzőkkel egészül ki.
5. Minden paraméterhez megfelelő értéket cserélje le a helyőrzőket.

### <a name="to-insert-an-asset-into-a-runbook"></a>Egy eszköz beszúrása a runbookba

1. A szöveges szerkesztő a vásznon vigye a kurzort, ahol a kódot, hogy a gyermekrunbook szeretné.
2. Bontsa ki a **eszközök** csomópont a könyvtár vezérlőben.
3. Bontsa ki a kívánt eszköz típusú csomópontot.
4. Kattintson jobb gombbal a Beszúrás, és válassza ki az eszköz **adja hozzá a vászonhoz**. A [változó adategységek](automation-variables.md), válassza **hozzáadása "Változó beolvasása" vászonalapú** vagy **hozzáadása "Változó beállítása" vászonalapú** attól függően, hogy, vagy állítsa be a változót.
5. A kód az eszköz a rendszer beszúrta a runbookot.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Egy Windows PowerShell-lel az Azure Automation-runbook szerkesztése

A Windows PowerShell-lel runbook szerkesztése a választott szerkesztővel, és mentse azt egy .ps1 kiterjesztésű fájlba. Használhatja a [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) a runbook tartalmának beolvasásához, majd [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) parancsmaggal lecserélheti a meglévő runbook-vázlat a módosított egy.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>A Windows PowerShell-lel Runbook tartalmának beolvasása

Az alábbi Példaparancsok szemléltetik egy runbook parancsprogramjának beolvasását, és mentse azt egy parancsfájl. Ebben a példában a rendszer lekéri a vázlatként megjelölt verziót. Akkor is, a runbook közzétett verziójának lekérése, bár ez a verzió nem módosítható.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>A Windows PowerShell-lel Runbook tartalmának módosítása

Az alábbi Példaparancsok szemléltetik egy runbook meglévő tartalma egy parancsfájl tartalmával. Vegye figyelembe, hogy ennek az eljárásnak minta [runbook importálása parancsfájlból a Windows PowerShell-lel](automation-creating-importing-runbook.md).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Létrehozása vagy importálása az Azure Automation-runbook](automation-creating-importing-runbook.md)
* [PowerShell-munkafolyamat megismerése](automation-powershell-workflow.md)
* [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md)
* [Tanúsítványok](automation-certificates.md)
* [Kapcsolatok](automation-connections.md)
* [Hitelesítő adatok](automation-credentials.md)
* [Ütemezések](automation-schedules.md)
* [Változók](automation-variables.md)
