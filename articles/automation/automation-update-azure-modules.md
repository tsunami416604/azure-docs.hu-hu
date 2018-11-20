---
title: Az Azure Automationben Azure-modulok frissítése
description: Ez a cikk bemutatja, hogyan frissítheti Azure Automation alapértelmezés szerint biztosított közös Azure PowerShell-modulok.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 31d794aa1dccddc8018e9413c9743a0abe13f4ac
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52160195"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Az Azure Automationben az Azure PowerShell-modulok frissítése

A leggyakrabban használt Azure PowerShell-modulok minden Automation-fiókban alapértelmezés szerint biztosított. Az Azure-csapat rendszeresen frissíti az Azure-modulokat, ezért az Automation-fiókban a modulok a fiókban lévő frissítéséhez, ha elérhetővé válik a portálról az új verziók úgy vannak megadva.

A termékcsoport modulok rendszeresen frissülnek, mert módosítások előfordulhatnak változás, például egy paraméter átnevezése vagy teljes egészében a parancsmag elavulttá típusától függően a runbookok negatív hatással lehet a csomagban foglalt parancsmagok. Hatással lenne a runbookok és a folyamatok automatizálása azok elkerüléséhez ajánlott, hogy tesztelje, és a folytatás előtt. Ha erre a célra készült dedikált Automation-fiók nem rendelkezik, érdemes lehet létrehozni egy, így tesztelheti számos különféle célra és permutációk a runbookok fejlesztése során ezen kívül, iteratív változások, például frissítése a PowerShell-modulok. Után az eredményeket a rendszer érvényesíti, és telepítette a szükséges módosításokat, összehangolása a módosítást igénylő runbookokat migrálásának folytatásához, és hajtsa végre a következő frissítés termelési leírtak szerint.

> [!NOTE]
> Egy új Automation-fiók nem tartalmazhatja a legújabb modulok.

## <a name="updating-azure-modules"></a>Az Azure-modulok frissítése

1. Az Automation-fiók a modulok oldalon lehetősége van nevű **frissítés az Azure-modulok**. Mindig engedélyezve van.<br><br> ![Modulok oldalon lehetőség az Azure-modulok frissítése](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Javasoljuk egy teszt annak érdekében, hogy az Automation-fiók frissítése az Azure-modulok frissítése előtt a meglévő parancsfájljait működnek megfelelően, az Azure-modulok frissítése előtt.
  >
  > A **frissítés az Azure-modulok** gomb érhető el csak a nyilvános felhőben. A nem érhető el a [szuverén régiók](https://azure.microsoft.com/global-infrastructure/). Lásd: [alternatív módszereket a modulok frissítése](#alternative-ways-to-update-your-modules) szakaszban talál.


2. Kattintson a **frissítés az Azure-modulok**, a megerősítési értesítés jelenik meg, amely engedélyt kér a folytatáshoz.<br><br> ![Értesítés az Azure-modulok frissítése](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Kattintson a **Igen** és a modul frissítési folyamat megkezdése. A frissítési folyamat nagyjából 15 – 20 percet vesz igénybe a következő modulok frissítése:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Ha a modul már naprakész, majd a folyamat befejeződése után pár másodpercen belül. Ha a frissítési folyamat befejeződött, értesítést kap.<br><br> ![Azure-modulok frissítése állapotának frissítése](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    A .NET core AzureRm-modulok (AzureRm.*. Core) nem támogatottak az Azure Automationben, és nem lehet importálni.

> [!NOTE]
> Az Azure Automation használja a legújabb modulokat az Automation-fiók egy új ütemezett feladatot a futása során.  

Ezeket az Azure PowerShell-modulok a parancsmagok használata a runbookokban, szeretné-e a frissítési folyamat minden hónapban lefusson, vagy ezért győződjön meg arról, hogy rendelkezik-e a legújabb modulok. Azure Automation az AzureRunAsConnection kapcsolat a modulok frissítésekor, ha az egyszerű szolgáltatás lejárt, vagy már nem létezik az előfizetés szintjén, a modul frissítése a hitelesítés sikertelen lesz.

## <a name="alternative-ways-to-update-your-modules"></a>Alternatív módszereket a modulok frissítése

Ahogy említettük, a **frissítés az Azure-modulok** gomb nem érhető el a szuverén felhőkben, mert csak érhető el a globális Azure-felhőben. Ez az az oka, hogy az a PowerShell-galériából, az Azure PowerShell-modulok legújabb verziója esetleg nem fog a jelenleg telepített ezek a felhők erőforrás-kezelő szolgáltatásokat.

A modulok frissítése továbbra is végezhető importálásával a [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook az Automation-fiókját, és azt futtatja. Ez a forgatókönyv segítségével frissítheti az Azure-modulok, de még nem feltétlenül kompatibilis a mi van üzembe helyezése az Azure-környezet. Győződjön meg, hogy a megfelelő verzió szükséges az egyes modulok, győződjön meg arról, hogy az Azure Automation továbbra is kezelheti az erőforrásokat kell. Az Azure Automation nem kezelheti, és nyomon követheti a telepített szolgáltatások verzióit.

Használja a `AzureRmEnvironment` paraméter a megfelelő környezet átadása a runbookot.  Elfogadható értékek a következők **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, és **AzureUSGovernmentCloud**. Ezek az értékek használatával szerezhető `Get-AzureRmEnvironment | select Name`. Ha nem ad egy értéket ehhez a paraméterhez, a runbook alapértelmezés szerint az Azure nyilvános felhőbe **AzureCloud**

Ha szeretné egy adott Azure PowerShell modul verziója helyett a legújabb elérhető használata a PowerShell-galériából, át ezeket a verziókat a választható `ModuleVersionOverrides` paraméterében a **Update-AzureModule** runbook. Példák: a [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook. Az Azure PowerShell-modulokat, amelyek nem szerepelnek a `ModuleVersionOverrides` paraméter frissülnek a legújabb verziója a PowerShell-galériából. Ha semmi átadott a `ModuleVersionOverrides` paraméter, minden modul frissülnek a legújabb verziója a PowerShell-galériából, a azaz viselkedését a **frissítés az Azure-modulok** gombra.

## <a name="next-steps"></a>További lépések

* Integrációs modulok és egyéni modulok további Automation integrálása más rendszerek, szolgáltatások és megoldások létrehozásával kapcsolatos további információkért lásd: [integrációs modulok](automation-integration-modules.md).

* Érdemes lehet forrás vezérlő integrációs [GitHub Enterprise-zal](automation-scenario-source-control-integration-with-github-ent.md) vagy [Azure DevOps](automation-scenario-source-control-integration-with-vsts.md) központilag és kiadások, az Automation-runbook és konfigurációs portfólió szabályozására.  
