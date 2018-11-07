---
title: Magas rendelkezésre állást és az SQL Server Vészhelyreállítása |} A Microsoft Docs
description: Ismerteti azokat a különböző fájltípusok közül a HADR stratégiák az Azure Virtual Machines szolgáltatásban futó SQL Serverhez.
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
ms.openlocfilehash: 463ef5f4a655617074915078fb4ced9e596f8957
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257714"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Magas rendelkezésre állás és vészhelyreállítás az Azure-beli SQL Server-alapú virtuális gépeken

A Microsoft Azure virtuális gépeken (VM) az SQL Server segítségével csökkentheti a magas rendelkezésre állás és vészhelyreállítás helyreállítási (HADR) adatbázis-megoldás. Az Azure virtual machines, csak az Azure- és hibrid megoldásokat, legtöbb az SQL Server HADR megoldás támogatottak. A csak az Azure-megoldás a teljes HADR rendszer fut, az Azure-ban. Hibrid konfigurációban a megoldás részét képező Azure-beli és az egyéb részben futtatások helyszíni a szervezet futtatja. Az Azure-környezet rugalmasságát lehetővé teszi, hogy részben vagy teljesen áthelyezése az Azure-bA a költségvetés és az SQL Server adatbázis-rendszerek HADR követelményeinek kielégítéséhez.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Egy HADR megoldás szükség ismertetése
Feladata, hogy győződjön meg arról, hogy az adatbázis-rendszer rendelkezik-e a HADR képességeket, amely a szolgáltatásiszint-szerződés (SLA) igényel. A tény, hogy az Azure biztosít magas rendelkezésre állású mechanizmusok, például a szolgáltatás javítása felhőszolgáltatásokhoz és virtuális gépekhez helyreállítási hibaészlelés nem nem magát szavatolja teljesítheti a kívánt szolgáltatásiszint-szerződés. Ezek a mechanizmusok a magas rendelkezésre állású virtuális gépet, de nem magas rendelkezésre állásának belül a virtuális gépeken futó SQL Server védelme. Az SQL Server-példány sikertelen lesz, míg a virtuális gép online és kifogástalan állapotú lehetőség. Ezenkívül akkor is igaz, a magas rendelkezésre állás az Azure által biztosított mechanizmus lehetővé teszi az állásidő eseményeket, mint például a szoftver vagy hardver-meghibásodásokkal és operációsrendszer-frissítést történő helyreállítás miatt a virtuális gépek számára.

