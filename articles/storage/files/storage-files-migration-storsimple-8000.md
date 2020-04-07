---
title: StorSimple 8000 sorozatú áttelepítés az Azure File Sync szolgáltatásba
description: Ismerje meg, hogyan telepítheti át a StorSimple 8100 vagy 8600 készüléket az Azure File Sync szolgáltatásba.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7f0c4da7caf71670746e84d5cfaa457ebae57156
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755043"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 és 8600 áttelepítés az Azure File Sync

A StorSimple 8000 sorozatot a 8100-as vagy a 8600-as fizikai, helyszíni berendezések és felhőszolgáltatás-összetevőik képviselik. Lehetőség van az adatok áttelepítése ezek közül bármelyik készülék egy Azure File Sync környezetbe. Az Azure File Sync az alapértelmezett és stratégiai hosszú távú Azure-szolgáltatás, amelya StorSimple-készülékek áttelepíthető.

A StorSimple 8000 sorozat 2022 decemberében éri el [az élettartama végét.](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) Fontos, hogy a lehető leghamarabb megkezdje a migráció tervezését. Ez a cikk a szükséges háttérismereteket és áttelepítési lépéseket az Azure File Sync sikeres áttelepítéséhez. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> A Microsoft elkötelezett az ügyfelek áttelepítésének segítése mellett. E-mail AzureFilesMigration@microsoft .com egy személyre szabott áttelepítési terv, valamint a segítséget az áttelepítés során.

Az Azure File Sync a Microsoft felhőszolgáltatása, amely két fő összetevőn alapul:

* Fájlszinkronizálás és felhőrétegezés.
* Fájlmegosztások natív tárolóként az Azure-ban, amely több protokollon, például az SMB-n és a REST fájlon keresztül érhető el. Az Azure-fájlmegosztás hasonló a Windows Serveren lévő fájlmegosztáshoz, amelyet natív módon csatlakoztathat hálózati meghajtóként. Támogatja a fontos fájlhűségi szempontokat, például az attribútumokat, az engedélyeket és az időbélyegeket. Az Azure-fájlmegosztások, már nincs szükség egy alkalmazás vagy szolgáltatás a felhőben tárolt fájlok és mappák értelmezéséhez. Natív módon elérheti őket az ismerős protokollokon és ügyfeleken, például a Windows Fájlkezelőben. Ez teszi az Azure-fájlmegosztások az ideális, és legrugalmasabb megközelítés tárolására általános célú fájl kiszolgáló adatait, valamint néhány alkalmazás adatok, a felhőben.

Ez a cikk az áttelepítési lépésekre összpontosít. Ha az áttelepítés előtt szeretne többet megtudni az Azure File Sync szolgáltatásról, a következő cikkeket javasoljuk:

