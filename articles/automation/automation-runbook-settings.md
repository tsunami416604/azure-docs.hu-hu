---
title: "Runbookok beállításainak |} Microsoft Docs"
description: "Azure Automation és módosítsa őket az Azure portál és a Windows PowerShell használatával az egyes runbookok konfigurációs beállításokat ismerteti."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
ms.assetid: a726f20c-a952-48b8-88ee-36d76aa3ac61
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: bwren
ms.openlocfilehash: 527c40c4a0c5c9a0eb4745f542a8afcc63281416
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2018
---
# <a name="runbook-settings"></a>Runbook beállításai
Azure Automation minden egyes runbookja több beállítással, amelyek segítenek azonosítani és módosítani annak naplózási viselkedését rendelkezik. Egyes beállítások alábbiakban módosításukhoz szükséges eljárások leírását.

## <a name="settings"></a>Beállítások
### <a name="name-and-description"></a>Név és leírás
A runbook a neve nem módosítható, miután lett létrehozva. A leírás megadása nem kötelező, és legfeljebb 512 karakterből állhat.

### <a name="tags"></a>Címkék
A címkék lehetővé teszik önálló szavak és kifejezések runbook azonosításához hozzárendelni. Ha például a runbook elküldeni a [PowerShell-galériában](https://www.powershellgallery.com/), megadhatja az adott címkék szerepelnie kell a runbook kategóriák azonosításához. Megadhat egy runbook több címkék vesszővel elválasztva őket.

### <a name="logging"></a>Naplózás
Alapértelmezés szerint részletes és az állapot rekord nem szerepel a feladatelőzményekben. Módosíthatja az egyes runbookok beállításait, és ezeket a rekordokat naplózzák. Rekordokkal kapcsolatos további információkért lásd: [Runbook-kimenet és üzenetek](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Runbookok beállításainak módosítása

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Az Azure-portálon runbookok beállításainak módosítása
Az Azure-portálon az egyes runbookok beállításait módosíthatja a **beállítások** panel a runbook.

1. Válassza ki az Azure-portálon **Automation** és majd kattintson az automation-fiók nevét.
2. Válassza ki a **Runbookok** fülre.
3. Kattintson a nevére, a runbook, és a program kéri, hogy a beállítások panelről a runbook. Itt adja meg vagy módosíthatja a címkék, a forgatókönyv leírása, naplózási és nyomkövetési beállítások konfigurálása és problémák megoldásához támogatási eszközök eléréséhez.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>A Windows PowerShell segítségével a runbookok beállításainak módosítása
Használhatja a [Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) parancsmag használatával módosíthatja az egyes runbookok beállításait. Ha azt szeretné, több címkék adhatók meg, vagy megadhatja egy tömb vagy egy karakterláncot a címkék paraméter vesszővel tagolt értékeket tartalmazó. Az aktuális címkék kaphat a [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx).

Az alábbi Példaparancsok szemléltetik egy runbook tulajdonságainak beállításához. Ez a minta három címkék hozzáadása a meglévő címkét, és határozza meg, hogy részletes rekordok legyenek naplózva.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan hozhat létre és runbook-kimenet és a hiba üzeneteket beolvasni, lásd: [Runbook-kimenet és üzenetek](automation-runbook-output-and-messages.md) 
* Annak megértése, hogyan hozzáadása egy runbookot, amely már fejlesztette ki a közösségi vagy más forrásból, és a saját runbook létrehozásáról [létrehozása vagy egy Runbook importálása](automation-creating-importing-runbook.md) 

