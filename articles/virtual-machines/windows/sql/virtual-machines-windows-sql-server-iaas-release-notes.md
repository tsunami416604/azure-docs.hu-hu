---
title: Az Azure Virtual Machines SQL Server dokumentációjának változásai | Microsoft Docs
description: Ismerkedjen meg az Azure-beli virtuális gépek SQL Serverjának új szolgáltatásaival és tökéletesítésével
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
ms.openlocfilehash: b9d2b3916e4509e5ce3aa8a9ddd66f1cf14a8827
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690874"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Az Azure-beli SQL Server dokumentációjának változásai Virtual Machines

Az Azure-ban a virtuális gépek (VM-EK) üzembe helyezését SQL Server beépített lemezképével végezheti el. Ez a cikk az új funkciókkal és fejlesztésekkel kapcsolatos dokumentációs módosításokat összegzi az [Azure Virtual Machines SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)legújabb kiadásaiban. 


## <a name="january-2020"></a>Január 2020

| Változások | Részletek |
| --- | --- |
| **Azure Government támogatás** | Mostantól regisztrálhat SQL Server virtuális gépeket az SQL VM erőforrás-szolgáltatóval a [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) felhőben üzemeltetett virtuális gépekhez. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Változások | Részletek |
 --- | --- |
| **Ingyenes DR-replika az Azure-ban** | Ha rendelkezik frissítési [garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), az Azure-ban helyszíni SQL Server-példány esetén is üzemeltetheti a vész-helyreállítási [ingyenes passzív példányt](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) . | 
| **Tömeges erőforrás-szolgáltató regisztrációja** | Mostantól [tömegesen regisztrálhatja](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) az SQL-alapú virtuális gépeket az erőforrás-szolgáltató használatával. | 
|**Teljesítményre optimalizált tárolási konfiguráció** | Most már [teljes mértékben testreszabhatja a tárolási konfigurációt](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) új SQL Server VM létrehozásakor. |
|**Prémium fájlmegosztás a következőhöz:.** | Mostantól a [közvetlen tárolóhelyek](virtual-machines-windows-portal-sql-create-failover-cluster.md)eredeti metódusa helyett egy [prémium fájlmegosztás](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) használatával is létrehozhat egy feladatátvevő fürtöt. 
| **Dedikált Azure-gazdagép** | A SQL Server VM a [dedikált Azure-gazdagépen](virtual-machines-windows-sql-dedicated-host.md)is futtathatja. | 
| **SQL virtuális gép áthelyezése másik régióba** | A Azure Site Recovery használatával [áttelepítheti a SQL Server VM az egyik régióból a másikba](virtual-machines-windows-sql-move-different-region.md). |
|  **Új SQL-IaaS telepítési módjai** | A SQL Server szolgáltatás újraindításának elkerülése érdekében mostantól a SQL Server IaaS-bővítményt [egyszerűsített módban](virtual-machines-windows-sql-server-agent-extension.md) is telepítheti.  |
| **SQL Server kiadás módosítása** | Most már módosíthatja a SQL Server VM [kiadás tulajdonságát](virtual-machines-windows-sql-change-edition.md) . |
| **Az SQL virtuális gép erőforrás-szolgáltatójának módosításai** | A [SQL Server VM az SQL VM erőforrás-szolgáltatóban regisztrálhatja](virtual-machines-windows-sql-register-with-resource-provider.md) az új SQL IaaS módok használatával. Ez a funkció [Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) rendszerképeket tartalmaz.|
| **Saját licencű lemezképek a Azure Hybrid Benefit használatával** | Az Azure Marketplace-ről üzembe helyezett saját licencű lemezképek mostantól az utólagos elszámolású [licencre](virtual-machines-windows-sql-ahb.md#remarks)válthatnak.| 
| **Új SQL Server VM-kezelés a Azure Portal** | Most már lehetőség van a SQL Server VM kezelésére a Azure Portal. További információ: [SQL Server virtuális gépek kezelése a Azure Portalban](virtual-machines-windows-sql-manage-portal.md).  | 
| **Kiterjesztett támogatás a SQL Server 2008/2008 R2 rendszerhez** | *Az Azure* -beli virtuális gépekre való áttelepítéssel bővítheti SQL Server 2008 és SQL Server 2008 R2 [támogatását](virtual-machines-windows-sql-server-2008-eos-extend-support.md) . | 
| **Egyéni rendszerkép-támogatás** | Most már telepítheti a [SQL Server IaaS bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) az egyéni operációs rendszerre és az SQL-lemezképekre, amely a [rugalmas licencelés](virtual-machines-windows-sql-ahb.md)korlátozott funkcionalitását kínálja. Ha az egyéni rendszerképét az SQL-erőforrás-szolgáltatóval regisztrálja, adja meg a licenc típusát "AHUB" néven. Ellenkező esetben a regisztráció sikertelen lesz. | 
| **Megnevezett példányok támogatása** | Mostantól a [SQL Server IaaS bővítményt](virtual-machines-windows-sql-server-agent-extension.md#installation) megnevezett példánnyal is használhatja, ha az alapértelmezett példány eltávolítása megfelelő volt. | 
| **Portál továbbfejlesztése** | A SQL Server VM üzembe helyezésének Azure Portali élményét a használhatóság javítása érdekében kialakítjuk. További információkért tekintse meg [a rövid útmutatót, valamint](quickstart-sql-vm-create-portal.md) a SQL Server VM üzembe helyezésének részletes [leírását](virtual-machines-windows-portal-sql-server-provision.md) .|
|  **Portál fejlesztése** | Mostantól lehetőség van egy SQL Server VM licencelési modelljének módosítására, ha a saját licencét a [Azure Portal](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider)használatával szeretné felvenni.|
| **A rendelkezésre állási csoport központi telepítésének egyszerűsítése az Azure SQL Server VM parancssori felülettel** | Mostantól minden eddiginél könnyebben üzembe helyezhet egy rendelkezésre állási csoportot az Azure-SQL Server VM. Az [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) használatával létrehozhatja a Windows feladatátvevő fürtöt, a belső terheléselosztó és a rendelkezésre állási csoport figyelőit a parancssorból. További információ: [az azure SQL Server VM CLI használata az Always On rendelkezésre állási csoport konfigurálásához az Azure-beli virtuális](virtual-machines-windows-sql-availability-group-cli.md)gépeken való SQL Serverhoz. | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Változások | Részletek |
| --- | --- |
|  **Új erőforrás-szolgáltató SQL Server-fürthöz** | Az új erőforrás-szolgáltató (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) a Windows feladatátvevő fürt metaadatait határozza meg. SQL Server VM csatlakoztatása a *SqlVirtualMachineGroups* a Windows Server feladatátvételi fürt (WSFC) szolgáltatáshoz csatlakozik, és csatlakoztatja a virtuális gépet a fürthöz.  |
| **Rendelkezésre állási csoport üzembe helyezésének automatikus beállítása az Azure Gyorsindítás sablonjaival** |Most már lehetséges a Windows feladatátvevő fürt létrehozása, csatlakoztatás SQL Server virtuális gépekhez, a figyelő létrehozása és a belső terheléselosztó konfigurálása két Azure-beli gyors üzembe helyezési sablonnal. További információt az Azure-beli virtuális gépeken [elérhető SQL Server Azure-beli Gyorsindítás sablonokkal való konfigurálását](virtual-machines-windows-sql-availability-group-quickstart-template.md)ismertető témakörben talál. | 
| **Automatikus regisztráció az SQL VM erőforrás-szolgáltatóhoz** | SQL Server a hónap után üzembe helyezett virtuális gépek automatikusan regisztrálva lesznek az új SQL Server erőforrás-szolgáltatónál. SQL Server a hónap előtt üzembe helyezett virtuális gépeket is manuálisan kell regisztrálni. További információ: [SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval](virtual-machines-windows-sql-register-with-resource-provider.md).|
|**Új SQL virtuális gép erőforrás-szolgáltatója** |  Az új erőforrás-szolgáltató (Microsoft. SqlVirtualMachine) jobb felügyeletet biztosít a SQL Server virtuális gépeken. A virtuális gépek regisztrálásával kapcsolatos további információkért lásd: [SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Licencelési modell váltása** | Az Azure CLI vagy a PowerShell használatával mostantól válthat a díjköteles használat és a saját licencek között a SQL Server VMhoz. További információkért lásd: [SQL Server virtuális gép licencelési modelljének módosítása az Azure-ban](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>További források

**Windows rendszerű virtuális gépek**:

* [Windows rendszerű virtuális gépek SQL Server áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows rendszerű virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md)
* [Adatbázis migrálása SQL Server Azure-beli virtuális gépen](virtual-machines-windows-migrate-sql.md)
* [Magas rendelkezésre állás és vész-helyreállítási SQL Server az Azure-ban Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Az Azure-beli SQL Server teljesítményének bevált eljárásai Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Alkalmazási minták és fejlesztési stratégiák az Azure-beli SQL Serverhoz Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gépen lévő SQL Server áttekintése](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux rendszerű virtuális gép kiépítése](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Gyakori kérdések (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [A Linux rendszeren futó SQL Server dokumentációja](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
