---
title: "Automatikus javítás az SQL Server VMs (klasszikus) |} Microsoft Docs"
description: "Az SQL Server rendszeren futó virtuális gépek az Azure-ban a klasszikus üzembe helyezési mód azt ismerteti, az automatikus javítás funkció."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: 1959871141f196ba80ffd7b37e62e5ea5b42dba3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatikus javítás az SQL Server Azure virtuális gépekben (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klasszikus](../classic/sql-automated-patching.md)
> 
> 

Automatikus javítás karbantartási időszak az az Azure virtuális gép fut az SQL Server hoz létre. Az automatikus frissítések csak a karbantartási időszak alatt lesznek telepítve. Az SQL Server Ez biztosítja, hogy a rendszerfrissítések és minden kapcsolódó újraindítások halasztja az adatbázis lehető legjobb idő. Automatikus javítás függ a [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ez a cikk erőforrás-kezelő verziójának megtekintése: [automatikus javítás az SQL Server az Azure virtuális gépek erőforrás-kezelő](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Előfeltételek
Automatikus javítás használatához vegye figyelembe a következő előfeltételek teljesülését:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-verzió**:

* SQL Server 2012-ben
* SQL Server 2014
* SQL Server 2016

**Az Azure PowerShell**:

* [Telepítse a legújabb Azure PowerShell-parancsok](/powershell/azure/overview).

**SQL Server IaaS bővítmény**:

* [Az SQL Server IaaS-bővítményének telepítése](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
A következő táblázat ismerteti a beállításokat, amelyek képesek automatikus javítás céljából. Klasszikus virtuális gépeket a beállítások konfigurálásához PowerShell kell használnia.

| Beállítás | Lehetséges értékek | Leírás |
| --- | --- | --- |
| **Automatikus javítás** |Engedélyezi/letiltja (letiltva) |Engedélyezi vagy letiltja az automatikus javítás egy Azure virtuális géphez. |
| **Karbantartási ütemezését** |Mindennap, hétfő, kedd, szerda, csütörtök, péntek, szombat, vasárnap |A virtuális gép Windows, az SQL Server és a Microsoft-frissítések letöltése és telepítése ütemezés. |
| **A karbantartás indításának időpontja** |0-24 |A helyi kezdési ideje frissíteni a virtuális gépet. |
| **Karbantartási ablak időtartama** |30-180 |A percet engedélyezett a letöltés és a frissítések telepítésének befejezéséhez. |
| **Javítás kategória** |Fontos |A kategória a frissítések letöltéséhez és telepítéséhez. |

## <a name="configuration-with-powershell"></a>PowerShell-konfiguráció
A következő példában PowerShell segítségével konfigurálja az automatikus javítás a meglévő SQL Server virtuális. A **New-AzureVMSqlServerAutoPatchingConfig** parancs konfigurálja az automatikus frissítések egy új karbantartási időszakot.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Ez a példa alapján, az alábbi táblázat ismerteti a cél Azure virtuális gép hatására:

| Paraméter | Következmény |
| --- | --- |
| **DayOfWeek** |Javítások minden csütörtök telepítve. |
| **MaintenanceWindowStartingHour** |A kezdő frissítések 11:00 órakor. |
| **MaintenanceWindowsDuration** |Javítások 120 percen belül kell telepíteni. A kezdési idő alapján, el kell végezniük 1:00 pm által. |
| **PatchCategory** |Ez a paraméter csak lehetséges értéke "Fontos". |

Eltarthat néhány percig, telepítése és konfigurálása az SQL Server IaaS-ügynök.

Automatikus javítás letiltásához futtassa azonos parancsfájl-az - Enable paramétert a New-AzureVMSqlServerAutoPatchingConfig nélkül. Csakúgy, mint a telepítés, tiltsa le az automatikus javítás több percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
Más elérhető automation feladatokkal kapcsolatos további információkért lásd: [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](../classic/sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server Azure virtuális gépek – áttekintés](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

