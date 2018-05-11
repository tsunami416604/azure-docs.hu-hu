---
title: Azure Automation runbookjai szöveges szerkesztése
description: Ez a cikk biztosít a különböző eljárásokkal a PowerShell és a PowerShell-munkafolyamati forgatókönyvek az Azure Automationben használata a szöveges szerkesztővel.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b5c12507ccf1ba290c4063f4e4e0e0704eec4b9b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Azure Automation runbookjai szöveges szerkesztése

Az Azure Automationben szöveges szerkesztő segítségével szerkesztése [PowerShell-forgatókönyvek](automation-runbook-types.md#powershell-runbooks) és [PowerShell munkafolyamat-forgatókönyvekről](automation-runbook-types.md#powershell-workflow-runbooks). Azt az egyéb kód szerkesztőt, például a intellisense és további speciális funkciók színkódolás segítséget nyújt a runbookok közös erőforrások eléréséhez a tipikus funkcióit. Ez a cikk nyújt a részletes lépéseket a szerkesztővel különböző funkcióihoz.

A szöveges szerkesztőjének egyik funkciójával, a parancsmagok, eszközök és a gyermekrunbookok kód beszúrása a runbookba. Ahelyett, hogy írja be a kódját magát, válassza ki a rendelkezésre álló erőforrások listája, és rendelkezik a megfelelő kódot a runbook beszúrt.

Azure Automation összes runbookjának két verziója van, vázlat és egy közzétett. A runbook vázlatverzióját szerkesztheti, és majd közzétenni ahhoz végrehajtható legyen. A közzétett verzió nem szerkeszthető. Lásd: [runbook közzététele](automation-creating-importing-runbook.md#publishing-a-runbook) további információt.

Történő együttműködésre [grafikus forgatókönyvek](automation-runbook-types.md#graphical-runbooks), lásd: [grafikus készítése az Azure Automationben](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Az Azure portálon runbook szerkesztése

A következő eljárás segítségével nyissa meg szerkesztésre a szöveges szerkesztőben egy runbookot.

1. Az Azure-portálon válassza ki az automation-fiók.
2. A **folyamat**, jelölje be **Runbookok** runbookok listájának megnyitásához.
3. Válassza ki a runbookot szeretne szerkessze, majd kattintson a **szerkesztése** gombra.
4. Hajtsa végre a szükséges módosításokat.
5. Kattintson a **mentése** amikor a módosítások nem fejeződik.
6. Kattintson a **közzététel** Ha azt szeretné, hogy közzé kell tenni a runbook legfrissebb vázlatverzióját.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>A parancsmag beszúrása a runbookba

1. A vásznon, a szöveges szerkesztő vigye a kurzort hol szeretné elhelyezni a parancsmag.
2. Bontsa ki a **parancsmagok** csomópont található a könyvtárban.
3. Bontsa ki a használni kívánt parancsmag tartalmazó modult.
4. Kattintson jobb gombbal a Beszúrás, és válassza ki a parancsmag **vászonra Hozzáadás**. Ha a parancsmag beállítása egynél több paraméterrel rendelkezik, az alapértelmezés szerint lesz hozzáadva. A parancsmag segítségével válassza ki egy másik paraméter bővítheti is.
5. A parancsmag a kódját a paraméterek teljes listája a egészül ki.
6. Adjon meg egy megfelelő értéket a csúcsos zárójelek <> minden kötelező paraméterhez között feltüntetett adattípus helyett. Távolítsa el a felesleges paramétereket.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>A gyermekrunbook kód beszúrása a runbookba

1. A vásznon, a szöveges szerkesztő, vigye a kurzort ahová kódját a [gyermekrunbook](automation-child-runbooks.md).
2. Bontsa ki a **Runbookok** csomópont található a könyvtárban.
3. Kattintson jobb gombbal a Beszúrás, és válassza ki a runbook **vászonra Hozzáadás**.
4. A runbook paramétereket bármely helyőrzőit a kódot, hogy a gyermekrunbook egészül ki.
5. A helyőrzőket cserélje le minden paraméterhez megfelelő értéket.

### <a name="to-insert-an-asset-into-a-runbook"></a>Egy eszköz beszúrása a runbookba

1. A vásznon, a szöveges szerkesztő helyezze a mutatót a kódot, hogy a gyermekrunbook ahová.
2. Bontsa ki a **eszközök** csomópont található a könyvtárban.
3. Bontsa ki a kívánt eszköz típusú csomópont.
4. Kattintson jobb gombbal a Beszúrás, és válassza ki az eszköz **vászonra Hozzáadás**. A [változó eszközök](automation-variables.md), válassza **hozzáadása "Változó beolvasása" vászonra** vagy **hozzáadása "Változó beállítása" vászonra** attól függően, hogy a vagy állítsa be a változót.
5. Az eszköz a kód bekerülnek a runbookot.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>A Windows PowerShell egy Azure Automation-runbook szerkesztése

A Windows PowerShell-lel runbook szerkesztése a szerkesztővel az Ön által választott, és mentse azt egy .ps1 kiterjesztésű fájlba. Használhatja a [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) parancsmagot a runbook tartalmának beolvasásához, majd [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) parancsmaggal lecserélheti a meglévő vázlatszintű vázlatot a módosítottra runbook.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>A Windows PowerShell használatával Runbook tartalmának beolvasása

Az alábbi Példaparancsok szemléltetik egy runbook parancsprogramjának beolvasását, és mentse a parancsfájlt. Ebben a példában a rendszer lekéri a vázlatként megjelölt verziót. Akkor is a runbook közzétett verziójának beolvasásához, bár ez a verzió nem módosítható.

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

$runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
$runbookContent = $runbookDefinition.Content

Out-File -InputObject $runbookContent -FilePath $scriptPath
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>A Windows PowerShell használatával Runbook tartalmának módosítása

Az alábbi Példaparancsok szemléltetik egy runbook létező tartalmának lecserélése a parancsfájl. Vegye figyelembe, hogy ez a minta eljárásnak és a [runbook importálása parancsfájlból a Windows PowerShell-lel](automation-creating-importing-runbook.md).

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName
```

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Létrehozása vagy egy Azure Automation forgatókönyv importálása](automation-creating-importing-runbook.md)
* [Learning PowerShell munkafolyamat](automation-powershell-workflow.md)
* [Grafikus készítése az Azure Automationben](automation-graphical-authoring-intro.md)
* [Tanúsítványok](automation-certificates.md)
* [Kapcsolatok](automation-connections.md)
* [Hitelesítő adatok](automation-credentials.md)
* [Ütemezések](automation-schedules.md)
* [Változók](automation-variables.md)
