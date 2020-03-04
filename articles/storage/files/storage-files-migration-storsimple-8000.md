---
title: StorSimple 8000 sorozat migrálása Azure File Syncre
description: Megtudhatja, hogyan telepíthet át egy StorSimple 8100-es vagy 8600-es készüléket Azure File Syncba.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 78100a5dd38b211f6b0241d5a0bac10cf86b09f6
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250947"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 és 8600 Migrálás Azure File Syncre

A StorSimple 8000 sorozat két különálló SKU-t jelöl, és ezek közül bármelyik SKU adatait áttelepítheti egy Azure File Sync környezetbe. Ez a cikk ismerteti mindkét készülék áttelepítését Azure File Syncre, és biztosítja a szükséges háttérbeli ismereteket és áttelepítési lépéseket, hogy a Migrálás Azure File Sync sikeres legyen.

## <a name="azure-file-sync"></a>Azure File Sync

A Azure File Sync egy Microsoft Cloud Service, amely két fő összetevő alapján érhető el:

* A fájlok szinkronizálása és a felhő szintjei.
* A fájlmegosztás natív tárolóként az Azure-ban, amely több protokollon keresztül érhető el, például az SMB és a file REST használatával. Az Azure-fájlmegosztás összehasonlítható a Windows Server egy fájlmegosztási fájljával, amelyet a hálózati meghajtóként natív módon csatlakoztathat. Támogatja a fontos fájl-megbízhatósági szempontokat, például az attribútumokat, az engedélyeket és az időbélyegeket. Az Azure-fájlmegosztás esetében már nincs szükség egy alkalmazásra vagy szolgáltatásra a felhőben tárolt fájlok és mappák értelmezéséhez. Ezeket natív módon érheti el az ismerős protokollokkal és az ügyfelekkel, például a Windows fájlkezelővel. Ez lehetővé teszi, hogy az Azure-fájlmegosztás ideális és legrugalmasabb megközelítést biztosítson az általános célú fájlkiszolgáló-adathalmazok, valamint néhány alkalmazásadatok tárolásához a felhőben.

Ez a cikk az áttelepítési lépésekre összpontosít. Ha az áttelepítés előtt szeretne többet megtudni a Azure File Syncről, javasoljuk a következő cikkeket:

