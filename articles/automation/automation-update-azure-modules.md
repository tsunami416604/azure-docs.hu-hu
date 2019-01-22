---
title: Az Azure Automationben Azure-modulok frissítése
description: Ez a cikk bemutatja, hogyan frissítheti Azure Automation alapértelmezés szerint biztosított közös Azure PowerShell-modulok.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c0636a3e1fa50f90c68393aea910f36d38d8eaf5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437267"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Az Azure Automationben az Azure PowerShell-modulok frissítése

A leggyakrabban használt Azure PowerShell-modulok minden Automation-fiókban alapértelmezés szerint biztosított. Az Azure-csapat rendszeresen frissíti az Azure-modulokat. Az Automation-fiók már megadott oly módon, a modulok a fiókban lévő frissítéséhez, ha elérhetővé válik a portálról az új verziók.

> [!NOTE]
> Az új [Az Azure PowerShell modul](/powershell/azure/new-azureps-module-az?view=azurermps-6.13.0) nem támogatottak az Azure Automationben.

Modulok rendszeresen frissülnek a termékcsoport, mert a csomagban foglalt parancsmagok módosítások előfordulhatnak. Ez a művelet lehetséges, hogy negatív hatással a runbookok módosítása, például egy paraméter átnevezése vagy teljes egészében a parancsmag elavulttá típusától függően. Hatással lenne a runbookok és a folyamatok automatizálása azok elkerüléséhez tesztelése, és ellenőrizze a folytatás előtt. Ha erre a célra készült dedikált Automation-fiók nem rendelkezik, fontolja meg, hogy a runbookok fejlesztése során számos különféle célra teszteléséhez hozzon létre egyet. Ez a tesztelés iteratív változások, például a PowerShell-modulok frissítése tartalmaznia kell. Ha a parancsfájlok helyi fejleszt, ajánlott ugyanazt a modul verziószámát helyileg is van az Automation-fiókját, ha a tesztelés célja annak biztosítása érdekében, hogy ugyanazt az eredményt fog kapni. Után az eredményeket a rendszer érvényesíti, és alkalmazta a szükséges módosításokat, továbbléphet a módosításokat az éles környezetbe.

> [!NOTE]
> Egy új Automation-fiók nem tartalmazhatja a legújabb modulok.

## <a name="updating-azure-modules"></a>Az Azure-modulok frissítése

1. Az Automation-fiók a modulok oldalon lehetősége van nevű **frissítés az Azure-modulok**. Mindig engedélyezve van.<br><br> ![Modulok oldalon lehetőség az Azure-modulok frissítése](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Javasoljuk egy teszt annak érdekében, hogy az Automation-fiók frissítése az Azure-modulok frissítése előtt a meglévő parancsfájljait működnek megfelelően, az Azure-modulok frissítése előtt.
  >
  > A **frissítés az Azure-modulok** gomb érhető el csak a nyilvános felhőben. a nem érhető el a [szuverén régiók](https://azure.microsoft.com/global-infrastructure/). Lásd: [alternatív módszereket a modulok frissítése](#alternative-ways-to-update-your-modules) szakaszban talál.

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

    Ha a modul már naprakész, majd a folyamat befejeződése után pár másodpercen belül. A frissítési folyamat befejezését követően, értesítést kap.<br><br> ![Azure-modulok frissítése állapotának frissítése](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    A .NET core AzureRm-modulok (AzureRm.*. Core) nem támogatottak az Azure Automationben, és nem lehet importálni.

> [!NOTE]
> Az Azure Automation használja a legújabb modulokat az Automation-fiók egy új ütemezett feladatot a futása során.  

Ezeket az Azure PowerShell-modulok a parancsmagok használata a runbookokban, szeretné-e a frissítési folyamat minden hónapban lefusson, vagy ezért győződjön meg arról, hogy rendelkezik-e a legújabb modulok. Az Azure Automation használja a `AzureRunAsConnection` kapcsolat hitelesítéséhez a modulok frissítésekor. Ha az egyszerű szolgáltatás lejárt, vagy már nem található az előfizetés szintjén, a modul frissítés sikertelen lesz.

## <a name="alternative-ways-to-update-your-modules"></a>Alternatív módszereket a modulok frissítése

Ahogy említettük, a **frissítés az Azure-modulok** a szuverén felhőkben gomb nem érhető el, mert csak érhető el a globális Azure-felhőben. Ez az az oka, hogy az a PowerShell-galériából, az Azure PowerShell-modulok legújabb verziója esetleg nem fog a jelenleg telepített, ezek a felhők a Resource Manager-erőforrást.

Importálja, majd futtassa a [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbookot, hogy próbálja meg frissíteni az Azure-modulokat az Automation-fiókban. Általában célszerű egyszerre az összes Azure-modulok frissítése. De ez a folyamat sikertelen lehet, ha a verzió importálása a katalógusból kívánt nem lesznek kompatibilisek a jelenleg telepített, a cél Azure-környezethez az Azure-szolgáltatások. Ez előfordulhat, hogy ellenőrizze a runbook paramétereinek megadott modulok kompatibilis verzióját.

Használja a `AzureRmEnvironment` paraméter a megfelelő környezet átadása a runbookot.  Elfogadható értékek a következők **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, és **AzureUSGovernment**. Ezek az értékek használatával lekérhetők `Get-AzureRmEnvironment | select Name`. Egy érték nem adhatók át ezt a paramétert, ha a runbook alapértelmezés szerint az Azure nyilvános felhőbe **AzureCloud**

Ha szeretné egy adott Azure PowerShell modul verziója helyett a legújabb elérhető használata a PowerShell-galériából, át ezeket a verziókat a választható `ModuleVersionOverrides` paraméterében a **Update-AzureModule** runbook. Példák: a [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook. Az Azure PowerShell-modul, amely nem szerepel a `ModuleVersionOverrides` paraméter frissülnek a legújabb verziója a PowerShell-galériából. Ha semmit nem kell a `ModuleVersionOverrides` paraméter, minden modulok a legújabb verziója a PowerShell-galériából a frissülnek. Ez a viselkedés megegyezik a **frissítés az Azure-modulok** gombra.

## <a name="next-steps"></a>További lépések

* Integrációs modulok és egyéni modulok további Automation integrálása más rendszerek, szolgáltatások és megoldások létrehozásával kapcsolatos további információkért lásd: [integrációs modulok](automation-integration-modules.md).


