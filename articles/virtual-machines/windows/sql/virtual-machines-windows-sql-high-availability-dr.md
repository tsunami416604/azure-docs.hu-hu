---
title: Magas rendelkezésre álláshoz és Vészhelyreállításhoz az SQL Server |} Microsoft Docs
description: A különböző Azure virtuális gépeken futó SQL Server HADR stratégiák tárgyalja.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: e9b4ca959b93e097bb52a841cec02cc476ef5f48
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401259"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Magas rendelkezésre állás és vészhelyreállítás az Azure-beli SQL Server-alapú virtuális gépeken

A Microsoft Azure virtuális gépek (VM) az SQL Server segítségével egy magas rendelkezésre állási és vészhelyreállítási (HADR) helyreállítási adatbázis megoldás költségeinek csökkentése. A legtöbb SQL Server HADR megoldás az Azure virtuális gépeken, csak Azure- és hibrid megoldásként támogatottak. A csak Azure megoldás a teljes HADR rendszer fut, az Azure-ban. A hibrid konfiguráció a megoldás részét képező Azure-ban és a többi részben futtatása helyszíni a szervezet fut. Az Azure környezetbe rugalmasan lehetővé teszi a keret és az SQL Server adatbázis-rendszerek HADR követelményeinek kielégítéséhez Azure részben vagy teljesen áthelyezése.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>A szükséges HADR megoldás ismertetése
Esetén a biztosíthatja, hogy az adatbázis-rendszer rendelkezik a HADR képességeket, amelyek a szolgáltatásszint szerződéssel (SLA) szükséges. Az, hogy az Azure biztosít magas rendelkezésre állású mechanizmusok, például a felhőszolgáltatásokhoz és virtuális gépek helyreállítási hibaészlelés javító szolgáltatás maga nem garantálható, hogy is felel meg a kívánt szolgáltatásiszint-szerződést. Ezek a mechanizmusok a magas rendelkezésre állású virtuális gépek, de nem magas rendelkezésre állását a virtuális gépeken belül futó SQL Server védelme. Úgy is sikertelen lesz, amíg a virtuális gép online állapotú, és a megfelelő SQL Server-példányhoz. Továbbá akkor is igaz, az Azure által biztosított mechanizmusok lehetővé teszik a leállás miatt események például a szoftver vagy a hardver meghibásodása és az operációs rendszer frissítése a helyreállítási virtuális gépek magas rendelkezésre állású.

