---
title: Az Azure Automationben Azure modulok frissítése
description: Ez a cikk ismerteti, hogyan most már frissítheti az Azure Automationben alapértelmezés szerint biztosított közös Azure PowerShell-modulok.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: f1f7068de1781d1cc66a412752f6fd99d603a6be
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Az Azure Automationben Azure PowerShell-modulok frissítése

A leggyakoribb Azure PowerShell-modulok minden Automation-fiókban alapértelmezés szerint biztosítottak. Az Azure-csapat rendszeresen, frissíti az Azure modulok, így az Automation-fiók rendelkezésre állnak a modulok a fiók frissíteni, ha elérhetők új verziók a portálról úgy.  

Modulok rendszeresen frissülnek a csoport által, mert negatívan befolyásolhatja a változás, például egy paraméter átnevezése vagy teljesen elavulttá parancsmag típusától függően a runbookok a belefoglalt parancsmagok módosítások alakulhat ki. A runbookok és a folyamatok automatizálásához azok érintő elkerülése ajánlott tesztelése, és a folytatás előtt ellenőrzi. Ha erre a célra készült dedikált Automation-fiók nem rendelkezik, fontolja meg, hogy a teszteléssel számos különböző alkalmazási helyzetek és kombinációinak a runbookok fejlesztése során továbbá frissítése iteratív változásokra hozzon létre egyet a PowerShell-modulok. Után az eredményeket a rendszer érvényesíti, és telepítette a szükséges módosításokat, összehangolása a runbookokat, amely szükséges a módosítási áttelepítésének folytatásához, és hajtsa végre a következő frissítés termelési leírtak szerint.

## <a name="updating-azure-modules"></a>Az Azure modulok frissítése

1. Az Automation-fiók modulok lapján, a rendszer felajánlja a nevű **frissítés Azure modulok**. Mindig engedélyezve van.<br><br> ![Azure-modulok beállítás modulok oldalon frissítése](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Kattintson a **frissítés Azure modulok**, megerősítési értesítés jelenik meg, amely engedélyt kér a folytatáshoz.<br><br> ![Azure-modulok értesítési frissítése](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Kattintson a **Igen** és a modul frissítési folyamat megkezdése. A frissítési folyamat veszi körülbelül 15 – 20 perc frissítése a következő modult:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Ha a modulok már naprakészek, a folyamat néhány másodpercen belül befejeződik. Ha a frissítési folyamat befejeződik, értesítés jelenik meg.<br><br> ![Azure-modulok frissítési állapotának frissítése](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation szolgáltatásbeli használja a legújabb modulok az Automation-fiók egy új ütemezett feladat futtatásakor.    

Ezek az Azure PowerShell-modul a parancsmagok használata a runbookokban, ha szeretne a frissítési folyamat minden hónapban lefusson, vagy ezért győződjön meg arról, hogy rendelkezik-e a legújabb modulok.

## <a name="next-steps"></a>További lépések

* Integrációs modulok és az egyéni modulok további Automation integrálása más rendszerek, a szolgáltatások vagy a megoldások létrehozásával kapcsolatos további információkért lásd: [integrációs modulok](automation-integration-modules.md).

* Érdemes lehet forrás vezérlő integrációs [GitHub vállalati](automation-scenario-source-control-integration-with-github-ent.md) vagy [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) központi kezelésére, és szabályozhatja a automatizálási runbook és konfigurációs portfóliót kiadásaiban.  