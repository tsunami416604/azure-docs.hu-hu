---
title: Automatikus javítása az SQL Server virtuális gépek (klasszikus) |} A Microsoft Docs
description: Az automatikus javítás funkció az SQL Server virtuális gépek a klasszikus üzembe helyezési mód használata Azure-ban futó ismerteti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aa912e3eb76d72e7a79c83d7e51d493310bd36b3
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331316"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatikus javítás SQL Serverhez Azure-beli virtuális gépeken (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klasszikus](../classic/sql-automated-patching.md)
> 
> 

Automatikus javítás az Azure virtuális gépként futó SQL Server karbantartási időszak hoz létre. Az automatikus frissítéseket csak a karbantartási időszak alatt telepíthető. Az SQL Server esetében ez biztosítja, hogy a rendszerfrissítések és minden kapcsolódó újraindítások történnek, az adatbázis leginkább lehetséges időpontban. 

> [!IMPORTANT]
> Csak a megjelölt frissítéseket Windows **fontos** vannak telepítve. Az SQL Server egyéb frissítések, kumulatív frissítések, például manuálisan kell telepíteni. 

Automatikus javítás függ a [SQL Server IaaS-ügynök bővítmény](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ez a cikk Resource Managerre vonatkozó verziójának megtekintéséhez lásd: [automatikus javítás SQL Serverhez az Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md).

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

* [Telepítse a legújabb Azure PowerShell-parancsok](/powershell/azure/overview).

**SQL Server IaaS Extension**:

* [Telepítse az SQL Server IaaS-bővítményt](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
A következő táblázat ismerteti a beállításokat, amelyeket az automatikus javítás állítható be. Klasszikus virtuális gépeket, a PowerShell kell használnia, ezek a beállítások konfigurálása.

| Beállítás | Lehetséges értékek | Leírás |
| --- | --- | --- |
| **Automatikus javítás** |Engedélyezés/letiltás (letiltva) |Engedélyezi vagy letiltja az automatikus javítás az Azure virtuális gépként. |
| **Karbantartási ütemezés** |Mindennap., hétfő, kedd, szerda, csütörtök, péntek, szombat, vasárnap |A virtuális gép Windows, az SQL Server és a Microsoft frissítések letöltése és telepítése a ütemezését. |
| **A karbantartás indításának időpontja** |0-24 |A helyi ideje frissíteni a virtuális gép elindításához. |
| **Karbantartási időszak hossza** |30-180 |A letöltés és frissítések telepítésének befejezéséhez engedélyezett percek számát. |
| **Javítás kategória** |Fontos |A kategória, a frissítések letöltéséhez és telepítéséhez. |

## <a name="configuration-with-powershell"></a>PowerShell-konfiguráció
A következő példában a PowerShell konfigurálása az automatikus javítás az SQL Server meglévő virtuális gép szolgál. A **New-AzureVMSqlServerAutoPatchingConfig** parancs konfigurálja az automatikus frissítések egy új karbantartási időszakot.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Ebben a példában alapján, a következő táblázat ismerteti a gyakorlati hatást a cél Azure virtuális Gépen:

| Paraméter | Következmény |
| --- | --- |
| **DayOfWeek** |Javítások telepítése minden csütörtökön tartjuk. |
| **MaintenanceWindowStartingHour** |11:00 órakor kezdje a frissítések. |
| **MaintenanceWindowDuration** |Javítások követő 120 percen belül kell telepíteni. A kezdési időpont alapján, el kell végezniük 1:00 -kor szerint. |
| **PatchCategory** |Ezt a paramétert egyetlen lehetséges érték "Fontos". |

Telepítse és konfigurálja az SQL Server IaaS-ügynök több percet igénybe vehet.

Automatikus javítás letiltásához futtassa ugyanazt a - Enable paramétert a New-AzureVMSqlServerAutoPatchingConfig nélkül parancsfájl. Csakúgy, mint a telepítés, automatikus javítás letiltása több percig is eltarthat.

## <a name="next-steps"></a>További lépések
Más elérhető automation-feladatokkal kapcsolatos további információkért lásd: [SQL Server IaaS-ügynök bővítmény](../classic/sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server on Azure Virtual Machines – áttekintés](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

