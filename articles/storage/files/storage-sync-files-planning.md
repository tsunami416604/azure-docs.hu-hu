---
title: "Egy Azure fájlszinkronizálás (előzetes verzió) telepítésének tervezése |} Microsoft Docs"
description: "További tudnivalók az Azure-fájlok központi telepítésének tervezése során megfontolandó tényezőkről."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: f2e7f93d2d2914399f3fc7b24a00540f1c045b58
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
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
Egy kiszolgáló végpont képviseli regisztrált kiszolgálón, például az egyik mappájába, egy kiszolgáló vagy a kötet gyökerében egy konkrét helyre. Több kiszolgáló végpont ugyanazon a köteten létezhet, ha a névterek nem lehetnek átfedésben (például F:\sync1 és F:\sync2). Beállíthatja felhő rétegezési egyenként az egyes kiszolgáló-végpont esetében. Ha egy kiszolgálón, amelyen egy meglévő fájlokat egy server végpontként szinkronizálási csoporthoz ad hozzá, azokat a fájlokat egyesítve lesznek az egyéb fájlokat, amelyek már megtalálhatók a szinkronizálási csoport végpontja.

> [!Note]  
> A kiszolgáló végpont helyezkedhet el a Windows rendszer kötetén. Felhő rétegezéséhez nem támogatott a rendszerköteten.

### <a name="cloud-endpoint"></a>Felhő végpont
A felhő-végpont esetében az Azure fájlmegosztások, amely a szinkronizálási csoport része. A teljes fájl Azure fájlmegosztás szinkronizálások és az Azure fájlmegosztások csak egy felhőhöz végpont tagja lehet. Ezért az Azure fájlmegosztások lehet csak egyetlen szinkronizálási csoport tagja. Ha az Azure fájlmegosztások, amely rendelkezik egy meglévő fájlkészlet a felhővégpontnak szinkronizálási csoporthoz ad hozzá, a meglévő fájlokat egyesítve lesznek az egyéb fájlokat, amelyek már megtalálhatók a szinkronizálási csoport végpontja.

> [!Important]  
> Az Azure fájlszinkronizálás támogatja közvetlenül az Azure fájlmegosztások hajt végre módosításokat. Azonban a Azure fájlmegosztás végrehajtott módosítások először kell egy Azure fájlszinkronizálás módosítás észlelése észlelhetők. A módosítás észlelése a felhőbeli végpont 24 óránként csak egyszer indítható. További információkért lásd: [Azure fájlok gyakran ismételt kérdések](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Felhőbeli rétegzés 
Felhő rétegezéséhez, amelyben a ritkán használt vagy a használt fájlokat is helyezhető el a az Azure Fileshoz Azure fájlszinkronizálás választható lehetőség. Ha egy fájl többszintű, Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys) lecseréli a fájlt helyileg mutatót, vagy újraelemzési pont. Az újraelemzési pont Azure fájlok a fájl URL-címet jelöli. A rétegzett fájl beállítani az NTFS-sel, harmadik féltől származó alkalmazások azonosíthassa a rétegzett fájlokat a "kapcsolat nélküli" attribútummal rendelkezik. Ha a felhasználó megnyit egy rétegzett fájlt, Azure fájlszinkronizálás zökkenőmentesen visszahívja az Azure-fájlokból a fájladatok anélkül, hogy a felhasználó ismeri az, hogy a fájl nem található helyileg a rendszeren. Ez a funkció hierarchikus tárolás (HSM) is nevezik.

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
| Alternatív adatfolyamok (ADS) | Megmarad, de nem szinkronizált | |

> [!Note]  
> Csak az NTFS-köteteket támogatottak. A refs fájlrendszer, FAT, FAT32 és más fájlrendszerek nem támogatottak.

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

### <a name="antivirus-solutions"></a>Víruskereső megoldások
A víruskereső vizsgálja a fájlokat az ismert rosszindulatú kódot úgy működik, mert egy víruskereső szoftver a rétegzett fájlok visszahívása okozhatja. Mivel rétegzett fájlokat a "kapcsolat nélküli" attribútummal rendelkezik, ajánlott a szoftver gyártójához annak megismerése, hogyan konfigurálhatja a kapcsolat nélküli fájlok olvasásának kihagyását megoldás egyeztetett. 

A következő megoldásokat ismert, hogy támogatja a rendszer kihagyja a kapcsolat nélküli fájlok:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee végpont biztonsági](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (lásd a "Vizsgálata csak mit kell" a PDF 90 lapján)
- [Kaspersky víruskereső](https://support.kaspersky.com/4684)
- [Sophos az Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Biztonsági mentési megoldás
Víruskereső megoldások, például a biztonsági mentési megoldás a rétegzett fájlok visszahívása okozhat. Azt javasoljuk, hogy a felhő biztonsági mentési megoldás segítségével készítsen biztonsági másolatot az Azure-fájlmegosztáshoz egy helyszíni biztonsági mentési termék helyett.

### <a name="encryption-solutions"></a>Titkosítási megoldások
Titkosítási megoldások támogatása attól függ, hogyan használják azokat. Az Azure fájlszinkronizálás ismert használható:

- A BitLocker titkosítást
- Az Azure Rights Management Services (Azure RMS) (és az örökölt Active Directory RMS)

Azure fájl szinkronizálása nem működik az ismert:

- NTFS titkosított fájlrendszer (EFS)

Általában Azure fájlszinkronizálás támogatnia kell az együttműködési képesség, amely alatt a fájlrendszerben, például a BitLocker, elhelyezkedik titkosítási megoldásokkal és, amelyeket a rendszer a fájlformátumban, például a BitLocker megoldásokkal. Nincsenek speciális együttműködési megoldások, amelyek fölött a fájlrendszer (például NTFS EFS) elhelyezkedik azt.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Egyéb hierarchikus tárolás (HSM) megoldások
Egyéb HSM-megoldások Azure fájl szinkronizálással használható.

## <a name="region-availability"></a>Régiónkénti elérhetőség
Az Azure fájlszinkronizálás csak a következő régiókban Preview érhető el:

| Régió | Adatközpont helye |
|--------|---------------------|
| USA nyugati régiója | Kaliforniai, USA |
| Nyugat-Európa | Hollandia |
| Délkelet-Ázsia | Szingapúr |
| Kelet-Ausztrália | Új déli Walesben Ausztrália |

A képen támogatjuk a szinkronizálás csak a az Azure fájlmegosztások, amely ugyanabban a régióban, mint a tároló szinkronizálási szolgáltatás.

## <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Következő lépések
* [Az Azure-fájlok központi telepítésének tervezése](storage-files-planning.md)
* [Az Azure Files telepítése](storage-files-deployment-guide.md)
* [Az Azure File-szinkronizálás telepítése](storage-sync-files-deployment-guide.md)
