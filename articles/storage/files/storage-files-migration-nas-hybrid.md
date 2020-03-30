---
title: Helyszíni NAS-áttelepítés az Azure File Sync szolgáltatásba
description: Ismerje meg, hogyan telepítheti át a fájlokat egy helyszíni hálózati csatolt tárolóból (NAS) egy hibrid felhőbeli üzembe helyezésre az Azure File Sync és az Azure fájlmegosztások segítségével.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247350"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Áttelepítés a hálózathoz csatlakoztatott tárolóról (NAS) egy hibrid felhőtelepítésre az Azure File Sync segítségével

Az Azure File Sync a Közvetlen csatlakoztatott tároló (DAS) helyeken működik, és nem támogatja a hálózati csatolt tároló (NAS) helyekkel való szinkronizálást.
Ez a tény teszi a fájlok áttelepítése szükséges, és ez a cikk végigvezeti Önt a tervezés és az ilyen áttelepítés végrehajtása.

## <a name="migration-goals"></a>Migrálási célok

A cél az, hogy a NAS-készüléken lévő megosztásokat windows Server rendszerbe helyezze át. Ezután használja az Azure File Sync egy hibrid felhőbeli telepítés. Ezt az áttelepítést úgy kell elvégezni, hogy az garantálja a termelési adatok integritását, valamint az áttelepítés során rendelkezésre álló. Ez utóbbi minimálisra kell szorítani az állásidőt, hogy beférjen a szokásos karbantartási időszakokba, vagy csak kis mértékben haladja meg azt.

## <a name="migration-overview"></a>Áttelepítés – áttekintés

Ahogy azt az Azure Files [áttelepítésáttekintése cikkben,](storage-files-migration-overview.md)a megfelelő másolási eszköz és a megközelítés használata fontos. A NAS készülék közvetlenül a helyi hálózaton teszi ki az SMB-megosztásokat. RoboCopy, beépített Windows Server, a legjobb módja annak, hogy helyezze át a fájlokat ebben az áttelepítési forgatókönyv.

