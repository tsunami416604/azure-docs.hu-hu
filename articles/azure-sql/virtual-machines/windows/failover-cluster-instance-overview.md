---
title: Feladatátvevő fürt példányai
description: Ismerkedjen meg a feladatátvevő fürtök példányaival (FCIs) az Azure Virtual Machines SQL Serverával.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 00c9482eab74003f6a667d52440d4cb6dd21fcfc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287367"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Feladatátvevő fürt példányai SQL Server az Azure-ban Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk a (z) Azure Virtual Machines (VM) SQL Server való használata esetén a feladatátvételi fürtszolgáltatási példányok (verzió) használatát mutatja be. 

## <a name="overview"></a>Áttekintés

SQL Server az Azure-beli virtuális gépeken a Windows Server feladatátvételi fürtszolgáltatás (WSFC) funkciója a helyi magas rendelkezésre állást biztosítja a redundancia révén a kiszolgálói példány szintjén: a feladatátvevő fürt példánya. Az egy-egy SQL Server-példány, amely a WSFC (vagy egyszerűen a fürt) csomópontokon, illetve valószínűleg több alhálózaton is telepítve van. A hálózaton úgy tűnik, hogy az egy adott számítógépen futó SQL Server példánya lesz. Az WSFC azonban az egyik csomópontról a másikra történő feladatátvételt biztosít, ha az aktuális csomópont elérhetetlenné válik.

A cikk további része a feladatátvevő fürt példányainak a SQL Server Azure-beli virtuális gépeken való használatakor felhasználható különbségekre koncentrál. A feladatátvételi fürtszolgáltatás technológiával kapcsolatos további tudnivalókért tekintse meg a következő témakört: 