Georedundáns tárolás (GRS) az Azure-ban, amely georeplikációs szolgáltatás segítségével valósul meg, továbbá nem lehet az adatbázisok egy megfelelő vész-helyreállítási megoldást. Georeplikáció aszinkron módon elküldi az adatokat, mert a legutóbbi frissítések elveszett katasztrófa lehet. Georeplikáció korlátozások kapcsolatos további információkat olvashat: a [georeplikáció nem támogatja a különálló lemezek adathoz és naplófájlhoz](#geo-replication-support) szakaszban.

## <a name="hadr-deployment-architectures"></a>HADR üzembe helyezési architektúra
Az SQL Server HADR támogatott technológiákra van az Azure-ban a következők:

* [Always On rendelkezésre állási csoportok](https://technet.microsoft.com/library/hh510230.aspx)
* [Mindig a Feladatátvevőfürt-példányok](https://technet.microsoft.com/library/ms189134.aspx)
* [Naplóküldés](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server biztonsági mentés és visszaállítás Azure Blob Storage szolgáltatással](https://msdn.microsoft.com/library/jj919148.aspx)
* [Az adatbázis-tükrözés](https://technet.microsoft.com/library/ms189852.aspx) – az SQL Server 2016 elavult

Úgy, hogy a technológiák együttesen egy SQL Server-megoldást, amely magas rendelkezésre állású és vész-helyreállítási lehetőségei is lehetőség. Attól függően, a technológiát használja egy hibrid telepítésben a az Azure virtual network VPN-alagút lehet szükség. Az alábbi szakaszok bemutatják, a példában üzembe helyezési architektúra része.

## <a name="azure-only-high-availability-solutions"></a>Csak az Azure-: magas rendelkezésre állású megoldások

Egy magas rendelkezésre állású megoldás az SQL Server Always On rendelkezésre állási csoportok – nevű rendelkezésre állási csoportok az adatbázis szintjén is. Is létrehozhat egy magas rendelkezésre állású megoldás egy példány szintjén az mindig a Feladatátvevőfürt-példányok – feladatátvételi fürtbeli példányok. A további redundancia biztosítása érdekében létrehozhat redundancia mindkét szinten rendelkezésre állási csoportok létrehozása a feladatátvételi fürtbeli példányok. 

| Technológia | Példa-architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Rendelkezésre állási másodpéldányt futtató Azure virtuális gépek ugyanabban a régióban a magas rendelkezésre állást biztosít. Kell konfigurálása a tartományvezérlő virtuális Gépeket, mivel a Windows feladatátvételi fürtszolgáltatás szükséges Active Directory-tartományban.<br/> ![Rendelkezésre állási csoportok](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>További információkért lásd: [rendelkezésre állási csoportok konfigurálása az Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Feladatátvevőfürt-példányok** |A feladatátvevő fürt példány (FCI), megosztott tárolóra van szükség, amely 3 különböző módon hozható létre.<br/><br/>1. Csatolt storage használata az Azure-beli virtuális gépeken futó kétcsomópontos feladatátvevő fürt [Windows Server 2016 közvetlen tárolóhelyek \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) biztosít egy szoftveralapú virtuális SAN.<br/><br/>2. A storage-külső fürtözési megoldások által támogatott Azure-beli virtuális gépeken futó kétcsomópontos feladatátvevő fürt. Egy olyan példát, amely az SIOS DataKeeper használja, lásd: [egy fájlmegosztást a Feladatátvételi fürtszolgáltatás és a 3. fél szoftverek az SIOS DataKeeper magas rendelkezésre állású](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Az iSCSI-cél távoli Azure-beli virtuális gépeken futó kétcsomópontos feladatátvevő fürt megosztott blokktárolót expressroute-on keresztül. NetApp Private Storage (NPS) például iSCSI-tároló az Azure virtuális gépekre Equinix expressroute-on keresztül tesz elérhetővé.<br/><br/>A külső megosztott tárolók és a replikációs megoldások, forduljon a szállító feladatátvételi lévő adatok elérésével kapcsolatos problémákkal.<br/><br/>Vegye figyelembe, hogy az FCI-t a [Azure File storage](https://azure.microsoft.com/services/storage/files/) még nem támogatott, mert ez a megoldás nem használja a Premium Storage. Dolgozunk ennek hamarosan támogatása érdekében. |

## <a name="azure-only-disaster-recovery-solutions"></a>Csak az Azure-: a vészhelyreállítási megoldások
A vész-helyreállítási megoldást rendelkezik az SQL Server-adatbázisait az Azure-ban a rendelkezésre állási csoportokkal, az adatbázis-tükrözés vagy biztonsági mentés, és állítsa vissza a storage-blobokat.

| Technológia | Példa-architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Rendelkezésre állási másodpéldányt futtató több adatközpontra elosztva több Azure-beli virtuális gépeken vész-helyreállítási. A régiók közötti megoldás védi a webhely teljes üzemkimaradások esetére. <br/> ![Rendelkezésre állási csoportok](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Egy adott régión belül az összes replika ugyanazon a felhőszolgáltatáson, és ugyanazon a Vneten belül kell lennie. Minden egyes régió egy külön virtuális hálózatot fog működni, mert ezek a megoldások virtuális hálózat virtuális hálózatok közötti kapcsolat szükséges. További információkért lásd: [az Azure Portalon a virtuális hálózatok közötti kapcsolat konfigurálása](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Részletes útmutatásért lásd: [egy SQL Server rendelkezésre állási csoport konfigurálása Azure-beli virtuális gépeken különböző régiókban lévő](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Az adatbázis-tükrözés** |Egyszerű és a tükrözött és a vész-helyreállítási különböző adatközpontokban futó kiszolgálók. Kiszolgálói tanúsítványok használatával, mert egy active directory-tartomány nem ívelhet át több adatközpontban kell telepítenie.<br/>![Az adatbázis-tükrözés](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Biztonsági mentés és visszaállítás Azure Blob Storage szolgáltatással** |Éles adatbázisok biztonsági mentését közvetlenül vész-helyreállítási egy másik adatközpontban lévő blobtárolóba.<br/>![Biztonsági mentés és visszaállítás](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>További információkért lásd: [biztonsági mentése és visszaállítása az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikálás és feladatátvétel az SQL Server az Azure-bA az Azure Site Recoveryvel** |Éles SQL Server közvetlenül az Azure Storage a különböző Azure-adatközpont vészhelyreállítási replikált egy Azure-adatközpontban.<br/>![Replikálása az Azure Site Recovery használatával](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>További információkért lásd: [védelme SQL Server az SQL Server-vészhelyreállítás és az Azure Site Recovery használatával](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>A hibrid informatikai: A vészhelyreállítási megoldások
Rendelkezik egy vész-helyreállítási megoldás az SQL Server-adatbázisok egy hibrid-IT-környezet rendelkezésre állási csoportok, az adatbázis-tükrözés, a naplóküldés és a biztonsági mentés, és állítsa vissza az Azure-blog storage.

| Technológia | Példa-architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Egyes rendelkezésre állási másodpéldányok futó Azure virtuális gépekkel és más a helyszínen futó webhelyek közötti vész-helyreállítási replika. Az élesben használt helyet is lehet a helyszínen vagy az Azure-adatközpontban.<br/>![Rendelkezésre állási csoportok](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Az összes rendelkezésre állási másodpéldányok a feladatátvevő fürtön kell lennie, mert a fürt mindkét hálózatok (több alhálózatos feladatátvevő fürtöket) kell kiterjednek. Ehhez a konfigurációhoz szükséges VPN-kapcsolat az Azure és a helyszíni hálózat között.<br/><br/>Sikeres vész-helyreállítási az adatbázisok telepítenie kell, a vész-helyreállítási webhelyként replika tartományvezérlő.<br/><br/>A replika hozzáadása varázsló használható az SSMS egy Azure-replika hozzáadása egy meglévő Always On rendelkezésre állási csoport. További információkért lásd az oktatóanyag: az Always On rendelkezésre állási csoport kiterjesztése az Azure-bA. |
| **Az adatbázis-tükrözés** |Az Azure virtuális gép és a többi futó fut egy partneri helyszíni többhelyes vész-helyreállítási tanúsítványok használatával. Partnerek nem kell lennie az Active Directory-tartományhoz, és nem VPN-kapcsolat nem szükséges.<br/>![Az adatbázis-tükrözés](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Egy másik adatbázis-tükrözési forgatókönyv az Azure virtuális gép és a többi futó helyszíni többhelyes vész-helyreállítási azonos Active Directory tartományban fut egy partneri foglalja magában. A [az Azure virtuális hálózat és a helyszíni hálózat közti VPN-kapcsolat](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) megadása kötelező.<br/><br/>Sikeres vész-helyreállítási az adatbázisok telepítenie kell, a vész-helyreállítási webhelyként replika tartományvezérlő. |
| **Naplóküldés** |Az Azure virtuális gép és a többi futó helyszíni többhelyes vész-helyreállítási t futtató kiszolgálón. Naplóküldés függ Windows fájlmegosztás, így az Azure virtuális hálózat és a helyszíni hálózat közötti VPN-kapcsolat megadása kötelező.<br/>![Naplóküldés](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Sikeres vész-helyreállítási az adatbázisok telepítenie kell, a vész-helyreállítási webhelyként replika tartományvezérlő. |
| **Biztonsági mentés és visszaállítás Azure Blob Storage szolgáltatással** |A helyszíni éles adatbázisok biztonsági mentését közvetlenül az Azure blob storage vész-helyreállítási.<br/>![Biztonsági mentés és visszaállítás](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>További információkért lásd: [biztonsági mentése és visszaállítása az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikálás és feladatátvétel az SQL Server az Azure-bA az Azure Site Recoveryvel** |A helyszíni éles közvetlenül az Azure Storage vész-helyreállítási replikált SQL Server.<br/>![Replikálása az Azure Site Recovery használatával](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>További információkért lásd: [védelme SQL Server az SQL Server-vészhelyreállítás és az Azure Site Recovery használatával](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Az Azure-beli SQL Server HADR fontos szempontjai
Az Azure virtuális gépek, tárolási és hálózatkezelési, mint egy helyszíni informatikai infrastruktúrával nem virtualizált különböző működési jellemzőkkel rendelkeznek. Az Azure-ban HADR SQL Server-megoldások sikeres végrehajtása szükséges, hogy ezek a különbségek megértéséhez, és a megoldás kialakításakor megtervezése.

### <a name="high-availability-nodes-in-an-availability-set"></a>Magas rendelkezésre állású csomópontok egy rendelkezésre állási csoportban
Az Azure-beli rendelkezésre állási csoportok lehetővé teszik a magas rendelkezésre állású csomópontok helyezze külön tartalék tartományok és frissítési tartományok (frissítési). Az Azure virtuális gépek ugyanazon rendelkezésre állási csoportban kell helyezni telepítenie kell őket az ugyanazon felhőszolgáltatásban. Csak az ugyanazon a felhőszolgáltatáson csomópontok vehet részt az ugyanazon rendelkezésre állási csoportban. További információk: [Manage the Availability of Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Virtuális gépek rendelkezésre állásának kezelése).

### <a name="failover-cluster-behavior-in-azure-networking"></a>A feladatátvevő fürt viselkedését az Azure-hálózatok
A nem RFC-kompatibilis DHCP-szolgáltatás az Azure-ban bizonyos feladatátvételi létrehozása sikertelen lesz, a fürthálózat nevének érvényessége a felhasználóhoz ismétlődő IP-címet, például az egyik fürtcsomópont ugyanazon IP-cím miatt fürtkonfigurációk okozhat. Ez a hiba, amikor rendelkezésre állási csoportokat, amelyek attól függ, a Windows feladatátvevő fürtszolgáltatást megvalósítása.

Amikor egy két csomópontos fürt létrehozása és online állapotba, vegye figyelembe a forgatókönyvet:

1. A fürt online állapotba kerül, majd csomópont1 kér egy dinamikusan hozzárendelt IP-címet a fürt hálózati neve.
2. Nincs IP-cím csomópont1 saját IP-cím nem a DHCP szolgáltatás van megadva, mivel a DHCP szolgáltatás felismeri, hogy a kérelem származik csomópont1 magát.
3. Windows észleli, hogy ismétlődő címet csomópont1 és a feladatátvevő fürt hálózati neve is hozzá van rendelve, és a fürt alapértelmezett csoport nem lehet online állapotba.
4. Az alapértelmezett fürtcsoport csomópont2, amely kezeli a fürt IP-cím csomópont1 az IP-cím és az alapértelmezett fürt csoport online állapotba hozása helyezi át.
5. Amikor csomópont2 csomópont1 csatlakozni próbál, a csomópont1 irányuló csomagokat soha ne hagyja csomópont2, mert maga legyen hozzárendelve csomópont1 az IP-cím. Csomópont2 csomópont1 kapcsolatot nem lehet létrehozni, majd elveszti a kvórumot, és a fürt leáll.
6. Addig is csomópont1, csomópont2 is küldése csomagok, de nem tud válaszolni, csomópont2. Csomópont1 elveszti a kvórumot, és a fürt leáll.

Ebben a forgatókönyvben egy nem használt statikus IP-címet, például egy kapcsolatszintű 169.254.1.1, például IP-cím hozzárendelése a fürthálózat nevének annak érdekében, hogy a fürt hálózati név online állapotba elkerülhető. A folyamat leegyszerűsítése érdekében tekintse meg a [konfigurálása Windows feladatátvevő fürt az Azure-beli rendelkezésre állási csoportokra vonatkozó](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

További információkért lásd: [rendelkezésre állási csoportok konfigurálása az Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Rendelkezésre állási csoport figyelőjének támogatása
Rendelkezésre állási csoport figyelője támogatottak a Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016 rendszert futtató Azure virtuális gépeken. Ez a támogatás az Azure virtuális gépek, amelyek a rendelkezésre állási csoport csomópontjain engedélyezve elosztott terhelésű végpontok használata tette lehetővé. Mindkét ügyfélalkalmazások az Azure, valamint azok a helyszínen futó futó működéséhez a figyelők esetében külön konfigurációs lépéseket kell követnie.

A figyelővel fenntartott beállításához két fő módja van: külső (nyilvános) vagy belső. A külső (nyilvános) figyelő használó internetkapcsolattal rendelkező load balancer és a egy nyilvános virtuális IP-cím (VIP), amely elérhető az interneten keresztül. Belső figyelő belső terheléselosztót használ, és csak az azonos virtuális hálózatban található ügyfelek támogatja. Terheléselosztó típusa vagy terheléselosztást, engedélyeznie kell a közvetlen kiszolgálói válasz. 

Ha a rendelkezésre állási csoportot (például az üzembe helyezés Azure-régiókban is magába foglaló) Azure alhálózatokra is kiterjed, tartalmaznia kell az ügyfél-kapcsolati karakterlánc "**MultisubnetFailover = True**". Az eredmény párhuzamos kapcsolódási kísérletek, a replikákat a különböző alhálózatokon. Egy figyelő beállításával kapcsolatos utasításokért lásd:

* [ILB-figyelő rendelkezésre állási csoportok konfigurálása az Azure-ban](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Külső figyelő rendelkezésre állási csoportok konfigurálása az Azure-ban](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Továbbra is csatlakozhat minden rendelkezésre állási másodpéldány külön-külön közvetlenül csatlakozik a szolgáltatáspéldányt. Is mivel a rendelkezésre állási csoportok visszamenőleg kompatibilis az adatbázis-tükrözési ügyfelek, csatlakoztathatja a rendelkezésre állási replikához, például adatbázis-tükrözési partnerei, mindaddig, amíg a replikák konfigurált adatbázis-tükrözés hasonló:

* Egy elsődleges replika és a egy másodlagos másodpéldány
* A másodlagos replika van beállítva, nem olvasható (**olvasható másodlagos** beállítás **nem**)

Egy példa ügyfél-kapcsolati sztringet, amely megfelel az adatbázis tükrözési-szerű konfigurációja ADO.NET-vagy SQL Server Native Client nem éri el:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Ügyfélkapcsolat további információkért lásd:

* [Kapcsolati karakterlánc kulcsszó használata az SQL Server natív ügyfél](https://msdn.microsoft.com/library/ms130822.aspx)
* [Az ügyfelek csatlakozni az adatbázis-tükrözési munkamenetben (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Rendelkezésre állási csoport figyelőjével hibrid informatikai csatlakozik](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Rendelkezésre állási csoport figyelője ügyfélkapcsolat és alkalmazás feladatátvétel (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Kapcsolati karakterláncok adatbázis-tükrözés használata a rendelkezésre állási csoportok](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>A hibrid informatikai hálózati késés
Üzembe kell helyeznie a HADR megoldás feltételezve, hogy az idő alatt nagy hálózati késést a helyszíni hálózat és az Azure között lehet. Replikák üzembe az Azure-ba, amikor a szinkronizálási mód az aszinkron véglegesítésű használjon szinkron véglegesítést helyett. Ha az adatbázis-tükrözés kiszolgálók üzembe helyezése a helyszíni, mind az Azure-ban, a nagy teljesítményű mód használata helyett a magas biztonsági üzemmódú.

### <a name="geo-replication-support"></a>Georeplikáció támogatása
Georeplikáció az Azure-lemezek nem támogatja az adatfájlban és a naplófájlt is tárolja a különálló lemezek ugyanabban az adatbázisban. GRS egyes lemezek módosítását replikálja, függetlenül és aszinkron módon történik. Ez a mechanizmus biztosítja egyetlen lemezre a georeplikált példányán, de több lemez georeplikált példánya között nem a írási sorrend. Ha konfigurál egy adatbázist a az adatfájlban és a naplófájl a különálló lemezek, a helyreállított lemezeket egy vészhelyzetet követően tartalmazhat naprakész állapotban fájl egy példányát az adatokat, mint a naplófájlt, amely az SQL Server és a tranzakció válaszide ACID tulajdonságainak írási előre bejelentkezési működésképtelenné válik ctions. Ha letiltását georeplikáció a storage-fiók nem rendelkezik, érdemes megtartani az összes adathoz és naplófájlhoz egy adott adatbázis ugyanazon a lemezen. Ha egynél több lemezt, mert az adatbázis méretét kell használnia, kell az adatokat a redundancia biztosítása érdekében a fent felsorolt vészhelyreállítási megoldások üzembe helyezéséhez.

## <a name="next-steps"></a>További lépések
Ha egy Azure virtuális gép létrehozása az SQL Server van szüksége, tekintse meg [kiépítése egy SQL Server virtuális gépet az Azure-ban](virtual-machines-windows-portal-sql-server-provision.md).

Egy Azure virtuális gépeken futó SQL Serverről a lehető legjobb teljesítményt lekéréséhez lásd az útmutató [teljesítmény az Azure Virtual machines gépeken az SQL Server ajánlott eljárásai](virtual-machines-windows-sql-performance.md).

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos témaköröket talál [SQL Server Azure virtuális gépeken](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Egyéb erőforrások
* [Új Active Directory-erdő telepítése az Azure-ban](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Feladatátvevő fürt létrehozása az Azure-beli virtuális gépen rendelkezésre állási csoportok](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

