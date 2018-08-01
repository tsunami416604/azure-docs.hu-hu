---
title: Az Azure Automation forgatókönyv beállításai
description: Az Azure Automation és módosítása az Azure portal és a Windows PowerShell-lel runbook konfigurációs beállításait ismerteti.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2174135aaf2e16907f16f38c1df1ec002b3083fd
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391435"
---
# <a name="runbook-settings"></a>Runbook beállításai
Az Azure Automationben minden runbook rendelkezik több beállítások, amelyek segítenek azonosítani és módosítani annak naplózási viselkedését. Ezek a beállítások leírását alább módosításukkal követ.

## <a name="settings"></a>Beállítások
### <a name="name-and-description"></a>Név és leírás
Runbook nevével létrehozásukat követően nem módosítható. A leírás megadása nem kötelező, és legfeljebb 512 karakter lehet.

### <a name="tags"></a>Címkék
A címkék lehetővé teszik önálló szavak és kifejezések, hogy könnyebben azonosítsák a runbook rendelhet hozzá. Például, amikor közzétételre küld be egy runbookot, hogy a [PowerShell-galériából](https://www.powershellgallery.com/), mely kategóriák szerepelnie kell a runbook azonosításához adott címkék adhatók meg. Egy runbook több címkét is megadhat vesszővel válassza el egymástól elválasztva őket.

### <a name="logging"></a>Naplózás
Alapértelmezés szerint részletes és az állapot rekord nem szerepel a feladatelőzményekben. Módosíthatja a beállításokat egy adott forgatókönyvhöz, ezeket a rekordokat naplózzák. Ezeket a rekordokat a további információkért lásd: [Runbook-kimenet és üzenetek](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Runbookok beállításainak módosítása

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Az Azure Portalon runbookok beállításainak módosítása
Az Azure Portalon az egyes runbookok beállításait módosíthatja a **beállítások** a runbook paneljét.

1. Az Azure Portalon válassza ki a **Automation** és kattintson az automation-fiók nevére.
2. Válassza ki a **Runbookok** fülre.
3. Kattintson a runbook nevére, és átirányítjuk a beállítások panelről a runbook. Itt adja meg vagy módosíthatja a címkék és a runbook leírása, naplózás és nyomkövetés beállításainak konfigurálása, és segítséget nyújt a problémák megoldása támogatási eszközök elérése.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>A Windows PowerShell-lel runbookok beállításainak módosítása
Használhatja a [Set-azurermautomationrunbook parancsmag](https://msdn.microsoft.com/library/mt603786.aspx) parancsmaggal módosíthatja az egyes runbookok beállításait. Ha azt szeretné, több címkék adhatók meg, vagy megadhat egy tömböt vagy karakterláncot egyetlen a Tags paraméter vesszővel tagolt értékeket tartalmazó. Megtekintheti az aktuális címkéket azokkal a [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx).

Az alábbi Példaparancsok szemléltetik egy runbook tulajdonságainak beállítását. Ez a példa három címkét ad hozzá a meglévő címkéket, és meghatározza, hogy részletes rekordok legyenek naplózva.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan hozhat létre és kimenetet és üzeneteket beolvasni a runbookok, lásd: [Runbook-kimenet és üzenetek](automation-runbook-output-and-messages.md) 
* Megtudhatja, hogyan hozzáadni egy runbookot, amely már fejlesztette a Közösség vagy más forrásból, vagy a saját runbook létrehozásáról [létrehozása vagy egy Runbook importálása](automation-creating-importing-runbook.md) 

