---
title: "Egy fájlkiszolgáló védelméhez az Azure Site Recovery használatával"
description: "Ez a cikk ismerteti, hogyan védheti meg egy fájlkiszolgálón, Azure Site Recovery segítségével"
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: cc585d6add9b9c5ce7f3379aeaf5ec79f5c61d51
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Egy fájlkiszolgáló védelméhez az Azure Site Recovery használatával 

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a tervezett és nem tervezett leállások során rendelkezésre álló üzleti alkalmazások tartja hozzájárul az üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási stratégiát. A Site Recovery koordinálja a vész-helyreállítási a helyi gép és az Azure virtuális gépek (VM), beleértve a replikáció, feladatátvétel és helyreállítás a különböző munkaterhelések, valamint a kezelésére.

A cikkből megtudhatja, hogyan védheti meg a fájlkiszolgáló Azure Site Recovery és más javaslatokról a különböző környezetek megfelelően.

## <a name="file-server-architecture"></a>Fájl kiszolgáló architektúrája
Egy nyitott, elosztott fájlmegosztási rendszer biztosít egy olyan környezetben, ahol földrajzilag elosztott felhasználók dolgozhatnak a fájlok integritásának követelmények veszélyeztetése nélkül. 

Egy tipikus helyszíni fájl server ökoszisztémáját, amely támogatja az egyidejű felhasználók és a tartalom elemek nagy számú Distributed File System replikációs (DFSR) replikációs ütemezési és sávszélesség-szabályozás használ. Elosztott fájlrendszer replikációs szolgáltatása segítségével hatékonyan frissítésfájlok korlátozott sávszélességű hálózaton keresztül távoli különbözeti tömörítés (RDC) tömörítési algoritmust használ. Távoli különbözeti Tömörítés észleli a beszúrások, eltávolítására és átrendezést egyaránt képes észlelni az adatok a fájlokat. Csak a módosított fájlblokkokat replikálja, ha a fájlok is frissülnek az elosztott fájlrendszer replikációs szolgáltatása lehetővé teszi. 

Néhány fájl kiszolgálói környezetekben napi biztonsági mentést készít a nem csúcsidőre időpontokban vész-helyreállítási. Ezekben a környezetekben az elosztott fájlrendszer-replikációs ne használjon.

A következő topológia egy fájl kiszolgálói környezet az elosztott fájlrendszer-replikációs megvalósított mutatja be:
                
![Elosztott fájlrendszer-replikációs architektúrája](media/site-recovery-file-server/dfsr-architecture.JPG)

Az ábrán több fájlkiszolgáló (néven tagok) aktívan részt fájlok replikálására a replikációs csoport között. A replikált mappa tartalmát a kívánt összes ügyfélszámítógép üzenetet küld kérelmeket vagy a tagok, rendelkezésére állnak, még akkor is, ha egy tagja offline állapotba kerül.

## <a name="disaster-recovery-strategies-for-file-servers"></a>Vész-helyreállítási stratégiák fájlkiszolgálók

- **Egy fájlkiszolgáló replikálása Azure-bA Azure Site Recovery segítségével**: egy vagy több fájlt a helyi kiszolgálón nem érhetők el, ha a helyreállítási virtuális gépek elérhetővé tehető az Azure-ban. A helyreállítási virtuális gépek is majd kérelmek kiszolgálását az ügyfelektől, a helyszínen, ha a pont-pont VPN-kapcsolatot, és az Active Directory úgy van beállítva, az Azure-ban. Ezt megteheti elosztott fájlrendszer-replikációs konfigurált környezetben vagy az elosztott fájlrendszer-replikációs nem egyszerű fájl kiszolgálói környezetben. 

