---
title: Azure-modulok frissítése Azure Automation
description: Ez a cikk azt ismerteti, hogyan frissítheti a Azure Automation alapértelmezés szerint elérhető általános Azure PowerShell modulokat.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 23475fb77210eeea0568bb996529c81458db9c6c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382762"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>A Azure Automation Azure PowerShell moduljainak frissítése

Az Automation-fiókban lévő Azure-modulok frissítéséhez az Azure- [modulok frissítése runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)kell használnia, amely nyílt forráskódúként érhető el. Ha szeretné megkezdeni az Azure-modulok frissítését az **Update-AutomationAzureModulesForAccount** runbook, töltse le az Azure-modulok frissítése a githubon a [runbook-adattárból](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) . Ezután importálhatja az Automation-fiókjába, vagy parancsfájlként futtathatja azt. A runbook az Automation-fiókban való importálásával kapcsolatos további információkért lásd: [Runbook importálása](manage-runbooks.md#import-a-runbook).

A leggyakoribb AzureRM PowerShell-modulok alapértelmezés szerint minden Automation-fiókban elérhetők. Az Azure csapata rendszeresen frissíti az Azure-modulokat, ezért az [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook segítségével frissítheti az Automation-fiókokban lévő modulokat.

Mivel a modulokat a termékcsoport rendszeresen frissíti, a befoglalt parancsmagokkal végzett módosítások is előfordulhatnak. Ez a művelet negatív hatással lehet a runbookok a változás típusától függően, például egy paraméter átnevezésével vagy a parancsmag teljes érvénytelenítésével.

Ha el szeretné kerülni a runbookok és az általuk automatizálható folyamatok hatását, tesztelje és érvényesítse a továbblépés előtt. Ha nem rendelkezik erre a célra szánt dedikált Automation-fiókkal, érdemes lehet létrehozni egy másikat, hogy tesztelje a runbookok fejlesztésének számos különböző forgatókönyvét. A tesztelésnek olyan iterációs módosításokat kell tartalmaznia, mint például a PowerShell-modulok frissítése.

Ha helyileg fejleszti a parancsfájlokat, akkor azt javasoljuk, hogy az Automation-fiókjában lévő, azonos verziójú modulokat a tesztelés során is ugyanazzal az eredménnyel kapja meg. Az eredmények ellenőrzése és a szükséges módosítások alkalmazása után áthelyezheti a módosításokat az éles környezetbe.

> [!NOTE]
> Előfordulhat, hogy egy új Automation-fiók nem tartalmazza a legújabb modulokat.

> [!NOTE]
> Nem fogja tudni törölni a globális modulokat – az Automation által biztosított modulok.

## <a name="considerations"></a>Megfontolandó szempontok

Az alábbiakban néhány megfontolandó szempontot figyelembe kell venni, ha ezt a folyamatot használja az Azure-modulok frissítéséhez:

* Ez a runbook alapértelmezés szerint támogatja az **Azure** -és **AzureRm** -modulok frissítését. Ez a runbook az az **modulok** frissítését is támogatja. A modulok ezzel a runbook való frissítésével `Az` kapcsolatos további információkért tekintse meg az Azure- [modulok frissítése runbook readme](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) című témakört. További fontos tényezőket kell figyelembe vennie, amikor az `Az` Automation-fiókban lévő modulokat használja, további információért lásd: az [az modulok használata az Automation-fiókban](az-modules.md).

* A runbook megkezdése előtt győződjön meg arról, hogy az Automation-fiókja rendelkezik egy Azure-beli [futtató fiók hitelesítő adatainak](manage-runas-account.md) létrehozásával.

* Ezt a kódot normál PowerShell-parancsfájlként használhatja runbook helyett: először jelentkezzen be az Azure-ba a [AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancs használatával, majd továbbítsa `-Login $false` a parancsfájlnak.

* Ha ezt a runbook a szuverén felhőkön szeretné használni, `AzureRmEnvironment` használja a paramétert a megfelelő környezet átadásához a runbook.  Elfogadható értékek: **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**és **AzureUSGovernment**. Ezek az értékek a használatával `Get-AzureRmEnvironment | select Name`kérhetők le. Ha nem ad át értéket a paraméternek, a runbook alapértelmezés szerint az Azure nyilvános Felhőbeli **AzureCloud**

* Ha a PowerShell-Galéria elérhető legújabb verzió helyett egy adott Azure PowerShell modul verzióját szeretné használni, adja át ezeket a verziókat az `ModuleVersionOverrides` **Update-AutomationAzureModulesForAccount** runbook választható paraméterének. Példákat a [Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook talál. Azure PowerShell a `ModuleVersionOverrides` paraméterben nem említett modulok a PowerShell-Galéria legújabb moduljaival frissülnek. Ha nem adja meg a `ModuleVersionOverrides` paramétert, a rendszer az összes modult frissíti a PowerShell-Galéria legújabb moduljaival. Ez a viselkedés ugyanaz, mint az **Azure-modulok frissítése** gomb.

## <a name="next-steps"></a>További lépések

A nyílt forráskódú [Azure-modulok frissítésével](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) kapcsolatos további információkért tekintse meg a runbook.
