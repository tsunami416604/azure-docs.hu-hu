---
title: Az Azure File Sync telepítésének megtervezése | Microsoft dokumentumok
description: Ismerje meg, mit kell figyelembe venni az Azure Files központi telepítésének tervezésekor.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255118"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Az Azure File Sync üzembe helyezésének megtervezése
[Az Azure Files](storage-files-introduction.md) kétféleképpen telepíthető: a kiszolgáló nélküli Azure-fájlmegosztások közvetlen csatlakoztatásával vagy az Azure-fájlmegosztások helyszíni gyorsítótárazásával az Azure File Sync használatával. Melyik telepítési lehetőséget választja, módosítja azokat a dolgokat, amelyeket figyelembe kell vennie a központi telepítés megtervezésekor. 

- **Az Azure-fájlmegosztás közvetlen csatlakoztatása:** Mivel az Azure Files SMB-hozzáférést biztosít, a Windows, macOS és Linux rendszerben elérhető szabványos SMB-ügyfél használatával csatlakoztathatja az Azure-fájlmegosztásokat a helyszínen vagy a felhőben. Mivel az Azure-fájlmegosztások kiszolgáló nélküliek, az éles környezetben történő üzembe helyezéshez nincs szükség fájlkiszolgáló vagy NAS-eszköz kezelésére. Ez azt jelenti, hogy nem kell szoftverjavításokat alkalmaznia, vagy fizikai lemezeket cserélnie. 

- **Gyorsítótár az Azure-fájlmegosztás helyszíni Azure File Sync:** Azure File Sync lehetővé teszi, hogy központosítsa a szervezet fájlmegosztások az Azure Files, miközben a rugalmasság, a teljesítmény és a kompatibilitás egy helyszíni fájlkiszolgáló. Az Azure File Sync átalakítja a helyszíni (vagy felhőbeli) Windows Server egy gyors gyorsítótár az Azure-fájlmegosztás. 

Ez a cikk elsősorban az Azure File Sync központi telepítésével kapcsolatos szempontokat ismerteti. Ha meg szeretné tervezni, hogy az Azure-fájlmegosztások központi telepítését közvetlenül egy helyszíni vagy felhőbeli ügyfél csatlakoztatja, olvassa el [az Azure Files telepítésének megtervezése című témakört.](storage-files-planning.md)

## <a name="management-concepts"></a>Kezelési koncepciók
Az Azure File Sync központi telepítése három alapvető felügyeleti objektumot tartalmaz:

- **Azure-fájlmegosztás:** Az Azure-fájlmegosztás egy kiszolgáló nélküli felhőalapú fájlmegosztás, amely az Azure File Sync szinkronizálási kapcsolat *felhővégpontját* biztosítja. Az Azure-fájlmegosztásban lévő fájlok közvetlenül elérhetők az SMB-vel vagy a FileREST protokollal, bár javasoljuk, hogy elsősorban a Windows Server gyorsítótárán keresztül érje el a fájlokat, amikor az Azure fájlmegosztást az Azure File Sync használatával használja. Ennek az az oka, hogy az Azure Files ma nem rendelkezik a Windows Serverhez hasonló hatékony változásészlelési mechanizmussal, ezért az Azure-fájlmegosztás közvetlen módosításai időbe telik, amíg visszakerülnek a kiszolgáló végpontjaira.
- **Kiszolgálóvégpont**: A Windows Server azon elérési útja, amelyet egy Azure-fájlmegosztással szinkronizálnak. Ez lehet egy adott mappa egy köteten vagy a kötet gyökere. Több kiszolgálóvégpont is létezhet ugyanazon a köteten, ha a névterük nem fedi át egymást.
- **Szinkronizálási csoport**: Az objektum, amely meghatározza a szinkronizálási kapcsolatot egy **felhővégpont**, vagy az Azure-fájlmegosztás, és a kiszolgáló végpont. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. Ha például két különböző fájlkészlettel rendelkezik, amelyeket az Azure File Sync használatával szeretne kezelni, hozzon létre két szinkronizálási csoportot, és adjon hozzá különböző végpontokat az egyes szinkronizálási csoportokhoz.

### <a name="azure-file-share-management-concepts"></a>Az Azure fájlmegosztás-kezelési fogalmai
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Az Azure Fájlszinkronizálás kezelési fogalmai
A szinkronizálási csoportok a **Storage Sync Services**szolgáltatásba kerülnek, amelyek legfelső szintű objektumok, amelyek regisztrálja a kiszolgálókat az Azure File Sync használatával való használatra, és tartalmazzák a szinkronizálási csoport kapcsolatait. A Storage Sync Service erőforrás a tárfiók-erőforrás egyenrangú, és hasonlóképpen telepíthető az Azure erőforráscsoportok. A Storage Sync Service hozhat létre szinkronizálási csoportokat, amelyek tartalmazzák az Azure fájlmegosztások több tárfiókok és több regisztrált Windows-kiszolgálók között.

Mielőtt szinkronizálási csoportot hozna létre egy tárolószinkronizálási szolgáltatásban, először regisztrálnia kell egy Windows Server rendszert a Storage Sync Szolgáltatásban. Ez létrehoz egy **regisztrált kiszolgálóobjektumot,** amely a kiszolgáló vagy a fürt és a Tárolószinkronizálási szolgáltatás közötti megbízhatósági kapcsolatot jelenti. Storage Sync Service regisztrálásához először telepítenie kell az Azure File Sync ügynök a kiszolgálón. Egy adott kiszolgáló vagy fürt egyszerre csak egy tárolószinkronizálási szolgáltatással regisztrálható.