- **Terjessze ki az Azure infrastruktúra-szolgáltatási virtuális gép elosztott fájlrendszer-replikációs**: fürtözött fájlkiszolgálók kiszolgálói környezetben az elosztott fájlrendszer-replikációs megvalósítva, egy javasolt megoldás, kibővítheti a helyszíni elosztott fájlrendszer-replikációs az Azure-bA. Egy Azure virtuális gép végezhet el a Fájlkiszolgáló szerepkör. 

    Miután a függőségeit, pont-pont VPN-kapcsolat és az Active Directory kezeli, és elosztott fájlrendszer-replikációs helyen, akkor, ha egy vagy több fájlt a helyi kiszolgálón nem érhetők el, az az ügyfelek még lehet kapcsolódni az Azure virtuális Géphez. Az Azure virtuális Gépen a kérelmek szolgálja ki.

    Ezt a módszert javasoljuk, hogy ha a virtuális gépek Azure Site Recovery nem támogató konfigurációk. Egy példa ilyen konfiguráció fájl kiszolgálói környezetekben gyakran használt megosztott fürtlemezre mutat. Elosztott fájlrendszer-replikációs közepes változási sebessége a kis sávszélességű környezetekben is működik. Ezzel a megközelítéssel kezeléséhez szükség van egy Azure virtuális gép, és meg folyamatosan futó további költségét.  

- **Azure fájlszinkronizálás használja replikálja a fájlok**: Ha a felhőbe a használatában elő, vagy egy Azure virtuális gép már használ, javasoljuk, hogy Azure fájlszinkronizálás használatát. Ezt a szolgáltatást kínál a teljes körűen felügyelt fájlmegosztások a felhőben, amelyek elérhetők a szabványos szinkronizálása [Server Message Block ](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)(SMB) protokollt. Ezek közül egyszerre felhőalapú vagy helyszíni telepítésekkel Windows, Linux és macOS csatlakoztatási Azure fájlmegosztásokat. 

A következő ábra segítségével eldöntheti, milyen stratégia fájl kiszolgálói környezetre:

