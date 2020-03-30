---
title: StorSimple 1200 áttelepítés az Azure File Sync
description: Ismerje meg, hogyan telepítheti át a StorSimple 1200 sorozatú virtuális készüléket az Azure File Sync szolgáltatásba.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502365"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 áttelepítés az Azure File Sync

A StorSimple 1200 sorozat egy helyszíni adatközpontban futó virtuális berendezés. Lehetőség van az adatok áttelepítése a készülékről egy Azure File Sync környezetbe. Az Azure File Sync az alapértelmezett és stratégiai hosszú távú Azure-szolgáltatás, amelya StorSimple-készülékek áttelepíthető.

A StorSimple 1200 sorozat 2022 decemberében éri el [az élettartama végét.](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)  Fontos, hogy a lehető leghamarabb megkezdje a migráció tervezését. Ez a cikk a szükséges háttérismereteket és áttelepítési lépéseket az Azure File Sync sikeres áttelepítéséhez. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> A Microsoft elkötelezett az ügyfelek áttelepítésének segítése mellett. E-mail AzureFilesMigration@microsoft .com egy személyre szabott áttelepítési terv, valamint a segítséget az áttelepítés során.

Az Azure File Sync a Microsoft felhőszolgáltatása, amely két fő összetevőn alapul:

* Fájlszinkronizálás és felhőrétegezés.
* Fájlmegosztások natív tárolóként az Azure-ban, amely több protokollon, például az SMB-n és a REST fájlon keresztül érhető el. Az Azure-fájlmegosztás hasonló a Windows Serveren lévő fájlmegosztáshoz, amelyet natív módon csatlakoztathat hálózati meghajtóként. Támogatja a fontos fájlhűségi szempontokat, például az attribútumokat, az engedélyeket és az időbélyegeket. A StorSimple-től eltérően nincs szükség alkalmazásra/szolgáltatásra a felhőben tárolt fájlok és mappák értelmezéséhez. Az ideális és legrugalmasabb megközelítés az általános célú fájlkiszolgálói adatok, valamint néhány alkalmazásadat felhőben való tárolásához.

Ez a cikk az áttelepítési lépésekre összpontosít. Ha az áttelepítés előtt szeretne többet megtudni az Azure File Sync szolgáltatásról, a következő cikkeket javasoljuk:

