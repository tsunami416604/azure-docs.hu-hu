---
title: Helyszíni NAS-áttelepítés Azure File Syncre
description: Megtudhatja, hogyan telepítheti át a fájlokat egy helyszíni hálózati tároló (NAS) helyről egy hibrid Felhőbeli üzemelő példányra Azure File Sync és Azure-fájlmegosztás használatával.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 0d8d19256dfca21cc805c2689557099a6785f76b
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629206"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrálás hálózati csatlakoztatott tárolóból (NAS) hibrid felhőbe történő központi telepítésre Azure File Sync

Azure File Sync a közvetlenül csatlakoztatott tároló (DAS) helyein működik, és nem támogatja a hálózati csatolású tárolók (NAS) helyein való szinkronizálást.
Ez a tény a szükséges fájlok áttelepítését végzi el, és ez a cikk végigvezeti Önt a Migrálás megtervezésén és végrehajtásán.

## <a name="migration-goals"></a>Migrálási célok

A cél a NAS-berendezésen lévő megosztások áthelyezése Windows Serverre. Ezután használja a Azure File Synct a hibrid felhőalapú telepítéshez. Ezt az áttelepítést olyan módon kell végrehajtani, amely garantálja az üzemi adatok integritását, valamint a rendelkezésre állást az áttelepítés során. Az utóbbi megköveteli, hogy a leállások minimálisra kerüljenek, így az csak kis mértékben meghaladhatja a normál karbantartási időszakokat.

## <a name="migration-overview"></a>Migrálás áttekintése

Ahogy azt a Azure Files [áttelepítésének áttekintése című cikkben](storage-files-migration-overview.md)említettük, fontos a megfelelő másolási eszköz és megközelítés használata. A NAS-berendezés közvetlenül a helyi hálózaton teszi elérhetővé az SMB-megosztásokat. A RoboCopy, beépített Windows Server, a legjobb módszer a fájlok áthelyezésére ebben az áttelepítési forgatókönyvben.

