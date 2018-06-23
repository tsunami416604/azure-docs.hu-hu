---
title: Egy Azure fájlszinkronizálás (előzetes verzió) telepítésének tervezése |} Microsoft Docs
description: További tudnivalók az Azure-fájlok központi telepítésének tervezése során megfontolandó tényezőkről.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 1927ab29e82836c60b2ba36c3eec0acf49778082
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335839"
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Egy Azure fájlszinkronizálás (előzetes verzió) telepítésének tervezése
Sync szolgáltatás használatával Azure fájl (előzetes verzió) központosítása fájlmegosztások a szervezet Azure fájlokban, ugyanakkor változatlanul megőrizze a rugalmasság, a teljesítmény és a kompatibilitási egy helyszíni fájlkiszolgáló. Azure fájlszinkronizálás átalakítja a Windows Server az Azure fájlmegosztás gyors gyorsítótárába. Minden protokoll, amely a Windows Server helyileg, az adatok eléréséhez használhatja, többek között a ftps-t, SMB és NFS. Akkor is annyi gyorsítótárak világszerte szükség szerint.

Ez a cikk egy Azure fájlszinkronizálás telepítését fontos szempontokat ismerteti. Azt javasoljuk, hogy akkor is olvassa el [Azure fájlok központi telepítésének tervezésében](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Az Azure fájlszinkronizálás terminológia
Beolvasása fájlszinkronizálás Azure központi telepítésének tervezésében az részleteinek, előtt fontos tudni, hogy a terminológia.

### <a name="storage-sync-service"></a>Társzinkronizálási szolgáltatás
A tároló szinkronizálási szolgáltatás az Azure-fájl szinkronizálás a legfelső szintű Azure-erőforrás. A tároló szinkronizálási szolgáltatás erőforrás, a tárolási fiók erőforrás társa, és hasonló módon léptethetők hatályba Azure erőforráscsoport-sablonok. A tárolási fiók erőforrások különálló legfelső szintű erőforrás kell adni, mert a tároló szinkronizálási szolgáltatás szinkronizálási kapcsolat több tárfiókot több szinkronizálási csoportok segítségével hozhatja létre. Előfizetés rendelkezhet több tároló szinkronizálási szolgáltatás források.

### <a name="sync-group"></a>Szinkronizálási csoport
A szinkronizálás csoport adja meg a sync-topológiát a fájlokat. A szinkronizálási csoporton belüli végpontok tartják szinkronban vannak egymással. Ha például van két különálló Azure fájlszinkronizálás felügyelni kívánt fájlokat, ehhez két szinkronizálási csoportok létrehozása, és különböző végpontokhoz hozzá minden egyes szinkronizálás csoporthoz. Egy tároló szinkronizálási szolgáltatás kell szinkronizálási csoportok tárolhatja.  

### <a name="registered-server"></a>Regisztrált kiszolgáló
A regisztrált kiszolgáló objektum által jelképezett a kiszolgáló (vagy a fürt) közötti megbízhatósági kapcsolat és a tároló szinkronizálási szolgáltatást. Regisztrálhatja annyi kiszolgálókat egy tárolási szinkronizálási szolgáltatáspéldány, ahányat csak szeretne. Azonban a kiszolgáló (vagy a fürt) regisztrálhatók csak egy tároló szinkronizálási szolgáltatás egyszerre.

### <a name="azure-file-sync-agent"></a>Azure fájl Sync-ügynök
Az Azure fájlszinkronizálás ügynök csomag egy letölthető, amely lehetővé teszi a Windows Server az Azure fájlmegosztások szinkronizálhatóak. Az Azure fájlszinkronizálás ügynök három fő részből áll: 
- **FileSyncSvc.exe**: A háttérben történő Windows-szolgáltatás, amelyet figyeli a változásokat server végponton, és a szinkronizálási munkamenetek Azure kezdeményezése felelős.
- **StorageSync.sys**: az Azure fájlszinkronizálás fájlrendszerszűrő, amely az Azure Fileshoz rétegezési fájlok felelős (amikor a felhő rétegezéséhez engedélyezve van).
- **PowerShell parancsmagokat**: PowerShell-parancsmagok használata interaktív Microsoft.StorageSync Azure erőforrás-szolgáltató. Ezek a következő (alapértelmezett) helyen található:
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Kiszolgáló-végpont
Egy kiszolgáló végpont képviseli regisztrált kiszolgálón, például egy mappát egy olyan kiszolgáló köteten egy konkrét helyre. Több kiszolgáló végpont ugyanazon a köteten található is, ha a névterek nem lehetnek átfedésben (például `F:\sync1` és `F:\sync2`). Beállíthatja felhő rétegezési egyenként az egyes kiszolgáló-végpont esetében. 

A kiszolgáló végpont a csatlakoztatási pont használatával hozhat létre. Jegyezze fel, akkor a kiszolgáló végpont belül csatlakozási kimarad.  

Létrehozhat egy végpontját a rendszerkötet, de nincsenek két korlátozások vonatkoznak, ha ezt teszi:
* Felhő rétegezéséhez nem engedélyezhető.
* Gyors névtér visszaállítást (ahol a rendszer gyorsan számos lehetőséget kínál a teljes névteret le, majd elindítja tartalom visszahívásához) a rendszer nem végez.


> [!Note]  
> Csak nem cserélhető kötetek támogatottak.  Távoli megosztásból csatlakoztatott meghajtók nem támogatottak a kiszolgáló végpont elérési útvonalat.  A kiszolgáló végpont elhelyezhető továbbá a rendszerkötet, ha felhőalapú Windows rétegezéséhez nem támogatott a rendszerköteten.

Ha egy kiszolgálón, amelyen egy meglévő fájlokat egy server végpontként szinkronizálási csoporthoz ad hozzá, azokat a fájlokat egyesítve lesznek az egyéb fájlokat, amelyek már megtalálhatók a szinkronizálási csoport végpontja.

### <a name="cloud-endpoint"></a>Felhő végpont
A felhő-végpont esetében az Azure fájlmegosztások, amely a szinkronizálási csoport része. A teljes fájl Azure fájlmegosztás szinkronizálások és az Azure fájlmegosztások csak egy felhőhöz végpont tagja lehet. Ezért az Azure fájlmegosztások lehet csak egyetlen szinkronizálási csoport tagja. Ha az Azure fájlmegosztások, amely rendelkezik egy meglévő fájlkészlet a felhővégpontnak szinkronizálási csoporthoz ad hozzá, a meglévő fájlokat egyesítve lesznek az egyéb fájlokat, amelyek már megtalálhatók a szinkronizálási csoport végpontja.

> [!Important]  
> Az Azure fájlszinkronizálás támogatja közvetlenül az Azure fájlmegosztások hajt végre módosításokat. Azonban a Azure fájlmegosztás végrehajtott módosítások először kell egy Azure fájlszinkronizálás módosítás észlelése észlelhetők. A módosítás észlelése a felhőbeli végpont 24 óránként csak egyszer indítható. Ezenkívül az Azure fájlmegosztások a többi protokollon keresztül végzett módosítások nem fogja frissíteni az SMB-utolsó módosítás időpontja, és nem látható, a módosítás szinkronizálás által. További információkért lásd: [Azure fájlok gyakran ismételt kérdések](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Felhőbeli rétegzés 
A felhő rétegezéséhez választható szolgáltatás Azure fájlszinkronizálás, amelyben a ritkán használt, vagy nagyobb, mint 64 KiB méretű képes helyezhető el a az Azure Fileshoz érhető el. Ha egy fájl többszintű, Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys) lecseréli a fájlt helyileg mutatót, vagy újraelemzési pont. Az újraelemzési pont Azure fájlok a fájl URL-címet jelöli. A rétegzett fájl beállítani az NTFS-sel, harmadik féltől származó alkalmazások azonosíthassa a rétegzett fájlokat a "kapcsolat nélküli" attribútummal rendelkezik. Ha a felhasználó megnyit egy rétegzett fájlt, Azure fájlszinkronizálás zökkenőmentesen visszahívja az Azure-fájlokból a fájladatok anélkül, hogy a felhasználó ismeri az, hogy a fájl nem található helyileg a rendszeren. Ez a funkció hierarchikus tárolás (HSM) is nevezik.

> [!Important]  
> Felhő server végpontok a Windows rendszert tartalmazó kötetek adatdeduplikációval esetén nem támogatott.

## <a name="azure-file-sync-interoperability"></a>Az Azure fájlszinkronizálás együttműködés 
Ez a fejezet a Windows Server szolgáltatásaival és a szerepkörök és a külső megoldások Azure fájlszinkronizálás együttműködés.

### <a name="supported-versions-of-windows-server"></a>Windows Server támogatott verziói
Jelenleg a Windows Server által Azure fájlszinkronizálás verziók a következők:

| Verzió | Támogatott termékváltozat | Támogatott üzembe helyezési lehetőségei |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter és Standard | Teljes (a felhasználói felület server) |
| Windows Server 2012 R2 | Datacenter és Standard | Teljes (a felhasználói felület server) |

Vannak a Windows Server későbbi verzióiban lesz hozzáadva. A Windows korábbi verzióiban előfordulhat, hogy a felhasználói visszajelzések alapján adható hozzá.

> [!Important]  
> Azt javasoljuk, hogy naprakészen a legújabb frissítéseket a Windows Update-ről az Azure-fájl szinkronizálással használt összes kiszolgálónak. 

### <a name="file-system-features"></a>Fájlrendszer szolgáltatásokat
| Szolgáltatás | Támogatási állapota | Megjegyzések |
|---------|----------------|-------|
| Hozzáférés-vezérlési listák (ACL) | Teljes mértékben támogatott. | Windows ACL-ek által Azure fájlszinkronizálás megmaradnak, és kikényszeríti a Windows Server server végpontokon. A Windows hozzáférés-vezérlési listák (még nem) Azure-fájlokat támogatja, ha a fájlok érhetők el közvetlenül a felhőben. |
| Rögzített hivatkozások | Kihagyva | |
| Szimbolikus hivatkozásokat | Kihagyva | |
| Csatlakoztatási pontok | Részlegesen támogatott | Csatlakoztatási pontok lehet, hogy a legfelső szintű egy kiszolgáló végpont, de ha egy kiszolgáló végpont névtérben található kimarad. |
| Elhelyezni pontokra | Kihagyva | Például elosztott fájl rendszer DfrsrPrivate és DFSRoots mappák. |
| Újraelemzési pontok | Kihagyva | |
| NTFS-tömörítése | Teljes mértékben támogatott. | |
| Ritka fájlok | Teljes mértékben támogatott. | Ritka fájlok szinkronizálási (nem zárolt), de a szinkronizálás a felhőbe egy teljes fájlként. Ha a fájl tartalmának módosítása a felhőben (vagy egy másik kiszolgálón), a fájl már nem ritka, amikor a módosítás. |
| Alternatív adatfolyamok (ADS) | Megmarad, de nem szinkronizált | Például hozta létre a fájlbesorolási infrastruktúra besorolási címkék nem lett szinkronizálva. Az egyes server végpontjainak a meglévő besorolási címkék balra hagyása. |

> [!Note]  
> Csak az NTFS-köteteket támogatottak. A refs fájlrendszer, FAT, FAT32 és más fájlrendszerek nem támogatottak.

### <a name="files-skipped"></a>Kimaradt fájlok
| File/Folder | Megjegyzés |
|-|-|
| Desktop.ini | A fájl adott rendszerhez |
| ethumbs.db$ | Ideiglenes fájlt a miniatűrök |
| ~$\*.\* | Office-fájl ideiglenes |
| \*.tmp | Ideiglenes fájl |
| \*.laccdb | DB hozzáférés a zárolási fájl|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Belső szinkronizálási fájl|
| \\A System Volume Information | Adott kötetre mappa |
| $RECYCLE. BIN| Mappa |
| \\SyncShareState | Mappa-szinkronizáláshoz |

### <a name="failover-clustering"></a>Feladatátvételi fürtszolgáltatás
Windows Server feladatátvételi fürtszolgáltatás támogatott Azure fájl szinkronizálás által "Általános felhasználású fájlkiszolgáló" rendszerbe állítási beállításként. Feladatátvételi fürtszolgáltatás nem támogatott a "Kibővített fájlkiszolgáló az alkalmazásadatokhoz" (SOFS) vagy a megosztott fürtkötetek (CSV).

> [!Note]  
> Az Azure fájlszinkronizálás ügynököt telepíteni kell egy feladatátvevő fürtben való szinkronizálás megfelelő működéséhez minden csomóponton.

### <a name="data-deduplication"></a>Adatdeduplikáció
A kötetek adatdeduplikációval engedélyezett felhő nem rendelkezik Azure fájlszinkronizálás támogatja a Windows Server az Adatdeduplikáció engedélyezése a köteten. Jelenleg nem támogatjuk Azure fájlszinkronizálás rétegezéséhez engedélyezve van a felhő és az Adatdeduplikáció közötti együttműködés.

### <a name="distributed-file-system-dfs"></a>Elosztott fájlrendszer (DFS)
Azure fájlszinkronizálás támogatja az elosztott Fájlrendszerbeli névterek (DFS-N) és az Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) kezdődő, és együttműködési [Azure fájlszinkronizálás ügynök 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**Az elosztott Fájlrendszerbeli névterek (DFS-N)**: Azure fájlszinkronizálás teljes mértékben támogatja a DFS-N kiszolgálókon. Az Azure fájlszinkronizálás ügynök telepíthető a server-végpontokat és a felhőbeli végpont között szinkronizálja az adatokat egy vagy több DFS-N tagokat. További információkért lásd: [az elosztott Fájlrendszerbeli névterek – áttekintés](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Elosztott fájlrendszer replikációs szolgáltatása (DFS-R)**: mivel a DFS-R és Azure fájlszinkronizálás mindkét replikációs megoldás, a legtöbb esetben, javasoljuk, hogy a DFS-R lecserélését Azure fájlszinkronizálás. Van, de több forgatókönyv szerint, ha meg szeretné használni a DFS-R és Azure fájlszinkronizálás együtt:

- Egy Azure fájlszinkronizálás telepítésre a DFS-R-telepítés telepít. További információkért lásd: [egy Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) központi telepítés át Azure fájlszinkronizálás](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem minden a helyi kiszolgálót, amely az adatokat egy példányát kell csatlakoztatható közvetlenül az internethez.
- Fiókirodai kiszolgáló összevonni az adatokat, amelyhez Azure fájl Sync szolgáltatás használatával szeretné egy központi kiszolgálóval.

Az Azure fájlszinkronizálás és DFS-R egymás melletti működéséhez:

1. Azure fájl szinkronizálása a felhőalapú rétegezési kell letiltani a DFS-R replikált mappák rendelkező köteteket.
2. Kiszolgáló végpontok nem csak olvasható replikációs mappák a DFS-R kell beállítani.

További információkért lásd: [Elosztott fájlrendszer replikációs szolgáltatása áttekintése](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>A Sysprep
A kiszolgálón, amely az Azure fájl Sync-ügynök van telepítve a sysprep használata nem támogatott, és váratlan eredményekhez vezethet. Az ügynök telepítése és a kiszolgáló regisztrálása a kiszolgálói lemezkép telepítése és a sysprep minitelepítő befejezése után kell végrehajtani.

### <a name="windows-search"></a>A Windows Search
Ha felhőalapú rétegezéséhez engedélyezve van a kiszolgáló végponton, a fájlokat, amelyek Unja már kihagyva, és nem indexelik a Windows Search. A fájlok nem rétegű indexelt megfelelően.

### <a name="antivirus-solutions"></a>Víruskereső megoldások
A víruskereső vizsgálja a fájlokat az ismert rosszindulatú kódot úgy működik, mert egy víruskereső szoftver a rétegzett fájlok visszahívása okozhatja. Mivel rétegzett fájlokat a "kapcsolat nélküli" attribútummal rendelkezik, ajánlott a szoftver gyártójához annak megismerése, hogyan konfigurálhatja a kapcsolat nélküli fájlok olvasásának kihagyását megoldás egyeztetett. 

A következő megoldásokat ismert, hogy támogatja a rendszer kihagyja a kapcsolat nélküli fájlok:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee végpont biztonsági](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (lásd a "Vizsgálata csak mit kell" a PDF 90 lapján)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos az Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Biztonsági mentési megoldás
Víruskereső megoldások, például a biztonsági mentési megoldás a rétegzett fájlok visszahívása okozhat. Azt javasoljuk, hogy a felhő biztonsági mentési megoldás segítségével készítsen biztonsági másolatot az Azure-fájlmegosztáshoz egy helyszíni biztonsági mentési termék helyett.

Ha egy helyszíni biztonsági mentési megoldás használata esetén biztonsági mentések felhő rétegezéséhez letiltott a szinkronizálási csoportba a kiszolgálón kell elvégezni. Fájlok belül a kiszolgáló végponthelyét visszaállításakor szintű visszaállítási beállítást használja. A szinkronizálás csoportban található összes végpontok visszaállított fájlok lesznek szinkronizálva, és meglévő fájlok váltja fel a biztonsági másolatból történt visszaállítása verziót.

> [!Note]  
> Alkalmazás-kompatibilis, kötetszintű és az operációs rendszer nélküli (BMR) visszaállítási lehetőségek várt következményekhez vezethet, és jelenleg nem támogatottak. A visszaállítási lehetőségek egy későbbi kiadásban támogatott.

### <a name="encryption-solutions"></a>Titkosítási megoldások
Titkosítási megoldások támogatása attól függ, hogyan használják azokat. Az Azure fájlszinkronizálás ismert használható:

- A BitLocker titkosítást
- Azure Information Protection, Azure Rights Management Services (az Azure RMS), és az Active Directory RMS

Azure fájl szinkronizálása nem működik az ismert:

- NTFS titkosított fájlrendszer (EFS)

Általában Azure fájlszinkronizálás támogatnia kell az együttműködési képesség, amely alatt a fájlrendszerben, például a BitLocker, elhelyezkedik titkosítási megoldásokkal és, amelyeket a rendszer a fájlformátumban, például a BitLocker megoldásokkal. Nincsenek speciális együttműködési megoldások, amelyek fölött a fájlrendszer (például NTFS EFS) elhelyezkedik azt.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Egyéb hierarchikus tárolás (HSM) megoldások
Egyéb HSM-megoldások Azure fájl szinkronizálással használható.

## <a name="region-availability"></a>Régiónkénti elérhetőség
Az Azure fájlszinkronizálás csak a következő régiókban Preview érhető el:

| Régió | Adatközpont helye |
|--------|---------------------|
| Kelet-Ausztrália | Új-Dél-Wales |
| Délkelet-Ausztrália | Victoria |
| Közép-Kanada | Toronto |
| Kelet-Kanada | Quebec City |
| USA középső régiója | Iowa |
| Kelet-Ázsia | Hongkong |
| USA keleti régiója | Virginia |
| Kelet-US2 | Virginia |
| Észak-Európa | Írország |
| Délkelet-Ázsia | Szingapúr |
| Az Egyesült Királyság déli régiója | London |
| Az Egyesült Királyság nyugati régiója | Cardiff |
| Nyugat-Európa | Hollandia |
| USA nyugati régiója | Kalifornia |

A képen támogatjuk a szinkronizálás csak a az Azure fájlmegosztások, amely ugyanabban a régióban, mint a tároló szinkronizálási szolgáltatás.

## <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>További lépések
* [Fontolja meg a tűzfal és a proxykiszolgáló beállításait](storage-sync-files-firewall-and-proxy.md)
* [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [Az Azure Files telepítése](storage-files-deployment-guide.md)
* [Az Azure File-szinkronizálás telepítése](storage-sync-files-deployment-guide.md)
