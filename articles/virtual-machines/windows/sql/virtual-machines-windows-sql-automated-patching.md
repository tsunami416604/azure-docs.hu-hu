---
title: Automatikus javítás SQL Server virtuális gépekhez (Resource Manager) | Microsoft dokumentumok
description: A cikk az Azure-ban az Erőforrás-kezelő vel futó SQL Server virtuális gépek automatikus javítási szolgáltatását ismerteti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 292f3e8819f6f9f4b2989423814e02dfcfb4bfdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127682"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Az SQL Server automatikus javítása az Azure Virtual Machines szolgáltatásban (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Az Automatikus javítás egy karbantartási időszakok szab az SQL Servert futtató Azure-beli virtuális gépnek. Az automatikus frissítéseket csak ebben a karbantartási időszakban lehet telepíteni. Az SQL Server esetén ez a korlátozás gondoskodik róla, hogy a rendszerfrissítésekre és az esetleges újraindításokra az adatbázis számára a lehető legkedvezőbb időpontban kerüljön sor. 

> [!IMPORTANT]
> Csak a Fontos ként vagy **kritikusként** megjelölt Windows és SQL Server frissítések vannak telepítve. **Important** Az SQL Server egyéb frissítéseit, például a szervizcsomagokat és a **fontosként** vagy **kritikusként** nem jelölt összesítő frissítéseket manuálisan kell telepíteni. 

Az automatikus javítás az [SQL Server IaaS-ügynök bővítményétől](virtual-machines-windows-sql-server-agent-extension.md) függ.

## <a name="prerequisites"></a>Előfeltételek
Az automatikus javítás használatához vegye figyelembe az alábbi előfeltételeket:

**Operációs rendszer**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server verzió:**

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell:**

* [Telepítse a legújabb Azure PowerShell-parancsokat,](/powershell/azure/overview) ha az automatikus javítást a PowerShellhasználatával kívánja konfigurálni.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Az automatikus javítás az SQL Server IaaS-ügynökbővítményre támaszkodik. Az aktuális SQL virtuálisgép-gyűjtemény képei alapértelmezés szerint hozzáadják ezt a bővítményt. További információt az [SQL Server IaaS Agent Extension című](virtual-machines-windows-sql-server-agent-extension.md)témakörben talál.
> 
> 

## <a name="settings"></a>Beállítások
Az alábbi táblázat az automatikus javításhoz konfigurálható beállításokat ismerteti. A tényleges konfigurációs lépések attól függően változnak, hogy az Azure Portalvagy az Azure Windows PowerShell-parancsokat használja.The actual configuration steps vary on whether you use the Azure Portal or Azure Windows PowerShell commands.

| Beállítás | Lehetséges értékek | Leírás |
| --- | --- | --- |
| **Automatikus javítás** |Engedélyezés/letiltás (letiltva) |Engedélyezi vagy letiltja az automatikus javítást egy Azure virtuális géphez. |
| **Karbantartási ütemezés** |Minden nap, hétfő, kedd, szerda, csütörtök, péntek, szombat, vasárnap |A Windows, az SQL Server és a Microsoft frissítések letöltésének és telepítésének ütemezése a virtuális géphez. |
| **Karbantartás kezdési órája** |0-24 |A virtuális gép frissítésének helyi kezdési időpontja. |
| **Karbantartási időszak időtartama** |30-180 |A frissítések letöltésének és telepítésének befejezésére engedélyezett percek száma. |
| **Javítás kategória** |Fontos | A letöltandó és telepítandó Windows-frissítések kategóriája.|

## <a name="configuration-in-the-portal"></a>Konfiguráció a portálon
Az Azure Portal segítségével konfigurálhatja az automatikus javítás kiépítése során, vagy a meglévő virtuális gépek.

### <a name="new-vms"></a>Új virtuális gépek
Az Azure Portal használatával konfigurálhatja az automatikus javítást, amikor új SQL Server virtuális gépet hoz létre az Erőforrás-kezelő telepítési modelljében.

Az **SQL Server beállításai** lapján válassza a **Konfiguráció módosítása** lehetőséget az Automatikus **javítás csoportban.** Az alábbi Azure Portalképernyőkép az **SQL automatikus javítási panelt** mutatja.

![SQL automatikus javítás az Azure Portalon](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

A környezetről az [SQL Server virtuális gépek Azure-beli kiépítéséről](virtual-machines-windows-portal-sql-server-provision.md)szóló teljes témakörben olvashat.

### <a name="existing-vms"></a>Meglévő virtuális gépek

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Meglévő SQL Server virtuális gépek esetén nyissa meg az [SQL virtuális gépek erőforrását,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) és válassza a **Javítás** lehetőséget a **Beállítások csoportban.** 

![SQL automatikus javítás meglévő virtuális gépekhez](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Ha végzett, kattintson az **OK** gombra az **SQL Server konfigurációs** panel alján a módosítások mentéséhez.

Ha első alkalommal engedélyezi az automatikus javítást, az Azure a háttérben konfigurálja az SQL Server IaaS-ügynököt. Ez alatt az idő alatt előfordulhat, hogy az Azure Portal nem mutatja, hogy az automatikus javítás konfigurálva van. Várjon néhány percet, amíg az ügynök telepítése konfigurálva van. Ezt követően az Azure Portal tükrözi az új beállításokat.

## <a name="configuration-with-powershell"></a>Konfiguráció a PowerShell használatával
Az SQL virtuális gép kiépítése után a PowerShell segítségével konfigurálhatja az automatikus javítást.

A következő példában a PowerShell segítségével konfigurálhatja az automatikus javítást egy meglévő SQL Server virtuális gépen. A **New-AzVMSqlServerAutoPatchingConfig** parancs új karbantartási időszakot konfigurál az automatikus frissítésekhez.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Ha a bővítmény még nincs telepítve, a bővítmény telepítése újraindítja az SQL Server szolgáltatást.

A példa alapján az alábbi táblázat ismerteti a gyakorlati hatása a cél Azure virtuális gép:

| Paraméter | Hatás |
| --- | --- |
| **DayOfWeek (Hét napja)** |Javítások telepítve minden csütörtökön. |
| **MaintenanceWindowStartingHour** |Kezdje a frissítéseket 11:00-kor. |
| **KarbantartásWindowsDuration** |A javításokat 120 percen belül be kell szerelni. A kezdési időpont alapján 13:00-ig be kell fejezniük. |
| **PatchKategória** |A paraméter egyetlen lehetséges beállítása a **Fontos**. Ez telepíti a Fontos ként megjelölt Windows-frissítést; nem telepít olyan SQL Server frissítéseket, amelyek nem szerepelnek ebben a kategóriában. |

Az SQL Server IaaS-ügynök telepítése és konfigurálása több percig is eltarthat.

Az automatikus javítás letiltásához futtassa ugyanazt a parancsfájlt az **-Enable** paraméter nélkül a **New-AzVMSqlServerAutoPatchingConfig paraméterhez.** A **-Enable** paraméter hiánya jelzi a parancs nak a szolgáltatás letiltását.

## <a name="next-steps"></a>További lépések
Az egyéb elérhető automatizálási feladatokról az [SQL Server IaaS Ügynökbővítmény című](virtual-machines-windows-sql-server-agent-extension.md)témakörben talál további információt.

Az SQL Server Azure-beli virtuális gépeken való futtatásáról az [SQL Server azure-beli virtuális gépek – áttekintés című témakörben olvashat bővebben.](virtual-machines-windows-sql-server-iaas-overview.md)

