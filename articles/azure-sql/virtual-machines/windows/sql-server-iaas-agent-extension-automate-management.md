---
title: Felügyeleti feladatok automatizálása a IaaS-ügynök bővítménnyel
description: Ez a cikk ismerteti, hogyan kezelheti a SQL Server IaaS-ügynök bővítményt, amely automatizálja az adott SQL Server felügyeleti feladatokat. Ezek közé tartozik az automatikus biztonsági mentés, az automatikus javítás és a Azure Key Vault integráció.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 89210cda6390fd7c3cf4ca2877b8899559a41321
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84668712"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Felügyeleti feladatok automatizálása Azure-beli virtuális gépeken a SQL Server IaaS-ügynök bővítmény használatával
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


> [!div class="op_single_selector"]
> * [Resource Manager](sql-server-iaas-agent-extension-automate-management.md)
> * [Klasszikus](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Az SQL Server IaaS-ügynök bővítmény (SqlIaasExtension) Azure-beli virtuális gépeken fut, és az adminisztrációs feladatok automatizálását végzi. Ez a cikk áttekintést nyújt a bővítmény által támogatott szolgáltatásokról. Ez a cikk a bővítmény telepítésére, állapotára és eltávolítására vonatkozó utasításokat is tartalmaz.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

A cikk klasszikus verziójának megtekintéséhez lásd: [SQL Server IaaS-ügynök bővítmény SQL Server virtuális gépekhez (klasszikus)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Támogatott szolgáltatások
A SQL Server IaaS-ügynök bővítmény a következő felügyeleti feladatokat támogatja:

| Felügyeleti funkció | Description |
| --- | --- |
| **Automatikus biztonsági mentés SQL Server** |Automatizálja a biztonsági mentések ütemezését az összes adatbázishoz az alapértelmezett példányhoz, vagy a virtuális gépen a SQL Server [megfelelően telepített](frequently-asked-questions-faq.md#administration) példányát. További információ: [SQL Server automatikus biztonsági mentése az Azure Virtual Machines szolgáltatásban (Resource Manager)](automated-backup-sql-2014.md). |
| **Automatikus javítás SQL Server** |Beállítja a karbantartási időszakot, amely alatt a virtuális gép fontos Windows-frissítései elhelyezhetők, így elkerülhetők a frissítések a számítási feladatok maximális ideje alatt. További információ: [SQL Server automatikus javítása az Azure Virtual Machines szolgáltatásban (Resource Manager)](automated-patching.md). |
| **Azure Key Vault-integráció** |Lehetővé teszi Azure Key Vault automatikus telepítését és konfigurálását a SQL Server VM. További információ: [Azure Key Vault integrációjának konfigurálása az Azure-beli SQL Serverhoz Virtual Machines (Resource Manager)](azure-key-vault-integration-configure.md). |

A SQL Server IaaS-ügynök bővítmény telepítése és futtatása után elérhetővé válik az adminisztrációs funkciók:

* A Azure Portal virtuális gép SQL Server paneljén, valamint az Azure Marketplace-en SQL Server rendszerképeken Azure PowerShellon keresztül.
* Azure PowerShell a bővítmény manuális telepítéséhez. 

## <a name="prerequisites"></a>Előfeltételek
Az alábbi követelmények vonatkoznak a SQL Server IaaS-ügynök bővítmény használatára a virtuális gépen:

**Operációs rendszer**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server verziója**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [A legújabb Azure PowerShell parancsok letöltése és konfigurálása](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Telepítés
A SQL Server IaaS bővítmény akkor települ, amikor regisztrálja az SQL Server VM a [SQL Server VM erőforrás-szolgáltatóval](sql-vm-resource-provider-register.md). Ha szükséges, a SQL Server IaaS-ügynököt manuálisan is telepítheti az alábbi PowerShell-parancs használatával: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> A bővítmény telepítése újraindítja a SQL Server szolgáltatást. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Telepítés egyetlen elnevezett SQL Server-példánnyal rendelkező virtuális gépen
A SQL Server IaaS bővítmény a SQL Server megnevezett példányával fog működni, ha az alapértelmezett példány el lett távolítva, és a IaaS-bővítmény újratelepítése megtörténik.

SQL Server megnevezett példányának használatához hajtsa végre az alábbi lépéseket:
   1. SQL Server VM üzembe helyezése az Azure piactéren. 
   1. Távolítsa el a IaaS bővítményt a [Azure Portalból](https://portal.azure.com).
   1. Távolítsa el SQL Server teljesen a SQL Server VMon belül.
   1. Telepítse a SQL Servert egy megnevezett példánnyal a SQL Server VMon belül. 
   1. Telepítse a IaaS bővítményt a Azure Portal.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>A SQL Server IaaS-bővítmény állapotának beolvasása
A bővítmény telepítésének egyik módja az, hogy megtekintse az ügynök állapotát a Azure Portalban. Válassza a **minden beállítás** lehetőséget a virtuális gép ablakban, majd válassza a **bővítmények**lehetőséget. Ekkor meg kell jelennie a felsorolt **SqlIaasExtension** -bővítménynek.

![A SQL Server IaaS-ügynök bővítmény állapota a Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

A **Get-AzVMSqlServerExtension** Azure PowerShell parancsmagot is használhatja:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Az előző parancs megerősíti, hogy az ügynök telepítve van, és általános állapotinformációkat biztosít. Az automatikus biztonsági mentéssel és javítással kapcsolatos információkat az alábbi parancsokkal érheti el:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Eltávolítása
A Azure Portal távolítsa el a bővítményt a virtuális gép tulajdonságainak **bővítmények** ablakában található három pontot kiválasztva. Ezután válassza a **Törlés** elemet.

![A SQL Server IaaS-ügynök bővítményének eltávolítása Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

A **Remove-AzVMSqlServerExtension PowerShell-** parancsmagot is használhatja:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Következő lépések
Kezdje el használni a bővítmény által támogatott szolgáltatások egyikét. További információkért tekintse meg a jelen cikk [támogatott szolgáltatások](#supported-services) című részében hivatkozott cikkeket.

További információ az Azure Virtual Machines SQL Server futtatásáról: [mi SQL Server az azure Virtual Machines?](sql-server-on-azure-vm-iaas-what-is-overview.md)
