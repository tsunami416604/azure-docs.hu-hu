---
title: Az Azure Automationben Azure-modulok frissítése
description: Ez a cikk bemutatja, hogyan frissítheti Azure Automation alapértelmezés szerint biztosított közös Azure PowerShell-modulok.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd7c268008afbd87e855516d5834676423272646
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67146717"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Az Azure Automationben az Azure PowerShell-modulok frissítése

Frissítése az Azure-modulokat az Automation-fiókot kell használnia: a [frissítése az Azure-modulok runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), amely nyílt forráskódként érhető el. Használatához a **Update-AutomationAzureModulesForAccount** runbookot, hogy az Azure-modulok frissítése töltse le a [frissítése az Azure-modulok runbook tárház](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) a Githubon. Ezután importálná az Automation-fiók vagy egy parancsfájlt futtató azt. Az Automation-fiókban lévő runbook importálása kapcsolatban lásd: [runbook importálása](manage-runbooks.md#import-a-runbook).

A leggyakoribb AzureRM PowerShell-modulok minden Automation-fiókban alapértelmezés szerint biztosított. Az Azure csapata rendszeresen frissíti az Azure-modulokat, ezért naprakészen tartásához érdemes használni a [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbookot, hogy a modulokat az Automation-fiókok frissítése.

Modulok rendszeresen frissülnek a termékcsoport, mert a csomagban foglalt parancsmagok módosítások előfordulhatnak. Ez a művelet lehetséges, hogy negatív hatással a runbookok módosítása, például egy paraméter átnevezése vagy teljes egészében a parancsmag elavulttá típusától függően.

Hatással lenne a runbookok és a folyamatok automatizálása azok elkerüléséhez tesztelése, és ellenőrizze a folytatás előtt. Ha erre a célra készült dedikált Automation-fiók nem rendelkezik, fontolja meg, hogy a runbookok fejlesztése során számos különféle célra teszteléséhez hozzon létre egyet. Ez a tesztelés iteratív változások, például a PowerShell-modulok frissítése tartalmaznia kell.

Ha a parancsfájlok helyi fejleszt, azt javasoljuk, hogy ugyanazt a modul verziószámát helyileg is van az Automation-fiókját, ha a tesztelés célja annak biztosítása érdekében, hogy ugyanazt az eredményt fog kapni. Után az eredményeket a rendszer érvényesíti, és alkalmazta a szükséges módosításokat, továbbléphet a módosításokat az éles környezetbe.

> [!NOTE]
> Egy új Automation-fiók nem tartalmazhatja a legújabb modulok.

## <a name="considerations"></a>Megfontolandó szempontok

Az alábbiakban néhány szempontot figyelembe kell venni, amikor ez a folyamat segítségével az Azure-modulok frissítése:

* Ez a runbook támogatja frissítése a **Azure** és **AzureRm** modulok alapértelmezés szerint. Ez a runbook támogatja frissítése a **Az** modulokat is. Tekintse át a [frissítés az Azure-modulok runbook információs](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) frissítésével kapcsolatos további információk `Az` a runbook-modulok. További fontos tényező használatakor figyelembe kell a `Az` modulokat az Automation-fiókban, további információért lásd: [modulokat az Automation-fiók használata Az](az-modules.md).

* Mielőtt elkezdené a forgatókönyv, ellenőrizze, hogy az Automation-fiók rendelkezik egy [Azure-beli futtató fiók hitelesítő adatait](manage-runas-account.md) létrehozott.

* Egy runbook helyett rendszeres PowerShell-parancsfájlt is használhatja ezt a kódot: csak jelentkezzen be Azure-bA a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) először a parancsot, majd át `-Login $false` a parancsfájlt.

* Használja ezt a runbookot a független felhőkben, használja a `AzureRmEnvironment` paraméter a megfelelő környezet átadása a runbookot.  Elfogadható értékek a következők **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, és **AzureUSGovernment**. Ezek az értékek használatával lekérhetők `Get-AzureRmEnvironment | select Name`. Egy érték nem adhatók át ezt a paramétert, ha a runbook alapértelmezés szerint az Azure nyilvános felhőbe **AzureCloud**

* Ha szeretné egy adott Azure PowerShell modul verziója helyett a legújabb elérhető használata a PowerShell-galériából, át ezeket a verziókat a választható `ModuleVersionOverrides` paraméterében a **Update-AutomationAzureModulesForAccount**runbook. Példák: a [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. Az Azure PowerShell-modul, amely nem szerepel a `ModuleVersionOverrides` paraméter frissülnek a legújabb verziója a PowerShell-galériából. Ha semmit nem kell a `ModuleVersionOverrides` paraméter, minden modulok a legújabb verziója a PowerShell-galériából a frissülnek. Ez a viselkedés megegyezik a **frissítés az Azure-modulok** gombra.

## <a name="known-issues"></a>Ismert problémák

Frissítése az AzureRM-modulokat: Automation-fiók, amely egy erőforráscsoportot a numerikus kezdetű névvel rendelkező 0-val rendelkező egy ismert probléma van. Az Azure-modulokat: Automation-fiók frissítéséhez egy alfanumerikus nevű erőforráscsoportban kell lennie. Erőforráscsoportok 0-tól induló numerikus nevek nem tudnak AzureRM-modulok frissítése most.

## <a name="next-steps"></a>További lépések

Keresse fel a nyílt forráskódú [frissítés az Azure-modulok runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) , ha többet szeretne.
