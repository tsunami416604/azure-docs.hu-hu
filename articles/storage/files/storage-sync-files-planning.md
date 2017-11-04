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
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: d626f71aa21cea562ef6c9554c05e6de027e7f4d
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Egy Azure fájlszinkronizálás (előzetes verzió) telepítésének tervezése
Az Azure File Sync (előzetes verzió) lehetővé teszi a szervezet Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy éri el, hogy átalakítja a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótáraivá. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

Ez az útmutató során megfontolandó tényezőkről Azure fájlszinkronizálás telepítése. Javasoljuk, hogy olvassa el [Azure fájlok központi telepítésének tervezésében](storage-files-planning.md) tesztelési útmutató. 

## <a name="understanding-azure-file-sync-terminology"></a>Azure fájlszinkronizálás terminológia ismertetése
Mielőtt továbblép az Azure fájlszinkronizálás részleteinek, fontos terminológia megismerése.

### <a name="storage-sync-service"></a>Tároló szinkronizálási szolgáltatás
A tárolási Sync szolgáltatás nem Azure fájlszinkronizálás képviselő legfelső szintű Azure-erőforrás. A tároló szinkronizálási szolgáltatás erőforrás a Tárfiók erőforrás társa, és is similarily telepíthető az Azure erőforrás-csoportok. A Tárfiók erőforrás különböző legfelső szintű erőforrás kell adni, mert a tároló szinkronizálási szolgáltatás szinkronizálási kapcsolat több tárfiókot több szinkronizálási csoportok segítségével hozhatja létre. Előfizetés rendelkezhet több tároló szinkronizálási szolgáltatás források.

### <a name="sync-group"></a>Szinkronizálási csoport
A szinkronizálás csoport adja meg a sync-topológiát a fájlokat. A szinkronizálási csoporton belüli végpontok folyamatosan szinkronban vannak egymással. Ha például van két különálló AFS felügyelni kívánt fájlokat, ehhez két szinkronizálási csoportok létrehozása, és adja hozzá a különböző végpontok az egyes. Egy tároló szinkronizálási szolgáltatás kell szinkronizálási csoportok tárolhatja.  

### <a name="registered-server"></a>Regisztrált kiszolgáló
A regisztrált kiszolgáló objektum által jelképezett a kiszolgáló (vagy a fürt) közötti megbízhatósági-kapcsolat és a tároló szinkronizálási szolgáltatást. Regisztrálhatja annyi kiszolgálókat tároló szinkronizálási szolgáltatás példányra van lehetősége. Azonban a kiszolgáló (vagy a fürt) csak regisztrálhatók egy tároló szinkronizálási szolgáltatás az adott időpontban.

### <a name="azure-file-sync-agent"></a>Azure fájl Sync-ügynök
Az Azure fájlszinkronizálás ügynök egy letölthető csomagot, mely lehetővé teszi, hogy a Windows Server szinkronizálni kell egy Azure-fájl megosztása nem. Az Azure fájlszinkronizálás ügynök három fő összetevőből áll: 
- **FileSyncSvc.exe**: a háttérben felelős Server végpontokon változásainak figyelését, és kezdeményezi az Azure-bA szinkronizálási munkamenetek Windows-szolgáltatás.
- **StorageSync.sys**: az Azure fájlszinkronizálás fájlrendszer szűrésére, az Azure Fileshoz felelős rétegezési fájlok (Ha a felhő rétegezéséhez engedélyezve van).
- **PowerShell parancsmagokat**: PowerShell-parancsmagok és a Microsoft.StorageSync Azure erőforrás-szolgáltató közötti kommunikáció során. Ezek találhatók a következő helyeken (alapértelmezett):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Kiszolgáló-végpont
Egy kiszolgáló végpont képviseli egy konkrét helyre a kiszolgálón regisztrált, például egy kiszolgáló vagy a kötet gyökerében az egyik mappájába. Több kiszolgáló végpont ugyanazon a köteten létezhet, ha a névterek nem átfedésben lévő, (pl. F:\sync1 és F:\sync2). Beállíthatja felhő rétegezési külön-külön kiszolgáló végpontok. Ha egy kiszolgáló-végpontként szinkronizálási csoport hozzáadása egy kiszolgálón található fájlokat egy meglévő állítja be, ezeket a fájlokat egyesül többi végpont a szinkronizálási csoportban található egyéb fájlok.

