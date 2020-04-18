---
title: Azure-modulok frissítése az Azure Automationben
description: Ez a cikk bemutatja, hogyan frissítheti most az Azure Automationben alapértelmezés szerint biztosított általános Azure PowerShell-modulokat.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: ba926ccbb069c8620259514e0a64c56957529a0f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617474"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure PowerShell-modulok frissítése az Azure Automationben

Az Automation-fiókBan az Azure-modulok frissítéséhez az [Azure-modulok frissítése runbookot kell használnia,](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)amely nyílt forrásként érhető el. Az **Update-AutomationAzureModulesForAccount** runbook használatával az Azure-modulok frissítéséhez töltse le a GitHubOn lévő [Azure-modulok frissítése runbook tárházból.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Ezután importálhatja az Automation-fiókba, vagy parancsfájlként futtathatja. Ha tudni szeretné, hogyan importálhat egy runbookot az Automation-fiókban, olvassa el [a Runbook importálása című témakört.](manage-runbooks.md#importing-a-runbook)

A leggyakoribb PowerShell-modulok alapértelmezés szerint minden Automation-fiókban vannak biztosítva. Az Azure-csapat rendszeresen frissíti az Azure-modulokat. Ezért az Automation-fiókok moduljainak naprakészen tartásához használja az [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbookot.

Mivel a termékcsoport rendszeresen frissíti a modulokat, a mellékelt parancsmagok esetén változások következhetnek be. Ezek a módosítások, például egy paraméter átnevezése vagy egy parancsmag teljes deprecating negatívan befolyásolhatja a runbookok.

A runbookok és az általuk automatizált folyamatok befolyásolásának elkerülése érdekében tesztelje és ellenőrizze a folytatás előtt. Ha nem rendelkezik erre a célra szánt dedikált Automation-fiókkal, fontolja meg egy, hogy a runbookok fejlesztése során számos különböző forgatókönyvet tesztelhet. Ez a tesztelés iteratív módosításokat, például a PowerShell-modulok frissítése.

Ha helyileg fejleszti a parancsfájlokat, javasoljuk, hogy az Automation-fiókban lévő, helyileg lévő modulverziókkal rendelkezzen a tesztelés során, hogy megbizonyosodjon arról, hogy ugyanazokat az eredményeket kapja. Miután az eredményeket érvényesítették, és alkalmazta a szükséges módosításokat, áthelyezheti a módosításokat a termelésbe.

> [!NOTE]
> Előfordulhat, hogy egy új Automation-fiók nem tartalmazza a legújabb modulokat.

> [!NOTE]
> Nem fogja tudni törölni a globális modulokat, amelyek olyan modulok, amelyeket az Automation a dobozból biztosít.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) 

## <a name="considerations"></a>Megfontolandó szempontok

A cikk azure-moduljainak frissítésekének használatakor az alábbi szempontokat kell figyelembe venni:

* A cikkben ismertetett runbook alapértelmezés szerint támogatja az Azure, az AzureRM és az Az modulok frissítését. Tekintse át az [Azure-modulok frissítése runbook README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) további információkért az Az.Automation-modulok frissítéséről ezzel a runbookkal. Vannak további fontos tényezők, amelyeket figyelembe kell vennie az Az modulok automatizálási fiókban való használatakor. További információ: [Az modulok használata az Automation-fiókban.](az-modules.md)

* A runbook elindítása előtt győződjön meg arról, hogy az Automation-fiók rendelkezik egy [Azure Run As fiók hitelesítő adatok](manage-runas-account.md) létre.

* Ezt a kódot normál PowerShell-parancsfájlként használhatja runbook helyett: csak jelentkezzen be az Azure-ba `-Login $false` a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) parancsmag használatával, majd adja át a parancsfájlnak.

* A runbook a szuverén felhők, `AzEnvironment` használja a paramétert a megfelelő környezetet a runbook.  Az elfogadható értékek az AzureCloud (azure nyilvános felhő), az AzureChinaCloud, az AzureGermanCloud és az AzureUSGovernment. Ezek az értékek a `Get-AzEnvironment | select Name`használatával beolvashatók. Ha nem adja át ezt a parancsmagot, a runbook alapértelmezés szerint az AzureCloud.

* Ha a PowerShell-galériában elérhető legújabb modul helyett egy adott Azure PowerShell-modulverziót `ModuleVersionOverrides` szeretne használni, adja át ezeket a verziókat az **Update-AutomationAzureModulesForAccount** runbook választható paraméterének. Példák: [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. A `ModuleVersionOverrides` paraméterben nem említett Azure PowerShell-modulok frissülnek a PowerShell-galériában a legújabb modulverziókkal. Ha nem ad `ModuleVersionOverrides` át semmit a paraméternek, az összes modul frissül a PowerShell-galériában a legújabb modulverziókkal. Ez a viselkedés megegyezik az **Azure-modulok frissítése** gombbal.

## <a name="next-steps"></a>További lépések

További információért látogasson el a nyílt forráskódú [Frissítési Azure-modulok runbookjára.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)
