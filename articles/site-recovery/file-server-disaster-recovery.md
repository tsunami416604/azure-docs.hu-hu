---
title: Egy fájlkiszolgáló védelme az Azure Site Recovery használatával
description: Ez a cikk bemutatja, hogyan védheti meg az Azure Site Recovery használatával egy fájlkiszolgáló
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 0b6d5dccbce30c55e259e4bb3f8ae4194a02b646
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916883"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Egy fájlkiszolgáló védelme az Azure Site Recovery használatával 

Az [Azure Site Recovery](site-recovery-overview.md) működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. Site Recovery felügyeli és koordinálja a helyszíni gépek és Azure-beli virtuális gépek (VM). Vész-helyreállítási magában foglalja a replikáció, feladatátvétel és helyreállítás különböző számítási feladatok.

Ez a cikk ismerteti, hogyan lehet egy fájlkiszolgáló védeni a Site Recovery használatával, és igény szerint a különböző környezetek egyéb javaslatokat tesz. 

- [Kiszolgáló gépek replikálása az Azure IaaS fájl](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Egy helyszíni fájlkiszolgáló replikálni a Site Recovery használatával](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Fájl kiszolgáló architektúrája
Nyissa meg az elosztott fájlmegosztási rendszer célja, hogy adjon meg egy környezetben, ahol együttműködhet a földrajzilag elosztott felhasználók egy csoportjára hatékonyan használhatják a fájlokat, és biztosítani kell, hogy a kódintegritási követelmények érvényben vannak. Egy tipikus helyszíni file server-környezettel, amely támogatja a nagyszámú egyidejű felhasználót és a tartalmak nagy számú Distributed File System replikációs (DFSR) replikációs ütemezési és sávszélesség-szabályozás használ. 

Elosztott fájlrendszer-replikációs, távoli különbözeti tömörítés (RDC), amely hatékonyan korlátozott sávszélességű hálózaton keresztül a fájlok frissítéséhez használható ismert tömörítési algoritmust alkalmazza. Fájlok a beszúrások, eltávolításokkal és átrendezést egyaránt képes észlelni az adatok észlel. Elosztott fájlrendszer-replikációs engedélyezve van, csak a módosított fájlblokkokat replikálja a fájlok frissítésekor. Is találhatók kiszolgálói környezetekben, ahol nem csúcsidőre időzítésüket, amely vészhelyreállítási igényeinek megfelelően biztosíthat a napi biztonsági mentések megnyílik. Elosztott fájlrendszer replikációs szolgáltatása nincs megvalósítva.

A következő ábra szemlélteti a fájl-kiszolgálói környezet az elosztott fájlrendszer-replikációs implementálva.
                
![Elosztott fájlrendszer-replikációs architektúra](media/site-recovery-file-server/dfsr-architecture.JPG)

Az előző ábrán a tagok aktívan nevű több fájlkiszolgáló részt fájlok replikálására a replikációs csoport között. A replikált mappa tartalmát az ügyfelek által küldött kérések vagy a tagok rendelkezésére állnak, még akkor is, ha tagja offline állapotba kerül.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>A fájlkiszolgálók vész helyreállítási javaslatok

* **Egy fájlkiszolgáló replikálni a Site Recovery használatával**: fájlkiszolgálók replikálhatók az Azure Site Recovery használatával. Ha egy vagy több helyszíni fájlkiszolgálók nem érhető el, a helyreállítási virtuális gépek elérhetővé tehető az Azure-ban. A virtuális gépek majd is képes kiszolgálni kéréseket az ügyfelektől érkező, a helyszínen, feltéve, hogy helyek közötti VPN-kapcsolat és az Active Directory úgy van beállítva az Azure-ban. Ez a módszer egy elosztott fájlrendszer-replikációs konfigurált környezet és a egy egyszerű fájlt kiszolgálói környezet esetén az elosztott fájlrendszer-replikációs nem is használhatja. 

* **Elosztott fájlrendszer-replikációs kiterjesztése az Azure IaaS virtuális gépekhez**: fürtözött fájlkiszolgáló kiszolgálói környezetben az elosztott fájlrendszer-replikációs megvalósítva, kiterjesztheti a helyszíni elosztott fájlrendszer replikációs szolgáltatása az Azure-bA. Egy Azure virtuális Gépen hajtsa végre a fájlkiszolgáló szerepkört, majd engedélyezve van. 

    * Site-to-site VPN-kapcsolat és az Active Directory, a függőségek kezelése és az elosztott fájlrendszer-replikációs helyen, akkor, ha egy vagy több helyszíni fájlkiszolgálók nem érhető el, miután ügyfelek csatlakozhatnak az Azure virtuális Gépen, amely a kérések szolgál.

    * Ez a megközelítés is használhatja, ha a virtuális gépek Site Recovery által nem támogatott konfigurációkat. Ilyen például, egy megosztott fürtlemezre, néha gyakori használati módszereinek fájl kiszolgálói környezetekben. Elosztott fájlrendszer-replikációs is jól közepes lemorzsolódási rátához alacsony sávszélességű környezetben működik. Fontolja meg a további költségeket kellene egy Azure virtuális gép, és folyamatosan futó kell. 

* **Az Azure File Sync használatával replikálni a fájlok**: Ha azt tervezi, a felhő segítségével, vagy már használja egy Azure virtuális Gépen, használhatja az Azure File Sync. Az Azure File Sync kínál a teljes körűen felügyelt fájlmegosztások a felhőben, amely az iparági szabvány-en keresztül elérhető szinkronizálása [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (SMB) protokollt. Azure-fájlmegosztások tudja majd üzemelésenként csatlakoztathatja a Windows, Linux és macOS felhőbeli vagy helyszíni üzemelő példányok. 

Az alábbi ábra segítségével meghatározhatja, hogy milyen stratégiát a fájl kiszolgálói környezetben használandó.

![Döntési fa](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Az Azure-bA vész-helyreállítási kapcsolatos döntéseket hozta a mérlegelendő

|Környezet  |Ajánlás  |Megfontolandó szempontok |
|---------|---------|---------|
|Fájl kiszolgálói környezet vagy elosztott fájlrendszer-replikációs nélkül|   [A Site Recovery-replikációhoz](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    A Site Recovery nem támogatja a megosztott lemezfürtöket vagy hálózati tárolóeszközök (NAS). Ha a környezet ezeket a beállításokat használja, használja az egyéb módszerek bármelyikét. <br> A Site Recovery nem támogatja az SMB 3.0-s. A replikált virtuális gép csak akkor, ha a fájlokon végrehajtott módosítások frissülnek a fájlok az eredeti helyre változtatásokat foglalja magában.
|Az elosztott fájlrendszer-replikációs fájl kiszolgálói környezet     |  [Elosztott fájlrendszer-replikációs kiterjesztése az Azure IaaS virtuális gépeken](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      Elosztott fájlrendszer-replikációs sávszélesség crunched rendkívül környezetben működik. Ez a módszer egy Azure virtuális gép működik, és folyamatosan futó igényli. A virtuális gép költsége figyelembe a tervezés kell.         |
|Azure IaaS virtuális Gépeken     |     [File Sync ](#use-azure-file-sync-service-to-replicate-your-files)   |     A vész-helyreállítási helyzetekre File Sync használja, ha feladatátvétel során meg kell tennie, győződjön meg arról, hogy a fájlmegosztások elérhetők az ügyfélszámítógép átlátható módon, manuális műveleteket. File Sync van szükség az ügyfélszámítógép megnyitni a 445-ös port.     |


### <a name="site-recovery-support"></a>Site Recovery támogatási
Mivel a Site Recovery replikációs alkalmazás független, ezekkel az ajánlásokkal várhatóan igaz az alábbi esetekben tárolásához.
| Forrás    |Egy másodlagos helyre    |Az Azure-bA
|---------|---------|---------|
|Azure| -|Igen|
|Hyper-V|   Igen |Igen
|VMware |Igen|   Igen
|Fizikai kiszolgáló|   Igen |Igen
 

> [!IMPORTANT]
> Az alábbi három módszer bármelyikével folytatás előtt győződjön meg arról, hogy ezeket a függőségeket is elvégzi.

**Hely – hely kapcsolat**: a helyszíni hely és az Azure-hálózat között közvetlen kapcsolatot kell létrehozni, hogy a kiszolgálók közötti kommunikációt. Használja a biztonságos helyek közötti VPN-kapcsolatot a vész-helyreállítási webhelyként, amely az Azure virtual Networkhöz. További információkért lásd: [egy helyszíni hely és a egy Azure virtuális hálózat között helyek közötti VPN-kapcsolat létrehozására](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Az Active Directory**: elosztott fájlrendszer replikációs szolgáltatása az Active Directory függ. Ez azt jelenti, hogy helyi tartományvezérlőkön az Active Directory-erdő kiterjed a vész-helyreállítási helyként az Azure-ban. Akkor is, ha az elosztott fájlrendszer-replikációs, ha az importálni kívánt felhasználók kell hozzáférést vagy a hozzáférés ellenőrzött nem használ, ezeket a lépéseket kell végeznie. További információkért lásd: [kiterjesztése a helyszíni Active Directory Azure-bA](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Azure IaaS virtuális gépek vész helyreállítási javaslat

Ha konfigurálja, és vészhelyreállítása az Azure IaaS virtuális gépeken üzemeltetett fájlkiszolgálók kezelése, választhat két lehetőség közül választhat, alapján, hogy kívánja-e át [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [File Sync használata](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [A Site Recovery használata](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Replikálni egy IaaS virtuális gépen üzemeltetett fájlokat a File Sync használatával

Az Azure Files használatával teljes mértékben lecserélheti vagy kiegészítheti a hagyományos helyszíni fájlkiszolgálókat vagy NAS-eszközöket. Azure-fájlmegosztások is replikálhatók a File Sync Windows-kiszolgálók, a helyszínen vagy a felhőben, a teljesítmény- és elosztott gyorsítótárazása érdekében az adatok hol használják. A vész helyreállítási javaslat az Azure virtuális gépek, amelyek ugyanazokat a funkciókat, mint a hagyományos fájlkiszolgálókhoz hajtanak végre a következő lépésekből áll:
* Gépek védelme a Site Recovery használatával. Kövesse a [egy Azure virtuális gép replikálása másik Azure-régióba](azure-to-azure-quickstart.md).
* File Sync használatával replikálni a fájlokat a virtuális gépről, amely a fájlkiszolgálóra a felhőbe.
* A Site Recovery használata [helyreállítási terv](site-recovery-create-recovery-plans.md) szkriptek hozzáadása a szolgáltatás [az Azure-fájlmegosztás csatlakoztatása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) és a virtuális gépen a megosztás eléréséhez.

A következő lépések röviden ismertesse a File Sync használatával:

1. [Storage-fiók létrehozása az Azure-ban](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha írásvédett georedundáns tárolás a storage-fiókok, olvasási hozzáférést kap az adatokhoz a másodlagos régióból egy esetleges vészhelyzet esetén. További információkért lásd: [Azure-beli fájlmegosztás vészhelyreállítási stratégiái](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Indítsa el a File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) az Azure-beli kiszolgálón.
4. Szinkronizálási csoport létrehozása. Végpontok egy szinkronizálási csoportban vannak szinkronban egymással. Szinkronizálási csoport tartalmaznia kell legalább egy felhőbeli végpont, amely Azure-fájlmegosztások. Szinkronizálási csoport is tartalmaznia kell egy kiszolgálói végpont, amely egy-egy Windows server elérési útját jelöli.
5. Vannak, a fájlok mostantól szinkronban tartani az Azure-fájlmegosztást és a helyszíni kiszolgáló között.
6. A helyszíni környezetben katasztrófa esetén végezzen el egy feladatátvételt a használatával egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Adja hozzá a szkriptet [az Azure-fájlmegosztás csatlakoztatása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) és a virtuális gépen a megosztás eléréséhez.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Egy IaaS fájl Servert futtató virtuális gép replikálásához a Site Recovery használatával

Ha rendelkezik olyan helyszíni ügyfelek, amelyek a IaaS file server virtuális gép elérhető, az összes az alábbi lépéseket. Egyéb esetben folytassa a 3. lépés.

1. A helyszíni hely és az Azure-hálózat között helyek közötti VPN-kapcsolat létrehozására.
2. Kiterjesztheti a helyszíni Active Directoryban.
3. [Vészhelyreállítás beállítása](azure-to-azure-tutorial-enable-replication.md) IaaS fájl kiszolgáló gép, egy másodlagos régióba.


A vész-helyreállítási egy másodlagos régióba további információkért lásd: [Ez a cikk](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Egy helyszíni fájlkiszolgáló replikálni a Site Recovery használatával

Az alábbi lépések bemutatják egy VMware virtuális gép replikációját. Hyper-V virtuális gépek replikálásához lépéseiért lásd: [ebben az oktatóanyagban](tutorial-hyper-v-to-azure.md).

1. [Azure-erőforrások előkészítése](tutorial-prepare-azure.md) a helyszíni gépek replikálásához.
2. A helyszíni hely és az Azure-hálózat között helyek közötti VPN-kapcsolat létrehozására. 
3. Kiterjesztheti a helyszíni Active Directoryban.
4. [A helyszíni VMware-kiszolgálók előkészítése](tutorial-prepare-on-premises-vmware.md).
5. [Vészhelyreállítás beállítása](tutorial-vmware-to-azure.md) a helyszíni virtuális gépek Azure-bA.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Elosztott fájlrendszer-replikációs kiterjesztése az Azure IaaS virtuális gépeken

1. A helyszíni hely és az Azure-hálózat között helyek közötti VPN-kapcsolat létrehozására. 
2. Kiterjesztheti a helyszíni Active Directoryban.
3. [Hozzon létre, és a fájlkiszolgáló virtuális gép kiépítése](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) az Azure-beli virtuális hálózaton.
Győződjön meg arról, hogy a virtuális gép bekerül az azonos Azure virtuális hálózat, amely a helyszíni környezet közötti kapcsolattal rendelkezik. 
4. Telepítse és [konfigurálja a DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) Windows Server rendszeren.
5. [Az elosztott Fájlrendszerbeli névtér megvalósítása](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Az elosztott Fájlrendszerbeli névterek megvalósítva, a vész-helyreállítási helyeken a nem éles környezetben a megosztott mappák feladatátvételi hajtható végre az elosztott Fájlrendszerbeli névtércélpont frissítése. Után ezek az elosztott Fájlrendszerbeli névtér replikálja a módosításokat az Active Directory felhasználók csatlakoznak a megfelelő mappát célok transzparens módon.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Replikálja a helyszíni fájlok a File Sync használatával
Használhatja a File Sync fájlok replikálására a felhőbe. Katasztrófa, és a helyi fájlkiszolgáló a elérhetetlensége esetén csatlakoztassa a kívánt fájlhelyeket a felhőből, és továbbra is az ügyfélgépek a szolgáltatáskéréseket.
A Site Recovery integrálható a File Sync:

* A file server gépek védelme a Site Recovery használatával. Kövesse a [ebben az oktatóanyagban](tutorial-vmware-to-azure.md).
* File Sync használatával replikálni a fájlokat a számítógépről, amely egy fájlkiszolgálón, a felhőbe.
* A Site Recovery a helyreállítási terv funkció használatával adja hozzá az Azure-fájlmegosztás csatlakoztatása a fájlkiszolgálón átvevő virtuális gép az Azure-ban parancsprogramokat.

Kövesse az alábbi lépéseket a File Sync használata:

1. [Storage-fiók létrehozása az Azure-ban](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Ha írásvédett georedundáns tárolás (ajánlott), a storage-fiókok, rendelkezik olvasási hozzáférés az adatokhoz a másodlagos régióból egy esetleges vészhelyzet esetén. További információkért lásd: [Azure-beli fájlmegosztás vészhelyreállítási stratégiái](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [File Sync üzembe helyezése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) a helyi fájlkiszolgálón.
4. Szinkronizálási csoport létrehozása. Végpontok egy szinkronizálási csoportban vannak szinkronban egymással. Szinkronizálási csoport tartalmaznia kell legalább egy felhőbeli végpont, amely Azure-fájlmegosztások. A szinkronizálási csoport is tartalmaznia kell egy kiszolgálói végpont, amely egy a helyszíni Windows server-elérési útját jelöli.
5. Vannak, a fájlok mostantól szinkronban tartani az Azure-fájlmegosztást és a helyszíni kiszolgáló között.
6. A helyszíni környezetben katasztrófa esetén végezzen el egy feladatátvételt a használatával egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Adja hozzá a szkriptet az Azure-fájlmegosztás csatlakoztatása és a virtuális gépen a megosztás eléréséhez.

> [!NOTE]
> Győződjön meg arról, hogy a 445-ös port nyitva-e. Az Azure Files SMB protokollt használ. Az SMB a 445-ös TCP-porton keresztül kommunikál. Ellenőrizze, hogy ha a tűzfal nem blokkolja-e egy ügyfélszámítógép a 445-ös TCP-port.


## <a name="do-a-test-failover"></a>Végezzen feladatátvételi tesztet

1. Nyissa meg az Azure Portalon, és válassza ki a Recovery Services-tárolót.
2. Válassza ki a helyreállítási tervet létrehozni a fájl-kiszolgálói környezet.
3. Válassza ki **feladatátvételi teszt**.
4. Válassza ki a helyreállítási pont és az Azure virtuális hálózat, a teszt feladatátvételi folyamat elindításához.
5. A másodlagos környezet után hajtsa végre az ellenőrzések.
6. Az ellenőrzés után válassza ki a **feladatátvételi teszt utáni karbantartás** megtisztítását a helyreállítási tervet, és a feladatátvételi tesztet.

További információ a feladatátvételi teszt végrehajtása: [a Site Recovery feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

Active Directory és DNS feladatátvételi teszt során, tekintse át [feladatátvételi szempontokat részletező cikkben, az Active Directory és DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Ehhez a feladatátvétel

1. Nyissa meg az Azure Portalon, és válassza ki a Recovery Services-tárolót.
2. Válassza ki a helyreállítási tervet létrehozni a fájl-kiszolgálói környezet.
3. Válassza ki **feladatátvételi**.
4. Válassza ki a helyreállítási pont a feladatátvételi folyamat elindításához.

A feladatátvétel elvégzéséhez további információkért lásd: [feladatátvétel a Site Recoveryben](site-recovery-failover.md).