A szinkronizálási csoport tartalmaz egy felhővégpontot, vagy az Azure-fájlmegosztást, és legalább egy kiszolgálóvégpontot. A kiszolgálóvégpont-objektum tartalmazza a **felhőrétegezési képességet** konfiguráló beállításokat, amely az Azure File Sync gyorsítótárazási képességét biztosítja. Az Azure-fájlmegosztással való szinkronizáláshoz az Azure-fájlmegosztást tartalmazó tárfióknak ugyanabban az Azure-régióban kell lennie, mint a Storage Sync Service.

### <a name="management-guidance"></a>Vezetői útmutató
Az Azure File Sync telepítésekor a következőket javasoljuk:

- Az Azure-fájlmegosztások telepítése 1:1 Windows-fájlmegosztásokkal. A kiszolgálóvégpont-objektum nagyfokú rugalmasságot biztosít a szinkronizálási topológia beállításának módját a szinkronizálási kapcsolat kiszolgálói oldalán. A felügyelet egyszerűsítése érdekében a kiszolgálóvégpont elérési útját egyeztetni kell a Windows fájlmegosztás elérési útját. 

- Használja a lehető legkevesebb storage-szinkronizálási szolgáltatást. Ez egyszerűsíti a felügyeletet, ha több kiszolgálóvégpontot tartalmazó szinkronizálási csoportokkal rendelkezik, mivel a Windows Server egyszerre csak egy tárolószinkronizálási szolgáltatásba regisztrálható. 

- Az Azure-fájlmegosztások üzembe helyezésekor a tárfiók IOPS-korlátaira figyelve. Ideális esetben a fájlmegosztások 1:1 tárfiókkal, de ez nem mindig lehetséges, mivel a különböző korlátok és korlátozások, mind a szervezet és az Azure-ból. Ha nem lehetséges, hogy csak egy fájlmegosztás takarásban van telepítve, fontolja meg, hogy mely megosztások lesznek nagyon aktívak, és mely megosztások lesznek kevésbé aktívak annak érdekében, hogy a legforróbb fájlmegosztások ne kerüljön ugyanabba a tárfiókba.

## <a name="windows-file-server-considerations"></a>A Windows fájlkiszolgálóval kapcsolatos szempontok
A szinkronizálási funkció Windows Serveren való engedélyezéséhez telepítenie kell az Azure File Sync letölthető ügynökét. Az Azure File Sync ügynök két `FileSyncSvc.exe`fő összetevő: , a háttérben Windows szolgáltatás, amely felelős a változások `StorageSync.sys`figyelése a kiszolgáló végpontok és a szinkronizálási munkamenetek indítása, és egy fájlrendszer szűrő, amely lehetővé teszi a felhőrétegezés és a gyors vész-helyreállítási.  

### <a name="operating-system-requirements"></a>Operációsrendszer-követelmények
Az Azure File Sync a Windows Server következő verzióival támogatott:

| Verzió | Támogatott SKUs | Támogatott telepítési lehetőségek |
|---------|----------------|------------------------------|
| Windows Server 2019 | Adatközpont, standard és IoT | Teljes és core |
| Windows Server 2016 | Adatközpont, szabványos és tárolókiszolgáló | Teljes és core |
| Windows Server 2012 R2 | Adatközpont, szabványos és tárolókiszolgáló | Teljes és core |

A Windows Server jövőbeli verziói a kiadásukkor kerülnek hozzáadásra.

> [!Important]  
> Azt javasoljuk, hogy az Azure File Sync szolgáltatással használt összes kiszolgálót tartsa naprakészen a Windows Update legújabb frissítéseivel. 

### <a name="minimum-system-resources"></a>Minimális rendszererőforrások
Az Azure File Sync használatához fizikai vagy virtuális kiszolgálóra van szükség, amelylegalább egy processzorral és legalább 2 GiB memóriával van eljárva.

> [!Important]  
> Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.

