---
title: Azure Automation GYIK | Microsoft Docs
description: Válaszok a Azure Automationekkel kapcsolatos gyakori kérdésekre.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405964"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation gyakori kérdések

A Microsoft gyakori kérdései a Azure Automationkal kapcsolatos gyakori kérdések listája. Ha további kérdései vannak a képességeivel kapcsolatban, látogasson el a vitafórumra, és tegye fel kérdéseit. Ha egy kérdést gyakran megkérdeznek, azt a cikkhez adja hozzá, hogy gyorsan és könnyen elérhető legyen.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="update-management-solution"></a>Az Update Management megoldás

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Megakadályozható a váratlan operációsrendszer-szintű frissítések?

Bizonyos Linux-változatoknál, például a Red Hat Enterprise Linux, az operációsrendszer-szintű frissítések a csomagokon keresztül is előfordulhatnak. Ennek eredményeként előfordulhat, hogy Update Management fut, amelyben az operációs rendszer verziószáma megváltozik. Mivel a Update Management ugyanazokat a módszereket használja, mint például a rendszergazdák által a Linux rendszerű gépen helyileg használt csomagok frissítése, ez a viselkedés szándékos.

Ha el szeretné kerülni az operációs rendszer verziójának Update Management központi telepítéseken keresztüli frissítését, használja a **kizárás** funkciót.

Red Hat Enterprise Linux a kizárni kívánt csomag neve `redhat-release-server.x86_64`.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Miért nem alkalmaz kritikus/biztonsági frissítéseket?

Ha Linux rendszerű gépre telepít frissítéseket, válassza a frissítési besorolások lehetőséget. Ez a beállítás a megadott feltételeknek megfelelő frissítéseket szűri. Ezt a szűrőt a rendszer helyileg alkalmazza a számítógépen a frissítés telepítésekor.

Mivel a Update Management a felhőben frissíti a frissítés bővítését, biztonsági hatással lehet Update Management egyes frissítéseire, még akkor is, ha a helyi gép nem rendelkezik ezekkel az információkkal. Ha a kritikus frissítéseket egy Linux rendszerű gépre alkalmazza, előfordulhat, hogy olyan frissítések vannak, amelyek nincsenek megjelölve biztonsági hatással a gépre, ezért nem alkalmazhatók. Előfordulhat azonban, hogy Update Management még mindig nem megfelelőként jelenti a gépet, mert további információkra van szükség a megfelelő frissítésről.

A frissítések besorolás szerinti központi telepítése nem működik a CentOS RTM-verzióin. A CentOS-frissítések megfelelő telepítéséhez válassza a minden besorolás lehetőséget, hogy a rendszer a frissítéseket alkalmazza. A SUSE esetében, ha csak **más frissítések** vannak kiválasztva besorolással, akkor további biztonsági frissítéseket is telepíthet, ha először a Zypper (csomagkezelő) vagy annak függőségeire vonatkozó biztonsági frissítéseket kell telepíteni. Ez a viselkedés a Zypper korlátozása. Bizonyos esetekben előfordulhat, hogy újra kell futtatnia a frissítés központi telepítését, majd ellenőriznie kell a központi telepítést a frissítési naplóban.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Telepíthetek frissítéseket az Azure-bérlők között?

Ha olyan gépekkel rendelkezik, amelyeknek egy másik Azure-Bérlőnek kell bejelentkezniük Update Management, akkor a következő megkerülő megoldást kell használnia az ütemezett kezdéshez. A [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) parancsmagot használhatja a `ForUpdateConfiguration` megadott paraméterrel az ütemterv létrehozásához. Használhatja a [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) parancsmagot, és átadhatja a többi bérlőben lévő gépeket a `NonAzureComputer` paraméternek. Az alábbi példa azt szemlélteti, hogyan teheti ezt meg.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>További lépések

Ha a kérdés itt nem válaszol, további kérdéseit és válaszait az alábbi forrásokban tekintheti meg.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Visszajelzési fórum](https://feedback.azure.com/forums/905242-update-management)
