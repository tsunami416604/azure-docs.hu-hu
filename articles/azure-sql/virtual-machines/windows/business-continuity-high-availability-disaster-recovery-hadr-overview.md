---
title: Magas rendelkezésre állás és vész-helyreállítási SQL Server | Microsoft Docs
description: Az Azure Virtual Machines szolgáltatásban futó SQL Server különböző HADR stratégiákkal kapcsolatos megbeszélések.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 43b218e1b86dd17a8f46cc010c8bddaf92694a00
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669222"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-on-azure-virtual-machines"></a>Magas rendelkezésre állás és vész-helyreállítási SQL Server az Azure-ban Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Az Azure Virtual Machines Microsoft SQL Server csökkentheti a magas rendelkezésre állású és vész-helyreállítási (HADR) adatbázis-megoldás költségeit. A legtöbb SQL Server HADR-megoldás támogatott a virtuális gépeken (VM), mind az Azure-ban, mind pedig hibrid megoldásként. Egy csak Azure-megoldásban a teljes HADR rendszer fut az Azure-ban. A hibrid konfigurációban a megoldás egy része az Azure-ban fut, a másik pedig a helyszínen fut a szervezeten belül. Az Azure-környezet rugalmassága lehetővé teszi, hogy részben vagy egészben helyezze át az Azure-ba, hogy kielégítse a SQL Server adatbázis-rendszereinek költségvetési és HADR követelményeit.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>HADR-megoldás szükségességének megértése
Gondoskodjon arról, hogy az adatbázisrendszer rendelkezzen a szolgáltatói szerződés (SLA) által igényelt HADR képességekkel. Az a tény, hogy az Azure magas rendelkezésre állási mechanizmusokat biztosít, például a Cloud Services szolgáltatásának gyógyulását és a virtuális gépek meghibásodásának helyreállítását, nem garantálja, hogy kielégíti a kívánt SLA-t. Ezek a mechanizmusok védik a virtuális gépek magas rendelkezésre állását, de nem a virtuális gépeken belül futó SQL Server magas rendelkezésre állását. Előfordulhat, hogy a SQL Server-példány meghibásodik, miközben a virtuális gép online állapotú és kifogástalan állapotban van. Emellett az Azure által biztosított magas rendelkezésre állási mechanizmusok is lehetővé teszik a virtuális gépek leállását olyan események miatt, mint például a szoftveres vagy hardveres hibák helyreállítása, valamint az operációs rendszer frissítései.

