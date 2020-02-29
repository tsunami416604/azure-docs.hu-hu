---
title: Azure Automation GYIK | Microsoft Docs
description: Válaszok a Azure Automationekkel kapcsolatos gyakori kérdésekre.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925812"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation gyakori kérdések

A Microsoft gyakori kérdései a Azure Automationkal kapcsolatos gyakori kérdések listája. Ha további kérdései vannak a képességeivel kapcsolatban, látogasson el a vitafórumra, és tegye fel kérdéseit. Egy kérdést gyakran ismételt, amikor hozzáadjuk ehhez a cikkhez, hogy gyorsan és könnyen megtalálhatók.

## <a name="update-management-solution"></a>Update Management megoldás

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Megakadályozható a váratlan operációsrendszer-szintű frissítések?

Bizonyos Linux-változatoknál, például a Red Hat Enterprise Linux, az operációsrendszer-szintű frissítések a csomagokon keresztül is előfordulhatnak. Ennek eredményeként előfordulhat, hogy Update Management futtatja az operációs rendszer verziószámát. Mivel a Update Management ugyanazokat a módszereket használja, mint például a rendszergazdák által a Linux-gépen helyileg használt csomagok frissítése, ez a viselkedés szándékos.

Ha el szeretné kerülni az operációs rendszer verziójának Update Management központi telepítéseken keresztüli frissítését, használja a **kizárás** funkciót.

Red Hat Enterprise Linux a kizárni kívánt csomag neve a RedHat-Release-Server. x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Miért nem alkalmaz kritikus/biztonsági frissítéseket?

Ha Linux rendszerű gépre telepít frissítéseket, válassza a frissítési besorolások lehetőséget. Ez a beállítás a megadott feltételeknek megfelelő gépre alkalmazott frissítéseket szűri. Ezt a szűrőt a rendszer helyileg alkalmazza a számítógépen a frissítés telepítésekor.

Mivel a Update Management a felhőben frissíti a frissítés bővítését, néhány frissítés biztonsági hatással lehet a Update Managementban, még akkor is, ha a helyi gép nem rendelkezik ezekkel az információkkal. Ennek eredményeképpen, ha a kritikus frissítéseket egy Linux rendszerű gépre alkalmazza, előfordulhat, hogy olyan frissítések vannak, amelyek nincsenek megjelölve biztonsági hatással a gépen, ezért a frissítések nincsenek alkalmazva. Előfordulhat azonban, hogy Update Management még mindig nem megfelelőként jelentheti a gépet, mert további információkra van szükség a megfelelő frissítésről.

A frissítések besorolás szerinti központi telepítése nem működik a CentOS RTM-verzióin. A CentOS-frissítések megfelelő telepítéséhez válassza a minden besorolás lehetőséget, hogy a rendszer a frissítéseket alkalmazza. A SUSE esetében, ha *csak* **más frissítések** vannak kiválasztva besorolással, akkor a biztonsági frissítések is telepíthetők, ha a Zypper (Package Manager) vagy annak függőségeire vonatkozó biztonsági frissítések szükségesek először. Ez a viselkedés a Zypper korlátozása. Bizonyos esetekben előfordulhat, hogy újra kell futtatnia a frissítés központi telepítését. Az ellenőrzéshez ellenőrizze a frissítési naplót.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Telepíthetek frissítéseket az Azure-bérlők között?

Ha olyan gépekkel rendelkezik, amelyeket egy másik Azure-bérlő jelent a javításhoz szükséges Update Management, akkor a következő megkerülő megoldást kell használnia ahhoz, hogy ütemezve legyen. A [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmaggal a `-ForUpdate` kapcsolóval hozhat létre ütemtervet, és használhatja a [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) parancsmagot, és átadhatja a többi bérlőben lévő gépeket a `-NonAzureComputer` paraméternek. Az alábbi példa bemutatja, hogyan teheti meg ezt:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Következő lépések

Ha a kérdés itt nem válaszol, további kérdéseit és válaszait az alábbi fórumon tekintheti meg.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Az Update Management-megoldással kapcsolatos általános Visszajelzésért látogasson el a [visszajelzési fórumra](https://feedback.azure.com/forums/905242-update-management).