Ezenkívül földrajzi redundáns tárolás (GRS) az Azure, georeplikáció nevezett szolgáltatással segítségével valósul meg, amely nem lehet egy megfelelő vész-helyreállítási megoldást az adatbázisok esetében. A georeplikáció aszinkron módon elküldi az adatokat, mert a legutóbbi frissítések elveszhet katasztrófa esetén. Georeplikálási korlátozások kapcsolatos további tudnivalókért lásd: a [georeplikáció nem támogatott az adat- és naplófájlok külön lemezeken](#geo-replication-support) szakasz.

## <a name="hadr-deployment-architectures"></a>HADR telepítési architektúrák
Azure által támogatott SQL Server HADR a technológiák többek között:

* [Always On rendelkezésre állási csoportok](https://technet.microsoft.com/library/hh510230.aspx)
* [Mindig a Feladatátvevőfürt-példányok](https://technet.microsoft.com/library/ms189134.aspx)
* [Naplóküldés](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server biztonsági mentése és visszaállítása az Azure Blob Storage szolgáltatással](https://msdn.microsoft.com/library/jj919148.aspx)
* [Az adatbázis-tükrözés](https://technet.microsoft.com/library/ms189852.aspx) – az SQL Server 2016 elavult

Akkor lehet kombinálni a technológiák együttesen egy SQL Server megoldást, amely a magas rendelkezésre állású és vész-helyreállítási funkciók végrehajtásához. Attól függően, hogy a technológiát használja a hibrid telepítéséhez szükség lehet az Azure virtuális hálózathoz egy VPN-alagúton. Az alábbi szakaszok mutatják be a a példa telepítési architektúrák.

## <a name="azure-only-high-availability-solutions"></a>Csak Azure: magas rendelkezésre állású megoldások

Az SQL Server egy magas rendelkezésre állású megoldás lehet az Always On rendelkezésre állási csoportok - nevű rendelkezésre állási csoportok egy adatbázis szintjén. Is létrehozhat egy magas rendelkezésre állású megoldás egy példány szintjén az mindig a Feladatátvevőfürt-példányok - feladatátvevő fürtpéldányok. További redundancia jelentkezhet létrehozhat redundancia mindkét szinten létrehozásával rendelkezésre állási csoportok feladatátvevő fürtpéldányok. 

| Technológia | Példa architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Az Azure virtuális gépeken ugyanabban a régióban fut, a rendelkezésre állási másodpéldányok magas rendelkezésre állás biztosításához. Kell egy tartományvezérlő virtuális gép, mert a Windows feladatátvételi fürtszolgáltatás szükséges Active Directory-tartományhoz.<br/> ![Rendelkezésre állási csoportok](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>További információkért lásd: [rendelkezésre állási csoportok konfigurálása az Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Feladatátvevőfürt-példányok** |Feladatátvevő fürt példány (FCI), amely megosztott tárolóra van szükség, 3 különböző módon is létrehozható.<br/><br/>1. Egy két csomópontos feladatátvevő fürt csatlakoztatott tárolók használata az Azure virtuális gépeken futó [Windows Server 2016 közvetlen tárolóhelyek \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) arra, hogy egy szoftveres virtuális SAN.<br/><br/>2. Külső fürtözési megoldás által támogatott tárolóval az Azure virtuális gépeken futó kétcsomópontos feladatátvevő fürt. Például egy adott SIOS DataKeeper használó, lásd: [magas rendelkezésre állás a Feladatátvételi fürtszolgáltatás és a 3. fél szoftver SIOS DataKeeper fájlmegosztás](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Az Azure virtuális gépeken futó távoli iSCSI cél megosztott blokktárolást keresztül ExpressRoute kétcsomópontos feladatátvevő fürt. NetApp titkos tárolási (NPS) például iSCSI-tároló az Azure virtuális gépek Equinix ExpressRoute keresztül mutatja.<br/><br/>Külső megosztott tároló és a replikációs megoldás, lépjen kapcsolatba a forgalmazójával, ha feladatátvételi lévő adatok elérésével kapcsolatos problémát talál.<br/><br/>Vegye figyelembe, hogy használja a FCI [Azure File storage](https://azure.microsoft.com/services/storage/files/) még nem támogatott, mert ez a megoldás nem használja a prémium szintű Storage. Jelenleg dolgozunk, ez sokkal támogatásához. |

## <a name="azure-only-disaster-recovery-solutions"></a>Csak Azure: vész-helyreállítási megoldások
A vész-helyreállítási megoldást rendelkezik az SQL Server-adatbázisokat az Azure-ban rendelkezésre állási csoportok, az adatbázis-tükrözés vagy biztonsági másolat, és állítsa vissza a tárolási BLOB.

| Technológia | Példa architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Rendelkezésre állási másodpéldányok több adatközpontot az Azure virtuális gépeken vész-helyreállítási futtatásáért. A kereszt-régió megoldás a webhely teljes kimaradás véd. <br/> ![Rendelkezésre állási csoportok](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>A régión belül az összes replika a azonos felhőszolgáltatás és az azonos virtuális hálózaton belül kell lennie. Mivel minden egyes régió egy külön virtuális hálózat fog rendelkezni, ezek a megoldások VNet kötelező VNet-kapcsolatot. További információkért lásd: [konfigurálja a VNet – VNet-kapcsolatot az Azure portál használatával](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Részletes útmutatásért lásd: [egy SQL Server rendelkezésre állási csoport konfigurálása Azure virtuális gépeken különböző régiókban](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Az adatbázis-tükrözés** |Elsődleges és tükrözött és vész-helyreállítási különböző adatközpontokban futtató kiszolgálók. Kiszolgálói tanúsítványok használatával, mert egy active directory-tartomány nem terjedhetnek ki több adatközpontot kell telepítenie.<br/>![Az adatbázis-tükrözés](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Biztonsági mentés és visszaállítás az Azure Blob Storage szolgáltatással** |Éles környezetben használt adatbázisait biztonsági mentését közvetlenül a blob storage egy vész-helyreállítási ugyanabban az adatközpontban.<br/>![Biztonsági mentés és visszaállítás](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>További információkért lásd: [biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-backup-recovery.md). |
| **A Replicate és a feladatátvételt az SQL Server az Azure-bA az Azure Site Recovery szolgáltatással** |Az üzemi SQL Server közvetlenül az Azure Storage vész-helyreállítási különböző Azure Datacenter replikált egy Azure Datacenter.<br/>![Replikálása az Azure Site Recovery segítségével](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>További információkért lásd: [védelme SQL Server SQL Server vészhelyreállítási és az Azure Site Recovery segítségével](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hibrid informatikai: Vész-helyreállítási megoldások
Rendelkezik egy vész-helyreállítási megoldást hibrid-környezetben használatával a rendelkezésre állási csoportok, az adatbázis-tükrözés, a naplóküldési és a biztonsági mentés az SQL Server adatbázisok esetében, és állítsa vissza az Azure blog tárolóval.

| Technológia | Példa architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Egyes rendelkezésre állási másodpéldányok futó Azure virtuális gépek és egyéb helyileg futó többhelyes vész-helyreállítási replika. A munkakörnyezeti helyet is lehet a helyi vagy egy Azure-adatközpontban található.<br/>![Rendelkezésre állási csoportok](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Az összes rendelkezésre állási másodpéldányok ugyanazt a feladatátvevő fürtöt kell lennie, mert a fürt mindkét hálózatok (több alhálózatos feladatátvevő fürt) kell span. Ez a konfiguráció Azure és a helyszíni hálózat között virtuális Magánhálózati kapcsolat szükséges.<br/><br/>Sikeres vész-helyreállítási adatbázis telepítenie kell egy replika tartományvezérlő, a vész-helyreállítási helyen.<br/><br/>Úgy is hozzáadása varázslóval replika szolgáltatáshoz az ssms Azure replikájának hozzáadása egy meglévő Always On rendelkezésre állási csoportnak. További információkért lásd az oktatóanyag: Azure terjessze ki az Always On rendelkezésre állási csoportnak. |
| **Az adatbázis-tükrözés** |Egy Azure virtuális gép és a többi futó fut egy partneri helyszíni többhelyes vész-helyreállítási kiszolgálói tanúsítványok használatával. Partnerek nem kell lennie az Active Directory-tartományhoz, és nem VPN-kapcsolatra szükség.<br/>![Az adatbázis-tükrözés](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Egy másik adatbázis-tükrözési forgatókönyv egy Azure virtuális gép és a többi futó helyszíni ugyanabban a tartományban az Active Directory többhelyes vész-helyreállítási fut egy partneri magában foglalja. A [VPN-kapcsolat az Azure virtuális hálózat és a helyszíni hálózat között](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) szükséges.<br/><br/>Sikeres vész-helyreállítási adatbázis telepítenie kell egy replika tartományvezérlő, a vész-helyreállítási helyen. |
| **Naplóküldés** |Egy Azure virtuális gép és a többi futó helyszíni többhelyes vész-helyreállítási-t futtató kiszolgáló egy. Naplóküldésben függ Windows fájlmegosztás, így nincs szükség a VPN-kapcsolat az Azure virtuális hálózat és a helyszíni hálózat között.<br/>![Naplóküldés](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Sikeres vész-helyreállítási adatbázis telepítenie kell egy replika tartományvezérlő, a vész-helyreállítási helyen. |
| **Biztonsági mentés és visszaállítás az Azure Blob Storage szolgáltatással** |A helyi biztonsági másolat közvetlenül az Azure blob storage vész-helyreállítási éles környezetben használt adatbázisait.<br/>![Biztonsági mentés és visszaállítás](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>További információkért lásd: [biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-backup-recovery.md). |
| **A Replicate és a feladatátvételt az SQL Server az Azure-bA az Azure Site Recovery szolgáltatással** |A helyszíni üzemi SQL Server közvetlenül az Azure Storage vész-helyreállítási replikálni.<br/>![Replikálása az Azure Site Recovery segítségével](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>További információkért lásd: [védelme SQL Server SQL Server vészhelyreállítási és az Azure Site Recovery segítségével](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Fontos tudnivalók találhatók az SQL Server HADR az Azure-ban
Az Azure virtuális gépeken, tárolási és hálózatkezelési rendelkezik egy helyszíni informatikai infrastruktúra nem virtualizált eltérő működési jellemzői. Az Azure-ban HADR SQL Server megoldás sikeres végrehajtása szükséges, hogy ezek a különbségek megismeréséhez, és a megoldás kialakításakor megtervezése.

### <a name="high-availability-nodes-in-an-availability-set"></a>Magas rendelkezésre állású csomópont a rendelkezésre állási csoportok
Az Azure-ban rendelkezésre állási készletek lehetővé teszik a magas rendelkezésre állású csomópontok helyezzen külön tartalék tartományok (FDs) és a frissítési tartományok (UDs). Azure virtuális gépek az azonos rendelkezésre állási csoportba kerülnek telepítenie kell őket ugyanabban a felhőszolgáltatásban található. Az azonos rendelkezésre állási készlet csak ugyanazon a felhőszolgáltatásban található csomópontok részt. További információk: [Manage the Availability of Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Virtuális gépek rendelkezésre állásának kezelése).

### <a name="failover-cluster-behavior-in-azure-networking"></a>A feladatátvevő fürt viselkedés Azure hálózatkezelés
A nem RFC-kompatibilis DHCP-szolgáltatás az Azure-ban okozhat egyes feladatátvételi létrehozásának fürtkonfigurációk a fürthálózat nevének folyik a hozzárendelése egy ismétlődő IP-címet, például az azonos IP-címre a fürtcsomópontok egyike miatt sikertelen. Ez a probléma, ha rendelkezésre állási csoportokat, amelyek attól függ, a Windows feladatátvevő fürt funkciót.

Amikor egy két csomópontos fürt létrehozása és a hálózatra, vegye figyelembe a forgatókönyv:

1. A fürt online állapotba kerül, majd csomópont1 kérelmek dinamikusan hozzárendelt IP-címnek a fürt hálózati neve.
2. Csomópont1 saját IP-címe eltérő IP-cím a DHCP-szolgáltatás által megadott, mivel a DHCP szolgáltatás észleli, hogy a kérelem csomópont1 származik-e saját magát.
3. A Windows észleli, hogy egy duplikált cím hozzá van rendelve, 1. csomópont és a feladatátvevő fürt hálózati nevét, és az alapértelmezett fürtcsoport nem lehet online állapotba.
4. Az alapértelmezett fürtcsoport csomópont2, amely csomópont1 tartozó IP-cím kezeli a fürt IP-címet, és az alapértelmezett fürt csoport online állapotba hozása helyezi át.
5. Amikor csomópont2 megpróbálja csomópont1 kapcsolatot létrehozni, csomópont1 célzó csomagok sosem hagyják el csomópont2, mert ez megoldja-csomópont1 tartozó IP-címet saját magára. 2. csomópont nem tud csomópont1 kapcsolatot létrehozni, majd megszűnik a kvóruma és leáll a fürtben.
6. Időközben csomópont1 csomagok küldését a csomópont2, de nem tud válaszolni, csomópont2. 1. csomópont megszűnik a kvóruma, és a fürt leállítása.

Ebben a forgatókönyvben elkerülhető egy nem használt statikus IP-címet, például a 169.254.1.1, például egy kapcsolatszintű IP-cím hozzárendelése a fürthálózat nevének ahhoz, hogy a fürt hálózati név online állapotba. A folyamat leegyszerűsítése érdekében, lásd: [konfigurálása Windows feladatátvevő fürtöt az Azure-ban a rendelkezésre állási csoportok](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

További információkért lásd: [rendelkezésre állási csoportok konfigurálása az Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Rendelkezésre állási csoport figyelőjének támogatása
Rendelkezésre állási csoport figyelői Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016 rendszert futtató Azure virtuális gépeken támogatottak. Ez a támogatás elosztott terhelésű végpont már engedélyezve van az Azure virtuális gépeken, amelyek a rendelkezésre állási csoport csomópontok használatát tette lehetővé. Hajtsa végre a figyelők, mind az Azure, valamint a helyszíni futó futó ügyfélalkalmazások működéséhez speciális konfigurációs lépéseket.

A figyelő beállításának két fő lehetőség: a külső (nyilvános) vagy belső. A külső (nyilvános) figyelő használó internet felé néző terheléselosztó és az egy nyilvános virtuális IP-cím (VIP), amely elérhető az interneten keresztül. Egy belső figyelő belső terheléselosztót használ, és csak támogatja az ügyfelek az azonos virtuális hálózaton belül. Vagy a betöltés típusokra, engedélyeznie kell a közvetlen kiszolgálói válasz. 

Tartalmaznia kell a rendelkezésre állási csoport több Azure alhálózattal (például olyan környezetben, Azure-régiók keverve) is, ha az ügyfél kapcsolati karakterlánc "**MultisubnetFailover = True**". Az eredmény párhuzamos próbálnak meg kapcsolódni a különböző alhálózatokon a replikához. Egy figyelő beállításával kapcsolatos útmutatásért lásd:

* [Egy ILB figyelőt, a rendelkezésre állási csoportok konfigurálása az Azure-](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Egy külső figyelőt a rendelkezésre állási csoportok konfigurálása az Azure-](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Továbbra is csatlakozhat minden egyes rendelkezésre állási másodpéldány külön csatlakozzon közvetlenül a szolgáltatáspéldányt. Is mivel a rendelkezésre állási csoportok visszamenőleg kompatibilis az adatbázis-tükrözési ügyfelek, csatlakozhat a rendelkezésre állási másodpéldányok, például adatbázis-tükrözési partnerei mindaddig, amíg a replikák konfigurált adatbázis-tükrözés hasonlít:

* Egy elsődleges másodpéldány és egy másodlagos másodpéldány
* A másodlagos másodpéldány van konfigurálva, nem olvasható (**olvasható másodlagos** beállítás **nem**)

Egy példa ügyfél kapcsolati karakterláncot, használja az ADO.NET vagy SQL Server Native Client adatbázis-tükrözés hasonló konfiguráció megfelelő alatt van:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Ügyfélkapcsolat további információkért lásd:

* [Kapcsolati karakterlánc kulcsszavak használatával az SQL Server natív ügyfél](https://msdn.microsoft.com/library/ms130822.aspx)
* [Az ügyfelek kapcsolódni az adatbázis-tükrözési munkamenetben (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [A hibrid informatikai rendelkezésre állási csoport figyelőjének csatlakozik](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Rendelkezésre állási csoport figyelői ügyfélkapcsolat és alkalmazás feladatátvételi (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Az adatbázis-tükrözési kapcsolati karakterláncok használatával a rendelkezésre állási csoportokkal](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>A hibrid informatikai hálózati késés
Ha telepíti a HADR megoldás feltételezve, hogy a nagy hálózati késés a helyszíni hálózat és az Azure közötti idő is lehet. Replikák az Azure-ba való telepítésekor a szinkronizálási mód aszinkron véglegesítési használjon szinkron véglegesítésre helyett. Az adatbázis-tükrözés kiszolgálók telepítése mind a helyszíni, miután az Azure, a nagy teljesítményű mód használata helyett a magas biztonsági módot.

### <a name="geo-replication-support"></a>A georeplikáció támogatása
Az Azure-lemezeket a georeplikáció nem támogatja a adatfájl és a különálló lemezek tárolását ugyanazon adatbázis naplófájlját. Georedundáns replikál az egyes lemezek külön és aszinkron módon. Ez a módszer biztosítja belül egyetlen lemezt a georeplikált másolatán, de több lemez georeplikált példánya között nem írási sorrendjét. Ha konfigurálja az adatfájl és a naplófájl külön lemezeken tárolja az adatbázis, a helyreállított lemezek katasztrófa utáni tartalmazhat az adatfájl legújabb példányát, mint a naplófájl, amely az SQL Server és a tranzakciók ACID tulajdonságainak írási előre bejelentkezési. Ha nem rendelkezik arra, hogy letiltsa a tárfiók georeplikáció, adatainak és naplókönyvtárainak található összes fájl egy adott adatbázis ugyanazon a lemezen legyen. Ha az adatbázis mérete miatt több lemez kell használnia, akkor kell telepíteni adatredundanciát biztosításához a fent felsorolt vész-helyreállítási megoldások egyikét.

## <a name="next-steps"></a>További lépések
Ha az SQL Server Azure virtuális gép létrehozásához szüksége, tekintse meg [Azure SQL Server virtuális gépek kiépítése](virtual-machines-windows-portal-sql-server-provision.md).

Ahhoz, hogy a legjobb teljesítmény érdekében az SQL Serverről az Azure virtuális gép futó, tekintse meg az útmutató [teljesítmény ajánlott eljárások az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-performance.md).

Egyéb Azure virtuális gépeken futó SQL Server kapcsolatos témaköröket, lásd: [SQL Server Azure virtuális gépeken](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Egyéb erőforrások
* [Egy új Active Directory-erdő telepítése az Azure-ban](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [A rendelkezésre állási csoportok Azure virtuális gépen a feladatátvevő fürt létrehozása](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

