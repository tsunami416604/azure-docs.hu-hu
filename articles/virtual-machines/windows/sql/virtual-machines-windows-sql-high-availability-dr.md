---
title: Magas rendelkezésre állás és vészhelyreállítás az SQL Server kiszolgálóhoz | Microsoft dokumentumok
description: Az Azure virtuális gépeken futó SQL Server-stratégiák különböző típusú HADR-stratégiáinak megvitatása.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: f58bb534728660b85f7d16910dde7a37914fd571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249762"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Magas rendelkezésre állás és vészhelyreállítás az Azure-beli SQL Server-alapú virtuális gépeken

Az SQL Serverrel rendelkező Microsoft Azure virtuális gépek (VM-ek) segíthetnek csökkenteni a magas rendelkezésre állású és vész-helyreállítási (HADR) adatbázis-megoldás költségeit. A legtöbb SQL Server HADR-megoldás támogatott az Azure virtuális gépeiben, mind csak Azure-ként, mind hibrid megoldásként. A csak Azure-ban a teljes HADR-rendszer fut az Azure-ban. Egy hibrid konfigurációban a megoldás egy része az Azure-ban fut, a másik része pedig a szervezeten belüli helyszíni futtatások. Az Azure-környezet rugalmassága lehetővé teszi, hogy részben vagy teljesen átköltözzön az Azure-ba, hogy megfeleljen az SQL Server adatbázis-rendszerek költségvetési és HADR követelményeinek.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>A HADR megoldás szükségességének megértése
Önnek kell gondoskodnia arról, hogy az adatbázis-rendszer rendelkezzen a szolgáltatásiszint-szerződés (SLA) által igényelt HADR-képességekkel. Az a tény, hogy az Azure magas rendelkezésre állási mechanizmusokat biztosít, például a felhőszolgáltatások szolgáltatásának helyreállítását és a virtuális gépek hiba-helyreállítási észlelését, önmagában nem garantálja, hogy megfelel a kívánt SLA-nak. Ezek a mechanizmusok védik a virtuális gépek magas rendelkezésre állását, de nem a virtuális gépeken belül futó SQL Server magas rendelkezésre állását. Lehetséges, hogy az SQL Server-példány sikertelen, amíg a virtuális gép online állapotban van, és kifogástalan állapotban van. Ezenkívül még az Azure által biztosított magas rendelkezésre állási mechanizmusok is lehetővé teszik a virtuális gépek állásidejét olyan események miatt, mint például a szoftver- vagy hardverhibák ból való helyreállítás és az operációs rendszer frissítései.