- 1. fázis: [Azonosítsa, hogy hány Azure-fájlmegosztásra van szüksége](#phase-1-identify-how-many-azure-file-shares-you-need)
- 2. fázis: [Megfelelő helyszíni Windows Server kiépítése](#phase-2-provision-a-suitable-windows-server-on-premises)
- 3. fázis: [Az Azure File Sync felhőalapú erőforrásának üzembe helyezése](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 4. fázis: [Azure storage-erőforrások üzembe helyezése](#phase-4-deploy-azure-storage-resources)
- 5. fázis: [Az Azure File Sync ügynök telepítése](#phase-5-deploy-the-azure-file-sync-agent)
- 6. fázis: [Az Azure File Sync konfigurálása Windows Server rendszeren](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 7. fázis: [RoboCopy](#phase-7-robocopy)
- 8. fázis: [A felhasználó által idoben](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1. fázis: Azonosítsa, hogy hány Azure-fájlmegosztásra van szüksége

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>2. fázis: Megfelelő helyszíni Windows Server kiépítése

* Hozzon létre egy Windows Server 2019 - legalább 2012R2 - virtuális gépként vagy fizikai kiszolgálóként. A Windows Server feladatátvételi fürtje is támogatott.
* Közvetlen csatlakoztatott tároló kiépítése vagy hozzáadása (DAS a NAS-hoz képest, amely nem támogatott).

    Az Azure File Syncs [felhőrétegezési](storage-sync-cloud-tiering.md) funkció használata esetén a rendelkezésre álló tárterület mennyisége kisebb lehet, mint amit jelenleg használ a NAS-készüléken.
    Ha azonban a fájlokat a nagyobb NAS-területről a kisebb Windows Server-kötetre másolja egy későbbi fázisban, kötegekben kell dolgoznia:

    1. A lemezre illeszkedő fájlok áthelyezése
    2. hagyja, hogy a fájlok szinkronizálása és a felhőrétegezés
    3. ha több szabad terület jön létre a köteten, folytassa a következő fájlkötegeléssel. 
    
    Ezt a kötegelési megközelítést elkerülheti, ha a Windows Serveren a nas-készüléken elfoglalni kívánt megfelelő helyet építi ki. Fontolja meg a deduplikáció a NAS / Windows. Ha nem szeretné véglegesen véglegesen lekötni ezt a nagy mennyiségű tárhelyet a Windows Server kiszolgálón, csökkentheti a kötet méretét az áttelepítés után és a felhőrétegezési házirendek módosítása előtt. Ez létrehoz egy kisebb helyszíni gyorsítótárazásaz Azure-fájlmegosztások.

A telepített Windows Server erőforrás-konfigurációja (számítási és RAM-ja) főként a szinkronizálandó elemek (fájlok és mappák) számától függ. Javasoljuk, hogy ha bármilyen problémája van, menjen magasabb teljesítménykonfigurációval.

[Megtudhatja, hogy miként méretezheti a Windows Serverrendszert a szinkronizálandó elemek (fájlok és mappák) száma alapján.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> A korábban csatolt cikk egy kiszolgálói memória (RAM) tartományt jelző táblázatot jelenít meg. A kiszolgáló kisebb száma felé orientálódik, de arra számít, hogy a kezdeti szinkronizálás lényegesen több időt vehet igénybe.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>3. fázis: Az Azure File Sync felhőalapú erőforrásának üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>4. fázis: Azure storage-erőforrások üzembe helyezése

Ebben a fázisban tekintse meg a leképezési táblát az 1.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>5. fázis: Az Azure File Sync ügynök telepítése

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>6. fázis: Az Azure File Sync konfigurálása Windows Server rendszeren

A regisztrált helyszíni Windows Server rendszernek készen kell lennie, és csatlakoznia kell az internethez ehhez a folyamathoz.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> A felhőrétegezés az AFS-szolgáltatás, amely lehetővé teszi, hogy a helyi kiszolgáló kevesebb tárolókapacitással rendelkezik, mint a felhőben, de a teljes névtér elérhető. A helyileg érdekes adatok helyi gyorsítótárba helyezhetőak a gyors hozzáférési teljesítmény érdekében. A felhőrétegezés egy opcionális funkció az Azure File Sync "kiszolgálóvégpont" szerint.

> [!WARNING]
> Ha kevesebb tárhelyet épített ki a Windows-kiszolgálókötet(ek)en, mint a NAS-készüléken használt adatok, akkor a felhőrétegezés kötelező. Ha nem kapcsolja be a felhőrétegezést, a kiszolgáló nem szabadít fel helyet az összes fájl tárolásához. Állítsa be a rétegezési szabályzatot, ideiglenesen az áttelepítéshez, 99%-os szabad területre. Győződjön meg róla, hogy az áttelepítés befejezése után visszatér a felhőrétegezési beállításokhoz, és állítsa be egy hosszú távú hasznos szintre.

Ismételje meg a szinkronizálási csoport létrehozásának lépéseit és a megfelelő kiszolgálómappa kiszolgálóvégpontként való hozzáadását az összes Azure-fájlmegosztáshoz / kiszolgálóhelyhez, amelyeket szinkronizálásra kell konfigurálni.

Az összes kiszolgálóvégpont létrehozása után működik a szinkronizálás. Létrehozhat egy tesztfájlt, és láthatja, hogy szinkronizálja a kiszolgáló helyét a csatlakoztatott Azure-fájlmegosztás (a felhő végpont a szinkronizálási csoportban).

Mindkét hely, a kiszolgálómappák és az Azure-fájlmegosztások egyébként üresek, és mindkét helyen adatokra várnak. A következő lépésben elkezdi a fájlok másolását a Windows Server for Azure File Sync-be, hogy áthelyezze őket a felhőbe. Abban az esetben, ha engedélyezte a felhőrétegezést, a kiszolgáló megkezdi a fájlok rétegezését, ha elfogy a kapacitás a helyi kötet(ek)en.

## <a name="phase-7-robocopy"></a>7. fázis: RoboCopy

Az alapvető áttelepítési megközelítés egy RoboCopy a NAS-készülékről a Windows Server, és az Azure File Sync azure-fájlmegosztások.

Futtassa a Windows Server célmappájának első helyi példányát:

* Azonosítsa a NAS készülék első helyét.
* Azonosítsa az egyező mappát a Windows Server, amely már rendelkezik az Azure File Sync konfigurálva van.
* A másolás indítása a RoboCopy segítségével

A következő RoboCopy parancs fájlokat másol a NAS-tárolóból a Windows Server célmappájába. A Windows Server szinkronizálja azt az Azure fájlmegosztás(ok)kal. 

Ha kevesebb tárhelyet épített ki a Windows Serveren, mint amennyit a fájlok a NAS-eszközön vesznek fel, akkor konfigurálta a felhőrétegezést. Ahogy a helyi Windows Server-kötet megtelik, a [felhőrétegezés](storage-sync-cloud-tiering.md) beindul, és a már sikeresen szinkronizált fájlok at. A felhőrétegezés elegendő helyet biztosít a NAS-készülékről való másolás folytatásához. A felhőrétegezés óránként egyszer ellenőrzi, hogy mi szinkronizált, és lemezterületet szabadítson fel a 99%-os szabad terület eléréséhez.
Lehetséges, hogy a RoboCopy gyorsabban mozgatja a fájlokat, mint ahogy a felhőre és a rétegre helyileg szinkronizálható, így elfogy a helyi lemezterület. RoboCopy akarat megbukik. Javasoljuk, hogy a megosztások on egy sorozat, amely megakadályozza, hogy a munka. Például nem indítroboCopy feladatokat az összes megosztásegy időben, vagy csak a mozgó részvények, amelyek illeszkednek az aktuális szabad hely a Windows Server, hogy csak néhányat említsünk.

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
      /UNILOG:\<fájlnév\>
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
      Lehetővé teszi, hogy futtassa ezt a RoboCopy parancsot többször, egymás után ugyanazon a célon / cél. Azonosítja, hogy mit másolt, és kihagyja azt. A program csak az utolsó futtatás óta bekövetkezett módosításokat, kiegészítéseket és "*törléseket*dolgozza fel. Ha a parancs nem futott korábban, semmi sem marad ki. A */MIR* jelző kiváló lehetőség a még aktívan használt és változó forráshelyekhez.
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

## <a name="phase-8-user-cut-over"></a>8. fázis: A felhasználó által idoben

Amikor először futtatja a RoboCopy parancsot, a felhasználók és az alkalmazások továbbra is hozzáférnek a NAS fájljaihoz, és potenciálisan módosítják azokat. Lehetséges, hogy a RoboCopy feldolgozta a könyvtárat, továbblép a következőre, majd a felhasználó a forrás helyen (NAS) hozzáad, módosít vagy töröl egy fájlt, amely most nem lesz feldolgozva ebben a jelenlegi RoboCopy futtatásban. Ez várt működés.

Az első futtatás az adatok nagy részét a Windows Serverbe és a felhőbe való áthelyezéséről szól az Azure File Sync segítségével. Ez az első példány hosszú időt vehet igénybe, a következőktől függően:

* a letöltési sávszélesség
* a feltöltési sávszélesség
* a helyi hálózati sebesség és annak száma, hogy a RoboCopy szálak száma milyen optimálisan illeszkedik
* a RoboCopy és az Azure File Sync által feldolgozandó elemek (fájlok és mappák) száma

A kezdeti futtatás befejezése után futtassa újra a parancsot.

A második alkalommal gyorsabban fog befejeződni, mert csak az utolsó futtatás óta bekövetkezett változásokat kell szállítania. A második futtatás során még mindig új módosítások halmozódhatnak fel.

Ismételje meg ezt a folyamatot, amíg meg nem győződik arról, hogy a RoboCopy kitöltéséhez szükséges idő egy adott helyen egy elfogadható ablakon belül van az állásidő számára.

Ha az állásidőt elfogadhatónak tartja, és készen áll arra, hogy a NAS-helyet offline állapotba vigye: A felhasználói hozzáférés offline állapotba hozásához lehetősége van arra, hogy a megosztási gyökérre vonatkozó Hozzáférés-fiókokat úgy módosítsa, hogy a felhasználók többé ne férhessenek hozzá a helyhez, vagy ne tegyünk meg minden más megfelelő lépést. amely megakadályozza, hogy a nas-on lévő mappában lévő tartalom megváltozzon.

Fuss egy utolsó RoboCopy kerek. Ez akarat felfedez akármi megváltozik, amit erő volt eltévesztett.
Mennyi ideig tart ez az utolsó lépés, a RoboCopy vizsgálat sebességététől függ. Megbecsülheti az időt (ami megegyezik az állásidővel) az előző futtatás élettartamának mérésével.

Hozzon létre egy megosztást a Windows Server mappában, és esetleg állítsa be a DFS-N központi telepítését úgy, hogy az mutasson rá. Ügyeljen arra, hogy ugyanazokat a megosztási szintű engedélyeket állítsa be, mint a NAS SMB-megosztáson. Ha vállalati szintű tartományhoz csatlakozott NAS-t tartalmaz, akkor a felhasználói azonosítók automatikusan egyeznek, mivel a felhasználók léteznek az Active Directoryban, és a RoboCopy teljes hűséggel másolja a fájlokat és a metaadatokat. Ha helyi felhasználókat használt a NAS-on, újra létre kell hoznia ezeket a felhasználókat Windows Server helyi felhasználóként, és le kell képeznie a meglévő Biztonsági azonosítókat, amelyeket a RoboCopy áthelyezett a Windows Server kiszolgálóra az új, Windows Server helyi felhasználók azonosítóihoz.

Befejezte a megosztás / megosztáscsoport közös gyökérbe vagy kötetbe történő áttelepítését. (Az 1. fázistól függően)

Megpróbálhat párhuzamosan futtatni néhány példányt. Azt javasoljuk, hogy egyszerre csak egy Azure-fájlmegosztás hatókörét dolgozza fel.

> [!WARNING]
> Miután áthelyezte az összes adatot a NAS-ból a Windows Serverkiszolgálóra, és az áttelepítés befejeződött: Térjen vissza az Azure ***Portalon az összes*** szinkronizálási csoporthoz, és állítsa be a felhőrétegezési kötet szabad területének értékét valami jobban alkalmas gyorsítótár-kihasználtsági értékre, mondjuk 20%. 

A felhőrétegezési kötet szabad helyházirendje kötetszinten működik, és potenciálisan több kiszolgálóvégpont szinkronizálódik belőle. Ha elfelejti beállítani a szabad területet akár csak egy kiszolgálóvégponton is, a szinkronizálás továbbra is a legszigorúbb szabályt alkalmazza, és 99%-os szabad lemezterületet próbál meg tartani, így a helyi gyorsítótár nem a várt módon fog elhelyezkedni. Kivéve, ha a cél az, hogy csak egy olyan kötet névtere legyen, amely csak ritkán elérhető, archiválási adatokat tartalmaz, és a tárolóhely többi részét egy másik forgatókönyv számára foglalja le.

## <a name="troubleshoot"></a>Hibaelhárítás

A legvalószínűbb probléma, amivel befuthat, az, hogy a RoboCopy parancs sikertelen a *"Kötet teljes"* a Windows Server oldalán. A felhőrétegezés óránként egyszer működik, hogy kiürítse a tartalmat a szinkronizált helyi Windows Server lemezről. Célja, hogy elérje a 99%-os szabad helyet a hangerőn.

Hagyja, hogy a szinkronizálási folyamat és a felhőrétegezés lemezterületet szabadítson fel. Ezt a Windows Server fájlkezelőjében is megfigyelheti.

Ha a Windows Server elegendő rendelkezésre álló kapacitással rendelkezik, a parancs újbóli futtatása megoldja a problémát. Semmi sem törik el, ha ilyen helyzetbe kerülsz, és magabiztosan haladhatsz előre. A parancs ismételt futtatásának kényelmetlensége az egyetlen következmény.

Ellenőrizze a következő szakaszban található hivatkozást az Azure File Sync problémák elhárításához.

## <a name="next-steps"></a>További lépések

Az Azure-fájlmegosztásokról és az Azure File Sync-ről további információk is megtudhatók. Az alábbi cikkek segítenek megérteni a speciális beállításokat, gyakorlati tanácsokat, és hibaelhárítási súgót is tartalmaznak. Ezek a cikkek adott [esetben az Azure fájlmegosztási dokumentációjára](storage-files-introduction.md) hivatkoznak.

* [AFS – áttekintés](https://aka.ms/AFS)
* [AFS telepítési útmutató](storage-files-deployment-guide.md)
* [AFS hibaelhárítás](storage-sync-files-troubleshoot.md)
