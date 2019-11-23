---
title: Azure File Sync központi telepítésének tervezése | Microsoft Docs
description: Megtudhatja, mit érdemes figyelembe venni Azure Files központi telepítés tervezésekor.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3c70d2086fc5866b07c31966343397f8ccc809be
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73846733"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Az Azure File Sync üzembe helyezésének megtervezése
A Azure File Sync segítségével központilag kezelheti a szervezete fájlmegosztást Azure Filesban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A Windows Serveren elérhető bármely protokoll használatával helyileg férhet hozzá az adataihoz, beleértve az SMB-t, az NFS-t és a FTPS is. Tetszőleges számú gyorsítótárral rendelkezhet a világ minden tájáról.

Ez a cikk Azure File Sync központi telepítésének fontos szempontjait ismerteti. Javasoljuk, hogy olvassa el [a Azure Files központi telepítésének megtervezését](storage-files-planning.md)is. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure File Sync terminológia
Mielőtt beolvassa a Azure File Sync központi telepítés tervezésének részleteit, fontos megérteni a terminológiát.

### <a name="storage-sync-service"></a>Storage Sync szolgáltatás
A Storage Sync szolgáltatás a legfelső szintű Azure-erőforrás a Azure File Sync számára. A Storage Sync szolgáltatás erőforrása a Storage-fiók erőforrásának egyik társa, és hasonló módon telepíthető Azure-erőforráscsoportok számára is. A Storage-fiók erőforrásának külön legfelső szintű erőforrására van szükség, mert a Storage Sync szolgáltatás több szinkronizálási csoporton keresztül több Storage-fiókkal is létrehozhat szinkronizálási kapcsolatokat. Egy előfizetés több Storage Sync Service-erőforrást is üzembe helyezhet.

### <a name="sync-group"></a>Szinkronizálási csoport
A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. Ha például két különálló fájllal szeretne felügyelni Azure File Sync, akkor két szinkronizálási csoportot kell létrehoznia, és különböző végpontokat kell hozzáadnia az egyes szinkronizálási csoportokhoz. A Storage Sync szolgáltatás tetszőleges számú szinkronizálási csoportot képes tárolni.  

### <a name="registered-server"></a>Regisztrált kiszolgáló
A regisztrált kiszolgáló objektum megbízhatósági kapcsolatot képvisel a kiszolgáló (vagy a fürt) és a Storage Sync szolgáltatás között. Tetszőleges számú kiszolgáló regisztrálható egy Storage Sync Service-példányhoz. A kiszolgálók (vagy fürtök) azonban egyszerre csak egy Storage Sync szolgáltatással regisztrálhatók.

### <a name="azure-file-sync-agent"></a>Azure File Sync ügynök
Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással. A Azure File Sync ügynöknek három fő összetevője van: 
- **FileSyncSvc. exe**: a háttérben futó Windows-szolgáltatás, amely a kiszolgálók végpontjai változásainak figyelésére, valamint a szinkronizálási munkamenetek Azure-ba való indítására felelős.
- **StorageSync. sys**: a Azure file Sync fájlrendszer-szűrő, amely a fájlok Azure Files (a felhőalapú rétegek engedélyezése esetén) felelős.
- **PowerShell-kezelési parancsmagok**: a Microsoft. StorageSync Azure erőforrás-szolgáltatóval való interakcióhoz használt PowerShell-parancsmagok. Ezeket a következő (alapértelmezett) helyeken találja:
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Kiszolgálói végpont
A kiszolgálói végpont a regisztrált kiszolgálón egy konkrét helyet jelöl, például egy mappát egy kiszolgálói köteten. Ugyanazon a köteten több kiszolgálói végpont is létezhet, ha a névterek nem fedik át egymást (például `F:\sync1` és `F:\sync2`). A felhő-előállítók házirendjeit egyenként is konfigurálhatja az egyes kiszolgálói végpontokhoz. 

Csatlakoztatási pont használatával létrehozhat egy kiszolgálói végpontot. Vegye figyelembe, hogy a kiszolgálói végponton belüli csatolási kimaradnak.  

