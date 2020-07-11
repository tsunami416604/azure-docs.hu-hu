---
title: Az Azure Virtual Machines SQL Server dokumentációjának változásai | Microsoft Docs
description: Ismerje meg az Azure Virtual Machines SQL Server új funkcióit és tökéletesítéseit.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 9f41cb11960dbe9487231f14b005376261075a83
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231505"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Az Azure-beli SQL Server dokumentációjának változásai Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Az Azure-ban a virtuális gépek (VM-EK) üzembe helyezését SQL Server beépített lemezképével végezheti el. Ez a cikk az új funkciókkal és fejlesztésekkel kapcsolatos dokumentációs módosításokat összegzi az [Azure Virtual Machines SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)legújabb kiadásaiban. 

## <a name="july-2020"></a>2020. július


| Változások | Részletek |
| --- | --- |
| **Napló átmigrálása Ultra diskre** | Megtudhatja, hogyan [telepítheti át a naplófájlt egy ultra-lemezre](storage-migrate-to-ultradisk.md) a nagy teljesítmény és az alacsony késés érdekében. | 




## <a name="june-2020"></a>2020. június


| Változások | Részletek |
| --- | --- |
| **Elosztott hálózat neve (DNN)** | A Windows Server 2016-ban a SQL Server 2019-es verzióban mostantól megtekinthető a feladatátvételi fürt példányának (verzió) útválasztási forgalmának támogatása [, nem pedig](hadr-distributed-network-name-dnn-configure.md) Azure Load Balancer. Ez a támogatás leegyszerűsíti és egyszerűsíti a magas rendelkezésre állású (HA) megoldáshoz való csatlakozást az Azure-ban. | 
| **És Azure-beli megosztott lemezekkel** | Most már lehetséges a [feladatátvevő fürt példányának (](failover-cluster-instance-overview.md) verziójának) üzembe helyezése az Azure-beli [megosztott lemezek](failover-cluster-instance-azure-shared-disks-manually-configure.md) használatával SQL Server 2019-mel egy Windows Server 2016 + virtuális gépen. |
| **Átszervezett** | Az Azure-beli [virtuális gépeken SQL Serverokkal rendelkező feladatátvevő fürt példányaira](failover-cluster-instance-overview.md) vonatkozó dokumentációt a rendszer újraírta és átrendezte az érthetőség kedvéért. Elválasztottunk néhány konfigurációs tartalmat, például a [fürtkonfiguráció ajánlott eljárásait](hadr-cluster-best-practices.md), a [virtuális gépek előkészítését egy SQL Server](failover-cluster-instance-prepare-vm.md)és a [Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md)konfigurálásának módját. | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>2020. május 

| Változások | Részletek |
| --- | --- |
| **Azure SQL-család** | Az Azure Virtual Machines SQL Server már része az [Azure SQL termékcsaládnak](../../azure-sql-iaas-vs-paas-what-is-overview.md). Tekintse meg az [új arculatot](../index.yml)! Semmi sem változott a termékben, de a dokumentáció célja, hogy megkönnyítse az Azure SQL-termék döntését. | 


## <a name="january-2020"></a>2020. január

| Változások | Részletek |
| --- | --- |
| **Azure Government támogatás** | Mostantól regisztrálhat SQL Server virtuális gépeket az SQL VM erőforrás-szolgáltatóval a [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) felhőben üzemeltetett virtuális gépekhez. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Változások | Részletek |
 --- | --- |
