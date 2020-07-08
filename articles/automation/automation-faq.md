---
title: Azure Automation GYIK | Microsoft Docs
description: Ez a cikk válaszokat ad a Azure Automationekkel kapcsolatos gyakori kérdésekre.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 1cd20e28d1b36167154059adf728a9cfdf8102bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83836617"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation gyakori kérdések

A Microsoft gyakori kérdései a Azure Automationkal kapcsolatos gyakori kérdések listája. Ha további kérdései vannak a képességeivel kapcsolatban, látogasson el a vitafórumra, és tegye fel kérdéseit. Ha egy kérdést gyakran megkérdeznek, azt a cikkhez adja hozzá, hogy gyorsan és könnyen elérhető legyen.

## <a name="update-management"></a>Frissítéskezelés

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Megakadályozható a váratlan operációsrendszer-szintű frissítések?

Bizonyos Linux-változatoknál, például a Red Hat Enterprise Linux, az operációsrendszer-szintű frissítések a csomagokon keresztül is előfordulhatnak. Ennek eredményeként előfordulhat, hogy Update Management fut, amelyben az operációs rendszer verziószáma megváltozik. Mivel a Update Management ugyanazokat a módszereket használja, mint például a rendszergazdák által a Linux rendszerű gépen helyileg használt csomagok frissítése, ez a viselkedés szándékos.

Ha el szeretné kerülni az operációs rendszer verziójának Update Management központi telepítéseken keresztüli frissítését, használja a **kizárás** funkciót.

Red Hat Enterprise Linux a kizárni kívánt csomag neve `redhat-release-server.x86_64` .

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

- [Azure Automation](https://docs.microsoft.com/answers/topics/azure-automation.html)
- [Visszajelzési fórum](https://feedback.azure.com/forums/905242-update-management)