A rendszerköteten létrehozhat egy kiszolgálói végpontot, de a következő két korlátozás létezik:
* A felhő-rétegek nem engedélyezhetők.
* Gyors névtér-visszaállítás (ahol a rendszer gyorsan leállítja a teljes névteret, és elkezdi a tartalom felidézését).


> [!Note]  
> Csak a nem cserélhető kötetek támogatottak.  A távoli megosztásból leképezett meghajtók nem támogatottak a kiszolgálói végpont elérési útján.  Emellett előfordulhat, hogy a kiszolgálói végpont a Windows rendszerköteten található, bár a rendszerköteten nem támogatott a felhőalapú rétegek használata.

Ha olyan kiszolgálói helyet ad hozzá, amely egy meglévő fájl-készlettel rendelkezik egy szinkronizálási csoportnak, akkor ezek a fájlok minden más olyan fájllal egyesülnek, amely már a szinkronizálási csoport többi végpontján található.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
A Felhőbeli végpont egy olyan Azure-fájlmegosztás, amely egy szinkronizálási csoport részét képezi. A teljes Azure-fájlmegosztás szinkronizálása és egy Azure-fájlmegosztás csak egy Felhőbeli végpont tagja lehet. Az Azure-fájlmegosztás ezért csak egy szinkronizálási csoport tagja lehet. Ha olyan Azure-fájlmegosztást ad hozzá, amely egy meglévő fájlokkal rendelkezik egy szinkronizálási csoportba, a meglévő fájlok egyesítése a szinkronizálási csoport többi végpontján már más fájlokkal történik.