Emellett a Geo-redundáns tárolás (GRS) az Azure-ban, amely a Geo-replikáció nevű funkcióval van megvalósítva, nem lehet megfelelő vész-helyreállítási megoldás az adatbázisokhoz. Mivel a Geo-replikáció aszinkron módon küld adatokat, a legutóbbi frissítések elvesznek a katasztrófa esetén. A földrajzi replikálásra vonatkozó korlátozásokkal kapcsolatos további információk a [geo-replikációban nem támogatottak az adatok és a naplófájlok különálló lemezeken](#geo-replication-support) című részében.

## <a name="hadr-deployment-architectures"></a>HADR üzembe helyezési architektúrák
SQL Server az Azure-ban támogatott HADR-technológiák a következők:

* [Always On rendelkezésre állási csoportok](https://technet.microsoft.com/library/hh510230.aspx)
* [Always On feladatátvevő fürt példányai](https://technet.microsoft.com/library/ms189134.aspx)
* [Naplóátvitel](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server biztonsági mentés és visszaállítás az Azure Blob Storage szolgáltatással](https://msdn.microsoft.com/library/jj919148.aspx)
* [Adatbázis-tükrözés](https://technet.microsoft.com/library/ms189852.aspx) – elavult SQL Server 2016

A technológiák összekapcsolhatók egy olyan SQL Server megoldás megvalósításával, amely magas rendelkezésre állású és vész-helyreállítási képességekkel is rendelkezik. A használt technológiától függően a hibrid üzembe helyezéshez szükség lehet egy VPN-alagútra az Azure Virtual Network használatával. Az alábbi részekben a telepítési architektúrák néhány példája látható.

## <a name="azure-only-high-availability-solutions"></a>Csak az Azure-ban: magas rendelkezésre állású megoldások

Az Always On rendelkezésre állási csoportokkal rendelkező adatbázis-szinten SQL Server magas rendelkezésre állási csoportok használatával. Magas rendelkezésre állású megoldást is létrehozhat egy példány szintjén, a feladatátvételi fürt példányain. A további redundancia érdekében mindkét szinten létrehozhat redundanciát, ha rendelkezésre állási csoportokat hoz létre a feladatátvevő fürt példányain. 

| Technológia | Példa architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Az azonos régióban található Azure-beli virtuális gépeken futó rendelkezésre állási replikák magas rendelkezésre állást biztosítanak. Konfigurálnia kell egy tartományvezérlő virtuális gépet, mivel a Windows feladatátvételi fürtszolgáltatáshoz Active Directory tartomány szükséges.<br/><br/> A magasabb szintű redundancia és rendelkezésre állás érdekében az Azure-beli virtuális gépek különböző [rendelkezésre állási zónákban](../../../availability-zones/az-overview.md) helyezhetők üzembe a [rendelkezésre állási csoport áttekintése című](availability-group-overview.md)cikkben leírtak szerint. Ha a rendelkezésre állási csoportba tartozó SQL Server virtuális gépek rendelkezésre állási zónákba vannak telepítve, akkor a figyelő [standard Load balancert](../../../load-balancer/load-balancer-standard-overview.md) használja az alábbi cikkekben ismertetett módon – [Azure SQL Virtual Machine CLI](availability-group-az-cli-configure.md)  &  [Azure Gyorsindítás sablonok](availability-group-quickstart-template-configure.md).<br/> ![Rendelkezésre állási csoportok](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>További információ: [rendelkezésre állási csoportok konfigurálása az Azure-ban (GUI)](availability-group-azure-marketplace-template-configure.md). |
| **Feladatátvevő fürt példányai** |A megosztott tárolót igénylő feladatátvételi fürtök példányai 4 különböző módon hozhatók létre.<br/><br/>1. egy kétcsomópontos feladatátvevő fürt, amely az Azure-beli virtuális gépeken, valamint a [Windows Server 2016 közvetlen tárolóhelyek \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) -vel rendelkező, csatlakoztatott tárolóval rendelkezik, és szoftveres virtuális San-t biztosít.<br/><br/> 2. az Azure virtuális gépeken futó két csomópontos feladatátvevő fürt [prémium szintű fájlmegosztás](failover-cluster-instance-premium-file-share-manually-configure.md)használatával. A prémium szintű fájlmegosztás SSD-alapú, következetesen alacsony késésű fájlmegosztás, amely teljes mértékben támogatott a Feladatátvevőfürt-példánnyal való használatra.<br/><br/>3. az Azure-beli virtuális gépeken futó kétcsomópontos feladatátvevő fürt, amely egy harmadik féltől származó fürtözési megoldás által támogatott tárterülettel rendelkezik. A SIOS DataKeeper használó konkrét példáért lásd: [a fájlmegosztás magas rendelkezésre állása a feladatátvételi fürtszolgáltatás és a külső gyártótól származó szoftverek SIOS DataKeeper használatával](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. egy kétcsomópontos feladatátvevő fürt, amely Azure-beli virtuális gépeken fut távoli iSCSI-tároló megosztott blokk-tárolóval a ExpressRoute-n keresztül. Például a NetApp Private Storage (NPS) egy iSCSI-célt tesz elérhetővé az ExpressRoute-n keresztül az Azure-beli virtuális gépek Equinix.<br/><br/>A külső felek megosztott tárolási és adatreplikációs megoldásai esetében forduljon a szállítóhoz a feladatátvételi adatokhoz való hozzáféréssel kapcsolatos esetleges problémákhoz.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Csak az Azure-ban: vész-helyreállítási megoldások
A rendelkezésre állási csoportokkal, adatbázis-tükrözéssel, illetve a Storage-Blobokkal történő biztonsági mentéssel és visszaállítással vészhelyzeti helyreállítási megoldással rendelkezhet az Azure SQL Server adatbázisaihoz.

| Technológia | Példa architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Az Azure-beli virtuális gépek több adatközpontjában futó rendelkezésre állási replikák a vész-helyreállítás érdekében. Ez a régiók közötti megoldás védelmet biztosít a teljes hely KIMARADÁSÁVAL szemben. <br/> ![Rendelkezésre állási csoportok](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>Egy adott régión belül minden replikának ugyanabban a felhőalapú szolgáltatásban és azonos VNet kell lennie. Mivel minden egyes régió külön VNet fog rendelkezni, ezekhez a megoldásokhoz VNet szükséges a VNet-kapcsolathoz. További információ: [VNet-VNet-kapcsolatok konfigurálása a Azure Portal használatával](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Részletes útmutatás: [SQL Server rendelkezésre állási csoport konfigurálása az Azure Virtual Machines különböző régiókban](availability-group-manually-configure-multiple-regions.md).|
| **Adatbázis-tükrözés** |A különböző adatközpontokban futó elsődleges és tükrözött kiszolgálók a vész-helyreállításhoz. A kiszolgálót kiszolgálói tanúsítványokkal kell üzembe helyezni. SQL Server adatbázis-tükrözés nem támogatott az Azure-beli virtuális gépen SQL Server 2008 és SQL Server 2008 R2 esetén. <br/>![Adatbázis-tükrözés](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Biztonsági mentés és visszaállítás az Azure Blob Storage-ban** |A termelési adatbázisok biztonsági mentése egy másik adatközpontban, a vész-helyreállítás érdekében közvetlenül a blob Storage-ba.<br/>![Biztonsági mentés és visszaállítás](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>További információ: [SQL Server biztonsági mentése és visszaállítása az Azure Virtual Machinesban](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **SQL Server replikálása és feladatátvétele az Azure-ba Azure Site Recovery** |Az egyik Azure-adatközpont éles üzemi SQL Server a különböző Azure-adatközpontok Azure Storage-ba replikálása vész-helyreállítási célra.<br/>![Replikálás Azure Site Recovery használatával](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>További információkért tekintse meg a [SQL Server SQL Server vész-helyreállítási és-Azure site Recovery használatával történő védelemmel](../../../site-recovery/site-recovery-sql.md)foglalkozó témakört. |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hibrid IT: vész-helyreállítási megoldások
A rendelkezésre állási csoportokkal, adatbázis-tükrözéssel, naplózással és az Azure blog Storage szolgáltatással történő biztonsági mentéssel és visszaállítással vészhelyzeti helyreállítási megoldást használhat a SQL Server adatbázisaihoz.

| Technológia | Példa architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Néhány rendelkezésre állási másodpéldány az Azure-beli virtuális gépeken és a helyszínen futó más replikákban a helyek közötti vész-helyreállításhoz. Az üzemi hely lehet akár helyszíni, akár egy Azure-adatközpontban is.<br/>![Rendelkezésre állási csoportok](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Mivel az összes rendelkezésre állási másodpéldánynak ugyanabban a feladatátvevő fürtben kell lennie, a fürtnek mindkét hálózatra (több alhálózatos feladatátvevő fürtre) kell terjednie. Ehhez a konfigurációhoz VPN-kapcsolat szükséges az Azure és a helyszíni hálózat között.<br/><br/>Az adatbázisok sikeres vész-helyreállításához a vész-helyreállítási helyen is telepítenie kell egy replika tartományvezérlőt.<br/><br/>Az Azure-replika meglévő always on rendelkezésre állási csoportba való felvételéhez használhatja a SSMS-replika hozzáadása varázslót. További információ: oktatóanyag: az Always On rendelkezésre állási csoport kiterjesztése az Azure-ra. |
| **Adatbázis-tükrözés** |Egy partner, amely egy Azure-beli virtuális gépen, a másik pedig a helyszínen fut a helyek közötti vész-helyreállításhoz a kiszolgálói tanúsítványok használatával. A partnereknek nem kell ugyanabban a Active Directory tartományban lennie, és nincs szükség VPN-kapcsolatra.<br/>![Adatbázis-tükrözés](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Egy másik adatbázis-tükrözési forgatókönyv egy olyan partnert foglal magában, amely egy Azure-beli virtuális gépen fut, a másik pedig ugyanazon a Active Directory tartományban fut a helyek közötti vész-helyreállítás érdekében. [Az Azure-beli virtuális hálózat és a helyszíni hálózat közötti VPN-kapcsolatra](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) van szükség.<br/><br/>Az adatbázisok sikeres vész-helyreállításához a vész-helyreállítási helyen is telepítenie kell egy replika tartományvezérlőt. SQL Server adatbázis-tükrözés nem támogatott az Azure-beli virtuális gépen SQL Server 2008 és SQL Server 2008 R2 esetén. |
| **Napló szállítása** |Egy Azure-beli virtuális gépen és a helyszínen futó másik kiszolgálón, amely a helyek közötti vész-helyreállítást végzi. A naplózás a Windows fájlmegosztás függvénye, így az Azure-beli virtuális hálózat és a helyszíni hálózat közötti VPN-kapcsolat szükséges.<br/>![Naplóátvitel](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Az adatbázisok sikeres vész-helyreállításához a vész-helyreállítási helyen is telepítenie kell egy replika tartományvezérlőt. |
| **Biztonsági mentés és visszaállítás az Azure Blob Storage-ban** |A helyszíni éles adatbázisok biztonsági mentése közvetlenül az Azure Blob Storage-ba, a vész-helyreállítás érdekében.<br/>![Biztonsági mentés és visszaállítás](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>További információ: [SQL Server biztonsági mentése és visszaállítása az Azure Virtual Machinesban](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **SQL Server replikálása és feladatátvétele az Azure-ba Azure Site Recovery** |A helyszíni üzemi SQL Server közvetlenül az Azure Storage-ba replikálja a vész-helyreállítást.<br/>![Replikálás Azure Site Recovery használatával](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>További információkért tekintse meg a [SQL Server SQL Server vész-helyreállítási és-Azure site Recovery használatával történő védelemmel](../../../site-recovery/site-recovery-sql.md)foglalkozó témakört. |


## <a name="free-dr-replica-in-azure"></a>Ingyenes DR-replika az Azure-ban

Ha rendelkezik frissítési [garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), hibrid vész-helyreállítási (Dr) terveket is alkalmazhat SQL Server a passzív Dr-példányra vonatkozó további licencelési költségek nélkül.

Az alábbi képen a telepítő az Azure-beli virtuális gépeken futó SQL Server a 12 maggal rendelkező helyszíni SQL Server üzemelő példányok vész-helyreállítási replikájának használatával futtatja. A múltban a SQL Server 12 maggal kell rendelkeznie a helyszíni és az Azure Virtual Machines üzemelő példányhoz. Az új előny az Azure-beli virtuális gépeken futó passzív replika előnyeit kínálja. Most csak a helyi gépen futó SQL Server 12 magot kell használnia, amennyiben az Virtual Machines Azure-beli passzív replika vész-helyreállítási feltételei teljesülnek.

![Ingyenes DR-replika az Azure-ban](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

További információkért tekintse meg a [termék licencelési feltételeit](https://www.microsoft.com/licensing/product-licensing/products). 

Ha engedélyezni szeretné ezt az előnyt, navigáljon a [SQL Server virtuálisgép-erőforráshoz](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource), válassza a **Konfigurálás** lehetőséget a beállítások területen, majd válassza a vész- **helyreállítási** lehetőséget a **SQL Server licenc**alatt. Jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy ez a SQL Server VM passzív replikaként lesz használatban, majd válassza az **alkalmaz** lehetőséget a beállítások mentéséhez. 

![DR replika konfigurálása az Azure-ban](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Fontos szempontok az Azure-beli SQL Server HADR
Az Azure-beli virtuális gépek, a tárolók és a hálózatkezelés eltérő működési jellemzőkkel rendelkeznek, mint a helyszíni, nem virtualizált informatikai infrastruktúra. Az Azure HADR SQL Server megoldásának sikeres megvalósítása megköveteli, hogy Ismerje meg ezeket a különbségeket, és tervezze meg a megoldását.

### <a name="high-availability-nodes-in-an-availability-set"></a>Magas rendelkezésre állású csomópontok rendelkezésre állási csoportokban
Az Azure-beli rendelkezésre állási csoportok lehetővé teszik a magas rendelkezésre állású csomópontok különálló tartalék tartományokra (tartalék) és frissítési tartományokra (frissítési) történő elhelyezését. A mögöttes Azure platform a rendelkezésre állási csoportban lévő mindegyik virtuális gépnek kioszt egy frissítési tartományt és egy tartalék tartományt. Ez az adatközponton belüli konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető legyen, és megfelel a 99,95%-os Azure SLA-nak. Magas rendelkezésre állású telepítés konfigurálásához helyezze az összes résztvevő SQL virtuális gépet ugyanabban a rendelkezésre állási csoportba, hogy elkerülje az alkalmazás vagy az adatvesztést a karbantartási események során. Ugyanahhoz a rendelkezésre állási csoporthoz csak az azonos felhőalapú szolgáltatásban lévő csomópontok vehetnek részt. További információk: [Virtuális gépek rendelkezésre állásának kezelése](../../../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Magas rendelkezésre állású csomópontok egy rendelkezésre állási zónában
A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. Egy régión belüli Availability Zones fizikai elkülönítése megvédi az alkalmazásokat és az adatközpontok meghibásodási adatait azáltal, hogy legalább egy virtuális gépet elérhetővé tesz, és megfelel a 99,99%-os Azure SLA-nak. A magas rendelkezésre állás konfigurálásához helyezze a részt vevő SQL-alapú virtuális gépeket a régióban elérhető Availability Zonesek között. A virtuális gépek és a virtuális gépek közötti adatátviteli díjak további rendelkezésre állási zónában lesznek. További információ: [rendelkezésre állási zónák](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>A feladatátvevő fürt működése az Azure hálózatkezelésében
A nem RFC-kompatibilis DHCP szolgáltatás az Azure-ban azt eredményezheti, hogy a feladatátvevő fürt egyes konfigurációi sikertelenek lettek, mert a fürt hálózati neve ismétlődő IP-címet rendel hozzá, például ugyanazt az IP-címet, mint az egyik fürtcsomópont. Ez a probléma olyan rendelkezésre állási csoportok megvalósításakor jelent problémát, amelyek a Windows feladatátvételi fürtszolgáltatástól függenek.

Két csomópontos fürt létrehozásakor és online állapotba állításakor vegye figyelembe a következőket:

1. A fürt online állapotba kerül, majd a CSOMÓPONT1 dinamikusan hozzárendelt IP-címet kér a fürt hálózati neveként.
2. A DHCP szolgáltatás nem a NODE1's saját IP-címétől eltérő IP-címet kap, mivel a DHCP-szolgáltatás felismeri, hogy a kérés a CSOMÓPONT1 származik.
3. A Windows azt észleli, hogy a rendszer duplikált címeket rendel hozzá a CSOMÓPONT1 és a feladatátvevő fürt hálózati nevéhez, és az alapértelmezett fürtcsomópont nem fog online állapotba jutni.
4. Az alapértelmezett fürtcsomópont a CSOMÓPONT2-re kerül, amely a NODE1's IP-címet a fürt IP-címével kezeli, és az alapértelmezett fürtöt online állapotba helyezi.
5. Ha a CSOMÓPONT2 megkísérli a CSOMÓPONT1 való kapcsolódást, a CSOMÓPONT1-re irányuló csomagok soha nem hagyják el a CSOMÓPONT2, mert a NODE1's IP-címet saját maga oldja fel. A CSOMÓPONT2 nem tud kapcsolatot létesíteni a CSOMÓPONT1, majd elveszíti a kvórumot, és leállítja a fürtöt.
6. Addig is a CSOMÓPONT1 csomagokat küldhet a CSOMÓPONT2, de a CSOMÓPONT2 nem tud válaszolni. A CSOMÓPONT1 elveszíti a kvórumot, és leállítja a fürtöt.

Ez a forgatókönyv elkerülhető egy nem használt statikus IP-cím, például egy kapcsolati helyi IP-cím, például a 169.254.1.1 hozzárendelésével, hogy a fürt hálózati neve online állapotba kerüljön. A folyamat leegyszerűsítése érdekében lásd: a [Windows feladatátvevő fürt konfigurálása az Azure-ban rendelkezésre állási csoportok számára](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

További információ: [rendelkezésre állási csoportok konfigurálása az Azure-ban (GUI)](availability-group-azure-marketplace-template-configure.md).

### <a name="availability-group-listener-support"></a>Rendelkezésre állási csoport figyelő támogatása
A rendelkezésre állási csoport figyelők támogatottak a Windows Server 2008 R2, a Windows Server 2012, a Windows Server 2012 R2 és a Windows Server 2016 rendszert futtató Azure-beli virtuális gépeken. Ezt a támogatást a rendelkezésre állási csoport csomópontjait használó Azure-beli virtuális gépeken engedélyezett elosztott terhelésű végpontok használatával lehet elvégezni. A figyelőkhöz speciális konfigurációs lépéseket kell követnie, amelyek az Azure-ban és a helyszínen futó összes ügyfélalkalmazás esetében működnek.

A figyelő beállításának két fő lehetősége van: külső (nyilvános) vagy belső. A külső (nyilvános) figyelő internetkapcsolattal rendelkező Load balancert használ, és egy nyilvános virtuális IP-címmel (VIP) van társítva, amely az interneten keresztül érhető el. Egy belső figyelő belső terheléselosztó használatával működik, és csak ugyanazon a Virtual Networkon belüli ügyfeleket támogatja. A terheléselosztó típusaként engedélyeznie kell a közvetlen kiszolgáló visszaküldését. 

Ha a rendelkezésre állási csoport több Azure-alhálózatra is kiterjed (például az Azure-régiókat keresztező központi telepítés), az ügyfél-kapcsolódási karakterláncnak tartalmaznia kell a következőt: "**MultisubnetFailover = True**". Ennek eredményeképpen a párhuzamos kapcsolódás megkísérli a különböző alhálózatokban lévő replikákat. A figyelő beállításával kapcsolatos utasításokért lásd:

* [ILB-figyelő konfigurálása a rendelkezésre állási csoportokhoz az Azure-ban](availability-group-listener-powershell-configure.md).
* [Külső figyelő konfigurálása a rendelkezésre állási csoportokhoz az Azure-ban](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Továbbra is csatlakozhat az egyes rendelkezésre állási replikához, ha közvetlenül a szolgáltatási példányhoz csatlakozik. Emellett mivel a rendelkezésre állási csoportok visszamenőleg kompatibilisek az adatbázis-tükrözési ügyfelekkel, csatlakozhat a rendelkezésre állási replikához, például az adatbázis-tükrözési partnerekhez, ha a replikák az adatbázis-tükrözéshez hasonló módon vannak konfigurálva:

* Egy elsődleges replika és egy másodlagos replika
* A másodlagos replika nem olvashatóként van konfigurálva (**olvasható másodlagos** beállítás a **nem**értékre állítva)

Az ehhez az adatbázis-tükrözéshez hasonló, az ADO.NET-t vagy SQL Server Native Client-t használó ügyfél-kapcsolódási karakterlánc a következő:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

További információ az ügyfelek kapcsolatáról:

* [A kapcsolatok sztring kulcsszavainak használata SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Ügyfelek összekötése egy adatbázis-tükrözési munkamenettel (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Csatlakozás a rendelkezésre állási csoport figyelője számára a hibrid informatikai szolgáltatásban](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [A rendelkezésre állási csoport figyelők, az ügyfélkapcsolatok és az alkalmazások feladatátvétele (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Adatbázis-tükrözési kapcsolatok karakterláncok használata rendelkezésre állási csoportokkal](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Hálózati késés a hibrid informatikai szolgáltatásban
A HADR-megoldás üzembe helyezésével feltételezhető, hogy a helyszíni hálózat és az Azure közötti idő nagy hálózati késéssel járhat. Amikor replikákat telepít az Azure-ba, szinkron véglegesítés helyett aszinkron commit-t kell használnia a szinkronizálási módhoz. Az adatbázis-tükrözési kiszolgálók helyszíni és Azure-beli üzembe helyezése esetén a nagy teljesítményű üzemmódot használja a magas biztonsági üzemmód helyett.

### <a name="geo-replication-support"></a>Geo-replikáció támogatása
A Geo-replikáció az Azure-lemezeken nem támogatja ugyanazon adatbázis adatfájlját és naplófájlját, hogy külön lemezen legyenek tárolva. A GRS egymástól függetlenül és aszinkron módon replikálja az egyes lemezek változásait. Ez a mechanizmus garantálja az írási sorrendet a földrajzilag replikált másolaton belül egy lemezen, a több lemez földrajzilag replikált példányai között azonban nem. Ha úgy konfigurálja az adatbázist, hogy az adatfájlját és a naplófájlját külön lemezeken tárolja, a katasztrófa utáni helyreállított lemezek a naplófájlban lévő adatfájlnak egy újabb másolatát is tartalmazhatják, amely megtöri a SQL Server írási naplóját és a tranzakciók savas tulajdonságait. Ha nem szeretné letiltani a földrajzi replikálást a Storage-fiókon, akkor az adott adatbázis összes adat-és naplófájlját ugyanazon a lemezen kell megőrizni. Ha az adatbázis mérete miatt egynél több lemezt kell használnia, a fentiekben felsorolt vész-helyreállítási megoldások egyikét kell telepítenie az adatredundancia biztosítása érdekében.

## <a name="next-steps"></a>Következő lépések
Ha SQL Server használatával szeretne Azure-beli virtuális gépet létrehozni, tekintse [meg a SQL Server virtuális gép üzembe helyezése az Azure](create-sql-vm-portal.md)-ban című témakört.

Az Azure-beli virtuális gépeken futó SQL Server legjobb teljesítményének megismeréséhez tekintse [meg az Virtual Machines Azure-beli SQL Server teljesítményének bevált eljárásaival](performance-guidelines-best-practices.md)foglalkozó témakör útmutatását.

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

### <a name="other-resources"></a>Egyéb erőforrások
* [Új Active Directory erdő telepítése az Azure-ban](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Feladatátvevő fürt létrehozása rendelkezésre állási csoportok számára az Azure-beli virtuális gépen](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