A legtöbb éles számítási feladatok esetében nem javasoljuk, hogy az Azure File Sync sync-kiszolgáló csak a minimális követelményekkel konfiguráljon. További információt az [Ajánlott rendszererőforrások](#recommended-system-resources) című témakörben talál.

### <a name="recommended-system-resources"></a>Ajánlott rendszererőforrások
Csakúgy, mint bármely kiszolgálói szolgáltatás vagy alkalmazás, az Azure File Sync rendszererőforrás-követelményeit is a központi telepítés mérete határozza meg; a kiszolgálón a nagyobb telepítések nagyobb rendszererőforrásokat igényelnek. Az Azure File Sync, skálázás határozza meg az objektumok száma a kiszolgáló végpontok között, és a lemorzsolódás az adatkészleten. Egyetlen kiszolgáló több szinkronizálási csoportban rendelkezhet a kiszolgálóvégpontokkal, és az alábbi táblázatban felsorolt objektumok száma a kiszolgálóhoz csatolt teljes névtérhez tartozik. 

Például az A kiszolgálóvégpont 10 millió objektummal + b kiszolgálóvégpont 10 millió objektummal = 20 millió objektummal. Ebben a példában a központi telepítés, azt javasoljuk, 8 CPU-k, 16 GiB memória állandósult állapot, és (ha lehetséges) 48 GiB memória a kezdeti áttelepítés.
 
A névtér adatai teljesítménybeli okokból tárolódnak a memóriában. Emiatt a nagyobb névterek több memóriát igényelnek a jó teljesítmény fenntartásához, és több lemorzsolódáshoz több PROCESSZOR feldolgozása szükséges. 
 
A következő táblázatban a névtér méretét, valamint a tipikus általános célú fájlmegosztások kapacitásra való konvertálását is biztosítottuk, ahol az átlagos fájlméret 512 KiB. Ha a fájlméretek kisebbek, fontolja meg további memória hozzáadását ugyanahhoz a kapacitáshoz. A memóriakonfiguráció alapja a névtér mérete.

| Névtér mérete - fájlok & könyvtárak (millió)  | Jellemző kapacitás (TiB)  | CPU magok  | Ajánlott memória (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (kezdeti szinkronizálás)/ 2 (tipikus lemorzsolódás)      |
| 5        | 2.3     | 2        | 16 (kezdeti szinkronizálás)/ 4 (tipikus lemorzsolódás)    |
| 10       | 4.7     | 4        | 32 (kezdeti szinkronizálás)/ 8 (tipikus lemorzsolódás)   |
| 30       | 14.0    | 8        | 48 (kezdeti szinkronizálás)/ 16 (tipikus lemorzsolódás)   |
| 50       | 23.3    | 16       | 64 (kezdeti szinkronizálás)/ 32 (tipikus lemorzsolódás)  |
| 100*     | 46.6    | 32       | 128 (kezdeti szinkronizálás)/ 32 (tipikus lemorzsolódás)  |

\*Több mint 100 millió fájl szinkronizálása & könyvtárak jelenleg nem ajánlottak. Ez egy puha határ a tesztelt küszöbértékek alapján. További információ: [Azure Files méretezhetőség és teljesítménycélok.](storage-files-scale-targets.md#azure-file-sync-scale-targets)

> [!TIP]
> A névtér kezdeti szinkronizálása intenzív művelet, és azt javasoljuk, hogy a kezdeti szinkronizálás befejezéséig több memóriát osszon ki. Ez nem szükséges, de felgyorsíthatja a kezdeti szinkronizálást. 
> 
> A tipikus lemorzsolódás a névtér napi módosításának 0,5%-a. A magasabb szintű lemorzsolódás, fontolja meg több CPU hozzáadása. 

- NTFS fájlrendszerrel formázott, helyileg csatolt kötet.

### <a name="evaluation-cmdlet"></a>Értékelési parancsmag
Az Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszer az Azure File Sync kiértékelési parancsmag használatával. Ez a parancsmag a fájlrendszerrel és az adatkészlettel kapcsolatos lehetséges problémákat, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját keresi. Ellenőrzései az alábbiakban említett funkciók többségét, de nem mindegyikét lefedik; Javasoljuk, hogy figyelmesen olvassa el a szakasz további részét, hogy a telepítés zökkenőmentesen menjen. 

A kiértékelési parancsmag telepíthető az Az PowerShell-modul telepítésével, amely az alábbi utasításokat követve telepíthető: [Az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Használat  
A kiértékelő eszközt többféleképpen is meghívhatja: a rendszerellenőrzéseket, az adatkészlet-ellenőrzéseket vagy mindkettőt elvégezheti. A rendszer- és adatkészlet-ellenőrzések végrehajtása: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Csak az adatkészlet tesztelése:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Csak a rendszerkövetelmények tesztelése:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Az eredmények megjelenítése csv-ben:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>Fájlrendszer kompatibilitása
Az Azure File Sync csak közvetlenül csatlakoztatott NTFS-köteteken támogatott. A Windows Server közvetlen csatlakoztatott tárolója vagy DAS-ja azt jelenti, hogy a Windows Server operációs rendszer a fájlrendszer tulajdonosa. A DAS a lemezek fájlkiszolgálóhoz való fizikai csatolásával, a virtuális lemezek fájlkiszolgálóvirtuális géphez (például a Hyper-V által üzemeltetett virtuális géphez) vagy akár AZ ISCSI-n keresztül történő csatolásával biztosítható.

Csak az NTFS-kötetek támogatottak; A ReFS, FAT, FAT32 és más fájlrendszerek nem támogatottak.

Az alábbi táblázat az NTFS fájlrendszer szolgáltatásainak megosztási állapotát mutatja be: 

| Szolgáltatás | Támogatás állapota | Megjegyzések |
|---------|----------------|-------|
| Hozzáférés-vezérlési lista (ACL-ek) | Teljes mértékben támogatott | A Windows-stílusú, diszkrecionális hozzáférés-vezérlési listákat az Azure File Sync őrzi meg, és a Windows Server kényszeríti ki a kiszolgálóvégpontokon. A cl-ek is kényszeríthető, ha közvetlenül az Azure-fájlmegosztás csatlakoztatása, azonban ez további konfigurációt igényel. További információkért lásd az [Identitás szakaszt.](#identity) |
| Rögzített hivatkozások | Kimarad | |
| Szimbolikus hivatkozások | Kimarad | |
| Rögzítési pontok | Részben támogatott | A csatlakoztatási pontok lehetnek a kiszolgálóvégpont okai, de kimaradnak, ha egy kiszolgálóvégpont névterében vannak. |
| Csomópontok | Kimarad | Például elosztott fájlrendszer DfrsrPrivate és DFSRoots mappák. |
| Újraelemzési pontok | Kimarad | |
| NTFS tömörítés | Teljes mértékben támogatott | |
| Ritka fájlok | Teljes mértékben támogatott | Ritka fájlok szinkronizálása (nem blokkolja), de szinkronizálni a felhő, mint egy teljes fájlt. Ha a fájl tartalma megváltozik a felhőben (vagy egy másik kiszolgálón), a fájl már nem ritka a módosítás letöltésekor. |
| Alternatív adatfolyamok (ADS) | Megőrizve, de nincs szinkronizálva | Például a fájlbesorolási infrastruktúra által létrehozott besorolási címkék nincsenek szinkronizálva. A kiszolgáló ivégpontokon lévő fájlok meglévő besorolási címkéi érintetlenek maradnak. |

<a id="files-skipped"></a>Az Azure File Sync bizonyos ideiglenes fájlokat és rendszermappákat is kihagy:

| Fájl/mappa | Megjegyzés |
|-|-|
| pagefile.sys | A rendszerre jellemző fájl |
| Desktop.ini | A rendszerre jellemző fájl |
| Remek | Miniatűrök ideiglenes fájlja |
| ehthumbs.db | Ideiglenes fájl médiabélyegképekhez |
| ~$\*.\* | Office ideiglenes fájl |
| \*.tmp | Ideiglenes fájl |
| \*.laccdb | Adatbázis-zárolási fájl elérése|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Belső szinkronizálási fájl|
| \\Rendszerkötet-információ | Kötetre jellemző mappa |
| $RECYCLE. Bin| Mappa |
| \\SyncShareState | Mappa szinkronizáláshoz |

### <a name="failover-clustering"></a>Feladatátvételi fürtszolgáltatás
A Windows Server feladatátvételi fürtözését az Azure File Sync támogatja a "Fájlkiszolgáló általános használatra" telepítési lehetőséghez. A feladatátvevő fürtözés nem támogatott az alkalmazásadatok horizontális felskálázási fájlkiszolgálója (SOFS) vagy fürtözött megosztott köteteken.

> [!Note]  
> A szinkronizálás hoz a szinkronizáláshoz az Azure File Sync ügynök kell telepíteni a feladatátvevő fürt minden csomópontjára.

### <a name="data-deduplication"></a>Adatdeduplikáció
**Windows Server 2016 és Windows Server 2019**   
Az adatdeduplikáció a Windows Server 2016 és a Windows Server 2019 rendszerben engedélyezett felhőrétegezést tartalmazó köteteken támogatott. Az adatdeduplikáció engedélyezése a felhőrétegezés engedélyezésével engedélyezett köteteken lehetővé teszi, hogy több fájlt gyorsítótárazhat a helyszínen anélkül, hogy további tárhelyet biztosítana. 

Ha az adatdeduplikáció engedélyezve van egy olyan köteten, amelyen engedélyezve van a felhőrétegezés, a Kiszolgálóvégponton belüli Dedup-optimalizált fájlok rétegezése a felhőrétegezési házirend-beállítások alapján egy normál fájlhoz hasonlóan lesz rétegezve. A Dedup optimalizált fájljainak rétegezése után a Data Dedupplication szemétgyűjtési feladat automatikusan elindul, hogy visszaszerezzék a lemezterületet azáltal, hogy eltávolítják a felesleges adattömböket, amelyekre már nem hivatkoznak a köteten lévő más fájlok.

Megjegyzés: a kötetmegtakarítás csak a kiszolgálóra vonatkozik; az Azure-fájlmegosztásban lévő adatok nem lesznek eltéve.

> [!Note]  
> A Windows Server 2019-en engedélyezett felhőrétegezéssel rendelkező kötetek adatdeduplikációjának támogatásához telepíteni kell a [KB4520062-es](https://support.microsoft.com/help/4520062) frissítést, és az Azure File Sync agent 9.0.0.0-s vagy újabb verzióját kell telepíteni.

**Windows Server 2012 R2**  
Az Azure File Sync nem támogatja az adatdeduplikációt és a felhőrétegezést ugyanazon a köteten Windows Server 2012 R2 rendszeren. Ha az adatdeduplikáció engedélyezve van egy köteten, a felhőrétegezést le kell tiltani. 

**Megjegyzések**
- Ha az Azure File Sync ügynök telepítése előtt telepítve van az adatdeduplikáció, újraindításra van szükség az adatdeduplikáció és a felhőrétegezés támogatásához ugyanazon a köteten.
- Ha az adatdeduplikáció engedélyezve van egy köteten a felhőrétegezés engedélyezése után, a kezdeti Deduplikációs optimalizálási feladat optimalizálja a köteten lévő, még nem rétegzett fájlokat, és a következő hatással lesz a felhőrétegezésre:
    - A szabad területházirend továbbra is rétegezi a fájlokat a köteten lévő szabad terület nek megfelelően a hőtérkép használatával.
    - A dátumházirend kihagyja azoknak a fájloknak a rétegezését, amelyek egyébként jogosultak voltak a rétegezésre a deduplikációs optimalizálási feladat nak a fájlok elérése miatt.
- A folyamatban lévő deduplikációs optimalizálási feladatok esetén a dátumházirenddel rendelkező felhőrétegezés késlelteti az Adatdeduplikációs [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) beállítást, ha a fájl még nincs rétegezve. 
    - Példa: Ha a MinimumFileAgeDays beállítás hét nap, a felhőrétegezési dátumházirend pedig 30 nap, a dátumházirend 37 nap után rétegezi a fájlokat.
    - Megjegyzés: Miután egy fájlt rétegzett az Azure File Sync, a deduplikációs optimalizálási feladat kihagyja a fájlt.
- Ha egy Windows Server 2012 R2 rendszert futtató, az Azure File Sync ügynökkel telepített kiszolgáló továbbfejlesztett Windows Server 2016 vagy Windows Server 2019 rendszerre, a következő lépéseket kell végrehajtani az adatdeduplikáció és a felhőrétegezés támogatására ugyanazon a köteten:  
    - Távolítsa el a Windows Server 2012 R2 Azure File Sync ügynökét, és indítsa újra a kiszolgálót.
    - Töltse le az Azure File Sync ügynököt az új kiszolgálói operációs rendszer (Windows Server 2016 vagy Windows Server 2019) verziójához.
    - Telepítse az Azure File Sync ügynök, és indítsa újra a kiszolgálót.  
    
    Megjegyzés: Az Azure File Sync konfigurációs beállításai a kiszolgálón megmaradnak, amikor az ügynök eltávolítása és újratelepítése.

### <a name="distributed-file-system-dfs"></a>Elosztott fájlrendszer (DFS)
Az Azure File Sync támogatja az interop dfs-névterek (DFS-N) és az elosztott fájlrendszer replikációs szolgáltatás (DFS-R).

**DFS-névterek (DFS-N)**: Az Azure File Sync teljes mértékben támogatott dfs-N kiszolgálókon. Az Azure File Sync ügynök egy vagy több DFS-N tagok adatok szinkronizálásához a kiszolgáló végpontok és a felhővégpontok közötti adatok szinkronizálásához. További információt az [ELOSZTOTT fájlrendszerbeli névterek – áttekintés című témakörben talál.](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)
 
**DFS-replikációs szolgáltatás (DFS-R)**: Mivel a DFS-R és az Azure File Sync egyaránt replikációs megoldások, a legtöbb esetben azt javasoljuk, hogy cserélje le a DFS-R-t az Azure File Sync szolgáltatásra. Vannak azonban több forgatókönyv, ahol szeretné használni DFS-R és az Azure File Sync együtt:

- Dfs-R-telepítésről egy Azure File Sync-üzembe helyezésre telepedik át. További információt az [Elosztott fájlrendszer replikációs szolgáltatásának (DFS-R) telepítése az Azure File Sync szolgáltatásba című témakörben](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)talál.
- Nem minden helyszíni kiszolgáló, amelynek szüksége van egy másolatot a fájladatok közvetlenül csatlakoztatható az internethez.
- A fiókkiszolgálók egyetlen központi kiszolgálóra konszolidálják az adatokat, amelyhez az Azure File Sync-et szeretné használni.

Az Azure File Sync és dfs-R egymás mellett:

1. Az Azure File Sync felhőrétegezésle kell tiltani a kötetek DFS-R replikált mappákat.
2. A kiszolgálóvégpontokat nem szabad csak olvasható DFS-R replikációs mappákban konfigurálni.

További információt az [Elosztott fájlrendszer replikációs szolgáltatásának áttekintése című témakörben talál.](https://technet.microsoft.com/library/jj127250)

### <a name="sysprep"></a>Sysprep
A sysprep használata olyan kiszolgálón, amelyen telepítve van az Azure File Sync ügynök, nem támogatott, és nem várt eredményekhez vezethet. Az ügynök telepítésének és a kiszolgáló regisztrációjának a kiszolgálólemezkép telepítése és a sysprep minitelepítő befejezése után kell történnie.

### <a name="windows-search"></a>Windows Search
Ha a felhőrétegezés engedélyezve van egy kiszolgálói végponton, a rendszer kihagyja a rétegzett fájlokat, és nem indexeli őket a Windows Search szolgáltatásban. A nem rétegzett fájlok indexelése megfelelően van elévülve.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Egyéb hierarchikus tárolási felügyeleti (HSM) megoldások
Az Azure File Sync szolgáltatással más HSM-megoldások nem használhatók.

## <a name="identity"></a>Identitás
Az Azure File Sync a szabványos AD-alapú identitással működik, a szinkronizálás beállításán túl minden speciális beállítás nélkül. Az Azure File Sync használata esetén az általános elvárás az, hogy a legtöbb hozzáférés az Azure File Sync gyorsítótárazási kiszolgálókon keresztül, nem pedig az Azure fájlmegosztáson keresztül történik. Mivel a kiszolgálóvégpontok a Windows Server rendszerben találhatók, és a Windows Server már nagyon régóta támogatja az AD és Windows stílusú ANC-ket, semmire nincs szükség azon túl, hogy a Storage Sync Szolgáltatásban regisztrált Windows fájlkiszolgálók tartományhoz csatlakozzanak. Az Azure File Sync tárolja az ACL-eket az Azure-fájlmegosztásban lévő fájlokon, és replikálja őket az összes kiszolgálói végpontra.

Annak ellenére, hogy a közvetlenül az Azure fájlmegosztáson végrehajtott módosítások szinkronizálása hosszabb időt vesz igénybe a szinkronizálási csoport kiszolgálói végpontjaival való szinkronizáláshoz, érdemes lehet biztosítani, hogy az AD-engedélyeket közvetlenül a felhőben is érvényesíthesse a fájlmegosztáson. Ehhez tartományban kell csatlakoznia a tárfiókhoz a helyszíni AD-hez, csakúgy, mint a Windows fájlkiszolgálók tartományhoz való csatlakozása. Ha többet szeretne tudni arról, hogy mi ként csatlakozik a tárfiókhoz egy ügyfél tulajdonában lévő Active Directoryhoz, olvassa el az [Azure Files Active Directory áttekintéscímű témakört.](storage-files-active-directory-overview.md)

> [!Important]  
> Az Azure File Sync sikeres üzembe helyezéséhez nincs szükség a tárfiókhoz való csatlakozáshoz. Ez egy szigorúan opcionális lépés, amely lehetővé teszi, hogy az Azure fájlmegosztás a helyszíni ACL-ek kényszerítése, amikor a felhasználók közvetlenül csatlakoztatják az Azure-fájlmegosztást.

## <a name="networking"></a>Hálózat
Az Azure File Sync ügynök kommunikál a Storage Sync Service és az Azure fájlmegosztás az Azure File Sync REST protokoll és a FileREST protokoll, amelyek mindegyike mindig https-porton 443 használatával. Az SMB-t soha nem használják adatok feltöltésére vagy letöltésére a Windows Server és az Azure-fájlmegosztás között. Mivel a legtöbb szervezet engedélyezi a HTTPS-forgalmat a 443-as porton keresztül, a legtöbb webhely felkereséséhez szükséges követelményként speciális hálózati konfiguráció általában nem szükséges az Azure File Sync üzembe helyezéséhez.

A szervezet szabályzata vagy egyedi szabályozási követelményei alapján szükség lehet szigorúbb kommunikációra az Azure-ral, ezért az Azure File Sync számos mechanizmust biztosít a hálózatkezelés konfigurálásához. Az Ön igényei nek megfelelően a következőket teheti:

- Bújtatási szinkronizálás és fájlfeltöltési/-letöltési forgalom az ExpressRoute vagy az Azure VPN-en keresztül. 
- Azure Files és az Azure Networking funkciók, például a szolgáltatás végpontok és a privát végpontok.
- Konfigurálja az Azure File Sync-et a proxy támogatásához a környezetben.
- Hálózati tevékenység szabályozása az Azure File Sync szolgáltatásból.

Az Azure File Sync hálózati funkcióinak konfigurálásáról a következő témakörben olvashat bővebben:
- [Az Azure File Sync proxy- és tűzfalbeállításai](storage-sync-files-firewall-and-proxy.md)
- [Annak biztosítása, hogy az Azure File Sync jó szomszéd legyen az adatközpontban](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Titkosítás
Az Azure File Sync használatakor három különböző titkosítási réteget kell figyelembe venni: titkosítás a Windows Server nyugalmi tárolásán, titkosítás az Azure File Sync ügynök és az Azure közötti átvitel során, és titkosítás az Azure-fájlmegosztásban lévő adatok többi részében. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server titkosítás inaktív állapotban 
A Windows Serveren lévő adatok titkosítására két olyan stratégia létezik, amelyek általában az Azure File Sync szolgáltatással működnek: titkosítás a fájlrendszer alatt, hogy a fájlrendszer és a hozzá írt összes adat titkosítva legyen, és a titkosítás magában a fájlformátumban. Ezek a módszerek nem zárják ki egymást; szükség esetén együtt is használhatók, mivel a titkosítás célja eltérő.

A fájlrendszer alatti titkosítás biztosításához a Windows Server biztosítja a BitLocker beérkezett üzenetek mappáját. A BitLocker teljesen átlátszó az Azure File Sync számára. A bitlockerhez hasonló titkosítási mechanizmus használatának elsődleges oka az adatok fizikai kiszivárgásának megakadályozása a helyszíni adatközpontból azáltal, hogy valaki ellopja a lemezeket, és megakadályozza, hogy egy jogosulatlan operációs rendszer jogosulatlan olvasási/írási műveleteket hajtson végre az adatokhoz. Ha többet szeretne tudni a BitLocker-ről, olvassa el a [BitLocker áttekintése című témakört.](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)

A BitLocker-hez hasonlóan működő harmadik féltől származó termékeknek az NTFS-kötet alatt kell működniük, hasonlóan kell működniük az Azure File Sync szolgáltatással. 

Az adatok titkosításának másik fő módja a fájl adatfolyamának titkosítása, amikor az alkalmazás menti a fájlt. Egyes alkalmazások ezt natívmódon tehetik meg, de ez általában nem így van. A fájl adatfolyamának titkosítására szolgáló módszer például az Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Az AIP/RMS-hez hasonló titkosítási mechanizmus használatának elsődleges oka az, hogy megakadályozza az adatok kiszivárgását a fájlmegosztásból olyan személyek által, akik más helyekre, például flash meghajtóra másolják, vagy e-mailben küldik el egy jogosulatlan személynek. Ha egy fájl adatfolyama a fájlformátum részeként titkosítva van, ez a fájl továbbra is titkosítva lesz az Azure fájlmegosztáson. 

Az Azure File Sync nem működik együtt az NTFS titkosított fájlrendszerrel (NTFS EFS) vagy a fájlrendszer felett, de a fájl adatfolyama alatt található harmadik fél től származó titkosítási megoldásokkal. 

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Az Azure File Sync ügynök kommunikál a Storage Sync Service és az Azure fájlmegosztás az Azure File Sync REST protokoll és a FileREST protokoll, amelyek mindegyike mindig https-porton 443 használatával. Az Azure File Sync nem küld titkosítatlan kérelmeket HTTP-n keresztül. 

Az Azure storage-fiókok tartalmaznak egy kapcsolót az átvitel során a titkosítás megköveteléséhez, amely alapértelmezés szerint engedélyezve van. Még akkor is, ha a rendszerváltás a tárfiók szintjén le van tiltva, ami azt jelenti, hogy az Azure-fájlmegosztásokhoz való titkosítatlan kapcsolatok lehetségesek, az Azure File Sync továbbra is csak titkosított csatornákat fog használni a fájlmegosztás eléréséhez.

A tárfiók átvitele során a titkosítás letiltásának elsődleges oka egy régebbi operációs rendszeren, például Windows Server 2008 R2 vagy régebbi Linux-disztribúción futtatandó örökölt alkalmazás támogatása, közvetlenül egy Azure-fájlmegosztással való kapcsolat. Ha az örökölt alkalmazás a fájlmegosztás Windows Server gyorsítótárához beszél, a beállítás váltása nem lesz hatással. 

Javasoljuk, hogy biztosítsa az adatok titkosításának engedélyezését az átvitel közbeni átvitel alatt.

Az átvitel közbeni titkosításról további információt az [Azure storage-beli biztonságos átvitel megkövetelése című témakörben talál.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-file-share-encryption-at-rest"></a>Az Azure fájlmegosztás titkosítása inaktív
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Tárolási rétegek
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>A szabványos fájlmegosztások akár 100 TiB-ig való lehetővé tétele
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Régiónkénti rendelkezésre állás
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Az Azure fájlszinkronizálási régió elérhetősége
Az Azure File Sync a következő régiókban érhető el:

| Azure-felhő | Földrajzi régió | Azure-régió | Régiókód |
|-------------|-------------------|--------------|-------------|
| Nyilvános | Ázsia | Kelet-Ázsia | `eastasia` |
| Nyilvános | Ázsia | Délkelet-Ázsia | `southeastasia` |
| Nyilvános | Ausztrália | Kelet-Ausztrália | `australiaeast` |
| Nyilvános | Ausztrália | Délkelet-Ausztrália | `australiasoutheast` |
| Nyilvános | Brazília | Dél-Brazília | `brazilsouth` |
| Nyilvános | Kanada | Közép-Kanada | `canadacentral` |
| Nyilvános | Kanada | Kelet-Kanada | `canadaeast` |
| Nyilvános | Európa | Észak-Európa | `northeurope` |
| Nyilvános | Európa | Nyugat-Európa | `westeurope` |
| Nyilvános | Franciaország | Közép-Franciaország | `francecentral` |
| Nyilvános | Franciaország | Franciaország Dél* | `francesouth` |
| Nyilvános | India | Közép-India | `centralindia` |
| Nyilvános | India | Dél-India | `southindia` |
| Nyilvános | Japán | Kelet-Japán | `japaneast` |
| Nyilvános | Japán | Nyugat-Japán | `japanwest` |
| Nyilvános | Dél-Korea | Dél-Korea középső régiója | `koreacentral` |
| Nyilvános | Dél-Korea | Dél-Korea déli régiója | `koreasouth` |
| Nyilvános | Dél-afrikai Köztársaság | Dél-Afrika Észak-Afrika | `southafricanorth` |
| Nyilvános | Dél-afrikai Köztársaság | Dél-Afrika Nyugat* | `southafricawest` |
| Nyilvános | EAE | Egyesült Arab Emírségek központi* | `uaecentral` |
| Nyilvános | EAE | Egyesült Arab Emírségek északi | `uaenorth` |
| Nyilvános | Egyesült Királyság | Az Egyesült Királyság déli régiója | `uksouth` |
| Nyilvános | Egyesült Királyság | Az Egyesült Királyság nyugati régiója | `ukwest` |
| Nyilvános | USA | USA középső régiója | `centralus` |
| Nyilvános | USA | USA keleti régiója | `eastus` |
| Nyilvános | USA | USA 2. keleti régiója | `eastus2` |
| Nyilvános | USA | USA északi középső régiója | `northcentralus` |
| Nyilvános | USA | USA déli középső régiója | `southcentralus` |
| Nyilvános | USA | USA nyugati középső régiója | `westcentralus` |
| Nyilvános | USA | USA nyugati régiója | `westus` |
| Nyilvános | USA | USA nyugati régiója, 2. | `westus2` |
| US Gov | USA | USA-beli államigazgatás – Arizona | `usgovarizona` |
| US Gov | USA | USA-beli államigazgatás – Texas | `usgovtexas` |
| US Gov | USA | USA-beli államigazgatás – Virginia | `usgovvirginia` |

Az Azure File Sync csak olyan Azure-fájlmegosztással támogatja a szinkronizálást, amely ugyanabban a régióban található, mint a Storage Sync Service.

A csillagokmegjelöléssel megjelölt régiók esetében kapcsolatba kell lépnie az Azure-támogatással, hogy hozzáférést kérjen az Azure Storage-hoz ezekben a régiókban. A folyamatot ez a [dokumentum](https://azure.microsoft.com/global-infrastructure/geographies/)ismerteti.

## <a name="redundancy"></a>Redundancia
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Georedundáns és geozóna redundáns tárolás a képesség, hogy manuálisan feladatátvételi tárolás a másodlagos régióba. Azt javasoljuk, hogy ne tegye ezt a katasztrófa kívül, ha az Azure File Sync használata esetén az adatvesztés nagyobb valószínűsége miatt. Abban az esetben, ha egy katasztrófa, ahol szeretne kezdeményezni egy manuális feladatátvétela a tárolás, meg kell nyitnia egy támogatási eset a Microsoft, hogy az Azure File Sync a másodlagos végpontdal való szinkronizálás folytatásához.

## <a name="migration"></a>Migrálás
Ha meglévő Windows-fájlkiszolgálóval rendelkezik, az Azure File Sync közvetlenül telepíthető a helyére anélkül, hogy az adatokat át kellene helyeznie egy új kiszolgálóra. Ha az Azure File Sync bevezetésének részeként egy új Windows-fájlkiszolgálóra szeretne áttérni, az adatok áthelyezésének számos lehetséges módszere lehetséges:

- Hozzon létre kiszolgálóvégpontokat a régi fájlmegosztáshoz és az új fájlmegosztáshoz, és hagyja, hogy az Azure File Sync szinkronizálja az adatokat a kiszolgáló végpontjai között. Ennek a megközelítésnek az az előnye, hogy nagyon egyszerűvé teszi a tárhely túljegyzését az új fájlkiszolgálón, mivel az Azure File Sync felhőrétegezést is figyelembe veszi. Ha készen áll, átvághatja a végfelhasználókat az új kiszolgálón lévő fájlmegosztásra, és eltávolíthatja a régi fájlmegosztás kiszolgálói végpontját.

- Csak az új fájlkiszolgálón hozzon létre kiszolgálói végpontot, `robocopy`és másolja az adatokat a régi fájlmegosztásba a használatával. Attól függően, hogy a topológia a fájlmegosztások az új kiszolgálón (hány megosztásvan az egyes köteteken, mennyire szabad `robocopy` az egyes kötetek, stb), előfordulhat, hogy ideiglenesen további tárhelyet, mivel várható, hogy a régi kiszolgálóról az új kiszolgálóra a helyszíni adatközpontban gyorsabban befejeződik, mint az Azure File Sync helyezi át az adatokat az Azure-ba.

A Data Box használatával is áttelepítheti az adatokat egy Azure File Sync központi telepítésbe. Az esetek többségében, amikor az ügyfelek szeretnék használni data box adatok betöltéséhez, akkor ezt azért, mert úgy gondolják, hogy növeli a sebességet a központi telepítés, vagy azért, mert segít a korlátozott sávszélesség forgatókönyvek. Bár igaz, hogy egy adatmező használatával adatok at az Azure File Sync üzembe helyezés csökkenti a sávszélesség-kihasználtság, valószínűleg gyorsabb lesz a legtöbb esetben, hogy folytassa az online adatfeltöltés a fent leírt módszerek egyikén keresztül. Ha többet szeretne tudni arról, hogy miként használhatja a Data Box segítségével az adatok at az Azure File Sync üzembe helyezésébe, olvassa el az [Adatok áttelepítése az Azure-fájlszinkronizálás az Azure Data boxba (Adatok áttelepítése az Azure Data boxkal) témakört.](storage-sync-offline-data-transfer.md)

Az ügyfelek gyakori hiba, amikor az adatok áttelepítése az új Azure File Sync központi telepítés az adatok másolása közvetlenül az Azure-fájlmegosztás, nem pedig a Windows fájlkiszolgálókon. Bár az Azure File Sync azonosítja az összes új fájlt az Azure-fájlmegosztáson, és szinkronizálja őket a Windows fájlmegosztások, ez általában lényegesen lassabb, mint az adatok betöltése a Windows fájlkiszolgálón keresztül. Számos Azure-másolási eszköz, például az AzCopy, rendelkezik azzal a további hátránya, hogy nem másolja a fájl összes fontos metaadatait, például az időbélyegeket és az ACL-eket.

## <a name="antivirus"></a>Vírusvédelem
Mivel a víruskereső úgy működik, hogy a fájlokat ismert kártékony kódokat keres, egy víruskereső termék a rétegzett fájlok visszahívását okozhatja. Az Azure File Sync ügynök 4.0-s és újabb verzióiban a rétegzett fájlok a biztonságos Windows attribútumFILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS beállítva. Javasoljuk, hogy konzultáljon a szoftver gyártójával, hogy megtudja, hogyan konfigurálhatja a megoldást úgy, hogy kihagyja a fájlok olvasását ezzel az attribútumkészlettel (sokan automatikusan csinálják). 

A Microsoft belső vírusvédelmi megoldásai, a Windows Defender és a System Center Endpoint Protection (SCEP) automatikusan kihagyják az ezen attribútummal rendelkező fájlok olvasását. Teszteltük őket, és azonosítottunk egy kisebb problémát: amikor egy kiszolgálót hozzáad egy meglévő szinkronizálási csoporthoz, a 800 bájtnál kisebb fájlokat a rendszer visszahívta (letöltötte) az új kiszolgálón. Ezek a fájlok az új kiszolgálón maradnak, és nem lesznek rétegezve, mivel nem felelnek meg a rétegezési méretkövetelménynek (>64kb).

> [!Note]  
> A víruskereső gyártók ellenőrizhetik a termék és az Azure File Sync közötti kompatibilitást az [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322)segítségével, amely letölthető a Microsoft letöltőközpontjából.

## <a name="backup"></a>Backup 
A víruskereső megoldásokhoz hasonlóan a biztonsági mentési megoldások is a rétegzett fájlok visszahívását okozhatják. Javasoljuk, hogy egy felhőalapú biztonsági mentési megoldás biztonsági másolatot készíteni az Azure fájlmegosztás helyett egy helyszíni biztonsági mentési termék.

Ha helyszíni biztonsági mentési megoldást használ, a biztonsági mentéseket a felhőrétegezést letiltó szinkronizálási csoport kiszolgálóján kell végrehajtani. Visszaállítás végrehajtásakor használja a kötet- vagy fájlszintű visszaállítási beállításokat. A fájlszintű visszaállítási beállítással visszaállított fájlok szinkronizálódnak a szinkronizálási csoport összes végpontjával, és a meglévő fájlok at a biztonsági mentésből visszaállíttatja a verzió.  A kötetszintű visszaállítások nem helyettesítik az újabb fájlverziókat az Azure-fájlmegosztásban vagy más kiszolgálói végpontokban.

> [!Note]  
> A bare-metal (BMR) visszaállítása váratlan eredményeket okozhat, és jelenleg nem támogatott.

> [!Note]  
> Az Azure File Sync ügynök 9-es verziójával a VSS-pillanatképek (beleértve a Korábbi verziók lapot is) mostantól támogatottak azokon a köteteken, amelyeken engedélyezve van a felhőrétegezés. Azonban engedélyeznie kell a korábbi verziókompatibilitást a PowerShellen keresztül. [További tudnivalókat itt talál](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>További lépések
* [Fontolja meg a tűzfal- és proxybeállításokat](storage-sync-files-firewall-and-proxy.md)
* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [Az Azure Files üzembe helyezése](storage-files-deployment-guide.md)
* [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
* [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)