| **Ingyenes DR-replika az Azure-ban** | Ha rendelkezik frissítési [garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), az Azure-ban helyszíni SQL Server-példány esetén is üzemeltetheti a vész-helyreállítási [ingyenes passzív példányt](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) . | 
| **Tömeges erőforrás-szolgáltató regisztrációja** | Mostantól [tömegesen regisztrálhat](sql-vm-resource-provider-bulk-register.md) SQL Server virtuális gépeket az erőforrás-szolgáltatóval. | 
|**Teljesítményre optimalizált tárolási konfiguráció** | Most már [teljes mértékben testreszabhatja a tárolási konfigurációt](storage-configuration.md#new-vms) új SQL Server VM létrehozásakor. |
|**Prémium fájlmegosztás a következőhöz:.** | Mostantól a [közvetlen tárolóhelyek](failover-cluster-instance-storage-spaces-direct-manually-configure.md)eredeti metódusa helyett létrehozhat egy feladatátvevő fürtöt a [prémium fájlmegosztás](failover-cluster-instance-premium-file-share-manually-configure.md) használatával. 
| **Azure Dedicated Host** | A SQL Server VM az [Azure dedikált gazdagépen](dedicated-host.md)futtathatja. | 
| **SQL Server VM Migrálás másik régióba** | A Azure Site Recovery használatával [áttelepítheti a SQL Server VM az egyik régióból a másikba](move-sql-vm-different-region.md). |
|  **Új SQL-IaaS telepítési módjai** | A SQL Server szolgáltatás újraindításának elkerülése érdekében mostantól a SQL Server IaaS-bővítményt [egyszerűsített módban](sql-server-iaas-agent-extension-automate-management.md) is telepítheti.  |
| **SQL Server kiadás módosítása** | Most már módosíthatja a SQL Server VM [kiadás tulajdonságát](change-sql-server-edition.md) . |
| **Az SQL virtuális gép erőforrás-szolgáltatójának módosításai** | A [SQL Server VM az SQL VM erőforrás-szolgáltatóban regisztrálhatja](sql-vm-resource-provider-register.md) az új SQL IaaS módok használatával. Ez a funkció [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes) rendszerképeket tartalmaz.|
| **Saját licencű lemezképek a Azure Hybrid Benefit használatával** | Az Azure Marketplace-ről üzembe helyezett saját licencű lemezképek mostantól az utólagos elszámolású [licencre](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)válthatnak.| 
| **Új SQL Server VM-kezelés a Azure Portal** | Most már lehetőség van a SQL Server VM kezelésére a Azure Portal. További információ: [SQL Server virtuális gépek kezelése a Azure Portalban](manage-sql-vm-portal.md).  | 
| **Kiterjesztett támogatás a SQL Server 2008 és a 2008 R2 rendszerhez** | *Az Azure* -beli virtuális gépekre való áttelepítéssel bővítheti SQL Server 2008 és SQL Server 2008 R2 [támogatását](sql-server-2008-extend-end-of-support.md) . | 
| **Egyéni rendszerkép-támogatás** | Most már telepítheti az [SQL Server IaaS-bővítményt](sql-server-iaas-agent-extension-automate-management.md#installation) az egyéni operációs rendszerre és a SQL Server lemezképekre, amely a [rugalmas licencelés](licensing-model-azure-hybrid-benefit-ahb-change.md)korlátozott funkcionalitását kínálja. Ha az egyéni rendszerképet az SQL VM erőforrás-szolgáltatóval regisztrálja, adja meg a licenc típusát "AHUB" néven. Ellenkező esetben a regisztráció sikertelen lesz. | 
| **Megnevezett példányok támogatása** | Mostantól a [SQL Server IaaS bővítményt](sql-server-iaas-agent-extension-automate-management.md#installation) megnevezett példánnyal is használhatja, ha az alapértelmezett példány eltávolítása megfelelő volt. | 
| **Portál továbbfejlesztése** | A SQL Server VM üzembe helyezésének Azure Portali élményét a használhatóság javítása érdekében kialakítjuk. További információkért tekintse meg [a rövid útmutatót, valamint](sql-vm-create-portal-quickstart.md) a SQL Server VM üzembe helyezésének részletes [leírását](create-sql-vm-portal.md) .|
| **Portál fejlesztése** | Mostantól lehetőség van egy SQL Server VM licencelési modelljének módosítására, ha a saját licencét a [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider)használatával szeretné felvenni.|
| **A rendelkezésre állási csoport központi telepítésének egyszerűsítése az Azure CLI-n keresztüli SQL Server VM** | Mostantól minden eddiginél könnyebben üzembe helyezhet egy rendelkezésre állási csoportot az Azure-SQL Server VM. Az [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) használatával létrehozhatja a Windows feladatátvevő fürtöt, a belső terheléselosztó és a rendelkezésre állási csoport figyelőit a parancssorból. További információ: az Azure [CLI használata az Always On rendelkezésre állási csoport konfigurálásához SQL Server Azure-beli virtuális gépen](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Változások | Részletek |
| --- | --- |
|  **Új erőforrás-szolgáltató SQL Server-fürthöz** | Az új erőforrás-szolgáltató (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) a Windows feladatátvevő fürt metaadatait határozza meg. SQL Server VM csatlakoztatása a *SqlVirtualMachineGroups* a Windows Server feladatátvételi fürt (WSFC) szolgáltatáshoz csatlakozik, és csatlakoztatja a virtuális gépet a fürthöz.  |
| **Rendelkezésre állási csoport üzembe helyezésének automatikus beállítása az Azure Gyorsindítás sablonjaival** |Most már lehetséges a Windows feladatátvevő fürt létrehozása, csatlakoztatás SQL Server virtuális gépekhez, a figyelő létrehozása és a belső terheléselosztó konfigurálása két Azure-beli rövid útmutató-sablon használatával. További információt az Azure-beli virtuális gépeken [elérhető SQL Server Azure-beli Gyorsindítás sablonokkal való konfigurálását](availability-group-quickstart-template-configure.md)ismertető témakörben talál. | 
| **Automatikus regisztráció az SQL VM erőforrás-szolgáltatóhoz** | SQL Server a hónap után üzembe helyezett virtuális gépek automatikusan regisztrálva lesznek az új SQL VM erőforrás-szolgáltatónál. SQL Server a hónap előtt üzembe helyezett virtuális gépeket is manuálisan kell regisztrálni. További információ: [SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval](sql-vm-resource-provider-register.md).|
|**Új SQL virtuális gép erőforrás-szolgáltatója** |  Az új erőforrás-szolgáltató (Microsoft. SqlVirtualMachine) jobb felügyeletet biztosít a SQL Server virtuális gépeken. A virtuális gépek regisztrálásával kapcsolatos további információkért lásd: [SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval](sql-vm-resource-provider-register.md). |
|**Licencelési modell váltása** | Az Azure CLI vagy a PowerShell használatával mostantól válthat a díjköteles használat és a saját licencek között a SQL Server VMhoz. További információkért lásd: [SQL Server virtuális gép licencelési modelljének módosítása az Azure-ban](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>További források

**Windows rendszerű virtuális gépek**:

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server kiépítése Windows rendszerű virtuális gépen](create-sql-vm-portal.md)
* [Adatbázis áttelepítése Azure-beli virtuális gépen futó SQL Serverre](migrate-to-vm-from-sql-server.md)
* [Magas rendelkezésre állás és vész-helyreállítási SQL Server az Azure-ban Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Az Azure-beli SQL Server teljesítményének bevált eljárásai Virtual Machines](performance-guidelines-best-practices.md)
* [Alkalmazási minták és fejlesztési stratégiák az Azure-beli SQL Serverhoz Virtual Machines](application-patterns-development-strategies.md)

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gépen lévő SQL Server áttekintése](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [SQL Server kiépítése Linux rendszerű virtuális gépen](../linux/sql-vm-create-portal-quickstart.md)
* [Gyakori kérdések (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server on Linux dokumentáció](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