* [Azure File Sync – áttekintés](https://aka.ms/AFS "Áttekintés")
* [Azure File Sync – telepítési útmutató](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migrálási célok

A cél a termelési adatok integritásának garantálása, valamint a rendelkezésre állás garantálása. Ez utóbbi minimálisra kell szorítani az állásidőt, hogy beférjen a szokásos karbantartási időszakokba, vagy csak kis mértékben haladja meg azt.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>A StorSimple 1200 áttelepítési elérési útja az Azure File Sync alkalmazáshoz

Az Azure File Sync ügynök futtatásához helyi Windows Server szükséges. A Windows Server legalább 2012R2 kiszolgáló lehet, de ideális esetben Windows Server 2019.

Számos alternatív áttelepítési útvonal létezik, és túl hosszú cikket hozna létre ahhoz, hogy mindegyiket dokumentálja, és bemutassa, hogy miért viselik a kockázatot vagy hátrányokat az általunk ajánlott útvonalon keresztül, mint a cikkben ajánlott legjobb gyakorlat.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Az áttelepítési útvonal áttekintése a cikkben alábbi lépésekről.":::

Az előző kép a cikk szakaszainak megfelelő lépéseket ábrázolja.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>1. lépés: A helyszíni Windows Server és a tárhely kiépítése

1. Hozzon létre egy Windows Server 2019 - legalább 2012R2 - virtuális gépként vagy fizikai kiszolgálóként. A Windows Server feladatátvételi fürtje is támogatott.
2. Közvetlen csatlakoztatott tároló kiépítése vagy hozzáadása (DAS a NAS-hoz képest, amely nem támogatott). A Windows Server-tároló méretének meg kell egyeznie a virtuális StorSimple 1200-as készülék rendelkezésre álló kapacitásának méretével.

### <a name="step-2-configure-your-windows-server-storage"></a>2. lépés: A Windows Server-tároló konfigurálása

Ebben a lépésben leképezheti a StorSimple tárolóstruktúrát (köteteket és megosztásokat) a Windows Server tárolóstruktúrájához.
Ha módosítani kívánja a tárolószerkezetet, azaz a kötetek számát, az adatmappák kötetekhez való társítását, vagy az almappa szerkezetét az aktuális SMB/NFS-megosztások felett vagy alatt, akkor itt az ideje, hogy ezeket a módosításokat figyelembe vegyük.
A fájl- és mappastruktúra módosítása az Azure File Sync konfigurálása után nehézkes, ezért el kell kerülni.
Ez a cikk feltételezi, hogy az 1:1 arányt képezi leképezve, ezért a jelen cikkben ismertetett lépések végrehajtásakor figyelembe kell vennie a leképezési módosításokat.

* A termelési adatok egyike sem kerülhet a Windows Server rendszerkötetére. A felhőrétegezés nem támogatott a rendszerköteteken. Ez a funkció azonban az áttelepítéshez, valamint a StorSimple-csereként végzett folyamatos műveletekhez szükséges.
* A StorSimple 1200 virtuális berendezésen ugyanannyi kötetet kell kiépíteni a Windows Serveren, mint a StorSimple 1200-as rendszerében.
* Konfigurálja a szükséges Windows Server-szerepköröket, -szolgáltatásokat és -beállításokat. Javasoljuk, hogy az operációs rendszer biztonságának és naprakészse érdekében engedélyezd a Windows Server frissítéseit. Hasonlóképpen azt javasoljuk, hogy a Microsoft Update szolgáltatás takarásban tartsa a Microsoft-alkalmazások naprakészen tartását, beleértve az Azure File Sync-ügynököt is.
* Ne állítson be mappákat vagy megosztásokat a következő lépések elolvasása előtt.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>3. lépés: Az első Azure File Sync felhőalapú erőforrás üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>4. lépés: A helyi kötet és mappastruktúra egyeztetése az Azure File Sync és az Azure fájlmegosztási erőforrásokkal

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>5. lépés: Azure-fájlmegosztások kiépítése

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>6. lépés: A Windows Server célmappáinak konfigurálása

Az előző lépésekben figyelembe vett minden olyan szempontot, amely meghatározza a szinkronizálási topológiák összetevőit. Itt az ideje, hogy előkészítse a szervert, hogy megkapja a fájlokat feltölteni.

Hozza létre **az összes** mappát, amely szinkronizálja az egyes saját Azure-fájlmegosztás.
Fontos, hogy kövesse a korábban dokumentált mappastruktúrát. Ha például úgy döntött, hogy több helyi SMB-megosztást szinkronizál egyetlen Azure-fájlmegosztásba, akkor a köteten egy közös gyökérmappába kell helyeznie őket. Hozza létre ezt a célgyökérmappát a köteten.

A kiépített Azure-fájlmegosztások számának meg kell egyeznie az ebben a lépésben létrehozott mappák számával + a gyökérszinten szinkronizálni kívánt kötetek számával.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>7. lépés: Az Azure File Sync ügynök telepítése

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>8. lépés: Szinkronizálás konfigurálása

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Ügyeljen arra, hogy kapcsolja be a felhőrétegezést!** Erre akkor van szükség, ha a helyi kiszolgálónak nincs elég hely az adatok teljes méretének tárolásához a StorSimple felhőalapú tárolójában. Állítsa be a rétegezési szabályzatot, ideiglenesen az áttelepítéshez, 99%-os szabad területre.

Ismételje meg a szinkronizálási csoport létrehozásának lépéseit és a megfelelő kiszolgálómappa kiszolgálóvégpontként való hozzáadását az összes Azure-fájlmegosztáshoz / kiszolgálóhelyhez, amelyeket szinkronizálásra kell konfigurálni.

### <a name="step-9-copy-your-files"></a>9. lépés: Fájlok másolása

Az alapvető áttelepítési megközelítés egy RoboCopy a StorSimple virtuális készülék a Windows Server, és az Azure File Sync azure-fájlmegosztások.

Futtassa a Windows Server célmappájának első helyi példányát:

* Azonosítsa az első helyet a virtuális StorSimple készüléken.
* Azonosítsa az egyező mappát a Windows Server, amely már rendelkezik az Azure File Sync konfigurálva van.
* A másolás indítása a RoboCopy segítségével

A következő RoboCopy parancs visszahívja a StorSimple Azure-tárolóból származó fájlokat a helyi StorSimple-be, majd áthelyezi őket a Windows Server célmappájába. A Windows Server szinkronizálja azt az Azure fájlmegosztás(ok)kal. Ahogy a helyi Windows Server-kötet megtelik, a felhőrétegezés beindul, és a már sikeresen szinkronizált fájlok at. A felhőrétegezés elegendő helyet hoz létre a StorSimple virtuális berendezésből való másolás folytatásához. A felhőrétegezés óránként egyszer ellenőrzi, hogy mi szinkronizált, és lemezterületet szabadítson fel a 99%-os szabad terület eléréséhez.

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
      Lehetővé teszi, hogy futtassa ezt a RoboCopy parancsot többször, egymás után ugyanazon a célon / cél. Azonosítja, hogy mit másolt, és kihagyja azt. A program csak az utolsó futtatás óta bekövetkezett módosításokat, kiegészítéseket és "*törléseket*dolgozza fel. Ha a parancs nem futott korábban, semmi sem marad ki. Ez egy kiváló lehetőség a forrás helyeken, amelyek még mindig aktívan használják, és a változó.
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

Amikor először futtatja a RoboCopy parancsot, a felhasználók és az alkalmazások továbbra is hozzáférnek a StorSimple fájlokhoz és mappákhoz, és potenciálisan módosítják azt. Lehetséges, hogy a RoboCopy feldolgozta a könyvtárat, továbblép a következőre, majd a felhasználó a forrás helyen (StorSimple) hozzáad, módosít vagy töröl egy fájlt, amely most nem lesz feldolgozva ebben a jelenlegi RoboCopy futtatásban. Az jó lesz.

Az első futtatás arról szól, hogy az adatok nagy részét visszahelyezi a helyszíni rendszerbe, át a Windows Serverbe, és az Azure File Sync segítségével biztonsági mentést a felhőbe. Ez hosszú időt vehet igénybe, attól függően, hogy:

* a letöltési sávszélesség
* a StorSimple felhőszolgáltatás visszahívási sebessége
* a feltöltési sávszélesség
* azon elemek (fájlok és mappák) száma, amelyeket bármelyik szolgáltatásnak fel kell dolgoznia

A kezdeti futtatás befejezése után futtassa újra a parancsot.

A második alkalommal gyorsabban fog befejeződni, mert csak az utolsó futtatás óta bekövetkezett változásokat kell szállítania. Ezek a változások valószínűleg a StorSimple már, mert a közelmúltban. Ez tovább csökkenti az időt, mert a felhőből való visszahívás szükségessége csökken. A második futtatás során még mindig új módosítások halmozódhatnak fel.

Ismételje meg ezt a folyamatot, amíg meg nem győződik arról, hogy a befejezéshez szükséges idő elfogadható állásidő.

Ha az állásidőt elfogadhatónak tartja, és készen áll arra, hogy a StorSimple helyet offline állapotba vigye, akkor tegye meg most: Például távolítsa el az SMB-megosztást, hogy a felhasználó ne férhessen hozzá a mappához, vagy ne tegyen meg minden olyan megfelelő lépést, amely megakadályozza, hogy a tartalom megváltozzon ebben a mappát a StorSimple-en.

Fuss egy utolsó RoboCopy kerek. Ez felveszi a változásokat, amelyek esetleg kimaradtak.
Mennyi ideig tart ez az utolsó lépés, a RoboCopy vizsgálat sebességététől függ. Megbecsülheti az időt (ami megegyezik az állásidővel) az előző futtatás élettartamának mérésével.

Hozzon létre egy megosztást a Windows Server mappában, és esetleg állítsa be a DFS-N központi telepítését úgy, hogy az mutasson rá. Ügyeljen arra, hogy ugyanazokat a megosztási szintű engedélyeket állítsa be, mint a StorSimple SMB-megosztás.

Befejezte a megosztás / megosztáscsoport közös gyökérbe vagy kötetbe történő áttelepítését. (Attól függően, hogy mit képez le, és úgy döntött, hogy ugyanabba az Azure-fájlmegosztásba kell bemenni.)

Megpróbálhat párhuzamosan futtatni néhány példányt. Azt javasoljuk, hogy egyszerre csak egy Azure-fájlmegosztás hatókörét dolgozza fel.

> [!WARNING]
> Miután áthelyezte az összes adatot a StorSimple-ből a Windows Serverkiszolgálóra, és az áttelepítés befejeződött: Térjen vissza az Azure ***Portalon lévő összes*** szinkronizálási csoporthoz, és állítsa be a felhőrétegezési kötet szabad területének értékét valami jobban alkalmas gyorsítótár-kihasználtságra, mondjuk 20%. 

A felhőrétegezési kötet szabad helyházirendje kötetszinten működik, és potenciálisan több kiszolgálóvégpont szinkronizálódik belőle. Ha elfelejti beállítani a szabad területet akár csak egy kiszolgálóvégponton is, a szinkronizálás továbbra is a legszigorúbb szabályt alkalmazza, és 99%-os szabad lemezterületet próbál meg tartani, így a helyi gyorsítótár nem a várt módon fog elhelyezkedni. Kivéve, ha a cél az, hogy csak egy olyan kötet névtere legyen, amely csak ritkán hozzáférhető archiválási adatokat tartalmaz.

## <a name="troubleshoot"></a>Hibaelhárítás

A legvalószínűbb probléma, amivel befuthat, az, hogy a RoboCopy parancs sikertelen a *"Kötet teljes"* a Windows Server oldalán. Ha ez a helyzet, akkor a letöltési sebesség valószínűleg jobb, mint a feltöltési sebesség. A felhőrétegezés óránként egyszer működik, hogy kiürítse a tartalmat a szinkronizált helyi Windows Server lemezről.

Hagyja, hogy a szinkronizálási folyamat és a felhőrétegezés lemezterületet szabadítson fel. Ezt a Windows Server fájlkezelőjében is megfigyelheti.

Ha a Windows Server elegendő rendelkezésre álló kapacitással rendelkezik, a parancs újbóli futtatása megoldja a problémát. Semmi sem törik el, ha ilyen helyzetbe kerülsz, és magabiztosan haladhatsz előre. A parancs ismételt futtatásának kényelmetlensége az egyetlen következmény.

Más Azure-fájlszinkronizálási problémák is meghatók.
Bármilyen valószínűtlenek is, ha ez megtörténik, tekintse meg a **LINK Azure File Sync hibaelhárítási útmutatóját.**

## <a name="relevant-links"></a>Releváns linkek

Áttelepítési tartalom:

* [StorSimple 8000 sorozatáttelepítési útmutató](storage-files-migration-storsimple-8000.md)

Azure File Sync tartalom:

* [AFS – áttekintés](https://aka.ms/AFS)
* [AFS telepítési útmutató](storage-files-deployment-guide.md)
* [AFS hibaelhárítás](storage-sync-files-troubleshoot.md)