Emellett az Azure-ban a georeplikáció nevű szolgáltatással megvalósított Geo Redundáns tárolás (GRS) nem feltétlenül megfelelő vész-helyreállítási megoldás az adatbázisok számára. Mivel a georeplikáció aszinkron módon küldi az adatokat, a legutóbbi frissítések elveszhetnek katasztrófa esetén. A földrajzi replikáció korlátaival kapcsolatos további információk a [földrajzi replikációnem támogatott a külön lemezeken lévő adatok és naplófájlok](#geo-replication-support) esetében.

## <a name="hadr-deployment-architectures"></a>HADR telepítési architektúrák
Az Azure által támogatott SQL Server HADR-technológiák a következők:

* [Mindig a rendelkezésre állási csoportok](https://technet.microsoft.com/library/hh510230.aspx)
* [Mindig a feladatátvevő fürtpéldányai](https://technet.microsoft.com/library/ms189134.aspx)
* [Log szállítás](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server biztonsági mentés és visszaállítás az Azure Blob Storage Szolgáltatással](https://msdn.microsoft.com/library/jj919148.aspx)
* [Adatbázis-tükrözés](https://technet.microsoft.com/library/ms189852.aspx) – elavult az SQL Server 2016-ban

A technológiák kombinálható egy olyan SQL Server-megoldás megvalósításához, amely magas rendelkezésre állású és vész-helyreállítási képességekkel is rendelkezik. A ttól függően, hogy a használt technológia, a hibrid központi telepítés szükség lehet egy VPN-alagút az Azure virtuális hálózat. Az alábbi szakaszok néhány példa üzembe helyezési architektúrát mutatnak be.

## <a name="azure-only-high-availability-solutions"></a>Csak Azure-ban: Magas rendelkezésre állású megoldások

Az SQL Server magas rendelkezésre állású megoldása adatbázis szinten, mindig rendelkezésre állási csoportok – az úgynevezett rendelkezésre állási csoportok. A példányok szintjén is létrehozhat magas rendelkezésre állású megoldást az Always On Failover Cluster Instances – feladatátvevő fürtpéldányokkal. További redundancia érdekében mindkét szinten redundanciát hozhat létre, ha feladatátvevő fürtpéldányokon hoz létre rendelkezésre állási csoportokat. 

| Technológia | Példa architektúrákra |
| --- | --- |
| **Rendelkezésre állási csoportok** |Az Azure-beli virtuális gépeken ugyanabban a régióban futó rendelkezésre állási replikák magas rendelkezésre állást biztosítanak.  Konfigurálnia kell egy tartományvezérlő virtuális gépét, mert a Windows feladatátvevő fürtözéséhez Active Directory-tartomány szükséges.<br/><br/> A nagyobb redundancia és a rendelkezésre állás az Azure virtuális gépek telepíthetők a különböző [rendelkezésre állási zónák](../../../availability-zones/az-overview.md) ban dokumentált [a rendelkezésre állási csoport áttekintése.](virtual-machines-windows-portal-sql-availability-group-overview.md) Ha egy rendelkezésre állási csoportban lévő SQL Server virtuális gépek vannak telepítve a rendelkezésre állási zónákban, majd használja [a figyelő szabványos terheléselosztót](../../../load-balancer/load-balancer-standard-overview.md) az ezekben a cikkekben – [az Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) & [Azure gyorsútmutató-sablonjai.](virtual-machines-windows-sql-availability-group-quickstart-template.md)<br/> ![Rendelkezésre állási csoportok](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>További információt a [Rendelkezésre állási csoportok konfigurálása az Azure-ban (GUI) című témakörben talál.](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) |
| **Feladatátvevő fürtpéldányai** |A megosztott tárolást igénylő feladatátvevő fürtpéldányok (FCI) 4 különböző módon hozhatók létre.<br/><br/>1. A kétcsomópontos feladatátvevő fürt futó Azure virtuális gépek csatlakoztatott tároló [windows \(Server\) 2016 tárolóhelyek közvetlen S2D](virtual-machines-windows-portal-sql-create-failover-cluster.md) szoftveralapú virtuális SAN.<br/><br/> 2. Az Azure virtuális gépein a [prémium szintű fájlmegosztás](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)használatával futó kétcsomópontos feladatátvevő fürt. A prémium szintű fájlmegosztások SSD-vel támogatott, következetesen alacsony késleltetésű fájlmegosztások, amelyek teljes mértékben támogatottak a feladatátvevő fürtpéldányhasználatával.<br/><br/>3. Egy kétcsomópontos feladatátvevő fürt, amely az Azure virtuális gépeken fut, és egy külső fürtözési megoldás által támogatott tárolóval. A SIOS DataKeeper t használó konkrét példát a [feladatátvevő fürtözést és a harmadik féltől származó SIOS DataKeeper szoftverrel megosztott fájlmegosztás magas rendelkezésre állása című témakörben található.](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)<br/><br/>4. Egy kétcsomópontos feladatátvevő fürt, amely az Azure-beli virtuális gépeken fut távoli iSCSI-tároló megosztott tárolóval az ExpressRoute-on keresztül. A NetApp private storage (NPS) például egy iSCSI-tárolót az Equinix-szel rendelkező ExpressRoute-on keresztül az Azure virtuális gépekszámára elérhetővé teszi.<br/><br/>Külső megosztott tárolási és adatreplikációs megoldások esetén vegye fel a kapcsolatot a szállítóval a feladatátvételi adatok elérésével kapcsolatos problémákmiatt.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Csak Azure-ban: Vész-helyreállítási megoldások
Rendelkezhet egy vész-helyreállítási megoldás az SQL Server-adatbázisok az Azure-ban a rendelkezésre állási csoportok, adatbázis-tükrözés, vagy biztonsági mentés és visszaállítás a tárolási blobok használatával.

| Technológia | Példa architektúrákra |
| --- | --- |
| **Rendelkezésre állási csoportok** |Rendelkezésre állási replikák futó több adatközpontok az Azure-beli virtuális gépek vész-helyreállítási. Ez a régióközi megoldás védelmet nyújt a teljes helykimaradás ellen. <br/> ![Rendelkezésre állási csoportok](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>Egy régión belül az összes replikák kell ugyanabban a felhőszolgáltatásban és ugyanazon a virtuális hálózaton belül. Mivel minden régió külön virtuális hálózattal fog rendelkezni, ezek a megoldások virtuális hálózat és virtuális hálózat közötti kapcsolatot igényelnek. További információ: [VNet-to-VNet kapcsolat konfigurálása az Azure Portalhasználatával című témakörben.](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) Részletes útmutatást az [SQL Server rendelkezésre állási csoportjának konfigurálása az Azure virtuális gépeken különböző régiókban című témakörben talál.](virtual-machines-windows-portal-sql-availability-group-dr.md)|
| **Adatbázis tükrözése** |A rendszer fő és tükör és a különböző adatközpontokban futó kiszolgálók vész-helyreállítási. Kiszolgálói tanúsítványokkal kell telepítenie. Az SQL Server adatbázis-tükrözése nem támogatott az SQL Server 2008 és az SQL Server 2008 R2 esetében egy Azure virtuális gépen. <br/>![Adatbázis tükrözése](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Biztonsági mentés és visszaállítás az Azure Blob Storage szolgáltatással** |Éles adatbázisok biztonsági mentést közvetlenül blob storage egy másik adatközpontban vész-helyreállítási.<br/>![Biztonsági mentés és visszaállítás](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>További információt az [SQL Server biztonsági mentése és visszaállítása az Azure virtuális gépeken című témakörben talál.](virtual-machines-windows-sql-backup-recovery.md) |
| **SQL Server replikálása és feladatátvétele az Azure-ba az Azure Site Recovery segítségével** |Egy Azure-adatközpont éles SQL Server replikálódik közvetlenül az Azure Storage különböző Azure-adatközpontok vész-helyreállítási.<br/>![Replikálás az Azure Site Recovery használatával](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>További információ: [Sql Server védelme SQL Server vészhelyreállítás és az Azure Site Recovery használatával](../../../site-recovery/site-recovery-sql.md)című témakörben talál. |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hibrid IT: Vész-helyreállítási megoldások
Az SQL Server-adatbázisokhoz egy hibrid informatikai környezetben is rendelkezhet vész-helyreállítási megoldással a rendelkezésre állási csoportok, az adatbázis-tükrözés, a naplószállítás, valamint a biztonsági mentés és visszaállítás használatával az Azure blog tárházával.

| Technológia | Példa architektúrákra |
| --- | --- |
| **Rendelkezésre állási csoportok** |Néhány rendelkezésre állási replikák futó Azure virtuális gépek és más replikák futó helyszíni helyek közötti vész-helyreállítási. Az éles hely lehet a helyszínen vagy egy Azure-adatközpontban.<br/>![Rendelkezésre állási csoportok](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Mivel az összes rendelkezésre állási replikának ugyanabban a feladatátvevő fürtben kell lennie, a fürtnek mindkét hálózatra (többalhálózati feladatátvevő fürtre) ki kell nyúlnia. Ehhez a konfigurációhoz VPN-kapcsolat ra van szükség az Azure és a helyszíni hálózat között.<br/><br/>Az adatbázisok sikeres vészhelyreállítása érdekében telepítenie kell egy replika-tartományvezérlőt is a vész-helyreállítási helyen.<br/><br/>Lehetőség van a Replika hozzáadása varázsló ssms-ben egy Azure-replika hozzáadása egy meglévő Mindig rendelkezésre állási csoport. További információ: Oktatóanyag: A mindig rendelkezésre állási csoport kiterjesztése az Azure-ra. |
| **Adatbázis tükrözése** |Az egyik partner fut egy Azure virtuális gép, a másik pedig a helyszíni kiszolgálók közötti vész-helyreállítási kiszolgálói tanúsítványok használatával. A partnereknek nem kell ugyanabba az Active Directory tartományba lenniük, és nincs szükség VPN-kapcsolatra.<br/>![Adatbázis tükrözése](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Egy másik adatbázis-tükrözési forgatókönyv magában foglalja az egyik partner fut egy Azure virtuális gép, a másik pedig a helyszíni ugyanazon az Active Directory-tartományban a helyek közötti vész-helyreállítási. Az [Azure virtuális hálózat és a helyszíni hálózat közötti VPN-kapcsolat](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) szükséges.<br/><br/>Az adatbázisok sikeres vészhelyreállítása érdekében telepítenie kell egy replika-tartományvezérlőt is a vész-helyreállítási helyen. Az SQL Server adatbázis-tükrözése nem támogatott az SQL Server 2008 és az SQL Server 2008 R2 esetében egy Azure virtuális gépen. |
| **Log szállítás** |Az egyik kiszolgáló egy Azure-beli virtuális gép, a másik pedig a helyszíni több helyszíni vész-helyreállítási fut. A naplószállítás a Windows fájlmegosztásáttól függ, ezért vpn-kapcsolatra van szükség az Azure virtuális hálózatés a helyszíni hálózat között.<br/>![Log szállítás](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Az adatbázisok sikeres vészhelyreállítása érdekében telepítenie kell egy replika-tartományvezérlőt is a vész-helyreállítási helyen. |
| **Biztonsági mentés és visszaállítás az Azure Blob Storage szolgáltatással** |A helyszíni éles adatbázisok ról közvetlenül az Azure blob storage vész-helyreállítási.<br/>![Biztonsági mentés és visszaállítás](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>További információt az [SQL Server biztonsági mentése és visszaállítása az Azure virtuális gépeken című témakörben talál.](virtual-machines-windows-sql-backup-recovery.md) |
| **SQL Server replikálása és feladatátvétele az Azure-ba az Azure Site Recovery segítségével** |A helyszíni éles SQL Server replikálódik közvetlenül az Azure Storage vész-helyreállítási.<br/>![Replikálás az Azure Site Recovery használatával](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>További információ: [Sql Server védelme SQL Server vészhelyreállítás és az Azure Site Recovery használatával](../../../site-recovery/site-recovery-sql.md)című témakörben talál. |


## <a name="free-dr-replica-in-azure"></a>Ingyenes DR-replika az Azure-ban

Frissítési [garancia esetén](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)hibrid vész-helyreállítási (DR) terveket valósíthat meg az SQL Server kiszolgálóval anélkül, hogy a passzív VÉSZ-példány további licencelési költségeket kellene alkalmaznia.

Az alábbi képen a telepítő az SQL Server 12 maghasználatával futó SQL Servert használja vész-helyreállítási replikaként egy helyszíni SQL Server 12 maghasználatával. A múltban 12 alaplemezSQL Server-t kell licencre adnia a helyszíni és az Azure virtuális gép üzembe helyezéséhez. Az új előny passzív replika előnyöket kínál egy Azure virtuális gépen futó. Most kellene, hogy csak a licenc 12 magok SQL Server helyszíni futtatása mindaddig, amíg a vész-helyreállítási feltételek az Azure virtuális gép passzív replika teljesül.

![Ingyenes DR-replika az Azure-ban](media/virtual-machines-windows-sql-high-availability-dr/free-dr-replica-azure.png)

További információt a [Terméklicencelési feltételek ben](https://www.microsoft.com/licensing/product-licensing/products)talál. 

A kedvezmény engedélyezéséhez keresse meg az [SQL Server virtuálisgép-erőforrást,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)válassza a **Beállítások** lehetőséget a Beállítások csoportban, majd válassza a **Vészhelyreállítás** lehetőséget az **SQL Server licenc**csoportban. Jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy ez az SQL Server virtuális gép passzív kópiaként lesz-e használva, majd válassza az **Alkalmaz** lehetőséget a beállítások mentéséhez. 

![Dr replika konfigurálása az Azure-ban](media/virtual-machines-windows-sql-high-availability-dr/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Fontos szempontok az SQL Server HADR-rel az Azure-ban
Az Azure virtuális gépek, a tárolás és a hálózatkülönböző működési jellemzőkkel rendelkeznek, mint egy helyszíni, nem virtualizált informatikai infrastruktúra. A HADR SQL Server-megoldás azure-beli sikeres megvalósításához meg kell értenie ezeket a különbségeket, és meg kell terveznie a megoldást azok befogadására.

### <a name="high-availability-nodes-in-an-availability-set"></a>Magas rendelkezésre állási csomópontok egy rendelkezésre állási csoportban
Az Azure-ban rendelkezésre állási készletek lehetővé teszik, hogy a magas rendelkezésre állású csomópontokat külön tartalék tartományokba (FD) és frissítési tartományokba (UDs) helyezze. A mögöttes Azure platform a rendelkezésre állási csoportban lévő mindegyik virtuális gépnek kioszt egy frissítési tartományt és egy tartalék tartományt. Ez a konfiguráció egy adatközponton belül biztosítja, hogy egy tervezett vagy nem tervezett karbantartási esemény során legalább egy virtuális gép elérhető, és megfelel a 99,95%-os Azure SLA. A magas rendelkezésre állású beállítás konfigurálásához helyezze az összes részt vevő SQL virtuális gépet ugyanabba a rendelkezésre állási készletbe, hogy elkerülje az alkalmazást vagy az adatvesztést egy karbantartási esemény során. Ugyanabban a felhőszolgáltatásban csak csomópontok vehetnek részt ugyanabban a rendelkezésre állási csoportban. További információ: [A virtuális gépek elérhetőségének kezelése.](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="high-availability-nodes-in-an-availability-zone"></a>Magas rendelkezésre állású csomópontok egy rendelkezésre állási zónában
A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rendelkezésre állási zónák fizikai elkülönítése egy régión belül védi az alkalmazásokat és az adatokat az adatközponti hibáktól azáltal, hogy biztosítja, hogy legalább egy virtuális gép elérhető legyen, és megfelel az Azure SLA 99,99%-ának. A magas rendelkezésre állás konfigurálásához helyezze n: a részt vevő SQL virtuális gépek a régió ban rendelkezésre álló rendelkezésre állási zónák között. Lesz további rendelkezésre állási zóna virtuális gép-virtuális gép adatátviteli díjakat. További információt a [Rendelkezésre állási zónák című témakörben talál.](/azure/availability-zones/az-overview) 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Feladatátvevő fürt viselkedése az Azure-hálózatban
Az Azure-ban a nem RFC-kompatibilis DHCP-szolgáltatás bizonyos feladatátvevő fürtkonfigurációk létrehozását okozhatja, mivel a fürthálózat neve duplikált IP-címet kap, például ugyanazt az IP-címet, mint a fürtcsomópontok egyikének. Ez a probléma a Rendelkezésre állási csoportok megvalósításakor történik, amely a Windows feladatátvevő fürtszolgáltatásától függ.

Vegye figyelembe a kétcsomópontos fürt létrehozásakor és online állapotba hozásakor a forgatókönyvet:

1. A fürt online állapotba kerül, majd a NODE1 dinamikusan hozzárendelt IP-címet kér a fürt hálózatnevéhez.
2. A DHCP-szolgáltatás nem ad meg más IP-címet, mint a NODE1 saját IP-címét, mivel a DHCP-szolgáltatás felismeri, hogy a kérelem magától a NODE1-től származik.
3. A Windows észleli, hogy egy duplikált cím van rendelve mind a NODE1, mind a feladatátvevő fürt hálózatának nevéhez, és az alapértelmezett fürtcsoport nem kapcsolódik online állapotba.
4. Az alapértelmezett fürtcsoport a NODE2-re kerül, amely a NODE1 IP-címét fürt IP-címként kezeli, és online állapotba hozza az alapértelmezett fürtcsoportot.
5. Amikor a NODE2 megpróbál kapcsolatot létesíteni a NODE1-el, a NODE1-re irányuló csomagok soha nem hagyják el a NODE2-t, mert saját magának oldja fel a NODE1 IP-címét. A NODE2 nem tud kapcsolatot létesíteni a NODE1-gyel, majd elveszíti a kvórumot, és leállítja a fürtöt.
6. Addig is a NODE1 küldhet csomagokat a NODE2-nek, de a NODE2 nem tud válaszolni. A NODE1 elveszíti a kvórumot, és leállítja a fürtöt.

Ez a forgatókönyv elkerülhető, ha egy nem használt statikus IP-címet, például egy kapcsolaton ként megadott IP-címet ( 169.254.1.1) rendel a fürt hálózatnevéhez a fürt hálózatnevének online állapotba hozása érdekében. A folyamat egyszerűsítése érdekében olvassa [el a Windows feladatátvételi fürtkonfigurálása az Azure-ban rendelkezésre állási csoportokhoz című témakört.](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)

További információt a [Rendelkezésre állási csoportok konfigurálása az Azure-ban (GUI) című témakörben talál.](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

### <a name="availability-group-listener-support"></a>Rendelkezésre állási csoport figyelőjének támogatása
A rendelkezésre állási csoport figyelői Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016 rendszert futtató Azure-virtuális gépeken támogatottak. Ezt a támogatást az Azure-beli virtuális gépeken engedélyezett, kisegítő csoportcsomópontokon engedélyezett terheléselosztási végpontok használata teszi lehetővé. Speciális konfigurációs lépéseket kell követnie ahhoz, hogy a figyelők az Azure-ban futó és a helyszíni ügyfélalkalmazásokhoz is működjenek.

A figyelő beállítására két fő lehetőség van: külső (nyilvános) vagy belső. A külső (nyilvános) figyelő egy internetfelé néző terheléselosztót használ, és egy nyilvános virtuális IP-címhez (VIP) van társítva, amely az interneten keresztül érhető el. Egy belső figyelő belső terheléselosztót használ, és csak az ugyanazon a virtuális hálózaton belüli ügyfeleket támogatja. Mindkét terheléselosztó típusához engedélyeznie kell a Direct Server Return-t. 

Ha a rendelkezésre állási csoport több Azure-alhálózatra is kiterjed (például az Azure-régiókat keresztező központi telepítésre), az ügyfélkapcsolati karakterláncnak tartalmaznia kell a "**MultisubnetFailover=True**" értéket. Ennek eredményeként a különböző alhálózatok replikái párhuzamos kapcsolati kísérleteket eredményeznek. A figyelő beállításával kapcsolatos tudnivalókat a

* [Konfiguráljon egy ILB-figyelőt az Azure-beli rendelkezésre állási csoportokhoz.](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)
* [Konfiguráljon egy külső figyelőt az Azure-beli rendelkezésre állási csoportokhoz.](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md)

Továbbra is csatlakozhat az egyes rendelkezésre állási replika külön-külön a szolgáltatáspéldányhoz való közvetlen csatlakozással. Mivel a rendelkezésre állási csoportok visszamenőlegesen kompatibilisek az adatbázis-tükröző ügyfelekkel, csatlakozhat a rendelkezésre álló replikákhoz, például az adatbázis-tükrözési partnerekhez, feltéve, hogy a replikák az adatbázis-tükrözéshez hasonlóan vannak konfigurálva:

* Egy elsődleges és egy másodlagos kópia
* A másodlagos replika nem olvasható **(Olvasható másodlagos** beállítás **nem)** értékre van állítva.

Az adatbázis tükrözésszerű konfigurációjának megfelelő ügyfélkapcsolati karakterlánc példáját ADO.NET vagy AZ SQL Server Native Client az alábbi táblázatban adja meg:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Az ügyfélkapcsolatról az:

* [Kapcsolati karakterlánc-kulcsszavak használata az SQL Server natív ügyféllel](https://msdn.microsoft.com/library/ms130822.aspx)
* [Ügyfelek összekapcsolása adatbázis-tükrözési munkamenethez (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Csatlakozás a rendelkezésre állási csoportfigyelőhibrid informatikai környezetben](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Rendelkezésre állási csoport figyelői, ügyfélkapcsolat és alkalmazásfeladat-átvétel (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Adatbázis-tükrözési kapcsolati karakterláncok használata rendelkezésre állási csoportokkal](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Hálózati késés hibrid informatikai környezetben
A HADR-megoldás üzembe helyezéséhez azzal a feltételezéssel, hogy előfordulhat, hogy a helyszíni hálózat és az Azure közötti nagy hálózati késés ű időszakok. Replikák üzembe helyezésekor az Azure-ba, aszinkron véglegesítéshasználata helyett szinkron véglegesítésa a szinkronizálási módban. Adatbázis-tükrözési kiszolgálók helyszíni és az Azure-beli telepítésekor használja a nagy teljesítményű módot a nagy biztonságú mód helyett.

### <a name="geo-replication-support"></a>Georeplikáció támogatása
Az Azure-lemezeken lévő georeplikáció nem támogatja ugyanannak az adatbázisnak az adatfájlját és naplófájlját, amelyet külön lemezeken kell tárolni. A GRS egymástól függetlenül és aszinkron módon replikálja az egyes lemezeken végrehajtott módosításokat. Ez a mechanizmus garantálja az írási sorrendet egyetlen lemezen belül a georeplikált másolaton, de nem több lemez georeplikált másolatai között. Ha úgy állít be egy adatbázist, hogy az adatfájlt és a naplófájlt külön lemezeken tárolja, a katasztrófa utáni helyreállított lemezek az adatfájl nak a naplófájlnál naprakészebb másolatát tartalmazhatják, ami megtöri az SQL Server ben az írási naplót és az ACID tulajdonságait. Tranzakciók. Ha nem tudja letiltani a georeplikációt a tárfiókban, akkor egy adott adatbázis összes adat- és naplófájlját ugyanazon a lemezen kell tárolnia. Ha az adatbázis mérete miatt egynél több lemezt kell használnia, az adatredundancia biztosítása érdekében telepítenie kell a fent felsorolt vész-helyreállítási megoldások egyikét.

## <a name="next-steps"></a>További lépések
Ha létre kell hoznia egy Azure virtuális gépet az SQL Server kiszolgálóval, olvassa el [az SQL Server virtuális gép kiépítése az Azure-ban](virtual-machines-windows-portal-sql-server-provision.md)című témakört.

Az Azure virtuális gépen futó SQL Server legjobb teljesítményének érdekében tekintse meg az [Azure virtuális gépek SQL Serverhez ajánlott eljárásokra vonatkozó útmutatást.](virtual-machines-windows-sql-performance.md)

Az SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témaköröket [az SQL Server azure-beli virtuális gépeken című témakörben talál.](virtual-machines-windows-sql-server-iaas-overview.md)

### <a name="other-resources"></a>Egyéb erőforrások
* [Új Active Directory-erdő telepítése az Azure-ban](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Feladatátvevő fürt létrehozása az Azure virtuális gépben rendelkezésre állási csoportokhoz](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

