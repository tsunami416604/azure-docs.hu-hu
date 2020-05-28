---
title: Automatikus javítás SQL Server virtuális gépekhez (klasszikus) | Microsoft Docs
description: Ismerteti az Azure-ban futó SQL Server Virtual Machines automatikus javítási funkcióját a klasszikus üzembe helyezési mód használatával.
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
ms.openlocfilehash: 3b977c0900178d64f202d4faa122f5d0ef90187c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014655"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Az Azure Virtual Machines SQL Server automatikus javítása (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/automated-patching.md)
> * [Klasszikus](../classic/sql-automated-patching.md)
> 
> 

Az Automatikus javítás egy karbantartási időszakok szab az SQL Servert futtató Azure-beli virtuális gépnek. Az automatikus frissítéseket csak ebben a karbantartási időszakban lehet telepíteni. SQL Server esetében ez biztosítja, hogy a Rendszerfrissítések és a kapcsolódó újraindítások az adatbázis lehető legjobb időpontjában történjenek. 

> [!IMPORTANT]
> Csak a **Fontos** jelzéssel ellátott Windows-frissítések települnek. Az SQL Server további frissítéseit, például az összegző frissítéseket, manuálisan kell telepíteni. 

Az automatikus javítás az [SQL Server IaaS-ügynök bővítményétől](../classic/sql-server-agent-extension.md) függ.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A cikk Resource Manager-verziójának megtekintéséhez lásd: a [SQL Server automatikus javítása az Azure Virtual Machines Resource Managerben](../../../azure-sql/virtual-machines/windows/automated-patching.md).

## <a name="prerequisites"></a>Előfeltételek
Az automatikus javítás használatához vegye figyelembe a következő előfeltételeket:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server verziója**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Telepítse a legújabb Azure PowerShell-parancsokat](/powershell/azure/overview).

**SQL Server IaaS-bővítmény**:

* [Telepítse a SQL Server IaaS bővítményt](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
Az alábbi táblázat az automatikus javításhoz konfigurálható beállításokat ismerteti. A klasszikus virtuális gépek esetében a PowerShell használatával kell konfigurálni ezeket a beállításokat.

| Beállítás | Lehetséges értékek | Leírás |
| --- | --- | --- |
| **Automatikus javítás** |Engedélyezés/letiltás (letiltva) |Engedélyezheti vagy letilthatja az Azure-beli virtuális gépek automatikus javítását. |
| **Karbantartási ütemezés** |Mindennap, hétfő, kedd, szerda, csütörtök, péntek, szombat, vasárnap |A virtuális géphez tartozó Windows-, SQL Server-és Microsoft-frissítések letöltésének és telepítésének ütemterve. |
| **Karbantartási kezdési óra** |0-24 |A virtuális gép frissítésének helyi indítási ideje. |
| **Karbantartási időszak időtartama** |30-180 |A frissítések letöltésének és telepítésének befejezéséhez szükséges percek száma. |
| **Javítás kategóriája** |Fontos |A letölteni és telepíteni kívánt frissítések kategóriája. |

## <a name="configuration-with-powershell"></a>Konfigurálás a PowerShell-lel
A következő példában a PowerShell segítségével konfigurálhatja az automatikus javítást egy meglévő SQL Server VMon. A **New-AzureVMSqlServerAutoPatchingConfig** parancs új karbantartási időszakot konfigurál az automatikus frissítésekhez.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

A jelen példa alapján a következő táblázat ismerteti a cél Azure-beli virtuális gép gyakorlati hatását:

| Paraméter | Hatás |
| --- | --- |
| **DayOfWeek** |A javítások minden csütörtökön telepítve vannak. |
| **MaintenanceWindowStartingHour** |A frissítés megkezdése 11:00 órakor. |
| **MaintenanceWindowDuration** |A javításokat 120 percen belül kell telepíteni. A kezdési időpont alapján a befejezésük 1 – 16:00. |
| **PatchCategory** |A paraméter egyetlen lehetséges beállítása "fontos". |

A SQL Server IaaS-ügynök telepítése és konfigurálása több percet is igénybe vehet.

Az automatikus javítás letiltásához futtassa ugyanazt a parancsfájlt anélkül, hogy a-enable paramétert a New-AzureVMSqlServerAutoPatchingConfig. A telepítéshez hasonlóan több percet is igénybe vehet, hogy letiltsa az automatikus javítást.

## <a name="next-steps"></a>További lépések
További információ az egyéb rendelkezésre álló automatizálási feladatokról: [SQL Server IaaS-ügynök bővítmény](../classic/sql-server-agent-extension.md).

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további információkért lásd: [SQL Server az azure Virtual Machines áttekintése](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