- [Windows-fürtök technológiái](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server feladatátvevő fürt példányai](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Kvórumerőforrás

A feladatátvevő fürtök példányai az Azure-SQL Serverokkal Virtual Machines támogatják a tanúsító lemez, a Felhőbeli tanú vagy egy tanúsító fájlmegosztás használatát a fürt kvóruma számára.

További információ: az [Azure-beli SQL Server virtuális gépekkel kapcsolatos ajánlott eljárások](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Storage

A hagyományos helyszíni fürtözött környezetekben a Windows feladatátvevő fürt a Tárolóhálózati (SAN) tárolót használja, amelyet mindkét csomópont a megosztott tárolóként is elérhet. SQL Server fájlok vannak tárolva a megosztott tárolóban, és csak az aktív csomópont fér hozzá egyszerre a fájlokhoz. 

Az Azure-beli virtuális gépeken SQL Server különböző lehetőségeket kínál megosztott tárolási megoldásként SQL Server feladatátvevő fürt példányainak üzembe helyezéséhez: 

||[Azure megosztott lemezek](../../../virtual-machines/windows/disks-shared.md)|[Prémium fájlmegosztás](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[Közvetlen tárolóhelyek (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Operációs rendszer minimális verziója**| Mind |Windows Server 2012|Windows Server 2016|
|**Minimális SQL Server-verzió**|Mind|SQL Server 2012|SQL Server 2016|
|**Támogatott virtuális gépek rendelkezésre állása** |Rendelkezésre állási csoportok Proximity elhelyezési csoportokkal |Rendelkezésre állási készletek és rendelkezésre állási zónák|Rendelkezésre állási csoportok |
|**A FileStream támogatása**|Igen|Nem|Igen |
|**Azure BLOB-gyorsítótár**|Nem|Nem|Igen|

A szakasz további része felsorolja az Azure-beli virtuális gépeken SQL Server számára elérhető egyes tárolási lehetőségek előnyeit és korlátozásait. 

### <a name="azure-shared-disks"></a>Azure megosztott lemezek

Az [Azure Shared Disks](../../../virtual-machines/windows/disks-shared.md) az [Azure Managed Disks](../../../virtual-machines/windows/managed-disks-overview.md)szolgáltatás. A Windows Server feladatátvételi fürtszolgáltatás támogatja az Azure-beli megosztott lemezek feladatátvevő fürt-példánnyal való használatát. 

**Támogatott operációs rendszer**: mind   
**Támogatott SQL-verzió**: ALL     

**Előnyök**: 
- Hasznos az Azure-ba migrálni kívánt alkalmazások számára, miközben a magas rendelkezésre állást és a vész-helyreállítási (HADR) architektúrát is megtartja. 
- Fürtözött alkalmazásokat telepíthet át az Azure-ba a SCSI-állandó lefoglalások (SCSI PR) támogatása miatt. 
- Támogatja a közös Azure-prémium SSD a SQL Server összes verziójának és a megosztott Azure Ultra Disk Storage SQL Server 2019-es verziójával. 
- Egyetlen megosztott lemezt vagy több megosztott lemezt is használhat egy megosztott tároló létrehozásához. 
- Támogatja a FileStream.


**Korlátozások**: 
- A virtuális gépeket ugyanahhoz a rendelkezésre állási csoporthoz és közelségi helyhez kell helyezni.
- A rendelkezésre állási zónák nem támogatottak.
- Prémium SSD lemez gyorsítótárazása nem támogatott.
 
Első lépésként tekintse meg [SQL Server feladatátvevő fürt példányát az Azure Shared Disks szolgáltatással](failover-cluster-instance-azure-shared-disks-manually-configure.md). 

### <a name="storage-spaces-direct"></a>Tárolóhelyek – Közvetlen

A [közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) egy olyan Windows Server-szolgáltatás, amely az Azure Virtual Machines feladatátvételi fürtszolgáltatását támogatja. Egy szoftveres virtuális SAN-t biztosít.

**Támogatott operációs rendszer**: Windows Server 2016 és újabb verziók   
**Támogatott SQL-verzió**: SQL Server 2016-es és újabb verziók   


**Előnyei** 
- A megfelelő hálózati sávszélesség lehetővé teszi a robusztus és nagy teljesítményű megosztott tárolási megoldás használatát. 
- Támogatja az Azure Blob cache-t, így az olvasások helyileg is kiszolgálható a gyorsítótárból. (A frissítések egyszerre lesznek replikálva mindkét csomópontra.) 
- Támogatja a FileStream. 

**Korlátozások**
- Csak a Windows Server 2016-es és újabb verzióiban érhető el. 
- A rendelkezésre állási zónák nem támogatottak.
- Ugyanazt a lemezterületet igényli mindkét virtuális géphez csatlakoztatva. 
- A folyamatban lévő lemezes replikáció miatt magas hálózati sávszélesség szükséges a nagy teljesítmény eléréséhez. 
- Nagyobb méretű virtuálisgép-méretet igényel, és a tároláshoz dupla díjat kell fizetnie, mivel az egyes virtuális gépekhez tárterület van csatolva. 

Első lépésként tekintse meg [a SQL Server feladatátvevő fürt példányát a közvetlen tárolóhelyek](failover-cluster-instance-azure-shared-disks-manually-configure.md)használatával. 

### <a name="premium-file-share"></a>Prémium fájlmegosztás

A [prémium szintű fájlmegosztás](../../../storage/files/storage-how-to-create-premium-fileshare.md) a [Azure Files](../../../storage/files/index.yml)egyik funkciója. A prémium szintű fájlmegosztás SSD-támogatással rendelkezik, és következetesen alacsony késéssel rendelkezik. Ezek teljes mértékben támogatottak a Windows Server 2012-es vagy újabb verzióiban SQL Server 2012-es vagy újabb feladatátvevő fürt példányaival. A prémium fájlmegosztás nagyobb rugalmasságot biztosít, mivel leállás nélkül átméretezheti és méretezheti a fájlmegosztást.

**Támogatott operációs rendszer**: Windows Server 2012 és újabb verziók   
**Támogatott SQL-verzió**: SQL Server 2012-es és újabb verziók   

**Előnyei** 
- Csak megosztott tárolási megoldás a virtuális gépekhez több rendelkezésre állási zónában elosztva. 
- Teljes körűen felügyelt fájlrendszer egyetlen számjegyből álló késéssel és a betört I/O-teljesítménnyel. 

**Korlátozások**
- Csak a Windows Server 2012-es és újabb verzióiban érhető el. 
- A FileStream nem támogatott. 


Első lépésként tekintse [meg a prémium szintű fájlmegosztás SQL Server feladatátvevő fürt példányát](failover-cluster-instance-premium-file-share-manually-configure.md). 

### <a name="partner"></a>Partner

A támogatott tárterülethez partner-fürtszolgáltatási megoldások tartoznak. 

**Támogatott operációs rendszer**: mind   
**Támogatott SQL-verzió**: ALL   

Az egyik példa a SIOS DataKeeper használja tárolóként. További információ: a blogbejegyzés [feladatátvételi fürtszolgáltatása és a SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI-és ExpressRoute

Az iSCSI-tárolók megosztott blokkos tárolóit az Azure ExpressRoute is elérhetővé teheti. 

**Támogatott operációs rendszer**: mind   
**Támogatott SQL-verzió**: ALL   

Például a NetApp Private Storage (NPS) egy iSCSI-célt tesz elérhetővé az ExpressRoute-n keresztül az Azure-beli virtuális gépek Equinix.

A Microsoft-partnerek megosztott tárolási és adatreplikációs megoldásaiért forduljon a szállítóhoz a feladatátvételi adatokhoz való hozzáféréssel kapcsolatos esetleges problémákhoz.

## <a name="connectivity"></a>Kapcsolatok

Az Azure-SQL Serverokkal rendelkező feladatátvevő fürt példányai Virtual Machines egy [elosztott hálózati nevet (DNN)](hadr-distributed-network-name-dnn-configure.md) vagy egy [virtuális hálózati nevet (VNN)](hadr-vnn-azure-load-balancer-configure.md) használ a Azure Load Balancer használatával, hogy a forgalmat az SQL Server példányra irányítsa, függetlenül attól, hogy melyik csomópont tulajdonosa a fürtözött erőforrás. Bizonyos szolgáltatások és a DNN SQL Server-vel való használata esetén további szempontokat is figyelembe kell venni. További információért lásd: [DNN együttműködés a SQL Server](failover-cluster-instance-dnn-interoperability.md) -os-vel. 

A fürt csatlakozási lehetőségeivel kapcsolatos további információkért lásd: [HADR-kapcsolatok továbbítása SQL Server Azure-beli virtuális gépeken](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Korlátozások

Vegye figyelembe az alábbi korlátozásokat a feladatátvevő fürt példányaihoz az Azure Virtual Machines SQL Server. 

### <a name="lightweight-resource-provider"></a>Egyszerűsített erőforrás-szolgáltató   
Jelenleg SQL Server az Azure-beli virtuális gépeken futó feladatátvevő fürtök példányai csak az [SQL Server IaaS-ügynök bővítmény](sql-server-iaas-agent-extension-automate-management.md) [egyszerűsített felügyeleti módjával](sql-vm-resource-provider-register.md#management-modes) támogatottak. Ha a teljes bővítmény módból egyszerűre szeretne váltani, törölje a megfelelő virtuális gépekhez tartozó **SQL** -virtuálisgép-erőforrást, majd az egyszerűsített módban regisztrálja őket az SQL VM erőforrás-szolgáltatóval. Ha a Azure Portal használatával törli az SQL-alapú **virtuális gép** erőforrását, törölje a megfelelő virtuális gép melletti jelölőnégyzet jelölését. 

A teljes bővítmény olyan funkciókat támogat, mint például az automatikus biztonsági mentés, a javítások és a speciális portálok kezelése. Ezek a funkciók nem fognak működni SQL Server virtuális gépeken, miután az ügynök újratelepítése egyszerűsített felügyeleti módban történik.

### <a name="msdtc"></a>MSDTC   
Az Azure Virtual Machines támogatja az MSDTC szolgáltatást a Windows Server 2019-ben a fürtözött megosztott kötetek (CSV) és az [Azure standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)tárolóval.

Az Azure Virtual Machines-on az MSDTC nem támogatott a Windows Server 2016-es vagy korábbi verzióiban, mert:

- A fürtözött MSDTC-erőforrás nem konfigurálható megosztott tároló használatára. Windows Server 2016 rendszeren, ha MSDTC-erőforrást hoz létre, az nem fog tudni használni megosztott tárterületet, még akkor sem, ha rendelkezésre áll tárterület. Ezt a problémát a Windows Server 2019-es verzióban javítottuk.
- Az alapszintű Load Balancer nem kezeli az RPC-portokat.


## <a name="next-steps"></a>További lépések

Tekintse át a [fürt konfigurációjának ajánlott eljárásait](hadr-cluster-best-practices.md), majd [készítse elő a SQL Server VMt a következőre:](failover-cluster-instance-prepare-vm.md). 

További információkért lásd: 

- [Windows-fürtök technológiái](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server feladatátvevő fürt példányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

