---
title: A Azure Automation Azure PowerShell moduljainak frissítése
description: Ez a cikk azt ismerteti, hogyan frissítheti a Azure Automation alapértelmezés szerint elérhető általános Azure PowerShell modulokat.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769668"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>A Azure Automation Azure PowerShell moduljainak frissítése

Az Automation-fiókban lévő Azure-modulok frissítéséhez az Azure- [modulok frissítése runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)kell használnia, amely nyílt forráskódúként érhető el. Ha szeretné megkezdeni az Azure-modulok frissítését az **Update-AutomationAzureModulesForAccount** runbook, töltse le az Azure-modulok frissítése a githubon a [runbook-adattárból](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) . Ezután importálhatja az Automation-fiókjába, vagy parancsfájlként futtathatja azt. A runbook az Automation-fiókban való importálásával kapcsolatos további információkért lásd: [Runbook importálása](manage-runbooks.md#importing-a-runbook).

A leggyakoribb PowerShell-modulok alapértelmezés szerint az egyes Automation-fiókokban vannak megadva. Az Azure csapata rendszeresen frissíti az Azure-modulokat. Ezért ahhoz, hogy a modulok naprakészen maradjanak az Automation-fiókokban, az [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook kell használnia.

Mivel a modulokat a termékcsoport rendszeresen frissíti, a befoglalt parancsmagokkal végzett módosítások is előfordulhatnak. Ezek a változások, például a paraméterek átnevezése vagy a parancsmagok teljesen elavulttá váltása negatív hatással lehet a runbookok.

Ha el szeretné kerülni a runbookok és az általuk automatizálható folyamatok hatását, tesztelje és érvényesítse a továbblépés előtt. Ha nem rendelkezik erre a célra szánt dedikált Automation-fiókkal, érdemes lehet létrehozni egy másikat, hogy tesztelje a runbookok fejlesztésének számos különböző forgatókönyvét. Ennek a tesztelésnek tartalmaznia kell az ismétlődő módosításokat, például a PowerShell-modulok frissítését.

Ha helyileg fejleszti a parancsfájlokat, akkor azt javasoljuk, hogy az Automation-fiókjában lévő, azonos verziójú modulokat a tesztelés során is ugyanazzal az eredménnyel kapja meg. Az eredmények ellenőrzése és a szükséges módosítások alkalmazása után áthelyezheti a módosításokat az éles környezetbe.

> [!NOTE]
> Előfordulhat, hogy egy új Automation-fiók nem tartalmazza a legújabb modulokat.

> [!NOTE]
> Nem fogja tudni törölni a globális modulokat, amelyek az Automation által biztosított modulok.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Megfontolandó szempontok

Az alábbiakban néhány megfontolandó szempontot figyelembe kell vennie, ha a jelen cikk használatával frissíti az Azure-modulokat:

* A jelen cikkben ismertetett runbook alapértelmezés szerint támogatja az Azure, a AzureRM és az az modulok frissítését. Tekintse át az [Azure-modulok frissítése runbook](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) további információt az az. Automation-modulok ezzel a runbook való frissítéséről. Az az Automation-fiókjában található modulok használatakor figyelembe kell vennie további fontos tényezőket. További információért lásd: [modulok kezelése Azure Automationban](shared-resources/modules.md).

* A runbook megkezdése előtt győződjön meg arról, hogy az Automation-fiókja rendelkezik egy Azure-beli [futtató fiók hitelesítő adatainak](manage-runas-account.md) létrehozásával.

* Ezt a kódot normál PowerShell-parancsfájlként használhatja runbook helyett: csak jelentkezzen be az Azure-ba a AzAccount [parancsmag használatával](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) , majd továbbítsa `-Login $false` a szkriptnek.

* Ha ezt a runbook a szuverén felhőkön szeretné használni, `AzEnvironment` használja a paramétert a megfelelő környezet átadásához a runbook.  Elfogadható értékek: AzureCloud (Azure Public Cloud), AzureChinaCloud, AzureGermanCloud és AzureUSGovernment. Ezek az értékek a használatával `Get-AzEnvironment | select Name`kérhetők le. Ha nem adott meg értéket ehhez a parancsmaghoz, a runbook alapértelmezés szerint a AzureCloud értéket adja meg.

* Ha a PowerShell-galéria elérhető legújabb modul helyett egy adott Azure PowerShell modul verzióját szeretné használni, adja át ezeket a verziókat az **Update-AutomationAzureModulesForAccount** runbook `ModuleVersionOverrides` választható paraméterének. Példákat a [Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook talál. Azure PowerShell a `ModuleVersionOverrides` paraméterben nem említett modulok a PowerShell-Galéria legújabb moduljaival frissülnek. Ha nem adja meg a `ModuleVersionOverrides` paramétert, a rendszer az összes modult frissíti a PowerShell-Galéria legújabb moduljaival. Ez a viselkedés ugyanaz, mint az **Azure-modulok frissítése** gomb.

## <a name="next-steps"></a>További lépések

A nyílt forráskódú [Azure-modulok frissítésével](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) kapcsolatos további információkért tekintse meg a runbook.
