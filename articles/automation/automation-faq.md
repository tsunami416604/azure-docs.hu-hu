---
title: Azure Automation – gyakori kérdések | Microsoft dokumentumok
description: Válaszok az Azure Automationszolgáltatással kapcsolatos gyakori kérdésekre.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925812"
---
# <a name="azure-automation-frequently-asked-questions"></a>Az Azure Automation gyakori kérdései

Ez a Microsoft GYIK az Azure Automationrel kapcsolatos gyakori kérdések listája. Ha bármilyen további kérdése van a képességeit, megy a vitafórum, és tegye meg kérdéseit. Amikor egy kérdést gyakran felteszünk, hozzáadjuk ehhez a cikkhez, hogy gyorsan és egyszerűen megtalálható legyen.

## <a name="update-management-solution"></a>Az Update Management megoldás

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Megakadályozhatom a váratlan operációsrendszer-szintű frissítéseket?

Egyes Linux-változatokon, például a Red Hat Enterprise Linuxon az operációs rendszer szintű frissítések csomagokon keresztül történhetnek. Ez azt eredményezheti, hogy az Update Management ott fut, ahol az operációs rendszer verziószáma megváltozik. Mivel az Update Management ugyanazokat a módszereket használja a csomagok frissítéséhez, amelyeket a rendszergazda helyileg használna a Linux-gépen, ez a viselkedés szándékos.

Ha el szeretné kerülni az operációs rendszer verziójának frissítési felügyeleti telepítéseken keresztüli frissítését, használja a **Kizárás** szolgáltatást.

A Red Hat Enterprise Linux rendszerben a kizárandó csomag neve redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Miért nem alkalmaznak kritikus/biztonsági frissítéseket?

Amikor frissítéseket telepít egy Linux-gépre, kiválaszthatja a frissítési besorolásokat. Ez a beállítás szűri a megadott feltételeknek megfelelő, a számítógépre alkalmazott frissítéseket. Ez a szűrő helyileg kerül alkalmazásra a számítógépen a frissítés telepítésekor.

Mivel az Update Management frissítésbővítést hajt végre a felhőben, egyes frissítések az Update Management ben biztonsági hatással rendelkezőként jelölhetők meg, még akkor is, ha a helyi számítógép nem rendelkezik ilyen információkkal. Ennek eredményeképpen, ha kritikus frissítéseket alkalmaz egy Linux-gépre, előfordulhat, hogy olyan frissítések vannak megjelölve, amelyek nincsenek megjelölve, amelyek biztonsági hatással vannak a számítógépre, és ezért a frissítések nem lesznek alkalmazva. Előfordulhat azonban, hogy az Update Management továbbra is nem megfelelőként jelenti a számítógépet, mert további információkat tartalmaz a megfelelő frissítésről.

A frissítések frissítésszerinti besorolás szerinti telepítése nem működik a CentOS RTM-verzióiban. A CentOS frissítéseinek megfelelő telepítéséhez válassza ki az összes besorolást, és győződjön meg arról, hogy a frissítések alkalmazása megvan. A SUSE esetében *csak* az **Egyéb frissítések** beállítása esetén egyes biztonsági frissítések is telepíthetők, ha először a zypperhez (csomagkezelőhez) kapcsolódó biztonsági frissítésekre vagy annak függőségeire van szükség. Ez a viselkedés a zypper korlátozása. Bizonyos esetekben szükség lehet a frissítés központi telepítésének újbóli futtatására. Az ellenőrzéshez ellenőrizze a frissítési naplót.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Telepíthetek frissítéseket az Azure-bérlők között?

Ha egy másik Azure-bérlői gépek jelentéskészítési frissítési kezelése, amely javításra van szüksége, a következő megoldás segítségével kell beszereznie őket ütemezett. Használhatja a [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmag `-ForUpdate` a kapcsolót, hogy hozzon létre egy ütemezést, és használja a [New-AzureRmAutomationSoftwareSoftwareConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) parancsmag, és adja át a gépeket a másik bérlőben a `-NonAzureComputer` paraméter. A következő példa bemutatja, hogyan kell ezt megtenni:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>További lépések

Ha a kérdésére itt nem kap választ, további kérdéseket és válaszokat az alábbi fórumon talál.

- [Azure-automatizálás](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Az Update Management megoldással kapcsolatos általános visszajelzésért látogasson el a [visszajelzési fórumra.](https://feedback.azure.com/forums/905242-update-management)