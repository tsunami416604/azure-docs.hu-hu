---
title: Az Azure Automationben Azure-modulok frissítése
description: Ez a cikk bemutatja, hogyan frissítheti Azure Automation alapértelmezés szerint biztosított közös Azure PowerShell-modulok.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d8f57310cf4dbc2a27761fc44cfde6c8fd2791a2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005539"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Az Azure Automationben az Azure PowerShell-modulok frissítése

Az Automation-fiókját az Azure-modulok frissítése azt javasoljuk, hogy most használja a [frissítése az Azure-modulok runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), azaz most nyílt forráskódú. Emellett továbbra is használhatja a **Azure-modulok frissítése** a portálon az Azure-modulok frissítése gombra. A nyílt forráskódú runbook használatával kapcsolatos további tudnivalókért lásd: [frissítés az Azure-modulok nyílt forráskódú runbookkal](#open-source).

A leggyakrabban használt Azure PowerShell-modulok minden Automation-fiókban alapértelmezés szerint biztosított. Az Azure-csapat rendszeresen frissíti az Azure-modulokat. Az Automation-fiók már megadott oly módon, a modulok a fiókban lévő frissítéséhez, ha elérhetővé válik a portálról az új verziók.

Modulok rendszeresen frissülnek a termékcsoport, mert a csomagban foglalt parancsmagok módosítások előfordulhatnak. Ez a művelet lehetséges, hogy negatív hatással a runbookok módosítása, például egy paraméter átnevezése vagy teljes egészében a parancsmag elavulttá típusától függően.

Hatással lenne a runbookok és a folyamatok automatizálása azok elkerüléséhez tesztelése, és ellenőrizze a folytatás előtt. Ha erre a célra készült dedikált Automation-fiók nem rendelkezik, fontolja meg, hogy a runbookok fejlesztése során számos különféle célra teszteléséhez hozzon létre egyet. Ez a tesztelés iteratív változások, például a PowerShell-modulok frissítése tartalmaznia kell. 

Ha a parancsfájlok helyi fejleszt, azt javasoljuk, hogy ugyanazt a modul verziószámát helyileg is van az Automation-fiókját, ha a tesztelés célja annak biztosítása érdekében, hogy ugyanazt az eredményt fog kapni. Után az eredményeket a rendszer érvényesíti, és alkalmazta a szükséges módosításokat, továbbléphet a módosításokat az éles környezetbe.

> [!NOTE]
> Egy új Automation-fiók nem tartalmazhatja a legújabb modulok.

## <a name="open-source"></a>A nyílt forráskódú runbook Azure-modulok frissítése

Használatához a **Update-AutomationAzureModulesForAccount** runbookot, hogy az Azure-modulok frissítése töltse le a [frissítése az Azure-modulok runbook tárház](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) a Githubon. Ezután importálná az Automation-fiók vagy egy parancsfájlt futtató azt. Ehhez útmutatást megtalálható a [frissítés az Azure-modulok runbook tárház](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

### <a name="considerations"></a>Megfontolandó szempontok

Az alábbiakban néhány szempontot figyelembe kell venni, amikor ez a folyamat segítségével az Azure-modulok frissítése:

* Ha ez a forgatókönyv az eredeti nevén importálja `Update-AutomationAzureModulesForAccount`, felülírja a belső runbook ezen a néven. Ennek eredményeképpen az importált runbook akkor futtatható, ha a **frissítés az Azure-modulok** gomb leküldéssel, vagy ha a runbook meghívása az Automation-fiókhoz tartozó Azure Resource Manager API segítségével közvetlenül.

* Csak `Azure` és `AzureRM.*` modulok jelenleg támogatott. Az új [Az Azure PowerShell-modulok](/powershell/azure/new-azureps-module-az) még nem támogatottak.

* Kerülje a runbook indítása az Automation-fiókok, amelyek tartalmazzák Az modulok.

* Mielőtt elkezdené a forgatókönyv, ellenőrizze, hogy az Automation-fiók rendelkezik egy [Azure-beli futtató fiók hitelesítő adatait](manage-runas-account.md) létrehozott.

* Egy runbook helyett rendszeres PowerShell-parancsfájlt is használhatja ezt a kódot: csak jelentkezzen be Azure-bA a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) először a parancsot, majd át `-Login $false` a parancsfájlt.

* Használja ezt a runbookot a független felhőkben, használja a `AzureRmEnvironment` paraméter a megfelelő környezet átadása a runbookot.  Elfogadható értékek a következők **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, és **AzureUSGovernment**. Ezek az értékek használatával lekérhetők `Get-AzureRmEnvironment | select Name`. Egy érték nem adhatók át ezt a paramétert, ha a runbook alapértelmezés szerint az Azure nyilvános felhőbe **AzureCloud**

* Ha szeretné egy adott Azure PowerShell modul verziója helyett a legújabb elérhető használata a PowerShell-galériából, át ezeket a verziókat a választható `ModuleVersionOverrides` paraméterében a **Update-AutomationAzureModulesForAccount**runbook. Példák: a [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. Az Azure PowerShell-modul, amely nem szerepel a `ModuleVersionOverrides` paraméter frissülnek a legújabb verziója a PowerShell-galériából. Ha semmit nem kell a `ModuleVersionOverrides` paraméter, minden modulok a legújabb verziója a PowerShell-galériából a frissülnek. Ez a viselkedés megegyezik a **frissítés az Azure-modulok** gombra.

## <a name="update-azure-modules-in-the-azure-portal"></a>Az Azure Portalon az Azure-modulok frissítése

1. Az Automation-fiók a modulok oldalon lehetősége van nevű **frissítés az Azure-modulok**. Mindig engedélyezve van.<br><br> ![Modulok oldalon lehetőség az Azure-modulok frissítése](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Javasoljuk egy teszt annak érdekében, hogy az Automation-fiók frissítése az Azure-modulok frissítése előtt a meglévő parancsfájljait működnek megfelelően, az Azure-modulok frissítése előtt.
  >
  > A **frissítés az Azure-modulok** gomb érhető el csak a nyilvános felhőben. a nem érhető el a [szuverén régiók](https://azure.microsoft.com/global-infrastructure/). Használja a **Update-AutomationAzureModulesForAccount** runbookot, hogy az Azure-modulok frissítése. A letöltheti a [frissítés az Azure-modulok runbook tárház](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update). A nyílt forráskódú runbook használatával kapcsolatos további tudnivalókért lásd: [frissítés az Azure-modulok nyílt forráskódú runbookkal](#open-source).

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

## <a name="next-steps"></a>További lépések

Keresse fel a nyílt forráskódú [frissítés az Azure-modulok runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) , ha többet szeretne.
