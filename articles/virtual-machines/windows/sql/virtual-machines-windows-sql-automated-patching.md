---
title: "Automatikus javítás az SQL Server VMs (erőforrás-kezelő) |} Microsoft Docs"
description: "Ismerteti az SQL Server rendszeren futó virtuális gépek az Azure Resource Manager használatával, az automatikus javítás funkció."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: 
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.openlocfilehash: 398e682db6c42bd7f4864113ddf10a6a75e2b65b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Az SQL Server automatikus javítása az Azure Virtual Machines szolgáltatásban (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatikus javítás karbantartási időszak az az Azure virtuális gép fut az SQL Server hoz létre. Az automatikus frissítések csak a karbantartási időszak alatt lesznek telepítve. Az SQL Server a rescriction biztosítja, hogy a rendszerfrissítések és minden kapcsolódó újraindítások halasztja az adatbázis lehető legjobb idő. 

> [!IMPORTANT]
> Csak a megjelölt frissítéseket Windows **fontos** vannak telepítve. Más SQL Server-frissítések, például az összegző frissítések, manuálisan kell telepíteni. 

Automatikus javítás függ a [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Előfeltételek
Automatikus javítás használatához vegye figyelembe a következő előfeltételek teljesülését:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-verzió**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Az Azure PowerShell**:

* [Telepítse a legújabb Azure PowerShell-parancsok](/powershell/azure/overview) Ha azt tervezi, hogy az automatikus javítás beállítása a PowerShell használatával.

> [!NOTE]
> Automatikus javítás az SQL Server IaaS ügynöke bővítmény támaszkodik. A gyűjtemény lemezképei aktuális SQL virtuális gép alapértelmezés szerint adja hozzá ezt a bővítményt. További információkért lásd: [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Beállítások
A következő táblázat ismerteti a beállításokat, amelyek képesek automatikus javítás céljából. A tényleges konfigurációs lépései eltérőek attól függően, hogy használ-e az Azure portálon vagy az Azure PowerShell-parancsokkal.

| Beállítás | Lehetséges értékek | Leírás |
| --- | --- | --- |
| **Automatikus javítás** |Engedélyezi/letiltja (letiltva) |Engedélyezi vagy letiltja az automatikus javítás egy Azure virtuális géphez. |
| **Karbantartási ütemezését** |Everyday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday |A virtuális gép Windows, az SQL Server és a Microsoft-frissítések letöltése és telepítése ütemezés. |
| **A karbantartás indításának időpontja** |0-24 |A helyi kezdési ideje frissíteni a virtuális gépet. |
| **Karbantartási ablak időtartama** |30-180 |A percet engedélyezett a letöltés és a frissítések telepítésének befejezéséhez. |
| **Javítás kategória** |Fontos | A Windows-frissítések letöltéséhez és telepítéséhez kategóriáját.|

## <a name="configuration-in-the-portal"></a>A portál konfigurálása
Az Azure portál segítségével konfigurálja az automatikus javítás kiépítése során, vagy meglévő virtuális gépekhez.

### <a name="new-vms"></a>Új virtuális gépek
Az Azure portál segítségével konfigurálhatja az automatikus javítás egy új SQL Server virtuális gép létrehozásakor a Resource Manager üzembe helyezési modellben.

Az a **SQL Server-beállítások** panelen válassza **automatikus javítás**. Az alábbi az Azure portál képernyőképe látható a **SQL automatikus javítás** panelen.

![SQL automatikus javítás Azure-portálon](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

A környezetben, tekintse meg a teljes témakör [az Azure SQL Server virtuális gépek kiépítése](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Meglévő virtuális gépek
Meglévő SQL Server virtuális gépek válassza ki az SQL Server virtuális gépet. Válassza ki a **SQL Server-konfigurációs** szakasza a **beállítások** panelen.

![SQL automatikus javítás meglévő virtuális gépek](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

A a **SQL Server-konfigurációs** panelen kattintson a **szerkesztése** az automatikus javítás szakasz gombjára.

![Meglévő virtuális gépek SQL automatikus javítás beállítása](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Ha elkészült, kattintson a **OK** gomb alján a **SQL Server-konfigurációs** panelt, és menti a módosításokat.

Ha engedélyezi az automatikus javítás először, Azure konfigurálja az SQL Server IaaS-ügynök a háttérben. Ebben az időszakban az Azure-portálon előfordulhat, hogy jelenjen meg, hogy konfigurálva van-e az automatikus javítás. Várjon egy pár percet, az ügynök telepítve, konfigurálva. Ezt követően az Azure-portálon tükrözi az új beállítások.

> [!NOTE]
> Automatikus javítás sablon használatával is konfigurálhatja. További információkért lásd: [Azure gyors üzembe helyezés sablon automatikus javítás céljából](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).
> 
> 

## <a name="configuration-with-powershell"></a>PowerShell-konfiguráció
Az SQL virtuális gép kiépítése, után a PowerShell használatával konfigurálja az automatikus javítás.

A következő példában PowerShell segítségével konfigurálja az automatikus javítás a meglévő SQL Server virtuális. A **AzureRM.Compute\New-AzureRmVMSqlServerAutoPatchingConfig** parancs konfigurálja az automatikus frissítések egy új karbantartási időszakot.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureRmVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Ha a kiterjesztés már nincs telepítve, a bővítmény telepítése újraindítja az SQL Server szolgáltatást.

Ez a példa alapján, az alábbi táblázat ismerteti a cél Azure virtuális gép hatására:

| Paraméter | Következmény |
| --- | --- |
| **DayOfWeek** |Javítások minden csütörtök telepítve. |
| **MaintenanceWindowStartingHour** |A kezdő frissítések 11:00 órakor. |
| **MaintenanceWindowsDuration** |Javítások 120 percen belül kell telepíteni. A kezdési idő alapján, el kell végezniük 1:00 pm által. |
| **PatchCategory** |Az egyetlen lehetséges beállítása ez a paraméter **fontos**. Ez telepíti a Windows update megjelölve fontos; nem telepíti, amelyek nem tartoznak ebbe a kategóriába tartozó SQL Server frissítéseket. |

Eltarthat néhány percig, telepítése és konfigurálása az SQL Server IaaS-ügynök.

Automatikus javítás letiltásához futtassa ugyanazt a parancsfájlt nélkül a **-engedélyezése** paramétert a **AzureRM.Compute\New-AzureRmVMSqlServerAutoPatchingConfig**. Hiányában a **-engedélyezése** paraméter jelzi a parancs a funkció letiltásához.

## <a name="next-steps"></a>További lépések
Más elérhető automation feladatokkal kapcsolatos további információkért lásd: [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server Azure virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