* [Azure File Sync – áttekintés](https://aka.ms/AFS "Áttekintés")
* [Azure File Sync – telepítési útmutató](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migrálási célok

A cél a termelési adatok integritásának garantálása, valamint a rendelkezésre állás garantálása. Ez utóbbi minimálisra kell szorítani az állásidőt, hogy beférjen a szokásos karbantartási időszakokba, vagy csak kis mértékben haladja meg azt.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000 sorozatáttelepítési útvonal az Azure File Sync alkalmazáshoz

Az Azure File Sync ügynök futtatásához helyi Windows Server szükséges. A Windows Server legalább 2012R2 kiszolgáló lehet, de ideális esetben Windows Server 2019.

Számos alternatív áttelepítési útvonal létezik, és túl hosszú cikket hozna létre ahhoz, hogy mindegyiket dokumentálja, és bemutassa, hogy miért viselik a kockázatot vagy hátrányokat az általunk ajánlott útvonalon keresztül, mint a cikkben ajánlott legjobb gyakorlat.

![StorSimple 8000 sorozatú áttelepítési fázisok áttekintése](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 sorozat áttelepítési útvonal áttekintése a fázisok tovább alább ebben a cikkben.")

Az előző kép a cikk szakaszainak megfelelő fázisokat ábrázol.
Felhőoldali áttelepítést használunk, hogy elkerüljük a fájlok szükségtelen visszahívását a helyi StorSimple készülékre. Ez a megközelítés elkerüli a helyi gyorsítótárazási viselkedés vagy a hálózati sávszélesség-használat befolyásolását, amelyek bármelyikhatással lehet az éles számítási feladatokra.
A felhőoldali áttelepítés az adatok pillanatképén (kötetklónozáson) működik. Így a termelési adatok elkülönítve vannak ettől a folyamattól - amíg az áttelepítés végén át nem csökken. Dolgozik le, ami lényegében egy biztonsági mentés, teszi a migráció biztonságos és könnyen megismételhető, ha befut bármilyen nehézséget.

## <a name="considerations-around-existing-storsimple-backups"></a>A meglévő StorSimple biztonsági mentések szempontjai

StorSimple lehetővé teszi, hogy biztonsági mentések formájában kötet klónok. Ez a cikk egy új kötetklónt használ az élő fájlok áttelepítéséhez.
Ha az élő adatok mellett biztonsági másolatokat is át kell telepítenie, akkor a jelen cikkben szereplő összes útmutatás továbbra is érvényes. Az egyetlen különbség az, hogy ahelyett, hogy egy új kötet klónnal kezdené, a legrégebbi biztonsági mentési kötet klónjával kezdi, amelyet át kell telepítenie.

A sorrend a következő:

* Határozza meg az áttelepítendő kötetklónok minimális készletét. Azt javasoljuk, hogy a lista a lehető legkisebb legyen, mert minél több biztonsági mentést telepít át, annál hosszabb ideig tart a teljes áttelepítési folyamat.
* Az áttelepítési folyamat során kezdje az áttelepítésre kívánt legrégebbi kötetklónnal, és minden további áttelepítéskor használja a következő legrégebbit.
* Amikor minden kötet klónáttelepítés befejeződik, el kell végeznie egy Azure-fájlmegosztás pillanatkép. [Az Azure-fájlmegosztási pillanatképek](storage-snapshots-files.md) hogyan őrizheti meg az Azure-fájlmegosztások fájljainak és mappastruktúrájának a fájlok és mappastruktúra pontos biztonsági mentéseit. Az áttelepítés befejezése után szüksége lesz ezekre a pillanatképekre, hogy az áttelepítés során minden egyes kötetklónok megőrzött verzióival rendelkezzen.
* Győződjön meg arról, hogy az Azure-fájlmegosztás pillanatképeit az összes Azure-fájlmegosztáshoz, amelyek ugyanazt a StorSimple-kötetet szolgálják ki. Kötet klónok a kötet szintjén, az Azure-fájlmegosztás-pillanatképek a megosztás szintjén vannak. A kötetklónok áttelepítése után el kell tartania egy megosztási pillanatképet (minden Egyes Azure-fájlmegosztáson).
* Ismételje meg az áttelepítési folyamatot egy kötetklónesetében, és minden kötetklónozás után ossza meg a pillanatképeket, amíg el nem kapja az élő adatok pillanatképét. A kötetklónok áttelepítésének folyamatát az alábbi fázisokban ismertetjük. 

Ha egyáltalán nem kell áthelyeznie a biztonsági másolatokat, és az Azure fájlmegosztási oldalán egy új biztonsági mentésláncot indíthat el, miután csak az élő adatok áttelepítése történik, akkor ez hasznos az áttelepítés összetettségének és az áttelepítés által igénybe vesz. Eldöntheti, hogy áthelyezi-e a biztonsági másolatokat, és hogy az egyes kötetek (nem az egyes megosztások) hány at rendelkezik a StorSimple-ben.

## <a name="phase-1-get-ready"></a>1. fázis: Felkészülés

:::row:::
    :::column:::
        ![A korábbi áttekintő kép egy részét bemutató kép, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        Az áttelepítés alapja egy kötetklón és egy virtuális felhőalapú berendezés, az úgynevezett StorSimple 8020.
Ez a fázis ezeknek az erőforrásoknak az Azure-ban való üzembe helyezésére összpontosít.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>StorSimple 8020 virtuális berendezés telepítése

A felhőalapú berendezés telepítése olyan folyamat, amely biztonságot, hálózatépítést és néhány egyéb szempontot igényel.

> [!IMPORTANT]
> A következő útmutató néhány felesleges szakaszt tartalmaz. Olvassa el és kövesse a cikket az elejétől a "3. Ezután térjen vissza ehhez a cikkhez. Nem kell befejezni "Step 3", vagy bármi azon túl, hogy az útmutató, ebben az időben.

[StorSimple 8020 virtuális berendezés telepítése](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>A használandó kötetklón meghatározása

Amikor készen áll az áttelepítés megkezdésére, az első lépés egy új kötetklónozás – ugyanúgy, mint a biztonsági mentés –, amely rögzíti a StorSimple felhőalapú tárhelyének aktuális állapotát. Vegyünk egy klón minden storsimple kötetek rendelkezik.
Ha áthelyezésre van szüksége, akkor az első kötetklón nem egy újonnan létrehozott klón, hanem a legrégebbi kötetklón (legrégebbi biztonsági másolat), amelyet át kell telepítenie.
A részletes útmutatásért tekintse meg [a "Meglévő StorSimple biztonsági mentések szempontjai"](#considerations-around-existing-storsimple-backups) című szakaszt.

> [!IMPORTANT]
> A következő útmutató néhány felesleges szakaszt tartalmaz. Olvassa el és kövesse csak a csatolt szakaszban ismertetett lépéseket. Ezután térjen vissza ehhez a cikkhez. Nem kell követnie a "Következő lépések" című szakaszt.

[Kötet klónjának létrehozása](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>A kötetklón használata

Az első fázis utolsó fázisa az, hogy a kiválasztott kötetklónt elérhetővé tegye az Azure 8020-as virtuális berendezésén.

> [!IMPORTANT]
> A következő útmutató tartalmazza a szükséges lépéseket, hanem - a végén - egy utasítást, hogy formázza a kötetet. **NE FORMÁZZA A KÖTETET** Olvassa el és kövesse a linkelt szakasz 7 az elejétől az utasításig: "10. Formázni egy egyszerű kötet, ..."  Állítsa le, mielőtt ezt a lépést követne, és térjen vissza ehhez a cikkhez.

[Kötetklón ozása az Azure 8020-as virtuális készülékére](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>1. fázis összefoglalása

Most, hogy befejezte az 1.

* Telepített egy StorSimple 8020 virtuális berendezést az Azure-ban.
* Meghatározva, hogy melyik kötetklónnal kezdi az áttelepítést.
* A kötetklón(ok) (minden élő kötethez) csatlakoztatva az Azure-beli StorSimple virtuális berendezéshez, a további használatra rendelkezésre álló adatokkal.

## <a name="phase-2-cloud-vm"></a>2. fázis: Felhőalapú virtuális gép

:::row:::
    :::column:::
        ![A korábbi áttekintő kép egy részét bemutató kép, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Miután a kezdeti klón elérhető a StorSimple 8020 virtuális berendezés az Azure-ban, most itt az ideje, hogy egy virtuális gép kiépítése, és tegye elérhetővé a kötet klón (vagy több) az adott virtuális gép iSCSI-n keresztül.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Azure-beli virtuális gép üzembe helyezése

A Windows Server virtuális gép az Azure-ban, mint a StorSimple 8020, egy ideiglenes infrastruktúra, amely csak akkor szükséges az áttelepítés során.
A telepített virtuális gép konfigurációja főként a szinkronizálandó elemek (fájlok és mappák) számától függ. Javasoljuk, hogy ha bármilyen problémája van, menjen magasabb teljesítménykonfigurációval.

Egyetlen Windows Server legfeljebb 30 Azure-fájlmegosztást szinkronizálhat.
A specifikációk úgy dönt, hogy magában kell foglalnia minden megosztás / elérési út, vagy a gyökér a StorSimple kötet és számít az elemek (fájlok és mappák).

Az adatok teljes mérete kisebb szűk keresztmetszet - ez a gép specifikációinak személyre szabásához szükséges elemek száma.

* [Megtudhatja, hogy miként méretezheti a Windows Serverrendszert a szinkronizálandó elemek (fájlok és mappák) száma alapján.](storage-sync-files-planning.md#recommended-system-resources)

    **Kérjük, vegye figyelembe:** A korábban csatolt cikk egy kiszolgálói memória (RAM) tartományt jelző táblázatot jelenít meg. Orientálja az Azure virtuális gép nagy száma felé. A helyszíni gép kisebb száma felé orientálható.

* [Ismerje meg, hogyan telepítheti a Windows Sever virtuális gép.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Győződjön meg arról, hogy a virtuális gép ugyanabban az Azure-régióban van telepítve, mint a StorSimple 8020 virtuális berendezés. Ha ennek az áttelepítésnek a részeként módosítania kell a felhőadatok régióját a mai régióból, ezt egy későbbi lépésben megteheti, amikor Azure-fájlmegosztásokat létesít.

> [!IMPORTANT]
> Gyakran előfordul, hogy egy helyszíni Windows Server a helyszíni StorSimple-készülék elülső állapotának. Egy ilyen konfigurációban engedélyezheti az "[Adatdeduplikáció](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)" szolgáltatást az adott Windows Server rendszeren. **Ha a StorSimple-adatokkal adatdeduplikációt használt, győződjön meg arról, hogy ezen az Azure-beli virtuális gépen is engedélyezi az adatdeduplikációt.** Ne keverje össze ezt a fájlszintű deduplikációt a StorSimples beépített blokkszintű deduplikációjával, amelyhez nincs szükség műveletre.

> [!IMPORTANT]
> A teljesítmény optimalizálása érdekében telepítsen egy **gyors operációsrendszer-lemezt** a felhőbeli virtuális géphez. A szinkronizálási adatbázist az összes adatkötet operációsrendszer-lemezén tárolja. Győződjön meg arról is, hogy **nagy operációsrendszer-lemezt**hoz létre. A StorSimple köteteken lévő elemek (fájlok és mappák) számától függően előfordulhat, hogy az operációs rendszer lemezének **több száz GiB-helyre** van szüksége a szinkronizálási adatbázis befogadásához.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>A StorSimple 8020 kötetek elérhetővé az Azure virtuális gép

Ebben a fázisban egy vagy több StorSimple kötetet csatlakoztat a 8020-as virtuális készülékről az iSCSI-n keresztül a kiépített Windows Server virtuális géphez.

> [!IMPORTANT]
> Az alábbi cikkekben csak a **Felhőalapú készülék privát IP-címének beküldése** és az **iSCSI-szakaszokon való csatlakozás,** majd térjen vissza ehhez a cikkhez.

1. [Magánhálózati IP-cím lekérése a felhőalapú berendezéshez](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Csatlakozás iSCSI-n keresztül](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>2. fázis összefoglalása

Most, hogy befejezte a második fázist, a következőket teszi: 

* Windows Server virtuális gép kiépítése a 8020-as virtuális StorSimple-készülékrégiójában
* A 8020-as készülék összes vonatkozó kötete elérhetővé téve a Windows Server virtuális gép számára iSCSI-n keresztül.
* Ekkor látnia kell a fájl- és mappatartalmat, amikor a kiszolgálói virtuális gépen a Fájlkezelőt használja a csatlakoztatott köteteken.

Csak akkor folytassa a 3.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>3. fázis: Az Azure-fájlmegosztások beállítása és az Azure File Sync-re való felkészülés

:::row:::
    :::column:::
        ![A korábbi áttekintő kép egy részét bemutató kép, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        Ebben a fázisban számos Azure-fájlmegosztást fog meghatározni és kiépíteni, létrehozva egy Windows Server-t a StorSimple-berendezés cseréjeként, és konfigurálhatja a kiszolgálót az Azure File Sync számára. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Meglévő névterek hozzárendelése az Azure-fájlmegosztásokhoz

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Azure-fájlmegosztások telepítése

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Ha módosítania kell az Azure-régiót az aktuális régióból, amelyben a StorSimple-adatok találhatók, majd üzembe kell építenie az Azure-fájlmegosztásokat a használni kívánt új régióban. Határozza meg a régió kiválasztásával, amikor az Azure-fájlmegosztásokat tároló tárfiókok kiépítésekor. Győződjön meg arról, hogy az Azure File Sync erőforrás is kiépíti az alábbi, ugyanabban az új régióban van.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Az Azure File Sync felhőalapú erőforrásának üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Ha módosítania kell az Azure-régiót a StorSimple-adatok jelenlegi régiójából, majd kiépítette az Azure-fájlmegosztások tárfiókokat az új régióban. Győződjön meg arról, hogy a Storage Sync Szolgáltatás telepítésekor ugyanazt a régiót választotta.

### <a name="deploy-an-on-premises-windows-server"></a>Helyszíni Windows Server telepítése

* Hozzon létre egy Windows Server 2019 - legalább 2012R2 - virtuális gépként vagy fizikai kiszolgálóként. A Windows Server feladatátvételi fürtje is támogatott. Ne használja újra a storSimple 8100 vagy 8600 előlapi kiszolgálóját.
* Közvetlen csatlakoztatott tároló kiépítése vagy hozzáadása (DAS a NAS-hoz képest, amely nem támogatott).

Ajánlott eljárás, hogy az új Windows Server ugyanolyan vagy nagyobb mennyiségű tárhelyet biztosítson, mint aStorSimple 8100 vagy 8600 készülék helyileg elérhető gyorsítótárazáshoz. A Windows Servert ugyanúgy fogja használni, mint a StorSimple készüléket, ha ugyanolyan mennyiségű tárolóval rendelkezik, mint a készülék, akkor a gyorsítótárazási élménynek hasonlónak kell lennie, ha nem ugyanaz.
A Windows Serverből tetszés szerint hozzáadhat vagy eltávolíthat tárhelyet. Ez lehetővé teszi a helyi kötet méretének és a gyorsítótárazáshoz rendelkezésre álló helyi tárterület méretének méretezését.

### <a name="prepare-the-windows-server-for-file-sync"></a>A Windows Server előkészítése a fájlszinkronizálásra

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Az Azure File Sync konfigurálása a Windows Server rendszeren

A regisztrált helyszíni Windows Server rendszernek készen kell lennie, és csatlakoznia kell az internethez ehhez a folyamathoz.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Ügyeljen arra, hogy kapcsolja be a felhőrétegezést!** A felhőrétegezés az AFS-szolgáltatás, amely lehetővé teszi, hogy a helyi kiszolgáló kevesebb tárolókapacitással rendelkezik, mint a felhőben, de a teljes névtér elérhető. A helyileg érdekes adatok helyi gyorsítótárba helyezhetőak a gyors, helyi hozzáférési teljesítmény érdekében. Egy másik ok, hogy kapcsolja be a felhőrétegezés ebben a lépésben az, hogy nem akarjuk szinkronizálni a fájltartalmát ebben a szakaszban, csak a névtér kell mozgatni ebben az időben.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>4. fázis: Konfigurálja az Azure virtuális gép szinkronizálásra

:::row:::
    :::column:::
        ![A korábbi áttekintő kép egy részét bemutató kép, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Ez a fázis az Azure Virtuális gépre vonatkozik az iSCSI-hez csatlakoztatott, első kötetes klónokkal. Ebben a fázisban a virtuális gép az Azure File Sync-en keresztül csatlakozik, és elindítja a fájlok áthelyezésének első körét a StorSimple kötetklón(ok)ból.
        
    :::column-end:::
:::row-end:::

Már konfigurálta a helyszíni kiszolgálót, amely lecseréli a StorSimple 8100 vagy 8600 készüléket az Azure File Sync számára. 

Az Azure virtuális gép konfigurálása egy majdnem azonos folyamat, egy további lépéssel. A következő lépések végigvezetik a folyamaton.

> [!IMPORTANT]
> Fontos, hogy az Azure virtuális gép **nincs konfigurálva a felhőrétegezés engedélyezve van!** A kiszolgáló kötetét újabb kötetklónokkal fogja cserélni az áttelepítés során. A felhőrétegezés nek nincs előnye, és a cpu-használat értendő többletterhelést.

1. [Telepítse az AFS-ügynököt. (lásd az előző szakaszt)](#prepare-the-windows-server-for-file-sync)
2. [A virtuális gép felindulása az Azure File Sync szolgáltatásra.](#get-the-vm-ready-for-azure-file-sync)
3. [Szinkronizálás konfigurálása](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>A virtuális gép felindulása az Azure File Sync szolgáltatásra

Az Azure File Sync segítségével a fájlokat a csatlakoztatott iSCSI StorSimple kötetek a cél Azure-fájlmegosztások.
Az áttelepítési folyamat során több kötetklónt fog csatlakoztatni a virtuális géphez, ugyanazon meghajtóbetűjel alatt. Az Azure File Sync-et úgy kell konfigurálni, hogy a fájlok és mappák újabb verziójaként csatlakoztatott következő kötetklónt láthassa, és frissítse az Azure File Sync-en keresztül csatlakoztatott Azure-fájlmegosztásokat. 

> [!IMPORTANT]
> Ahhoz, hogy ez működjön, az Azure File Sync konfigurálása előtt be kell állítani a beállításkulcsot a kiszolgálón.

1. Hozzon létre egy új könyvtárat a virtuális gép rendszermeghajtóján. Az Azure File Sync-adatokat ott kell megóvni a csatlakoztatott kötetklónok helyett. Például:`"C:\syncmetadata"`
2. Nyissa meg a regedit et, és keresse meg a következő beállításstruktúrát:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Új Karakterlánc típusú kulcs létrehozása, melynek ***neve: MetadataRootPath***
4. Állítsa be a teljes elérési utat a rendszerköteten létrehozott könyvtárhoz, például:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Az Azure File Sync konfigurálása az Azure virtuális gépen

Ez a lépés hasonló az előző szakaszhoz, amely bemutatja, hogyan konfigurálja az AFS-t a helyszíni kiszolgálón.

A különbség az, hogy nem engedélyezheti a felhőrétegezésezen a kiszolgálón, és hogy meg kell győződnie arról, hogy a megfelelő mappák kapcsolódnak a megfelelő Azure-fájlmegosztások. Ellenkező esetben az Azure-fájlmegosztások és az adattartalom elnevezése nem egyezik, és nincs mód a felhőbeli erőforrások vagy a helyi mappák átnevezésére a szinkronizálás újrakonfigurálása nélkül.

Az [Azure File Sync Windows Server rendszeren való konfigurálásáról szóló előző szakaszban](#configure-azure-file-sync-on-the-windows-server)található.

### <a name="step-4-summary"></a>4. lépés összefoglalása

Ezen a ponton sikeresen konfigurálta az Azure File Sync-et az On Azure Virtuális gépen, amelyhez a StorSimple kötetklón(ok)at iSCSI-n keresztül csatlakoztatta.
Az adatok most az Azure virtuális gépről a különböző Azure-fájlmegosztásokra áramlanak, és onnan egy teljesen fáradt névtér jelenik meg a helyszíni Windows Serveren.

> [!IMPORTANT]
> Győződjön meg arról, hogy jelenleg nincsenek módosítások vagy felhasználói hozzáférés a Windows Server számára.

A kezdeti kötet klónozási adatok az Azure virtuális gép az Azure-fájlmegosztások hosszú időt, esetleg heteket is igénybe vehet. Becslése az idő ez lesz trükkös, és számos tényezőtől függ. Leginkább az a sebesség, amellyel az Azure virtuális gép hozzáférhet a StorSimple-köteteken lévő fájlokhoz, és hogy az Azure File Sync milyen gyorsan tudja feldolgozni a szinkronizálást igénylő fájlokat és mappákat. 

Tapasztalatból kiindulva feltételezhetjük, hogy a sávszélesség – ezért a tényleges adatméret – alárendelt szerepet tölt be. Az az időpont, amerre ez vagy bármely későbbi áttelepítési kör tart, főként a másodpercenként feldolgozható elemek számától függ. Így például 1 TiB 100 000 fájllal és mappával valószínűleg lassabban fejeződik be, mint az 1 TiB, mindössze 50 000 fájllal és mappával.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>5. fázis: Több kötetes klónon keresztül itat

:::row:::
    :::column:::
        ![A korábbi áttekintő kép egy részét bemutató kép, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Az előző fázisban tárgyalt módon a kezdeti szinkronizálás hosszú időt vehet igénybe. A felhasználók és az alkalmazások továbbra is hozzáférnek a helyszíni StorSimple 8100 vagy 8600 készülékhez. Ez azt jelenti, hogy a változások halmozódnak, és minden nap egy nagyobb delta között az élő adatok és a kezdeti kötet klón, ön jelenleg migráció, formák. Ebben a szakaszban megtudhatja, hogyan minimalizálhatja az állásidőt több kötetklón használatával, és megtudhatja, hogy mikor történik a szinkronizálás.
    :::column-end:::
:::row-end:::

Sajnos a migrációs folyamat nem azonnali. Ez azt jelenti, hogy az élő adatok fent említett deltájának elkerülhetetlen következménye. A jó hír az, hogy meg lehet ismételni a folyamatot szerelési új kötet klónok. Minden kötet klón delta lesz fokozatosan kisebb. Így végül a szinkronizálás olyan időtartamon keresztül fejeződik be, amelyet ön elfogadhatónak tart ahhoz, hogy a felhasználók és az alkalmazások offline állapotba kerülhessenek a helyszíni Windows-kiszolgálóra való átvágáshoz.

Ismételje meg a következő lépéseket, amíg a szinkronizálás nem fejeződik be elég gyorsan, hogy a felhasználók és az alkalmazások offline állapotba kerülése kényelmes legyen:

1. [Határozza meg, hogy a szinkronizálás befejeződött egy adott kötetklónesetében.](#determine-when-sync-is-done)
2. [Vegyünk egy új kötet klón (ok), és csatlakoztassa a 8020 virtuális készülék.](#the-next-volume-clones)
3. [Határozza meg, hogy mikor készült el a szinkronizálás.](#determine-when-sync-is-done)
4. [Cut-over stratégia](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>A következő kötet klón(ok)

Már tárgyalt vesz egy kötet klón (ok) korábban ebben a cikkben.
Ennek a fázisnak két művelete van:

1. [Vegyünk egy kötet klón](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Csatlakoztassa a kötet klón (lásd fent)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>A szinkronizálás időpontjának meghatározása

Amikor a szinkronizálás befejeződött, leállíthatja az időmérést, és meghatározhatja, hogy meg kell-e ismételnie a kötetklón és a csatlakoztatás folyamatát, vagy ha az utolsó kötetklónlal való szinkronizálás elég kicsi volt.

Annak megállapításához, hogy a szinkronizálás befejeződött:

1. Az Eseménynapló megnyitása és az **Alkalmazások és szolgáltatások** megnyitása
2. Navigálás és a **Microsoft\FileSync\Agent\Telemetria megnyitása**
3. Keresse meg a legutóbbi **9102-es eseményt,** amely egy befejezett szinkronizálási munkamenetnek felel meg
4. Válassza a **Részletek** lehetőséget, és ellenőrizze, hogy a **SyncDirection** érték **Feltöltés**
5. Ellenőrizze a **HResult és** ellenőrizze, hogy azt **mutatja, 0**. Ez azt jelenti, hogy a szinkronizálási munkamenet sikeres volt. Ha a HResult értéke nem nulla, akkor hiba történt a szinkronizálás során. Ha a **PerItemErrorCount** nagyobb, mint 0, akkor néhány fájl vagy mappa szinkronizálása nem megfelelő. Lehetséges, hogy a HResult 0, de a PerItemErrorCount, amely nagyobb, mint 0. Ezen a ponton nem kell aggódnia a PerItemErrorCount. Majd később elkapjuk ezeket az aktákat. Ha ez a hibaszám jelentős, több ezer elem, forduljon az ügyfélszolgálathoz, és kérje, hogy csatlakozik az Azure File Sync termékcsoporthoz a legjobb útmutatást, a következő fázisok.
6. Ellenőrizze, hogy több 9102 esemény van a HResult 0-val egy sorban. Ez azt jelzi, hogy a szinkronizálás befejeződött ehhez a kötetklónozáshoz.

### <a name="cut-over-strategy"></a>Cut-over stratégia

1. Állapítsa meg, hogy a kötetklónból történő szinkronizálás elég gyors-e. (Delta elég kicsi.)
2. Állítsa offline állapotba a StorSimple készüléket.
3. Egy utolsó RoboCopy.

Mérje meg az időt, és állapítsa meg, hogy egy legutóbbi kötetklónszinkronizálás a rendszerben állásidőként megengedhető-e egy olyan időszakon belül, amelyet leállásként engedhet meg magának.

Itt az ideje, hogy tiltsa le a felhasználó hozzáférését a StorSimple készülékhez. Nincs több változás. Az állásidő elkezdődött.
A készüléket online állapotban kell hagynia, de most meg kell akadályoznia a rajta történő módosításokat.

Fázis 6 lesz felzárkózni minden delta az élő adatok, mivel az utolsó kötet klón.

## <a name="phase-6-a-final-robocopy"></a>6. fázis: A végső RoboCopy

Jelenleg két különbség van a helyszíni Windows Server és a StorSimple 8100 vagy 8600 készülék között:

1. Lehetnek olyan fájlok, amelyek nem szinkronizálódtak **(lásd: PerItemErrors** a fenti eseménynaplóból)
2. A StorSimple-készülék feltöltött gyorsítótárral rendelkezik, szemben a Windows Server kiszolgálóval, amely jelenleg nem tárol helyileg tárolt fájltartalmat.

![A korábbi áttekintő kép egy részét bemutató kép, amely segít a cikk ezen alszakaszának fókuszában.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Tudjuk, hogy a cache a Windows Server akár az állam a készülék, és biztosítja, hogy nem fájl marad a végső RoboCopy.

> [!CAUTION]
> Rendkívül fontos, hogy a RoboCopy parancsot követi, pontosan az alábbiakszerint. Csak olyan fájlokat szeretnénk másolni, amelyek helyiek, és olyan fájlokat, amelyek korábban nem haladtak át a kötetklón+szinkronizálási megközelítésen. Megoldhatjuk a problémákat, hogy miért nem szinkronizáltak később, miután az áttelepítés befejeződött. (Lásd: [Azure File Sync hibaelhárítás](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Valószínűleg nem nyomtatható karakterek a fájlnevekben, amelyek nem fognak hiányozni, amikor törli őket.)

RoboCopy parancs:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Háttér:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi a RoboCopy többszálas futtatását. Az alapértelmezett érték 8, max 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Unicode-ként adja ki a LOG fájl állapotát (felülírja a meglévő naplót).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Kimenetek a konzolablakba. A naplófájlkimenettel együtt használva.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Futtatja a RoboCopy-t ugyanabban az üzemmódban, amelyet egy biztonsági másolat készítő alkalmazás használna. Ez lehetővé teszi RoboCopy átfájlokat, hogy az aktuális felhasználó nem rendelkezik engedélyekkel.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi, hogy a RoboCopy csak a forrás (StorSimple-készülék) és a cél (Windows Server-könyvtár) közötti különbözeteket vegye figyelembe.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      a fájlmásolás hűsége (alapértelmezett: /COPY:DAT), másolási jelzők: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ACLs, O=Owner info, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      AZ ÖSSZES fájlinformáció MÁSOLÁSA (egyenértékű a /COPY:DATSOU kapcsolóval)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      könyvtárak másolásának hűsége (alapértelmezett: /DCOPY:DA), másolási jelzők: D=Adatok, A=Attribútumok, T=Időbélyegek
   :::column-end:::
:::row-end:::

Ezt a RoboCopy parancsot a Windows Server minden olyan könyvtárához kell futtatnia célként, amelyet egy Azure-fájlhoz való fájlszinkronizálással konfigurált.

Ezek közül több parancsot párhuzamosan is futtathat.
Miután ez a RoboCopy lépés befejeződött, engedélyezheti a felhasználók nak és az alkalmazásoknak, hogy úgy férjenek hozzá a Windows Serverhez, mint a StorSimple készülékelőtt.

Tekintse meg a robocopy naplófájl(ok) annak megtekintéséhez, hogy a fájlok at hátrahagyott. Ha problémák merülnek fel, a legtöbb esetben az áttelepítés befejezése után megoldhatja azokat, és a felhasználók és az alkalmazások újra a Windows Server kiszolgálóra kerültek. Ha bármilyen problémát meg kell oldania, tegye meg a 7.

Valószínűleg létre kell hoznia az SMB-megosztásokat a Windows Serveren, amelykorábban korábban a StorSimple-adatokon volt. Ezt a lépést előre betöltheti, és korábban megteheti, hogy ne veszítse el az idejét itt, de biztosítania kell, hogy ezen a ponton megelőzően ne történjen a fájlok módosítása a Windows kiszolgálón.

Ha DFS-N központi telepítéssel rendelkezik, a DFN-Namespaces-t az új kiszolgálómappa-helyekre irányíthatja. Ha nem rendelkezik DFS-N központi telepítéssel, és a 8100 8600-as készüléket helyileg egy Windows Server rel előadta, akkor a kiszolgálót leveheti a tartományból, és tartományhoz csatlakozhat az új Windows Server hez az AFS-sel a tartományhoz, ugyanazt a kiszolgálónevet adja meg, mint a régi kiszolgáló, és ugyanazt a megosztásnevet, akkor az új kiszolgálóra való átépítés átlátszó marad a felhasználók számára. , csoportházirend vagy parancsfájlok.

## <a name="phase-7-deprovision"></a>7. szakasz: Megszüntetés

Az utolsó fázisban több kötetklónon keresztül iterált, és végül sikerült csökkentenie a felhasználói hozzáférést az új Windows Serverhez, miután offline állapotba helyezte a StorSimple-készüléket.

Most már megkezdheti a felesleges erőforrások kioldását.
Mielőtt elkezdené, ajánlott megfigyelni az új Azure File Sync-telepítés éles környezetben, egy kicsit. Ez lehetőséget ad arra, hogy kijavítsa az esetlegesen felmerülő problémákat.

Miután elégedett, és legalább néhány napig megfigyelte az AFS-telepítést, a következő sorrendben kezdheti meg az erőforrások kiteljesítését:

1. Kapcsolja ki az Azure virtuális gép, amely már használt adatok áthelyezése a kötet klónok az Azure-fájlmegosztások fájlszinkronizálás sal.
2. Nyissa meg a Storage Sync Service-erőforrást az Azure-ban, és törölje az Azure virtuális gép regisztrációját. Ezzel eltávolítja az összes szinkronizálási csoportból.

    > [!WARNING]
    > **Győződjön meg róla, hogy a megfelelő gépet választja.** Kikapcsolta a felhőalapú virtuális gép kikapcsolását, ami azt jelenti, hogy a regisztrált kiszolgálók listájában csak offline kiszolgálóként kell megjelennie. Ebben a lépésben nem választhatja ki a helyszíni Windows Server t, mert ezzel nem regisztrálhatja azt.

3. Törölje az Azure virtuális gép és az erőforrások.
4. Tiltsa le a 8020 virtuális StorSimple készüléket.
5. Az Azure-beli Összes StorSimple-erőforrás kiirtása.
6. Húzza ki a StorSimple fizikai készüléket az adatközpontból.

Az áttelepítés befejeződött.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg az Azure File Sync szolgáltatással. Különösen a felhőrétegezési szabályzatok rugalmasságával.

Ha az Azure Portalon vagy a korábbi eseményekből azt látja, hogy egyes fájlok véglegesen nem szinkronizálódnak, tekintse át a hibaelhárítási útmutatót a problémák megoldásához szükséges lépésekért.

* [Az Azure File Sync áttekintése: aka.ms/AFS](https://aka.ms/AFS)
* [Felhőrétegezés](storage-sync-cloud-tiering.md) 
* [Útmutató az Azure File Sync hibaelhárítási útmutatójához](storage-sync-files-troubleshoot.md)