### <a name="cloud-endpoint"></a>Felhő végpont
A Felhővégpontnak egy Azure fájlmegosztás, amely a szinkronizálási csoport része. A teljes Azure File megosztás szinkronizálások és egy Azure fájlmegosztás csak lehet egy felhőalapú végpontja, ezért egy szinkronizálású csoport tagja. Ha egy Azure fájlmegosztás fájlok készlet ad hozzá egy felhő-végpontként szinkronizálási csoporthoz, ezeket a fájlokat egyesül többi végpont a szinkronizálási csoportban található egyéb fájlok.

> [!Important]  
> Azure fájlszinkronizálás támogatja a módosítások az Azure-fájl megosztása közvetlenül, azonban vegye figyelembe, hogy a Azure fájlmegosztás végrehajtott módosításokat először kell észlelhetők egy Azure fájlszinkronizálás módosítása észlelési feladat, amely csak a Felhőbeli végpont initatiated 24 óránként. Tekintse meg a [Azure fájlok gyakran ismételt kérdések](storage-files-faq.md#afs-change-detection) további információt.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés 
Felhő rétegezéséhez az opcionális szolgáltatása: az Azure fájlszinkronizálás, mely lehetővé teszi, hogy a ritkán használt vagy nem éri el fájlok helyezhető el a az Azure Fileshoz. Amikor egy fájl többszintű, Azure fájlszinkronizálás fájlrendszert szűréséhez (StorageSync.sys) cseréli a fájlt helyileg mutató, vagy újraelemzési pont URL-címet a fájlt a fájlok Azure képviselő. A rétegzett fájl, harmadik féltől származó alkalmazások rétegzett fájlok azonosíthassa az NTFS-sel, állítsa be a "kapcsolat nélküli" attribútummal rendelkezik. Ha a felhasználó megnyit egy rétegzett fájlt, az Azure fájlszinkronizálás zökkenőmentesen visszahívja a fájladatok Azure fájlokat anélkül, hogy a felhasználó ismeri a fájl nem tárolja a helyileg a rendszer. Ez a funkció hierarchikus tárolás (HSM) is nevezik.

## <a name="azure-file-sync-interoperability"></a>Az Azure File szinkronizálási együttműködés 
Ez a fejezet a Windows Server szolgáltatásaival és a szerepkörök és a harmadik féltől származó megoldások Azure fájlszinkronizálás együttműködés.

### <a name="supported-versions-of-windows-server"></a>Windows Server támogatott verziói
Jelenleg a Windows Server által Azure fájlszinkronizálás verziók a következők:

| Verzió | Támogatott termékváltozat | Támogatott üzembe helyezési lehetőségei |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter és Standard | Teljes (a felhasználói felület Server) |
| Windows Server 2012 R2 | Datacenter és Standard | Teljes (a felhasználói felület Server) |

A Windows Server későbbi verzióiban lesz hozzáadva, azok kiadásakor, valamint a Windows korábbi verziói a felhasználói visszajelzések alapján adhatók hozzá.

> [!Important]  
> Azt javasoljuk, hogy az összes Azure-fájl szinkronizálással naprakész, és a legújabb frissítéseket a Windows Update használt Windows-kiszolgálók. 

### <a name="file-system-features"></a>Fájlrendszer szolgáltatásokat
| Szolgáltatás | Támogatási állapota | Megjegyzések |
|---------|----------------|-------|
| Hozzáférés-vezérlési listák (ACL) | Teljes mértékben támogatott. | Windows ACL-ek által Azure fájlszinkronizálás megmaradnak, és a Windows Server Server végpontok érvényesíti. A Windows hozzáférés-vezérlési listák (még nem) Azure-fájlokat támogatja, ha a fájlok érhetők el közvetlenül a felhőben. |
| A rögzített hivatkozások | Kihagyva | |
| Szimbolikus hivatkozásokat | Kihagyva | |
| Csatlakoztatási pontok | Részlegesen támogatott | Csatlakoztatási pontok lehet, hogy a legfelső szintű egy kiszolgáló végpont, de kimarad, ha a kiszolgáló végpont névtérben található. |
| Elhelyezni pontokra | Kihagyva | |
| Újraelemzési pontok | Kihagyva | |
| NTFS-tömörítése | Teljes mértékben támogatott. | |
| Ritka fájlok | Teljes mértékben támogatott. | Szinkronizálja a ritka fájlokat (nincs letiltva), de szinkronizálva lesz a felhőbe egy teljes fájlként. Ha a fájl tartalmának módosítása a felhőben (vagy egy másik kiszolgálón), a fájl már nem lesz ritka, a módosítás, amikor |
| Alternatív adatfolyamok (ADS) | Megmarad, de nem szinkronizált | |

> [!Note]  
> Csak az NTFS-köteteket támogatottak.

### <a name="failover-clustering"></a>Feladatátvételi fürtszolgáltatás
Windows Server feladatátvételi fürtszolgáltatás támogatott Azure fájl szinkronizálás által "Általános felhasználású fájlkiszolgáló" rendszerbe állítási beállításként. Feladatátvételi fürtszolgáltatás nem támogatott a "Kibővített fájlkiszolgáló az alkalmazásadatokhoz" (SOFS) vagy a fürtözött megosztott kötetek (CSV).

> [!Note]  
> Az Azure fájlszinkronizálás ügynököt telepíteni kell egy feladatátvevő fürtben való szinkronizálás helyes működéséhez minden csomóponton.

### <a name="windows-server-data-deduplication"></a>Windows Server-Adatdeduplikáció
A kötetek adatdeduplikációval engedélyezett felhő nélkül a Azure fájlszinkronizálás támogatja az Adatdeduplikáció engedélyezése a köteten. Együttműködési közötti Azure fájl szinkronizálása a felhőalapú rétegezéséhez engedélyezett és az Adatdeduplikáció jelenleg nem támogatott.

### <a name="anti-virus-solutions"></a>Víruskereső megoldások
Víruskereső vizsgálja a fájlokat az ismert rosszindulatú kódot úgy működik, mert a víruskereső okozhat a rétegzett fájlok visszahívása. Mivel a rétegzett fájlokat a "kapcsolat nélküli" attribútummal rendelkezik, azt javasoljuk, a szoftvergyártó, a megoldás konfigurálása vonatkozó kapcsolat nélküli fájlok olvasásának kihagyását egyeztetett. 

A következő megoldásokat ismert, hogy támogatja a rendszer kihagyja a kapcsolat nélküli fájlok:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee végpont biztonsági](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (lásd a "Csak teendők vizsgálata" szakasz 90 oldalon)
- [Kaspersky víruskereső](https://support.kaspersky.com/4684)
- [Sophos az Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Biztonsági mentési megoldás
Víruskereső megoldások, például a biztonsági mentési megoldás okozhat a rétegzett fájlok visszahívása. Javasoljuk, hogy a felhő biztonsági mentési megoldás az Azure-fájlmegosztás biztonsági másolat használatával egy helyszíni biztonsági mentési megoldás használata helyett.

### <a name="encryption-solutions"></a>Titkosítási megoldások
Titkosítási megoldások támogatása attól függ, hogyan használják azokat. Az Azure fájlszinkronizálás ismert használható:

- A BitLocker titkosítást
- Azure RMS-hez (és az örökölt Active Directory RMS)

Azure fájl szinkronizálása nem működik az ismert:

- NTFS titkosított fájlrendszer (EFS)

Általános Azure fájlszinkronizálás titkosítási megoldás, amely alatt a fájlrendszerben, például a BitLocker és a megoldások fájlformátumban, például a BitLocker, megvalósított elhelyezkedik együttműködési képesnek kell lennie, de nincsenek különleges együttműködési történt, amely elhelyezkedik megoldásaival való együttműködési a fájlrendszer (például NTFS titkosított fájlrendszer) felett.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Egyéb hierarchikus tárolás (HSM) megoldások
Más hierarchikus Tárolókezelés megoldás nincs Azure-fájl szinkronizálással használható.

## <a name="region-availability"></a>Régiónkénti elérhetőség
Azure fájl szinkronizálása a következő régiókban Preview csak érhető el:

| Régió | Adatközpont helye |
|--------|---------------------|
| USA nyugati régiója | Kaliforniai, USA |
| Nyugat-Európa | Hollandia |
| Délkelet-Ázsia | Szingapúr |
| Kelet-Ausztrália | Új déli Walesben Ausztrália |

A képen egy Azure fájlmegosztás ugyanabban a régióban, mint a tároló szinkronizálási szolgáltatás szinkronban csak támogatott.

## <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Következő lépések
* [Az Azure-fájlok központi telepítésének tervezése](storage-files-planning.md)
* [Az Azure Files telepítése](storage-files-deployment-guide.md)
* [Az Azure File-szinkronizálás telepítése](storage-sync-files-deployment-guide.md)
