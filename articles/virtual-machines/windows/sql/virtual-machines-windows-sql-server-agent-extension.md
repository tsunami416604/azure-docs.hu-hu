---
title: Felügyeleti feladatok automatizálása az IaaS-ügynökbővítménysegítségével
description: Ez a cikk az SQL Server IaaS Agent Extension kezelését ismerteti, amely automatizálja az SQL Server egyes felügyeleti feladatait. Ezek közé tartozik az automatikus biztonsági mentés, az automatikus javítás és az Azure Key Vault-integráció.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
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
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030778"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Felügyeleti feladatok automatizálása az Azure virtuális gépeken az SQL Server IaaS Ügynökbővítmény használatával
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Az SQL Server IaaS-ügynök bővítmény (SqlIaasExtension) Azure-beli virtuális gépeken fut, és az adminisztrációs feladatok automatizálását végzi. Ez a cikk áttekintést nyújt a bővítmény által támogatott szolgáltatásokról. Ez a cikk a bővítmény telepítésével, állapotával és eltávolításával kapcsolatos utasításokat is tartalmaz.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

A cikk klasszikus verziójának megtekintéséhez olvassa el az [SQL Server IaaS Agent Extension for SQL Server virtuális gépek (klasszikus) című témakört.](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)


## <a name="supported-services"></a>Támogatott szolgáltatások
Az SQL Server IaaS Ügynökbővítmény a következő felügyeleti feladatokat támogatja:

| Felügyeleti szolgáltatás | Leírás |
| --- | --- |
| **SQL Server automatikus biztonsági mentése** |Automatizálja a biztonsági mentések ütemezését az összes adatbázishoz az alapértelmezett példányhoz vagy az SQL Server [megfelelően telepített](virtual-machines-windows-sql-server-iaas-faq.md#administration) elnevezett példányához a virtuális gépen. További információt az [SQL Server automatikus biztonsági mentése az Azure virtuális gépeken (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)című témakörben talál. |
| **SQL Server automatikus javítás** |Konfigurálja a karbantartási időszakot, amelynek során a virtuális gép fontos Windows-frissítései elhelyezhetőek, így elkerülheti a frissítéseket a munkaterhelés csúcsidején. További információ: [Automatikus javítás az SQL Server hez az Azure virtuális gépeken (Resource Manager) című témakörben.](virtual-machines-windows-sql-automated-patching.md) |
| **Azure Key Vault-integráció** |Lehetővé teszi az Azure Key Vault automatikus telepítését és konfigurálását az SQL Server virtuális gépén. További információ: [Az Azure Key Vault-integráció konfigurálása az SQL Server hez az Azure virtuális gépeken (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md)című témakörben. |

Az SQL Server Iaas Agent Extension telepítése és futtatása után elérhetővé teszi a felügyeleti funkciókat:

* Az Azure Portalon található virtuális gép SQL Server paneljén és az Azure PowerShellen keresztül az Azure Marketplace-en az SQL Server-lemezképeken keresztül.
* Az Azure PowerShell a bővítmény manuális telepítése. 

## <a name="prerequisites"></a>Előfeltételek
Az SQL Server IaaS Agent Extension használatának követelményei a virtuális gépen:

**Operációs rendszer**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server verzió:**

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell:**

* [A legújabb Azure PowerShell-parancsok letöltése és konfigurálása](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Telepítés
Az SQL Server IaaS-bővítmény akkor települ, amikor regisztrálja az SQL Server virtuális gépet az [SQL VM erőforrás-szolgáltatónál.](virtual-machines-windows-sql-register-with-resource-provider.md) Szükség esetén manuálisan telepítheti az SQL Server IaaS-ügynököt az alábbi PowerShell-paranccsal: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> A bővítmény telepítése újraindítja az SQL Server szolgáltatást. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Telepítés virtuális gépre egyetlen nevű SQL Server-példánysal
Az SQL Server IaaS-bővítmény egy elnevezett példánysal működik az SQL Server kiszolgálón, ha az alapértelmezett példányt eltávolítja, és újratelepíti az IaaS-bővítményt.

Az SQL Server névvel ellátott példányának használatához hajtsa végre az alábbi lépéseket:
   1. SQL Server virtuális gép üzembe helyezése az Azure Marketplace-ről. 
   1. Távolítsa el az IaaS-bővítményt az [Azure Portalról.](https://portal.azure.com)
   1. Távolítsa el teljesen az SQL Server kiszolgálót az SQL Server virtuális gépén belül.
   1. Telepítse az SQL Server t egy elnevezett példánysal az SQL Server virtuális gépen belül. 
   1. Telepítse az IaaS-bővítményt az Azure Portalról.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Az SQL Server IaaS-bővítmény állapotának beszereznie
A bővítmény telepítése az egyik módja az ügynök állapotának megtekintése az Azure Portalon. Válassza a Minden **beállítás lehetőséget** a virtuális gép ablakában, majd válassza a **Bővítmények**lehetőséget. Meg kell jelennie az **SqlIaasExtension** bővítmény szerepel.

![Az SQL Server IaaS-ügynökbővítmény állapota az Azure Portalon](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

A **Get-AzVMSqlServerExtension** Azure PowerShell-parancsmag is használható:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Az előző parancs megerősíti, hogy az ügynök telepítve van, és általános állapotinformációkat ad. Az automatikus biztonsági mentéssel és javítással kapcsolatos konkrét állapotinformációkat a következő parancsokkal kaphatja meg:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Eltávolítása
Az Azure Portalon eltávolíthatja a bővítményt a három pont kiválasztásával a virtuális gép **tulajdonságainak Bővítmények** ablakában. Ezután válassza a **Törlés** elemet.

![Az SQL Server IaaS Agent Extension eltávolítása az Azure Portalon](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Az **Remove-AzVMSqlServerExtension** PowerShell parancsmag is használható:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>További lépések
Kezdje el használni a bővítmény által támogatott szolgáltatások egyikét. További információt a cikk [Támogatott szolgáltatások](#supported-services) című szakaszában hivatkozott cikkekben talál.

Az SQL Server Azure virtuális gépeken való futtatásáról a [Mi az SQL Server az Azure virtuális gépeken című témakörben talál](virtual-machines-windows-sql-server-iaas-overview.md)további információt.
