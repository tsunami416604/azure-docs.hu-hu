---
title: "Az Azure Automationben Azure modulok frissítése |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan most már frissítheti az Azure Automationben alapértelmezés szerint biztosított közös Azure PowerShell-modulok."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2017
ms.author: magoedte
ms.openlocfilehash: 6bd259f3da1005228b8137415c30660221507909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Az Azure Automationben Azure PowerShell-modulok frissítése

A leggyakoribb Azure PowerShell-modulok minden Automation-fiókban alapértelmezés szerint biztosítottak.  Az Azure-csapat rendszeresen, frissíti az Azure modulok, így lehetővé teszi a modulok a fiók frissítését, ha új verziói érhetők el a portálról az általunk az Automation-fiókban.  

Modulok rendszeresen frissülnek a csoport által, mert negatívan befolyásolhatja a változás, például egy paraméter átnevezése vagy teljesen elavulttá parancsmag típusától függően a runbookok a belefoglalt parancsmagok módosítások alakulhat ki. A runbookok és a folyamatok automatizálásához azok érintő elkerüléséhez erősen ajánlott tesztelése, és a folytatás előtt ellenőrzi.  Ha erre a célra készült dedikált Automation-fiók nem rendelkezik, fontolja meg, hogy a teszteléssel számos különböző alkalmazási helyzetek és kombinációinak a runbookok fejlesztése során továbbá frissítése iteratív változásokra hozzon létre egyet a PowerShell-modulok.  Után az eredményeket a rendszer érvényesíti, és telepítette a szükséges módosításokat, folytassa a runbookokat, amely szükséges a módosítási áttelepítésének koordinációs, és hajtsa végre a frissítést, éles környezetben alább leírtak.     

## <a name="updating-azure-modules"></a>Az Azure modulok frissítése

1. A modulokban panelen található az Automation-fiók nincs lehetőség érhető el nevű **frissítés Azure modulok**.  Mindig engedélyezve van.<br><br> ![Modulok panel Azure modulok beállítás frissítése](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Kattintson a **frissítés Azure modulok** és az Ön számára jelenik meg, amely arra kéri, ha folytatni kívánja megerősítési értesítés.<br><br> ![Azure-modulok értesítési frissítése](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Kattintson a **Igen** , és a modul frissítési folyamat megkezdődik.  A frissítési folyamat veszi körülbelül 15 – 20 perc frissítése a következő modult:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Ha a modulok már naprakészek legyenek, majd a folyamat befejezi néhány másodpercen belül.  A frissítési folyamat befejezéséről értesítést fog kapni.<br><br> ![Azure-modulok frissítési állapotának frissítése](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation szolgáltatásbeli használni fog a legújabb modulok az Automation-fiók egy új ütemezett feladat futtatásakor.    

Ha használatával ezek Azure PowerShell-modulok parancsmagjait a runbookok Azure-erőforrások kezeléséhez, majd célszerű minden hónapban a frissítési folyamat végrehajtásához vagy így ahhoz, hogy biztosítsa, hogy rendelkezik-e a legújabb modulok.

## <a name="next-steps"></a>Következő lépések

* Integrációs modulok és az egyéni modulok további Automation integrálása más rendszerek, a szolgáltatások vagy a megoldások létrehozásával kapcsolatos további információkért lásd: [integrációs modulok](automation-integration-modules.md).

* Érdemes lehet forrás vezérlő integrációs [GitHub vállalati](automation-scenario-source-control-integration-with-github-ent.md) vagy [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) központi kezelésére, és szabályozhatja a automatizálási runbook és konfigurációs portfóliót kiadásaiban.  