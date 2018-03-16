---
title: "Egy fájlkiszolgáló védelméhez az Azure Site Recovery használatával"
description: "Ez a cikk ismerteti, hogyan védi a fájlkiszolgáló Azure Site Recovery segítségével"
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: f53a8641a50a6c968a6ba7b841e0e8f938b5d9f6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Egy fájlkiszolgáló védelméhez az Azure Site Recovery használatával 

Az [Azure Site Recovery](site-recovery-overview.md) működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery koordinálja a vészhelyreállítás Azure virtuális gépek (VM) és a helyszíni gépeket, valamint a kezelésére. Vész-helyreállítási magában foglalja a replikáció, feladatátvétel és helyreállítás a különböző munkaterhelések.

Ez a cikk ismerteti, hogyan védi meg a fájlkiszolgáló a Site Recovery segítségével, és más ajánlásokat megfelelően különböző környezetekben. 

- [Azure IaaS fájl server gépek replikálása](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Egy helyszíni fájlkiszolgáló replikálásához a Site Recovery segítségével](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Fájl kiszolgáló architektúrája
Nyissa meg az elosztott fájlmegosztási rendszer célja, hogy adjon meg egy olyan környezetben, ahol a földrajzilag elosztott felhasználók egy csoportjánál együttműködhetnek hatékonyan használhatják a fájlokat, és biztosítani kell, hogy integritási igényeik lépnek érvénybe. Egy tipikus helyszíni fájl server ökoszisztémáját nagyszámú egyidejű felhasználót és nagyszámú tartalomelem támogató replikációs ütemezési és sávszélesség-szabályozás Distributed File System replikációs (DFSR) használ. 

Elosztott fájlrendszer replikációs szolgáltatása, távoli különbözeti tömörítés (RDC), amely segítségével hatékonyan frissítésfájlok korlátozott sávszélességű hálózaton keresztül ismert tömörítési algoritmust alkalmazza. Fájlok a beszúrások, eltávolítására és átrendezést egyaránt képes észlelni az adatok észlel. Elosztott fájlrendszer-replikációs engedélyezték, így csak a módosított fájlblokkokat replikálja, ha a fájlok is frissülnek. Van még fájl kiszolgálói környezetekben, ahol készít a napi biztonsági mentései a nem csúcsidőre időzítést, amely automatikusan igazodnak a vész-igényekre. Elosztott fájlrendszer replikációs szolgáltatása nincs implementálva.

A következő ábra szemlélteti a fájl kiszolgálói környezet az elosztott fájlrendszer-replikációs megvalósítva.
                
![Elosztott fájlrendszer-replikációs architektúrája](media/site-recovery-file-server/dfsr-architecture.JPG)

Az előző ábrán aktívan nevű tagok több fájlkiszolgáló részt fájlok replikálására a replikációs csoport között. A replikált mappa tartalmát elérhetők az ügyfelek által küldött kérések vagy a tagok, még akkor is, ha tagja offline állapotba kerül.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>A fájlkiszolgálók vész helyreállítási javaslatok

* **Egy fájlkiszolgáló replikálásához a Site Recovery segítségével**: fájlkiszolgálók replikálható az Azure Site Recovery segítségével. Ha egy vagy több helyszíni fájlkiszolgálókon nem érhető el, a helyreállítási virtuális gépek tehető az Azure-ban. A virtuális gépeket is az ügyfelektől érkező kérelmek kiszolgálását majd helyszíni, feltéve, hogy pont-pont VPN-kapcsolatot, amely Azure Active Directory konfigurálva van. Ez a módszer egy elosztott fájlrendszer-replikációs konfigurált környezet vagy egy egyszerű fájl kiszolgálói környezet esetén nem DFSR használható. 

* **Terjessze ki az Azure infrastruktúra-szolgáltatási virtuális gép elosztott fájlrendszer-replikációs**: fürtözött fájlkiszolgálók kiszolgálói környezetben az elosztott fájlrendszer-replikációs megvalósítva, kiterjesztheti a helyszíni elosztott fájlrendszer replikációs szolgáltatása az Azure-bA. Egy Azure virtuális gép majd engedélyezve van a Fájlkiszolgáló szerepkör végrehajtásához. 

    * Miután a függőségeit, pont-pont VPN-kapcsolat és az Active Directory kezeli, és elosztott fájlrendszer replikációs szolgáltatása rendelkezésre áll, ha egy vagy több helyszíni fájl kiszolgáló nem érhető el, ügyfelek csatlakozhatnak az Azure virtuális Gépen, a kérelmek szolgál.

    * Ezt a módszert is használhatja, ha a virtuális gépek kell beállítani, a Site Recovery által nem támogatott. Példa: egy megosztott fürtlemezre mutat, egyes esetekben gyakori használati módszereinek fájl kiszolgálói környezetekben. Elosztott fájlrendszer-replikációs közepes változási sebessége a kis sávszélességű környezetekben is működik. Fontolja meg a további költségeket az Azure virtuális gép rendelkezik, és folyamatosan futó kell. 

* **Azure fájlszinkronizálás használja replikálja a fájlok**: Ha azt tervezi, a felhő, vagy egy Azure virtuális gép már használ, fájlszinkronizálás használhatja. Fájlszinkronizálás kínál a teljes körűen felügyelt fájlmegosztások a felhőben, amelyek elérhetők a szabványos szinkronizálása [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (SMB) protokollt. Azure fájlmegosztásokat majd lehet csatlakoztatni egyidejűleg Windows, Linux és macOS felhőalapú vagy helyszíni központi telepítések során. 

A következő diagram segít meghatározni, melyik stratégia fájl kiszolgálói környezetre.

![döntési fája](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>A vész-helyreállítási Azure döntéseket tényezőkről

|Környezet  |Ajánlás  |Megfontolandó szempontok |
|---------|---------|---------|
|Fájl kiszolgálói környezet vagy elosztott fájlrendszer-replikációs nélkül|   [A Site Recovery replikáláshoz használni](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    A Site Recovery nem támogatja a megosztott lemezfürtöket vagy hálózati tárolóeszközök (NAS). Ha a környezet ezeket a konfigurációkat, használja az egyéb módszerek bármelyikét. <br> A Site Recovery nem támogatja az SMB 3.0-s. A replikált virtuális gép csak akkor, ha a fájlokon végrehajtott változtatások az eredeti helyre a fájlok a változtatásokat foglalja magában.
|Az elosztott fájlrendszer replikációs szolgáltatása a fájl kiszolgálói környezet     |  [Terjessze ki a DFSR Azure IaaS virtuális gépként](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      Elosztott fájlrendszer replikációs szolgáltatása is rendkívül sávszélesség-crunched környezetekben is működik. Ez a módszer egy Azure virtuális Gépen, hogy működik-e, és folyamatosan futó igényli. A tervezés fiókot használja a VM költsége van szüksége.         |
|Azure IaaS virtuális gép     |     [Fájl szinkronizálása ](#use-azure-file-sync-service-to-replicate-your-files)   |     Ha fájlszinkronizálás vész-helyreállítási forgatókönyv használ, a feladatátvétel során meg kell tennie győződjön meg arról, hogy a fájlmegosztások számára érhetők el az ügyfélszámítógép átlátható módon manuális műveletek. Fájl Sync futtatásához telepíteni kell a is meg kell nyitni az ügyfélszámítógépen a 445-ös porton.     |


### <a name="site-recovery-support"></a>Webhely-helyreállítási támogatás
Mivel a Site Recovery replikációs alkalmazás független, ezek a javaslatok várt tartsa a következő forgatókönyvek esetén true.
| Forrás    |Egy másodlagos helyre    |To Azure
|---------|---------|---------|
|Azure| -|Igen|
|Hyper-V|   Igen |Igen
|VMware |Igen|   Igen
|Fizikai kiszolgáló|   Igen |Igen
 

> [!IMPORTANT]
> A következő három módszer bármelyikével folytatás előtt győződjön meg arról, hogy ezeket a függőségeket vannak szükséges beállításokat.

**Hely-hely kapcsolatot**: a helyszíni hely és az Azure-hálózat közvetlen kapcsolatot kell létrehozni a kiszolgálók közötti kommunikáció lehetővé tételéhez. Egy Azure virtuális hálózatra, a vész-helyreállítási helyen használt biztonságos webhelyek VPN-kapcsolatot használjon. További információkért lásd: [egy helyszíni hely és az Azure virtuális hálózat közötti pont-pont VPN kapcsolatot létesíteni](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Az Active Directory**: elosztott fájlrendszer replikációs szolgáltatása az Active Directory függ. Ez azt jelenti, hogy a helyi tartományvezérlővel rendelkező Active Directory-erdő az időtartam a vész-helyreállítási helyet az Azure-ban. Akkor is, ha az elosztott fájlrendszer replikációs szolgáltatása, nem használják, ha a kívánt felhasználók számára szeretné a hozzáférést vagy a hozzáférés ellenőrzött, ezeket a lépéseket kell végrehajtania. További információkért lásd: [kiterjesztése a helyszíni Azure Active Directory](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Katasztrófa utáni helyreállítás javaslat Azure IaaS virtuális gépekhez

Ha konfigurálja, és vészhelyreállítás Azure IaaS virtuális gépeket üzemeltet a fájlkiszolgálók kezelése, választhat két lehetőség közül választhat, alapján, hogy kívánja-e áthelyezése [Azure fájlok](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Fájl Sync szolgáltatás használatával](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [A Site Recovery használata](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Fájl-szinkronizálást használ a fájlok IaaS virtuális gépként üzemeltetett replikálásához

Az Azure Files használatával teljes mértékben lecserélheti vagy kiegészítheti a hagyományos helyszíni fájlkiszolgálókat vagy NAS-eszközöket. Azure fájlmegosztásokat is lehessen replikálni fájlszinkronizálás Windows-kiszolgálók, vagy a helyszínen vagy a felhőben, a teljesítmény- és az adatok felhasználási elosztott gyorsítótárazás. A vész helyreállítási javaslat Azure virtuális gépekhez, amely ugyanezeket a funkciókat, mint a hagyományos fájlkiszolgálók hajtsa végre a következő lépésekből áll:
* A Site Recovery segítségével gépek védelmére. Kövesse a [replikálása az Azure virtuális gép egy másik Azure-régiót](azure-to-azure-quickstart.md).
* Fájl-szinkronizálást használ a fájlok replikálni a virtuális Gépet, amely úgy működik, mint a fájlkiszolgáló a felhőbe.
* A Site Recovery használata [helyreállítási terv](site-recovery-create-recovery-plans.md) parancsfájlok hozzáadása a szolgáltatás [Azure fájlmegosztás csatlakoztatása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) és elérni a megosztást a virtuális gépen.

Az alábbi lépéseket röviden fájlszinkronizálás használata:

1. [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha úgy dönt, írásvédett georedundáns tárolás a storage-fiókok, kap olvasási hozzáféréssel az adatok egy katasztrófa esetén a másodlagos régióba. További információkért lásd: [az Azure file megosztás vész-helyreállítási stratégiák](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Indítsa el a fájlszinkronizálás](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) Azure fájlkiszolgáló.
4. Hozzon létre egy szinkronizálási csoportot. A szinkronizálási csoporton belüli végpontok tartják szinkronban vannak egymással. A szinkronizálási csoport tartalmaznia kell legalább egy felhő végpontot, amely jelöli az Azure fájlmegosztások. A szinkronizálás csoport is tartalmaznia kell egy kiszolgáló végpont, amely a Windows server egy elérési utat képvisel.
5. A fájlok vannak most szinkronban a Azure fájlmegosztás és a helyszíni kiszolgáló között.
6. Legyen katasztrófahelyzet esetén a helyszíni környezetben, végezzen el egy feladatátvételt a használatával egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Vegye fel a parancsfájlt, amellyel [Azure fájlmegosztás csatlakoztatása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) és elérni a megosztást a virtuális gépen.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Fájl server IaaS virtuális gépként replikálásához a Site Recovery segítségével

Ha a helyi ügyfelek férnek hozzá az infrastruktúra-szolgáltatási fájl kiszolgáló virtuális gép, minden a következő lépéseket. Egyéb esetben folytassa a 3. lépés.

1. A helyszíni hely és az Azure-hálózat közötti pont-pont VPN kapcsolatot létesíteni.
2. A helyszíni Active Directory kiterjeszteni.
3. [Vész-helyreállítási](azure-to-azure-tutorial-enable-replication.md) az infrastruktúra-szolgáltatási fájl server gép másodlagos régióba.


A vészhelyreállítás másodlagos régióba további információkért lásd: [Ez a cikk](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Egy helyszíni fájlkiszolgáló replikálásához a Site Recovery segítségével

A következő lépések bemutatják a VMware virtuális gépek replikációját. A Hyper-V virtuális gépek replikálásához lépéseiért lásd: [ebben az oktatóanyagban](tutorial-hyper-v-to-azure.md).

1. [Készítse elő az Azure-erőforrások](tutorial-prepare-azure.md) replikációja, a helyszíni gépeket.
2. A helyszíni hely és az Azure-hálózat közötti pont-pont VPN kapcsolatot létesíteni. 
3. A helyszíni Active Directory kiterjeszteni.
4. [Helyszíni VMware-kiszolgálók előkészítése](tutorial-prepare-on-premises-vmware.md).
5. [Vész-helyreállítási](tutorial-vmware-to-azure.md) a helyszíni virtuális gépek Azure-bA.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Terjessze ki a DFSR Azure IaaS virtuális gépként

1. A helyszíni hely és az Azure-hálózat közötti pont-pont VPN kapcsolatot létesíteni. 
2. A helyszíni Active Directory kiterjeszteni.
3. [Hozzon létre, és helyezze üzembe a virtuális gép fájlkiszolgáló](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) az Azure virtuális hálózaton.
Győződjön meg arról, hogy a virtuális gép legyen adva az azonos Azure virtuális hálózat, amelynek kereszt-kapcsolatot a helyszíni környezetben. 
4. Telepítse és [konfigurálja a DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) Windows Server rendszeren.
5. [Valósítja meg az elosztott Fájlrendszerbeli névtér](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Az elosztott Fájlrendszerbeli névterek megvalósítva, a vész-helyreállítási helyeken nem éles környezetben megosztott mappák feladatainak átvétele végezhető el a DFS-névtér mappa célok frissítése. Után ezek az elosztott Fájlrendszerbeli névtér replikálja a módosításokat az Active Directory felhasználók transzparens módon csatlakoznak a megfelelő mappát célokat.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Fájlszinkronizálás használja replikálja a helyi fájlok
Fájlszinkronizálás segítségével fájlok replikálódnak a felhőbe. Egy olyan vészhelyzet esetén, és a helyi fájlkiszolgáló elérhetetlensége esetén csatlakoztassa a kívánt fájlhelyek a felhőből, és továbbra is a szolgáltatáskérések az ügyfélszámítógépre.
Integrálható a fájlszinkronizálás Site Recovery szolgáltatással:

* A Site Recovery-védelemmel a fájlt server gépek. Kövesse a [ebben az oktatóanyagban](tutorial-vmware-to-azure.md).
* A fájlszinkronizálás használja replikálja a fájlokat a számítógépről, amely a felhőbe fájlkiszolgálóként szolgál.
* A helyreállítás a helyreállítási terv funkció használatával adhatja hozzá a parancsfájlok a fájlkiszolgálón átvevő Azure-ban Azure fájlmegosztás csatlakoztatásához.

Kövesse az alábbi lépéseket fájl Sync szolgáltatás használatával:

1. [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha úgy dönt, hogy írásvédett georedundáns tárolás (ajánlott) a storage-fiókok, rendelkezik olvasási jogosultsággal az adatok katasztrófa esetén egy másodlagos régióban. További információkért lásd: [az Azure file megosztás vész-helyreállítási stratégiák](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Központi telepítése a fájlszinkronizálás](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) a helyi fájlkiszolgálón.
4. Hozzon létre egy szinkronizálási csoportot. A szinkronizálási csoporton belüli végpontok tartják szinkronban vannak egymással. A szinkronizálási csoport tartalmaznia kell legalább egy felhő végpontot, amely jelöli az Azure fájlmegosztások. A szinkronizálás csoport is tartalmaznia kell egy kiszolgáló végpont, amely a helyszíni Windows Server egy elérési utat képvisel.
5. A fájlok vannak most szinkronban a Azure fájlmegosztás és a helyszíni kiszolgáló között.
6. Legyen katasztrófahelyzet esetén a helyszíni környezetben, végezzen el egy feladatátvételt a használatával egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Vegye fel a parancsfájlt, amellyel az Azure fájlmegosztások csatlakoztathatja és érheti el a megosztás a virtuális gépen.

> [!NOTE]
> Győződjön meg arról, hogy a 445-ös port nyitva-e. Az Azure Files az SMB protokollt használja. SMB 445-ös TCP-porton keresztül kommunikál. Ellenőrizze, hogy ha a tűzfal nem blokkolja-e ügyfélgépről 445-ös TCP-portot.


## <a name="do-a-test-failover"></a>Végezzen feladatátvételi tesztet

1. Nyissa meg az Azure portálra, és válassza ki a helyreállítási szolgáltatás tárolójából.
2. Válassza ki a helyreállítási terv létrehozása a fájl kiszolgálói környezet.
3. Válassza ki **feladatátvételi teszt**.
4. Válassza ki a helyreállítási pont és az Azure virtuális hálózat a teszt feladatátvételi megkezdéséhez.
5. A másodlagos környezet végeztével hajtsa végre az érvényesítést.
6. A ellenőrzések után válassza ki a **karbantartása a feladatátvételi teszt** megtisztítását a helyreállítási tervben és a feladatátvételi tesztet.

További információ a feladatátvételi teszt végrehajtásához: [Site Recovery feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

A teszt feladatátvétel az Active Directory és a DNS-útmutatóért lásd: [feladatátvételi szempontokat részletező cikket az Active Directory és a DNS-](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Feladatátvétellel

1. Nyissa meg az Azure portálra, és válassza ki a Recovery Services-tároló.
2. Válassza ki a helyreállítási terv létrehozása a fájl kiszolgálói környezet.
3. Válassza ki **feladatátvételi**.
4. Válassza ki a helyreállítási pontot, a feladatátvételi folyamat elindításához.

Végezzen el egy feladatátvételt kapcsolatos további információkért lásd: [a Site Recovery feladatátvételi](site-recovery-failover.md).
