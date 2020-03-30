---
title: Dokumentációváltozások az SQL Server számára az Azure virtuális gépeken| Microsoft dokumentumok
description: Ismerje meg az SQL Server új funkcióit és fejlesztéseit egy Azure virtuális gépen
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201645"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Az SQL Server dokumentációjának módosításai az Azure virtuális gépeken

Az Azure lehetővé teszi egy virtuális gép (VM) üzembe helyezését az SQL Server beépített rendszerképével. Ez a cikk összefoglalja az [SQL Server azure-beli virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/sql-server/)kiadott új funkciókkal és fejlesztésekkel kapcsolatos dokumentációs változásokat. 


## <a name="january-2020"></a>2020. január

| Változások | Részletek |
| --- | --- |
| **Az Azure Government támogatása** | Most már regisztrálhatja az SQL Server virtuális gépeket az Sql VM erőforrás-szolgáltatóval az [Azure Government-felhőben](https://azure.microsoft.com/global-infrastructure/government/) üzemeltetett virtuális gépekhez. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Változások | Részletek |
 --- | --- |
| **Ingyenes DR-replika az Azure-ban** | A helyszíni SQL Server-példányhoz [ingyenes passzív példányt](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) üzemeltethet az Azure-ban, ha [frissítési garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)rendelkezik. | 
| **Tömeges erőforrás-szolgáltató regisztrációja** | Most [már tömegesen regisztrálhatja](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) az SQL virtuális gépeket az erőforrás-szolgáltatónál. | 
|**Teljesítményre optimalizált tárterület konfigurációja** | Most [már teljes mértékben testreszabhatja a tárolási konfigurációt,](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) amikor új SQL Server virtuális gép létrehozása. |
|**Prémium fájlmegosztás az FCI-hez** | Most már létrehozhat egy feladatátvevő fürtpéldányt [prémium szintű fájlmegosztással](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) a [Közvetlen tárolóhelyek](virtual-machines-windows-portal-sql-create-failover-cluster.md)eredeti metódusa helyett. 
| **Dedikált Azure-házigazda** | Futtathatja az SQL Server virtuális gépét egy [Azure dedikált gazdagépen.](virtual-machines-windows-sql-dedicated-host.md) | 
| **SQL virtuális gép áthelyezése más területre** | Az Azure Site Recovery segítségével [migrálhatja az SQL Server virtuális gépét egyik régióból a másikba.](virtual-machines-windows-sql-move-different-region.md) |
|  **Új SQL IaaS telepítési módok** | Mostantól lehetőség van az SQL Server IaaS-bővítmény [könnyű üzemmódba](virtual-machines-windows-sql-server-agent-extension.md) való telepítésére, hogy elkerülje az SQL Server szolgáltatás újraindítását.  |
| **SQL Server kiadás módosítása** | Most már módosíthatja az SQL Server virtuális gép [kiadástulajdonságát.](virtual-machines-windows-sql-change-edition.md) |
| **Az SQL VM erőforrás-szolgáltató módosításai** | Az [SQL Server virtuális gép regisztrálhatja az SQL Virtuálisgép erőforrás-szolgáltató](virtual-machines-windows-sql-register-with-resource-provider.md) az új SQL IaaS-módok használatával. Ez a funkció [a Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) rendszerképeket is tartalmazza.|
| **Saját licenccel rendelkező képek megjelenítése az Azure Hybrid Benefit használatával** | Az Azure Marketplace-ről telepített saját licenccel rendelkező lemezképek mostantól átválthatnak [licenctípusukon a felosztó-kiosztó](virtual-machines-windows-sql-ahb.md#remarks)nasira.| 
| **Új SQL Server virtuálisgép-kezelés az Azure Portalon** | Most már van egy módja annak, hogy kezelje az SQL Server virtuális gép az Azure Portalon. További információ: [SQL Server virtuális gépek kezelése az Azure Portalon.](virtual-machines-windows-sql-manage-portal.md)  | 
| **Kiterjesztett támogatás az SQL Server 2008/2008 R2 rendszerhez** | Az SQL Server 2008 és az SQL Server 2008 R2 [támogatásának kiterjesztése](virtual-machines-windows-sql-server-2008-eos-extend-support.md) az Azure virtuális *gépekre* való áttelepítéssel. | 
| **Egyéni képtámogathatóság** | Most már telepítheti az [SQL Server IaaS bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) az egyéni operációs rendszer és az SQL lemezképekre, amely a [rugalmas licencelés](virtual-machines-windows-sql-ahb.md)korlátozott funkcionalitását kínálja. Amikor az egyéni lemezképet regisztrálja az SQL-erőforrás-szolgáltatónál, adja meg a licenc típusát "AHUB" néven. Ellenkező esetben a regisztráció sikertelen lesz. | 
| **Megnevezett példány támogatása** | Most már használhatja az [SQL Server IaaS-bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) egy elnevezett példánysal, ha az alapértelmezett példányt megfelelően eltávolította. | 
| **A portál fejlesztése** | Az Sql Server virtuális gép üzembe helyezéséhez az Azure Portalon az SQL Server virtuális gép telepítésének megújult a használhatóság javítása érdekében. További információt a rövid [rövid útmutató](quickstart-sql-vm-create-portal.md) és az SQL Server virtuális gép telepítéséhez [útmutatóban](virtual-machines-windows-portal-sql-server-provision.md) talál.|
| **A portál fejlesztése** | Mostantól az Azure Portal használatával módosíthatja az SQL Server virtuális gépek licencelési modelljét használatalapú fizetéssel [a](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider)saját licencre való szolgáltatásra.|
| **A rendelkezésre állási csoport telepítésének egyszerűsítése az Azure SQL Server VM CLI-vel** | Most már minden eddiginél egyszerűbben telepíthet egy rendelkezésre állási csoportot egy SQL Server virtuális gépre az Azure-ban. Az Azure [CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) segítségével létrehozhatja a Windows feladatátvételi fürt, belső terheléselosztó és a rendelkezésre állási csoport figyelői mind a parancssorból. További információ: [Az Azure SQL Server vm CLI használata az SQL Server mindig elérhető állási csoportjának konfigurálásához egy Azure virtuális gépen.](virtual-machines-windows-sql-availability-group-cli.md) | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Változások | Részletek |
| --- | --- |
|  **Új erőforrás-szolgáltató egy SQL Server-fürthöz** | Egy új erőforrás-szolgáltató (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) határozza meg a Windows feladatátvevő fürt metaadatait. Az SQL Server virtuális gép *sqlvirtualmachinegroups-hoz* való csatlakoztatása a Windows Server feladatátvevő fürt (WSFC) szolgáltatását, és a virtuális gépet a fürthöz csatlakoztatja.  |
| **A rendelkezésre állási csoport üzembe helyezésének automatikus beállítása az Azure rövid útmutató sablonjaival** |Most már létrehozhatja a Windows feladatátvevő fürtöt, csatlakozhat hozzá az SQL Server virtuális gépekhez, létrehozhatja a figyelőt, és konfigurálhatja a belső terheléselosztót két Azure-gyorsindítási sablonnal. További információ: [Az Azure gyorsindítási sablonjainak használata az SQL Server mindig elérhető állási csoportjának konfigurálásához egy Azure virtuális gépen című témakörben olvashat.](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 
| **Automatikus regisztráció az SQL Virtuálisgép-erőforrás-szolgáltatóhoz** | Az e hónap után telepített SQL Server virtuális gépek automatikusan regisztrálva vannak az új SQL Server erőforrás-szolgáltatónál. Az ebben a hónapban üzembe helyezett SQL Server virtuális gépeket manuálisan kell regisztrálni. További információ: [SQL Server virtuális gép regisztrálása az Azure-ban az SQL vm erőforrás-szolgáltatóval.](virtual-machines-windows-sql-register-with-resource-provider.md)|
|**Új SQL VM erőforrás-szolgáltató** |  Egy új erőforrás-szolgáltató (Microsoft.SqlVirtualMachine) biztosítja az SQL Server virtuális gépek jobb kezelését. A virtuális gépek regisztrálásáról további információt az [SQL Server virtuális gép regisztrálása az Azure-ban az SQL virtuálisgép-erőforrás-szolgáltatóval](virtual-machines-windows-sql-register-with-resource-provider.md)című témakörben talál. |
|**Licencelési modell váltása** | Most már válthat a pay-per-usage és bring-your-own-license modellek az SQL Server virtuális gép az Azure CLI vagy a PowerShell használatával. További információ: [Az SQL Server virtuális gépek licencelési modelljének módosítása az Azure-ban](virtual-machines-windows-sql-ahb.md)című témakörben talál. | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>További források

**Windows virtuális gépek**:

* [Az SQL Server áttekintése Windows virtuális gépen](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM kiépítése](virtual-machines-windows-portal-sql-server-provision.md)
* [Adatbázis áttelepítése az SQL Serverkiszolgálóra Egy Azure virtuális gépen](virtual-machines-windows-migrate-sql.md)
* [Magas rendelkezésre állás és vészhelyreállítás az SQL Server számára az Azure virtuális gépeken](virtual-machines-windows-sql-high-availability-dr.md)
* [Az SQL Server teljesítményre vonatkozó gyakorlati tanácsai az Azure virtuális gépeken](virtual-machines-windows-sql-performance.md)
* [Alkalmazásminták és fejlesztési stratégiák az SQL Server hez az Azure virtuális gépeken](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux virtuális gépek**:

* [Az SQL Server áttekintése Linux os virtuális gépen](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux virtuális gép kiépítése](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Gyakori kérdések (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server Linux on dokumentáció](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