> [!Important]  
> Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A változás-észlelési feladatok csak 24 óránként egyszer indíthatók el egy Felhőbeli végponton. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként. További információ: [Azure Files gyakori kérdések](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Felhőbeli rétegzés 
A felhőalapú rétegek a Azure File Sync választható funkciója, amelyekben a gyakran használt fájlok a kiszolgálón helyileg vannak gyorsítótárazva, míg az összes többi fájl a házirend-beállítások alapján Azure Files. További információ: a [felhőalapú rétegek ismertetése](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure File Sync rendszerkövetelmények és együttműködési képesség 
Ez a szakasz Azure File Sync ügynök rendszerkövetelményeit, valamint a Windows Server-funkciókkal és-szerepkörökkel, valamint a harmadik féltől származó megoldásokkal való együttműködésre vonatkozik.

### <a name="evaluation-cmdlet"></a>Próbaverziós parancsmag
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós parancsmag használatával. Ez a parancsmag ellenőrzi a fájlrendszerrel és az adatkészlettel kapcsolatos lehetséges problémákat, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. Vegye figyelembe, hogy az ellenőrzések a legtöbbet, de nem az összes alább említett funkciót lefedik. Javasoljuk, hogy alaposan olvassa el a szakasz további részeit, és győződjön meg arról, hogy az üzembe helyezés zökkenőmentesen működik. 

A kiértékelési parancsmag az az PowerShell-modul telepítésével telepíthető, amely az alábbi utasításokat követve telepíthető: [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Használat  
A kiértékelési eszközt többféleképpen is meghívhatja: elvégezheti a rendszer-ellenőrzéseket, az adatkészlet-ellenőrzéseket vagy mindkettőt. A rendszerek és az adatkészlet ellenőrzésének elvégzéséhez tegye a következőket: 

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
 
Az eredmények CSV-ben való megjelenítéséhez:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Rendszerkövetelmények
- A következő operációsrendszer-verziók egyikét futtató kiszolgáló:

    | Verzió | Támogatott SKU-i | Támogatott üzembe helyezési lehetőségek |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter és standard | Teljes és alapvető |
    | Windows Server 2016 | Datacenter és standard | Teljes és alapvető |
    | Windows Server 2012 R2 | Datacenter és standard | Teljes és alapvető |
    | Windows Server IoT 2019 a Storage-hoz| Datacenter és standard | Teljes és alapvető |
    | Windows Storage Server 2016| Datacenter és standard | Teljes és alapvető |
    | Windows Storage Server 2012 R2| Datacenter és standard | Teljes és alapvető |

    A rendszer a Windows Server jövőbeli verzióit adja hozzá a kiadásuk után.

    > [!Important]  
    > Azt javasoljuk, hogy az összes olyan kiszolgálót, amelyet a Azure File Sync naprakészen használ, Windows Update legújabb frissítéseivel. 

- Legalább 2 GiB memóriát tartalmazó kiszolgáló.

    > [!Important]  
    > Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
    
- Az NTFS fájlrendszerrel formázott, helyileg csatolt kötet.

### <a name="file-system-features"></a>Fájlrendszer-funkciók

| Funkció | Támogatási állapot | Megjegyzések |
|---------|----------------|-------|
| Hozzáférés-vezérlési listák (ACL-ek) | Teljes mértékben támogatott | A Windows ACL-eket a Azure File Sync megőrzi, és a Windows Server a kiszolgálói végpontokon kényszeríti ki. A Windows ACL-ek (még) a Azure Files által támogatottak, ha a fájlok közvetlenül a felhőben érhetők el. |
| Rögzített hivatkozások | Kimarad | |
| Szimbolikus hivatkozások | Kimarad | |
| Csatlakoztatási pontok | Részlegesen támogatott | A csatlakoztatási pontok lehetnek a kiszolgálói végpontok gyökeré, de a rendszer kihagyja őket, ha egy kiszolgálói végpont névterében szerepelnek. |
| Csomópontok | Kimarad | Például elosztott fájlrendszer DfrsrPrivate és DFSRoots mappákat. |
| Újraelemzési pontok | Kimarad | |
| NTFS-tömörítés | Teljes mértékben támogatott | |
| Ritka fájlok | Teljes mértékben támogatott | A ritka fájlok szinkronizálása (nincs letiltva), de teljes fájlként szinkronizálja őket a felhőbe. Ha a fájl tartalma megváltozik a felhőben (vagy egy másik kiszolgálón), a fájl már nem ritka, amikor a módosítás le van töltve. |
| Alternatív adatfolyamok (ADS) | Megőrzött, de nincs szinkronizálva | A fájl besorolási infrastruktúrája által létrehozott besorolási címkék például nem szinkronizálhatók. Az egyes kiszolgálói végpontokon található fájlokra vonatkozó meglévő besorolási címkék érintetlenek maradnak. |

> [!Note]  
> Csak NTFS-kötetek támogatottak. A ReFS, a FAT, a FAT32 és más fájlrendszerek nem támogatottak.

### <a name="files-skipped"></a>Kihagyott fájlok

| Fájl/mappa | Megjegyzés |
|-|-|
| Desktop. ini | A rendszerfájlra vonatkozó fájl |
| ethumbs.db$ | Ideiglenes fájl a miniatűrökhöz |
| ~$\*.\* | Ideiglenes Office-fájl |
| \*. tmp | Ideiglenes fájl |
| \*.laccdb | ADATBÁZIS-zárolási fájl elérése|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Belső szinkronizáló fájl|
| rendszerkötet-információk \\ | A kötethez tartozó mappa |
| $RECYCLE. BIN| Mappa |
| \\SyncShareState | A szinkronizáláshoz használt mappa |

### <a name="failover-clustering"></a>Feladatátvételi fürtszolgáltatás
A Windows Server feladatátvételi fürtszolgáltatást Azure File Sync támogatja a "fájlkiszolgáló az általános használatra" telepítési lehetőség. A feladatátvételi fürtszolgáltatás nem támogatott az alkalmazásadatok Kibővíthető fájlkiszolgáló (SOFS) vagy fürtözött megosztott köteteken (CSV).

> [!Note]  
> A Azure File Sync ügynöknek telepítve kell lennie a feladatátvevő fürt minden csomópontján a szinkronizálás megfelelő működéséhez.

### <a name="data-deduplication"></a>Az deduplikálás
**Windows server 2016 és Windows server 2019**   
Az deduplikálás olyan köteteken támogatott, amelyeken engedélyezve van a felhőalapú rétegek használata a Windows Server 2016 rendszeren. Ha engedélyezi az deduplikálás szolgáltatást egy olyan köteten, amelyen engedélyezve van a felhőalapú réteg, a további tárhelyek kiépítésének hiányában további fájlokat is gyorsítótárazhat a helyszínen. 

Ha az adatok deduplikálása engedélyezve van egy olyan köteten, amelyen engedélyezve van a felhőalapú rétegek használata, a deduplikáció optimalizált fájlokat a rendszer a felhőre vonatkozó házirend-beállítások alapján a normál fájlhoz hasonló módon fogja összeállítani. Ha a deduplikáció optimalizált fájlok lettek kiválasztva, az adatok deduplikálása adatgyűjtési feladatsor automatikusan lefuttatja a lemezterület felszabadítását a köteten lévő más fájlok által már nem hivatkozott felesleges adattömbök eltávolításával.

Vegye figyelembe, hogy a mennyiségi megtakarítás csak a kiszolgálón érvényes. Az Azure-fájlmegosztás adatait nem törli a rendszer.

> [!Note]  
> Az deduplikálás és a felhőalapú rétegek jelenleg nem támogatottak ugyanazon a köteten a 2019-as kiszolgálón, mert egy jövőbeli frissítésben kijavított hiba történik.

**Windows Server 2012 R2**  
A Azure file Sync nem támogatja az deduplikálás és a felhőalapú rétegek használatát ugyanarra a kötetre a Windows Server 2012 R2 rendszeren. Ha egy köteten engedélyezve van az deduplikálás, a Felhőbeli rétegek letiltását le kell tiltani. 

**Megjegyzések**
- Ha a Azure file Sync ügynök telepítése előtt telepíti az deduplikálás szolgáltatást, a rendszer újraindítást igényel az deduplikálás és a felhőalapú rétegek egyazon köteten történő támogatásához.
- Ha az adatok deduplikálása engedélyezve van egy köteten a felhőalapú rétegek engedélyezése után, a kezdeti deduplikálás optimalizálási feladata optimalizálja a köteten található fájlokat, amelyek még nem lettek felhasználva, és a következő hatással lesznek a Felhőbeli rétegek használatára:
    - A szabad terület házirendje a hő használatával továbbra is a köteten lévő szabad területtel rendelkező fájlokat fogja használni.
    - A Date Policy kihagyja a fájlokhoz hozzáférő, a deduplikálás optimalizálási feladata miatt esetlegesen felhasználható fájlok leválasztását.
- A folyamatban lévő deduplikálás optimalizációs feladatokhoz a dátummal ellátott felhő-előfizetések késleltetve lesznek az adatok deduplikálása [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) beállítással, ha a fájl még nincs feltömörítve. 
    - Példa: Ha a MinimumFileAgeDays beállítás értéke 7 nap, a felhő-előállítási dátum házirendje pedig 30 nap, a dátum-és 37 nap után a rendszer a fájlokat is felveszi.
    - Megjegyzés: Ha egy fájlt Azure File Sync, akkor a deduplikálás optimalizálási feladata kihagyja a fájlt.
- Ha a telepített Azure file Sync ügynökkel rendelkező Windows Server 2012 R2 rendszert futtató kiszolgáló a Windows Server 2016 vagy a Windows Server 2019 rendszerre frissül, a következő lépéseket kell végrehajtani az deduplikálás és a felhőalapú rétegek egyazon köteten történő támogatásához:  
    - Távolítsa el a Windows Server 2012 R2 Azure File Sync Agent ügynököt, és indítsa újra a kiszolgálót.
    - Töltse le az Azure File Sync-ügynököt az új kiszolgáló operációs rendszerének verziójára (Windows Server 2016 vagy Windows Server 2019).
    - Telepítse a Azure File Sync ügynököt, és indítsa újra a kiszolgálót.  
    
    Megjegyzés: az ügynök eltávolítása és újratelepítése után a rendszer megőrzi a Azure File Sync konfigurációs beállításait a kiszolgálón.

### <a name="distributed-file-system-dfs"></a>Elosztott fájlrendszer (DFS)
Azure File Sync támogatja az elosztott fájlrendszerbeli névterek (DFS-N) és a Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) közötti együttműködési lehetőséget.

**Elosztott fájlrendszerbeli névterek (DFS-n)** : a Azure file Sync teljes mértékben támogatott a DFS-n kiszolgálókon. Az Azure File Sync-ügynököt telepítheti egy vagy több elosztott fájlrendszerbeli tagon, hogy szinkronizálja az adatkapcsolatot a kiszolgálói végpontok és a felhő végpontja között. További információ: az [elosztott fájlrendszerbeli névterek áttekintése](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Elosztott fájlrendszer replikációs szolgáltatása (DFS-r)** : mivel a DFS-r és a Azure file Sync egyaránt replikációs megoldás, a legtöbb esetben azt javasoljuk, hogy a DFS-r-t Azure file Sync-re cserélje. A DFS-R és a Azure File Sync együttes használata azonban több esetben is lehetséges:

- Egy DFS-R üzemelő példányról telepít át egy Azure File Sync központi telepítésre. További információ: [elosztott fájlrendszer replikációs szolgáltatása (DFS-R) központi telepítésének áttelepítése Azure file Syncre](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem minden helyszíni kiszolgálónak, amelyhez a fájl adatai másolata szükséges, közvetlenül kapcsolódhat az internethez.
- A fiókirodai kiszolgálók egyetlen központi kiszolgálóra összesítik az adategységeket, amelyekhez a Azure File Sync-t szeretné használni.

A Azure File Sync és a DFS-R működéséhez egymás mellé:

1. A DFS-R replikált mappákkal rendelkező köteteken le kell tiltani Azure File Sync a Felhőbeli rétegeket.
2. A kiszolgálói végpontokat nem kell konfigurálni az elosztott fájlrendszer-R Írásvédett replikációs mappáin.

További információ: [elosztott fájlrendszer replikációs szolgáltatása Overview (áttekintés](https://technet.microsoft.com/library/jj127250)).

### <a name="sysprep"></a>Sysprep
Nem támogatott a Sysprep használata olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, és nem várt eredményekhez vezethet. Az ügynök telepítése és a kiszolgáló regisztrálása a kiszolgáló rendszerképének telepítése és a Sysprep mini-Setup befejezése után történik.

### <a name="windows-search"></a>Windows-Keresés
Ha a felhő-rétegek engedélyezve vannak egy kiszolgálói végponton, a rendszer kihagyja a többszintes fájlokat, és nem indexeli a Windows Search. A nem rétegű fájlok indexelése megfelelő.

### <a name="antivirus-solutions"></a>Vírusvédelmi megoldások
Mivel a víruskereső úgy működik, hogy a fájlokat az ismert kártékony kódok vizsgálatával végzi, a víruskereső termék a többplatformos fájlok visszahívását okozhatja. A Azure File Sync ügynök 4,0-es és újabb verzióiban a többplatformos fájlok a biztonságos Windows-attribútumot FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS beállítani. Javasoljuk, hogy a szoftver gyártójával való tanácsadással megtudja, hogyan konfigurálhatja a megoldását úgy, hogy kihagyja a fájlok olvasását ezzel az attribútummal (sok minden automatikusan). 

A Microsoft belső vírusvédelmi megoldásai, a Windows Defender és a System Center Endpoint Protection (SCEP) egyaránt automatikusan kihagyják az ezzel az attribútummal rendelkező fájlok olvasását. Teszteltük őket, és azonosított egy kisebb problémát: amikor hozzáad egy kiszolgálót egy meglévő szinkronizálási csoporthoz, a 800 bájtnál kisebb fájlokat a rendszer visszahívja (letölti) az új kiszolgálón. Ezek a fájlok az új kiszolgálón maradnak, és nem lesznek feldolgozva, mivel nem felelnek meg a rétegek méretére vonatkozó követelménynek (> 64 kb).

> [!Note]  
> A víruskereső-szállítók ellenőrizhetik a termékük és a Azure File Sync közötti kompatibilitást a [Azure file Sync Antivirus Compatibility test Suite](https://www.microsoft.com/download/details.aspx?id=58322)használatával, amely letölthető a Microsoft letöltőközpontból.

### <a name="backup-solutions"></a>Biztonsági mentési megoldások
A vírusvédelmi megoldásokhoz hasonlóan a biztonsági mentési megoldások is okozhatják a többplatformos fájlok visszahívását. Azt javasoljuk, hogy egy felhőalapú biztonsági mentési megoldás használatával készítsen biztonsági másolatot az Azure-fájlmegosztás biztonsági mentéséhez a helyszíni biztonsági mentési termék helyett.

Ha helyszíni biztonsági mentési megoldást használ, a biztonsági mentéseket olyan kiszolgálón kell végrehajtani a szinkronizálási csoportban, amelynél le van tiltva a Felhőbeli réteg. Ha visszaállítást végez, használja a kötet szintű vagy a fájl szintű visszaállítási lehetőségeket. A fájl szintű visszaállítási lehetőséggel visszaállított fájlok a szinkronizálási csoport összes végpontján szinkronizálva lesznek, és a meglévő fájlok a biztonsági másolatból visszaállított verzióra lesznek lecserélve.  A mennyiségi szintű visszaállítások nem cserélik le az újabb verziójú fájlokat az Azure-fájlmegosztás vagy más kiszolgálói végpontok között.

> [!Note]  
> Az operációs rendszer nélküli (BMR) visszaállítás váratlan eredményekhez vezethet, és jelenleg nem támogatott.

> [!Note]  
> A VSS-Pillanatképek (beleértve a korábbi verziók lapot) jelenleg nem támogatottak a felhőalapú rétegek használatát engedélyező köteteken. Ha a felhő-rétegek engedélyezve vannak, az Azure-fájlmegosztás pillanatképeit használva állíthatja vissza a fájlokat a biztonsági másolatból.

### <a name="encryption-solutions"></a>Titkosítási megoldások
A titkosítási megoldások támogatása a megvalósításuk módjától függ. Azure File Sync ismert, hogy működjön a következővel:

- BitLocker-titkosítás
- Azure Information Protection, Azure Rights Management Services (Azure RMS) és Active Directory RMS

Azure File Sync ismert, hogy nem működik a következővel:

- NTFS fájlrendszerű titkosított fájlrendszer (EFS)

A Azure File Sync általában támogatja a fájlrendszer alatt található titkosítási megoldásokkal való együttműködést, például a BitLockert, valamint a fájlformátumban megvalósított megoldásokkal (például Azure Information Protection). Nem történt különleges együttműködési képesség a fájlrendszer felett (például az NTFS EFS-ben) megjelenő megoldásokhoz.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Egyéb hierarchikus tárolási kezelési (HSM) megoldások
Nem kell más HSM-megoldásokat használni a Azure File Synchoz.

## <a name="region-availability"></a>Régiónkénti elérhetőség
Azure File Sync csak a következő régiókban érhető el:

| Régió | Adatközpont helye |
|--------|---------------------|
| Kelet-Ausztrália | Új-Dél-Wales |
| Délkelet-Ausztrália | Victoria |
| Dél-Brazília | Sao Paulo állam |
| Közép-Kanada | Toronto |
| Kelet-Kanada | Quebec város |
| Közép-India | Pune |
| USA középső régiója | Iowa |
| Kelet-Ázsia | Hongkong (KKT) |
| USA keleti régiója | Virginia |
| USA 2. keleti régiója | Virginia |
| Közép-Franciaország | Párizs |
| Dél-Franciaország * | Marseille |
| Korea középső régiója | Szöul |
| Korea déli régiója | Puszan |
| Kelet-Japán | Tokió, Szaitama |
| Nyugat-Japán | Oszaka |
| USA északi középső régiója | Illinois |
| Észak-Európa | Írország |
| Dél-Afrika északi régiója | Johannesburg |
| Dél-Afrika nyugati régiója * | Fokváros |
| USA déli középső régiója | Texas |
| Dél-India | Csennai |
| Délkelet-Ázsia | Szingapúr |
| Az Egyesült Királyság déli régiója | London |
| Az Egyesült Királyság nyugati régiója | Cardiff |
| USA-beli államigazgatás – Arizona | Arizona |
| USA-beli államigazgatás – Texas | Texas |
| USA-beli államigazgatás – Virginia | Virginia |
| Egyesült Arab Emírségek északi régiója | Dubai |
| Az Egyesült Arab Emírségek középső régiója * | Abu Dzabi |
| Nyugat-Európa | Hollandia |
| USA nyugati középső régiója | Wyoming |
| USA nyugati régiója | Kalifornia |
| USA nyugati régiója, 2. | Washington |

A Azure File Sync csak olyan Azure-fájlmegosztás szinkronizálását támogatja, amely ugyanabban a régióban található, mint a Storage Sync szolgáltatás.

A csillaggal jelölt régiók esetében kapcsolatba kell lépnie az Azure támogatási szolgálatával, hogy az ezekben a régiókban hozzáférést kérjen az Azure Storage-hoz. A folyamat [ebben a dokumentumban](https://azure.microsoft.com/global-infrastructure/geographies/)található.

### <a name="azure-disaster-recovery"></a>Azure vész-helyreállítás
Egy Azure-régió elvesztése elleni védelem érdekében Azure File Sync integrálódik a [geo-redundáns tárolási redundancia](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) lehetőséggel. A GRS-tároló az elsődleges régió tárterületének aszinkron blokkos replikálásával működik, amellyel a szokásos módon kommunikál, és a tároló a párosított másodlagos régióban történik. Abban az esetben, ha egy Azure-régió átmenetileg vagy véglegesen offline állapotba kerül, a Microsoft feladatátvételi tárterületet küld a párosított régiónak. 

> [!Warning]  
> Ha az Azure-fájlmegosztást Felhőbeli végpontként használja egy GRS, a Storage-fiók feladatátvételét nem kell elindítania. Ennek hatására a szinkronizálás leáll, és az újonnan rétegű fájlok esetében váratlan adatvesztést okozhat. Egy Azure-régió elvesztése esetén a Microsoft a Azure File Syncával kompatibilis módon aktiválja a Storage-fiók feladatátvételét.

A földrajzi redundáns tárolás és a Azure File Sync közötti feladatátvételi integráció támogatásához minden Azure File Sync régió egy másodlagos régióval van párosítva, amely megfelel a Storage által használt másodlagos régiónak. Ezek a párok a következők:

| Elsődleges régió      | Párosított régió      |
|---------------------|--------------------|
| Kelet-Ausztrália      | Délkelet-Ausztrália|
| Délkelet-Ausztrália | Kelet-Ausztrália     |
| Dél-Brazília        | USA déli középső régiója   |
| Közép-Kanada      | Kelet-Kanada        |
| Kelet-Kanada         | Közép-Kanada     |
| Közép-India       | Dél-India        |
| USA középső régiója          | USA 2. keleti régiója          |
| Kelet-Ázsia           | Délkelet-Ázsia     |
| USA keleti régiója             | USA nyugati régiója            |
| USA 2. keleti régiója           | USA középső régiója         |
| Közép-Franciaország      | Dél-Franciaország       |
| Dél-Franciaország        | Közép-Franciaország     |
| Kelet-Japán          | Nyugat-Japán         |
| Nyugat-Japán          | Kelet-Japán         |
| Korea középső régiója       | Korea déli régiója        |
| Korea déli régiója         | Korea középső régiója      |
| Észak-Európa        | Nyugat-Európa        |
| USA északi középső régiója    | USA déli középső régiója   |
| Dél-Afrika északi régiója  | Dél-Afrika nyugati régiója  |
| Dél-Afrika nyugati régiója   | Dél-Afrika északi régiója |
| USA déli középső régiója    | USA északi középső régiója   |
| Dél-India         | Közép-India      |
| Délkelet-Ázsia      | Kelet-Ázsia          |
| Az Egyesült Királyság déli régiója            | Az Egyesült Királyság nyugati régiója            |
| Az Egyesült Királyság nyugati régiója             | Az Egyesült Királyság déli régiója           |
| USA-beli államigazgatás – Arizona      | USA-beli államigazgatás – Texas       |
| US Gov Iowa         | USA-beli államigazgatás – Virginia    |
| USA-beli államigazgatás – Virginia      | USA-beli államigazgatás – Texas       |
| Nyugat-Európa         | Észak-Európa       |
| USA nyugati középső régiója     | USA nyugati régiója, 2.          |
| USA nyugati régiója             | USA keleti régiója            |
| USA nyugati régiója, 2.           | USA nyugati középső régiója    |

## <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Következő lépések
* [A tűzfal és a proxy beállításainak megfontolása](storage-sync-files-firewall-and-proxy.md)
* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [Azure Files üzembe helyezése](storage-files-deployment-guide.md)
* [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
* [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
