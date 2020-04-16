---
title: Azure Automation – gyakori kérdések | Microsoft dokumentumok
description: Válaszok az Azure Automationszolgáltatással kapcsolatos gyakori kérdésekre.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405964"
---
# <a name="azure-automation-frequently-asked-questions"></a>Az Azure Automation gyakori kérdései

Ez a Microsoft GYIK az Azure Automationrel kapcsolatos gyakori kérdések listája. Ha bármilyen további kérdése van a képességeit, megy a vitafórum, és tegye meg kérdéseit. Amikor egy kérdést gyakran felteszünk, hozzáadjuk ehhez a cikkhez, hogy gyorsan és egyszerűen megtalálható legyen.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="update-management-solution"></a>Az Update Management megoldás

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Megakadályozhatom a váratlan operációsrendszer-szintű frissítéseket?

Egyes Linux-változatokon, például a Red Hat Enterprise Linuxon az operációs rendszer szintű frissítések csomagokon keresztül történhetnek. Ez azt eredményezheti, hogy az Update Management elindul, amelyben az operációs rendszer verziószáma megváltozik. Mivel az Update Management ugyanazokat a módszereket használja a windowsos gépeken helyileg használt csomagok frissítéséhez, ez a viselkedés szándékos.

Ha el szeretné kerülni az operációs rendszer verziójának frissítési felügyeleti telepítéseken keresztüli frissítését, használja a **Kizárás** szolgáltatást.

A Red Hat Enterprise Linux-ban `redhat-release-server.x86_64`a kizárandó csomag neve .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Miért nem alkalmaznak kritikus/biztonsági frissítéseket?

Amikor frissítéseket telepít egy Linux-gépre, kiválaszthatja a frissítési besorolásokat. Ez a beállítás szűri a megadott feltételeknek megfelelő frissítéseket. Ez a szűrő helyileg kerül alkalmazásra a számítógépen a frissítés telepítésekor.

Mivel az Update Management frissítésbővítést hajt végre a felhőben, az Update Management egyes frissítéseit megjelölheti biztonsági hatásként, még akkor is, ha a helyi számítógép nem rendelkezik ezekkel az információkkal. Ha kritikus frissítéseket alkalmaz egy Linux-gépre, előfordulhat, hogy olyan frissítések vannak megjelölve, amelyek nincsenek megjelölve, amelyek biztonsági hatással vannak az adott gépre, és ezért nincsenek alkalmazva. Előfordulhat azonban, hogy az Update Management továbbra is nem megfelelőként jelenti a számítógépet, mert további információkat tartalmaz a megfelelő frissítésről.

A frissítések frissítésszerinti besorolás szerinti telepítése nem működik a CentOS RTM-verzióiban. A CentOS frissítéseinek megfelelő telepítéséhez válassza ki az összes besorolást, és győződjön meg arról, hogy a frissítések alkalmazása megvan. A SUSE esetében a CSAK **egyéb frissítések** kiválasztása a besorolás sal további biztonsági frissítések telepítését okozhatja, ha először a zypperhez (csomagkezelőhez) kapcsolódó biztonsági frissítésekre vagy annak függőségeire van szükség. Ez a viselkedés a zypper korlátozása. Bizonyos esetekben előfordulhat, hogy újra kell futtatnia a frissítés központi telepítését, majd ellenőriznie kell a központi telepítést a frissítési naplón keresztül.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Telepíthetek frissítéseket az Azure-bérlők között?

Ha olyan gépekkel rendelkezik, amelyekegy másik Azure-bérlői jelentéskészítési frissítési frissítési szolgáltatásban javításra szorulnak, a következő kerülő megoldást kell használnia az ütemezésükhöz. A [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) parancsmag a megadott `ForUpdateConfiguration` paraméterrel ütemezést hozhat létre. Használhatja a [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) parancsmag, és adja át a `NonAzureComputer` gépeket a másik bérlőben a paraméter. A következő példa bemutatja, hogyan kell ezt megtenni.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>További lépések

Ha a kérdésére itt nem kap választ, további kérdéseket és válaszokat az alábbi forrásokból kaphat.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Visszajelzési fórum](https://feedback.azure.com/forums/905242-update-management)
