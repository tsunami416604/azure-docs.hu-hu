---
title: Automatikus javítás SQL Server rendszerű virtuális gépekhez (Resource Manager) |} A Microsoft Docs
description: Az automatikus javítás funkció az SQL Server Virtual Machines Resource Manager használatával Azure-ban futó ismerteti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d9cedd05e949d9684473d620629314a34802c1fc
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977303"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Az SQL Server automatikus javítása az Azure Virtual Machines szolgáltatásban (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatikus javítás az Azure virtuális gépként futó SQL Server karbantartási időszak hoz létre. Az automatikus frissítéseket csak a karbantartási időszak alatt telepíthető. Az SQL Server esetében ez rescriction biztosítja, hogy a rendszerfrissítések és minden kapcsolódó újraindítások történnek, az adatbázis leginkább lehetséges időpontban. 

> [!IMPORTANT]
> Csak a megjelölt frissítéseket Windows **fontos** vannak telepítve. Az SQL Server egyéb frissítések, kumulatív frissítések, például manuálisan kell telepíteni. 

Automatikus javítás függ a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Előfeltételek
Automatikus javítás használatához vegye figyelembe a következő előfeltételek vonatkoznak:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-verzió**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Telepítse a legújabb Azure PowerShell-parancsok](/powershell/azure/overview) Ha azt tervezi, hogy az automatikus javítás beállítása a PowerShell használatával.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatikus javítás az SQL Server IaaS-ügynök bővítmény támaszkodik. Aktuális SQL virtuálisgép-katalógus rendszerképek alapértelmezés szerint ez a bővítmény hozzáadása. További információkért lásd: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Beállítások
A következő táblázat ismerteti a beállításokat, amelyeket az automatikus javítás állítható be. A tényleges konfigurációs lépések eltérőek lehetnek, ha használja az Azure portal vagy az Azure Windows PowerShell-parancsokat.

| Beállítás | Lehetséges értékek | Leírás |
| --- | --- | --- |
| **Automatikus javítás** |Engedélyezés/letiltás (letiltva) |Engedélyezi vagy letiltja az automatikus javítás az Azure virtuális gépként. |
| **Karbantartási ütemezés** |Mindennap., hétfő, kedd, szerda, csütörtök, péntek, szombat, vasárnap |A virtuális gép Windows, az SQL Server és a Microsoft frissítések letöltése és telepítése a ütemezését. |
| **A karbantartás indításának időpontja** |0-24 |A helyi ideje frissíteni a virtuális gép elindításához. |
| **Karbantartási időszak hossza** |30-180 |A letöltés és frissítések telepítésének befejezéséhez engedélyezett percek számát. |
| **Javítás kategória** |Fontos | A kategória Windows-frissítések letöltése és telepítése.|

## <a name="configuration-in-the-portal"></a>Konfigurációját a portálon
Az Azure portal segítségével a konfigurálása az automatikus javítás kiépítése során, vagy a meglévő virtuális gépek.

### <a name="new-vms"></a>Új virtuális gépek
Az Azure portal segítségével konfigurálhatja a automatikus javítás, amikor egy új SQL Server virtuális gépet hoz létre a Resource Manager-alapú üzemi modellben.

Az a **SQL Server-beállítások** panelen válassza ki **automatikus javítás**. Az alábbi Azure-portál képernyőképe látható a **SQL automatikus javítás** panelen.

![SQL automatikus javítás az Azure Portalon](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Környezet, tekintse meg a teljes témakör [az Azure-ban az SQL Server virtuális gép üzembe helyezésének](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Meglévő virtuális gépek
Meglévő SQL Server virtuális gépek esetében válassza ki az SQL Server virtuális gépet. Válassza ki a **SQL Server-konfiguráció** szakaszában a **beállítások** panelen.

![SQL automatikus javítás meglévő virtuális gépek](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

Az a **SQL Server-konfiguráció** panelen kattintson a **szerkesztése** gombra az automatikus javítás szakaszban.

![Meglévő virtuális gépek SQL automatikus javítás beállítása](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Ha elkészült, kattintson a **OK** gomb alján a **SQL Server-konfiguráció** panelen a módosítások mentéséhez.

Ha engedélyezi a automatikus javítás először, akkor az Azure az SQL Server IaaS-ügynök úgy konfigurálja a háttérben. Ebben az időszakban az Azure Portalon nem jelenik meg, hogy konfigurálva van-e az automatikus javítás. Várjon néhány percet az ügynök telepítve van, úgy konfigurálni. Ezt követően az Azure Portalon jeleníti meg az új beállítások.

> [!NOTE]
> Automatikus javítás sablon használatával is konfigurálhatja. További információkért lásd: [Azure gyorsindítási sablon az automatikus javítás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).
> 
> 

## <a name="configuration-with-powershell"></a>PowerShell-konfiguráció
Az SQL virtuális gép kiépítése, után a PowerShell segítségével konfigurálhatja az automatikus javítás.

A következő példában a PowerShell konfigurálása az automatikus javítás az SQL Server meglévő virtuális gép szolgál. A **AzureRM.Compute\New-AzVMSqlServerAutoPatchingConfig** parancs konfigurálja az automatikus frissítések egy új karbantartási időszakot.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Ha a bővítmény nem telepítette, a bővítmény telepítése újraindítja az SQL Server szolgáltatást.

Ebben a példában alapján, a következő táblázat ismerteti a gyakorlati hatást a cél Azure virtuális Gépen:

| Paraméter | Következmény |
| --- | --- |
| **DayOfWeek** |Javítások telepítése minden csütörtökön tartjuk. |
| **MaintenanceWindowStartingHour** |11:00 órakor kezdje a frissítések. |
| **MaintenanceWindowsDuration** |Javítások követő 120 percen belül kell telepíteni. A kezdési időpont alapján, el kell végezniük 1:00 -kor szerint. |
| **PatchCategory** |Az egyetlen lehetséges beállítása ez a paraméter **fontos**. Ez fontos; megjelölt Windows-frissítés telepítése ebbe a kategóriába nem szerepelnek az SQL Server frissítéseket nem telepít. |

Telepítse és konfigurálja az SQL Server IaaS-ügynök több percet igénybe vehet.

Automatikus javítás letiltásához futtassa ugyanazt a parancsprogramot nélkül a **-engedélyezése** paramétert a **AzureRM.Compute\New-AzVMSqlServerAutoPatchingConfig**. Hiányában a **-engedélyezése** paraméter jelzi a parancs a funkció letiltásához.

## <a name="next-steps"></a>További lépések
Más elérhető automation-feladatokkal kapcsolatos további információkért lásd: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