![döntési fája](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-for-making-a-disaster-recovery-decision"></a>Adja meg a megfelelő egy vész-helyreállítási döntési tényező

|Környezet  |Ajánlás  |Megfontolandó szempontok |
|---------|---------|---------|
|Fájl kiszolgálói környezet vagy elosztott fájlrendszer-replikációs nélkül|   [Azure Site Recovery replikáláshoz használni](#replicate-an-on-premises-file-server-by-using-azure-site-recovery)   |    A Site Recovery nem támogatja a megosztott lemezfürtöket vagy NAS. Ha a környezet ezen konfigurációk egyike, használja az egyéb módszerek valamelyikét. <br> Az Azure Site Recovery nem támogatja az SMB 3.0-s. A replikált virtuális gép tartalmazni fogja a fájlok csak akkor, ha a változtatások a fájl eredeti helye a végzett módosítások. 
|Az elosztott fájlrendszer replikációs szolgáltatása a fájl kiszolgálói környezet     |  [Terjessze ki a DFSR Azure IaaS virtuális gépként](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      Elosztott fájlrendszer-replikációs remekül működik a sávszélesség-crunched környezetekben. Ez a módszer azonban igényli, hogy az Azure virtuális gép működik, és folyamatosan. A tervezési kell fiókot használja a virtuális gép költségét.         |
|Azure IaaS virtuális gép     |     [Az Azure File Sync szolgáltatás használatával](#use-azure-file-sync-to-replicate-your-on-premises-files)   |     A vész-helyreállítási forgatókönyv használata Azure fájlszinkronizálás feladatátvételkor érdemes manuális műveleteket, győződjön meg arról, hogy a fájlmegosztások elérhetők-e az ügyfélszámítógép átlátható módon. Azure fájlszinkronizálás is meg kell nyitni az ügyfélszámítógépen a 445-ös portra van szüksége.     |


### <a name="site-recovery-support"></a>Webhely-helyreállítási támogatás
Mivel a Site Recovery replikációs alkalmazás független, a ajánlás itt várhatóan tartsa igaz az alábbi helyzetekben:
| Forrás    |Egy másodlagos helyre    |Az Azure-bA
|---------|---------|---------|
|Azure| -|Igen|
|Hyper-V|   Igen |Igen
|VMware |Igen|   Igen
|Fizikai kiszolgáló|   Igen |Igen
 

> [!IMPORTANT]
> Mielőtt továbblép a következő módszerek bármelyikével, ne feledje el cím függőségek.

**Hely-hely kapcsolatot**: a helyszíni hely és az Azure-hálózat közvetlen kapcsolatot kell létrehozni a kiszolgálók közötti kommunikáció lehetővé tételéhez. Egy Azure virtuális hálózatra, a vész-helyreállítási helyként használandó biztonságos webhelyek VPN-kapcsolatot is használhatja. További információkért lásd: [webhelyek kapcsolat létrehozása az Azure portálon](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Az Active Directory**: elosztott fájlrendszer replikációs szolgáltatása az Active Directory függ. Ez azt jelenti, hogy a helyi tartományvezérlővel rendelkező Active Directory-erdő az időtartam a vész-helyreállítási helyet az Azure-ban. Akkor is, ha nem használ elosztott fájlrendszer replikációs szolgáltatása, ha a kívánt felhasználók számára szeretné hozzáférési nyújtott/ellenőrizni kell, végezze el ezeket a lépéseket.
További információkért lásd: [kiterjesztése a helyszíni Azure Active Directory](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendations-for-azure-iaas-virtual-machines"></a>Katasztrófa utáni helyreállítás javaslatok Azure IaaS virtuális gépekhez

Ha konfigurálása és kezelése a vész-helyreállítási fájlkiszolgálók Azure IaaS virtuális gépeket üzemeltet, választhat két lehetőség közül választhat: Azure fájlszinkronizálás és az Azure Site Recovery. A döntési, attól függ, hogy az áthelyezni kívánt [Azure fájlok](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

### <a name="use-azure-file-sync-to-replicate-files-hosted-on-iaas-virtual-machines"></a>Fájlok üzemeltetett IaaS virtuális gépek replikálása Azure fájl Sync szolgáltatás használatával

Azure-fájlok segítségével teljesen lecseréli vagy kiegészítik a hagyományos helyszíni fájlkiszolgálókon és NAS-eszközökön. Azure fájlmegosztásokat is replikálható az Azure fájlszinkronizálás, Windows Server kiszolgálókon, vagy a helyszíni vagy felhőalapú performant és az adatokat, ha használatban van az elosztott gyorsítótárazás. 

Az alábbi lépéseket a vész-helyreállítási javaslat Azure virtuális gépekhez, amely a hagyományos fájlkiszolgálóként megegyező művelet végrehajtására szolgál(nak) részletesen:
1. Azure Site Recovery szolgáltatásban a az itt leírt lépéseket-gépek védelmére.

2. Azure fájlszinkronizálás segítségével replikálni a fájlokat a virtuális gép, amely úgy működik, mint a fájlkiszolgáló, a felhőhöz.

3. Használja a [helyreállítási terv](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-create-recovery-plans) az Azure Site Recovery parancsfájlok hozzáadása a szolgáltatás [Azure fájlmegosztás csatlakoztatása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) és elérni a megosztást a virtuális gépen.

Az alábbi lépéseket az Azure fájl Sync szolgáltatás használatával röviden:

1. [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha írásvédett georedundáns tárolás a storage-fiókok, egy katasztrófa esetén másodlagos régióban olvasási hozzáférést kap az adatait. További információkért lásd: [Azure File megosztás vész-helyreállítási stratégiák](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

2. [Fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

3. [Azure fájlszinkronizálás telepítése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) Azure fájlkiszolgáló.

4. Hozzon létre egy szinkronizálási csoportot. A szinkronizálási csoporton belüli végpontok szinkronban vannak egymással marad. A szinkronizálás csoport tartalmaznia kell legalább egy felhő végpont, amely egy Azure fájlmegosztás jelöli, és egy kiszolgáló végpont, amely a Windows server egy elérési utat képvisel.  
    A fájlok most marad szinkronban a Azure fájlmegosztás és a helyszíni kiszolgáló között.

5. Ha egy olyan vészhelyzet esetén a helyszíni környezetben, végezzen el egy feladatátvételt a helyreállítási terv használatával. Vegye fel a parancsfájlt, amellyel [Azure fájlmegosztás csatlakoztatása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) és elérni a megosztást a virtuális gépen.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-azure-site-recovery"></a>Fájl server IaaS virtuális gépként replikálása az Azure Site Recovery segítségével

Ha a helyi ügyfelek férnek hozzá az infrastruktúra-szolgáltatási fájl kiszolgáló virtuális gép, hajtsa végre az alábbi lépéseket. Ellenkező esetben hajtsa végre csak 3. lépés.

1. A helyszíni hely és az Azure-hálózat közötti pont-pont VPN kapcsolatot létesíteni.  

2. A helyszíni Active Directory kiterjeszteni.

3. [Vész-helyreállítási](azure-to-azure-tutorial-enable-replication.md) az infrastruktúra-szolgáltatási fájl server gép másodlagos régióba.

A vészhelyreállítás másodlagos régióba további információkért lásd: [Azure-Azure replikációs architektúra](concepts-azure-to-azure-architecture.md).

## <a name="disaster-recovery-recommendations-for-on-premises-virtual-machines"></a>A helyszíni virtuális gépek vész helyreállítási javaslatok 

### <a name="replicate-an-on-premises-file-server-by-using-azure-site-recovery"></a>Egy helyszíni fájlkiszolgáló replikálása az Azure Site Recovery segítségével
Az alábbi lépéseket a VMware virtuális gép replikációs adatok találhatók. A Hyper-V virtuális gépek replikálásához lépéseiért lásd: [beállítása az Azure-bA helyszíni Hyper-V virtuális gépek vész-helyreállítási](https://docs.microsoft.com/en-us/azure/site-recovery/tutorial-hyper-v-to-azure).

1. [Készítse elő az Azure-erőforrások](tutorial-prepare-azure.md) replikációja, a helyszíni gépeket.

2. A helyszíni hely és az Azure-hálózat közötti pont-pont VPN kapcsolatot létesíteni.  

3. A helyszíni Active Directory kiterjeszteni.

4. [Helyszíni VMware-kiszolgálók előkészítése](tutorial-prepare-on-premises-vmware.md).

5. [Vész-helyreállítási](tutorial-vmware-to-azure.md) a helyszíni virtuális gépek Azure-bA.

### <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Terjessze ki a DFSR Azure IaaS virtuális gépként

1. A helyszíni hely és az Azure-hálózat közötti pont-pont VPN kapcsolatot létesíteni. 

2. A helyszíni Active Directory kiterjeszteni.

3. [Hozzon létre, és helyezze üzembe a virtuális gép fájlkiszolgáló](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) az Azure virtuális hálózaton.  
    Győződjön meg arról, hogy a virtuális gép legyen adva az azonos Azure virtuális hálózat, amelynek kereszt-kapcsolatot a helyszíni környezetben. 

4. Telepítse és [konfigurálhatja az Elosztott fájlrendszer replikációs szolgáltatása](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) a Windows Server.

5. [Valósítja meg az elosztott Fájlrendszerbeli névtér](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).

6. Az elosztott Fájlrendszerbeli névterek megvalósítva, a feladatokat átveheti vész-helyreállítási helyeken nem éles környezetben megosztott mappák a DFS-névtér mappa célok frissítésével. Után ezek az elosztott Fájlrendszerbeli névtér replikálja a módosításokat az Active Directory felhasználók transzparens módon csatlakoznak a megfelelő mappát célokat.

### <a name="use-azure-file-sync-to-replicate-your-on-premises-files"></a>A helyi fájlok replikálása Azure fájl Sync szolgáltatás használatával
Az Azure fájl Sync szolgáltatás segítségével replikálhatja a kívánt fájlokat a felhőbe. Egy katasztrófa, és a helyi fájlkiszolgáló elérhetetlensége esetén csatlakoztassa a felhőből az kívánt helyét, majd a szolgáltatáskérések az ügyfél gépek továbbra is.

Az Azure fájlszinkronizálás integrálható az Azure Site Recovery javasolt módszer van:
1. Azure Site Recovery-védelemmel a fájlt server gépek. Kövesse a [vész-helyreállítási Azure beállítása a helyszíni VMware virtuális gépek](tutorial-vmware-to-azure.md).

2. Azure fájlszinkronizálás használja replikálja a fájlokat a számítógépről, amely a fájlkiszolgáló, a felhő funkcionál.

3. Az Azure Site Recovery a helyreállítási terv funkció használatával adhatja hozzá a parancsfájlok a fájlkiszolgálón átvevő Azure-ban Azure fájlmegosztás csatlakoztatásához.

A következő lépések részletes az Azure fájl Sync szolgáltatás használatával:

1. [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha úgy dönt, hogy írásvédett georedundáns tárolás (ajánlott) a storage-fiókok, egy katasztrófa esetén másodlagos régióban olvasási hozzáférés lesz az adatait. További információkért lásd: [Azure File megosztás vész-helyreállítási stratégiák](https://docs.microsoft.com/en-us/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

2. [Fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

3. [Azure fájlszinkronizálás telepítése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) a helyi fájlkiszolgálón.

4. Hozzon létre egy szinkronizálási csoportot. A szinkronizálási csoporton belüli végpontok szinkronban vannak egymással marad. A szinkronizálás csoport tartalmaznia kell legalább egy felhő végpontot, amely jelöli az Azure fájlmegosztások, és egy kiszolgáló végpont, amely a helyszíni Windows Server egy elérési utat képvisel.  
    A fájlok most marad szinkronban a Azure fájlmegosztás és a helyszíni kiszolgáló között.

5. Ha egy olyan vészhelyzet esetén a helyszíni környezetben, végezzen el egy feladatátvételt a helyreállítási terv használatával. Vegye fel a parancsfájlt, amellyel az Azure fájlmegosztások csatlakoztathatja és érheti el a megosztás a virtuális gépen.

> [!NOTE]
> Győződjön meg arról, hogy a 445-ös port nyitva-e. Az Azure Files az SMB protokollt használja. SMB 445-ös TCP-porton keresztül kommunikál. Ellenőrizze, hogy ha a tűzfal blokkolja az ügyfélszámítógépen a 445-ös portot.


## <a name="perform-a-test-failover"></a>Feladatátvételi teszt végrehajtása

1. Nyissa meg az Azure-portálon, és válassza ki a Recovery Services-tároló.

2. Válassza ki a helyreállítási terv létrehozása a fájl kiszolgálói környezet.

3. Válassza ki **feladatátvételi teszt**.

4. Válassza ki a helyreállítási pont és a teszt feladatátvételi megkezdéséhez Azure virtuális hálózat.

5. A másodlagos környezet esetén hajtsa végre az érvényesítést.

6. Amikor ellenőrzés be nem fejeződik, választhat **karbantartása a feladatátvételi teszt** megtisztítását a helyreállítási tervben és a feladatátvételi tesztet.

Feladatátvételi teszt elvégzésével kapcsolatos további információkért lásd: [tesztelése az Azure Site Recovery a](site-recovery-test-failover-to-azure.md).

Az Active Directory és a DNS a feladatátvételi teszt végrehajtásához útmutatóért lásd: [feladatátvételi szempontokat részletező cikket az Active Directory és a DNS-](site-recovery-active-directory.md).

## <a name="perform-a-failover"></a>Végezzen el egy feladatátvételt

1. Nyissa meg az Azure-portálon, és válassza ki a Recovery Services-tároló.

2. Válassza ki a helyreállítási terv létrehozása a fájl kiszolgálói környezet.

3. Válassza ki **feladatátvételi**.

4. Válassza ki a helyreállítási pontot, a feladatátvételi folyamat elindításához.

A feladatátvétel végrehajtásához kapcsolatos további információkért lásd: [a Site Recovery feladatátvételi](site-recovery-failover.md).
