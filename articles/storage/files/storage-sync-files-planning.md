---
title: Az Azure File Sync üzembe helyezésének megtervezése |} A Microsoft Docs
description: Ismerje meg az Azure Files üzembe helyezésének tervezése során megfontolandó szempontokat.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: a7d62531492695be6ec148c3bf7b9786b2a428cf
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247395"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Az Azure File Sync üzembe helyezésének megtervezése
Az Azure File Sync használatával fájlmegosztásainak a szervezet az Azure Files között, miközben gondoskodik a rugalmasságát, teljesítményét és kompatibilitását a helyszíni fájlkiszolgálók. Az Azure File Sync Windows Server az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. Helyileg, az adatok eléréséhez a Windows Serveren elérhető bármely protokollt használhatja, beleértve az SMB, NFS és FTPS. Tetszőleges számú gyorsítótárak világszerte igény szerint is rendelkezhet.

Ez a cikk az Azure File Sync üzembe helyezésének fontos szempontokat ismerteti. Azt javasoljuk, hogy Ön is olvashatja [Azure Files üzembe helyezésének megtervezése](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Azure File Sync-terminológia
Első adatait, az Azure File Sync üzembe helyezésének megtervezése, mielőtt fontos terminológia ismertetése.

### <a name="storage-sync-service"></a>Társzinkronizálási szolgáltatás
A Storage Sync Service az Azure File Sync legfelső szintű Azure-erőforrás. A Storage Sync Service erőforrás társa a tárfiók típusú erőforrást, és az Azure-erőforráscsoportok hasonló módon telepíthető. A tárfiók típusú erőforrást a distinct legfelsőbb szintű erőforráshoz szükség, mert a Storage Sync Service hozhat létre szinkronizálási kapcsolatot több tárfiókon keresztül több szinkronizálási csoporttal. Egy előfizetéshez tartozhat több Társzinkronizálási szolgáltatás forrásokkal.

### <a name="sync-group"></a>Szinkronizálási csoport
Szinkronizálási csoport határozza meg, hogy a fájlokat a sync-topológiát. Végpontok egy szinkronizálási csoportban vannak szinkronban egymással. Ha például van két külön csoportja, amely az Azure File Sync használatával kezelni kívánt fájlokat, lenne, két szinkronizálási csoport létrehozása, és különböző végpontok hozzáadása az összes szinkronizálási csoportból. Társzinkronizálási szolgáltatás tetszőleges számú szinkronizálási csoportok igény szerint is üzemeltethet.  

### <a name="registered-server"></a>Regisztrált kiszolgáló
A regisztrált kiszolgáló objektum képviseli a kiszolgáló (vagy fürt) közötti megbízhatósági kapcsolat, és a Storage Sync Service. A Storage Sync Service-példányhoz annyi kiszolgálót a kívánt regisztrálhat. Azonban a kiszolgáló (vagy fürt) regisztrálhatók csak egy Társzinkronizálási szolgáltatást egyszerre.

### <a name="azure-file-sync-agent"></a>Az Azure File Sync ügynök
Az Azure File Sync ügynök csomag egy letölthető, amely lehetővé teszi a Windows Serverről az Azure-fájlmegosztások való szinkronizálása megtörténik. Az Azure File Sync ügynök három fő részből áll: 
- **FileSyncSvc.exe**: A háttérben futó Windows-szolgáltatás, amely figyeli a változásokat a kiszolgálói végpontot, és az Azure-bA szinkronizálási munkamenetek kezdeményezése a felelős.
- **StorageSync.sys**: az Azure File Sync fájlrendszerszűrő, amelynek feladata rétegezési fájlokat az Azure Files (ha felhőbeli rétegezés engedélyezve van).
- **PowerShell-parancsmagok felügyeleti**: Microsoft.StorageSync Azure erőforrás-szolgáltatóval való kommunikációhoz használható PowerShell-parancsmagokkal. Ezek a következő helyeken (alapértelmezett) találhatja meg:
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Kiszolgálói végpont
Kiszolgálói végpont egy adott helyen, egy regisztrált kiszolgálón, például az egyik mappájába, egy kötetet jelöl. Ha a névterek ne legyenek átfedésben több kiszolgálói végpontot létezhet ugyanazon a köteten (például `F:\sync1` és `F:\sync2`). Külön-külön az egyes kiszolgálói végpontot a felhőalapú rétegezési házirendeket konfigurálhat. 

Kiszolgálói végpont a csatlakoztatási pont használatával is létrehozhat. Vegye figyelembe, hogy akkor csatlakozási belül a kiszolgálói végpontot a rendszer kihagyja.  

Kiszolgálói végpont a rendszerköteten hozhat létre, de korlátozottak két ekkor:
* Felhőbeli rétegezés nem lehet engedélyezni.
* (Ahol a rendszer gyorsan vonja le a teljes névteret, majd elindítja visszaírásához tartalom) gyors névtér visszaállítása nem történik meg.


> [!Note]  
> Csak nem cserélhető kötetek használata támogatott.  Egy távoli megosztásban található csatlakoztatott meghajtók nem támogatottak a kiszolgálói végpont elérési útja.  Emellett a kiszolgálói végpont is található, a rendszerkötet, ha a felhő Windows rétegezést nem támogatják a rendszerköteten.

Ha hozzáad egy kiszolgálón található, amely a szinkronizálási csoport kiszolgálói végpont fájlok meglévő készletének rendelkezik, ezeket a fájlokat az egyéb fájlokat, amelyek már a szinkronizálási csoport más végpontok összefésülése megtörténjen.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
Felhőbeli végpont egy Azure-fájlmegosztás, amely a szinkronizálási csoport része. Az Azure file teljes megosztás szinkronizálásokat, és Azure-fájlmegosztások csak egy felhőbeli végpont tagja lehet. Ezért Azure-fájlmegosztások lehet csak egyetlen szinkronizálási csoport tagja. Ha hozzáad egy Azure-fájlmegosztás, amely rendelkezik egy meglévő készlet fájlokat a szinkronizálási csoport felhőbeli végpont, a meglévő fájlokat összevonásra kerülnek, amelyek már más végpontok a szinkronizálási csoport többi fájlt.

> [!Important]  
> Az Azure File Sync közvetlenül támogatja az Azure-fájlmegosztás módosítását. Az Azure-fájlmegosztás végzett módosítások azonban először kell deríteni az az Azure File Sync módosítása észlelése. A módosítás észlelési feladat csak egyszer 24 óránként indul a felhőbeli végpont. Emellett az Azure-fájlmegosztások a REST protokoll használatával végrehajtott módosítások nem fogja frissíteni az SMB-utolsó módosítás időpontja, és nem látható, a módosítás az sync. További információkért lásd: [– gyakori kérdések az Azure Files](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Felhőbeli rétegzés 
Felhőbeli rétegezés választható szolgáltatás az Azure File Sync, amelyben a ritkán használt vagy nagyobb méretű 64 KiB helyezhető el az Azure Files fájlok érhető el. A rétegzett egy fájlt, az Azure File Sync fájlrendszerszűrő (StorageSync.sys) helyettesíti a fájlt helyileg egy mutatót, vagy az újraelemzési pont. Az újraelemzési pontot az Azure Files-fájlra mutató URL-címet jelöli. Egy rétegzett fájlt a harmadik féltől származó alkalmazások azonosíthatja a rétegzett fájlokhoz, az NTFS beállítása "offline" attribútummal rendelkezik. Amikor egy felhasználó megnyit egy rétegzett fájlt, az Azure File Sync zökkenőmentesen visszahívja a fájl adatait az Azure Files a ismerete, hogy a fájl nem található helyileg a rendszer a felhasználó nélkül. Ez a funkció hierarchikus tárolás (HSM) is nevezik.

> [!Important]  
> Felhőbeli rétegezés esetén nem támogatott a Windows rendszer köteteken kiszolgálói végpontot.

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Az Azure File Sync Rendszerkövetelmények és együttműködés 
Ez a szakasz ismerteti az Azure File Sync ügynök Rendszerkövetelmények és a Windows Server szolgáltatásaival és a szerepkörök és a külső felek megoldásaival való együttműködés.

### <a name="evaluation-tool"></a>Kiértékelési eszközével
Az Azure File Sync üzembe helyezése előtt, ki kell értékelni a rendszer az Azure File Sync értékelési eszközzel kompatibilis-e. Ez az eszköz az AzureRM PowerShell-parancsmag, amely ellenőrzi, a fájlrendszert és adatkészlethez, például a nem támogatott karaktereket vagy nem támogatott operációsrendszer-verzió a potenciális problémákat. Vegye figyelembe, hogy ellenőrizze az legtöbb terjed ki, de nem az összes alábbi; szolgáltatás azt javasoljuk, hogy olvassa el ezt a szakaszt körültekintően ellenőrizze az üzemelő példány kerül zökkenőmentesen, a rest-en keresztül. 

#### <a name="download-instructions"></a>Útmutató letöltése
1. Győződjön meg arról, hogy a PackageManagement legújabb verzióját, és a PowerShellGet telepítése (Ez lehetővé teszi, hogy az előzetes verziójú modulok telepítése)
    
    ```PowerShell
        Install-Module -Name PackageManagement -Repository PSGallery -Force
        Install-Module -Name PowerShellGet -Repository PSGallery -Force
    ```
 
2. Indítsa újra a PowerShell
3. A modulok telepítése
    
    ```PowerShell
        Install-Module -Name AzureRM.StorageSync -AllowPrerelease
    ```

#### <a name="usage"></a>Használat  
A kiértékelési eszközével hívhat meg több különböző módon is: a rendszer ellenőrzi, az adatkészlet-ellenőrzéseket vagy mindkettő hajthat végre. A rendszer és az adatkészletet ellenőrzések elvégzéséhez: 

```PowerShell
    Invoke-AzureRmStorageSyncCompatibilityCheck -Path <path>
```

Csak az adatkészlet teszteléséhez:
```PowerShell
    Invoke-AzureRmStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Csak a rendszerkövetelmények teszteléséhez:
```PowerShell
    Invoke-AzureRmStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Az eredmények megjelenítése a fürt megosztott kötetei szolgáltatás:
```PowerShell
    $errors = Invoke-AzureRmStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Rendszerkövetelmények
- A Windows Server 2012 R2 vagy Windows Server 2016 rendszert futtató kiszolgáló 

    | Verzió | Támogatott termékváltozatok | Támogatott központi telepítési beállítások |
    |---------|----------------|------------------------------|
    | Windows Server 2016 | Datacenter és Standard | Teljes (a felhasználói felületen keresztül kiszolgáló) |
    | Windows Server 2012 R2 | Datacenter és Standard | Teljes (a felhasználói felületen keresztül kiszolgáló) |

    A Windows Server későbbi verzióiban bekerül vannak. Windows korábbi verzióiban előfordulhat, hogy a felhasználói visszajelzések alapján adható hozzá.

- A kiszolgáló, egy legalább 2GB memória

    > [!Important]  
    > Ha a kiszolgáló a dinamikus memória engedélyezve van a virtuális gépen fut, a virtuális gép legalább 2048MB memória található kell konfigurálni.
    
- Az NTFS fájlrendszerrel formázott helyileg csatlakoztatott köteten

> [!Important]  
> Azt javasoljuk, hogy minden kiszolgáló, amelyet az Azure File Sync mindig naprakészek lehetnek az a legújabb frissítéseket a Windows Update gondoskodik. 

### <a name="file-system-features"></a>Fájlrendszer-szolgáltatásokat
| Szolgáltatás | Támogatás állapota | Megjegyzések |
|---------|----------------|-------|
| Hozzáférés-vezérlési listák (ACL) | Teljes mértékben támogatott. | Windows hozzáférés-vezérlési listák az Azure File Sync megmaradnak, és kényszeríti a Windows Server server végpontokon. Windows hozzáférés-vezérlési listák (még nem) támogatja az Azure Files, ha a fájlok érhetők el közvetlenül a felhőben. |
| Rögzített hivatkozások | Kihagyva | |
| Szimbolikus hivatkozások | Kihagyva | |
| Csatlakoztatási pontok | Részben támogatott | Csatlakoztatási pontok lehet, hogy a kiszolgálói végpont gyökerében, de a rendszer kihagyja őket, ha a kiszolgálói végpont névtér szerepel. |
| Elhelyezni pontokra | Kihagyva | Ha például Distributed File System DfrsrPrivate és DFSRoots mappákat. |
| Újraelemzési pontok | Kihagyva | |
| Az NTFS-tömörítés | Teljes mértékben támogatott. | |
| Ritka fájlok | Teljes mértékben támogatott. | Ritka fájlok szinkronizálása (nincs letiltva), de azok szinkronizálása a felhőbe teljes-fájlként. Ha a fájl tartalmát megváltoztatja a felhőben (vagy egy másik kiszolgálón), a fájl már nem ritka, amikor a módosítást. |
| Alternatív Adatstreamek (ADS) | Megmarad, de nincs szinkronizálva | Ha például létrehozta a fájlbesorolási infrastruktúra besorolási címkék nem lett szinkronizálva. Az egyes a kiszolgálói végpontot a meglévő besorolási címkék balra változatlanul. |

> [!Note]  
> Csak az NTFS-kötetekről támogatottak. Az reFS, FAT, FAT32 és más fájlrendszereket nem támogatottak.

### <a name="files-skipped"></a>Kihagyott fájlok
| Fájl vagy mappa | Megjegyzés |
|-|-|
| Desktop.ini | A fájl adott rendszerhez |
| ethumbs.db$ | Ideiglenes fájlt a miniatűrök |
| ~$\*.\* | Office-fájl ideiglenes |
| \*.tmp | Ideiglenes fájl |
| \*.laccdb | DB hozzáférés a zárolási fájl|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Belső szinkronizálási fájl|
| \\Rendszerkötet-információkat | A kötet adott mappa |
| $RECYCLE. DOBOZ| Mappa |
| \\SyncShareState | Szinkronizálási mappa |

### <a name="failover-clustering"></a>A Feladatátvételi fürtszolgáltatás
A Windows Server feladatátvételi fürtszolgáltatás támogatott az Azure File Sync "Általános felhasználású fájlkiszolgáló" beállítás. A Feladatátvételi fürtszolgáltatás nem támogatott a "Kibővített fájlkiszolgáló az alkalmazásadatokhoz" (SOFS) vagy a megosztott fürtkötetek (CSV).

> [!Note]  
> Az Azure File Sync ügynök megfelelő működéséhez szinkronizálási egy feladatátvevő fürt minden csomópontján telepítve kell lennie.

### <a name="data-deduplication"></a>Az Adatdeduplikáció
Olyan kötetek, amelyek nem rendelkeznek a felhőbeli rétegezés engedélyezve van, az Azure File Sync támogatja a Windows Server Adatdeduplikáció engedélyezése a köteten. Jelenleg nem támogatjuk együttműködés a felhőbeli rétegezés engedélyezve van az Azure File Sync és az Adatdeduplikáció között.

### <a name="distributed-file-system-dfs"></a>Az elosztott fájlrendszer (DFS)
Az Azure File Sync támogatja az elosztott Fájlrendszerbeli névtereket (DFS-N) és az elosztott fájlrendszer replikációs szolgáltatása (DFS-R) kezdődő együttműködési [Azure File Sync ügynök 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**Az elosztott Fájlrendszerbeli névtereket (DFS-N)**: az Azure File Sync teljes mértékben támogatott a DFS-N-kiszolgálókon. Egy vagy több DFS-N tagokat a kiszolgálói végpontot és a felhőbeli végpont közötti adatszinkronizálás az Azure File Sync ügynök telepíthető. További információkért lásd: [az elosztott Fájlrendszerbeli névterek áttekintése](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Elosztott fájlrendszer replikációs szolgáltatása (DFS-R)**: mivel DFS-R és az Azure File Sync, a legtöbb esetben a két replikációs megoldás, azt javasoljuk, hogy a DFS-R cserélje le az Azure File Sync. Van azonban számos forgatókönyv, ahol szeretne, használja a DFS-R és az Azure File Sync együtt:

- Az Azure File Sync üzembe helyezésének áttelepítése a DFS-R-telepítésből. További információkért lásd: [áttelepítheti egy elosztott fájlrendszer replikációs szolgáltatása (DFS-R) környezetet az Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem minden helyszíni kiszolgáló, amelyekre szüksége van egy példányát az adatokat közvetlenül az internethez való csatlakoztathatók.
- Fiókirodai kiszolgáló összevonni az adatokat az alakzatot, amelyhez szeretné használni az Azure File Sync egy központi kiszolgálóval.

Az Azure File Sync és a DFS-R egymás mellett működik:

1. Az Azure File Sync felhőbeli rétegezési kell letiltani a DFS-R replikált mappákat tartalmazó kötet.
2. A csak olvasható replikációs mappák a DFS-R kiszolgálóvégpontok nem kell konfigurálni.

További információkért lásd: [elosztott fájlrendszer replikációs szolgáltatása áttekintése](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>A Sysprep
A kiszolgálón, amely az Azure File Sync-ügynök van telepítve a sysprep használata nem támogatott, és váratlan eredményekhez vezethet. Az ügynök telepítése és a kiszolgáló regisztrálása a kiszolgálói lemezkép központi telepítése és a sysprep minitelepítő befejezése után történjen.

### <a name="windows-search"></a>A Windows Search
Ha a felhőbeli rétegezés engedélyezve van a kiszolgálóvégponton, a rendszer rétegzett fájlok kihagyva, és nem indexeli a Windows Search. A nem rétegzett fájlok megfelelően vannak indexelve.

### <a name="antivirus-solutions"></a>A víruskereső megoldásokkal
Víruskereső ismert kártevő kódja fájlok vizsgálata úgy működik, mert egy víruskereső előfordulhat, hogy a rétegzett fájlok visszahívása. Mert rétegzett fájlok beállítása "offline" attribútuma, javasoljuk, hogy a szoftver gyártójával megtudhatja, hogyan konfigurálhatja a kapcsolat nélküli fájlok olvasásakor kihagyandó megoldás egyeztetett. 

A következő megoldások ismert támogatására, a rendszer kihagyja a kapcsolat nélküli fájlok:

- [A Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - A Windows Defender automatikusan kihagyja az offline attribútum beállítása rendelkező fájlok olvasásához. Azt Defender teszteltük, és egy kisebb hibát azonosított: a kiszolgáló egy meglévő szinkronizálási csoporthoz való hozzáadásakor fájlok kisebb, mint 800 bájt vannak idézni (Letöltés) az új kiszolgálón. Ezek a fájlok továbbra is az új kiszolgálón, és nem lesz rétegzett, mivel azok nem felelnek meg a rétegzési méretkövetelményt (> 64 KB-os).
- [A System Center Endpoint Protection (SCEP)](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - SCEP működik, mint a Defender; Lásd a fenti
- [A Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (lásd a "Vizsgálat csak szükséges" 90 oldalán a PDF-fájl)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [A Sophos az Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Biztonsági mentési megoldások
Például a víruskereső megoldások biztonsági mentési megoldások okozhat a rétegzett fájlok visszahívása. Azt javasoljuk, hogy biztonsági mentése az Azure-fájlmegosztás helyett egy a helyszíni biztonsági mentési termék egy felhőalapú biztonsági mentési megoldás használatával.

Ha egy a helyszíni biztonsági mentési megoldást használ, az adott kiszolgálón a szinkronizálási csoport, amely rendelkezik a felhőbeli rétegezés letiltott az biztonsági mentések kell végezni. A kiszolgálói végpont helye tanúsítványfájljai visszaállításakor visszaállítás beállítást használja. Helyreállított fájlok lesznek szinkronizálva a szinkronizálási csoportban lévő összes végpontokra, és a meglévő fájlok váltja fel a biztonsági másolatból visszaállított verziót.

> [!Note]  
> Alkalmazásbarát, kötetszintű és az operációs rendszer nélküli (BMR) visszaállítási beállítások pedig váratlan helyzeteket eredményezhet, és jelenleg nem támogatottak. A visszaállítási lehetőségek egy későbbi kiadásban támogatott.

### <a name="encryption-solutions"></a>Titkosítási megoldások
Titkosítási megoldások támogatása attól függ, hogyan használják azokat. Az Azure File Sync ismert használata:

- BitLocker-titkosítást
- Az Azure Information Protection, az Azure Rights Management Services (Azure RMS), és az Active Directory RMS

Az Azure File Sync nem működik az ismert:

- Az NTFS fájlrendszer (EFS) titkosított

Általánosságban véve az Azure File Sync támogatnia kell a titkosítási megoldásokkal, a fájlrendszer, a BitLocker, például az alább található, és fájlformátumban, például az Azure Information Protection megvalósított megoldások együttműködési. Nincsenek speciális együttműködési megoldások, a fájlrendszer, (például NTFS EFS) felett található lett végrehajtva.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Egyéb hierarchikus tárolás (HSM) megoldások
Nincs más HSM-megoldások az Azure File Sync használatával kell használni.

## <a name="region-availability"></a>Régiónkénti elérhetőség
Az Azure File Sync csak az alábbi régiókban érhető el:

| Régió | Adatközpont helye |
|--------|---------------------|
| Kelet-Ausztrália | Új-Dél-Wales |
| Délkelet-Ausztrália | Victoria |
| Közép-Kanada | Toronto |
| Kelet-Kanada | Quebec város |
| Közép-India | Pune |
| USA középső régiója | Iowa |
| Kelet-Ázsia | Hongkong KKT |
| USA keleti régiója | Virginia |
| USA 2. keleti régiója | Virginia |
| Észak-Európa | Írország |
| Dél-India | Csennai |
| Délkelet-Ázsia | Szingapúr |
| Az Egyesült Királyság déli régiója | London |
| Az Egyesült Királyság nyugati régiója | Cardiff |
| Nyugat-Európa | Hollandia |
| USA nyugati régiója | Kalifornia |

Az Azure File Sync támogatja, és a Storage Sync Service ugyanabban a régióban található Azure-fájlmegosztások csak való szinkronizálását.

### <a name="azure-disaster-recovery"></a>Azure-beli vészhelyreállításához
Egy Azure-régióban, adatvesztés elleni védelme érdekében az Azure File Sync integrálható a [georedundáns tárhely-redundancia](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) lehetőséget. GRS tároló működéséről a párosított másodlagos régióban aszinkron blokkreplikációt az elsődleges régióban, amellyel normál esetben kezelheti, storage és a storage használatával. Egy katasztrófa, amely egy Azure-régió ideiglenesen vagy véglegesen offline állapotba a Microsoft feladatátvételt hajt végre a párosított régióban a storage. 

Georedundáns tárolás és az Azure File Sync feladatátvételi integrációjával támogatása érdekében minden Azure-fájlszinkronizálás régió egy másodlagos régióba, amely megfelel a másodlagos régióba storage által használt rendszer párban. A párok a következők:

| Elsődleges régió      | Párosított régió      |
|---------------------|--------------------|
| Kelet-Ausztrália      | Délkelet-Ausztrália |
| Délkelet-Ausztrália | Kelet-Ausztrália     |
| Közép-Kanada      | Kelet-Kanada        |
| Kelet-Kanada         | Közép-Kanada     |
| Közép-India       | Dél-India        |
| USA középső régiója          | USA 2. keleti régiója          |
| Kelet-Ázsia           | Délkelet-Ázsia     |
| USA keleti régiója             | USA nyugati régiója            |
| USA 2. keleti régiója           | USA középső régiója         |
| Észak-Európa        | Nyugat-Európa        |
| Dél-India         | Közép-India      |
| Délkelet-Ázsia      | Kelet-Ázsia          |
| Az Egyesült Királyság déli régiója            | Az Egyesült Királyság nyugati régiója            |
| Az Egyesült Királyság nyugati régiója             | Az Egyesült Királyság déli régiója           |
| Nyugat-Európa         | Észak-Európa       |
| USA nyugati régiója             | USA keleti régiója            |

## <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>További lépések
* [Vegye figyelembe a tűzfal és proxy-beállítások](storage-sync-files-firewall-and-proxy.md)
* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [Az Azure Files üzembe helyezése](storage-files-deployment-guide.md)
* [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