* [Azure File Sync – áttekintés](https://aka.ms/AFS "Áttekintés")
* [Azure File Sync – üzembe helyezési útmutató](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migrálási célok

A cél a termelési adatok integritásának garantálása, valamint a rendelkezésre állás garantálása. Az utóbbi megköveteli, hogy a leállások minimálisra kerüljenek, így az csak kis mértékben meghaladhatja a normál karbantartási időszakokat.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000 sorozat áttelepítési útvonala Azure File Sync

Azure File Sync-ügynök futtatásához helyi Windows Server szükséges. A Windows Server legalább 2012R2-kiszolgáló lehet, de ideális esetben a Windows Server 2019.

Számos alternatív áttelepítési útvonal létezik, és az is előfordulhat, hogy túl sok cikket hoz létre, és bemutatjuk, hogy miért viselik a kockázatot vagy hátrányt az útvonalon az ajánlott eljárásnak megfelelően ebben a cikkben.

![A StorSimple 8000 sorozat áttelepítési fázisainak áttekintése](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "A StorSimple 8000 sorozat áttelepítési útvonalának áttekintése a jelen cikkben lejjebb található fázisokról.")

Az előző rendszerkép a cikk szakaszainak megfelelő fázisokat ábrázolja.
A Felhőbeli áttelepítéssel elkerülhető, hogy a fájlok ne legyenek a helyi StorSimple-készülékre emlékeztetve. Ezzel a módszerrel elkerülhető a helyi gyorsítótárazási viselkedés vagy a hálózati sávszélesség használatának hatása, ami hatással lehet az éles számítási feladatokra.
A felhőalapú áttelepítés az adatok egy pillanatképén (egy kötet klónján) működik. Így a termelési adatok el vannak különítve ebből a folyamatból – az áttelepítés végén a kivágásig. Az alapvetően biztonsági mentésből kiindulva a Migrálás biztonságos és könnyen ismételhető, ha bármilyen problémába ütközik.

## <a name="considerations-around-existing-storsimple-backups"></a>A meglévő StorSimple biztonsági mentések szempontjai

A StorSimple lehetővé teszi, hogy kötetek klónozása formájában készítsen biztonsági másolatokat. Ez a cikk egy új kötet-klónozást használ az élő fájlok migrálása érdekében.
Ha az élő adatok mellett át kell telepítenie a biztonsági mentéseket, a cikkben szereplő összes útmutatás továbbra is érvényes. Az egyetlen különbség, hogy az új kötet klónozása helyett az áttelepíteni kívánt legrégebbi biztonsági mentési kötet klónját kell kezdenie.

A folyamat a következő:

* Határozza meg az áttelepítendő kötet-klónozások minimális készletét. Javasoljuk, hogy a lista minimálisan megtartása, ha lehetséges, mert minél több biztonsági mentést telepít, annál hosszabb ideig tart a teljes áttelepítési folyamat.
* Az áttelepítési folyamat elvégzése után kezdje az áttelepíteni kívánt legrégebbi kötet-klónozással, és minden további áttelepítéskor használja a következő legrégebbi elemet.
* Ha minden kötet klónozása befejeződik, el kell végeznie egy Azure-fájlmegosztás pillanatképét. Az [Azure file share-Pillanatképek](storage-snapshots-files.md) az Azure-fájlmegosztás fájljainak és a mappák struktúrájának időponthoz kapcsolódó biztonsági mentéseit ismertetik. Az áttelepítés befejeződése után ezekre a pillanatképekre szüksége lesz, így biztosítva, hogy az áttelepítés során az egyes kötet-klónok megmaradt verziói megmaradjanak.
* Győződjön meg arról, hogy az Azure-fájlmegosztás pillanatképei minden olyan Azure-fájlmegosztás esetében elérhetők, amelyet ugyanaz a StorSimple-kötet szolgáltat. A kötetek klónozása a kötet szintjén történik, az Azure fájlmegosztás pillanatképei a megosztási szinten vannak. A kötet klónozásának áttelepítése után el kell végeznie egy megosztási pillanatképet (mindegyik Azure-fájlmegosztás esetében).
* Ismételje meg a kötet klónozásának áttelepítési folyamatát, és az egyes kötetek klónozása után ossza meg a megosztási pillanatképeket, amíg az élő adatok pillanatképére nem kerül sor. A kötetek klónozásának áttelepítésének folyamatát az alábbi fázisok ismertetik. 

Ha a biztonsági mentéseket egyáltalán nem kell áthelyeznie, és a csak az élő adatokat áttelepítését követően új biztonsági mentési láncot indíthat el az Azure-fájlmegosztás oldalain, akkor ez hasznos lehet a migráció összetettségének csökkentése és az áttelepítés elvégzése során. Eldöntheti, hogy áthelyezi-e a biztonsági másolatokat, és hogy hány kötetre (nem az egyes megosztásokra) van-e szüksége a StorSimple.

## <a name="phase-1-get-ready"></a>1\. fázis: felkészülés

:::row:::
    :::column:::
        ![Egy képet, amely a korábbi, áttekintő rendszerkép egy részét illusztrálja, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        A Migrálás alapja a kötet klónja, valamint egy StorSimple 8020 nevű virtuális felhőalapú berendezés.
Ebben a fázisban ezeknek az erőforrásoknak az üzembe helyezésére koncentrálunk az Azure-ban.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>StorSimple 8020 virtuális készülék üzembe helyezése

A felhőalapú berendezések üzembe helyezése olyan folyamat, amely biztonságot, hálózatkezelést és néhány egyéb szempontot igényel.

> [!IMPORTANT]
> A következő útmutató néhány szükségtelen szakaszt tartalmaz. Olvassa el és kövesse a kezdetektől egészen a "3. lépésig" című cikket. Ezután térjen vissza ehhez a cikkhez. Ebben az útmutatóban nem kell végrehajtania a "3. lépés" vagy az azt meghaladó minden mást.

[StorSimple 8020 virtuális készülék üzembe helyezése](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>A használni kívánt kötet klónozásának meghatározása

Amikor készen áll az áttelepítés megkezdésére, az első lépés egy új kötet klónozása – akárcsak a Backup esetében –, amely rögzíti a StorSimple Felhőbeli tárhelyének aktuális állapotát. Készítsen egy klónt az összes StorSimple-kötethez.
Ha szüksége van a biztonsági mentések áthelyezésére, akkor a használt első kötet-klón nem egy újonnan létrehozott klón, hanem a legrégebbi kötet klón (legrégebbi biztonsági mentés), amelyet át kell telepítenie.
Részletes útmutatásért tekintse meg a ["meglévő StorSimple biztonsági mentések körüli megfontolások"](#considerations-around-existing-storsimple-backups) című szakaszt.

> [!IMPORTANT]
> A következő útmutató néhány szükségtelen szakaszt tartalmaz. Olvassa el és kövesse a csatolt szakasz lépéseit. Ezután térjen vissza ehhez a cikkhez. Nem kell követnie a "következő lépések" szakaszt.

[Kötet klónozásának létrehozása](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>A kötet klónozásának használata

Az 1. fázis utolsó fázisában az Azure-beli 8020 virtuális készüléken elérhető kötet-klónozást kell elvégeznie.

> [!IMPORTANT]
> A következő útmutató tartalmazza a szükséges lépéseket, de – a kötet formázására szolgáló utasítással is. ne **FORMÁZZA a kötetet** Olvassa el és kövesse az elejétől kezdve a csatolt "7. szakasz" kifejezést a "10" utasításig. Egyszerű kötet formázása... "  A lépés követése előtt állítsa le a lépést, és térjen vissza ehhez a cikkhez.

[Kötet klónozásának csatlakoztatása az Azure-beli 8020 virtuális készüléken](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>1\. fázis – összefoglalás

Most, hogy elvégezte az 1. fázist, a következő lépéseket hajtja végre:

* Üzembe helyezett egy StorSimple 8020 virtuális készüléket az Azure-ban.
* Határozza meg, hogy melyik kötet klónja fogja megkezdeni az áttelepítést.
* Csatlakoztatta a kötet klón (oka) t (az egyes élő kötetek közül egyet) az Azure-beli StorSimple virtuális készülékhez, és az adatai további használatra is rendelkezésre állnak.

## <a name="phase-2-cloud-vm"></a>2\. fázis: Felhőbeli virtuális gép

:::row:::
    :::column:::
        ![Egy képet, amely a korábbi, áttekintő rendszerkép egy részét illusztrálja, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Miután a kezdeti klónozás elérhető volt az Azure-beli StorSimple 8020 virtuális készüléken, most ideje egy virtuális gépet kiépíteni, és a kötet klónozását (vagy több) a virtuális GÉPHEZ iSCSI-kapcsolaton keresztül.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Azure-beli virtuális gép üzembe helyezése

Az Azure-beli Windows Server rendszerű virtuális gép ugyanúgy, mint a StorSimple 8020, egy ideiglenes infrastruktúra, amely csak az áttelepítés során szükséges.
Az üzembe helyezett virtuális gép konfigurációja a szinkronizálni kívánt elemek (fájlok és mappák) számától függ. Ha bármilyen probléma merül fel, javasoljuk, hogy nagyobb teljesítmény-konfigurációt végezzen.

Egyetlen Windows-kiszolgáló legfeljebb 30 Azure-fájlmegosztást tud szinkronizálni.
Az Ön által eldöntött specifikációk minden megosztást/elérési utat vagy a StorSimple-kötet gyökerét, valamint az elemek (fájlok és mappák) megszámlálását szeretnék felölelni.

Az adatok összesített mérete kisebb a szűk keresztmetszetnél – ez az elemek száma, amelyekre szüksége van a gépi specifikációk a szolgáltatáshoz való testreszabásához.

* [Megtudhatja, hogyan méretezhető a Windows Server a szinkronizálni kívánt elemek (fájlok és mappák) száma alapján.](storage-sync-files-planning.md#recommended-system-resources)
* [Megtudhatja, hogyan helyezhet üzembe egy Windows Server rendszerű virtuális gépet.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Győződjön meg arról, hogy a virtuális gép ugyanabban az Azure-régióban van telepítve, mint a StorSimple 8020 virtuális készülék. Ha a Migrálás részeként a Felhőbeli adatok régióját is módosítania kell a ma tárolt régióból, ezt később is megteheti, ha kiépíti az Azure-fájlmegosztást.

### <a name="expose-the-storsimple-8020-volumes-to-the-vm"></a>Tegye elérhetővé a StorSimple 8020 köteteket a virtuális géphez

Ebben a fázisban egy vagy több StorSimple-kötetet csatlakoztat az 8020 virtuális készülékről az iSCSI-kapcsolaton keresztül az üzembe helyezett Windows Server-alapú virtuális GÉPRE.

> [!IMPORTANT]
> A következő cikkek esetében csak a **Get Private IP-cím beszerzése a felhőalapú készülékhez** és az iSCSI-szakasszal való **kapcsolódáshoz** és a cikkhez való visszatéréshez.

1. [Privát IP-cím beszerzése a felhőalapú berendezéshez](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Összekapcsolás iSCSI-kapcsolaton keresztül](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>2\. fázis – összefoglalás

Most, hogy elvégezte a 2. fázist, a következőket teheti: 

* Windows Server rendszerű virtuális gép üzembe helyezése ugyanabban a régióban, mint az 8020 Virtual StorSimple készülék
* Az 8020-ből a Windows Server rendszerű virtuális gépre iSCSI-kapcsolaton keresztül elérhető összes kötet kitéve.
* Ekkor meg kell jelennie a fájl-és mappa tartalmának, amikor a fájlkezelőt használja a kiszolgálói virtuális gépen a csatlakoztatott köteteken.

Csak akkor folytassa a 3. fázissal, ha végrehajtotta ezeket a lépéseket az összes áttelepítést igénylő köteten.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>3\. fázis: Azure-fájlmegosztás beállítása és felkészülés a Azure File Syncra

:::row:::
    :::column:::
        ![Egy képet, amely a korábbi, áttekintő rendszerkép egy részét illusztrálja, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        Ebben a fázisban számos Azure-fájlmegosztás meghatározására és kiépítésére, valamint a helyi Windows Server-kiszolgáló StorSimple-készülékre való lecserélésére és a Azure File Sync-kiszolgáló konfigurálására szolgál. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Meglévő névterek leképezése az Azure-fájlmegosztás

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Azure-fájlmegosztás üzembe helyezése

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Ha módosítania kell az Azure-régiót az aktuális régióból a StorSimple-adatokkal, akkor az Azure-fájlmegosztást a használni kívánt új régióban kell kiépíteni. Az Azure-fájlmegosztás tárolására szolgáló Storage-fiókok kiépítésekor a régiót a kiválasztásával határozhatja meg. Győződjön meg arról, hogy az alábbi Azure File Sync erőforrás is ugyanezt az új régiót fogja létrehozni.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>A Azure File Sync felhőalapú erőforrás üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Ha módosítania kell az Azure-régiót az aktuális régióból a StorSimple-adatokkal, akkor az új régióban üzembe helyezi az Azure-fájlmegosztás tárolási fiókját. Győződjön meg arról, hogy ugyanezt a régiót választotta a Storage Sync szolgáltatás telepítésekor.

### <a name="deploy-an-on-premises-windows-server"></a>Helyszíni Windows Server üzembe helyezése

* Hozzon létre egy Windows Server 2019-at a minimális 2012R2 virtuális gép vagy fizikai kiszolgálóként. A Windows Server feladatátvételi fürt is támogatott. Ne használja újra a kiszolgálót, mert a StorSimple 8100 vagy 8600.
* Hozzon létre vagy adjon hozzá közvetlenül csatlakoztatott tárolót (a DAS-t a NAS-hez képest, amely nem támogatott).

Az ajánlott eljárás, hogy az új Windows Server-kiszolgálót egyenlő vagy nagyobb mennyiségű tárhelyet adjon meg, mint amennyi a StorSimple 8100-es vagy 8600-es készüléke helyileg elérhető a gyorsítótárazáshoz. A Windows Servert ugyanúgy kell használnia, ahogy az StorSimple berendezést használta, ha a berendezés mérete azonos, akkor a gyorsítótárazási élménynek hasonlónak kell lennie, ha nem ugyanaz a tárterület.
A (z) a Windows-kiszolgálóról is hozzáadhat vagy eltávolíthat tárolót. Ez lehetővé teszi a helyi kötet méretének és a gyorsítótárazáshoz elérhető helyi tárterület mennyiségének a méretezését.

### <a name="prepare-the-windows-server-for-file-sync"></a>A Windows Server előkészítése fájl-szinkronizálásra

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Azure File Sync konfigurálása a Windows Serveren

A regisztrált helyszíni Windows Servernek késznek kell lennie az internethez való csatlakozásra ehhez a folyamathoz.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Ügyeljen arra, hogy bekapcsolja a Felhőbeli rétegek bekapcsolását.** A felhő-rétegek az AFS szolgáltatás, amely lehetővé teszi, hogy a helyi kiszolgáló kevesebb tárolókapacitással rendelkezzen, mint amennyit a felhőben tárol, de a teljes névtér elérhetővé válik. A helyileg érdekes adat a gyors, helyi hozzáférési teljesítmény érdekében helyileg is gyorsítótárazható. Egy másik ok a Felhőbeli rétegek bekapcsolására ebben a lépésben, hogy jelenleg nem szeretnénk szinkronizálni a fájl tartalmát ebben a fázisban, csak a névteret kell áthelyezni.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>4\. fázis: az Azure-beli virtuális gép konfigurálása szinkronizáláshoz

:::row:::
    :::column:::
        ![Egy képet, amely a korábbi, áttekintő rendszerkép egy részét illusztrálja, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Ebben a fázisban az Azure-beli virtuális gép az iSCSI-csatlakoztatott, az első kötet klónozásával foglalkozik. Ebben a fázisban a virtuális gépet Azure File Sync-on keresztül csatlakoztatja, és elindít egy első kört a StorSimple kötet klón (ok) ból.
        
    :::column-end:::
:::row-end:::

Már konfigurálta a helyszíni kiszolgálót, amely lecseréli a StorSimple 8100-es vagy 8600-es készüléket a Azure File Sync. 

Az Azure-beli virtuális gép konfigurálása majdnem azonos folyamat, egy további lépéssel. A következő lépések végigvezetik a folyamaton.

> [!IMPORTANT]
> Fontos, hogy az Azure-beli virtuális gép **ne legyen engedélyezve a felhőalapú rétegek** beállításával! Ennek a kiszolgálónak a kötetét az áttelepítés során újabb kötet-klónokkal kell kicserélni. A Felhőbeli rétegek nem rendelkeznek előnyökkel és terheléssel a CPU-használat elkerüléséhez.

1. [Az AFS-ügynök üzembe helyezése. (lásd az előző szakaszt)](#prepare-the-windows-server-for-file-sync)
2. [A virtuális gép lekérése Azure File Syncre készen áll.](#get-the-vm-ready-for-azure-file-sync)
3. [Szinkronizálás konfigurálása](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>A virtuális gép lekérése Azure File Sync használatra kész

Azure File Sync használatával helyezheti át a fájlokat a csatlakoztatott iSCSI StorSimple-kötetekről a cél Azure-fájlmegosztás felé.
Az áttelepítési folyamat során több kötet-klónt fog csatlakoztatni a virtuális géphez ugyanazon a meghajtóbetűjelen. Azure File Synct úgy kell konfigurálni, hogy megtekintse a fájlok és mappák újabb verziójához csatlakoztatott következő kötet-klónt, és frissítse a Azure File Sync használatával csatlakoztatott Azure-fájlmegosztást. 

> [!IMPORTANT]
> Ahhoz, hogy működjön, meg kell adni egy beállításkulcsot a kiszolgálón, mielőtt a Azure File Sync konfigurálva lenne.

1. Hozzon létre egy új könyvtárat a virtuális gép rendszermeghajtóján. A csatlakoztatott kötetek klónozása helyett Azure File Sync adatokat is meg kell őrizni. Például:`“C:\syncmetadata”`
2. Nyissa meg a regeditt, és keresse meg a következő beállításjegyzék-struktúrát `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`:
3. Hozzon létre egy string típusú új kulcsot, amelynek neve: ***MetadataRootPath***
4. Állítsa be a rendszerköteten létrehozott könyvtár teljes elérési útját, például: `C:\syncmetadata”`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Azure File Sync konfigurálása az Azure-beli virtuális gépen

Ez a lépés hasonló az előző szakaszhoz, amely bemutatja, hogyan konfigurálja az AFS-t a helyszíni kiszolgálón.

A különbség az, hogy nem engedélyezheti a Felhőbeli rétegek bekapcsolását ezen a kiszolgálón, és meg kell győződnie arról, hogy a megfelelő mappák csatlakoznak a megfelelő Azure-fájlmegosztáshoz. Ellenkező esetben az Azure-fájlmegosztás és az adattartalom neve nem egyezik, és nincs lehetőség a Felhőbeli erőforrások vagy a helyi mappák átnevezésére a szinkronizálás újrakonfigurálása nélkül.

Tekintse át a [Azure file Sync konfigurálása Windows Serveren című szakaszt az előző szakaszban](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>4\. lépés – összefoglalás

Ezen a ponton sikeresen konfigurálta Azure File Synct azon az Azure-beli virtuális gépen, amelyhez a StorSimple-kötet klónozását iSCSI-n keresztül csatlakoztatta.
Az adatok mostantól az Azure-beli virtuális gépről a különböző Azure-fájlmegosztás felé áramlanak, és onnan teljesen megfáradt névtér jelenik meg a helyszíni Windows Serveren.

> [!IMPORTANT]
> Győződjön meg arról, hogy jelenleg nem történt módosítás vagy felhasználói hozzáférés a Windows Server számára.

Az Azure-beli virtuális gépen az Azure-fájlmegosztás felé haladó kezdeti kötet klónozása hosszú időt vehet igénybe, akár hetekig is. A következő idő megbecslése bonyolult, és számos tényezőtől függ. A legtöbb esetben az Azure-beli virtuális gép a StorSimple-köteteken található fájlokhoz fér hozzá, és hogy milyen gyors Azure File Sync képes feldolgozni a szinkronizálást igénylő fájlokat és mappákat. 

A tapasztalatok alapján feltételezhető, hogy a sávszélesség – ezért a tényleges adatméret – alárendelt szerepet játszik. Az ilyen vagy az azt követő áttelepítési kör elvégzésének időpontja többnyire a másodpercenként feldolgozható elemek számától függ. Így például az 1 TiB 100 000-fájlokkal és-mappákkal a legvalószínűbb, hogy az 1 TiB-nál lassabban fog futni, csak 50 000 fájlokkal és mappákkal.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>5\. fázis: iteráció több kötet klónozásán keresztül

:::row:::
    :::column:::
        ![Egy képet, amely a korábbi, áttekintő rendszerkép egy részét illusztrálja, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Ahogy az előző szakaszban is látható, a kezdeti szinkronizálás hosszú időt is igénybe vehet. A felhasználók és az alkalmazások továbbra is hozzáférnek a helyszíni StorSimple 8100 vagy 8600 készülékhez. Ez azt jelenti, hogy a változások felhalmozódnak, és az élő és a kezdeti kötetek klónozása közötti minden nap nagyobb különbözettel rendelkeznek, jelenleg Migrálás, űrlapok. Ebből a szakaszból megtudhatja, hogyan csökkentheti az állásidőt több kötet klónozásával, és mondja el, hogy mikor történik a szinkronizálás.
    :::column-end:::
:::row-end:::

Sajnos az áttelepítési folyamat nem azonnali. Ez azt jelenti, hogy az élő adatváltozások esetében a fent említett különbözet elkerülhetetlen következmény. A jó hír az, hogy megismételheti az új kötet-klónok csatlakoztatásának folyamatát. A kötetek klónozásának Delta-kötetei fokozatosan kisebbek lesznek. Így végül a szinkronizálás az idő múlásával fejeződik be, hogy a felhasználók és az alkalmazások offline állapotba helyezése a helyszíni Windows Serverre való kivágást követően is elfogadható.

Ismételje meg az alábbi lépéseket, amíg a szinkronizálás nem fejeződik be elég gyorsan, hogy a felhasználók és az alkalmazások offline állapotba helyezése megtörténjen:

1. [Megadhatja, hogy a szinkronizálás befejeződjön-e egy adott kötet klónozásakor.](#determine-when-sync-is-done)
2. [Vegyen fel egy új kötet-klónt, és csatlakoztassa az 8020 virtuális készülékhez.](#the-next-volume-clones)
3. [A szinkronizálás megtörténtének meghatározása.](#determine-when-sync-is-done)
4. [Feldarabolt stratégia](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>A következő kötet-klón (ok)

A jelen cikk korábbi részében a kötet klón (ok) beszedését tárgyaljuk.
Ebben a fázisban két művelet van:

1. [Kötet klónozása](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [A kötet klónozásának csatlakoztatása (lásd fent)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>A szinkronizálás megtörténtének meghatározása

A szinkronizálás elvégzése után leállíthatja az időmérést, és meghatározhatja, hogy meg kell-e ismételnie a kötetek klónozásának folyamatát, illetve azt, hogy az idő szinkronizálása az utolsó kötet klónozása során is elég kicsi volt-e.

A szinkronizálás befejezésének megállapításához:

1. Nyissa meg a Eseménynapló és navigáljon az **alkalmazásokhoz és a szolgáltatásokhoz**
2. A **Microsoft\FileSync\Agent\Telemetry** navigálása és megnyitása
3. Keresse meg a legutóbbi, **9102**-as eseményt, amely megfelel egy befejezett szinkronizálási munkamenetnek.
4. Válassza a **részletek** lehetőséget, és győződjön meg arról, hogy a **SyncDirection** érték **fel van feltöltve**
5. Ellenőrizze a **HResult** , és ellenőrizze, hogy a **0**érték jelenik-e meg. Ez azt jelenti, hogy a szinkronizálási munkamenet sikeres volt. Ha a HResult értéke nem nulla, akkor hiba történt a szinkronizálás során. Ha a **PerItemErrorCount** nagyobb, mint 0, akkor egyes fájlok vagy mappák nem voltak megfelelően szinkronizálva. Lehetséges, hogy HResult 0, de a PerItemErrorCount értéke 0-nál nagyobb. Ezen a ponton nem kell aggódnia a PerItemErrorCount. Ezeket a fájlokat később fogjuk megfogni. Ha ez a hiba jelentős, több ezer elemet tartalmaz, forduljon az ügyfélszolgálathoz, és kérje meg, hogy kapcsolódjon a Azure File Sync termékcsoport közvetlen útmutatóként a legjobb, következő fázisokban.
6. Ellenőrizze, hogy több 9102 eseményt HResult 0 értékkel egy sorba. Ez azt jelzi, hogy a kötet klónozásának szinkronizálása befejeződött.

### <a name="cut-over-strategy"></a>Feldarabolt stratégia

1. Annak megállapítása, hogy a kötet klónozásának szinkronizálása most már elég gyors-e. (A Delta elég kicsi.)
2. A StorSimple készülék offline állapotba helyezése.
3. Egy végső RoboCopy.

Mérje fel az időt, és állapítsa meg, hogy a legutóbbi kötetek klónozása egy adott időkereten belül befejeződik-e, és hogy a rendszer leállást biztosít-e.

Most letilthatja a felhasználói hozzáférést a StorSimple készülékhez. Nincs több változás. A leállás megkezdődött.
A készüléket online és csatlakoztatott állapotban kell hagyni, de most meg kell akadályoznia a módosításokat.

A 6. fázisban az utolsó kötet klónozása óta az élő adatforgalomban bármilyen különbözetet megtalál.

## <a name="phase-6-a-final-robocopy"></a>6\. fázis: A végső RoboCopy

Ezen a ponton két különbség van a helyszíni Windows Server és a StorSimple 8100-es vagy a 8600-es készülék között:

1. Lehetséges, hogy nem szinkronizált fájlok (lásd: **PerItemErrors** a fenti eseménynaplóból)
2. A StorSimple készülék egy feltöltött gyorsítótárral és a Windows Serverrel csak olyan névtérrel rendelkezik, amely nem helyileg tárolt tartalmat tartalmaz.

![Egy képet, amely a korábbi, áttekintő rendszerkép egy részét illusztrálja, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

A Windows-kiszolgáló gyorsítótárát akár a berendezés állapotára is felhasználhatja, így a végső RoboCopy nem marad a fájlban.

> [!CAUTION]
> Fontos, hogy az Ön által követett RoboCopy-parancs pontosan az alább leírtak szerint történjen. Csak azokat a fájlokat szeretnénk másolni, amelyek helyi és olyan fájlok, amelyek nem a Volume Clone + Sync megközelítéssel lettek áthelyezve. A Migrálás befejezése után megoldhatók a problémák, hogy miért nem voltak szinkronizálva később. (Lásd [Azure file Sync hibaelhárítást](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Ez valószínűleg nem nyomtatható karakter a fájlnevekben, amelyeket a rendszer nem fog kihagyni a törléskor.)

RoboCopy parancs:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Háttér

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi, hogy a RoboCopy több szálon fusson. Az alapértelmezett érték 8, Max 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Az állapotot a NAPLÓFÁJLba UNICODE-ként adja vissza (felülírja a meglévő naplót).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      A konzol ablakának kimenete. Egy naplófájlban a kimenettel együtt használatos.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi, hogy a RoboCopy csak a forrás (StorSimple-készülék) és a cél (Windows Server Directory) közötti különbözeteket vegye figyelembe.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      a fájlmásolás hűsége (az alapértelmezett érték a/COPY: DAT), a másolási jelzők: D = adat, A = attribútumok, T = időbélyeg, S = biztonság = NTFS ACL, O = tulajdonos adatai, U = naplózási információ
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      A fájl összes adatának másolása (egyenértékű a következő/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      a címtárak másolásának hűsége (az alapértelmezett érték a/DCOPY: DA), a másolási jelzők: D = az adat, A = attribútumok, A T = timestamps
   :::column-end:::
:::row-end:::

Ezt a RoboCopy-parancsot célként kell futtatnia a Windows Server minden egyes könyvtárához, ha a fájl-szinkronizálással konfigurálta az Azure-fájlt.

Ezeket a parancsokat párhuzamosan is futtathatja.
A RoboCopy lépés befejezése után lehetővé teheti, hogy a felhasználók és az alkalmazások hozzáférjenek a Windows Serverhez, például a StorSimple-berendezés előtt.

Tekintse át a Robocopy naplófájl (oka) t, és ellenőrizze, hogy a fájlok le vannak-e maradva. Ha problémákra van szüksége, akkor a legtöbb esetben a Migrálás befejezése után fel lehet oldani őket, és a felhasználók és az alkalmazások a Windows Serverre lettek újra felhasználva. Ha bármilyen problémát ki kell javítania, akkor a 7. fázis előtt tegye meg.

Valószínűleg létre kell hoznia az SMB-megosztásokat azon a Windows Serveren, amelyet korábban a StorSimple-on tárolt. Ezt a lépést előre betöltheti, és megteheti, hogy nem veszít időt ide, de ezt megelőzően mindenképpen meg kell győződnie arról, hogy a Windows Server nem módosítja a fájlokat.

Ha van DFS-N üzemelő példánya, a DFN-névtereket az új kiszolgálói mappák helyeire irányíthatja. Ha nem rendelkezik DFS-N üzemelő példányokkal, és a 8100 8600-es készüléket helyileg, egy Windows Serveren keresztül telepítette, akkor a kiszolgálót kikapcsolhatja a tartományból, és a tartományhoz csatlakoztathatja az új Windows Servert az AFS-vel a tartományhoz, hogy ugyanazt a kiszolgálónevet használja, mint a régi kiszolgálót és ugyanazokat a neveket használja, az új kiszolgálóra való kivágás pedig a felhasználók, a csoportházirendek és a parancsfájlok számára is transzparens marad.

## <a name="phase-7-deprovision"></a>7\. fázis: megszüntetés

Az utolsó fázisban több kötet klónozása is megtörtént, és végül a StorSimple készülék offline állapotba helyezése után kihasználhatja a felhasználói hozzáférést az új Windows Server-kiszolgálóhoz.

Most már elkezdheti a szükségtelen erőforrások megszüntetését.
Mielőtt elkezdené, érdemes megfigyelni az új Azure File Sync üzembe helyezését éles környezetben, egy kicsit. Ez lehetőséget biztosít az esetlegesen felmerülő problémák kijavítására.

Ha meggyőződött róla, hogy legalább néhány napig megfigyelte az AFS üzembe helyezését, megkezdheti az erőforrások kiépítését ebben a sorrendben:

1. Kapcsolja ki azt az Azure-beli virtuális gépet, amelyet a kötetek klónozásának az Azure-ba való áthelyezéséhez használtunk a file Sync használatával.
2. Lépjen a Storage Sync Service-erőforráshoz az Azure-ban, és szüntesse meg az Azure-beli virtuális gép regisztrációját. Ez eltávolítja az összes szinkronizálási csoportból.

    > [!WARNING]
    > **Győződjön meg arról, hogy a megfelelő gépet választotta.** A Felhőbeli virtuális gép ki van kapcsolva, ami azt jelenti, hogy az egyetlen kapcsolat nélküli kiszolgálóként jelenik meg a regisztrált kiszolgálók listáján. Ebben a lépésben nem kell kiválasztania a helyszíni Windows Server kiszolgálót, így a regisztrációja törlődik.

3. Törölje az Azure-beli virtuális gépet és annak erőforrásait.
4. Tiltsa le a 8020 virtuális StorSimple berendezést.
5. Az összes StorSimple erőforrás kiépítése az Azure-ban.
6. Húzza ki a StorSimple fizikai berendezést az adatközpontból.

A Migrálás befejeződött.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg Azure File Syncával. Különösen a felhő-előteret biztosító házirendek rugalmasságával.

Ha a Azure Portalban vagy a korábbi eseményekben látható, hogy egyes fájlok véglegesen nem szinkronizálhatók, tekintse át a hibaelhárítási útmutatót a hibák elhárításához.

* [Azure File Sync áttekintése: aka.ms/AFS](https://aka.ms/AFS)
* [Felhőbeli rétegek](storage-sync-cloud-tiering.md) 
* [Azure File Sync hibaelhárítási útmutató](storage-sync-files-troubleshoot.md)
