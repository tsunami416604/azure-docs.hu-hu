---
title: Automatikus javítás SQL Server virtuális gépekhez (klasszikus) | Microsoft dokumentumok
description: A klasszikus üzembe helyezési módban futó SQL Server virtuális gépek automatikus javítási szolgáltatását ismerteti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: efc6d0c25c5186b391deb08ee0e41dcb8ae6edf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978079"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatikus javítás az SQL Serverhez az Azure virtuális gépeken (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klasszikus](../classic/sql-automated-patching.md)
> 
> 

Az Automatikus javítás egy karbantartási időszakok szab az SQL Servert futtató Azure-beli virtuális gépnek. Az automatikus frissítéseket csak ebben a karbantartási időszakban lehet telepíteni. Az SQL Server esetében ez biztosítja, hogy a rendszerfrissítések és a kapcsolódó újraindítások az adatbázis számára a lehető legjobb időpontban történjenek. 

> [!IMPORTANT]
> Csak a **Fontos** jelzéssel ellátott Windows-frissítések települnek. Az SQL Server további frissítéseit, például az összegző frissítéseket, manuálisan kell telepíteni. 

Az automatikus javítás az [SQL Server IaaS-ügynök bővítményétől](../classic/sql-server-agent-extension.md) függ.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A cikk Resource Manager-verziójának megtekintéséhez olvassa el az Automatikus javítás az SQL Server hez az Azure Virtual Machines Resource Manager alkalmazásban című [témakört.](../sql/virtual-machines-windows-sql-automated-patching.md)

## <a name="prerequisites"></a>Előfeltételek
Az automatikus javítás használatához vegye figyelembe az alábbi előfeltételeket:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server verzió:**

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell:**

* [Telepítse a legújabb Azure PowerShell-parancsokat.](/powershell/azure/overview)

**SQL Server IaaS-bővítmény**:

* [Telepítse az SQL Server IaaS bővítményt](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
Az alábbi táblázat az automatikus javításhoz konfigurálható beállításokat ismerteti. Klasszikus virtuális gépek esetén a PowerShell használatával kell konfigurálniezeket a beállításokat.

| Beállítás | Lehetséges értékek | Leírás |
| --- | --- | --- |
| **Automatikus javítás** |Engedélyezés/letiltás (letiltva) |Engedélyezi vagy letiltja az automatikus javítást egy Azure virtuális géphez. |
| **Karbantartási ütemezés** |Minden nap, hétfő, kedd, szerda, csütörtök, péntek, szombat, vasárnap |A Windows, az SQL Server és a Microsoft frissítések letöltésének és telepítésének ütemezése a virtuális géphez. |
| **Karbantartás kezdési órája** |0-24 |A virtuális gép frissítésének helyi kezdési időpontja. |
| **Karbantartási időszak időtartama** |30-180 |A frissítések letöltésének és telepítésének befejezésére engedélyezett percek száma. |
| **Javítás kategória** |Fontos |A letöltandó és telepítandó frissítések kategóriája. |

## <a name="configuration-with-powershell"></a>Konfiguráció a PowerShell használatával
A következő példában a PowerShell segítségével konfigurálhatja az automatikus javítást egy meglévő SQL Server virtuális gépen. A **New-AzureVMSqlServerAutoPatchingConfig** parancs új karbantartási időszakot konfigurál az automatikus frissítésekhez.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

A példa alapján az alábbi táblázat ismerteti a gyakorlati hatása a cél Azure virtuális gép:

| Paraméter | Hatás |
| --- | --- |
| **DayOfWeek (Hét napja)** |Javítások telepítve minden csütörtökön. |
| **MaintenanceWindowStartingHour** |Kezdje a frissítéseket 11:00-kor. |
| **MaintenanceWindowDuration** |A javításokat 120 percen belül be kell szerelni. A kezdési időpont alapján 13:00-ig be kell fejezniük. |
| **PatchKategória** |A paraméter egyetlen lehetséges beállítása a "Fontos". |

Az SQL Server IaaS-ügynök telepítése és konfigurálása több percig is eltarthat.

Az automatikus javítás letiltásához futtassa ugyanazt a parancsfájlt az -Enable paraméter nélkül az Új-AzureVMSqlServerAutoPatchingConfig-hoz. Csakúgy, mint a telepítés, ez eltarthat néhány percig, hogy tiltsa le az automatikus javítás.

## <a name="next-steps"></a>További lépések
Az egyéb elérhető automatizálási feladatokról az [SQL Server IaaS Ügynökbővítmény című](../classic/sql-server-agent-extension.md)témakörben talál további információt.

Az SQL Server Azure-beli virtuális gépeken való futtatásáról az [SQL Server azure-beli virtuális gépek – áttekintés című témakörben olvashat bővebben.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

