---
title: "Egy Azure Site Recovery segítségével fájlkiszolgáló védelme"
description: "Ez a cikk ismerteti, hogyan védi az Azure Site Recovery segítségével fájlkiszolgáló"
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
ms.openlocfilehash: 779ec70a3b45a0ac3e766c956aac94932d4d126b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>Egy Azure Site Recovery segítségével fájlkiszolgáló védelme 

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás által az üzleti alkalmazások, és az elérhető futtató tervezett és nem tervezett leállások során hozzájárul az üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási stratégiát. A Site Recovery koordinálja a vész-helyreállítási a helyi gép és az Azure virtuális gépek (VM), beleértve a replikáció, feladatátvétel és helyreállítás a különböző munkaterhelések, valamint a kezelésére.

A cikkből megtudhatja, hogyan védi az Azure Site Recovery és egyéb javaslatok használatával különböző környezetekben megfelelően fájlkiszolgáló.     

- [Azure IaaS fájl server gépek replikálása](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Azure Site Recovery segítségével a helyi fájlkiszolgáló replikálása](#replicate-an-onpremises-file-server-using-azure-site-recovery)

## <a name="file-server-architecture"></a>Fájl kiszolgáló architektúrája
A rendszer megosztása nyitott elosztott fájlok célja, hogy adjon meg egy olyan környezetben, ahol a földrajzilag elosztott felhasználók egy csoportjánál együttműködhetnek hatékonyan fájlok, és biztosítani kell, hogy integritási igényeik lépnek érvénybe. Egy tipikus helyszíni fájlkiszolgáló ökoszisztéma nagyszámú egyidejű felhasználót és nagyszámú tartalomelem támogató replikációs ütemezési és sávszélesség-szabályozás Distributed File System replikációs (DFSR) használata. Elosztott fájlrendszer replikációs szolgáltatása, távoli különbözeti tömörítés (RDC), hatékony korlátozott sávszélességű hálózaton keresztül a fájlok frissítéséhez használható ismert tömörítési algoritmust alkalmazza. Észlel Beszúrások, eltávolítására és átrendezést egyaránt képes észlelni az adatok a, fájljaiban engedélyezése csak a módosított fájlblokkokat replikálja, ha a fájlok is frissülnek az elosztott fájlrendszer replikációs szolgáltatása. Fájlkiszolgáló környezetekben, ahol napi biztonsági mentés készül a nem csúcsidőre időzítést, amely automatikusan igazodnak a vész-igényekre és az elosztott fájlrendszer replikációs szolgáltatása nincs implementációja is vannak.

A következő topológia a fájlkiszolgáló-környezet az elosztott fájlrendszer-replikációs megvalósított mutatja be.
                
![dfsr-architexture](media/site-recovery-file-server/dfsr-architecture.JPG)

A fenti hivatkozás több fájlkiszolgáló említett tagként, aktívan részt vesz a fájlok egy replikációs csoport replikálása. A replikált mappa tartalmát küldött kérésekkel vagy a tagok, még offline állapotra vált, tag esetén minden ügyfél számára elérhető lesz.

## <a name="disaster-recovery-recommendation-for-file-servers"></a>Vész-helyreállítási javaslat fájlkiszolgálók:

1.  Egy Azure Site Recovery segítségével fájlkiszolgáló replikálni: az Azure Site Recovery segítségével Azure-bA replikálhatja a fájlkiszolgálók. Egy vagy több fájl kiszolgálókat a helyi nem érhető el, ha a helyreállítási virtuális gépek az Azure-ban is elérhetővé tehető, majd szolgálnak kéri le az ügyfelek, a helyszíni, feltéve, hogy a telephelyek közötti VPN-kapcsolat és az Active directory, az Azure-ban beállított. Ezt megteheti a konfigurálva a DFSR-környezet vagy egy egyszerű fájl kiszolgálói környezet nem elosztott fájlrendszer replikációs szolgáltatása esetén. 

2.  Elosztott fájlrendszer-replikációs kiterjeszteni egy Azure Iaas virtuális Gépen:-fürtözött fájlkiszolgálók kiszolgálói környezetben az elosztott fájlrendszer-replikációs megvalósítva, egy javasolt megoldás, kibővítheti a helyszíni elosztott fájlrendszer-replikációs az Azure-bA. Egy Azure virtuális gép majd engedélyezve van a Fájlkiszolgáló szerepkör végrehajtásához. 

    Miután telephelyek közötti VPN-kapcsolat és az Active directory függőségeit kezeli, és elosztott fájlrendszer replikációs szolgáltatása rendelkezésre áll, amikor egy vagy több fájl kiszolgálókat a helyi nem érhető el, akkor az ügyfelek továbbra is az Azure virtuális Gépen, amely teljesíti a kérelmeket a csatlakozhat.

    Ez a megközelítés a javaslat esetében a virtuális gépek rendelkezik konfigurációk nem támogatottak az Azure Site Recovery, mint például: megosztott fürtlemezre mutat, egyes esetekben gyakori használati módszereinek fájlkiszolgáló környezetekben.  Elosztott fájlrendszer-replikációs közepes változási sebessége a kis sávszélességű környezetekben is működik. Rendelkezik egy Azure virtuális gép, és folyamatosan fut a további költségeket is elhelyezkedhetnek, ennek kell.  

3.  Azure fájl Sync szolgáltatás segítségével replikálni a fájlokat: Ha elő a felhőbe a használatában, vagy egy Azure virtuális gép már használ, akkor javasoljuk, hogy a teljes körűen felügyelt fájlmegosztások a felhőben, amelyek elérhető v szinkronizálása kínál, amelyek az Azure File szinkronizálási szolgáltatás használata IA iparági szabvány [Server Message Block ](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)(SMB) protokollt. Megosztások egyidejűleg, majd lehet csatlakoztatni az Azure File felhőalapú vagy helyszíni a Windows, Linux és macOS központi telepítéséhez. 

Következő diagramon ad meg a döntést a fájl kiszolgálói környezet esetén használandó milyen stratégia enyhítése célzó a képi megjelenítése.

![decisiontree](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-decision-of-disaster-recovery-to-azure"></a>A vész-helyreállítási Azure döntési tétele közben megfontolandó tényezőt

|Környezet  |Ajánlás  |Megfontolandó szempontok |
|---------|---------|---------|
|A fájl/elosztott fájlrendszer replikációs szolgáltatása nem kiszolgálói környezet|   [Azure Site Recovery replikáláshoz használni](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    A Site Recovery nem támogatja a megosztott lemezfürt esetén NAS. Ha a környezet bármelyik ezeket a konfigurációkat, használja az egyéb módszerek valamelyikét. <br> Az Azure Site Recovery nem támogatja az SMB 3.0-s, ami azt jelenti, hogy csak akkor, ha a fájlok módosításainak frissíti benne a fájl eredeti helye lesz a replikált virtuális gép változtatásokat.
|Az elosztott fájlrendszer replikációs szolgáltatása a fájl kiszolgálói környezet     |  [Elosztott fájlrendszer-replikációs Azure IaaS virtuális gépként terjed ki:](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     Elosztott fájlrendszer-replikációs remekül működik rendkívül crunched sávszélesség környezetekben, ezt a módszert azonban szüksége van egy Azure virtuális gép mentése és bekapcsolva. A VM költsége kell a tervezés veendő.         |
|Azure Iaas virtuális gép     |     [Azure File Sync ](#use-azure-file-sync-service-to-replicate-your-files)   |     A vész-Helyreállítási forgatókönyvek használata Azure fájlszinkronizálás, a feladatátvétel során manuális műveletek kell tenni annak érdekében, hogy a fájlmegosztások, az ügyfélszámítógép átlátható módon érhető el. AFS is meg kell nyitni az ügyfélszámítógépen a 445-ös portra van szüksége.     |


### <a name="site-recovery-support"></a>Webhely-helyreállítási támogatás
A Site Recovery replikációs alkalmazás független, a ajánlás itt várhatóan tartsa igaz az alábbi helyzetekben:
| Forrás    |Egy másodlagos helyre    |To Azure
|---------|---------|---------|
|Azure| -|Igen|
|Hyper-V|   Igen |Igen
|VMware |Igen|   Igen
|Fizikai kiszolgáló|   Igen |Igen
 

> [!IMPORTANT]
> Az összes, a folytatás előtt az alábbi három módszer, gondoskodjon arról, hogy a következő függőségek vannak végrehajtott megvagyunk:

**Hely-hely kapcsolatot**: közvetlen kapcsolatot a helyszíni hely és az Azure-hálózatot kell létrehozni a kiszolgálók közötti kommunikáció lehetővé tételéhez.  Ez úgy biztosítható egy biztonságos telephelyek közötti VPN-kapcsolat egy Microsoft Azure virtuális hálózatra a vész-Helyreállítási helyként használandó.  
Lásd: [jöjjön létre pont-pont VPN-kapcsolat a helyszíni hely és az Azure-hálózat között](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

**Az Active Directory**: elosztott fájlrendszer replikációs szolgáltatása az Active Directory függ.  Ez azt jelenti, hogy a helyi tartományvezérlővel rendelkező Active Directory-erdő az időtartam a vész-Helyreállítási hely, az Azure-ban. Akkor is, ha az elosztott fájlrendszer-replikációs nem használnak, ha a kívánt felhasználók számára nyújtott / meg például a legtöbb szervezet hozzáférés ellenőrzése kell, a lépések végrehajtása szükséges.
Lásd: [terjessze ki a helyi Active Directory Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Katasztrófa utáni helyreállítás javaslat Azure IaaS virtuális gépekhez

Konfigurál, és vészhelyreállítás Azure IaaS virtuális gépeket üzemeltet a fájlkiszolgálók kezelése, választhat két lehetőség közül választhat, ha alapján, hogy kívánja-e áthelyezése [Azure fájlok](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

1. [Az Azure File Sync szolgáltatás használatával](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [Az Azure Site Recovery használata](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>IaaS virtuális gépen tárolt fájlok replikálása Azure fájl Sync szolgáltatás segítségével

**Az Azure Files** teljesen lecseréli vagy kiegészítik a hagyományos helyszíni fájlkiszolgálókon és NAS-eszközökön használható. Az Azure-fájlmegosztások az Azure File Sync használatával replikálhatóak helyszíni vagy felhőalapú Windows Serverekre, az adatok a használat helyéhez közeli nagy teljesítményű és elosztott gyorsítótárazása érdekében. Következő lépések a vész-Helyreállítási javaslat Azure virtuális gépek hagyományos fájlkiszolgálók azonos funkciókat ellátó részletesen:
1.  Azure Site Recovery segítségével leírt lépéseket segítségével gépek védelmére [Itt](azure-to-azure-quickstart.md).
2.  Azure fájlszinkronizálás segítségével replikálni a fájlokat a virtuális gép, amely úgy működik, mint a fájlkiszolgáló, a felhőhöz.
3.  Azure Site Recovery használata [helyreállítási terv](site-recovery-create-recovery-plans.md) parancsfájlok hozzáadása a szolgáltatás [Azure fájlmegosztás csatlakoztatása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) és elérni a megosztást a virtuális gépen.

A következő lépések röviden használatát ismertetik Azure fájl szinkronizálási szolgáltatás:

1. [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha úgy dönt, írásvédett Georedundáns tárolás (RA-GRS) a storage-fiókok, kap olvasási hozzáféréssel az adatok egy katasztrófa esetén a másodlagos régióba. Tekintse meg a [Azure File megosztás vész-helyreállítási stratégiák](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) további információért.
2. [Fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Azure fájlszinkronizálás telepítése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) Azure fájlkiszolgáló.
4. Hozzon létre egy szinkronizálási csoportot: a szinkronizálási csoporton belüli végpontok folyamatosan szinkronban vannak egymással. A szinkronizálás csoport tartalmaznia kell a felhő legalább egy végpontot, amely egy Azure fájlmegosztás jelöli, és egy kiszolgáló végpont, amely a Windows Server egy elérési utat képvisel.
5.  A fájlok most folyamatosan szinkronban a Azure fájlmegosztás és a helyszíni kiszolgáló között.
6.  Legyen katasztrófahelyzet esetén a helyszíni környezetben, hajtsa végre, mint a feladatátvétel használatával egy [helyreállítási terv](site-recovery-create-recovery-plans.md) , és adja hozzá a parancsfájlt, amellyel [Azure fájlmegosztás csatlakoztatása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) és elérni a megosztást a virtuális gépen.

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>Az infrastruktúra-szolgáltatási fájl kiszolgáló virtuális gép Azure Site Recovery segítségével replikálni.

Ha a helyszíni ügyfél fér hozzá az infrastruktúra-szolgáltatási fájl kiszolgáló virtuális gép hajtsa végre az első két lépés, valamint más folytassa a 3. lépés.

1. Helyszíni hely és az Azure-hálózat között telephelyek közötti VPN-kapcsolat létrehozásához.
1. A helyszíni Active Directory kiterjeszteni.
1. [Vész-helyreállítási](azure-to-azure-tutorial-enable-replication.md) az infrastruktúra-szolgáltatási fájl server gép másodlagos régióba.


A vészhelyreállítás másodlagos régióba további információkért tekintse meg a [Itt](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>Egy Azure Site Recovery segítségével a helyi fájlkiszolgáló replikálása

Az alábbi lépések részletes replikációs a VMware virtuális gépek replikálásához a Hyper-V virtuális lépéseket lásd [Itt](tutorial-hyper-v-to-azure.md).

1.  [Készítse elő az Azure-erőforrások](tutorial-prepare-azure.md) replikációja, a helyszíni gépeket.
2.  Helyszíni hely és az Azure-hálózat között telephelyek közötti VPN-kapcsolat létrehozásához.  
3. A helyszíni Active Directory kiterjeszteni.
4.  [Helyszíni VMware-kiszolgálók előkészítése](tutorial-prepare-on-premises-vmware.md).
5.  [Vész-helyreállítási](tutorial-vmware-to-azure.md) a helyszíni virtuális gépek Azure-bA.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Elosztott fájlrendszer-replikációs Azure IaaS virtuális gépként terjed ki:

1.  Helyszíni hely és az Azure-hálózat között telephelyek közötti VPN-kapcsolat létrehozásához. 
2.  A helyszíni Active Directory kiterjeszteni.
3.  [Hozzon létre, és helyezze üzembe a virtuális gép fájlkiszolgáló](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) a Windows Azure virtuális hálózaton.
Győződjön meg arról, hogy a virtuális gép legyen adva a ugyanazon Windows Azure virtuális hálózaton, ami az alhálózatok közötti csatlakozik a helyszíni környezetben. 
4.  Telepítse és [konfigurálhatja az Elosztott fájlrendszer replikációs szolgáltatása](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) a Windows Server.
5.  [Valósítja meg az elosztott fájlrendszer-Namespace](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6.  Az elosztott Fájlrendszerbeli Namespace megvalósítva, a vész-Helyreállítási helyeken nem éles környezetben megosztott mappák feladatainak átvétele végezhető el az elosztott Fájlrendszerbeli Namespace mappa célok frissítése.  Az elosztott Fájlrendszerbeli Namespace módosítások replikálja az Active Directory segítségével, ha felhasználók kapcsolódnak a megfelelő mappát célokat transzparens módon.

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>Azure fájl Sync szolgáltatás segítségével replikálni a helyi fájlokat:
Az Azure fájlszinkronizálás szolgáltatással replikálhatja a kívánt fájlokat a felhőbe, hogy egy katasztrófa, és a helyi fájlkiszolgáló elérhetetlensége esetén csatlakoztassa a kívánt fájlhelyek a felhőből történő be- és továbbra is a szolgáltatáskérések az ügyfél gépek.
Az ajánlott módszer az Azure fájlszinkronizálás integrálása az Azure Site Recovery
1.  A fájl server gépek említett lépéseket követve Azure Site Recovery segítségével védelmére [Itt](tutorial-vmware-to-azure.md).
2.  Azure fájlszinkronizálás használja replikálja a fájlokat a számítógépről, amely a fájlkiszolgáló, a felhő funkcionál.
3.  Azure Site Recovery helyreállítási terv funkció használatával adhatja hozzá a parancsfájlok Azure fájlmegosztás csatlakoztatása a feladatait a fájlkiszolgáló gép az Azure-ban.

Az alábbi lépések részletes Azure fájl Sync szolgáltatás használatával:

1. [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha úgy dönt, hogy írásvédett georedundáns tárolás (RA-GRS) (ajánlott) a storage-fiókok, rendelkezik olvasási jogosultsággal az adatok katasztrófa esetén egy másodlagos régióban. Tekintse meg a [Azure File megosztás vész-helyreállítási stratégiák](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) további információért.
2. [Fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Azure fájlszinkronizálás telepítése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) a helyi fájlkiszolgálón.
4. Hozzon létre egy szinkronizálási csoportot: a szinkronizálási csoporton belüli végpontok folyamatosan szinkronban vannak egymással. A szinkronizálás csoport tartalmaznia kell a felhő legalább egy végpontot, amely egy Azure fájlmegosztás jelöli, és egy kiszolgáló végpont, amely a helyszíni Windows Server egy elérési utat képvisel.
1. A fájlok most folyamatosan szinkronban a Azure fájlmegosztás és a helyszíni kiszolgáló között.
6.  Legyen katasztrófahelyzet esetén a helyszíni környezetben, hajtsa végre, mint a feladatátvétel használatával egy [helyreállítási terv](site-recovery-create-recovery-plans.md) , és adja hozzá a parancsfájlt, amellyel az Azure fájlmegosztást csatlakoztathatja és érheti el a megosztás a virtuális gépen.

> [!NOTE]
> Győződjön meg arról, 445-ös port meg nyitva: Azure fájlok SMB protokollt használja. Az SMB a 445-ös TCP-porton keresztül kommunikál – ellenőrizze, hogy a tűzfal nem blokkolja-e a 445-ös TCP-portot az ügyfél gépéről.


## <a name="doing-a-test-failover"></a>A teszt feladatátvétel

1.  Nyissa meg az Azure-portálhoz, és válassza a helyreállítási szolgáltatás tárolójából.
2.  Kattintson a helyreállítási terv létrehozása a fájlkiszolgáló környezet.
3.  Kattintson a "Test Failover".
4.  Válassza ki a helyreállítási pont és a teszt feladatátvételi megkezdéséhez Azure virtuális hálózat.
5.  A másodlagos környezetben működik, ha az érvényesítést végezheti el.
6.  Ha az ellenőrzés befejeződött, kattintson a helyreállítási terv "Karbantartása a feladatátvételi teszt", és a teszt feladatátvételi környezet karbantartása.

További információ a feladatátvételi teszt végrehajtása, [Itt](site-recovery-test-failover-to-azure.md).

A teszt feladatátvétel ad útmutatást, majd tekintse át a DNS, [feladatátvételi szempontokat részletező cikket az Active Directory és a DNS-](site-recovery-active-directory.md).

## <a name="doing-a-failover"></a>A feladatátvétel

1.  Nyissa meg az Azure-portálhoz, és válassza a Recovery Services-tároló.
2.  Kattintson a helyreállítási terv létrehozása a fájlkiszolgáló környezet.
3.  Kattintson a "Failover".
4.  Válassza ki a helyreállítási pontot a feladatátvételi folyamat elindításához.

A feladatátvételt hajt végre további információkért tekintse meg a [Itt](site-recovery-failover.md).