- 1. fázis: annak [meghatározása, hogy hány Azure-fájlmegosztás szükséges](#phase-1-identify-how-many-azure-file-shares-you-need)
- 2. fázis: [megfelelő Windows Server-kiszolgáló kiépítése a helyszínen](#phase-2-provision-a-suitable-windows-server-on-premises)
- 3. fázis: [a Azure file Sync felhőalapú erőforrás üzembe helyezése](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 4. fázis: az [Azure Storage-erőforrások üzembe helyezése](#phase-4-deploy-azure-storage-resources)
- 5. fázis: [az Azure file Sync-ügynök üzembe helyezése](#phase-5-deploy-the-azure-file-sync-agent)
- 6. fázis: [Azure file Sync konfigurálása a Windows Serveren](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 7. fázis: [Robocopy](#phase-7-robocopy)
- 8. fázis: [felhasználói kivágás](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1. fázis: annak meghatározása, hogy hány Azure-fájlmegosztás szükséges

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>2. fázis: megfelelő Windows Server-kiszolgáló kiépítése a helyszínen

* Hozzon létre egy Windows Server 2019-at a minimális 2012R2 virtuális gép vagy fizikai kiszolgálóként. A Windows Server feladatátvételi fürt is támogatott.
* Hozzon létre vagy adjon hozzá közvetlenül csatlakoztatott tárolót (a DAS-t a NAS-hez képest, amely nem támogatott).

    Ha az Azure file syncs [Cloud rétegű](storage-sync-cloud-tiering.md) funkciót használja, a kiépített tárterület mérete kisebb lehet, mint amit jelenleg használ a NAS-berendezésben.
    Ha azonban a nagyobb NAS-területről másolja a fájlokat a kisebb Windows Server-kötetre egy későbbi fázisban, akkor a kötegekben kell működnie:

    1. Helyezze át a lemezre illeszkedő fájlok készletét
    2. a fájlok szinkronizálása és a felhőalapú rétegek bevonása
    3. Ha a köteten több szabad terület jön létre, folytassa a következő batch-fájllal. 
    
    Ennek a kötegelt megközelítésnek a elkerülésével kiépítheti a Windows Server megfelelő területét, amelyet a fájlok elfoglalnak a NAS-berendezésen. Érdemes lehet a NAS/Windows-t megismételni. Ha nem szeretné véglegesen véglegesíteni ezt a nagy mennyiségű tárterületet a Windows-kiszolgálóval, csökkentheti a kötet méretét az áttelepítés után, és a felhőalapú adatkorlátozási szabályzatok módosítása előtt. Az Azure-fájlmegosztás kisebb helyszíni gyorsítótárát hozza létre.

A telepített Windows Server erőforrás-konfigurációja (számítás és RAM) a szinkronizálni kívánt elemek (fájlok és mappák) számától függ. Ha bármilyen probléma merül fel, javasoljuk, hogy nagyobb teljesítmény-konfigurációt végezzen.

[Megtudhatja, hogyan méretezhető a Windows Server a szinkronizálni kívánt elemek (fájlok és mappák) száma alapján.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> A korábban csatolt cikk egy olyan táblázatot mutat be, amely a kiszolgáló memóriájának (RAM) tartományát tartalmazza. A kiszolgáló kisebb számára irányíthatja a kiszolgálót, de várhatóan a kezdeti szinkronizálás sokkal több időt vehet igénybe.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>3. fázis: a Azure File Sync felhőalapú erőforrás üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>4. fázis: az Azure Storage-erőforrások üzembe helyezése

Ebben a fázisban az 1. fázisban található leképezési táblázat alapján kell kiépíteni az Azure Storage-fiókok és-megosztások megfelelő számát.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>5. fázis: az Azure File Sync-ügynök üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>6. fázis: Azure File Sync konfigurálása a Windows Serveren

A regisztrált helyszíni Windows Servernek késznek kell lennie az internethez való csatlakozásra ehhez a folyamathoz.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> A felhő-rétegek az AFS szolgáltatás, amely lehetővé teszi, hogy a helyi kiszolgáló kevesebb tárolókapacitással rendelkezzen, mint amennyit a felhőben tárol, de a teljes névtér elérhetővé válik. Helyileg érdekes adat a gyors elérés érdekében helyileg is gyorsítótárazható. A felhőalapú rétegek a Azure File Sync "kiszolgálói végpont" választható funkciói.

> [!WARNING]
> Ha kevesebb tárterületet telepített a Windows Server-kötet (ek) ben a NAS-berendezésen használt adatainál, akkor a Felhőbeli rétegek kitöltése kötelező. Ha nem kapcsolja be a Felhőbeli rétegek bekapcsolását, akkor a kiszolgáló nem szabadít fel lemezterületet az összes fájl tárolásához. Állítsa be az áttelepítéshez ideiglenesen az 99%-os mennyiségű szabad területre vonatkozó előállítási szabályzatot. A Migrálás befejezése után térjen vissza a Felhőbeli rétegbeli beállításokhoz, és állítsa be azt egy hosszú távú hasznos szintre.

Ismételje meg a szinkronizálási csoport létrehozásának lépéseit és a megfelelő kiszolgáló mappa hozzáadását kiszolgálói végpontként az összes Azure-fájlmegosztás/-kiszolgáló helye számára, amelyet a szinkronizáláshoz kell konfigurálni.

Az összes kiszolgálói végpont létrehozása után a szinkronizálás működik. Létrehozhat egy tesztoldalt, és megtekintheti a kiszolgáló helyéről a csatlakoztatott Azure-fájlmegosztás (a szinkronizálási csoport Felhőbeli végpontja által leírtak szerint) szinkronizálását.

A kiszolgálói mappák és az Azure-fájlmegosztás mindkét helyen üresen maradnak, és a rendszer mindkét helyen várja az adattárolást. A következő lépésben megkezdi a fájlok másolását a Windows Serverbe Azure File Sync a felhőbe való áthelyezéshez. Ha engedélyezte a Felhőbeli rétegek használatát, a kiszolgáló elkezdi a rétegek fájljait, ha a helyi kötet (ek) on kívülről kifogyott a kapacitás.

## <a name="phase-7-robocopy"></a>7. fázis: RoboCopy

Az alapszintű áttelepítési módszer egy RoboCopy a NAS-készülékről a Windows Serverre, és Azure File Sync az Azure-fájlmegosztást.

Futtassa az első helyi másolatot a Windows Server célmappájában:

* Azonosítsa a NAS-berendezés első helyét.
* Azonosítsa a Windows Server megfelelő mappáját, amely már konfigurálva van Azure File Sync.
* A másolás elindítása a RoboCopy használatával

A következő RoboCopy parancs a NAS-tárolóból másolja a fájlokat a Windows Server célmappába. A Windows Server szinkronizálja az Azure-fájlmegosztás (ok) val. 

Ha kevesebb tárterületet telepített a Windows-kiszolgálón, mint amennyit a fájlok felvesznek a NAS-készülékre, akkor konfigurálta a Felhőbeli adatmennyiséget. Mivel a helyi Windows Server-kötet betelik, a [Felhőbeli rétegek](storage-sync-cloud-tiering.md) beindulnak, és a már sikeresen szinkronizált fájlokat. A Felhőbeli rétegek létrehozásához elegendő hely áll rendelkezésre, hogy továbbra is a NAS-berendezésből folytassa a másolást. A Felhőbeli rétegek ellenőrzése óránként egyszer megtekintheti, hogy mi szinkronizált, és szabadítson fel lemezterületet a 99%-os mennyiségű szabad terület eléréséhez.
Lehetséges, hogy a RoboCopy a fájlokat gyorsabban helyezi át, mint amennyire a felhőbe és a szintjére tud szinkronizálni, így a helyi lemezterület nem működik. A RoboCopy sikertelen lesz. Azt javasoljuk, hogy a megosztásokat egy olyan sorozatban hajtsa meg, amely meggátolja a működését. Ha például nem indítja el a RoboCopy-feladatokat az összes megosztáshoz, vagy csak olyan megosztásokat helyez át, amelyek megfelelnek a Windows Server jelenlegi szabad területének, néhányat említve.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
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
      /UNILOG:\<file name\>
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
      /B
   :::column-end:::
   :::column span="1":::
      A RoboCopy szolgáltatást ugyanazon a módban futtatja, amikor a biztonságimásolat-készítő alkalmazás használni fogja. Lehetővé teszi, hogy a RoboCopy olyan fájlokat helyezzen át, amelyekhez az aktuális felhasználónak nincs engedélye.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi a RoboCopy parancs többszöri futtatását ugyanazon cél/cél esetén egymás után. Ez azonosítja a korábban másolt fájlt, és kihagyja azt. Csak a módosítások, kiegészítések és *törlések* lesznek feldolgozva, amelyek az utolsó Futtatás óta történtek. Ha a parancs korábban nem volt futtatva, semmi nincs megadva. A */Mir* jelző kiváló megoldás a forrásként szolgáló helyekhez, amelyek továbbra is aktívan használatban vannak és változnak.
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

## <a name="phase-8-user-cut-over"></a>8. fázis: felhasználói kivágás

Amikor első alkalommal futtatja a RoboCopy parancsot, a felhasználók és az alkalmazások továbbra is hozzáférhetnek a NAS-fájlokhoz, és potenciálisan megváltoztathatják azokat. Lehetséges, hogy a RoboCopy egy könyvtárat dolgoz fel, a következőre lép, majd egy felhasználó a forrás helye (NAS) egy olyan fájlt ad hozzá, módosít vagy töröl, amely most nem lesz feldolgozva ebben a RoboCopy-futtatásban. Ez várt működés.

Az első futtatás arról szól, hogy az adatmennyiséget a Windows Serverre és a felhőbe helyezi át Azure File Syncon keresztül. Ez az első másolat hosszú időt is igénybe vehet, attól függően, hogy:

* letöltési sávszélesség
* a feltöltési sávszélesség
* a helyi hálózati sebesség és a szám, hogy az optimálisan hány RoboCopy-szálnak felel meg
* a RoboCopy és a Azure File Sync által feldolgozandó elemek (fájlok és mappák) száma

A kezdeti Futtatás befejezése után futtassa újra a parancsot.

A második alkalommal, amikor a rendszer gyorsabban befejeződik, mert csak az utolsó Futtatás óta végrehajtott módosításokat kell továbbítania. A második futtatás során a rendszer továbbra is felhalmozhat új módosításokat.

Ismételje meg ezt a folyamatot, amíg meggyőződött arról, hogy egy adott helyhez tartozó RoboCopy végrehajtásához szükséges idő egy elfogadható ablakban van az állásidőhöz.

Ha figyelembe veszi az állásidőt, és készen áll arra, hogy offline állapotba hozza a hálózati hozzáférést: a felhasználói hozzáférés offline állapotba léptetéséhez lehetősége van módosítani a megosztási gyökér ACL-jeit úgy, hogy a felhasználók többé nem férhetnek hozzá a helyhez, vagy bármilyen más olyan lépést is megtehetnek, amely megakadályozza a tartalom módosítását a NAS kiszolgálón.

Futtasson egy utolsó RoboCopy kört. Felveszi a módosításokat, amelyek esetleg kimaradtak.
Az utolsó lépés elvégzésének időtartama a RoboCopy vizsgálat sebességétől függ. A korábbi Futtatás időtartamának mérésével megbecsülheti az időt (amely az állásidővel egyenlő).

Hozzon létre egy megosztást a Windows Server mappában, és módosítsa a DFS-N központi telepítését úgy, hogy mutasson rá. Ügyeljen arra, hogy ugyanazokat a megosztási szintű engedélyeket adja meg, mint a NAS SMB-megosztás. Ha nagyvállalati szintű tartományhoz csatlakoztatott NAS-kiszolgálóval rendelkezett, akkor a felhasználói biztonsági azonosítók automatikusan egyeznek, ahogy a felhasználók szerepelnek Active Directoryban, és a RoboCopy teljes hűséggel másolja a fájlokat és a metaadatokat. Ha helyi felhasználókat használt a NAS-on, újra létre kell hoznia ezeket a felhasználókat a Windows Server helyi felhasználóként, és le kell képeznie a meglévő SID-ket a Windows Serverre az új, Windows Server helyi felhasználók biztonsági azonosítói között.

Elvégezte a megosztások/csoportok egy közös gyökerébe vagy kötetbe való áttelepítését. (Az 1. fázisból származó leképezéstől függően)

A másolatok közül néhányat párhuzamosan is futtathat. Javasoljuk, hogy egyszerre egy Azure-fájlmegosztás hatókörét dolgozza fel.

> [!WARNING]
> Miután áthelyezte az összes olyan hálózati kiszolgálót a Windows Serverre, amelyről az áttelepítés befejeződött: térjen vissza a Azure Portal **minden** _ szinkronizálási csoportba, és állítsa be a Felhőbeli kötet szabad területének százalékos értékét a gyorsítótár kihasználtságára alkalmasabb értékre, azaz 20%-ot. 

A felhő-rétegek kötetének szabad területére vonatkozó házirend olyan kötet szintjén működik, amelynek több kiszolgálói végpontja is szinkronizálva van. Ha a szabad területet még egy kiszolgálói végponton is módosítani szeretné, a szinkronizálás továbbra is alkalmazza a legszigorúbb szabályt, és megkísérli a 99%-os szabad lemezterület fenntartását, így a helyi gyorsítótár nem végezhető el a várt módon. Kivéve, ha a célja, hogy csak a ritkán használt, archivált adatok tárolására szolgáló kötet névterét adja meg, és egy másik forgatókönyvben a tárterület többi részét is kiszolgálja.

## <a name="troubleshoot"></a>Hibaelhárítás

A legvalószínűbb probléma az, hogy a RoboCopy parancs a Windows Server oldalon a "Volume Full" * értékkel meghiúsul. A felhő-rétegek a szinkronizált helyi Windows Server-lemezről óránként egyszer elürítik a tartalmat. A cél az, hogy eléri a 99%-os szabad területet a köteten.

A szinkronizálási folyamat és a Felhőbeli rétegek felszabadítása szabad lemezterületet szabadít fel. Megfigyelheti, hogy a Fájlkezelőben a Windows Serveren.

Ha a Windows-kiszolgáló elegendő rendelkezésre álló kapacitással rendelkezik, a parancs újbóli futtatása megoldja a problémát. Ha ezt a helyzetet tapasztalja, nem szakítja meg a biztonságot, és nyugodtan mozoghat. A parancs futtatásának kellemetlensége az egyetlen következmény.

A következő szakaszban található hivatkozásra kattintva megtudhatja, hogyan hibaelhárítási Azure File Sync problémákat.

## <a name="next-steps"></a>További lépések

További információ az Azure-fájlmegosztás és a Azure File Sync. A következő cikkek segítséget nyújtanak a speciális beállítások, az ajánlott eljárások és a hibaelhárítással kapcsolatos súgó megismerésében. Ezek a cikkek szükség szerint az [Azure file share-dokumentációra](storage-files-introduction.md) mutató hivatkozást tartalmaznak.

* [Az AFS áttekintése](./storage-sync-files-planning.md)
* [Az AFS telepítési útmutatója](storage-files-deployment-guide.md)
* [AFS – hibaelhárítás](storage-sync-files-troubleshoot.md)