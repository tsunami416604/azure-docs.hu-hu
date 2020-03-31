---
title: Azure-modulok frissítése az Azure Automationben
description: Ez a cikk bemutatja, hogyan frissítheti most az Azure Automationben alapértelmezés szerint biztosított általános Azure PowerShell-modulokat.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3d7eaae452f307b350c111452b819576cf7f17e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420486"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure PowerShell-modulok frissítése az Azure Automationben

Az Automation-fiók Ban az Azure-modulok frissítéséhez kell használnia az [Azure-modulok frissítése runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), amely nyílt forráskódúként érhető el. Az **Update-AutomationAzureModulesForAccount** runbook használatával az Azure-modulok frissítéséhez töltse le a GitHubOn lévő [Azure-modulok frissítése runbook tárházból.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Ezután importálhatja az Automation-fiókba, vagy parancsfájlként futtathatja. Ha tudni szeretné, hogyan importálhat egy runbookot az Automation-fiókban, olvassa el [a Runbook importálása című témakört.](manage-runbooks.md#import-a-runbook)

A leggyakoribb AzureRM PowerShell-modulok alapértelmezés szerint minden Automation-fiókban vannak biztosítva. Az Azure-csapat rendszeresen frissíti az Azure-modulokat, ezért naprakészen tarthatja az [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbookot az Automation-fiókok moduljainak frissítéséhez.

Mivel a termékcsoport rendszeresen frissíti a modulokat, a mellékelt parancsmagok esetén változások következhetnek be. Ez a művelet negatívan befolyásolhatja a runbookok típusától függően a változás, például egy paraméter átnevezése vagy egy parancsmag teljes mértékben elavult.

A runbookok és az általuk automatizált folyamatok befolyásolásának elkerülése érdekében tesztelje és ellenőrizze a folytatás előtt. Ha nem rendelkezik erre a célra szánt dedikált Automation-fiókkal, fontolja meg egy, hogy a runbookok fejlesztése során számos különböző forgatókönyvet tesztelhet. Ez a tesztelés tartalmaznia kell az iteratív módosításokat, például a PowerShell-modulok frissítése.

Ha helyileg fejleszti a parancsfájlokat, javasoljuk, hogy az Automation-fiókban lévő, helyileg lévő modulverziókkal rendelkezzen a tesztelés során, hogy megbizonyosodjon arról, hogy ugyanazokat az eredményeket kapja. Miután az eredményeket érvényesítették, és alkalmazta a szükséges módosításokat, áthelyezheti a módosításokat a termelésbe.

> [!NOTE]
> Előfordulhat, hogy egy új Automation-fiók nem tartalmazza a legújabb modulokat.

> [!NOTE]
> Nem fogja tudni törölni a globális modulokat - azokat a modulokat, amelyeket az Automation a dobozból biztosít.

## <a name="considerations"></a>Megfontolandó szempontok

Az alábbi szempontokat kell figyelembe venni az Azure-modulok frissítéséhez a folyamat használatakor:

* Ez a runbook alapértelmezés szerint támogatja az **Azure-** és **Az AzureRm-modulok** frissítését. Ez a runbook támogatja az **Az** modulok frissítését is. Tekintse át az [Azure-modulok frissítése runbook README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) további információkért a modulok frissítése `Az` ezzel a runbook. További fontos tényezőket is figyelembe kell vennie `Az` az Automation-fiók moduljainak használatakor, hogy többet megtudjon, olvassa [el az Az modulok használata az Automation-fiókban című témakört.](az-modules.md)

* A runbook elindítása előtt győződjön meg arról, hogy az Automation-fiók rendelkezik egy [Azure Run As fiók hitelesítő adatok](manage-runas-account.md) létre.

* Ezt a kódot normál PowerShell-parancsfájlként használhatja runbook helyett: először csak jelentkezzen be az `-Login $false` [Azure-ba a Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) paranccsal, majd adja át a parancsfájlnak.

* A runbook a szuverén felhők, `AzureRmEnvironment` használja a paramétert a megfelelő környezetet a runbook.  Elfogadható értékek: **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**és **AzureUSGovernment**. Ezek az értékek a `Get-AzureRmEnvironment | select Name`használatával lehívhatók. Ha nem ad át értéket ennek a paraméternek, a runbook alapértelmezés szerint az Azure nyilvános **felhőben azurecloud**

* Ha a PowerShell-galériában elérhető legújabb verzió helyett egy adott Azure PowerShell-modulverziót szeretne használni, adja át ezeket a verziókat az `ModuleVersionOverrides` **Update-AutomationAzureModulesForAccount** runbook választható paraméterének. Példák: [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. A `ModuleVersionOverrides` paraméterben nem említett Azure PowerShell-modulok frissülnek a PowerShell-galériában a legújabb modulverziókkal. Ha nem ad `ModuleVersionOverrides` át semmit a paraméternek, az összes modul frissül a PowerShell-galériában a legújabb modulverziókkal. Ez a viselkedés megegyezik az **Azure-modulok frissítése** gombbal.

## <a name="next-steps"></a>További lépések

További információért látogasson el a nyílt forráskódú [Frissítési Azure-modulok runbookjára.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)
