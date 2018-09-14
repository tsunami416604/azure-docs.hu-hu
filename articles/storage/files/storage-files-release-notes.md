---
title: Az Azure File Sync ügynök kibocsátási megjegyzései |} A Microsoft Docs
description: Az Azure File Sync ügynök kibocsátási megjegyzései.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 08/30/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: e818e7ff3b9e312c640e1901d05706b97dbff8d0
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579106"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Az Azure File Sync ügynök kibocsátási megjegyzései
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. A Windows Server-telepítéseket az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
Az Azure File Sync ügynök alábbi verziói támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | status |
|----|----------------------|--------------|------------------|
| Augusztus. kumulatív frissítés | 3.2.0.0 | 2018. augusztus 15. | Támogatott (ajánlott verzió) |
| Általánosan elérhető | 3.1.0.0-s | 2018. július 19. | Támogatott |
| Június. kumulatív frissítés | 3.0.13.0 | 2018. június 29. | Ügynök verziója 2018. október 1-én jár le |
| 2 frissítése | 3.0.12.0 | 2018. május 22. | Ügynök verziója 2018. október 1-én jár le |
| Április. kumulatív frissítés | 2.3.0.0-s | 2018. május 8. | Ügynök verziója 2018. október 1-én jár le |
| Március kumulatív frissítés | 2.2.0.0 | 2018. március 12. | Ügynök verziója 2018. október 1-én jár le |
| Február. kumulatív frissítés | 2.1.0.0 | 2018. február 28. | Ügynök verziója 2018. október 1-én jár le |
| 1 frissítése | 2.0.11.0 | 2018. február 8. | Ügynök verziója 2018. október 1-én jár le |
| Január. kumulatív frissítés | 1.4.0.0 | 2018. január 8. | Ügynök verziója 2018. október 1-én jár le |
| . Novemberi kumulatív frissítés | 1.3.0.0 | 2017. november 30. | Ügynök verziója 2018. október 1-én jár le |
| . Októberi kumulatív frissítés | 1.2.0.0 | 2017. október 31. | Ügynök verziója 2018. október 1-én jár le |
| Kezdeti előzetes kiadás | 1.1.0.0 | 2017. szeptember 26. | Ügynök verziója 2018. október 1-én jár le |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-3200"></a>Ügynök verziója 3.2.0.0
A következő kiadási megjegyzések 3.2.0.0 az Azure File Sync ügynök verziója kiadás dátuma: 2018. augusztus 15 vonatkoznak. Ezek a megjegyzések kibocsátási megjegyzéseinek kiegészítéséül verzió 3.1.0.0-s kívül vannak.

Ebben a kiadásban a következő javítás tartalmazza:
- Szinkronizálás meghiúsul, és kevés a memória (0x8007000e) miatt memóriavesztés

## <a name="agent-version-3100"></a>Ügynök verziója 3.1.0.0-s
A következő kiadási megjegyzések 3.1.0.0-s az Azure File Sync ügynök verziója (kiadás dátuma: 2018. július 19.) vonatkoznak.

### <a name="evaluation-tool"></a>Kiértékelési eszközével
Az Azure File Sync üzembe helyezése előtt, ki kell értékelni a rendszer az Azure File Sync értékelési eszközzel kompatibilis-e. Ez az eszköz az AzureRM PowerShell-parancsmag, amely ellenőrzi, a fájlrendszert és adatkészlethez, például a nem támogatott karaktereket vagy nem támogatott operációsrendszer-verzió a potenciális problémákat. Telepítés és használat utasításokat: [Megoldásértékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) az útmutató a tervezési szakaszban. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Telepítése és konfigurálása az Azure File Sync ügynök Windows Serverrel kapcsolatos további információkért lásd: [Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md) és [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md).

- Az ügynöktelepítési csomagot emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök a Windows Server Core vagy Nano Server üzemelő példányain nem támogatott.
- Az ügynök csak a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynökhöz legalább 2 GB fizikai memóriára van szükség.
- A Storage Sync-ügynök (FileSyncSvc) szolgáltatás nem támogatja a kiszolgálói végpontot, amely rendelkezik a rendszer mennyiségi információk (SVI) könyvtár tömörített köteten található. Ez a konfiguráció váratlan eredményekhez vezet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információkért lásd: [hibaelhárítása az Azure File Sync](storage-sync-files-troubleshoot.md).
- Ne használja a Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrőit vagy egyéb fájlszűrőket. A fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha blokkolnak egyes fájlokat.
- A sysprep futtatása a kiszolgálón, amely az Azure File Sync-ügynök van telepítve, nem támogatott, és váratlan eredményekhez vezethet. Az ügynök telepítése és a kiszolgáló regisztrálása a kiszolgálói lemezkép központi telepítése és a sysprep minitelepítő befejezése után történjen.
- Az adatdeduplikáció és a felhőrétegzés ugyanazon a köteten nem támogatott.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- A 2048 karakternél hosszabb elérési utak.
- A biztonsági leíró tulajdonosi hozzáférés-vezérlési lista (DACL) része, ha az 2 KB-nál nagyobb. (Ez csak akkor probléma, ha több mint 40 hozzáférés-vezérlési bejegyzés (ACE) van egyetlen elemen.)
- Biztonsági leíró naplózáshoz használt rendszerhozzáférés-vezérlési lista (SACL) része.
- Kiterjesztett attribútumok.
- Alternatív adatstreamek.
- Újraelemzési pontok.
- Rögzített hivatkozások.
- A tömörítés (ha kiszolgálófájlon van beállítva) nem marad meg, amikor a módosításokat erre a fájlra szinkronizálja más végpontokból.
- Az EFS (vagy más felhasználói módú) titkosítással titkosított összes fájl, amely meggátolja, hogy a szolgáltatás beolvassa az adatokat.

    > [!Note]  
    > Az Azure File Sync mindig titkosítja az átvitt adatokat. Az adatok inaktív állapotban is mindig titkosítva vannak az Azure-ban.
 
### <a name="server-endpoint"></a>Kiszolgálói végpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- Rétegzett fájlok használhatatlanná válnak, ha a fájlok nem kell idézni a kiszolgálói végpont törlése előtt.
- Felhőbeli rétegezés nem támogatott a rendszerköteten. Kiszolgálói végpont létrehozása a rendszerköteten, tiltsa le a felhőbeli rétegezés a kiszolgálói végpont létrehozása során.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálóvégponton.
- A kiszolgáló nevét a portál nem frissül, ha a kiszolgáló neve. A kiszolgáló nevét a portál frissítéséhez regisztrációját, és regisztrálja újra a kiszolgálót.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Az Azure File Sync közvetlenül támogatja az Azure-fájlmegosztás módosítását. Az Azure-fájlmegosztás végzett módosítások azonban először kell deríteni az az Azure File Sync módosítása észlelése. A módosítás észlelési feladat a felhőbeli végpont 24 óránként indul. Emellett az Azure-fájlmegosztások a REST protokoll használatával végrehajtott módosítások nem fogja frissíteni az SMB-utolsó módosítás időpontja, és nem látható, a módosítás az sync.
- A társzinkronizálási szolgáltatás és/vagy a storage-fiókot is áthelyezhető másik erőforráscsoportba vagy előfizetésbe. Ha a tárfiókot, a hibrid File Sync szolgáltatásbeli hozzáférést a tárfiókhoz kell (lásd: [biztosítása érdekében az Azure File Sync hozzáfér a tárfiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.

## <a name="agent-version-30130"></a>Ügynök verziója 3.0.13.0
A következő kiadási megjegyzések 3.0.13.0 az Azure File Sync ügynök verziója kiadás dátuma: 2018. június 29 vonatkoznak. Ezek a megjegyzések kibocsátási megjegyzéseinek kiegészítéséül verzió 3.0.12.0 kívül vannak.

Ebben a kiadásban a következő javítás tartalmazza:
- Az újraelemzési pontok szinkronizálása meghiúsul, ha egy kiszolgáló egy meglévő szinkronizálási csoporthoz való hozzáadása, ha a kiszolgálói végpont helye a kiszolgálón található.

## <a name="agent-version-30120"></a>Ügynök verziója 3.0.12.0
Az alábbi kibocsátási megjegyzések olyan verziójára 3.0.12.0 az Azure File Sync ügynök (2018. május 22).

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Telepítése és konfigurálása az Azure File Sync ügynök Windows Serverrel kapcsolatos további információkért lásd: [Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md) és [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md).

- Az ügynöktelepítési csomagot emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök a Windows Server Core vagy Nano Server üzemelő példányain nem támogatott.
- Az ügynök csak a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynökhöz legalább 2 GB fizikai memóriára van szükség.
- A Storage Sync-ügynök (FileSyncSvc) szolgáltatás nem támogatja a kiszolgálói végpontot, amely rendelkezik a rendszer mennyiségi információk (SVI) könyvtár tömörített köteten található. Ez a konfiguráció váratlan eredményekhez vezet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információkért lásd: [hibaelhárítása az Azure File Sync](storage-sync-files-troubleshoot.md).
- Ne használja a Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrőit vagy egyéb fájlszűrőket. A fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha blokkolnak egyes fájlokat.
- A sysprep futtatása a kiszolgálón, amely az Azure File Sync-ügynök van telepítve, nem támogatott, és váratlan eredményekhez vezethet. Az ügynök telepítése és a kiszolgáló regisztrálása a kiszolgálói lemezkép központi telepítése és a sysprep minitelepítő befejezése után történjen.
- Az adatdeduplikáció és a felhőrétegzés ugyanazon a köteten nem támogatott.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- A 2048 karakternél hosszabb elérési utak.
- A biztonsági leíró tulajdonosi hozzáférés-vezérlési lista (DACL) része, ha az 2 KB-nál nagyobb. (Ez csak akkor probléma, ha több mint 40 hozzáférés-vezérlési bejegyzés (ACE) van egyetlen elemen.)
- Biztonsági leíró naplózáshoz használt rendszerhozzáférés-vezérlési lista (SACL) része.
- Kiterjesztett attribútumok.
- Alternatív adatstreamek.
- Újraelemzési pontok.
- Rögzített hivatkozások.
- A tömörítés (ha kiszolgálófájlon van beállítva) nem marad meg, amikor a módosításokat erre a fájlra szinkronizálja más végpontokból.
- Az EFS (vagy más felhasználói módú) titkosítással titkosított összes fájl, amely meggátolja, hogy a szolgáltatás beolvassa az adatokat. 
    
    > [!Note]  
    > Az Azure File Sync mindig titkosítja az átvitt adatokat. Az adatok inaktív állapotban is mindig titkosítva vannak az Azure-ban.
 
### <a name="server-endpoints"></a>Kiszolgálóvégpontok
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- Felhőbeli rétegezés nem támogatott a rendszerköteten. Kiszolgálói végpont létrehozása a rendszerköteten, tiltsa le a felhőbeli rétegezés a kiszolgálói végpont létrehozása során.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálóvégponton.
- Rétegzett fájlok használhatatlanná válnak, ha a fájlok nem kell idézni a kiszolgálói végpont törlése előtt.
 
### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.

## <a name="agent-version-2300"></a>Az ügynök 2.3.0.0-s
A következő kiadási megjegyzések 2.3.0.0-s az Azure File Sync ügynök kiadás dátuma: 2018. május 8. a rendszer. Ezek a megjegyzések 2.0.11.0-s verzió kibocsátási megjegyzéseinek kiegészítéséül szolgálnak.

Ebben a kiadásban a következő javításokat tartalmazza:
- Ügynökfrissítések előfordulhat, a felhő rétegezési szűrő-illesztőprogram nem távolítja el.
- Szinkronizálás teljesítménye csökkenhet, nagy mennyiségű fájlok szinkronizálása során.

## <a name="agent-version-2200"></a>Ügynök verziója 2.2.0.0
A következő kiadási megjegyzések 2.2.0.0 az Azure File Sync ügynök verziója kiadás dátuma: 2018. március 12. vonatkoznak.  Ezek a megjegyzések felsorolt 2.1.0.0 és 2.0.11.0-s verzió kibocsátási megjegyzéseinek vannak

Egyes ügyfelek számára v2.1.0.0 telepítését a FileSyncSvc nem leállítása miatt meghiúsul. Ez a frissítés megoldja ezt a problémát.

## <a name="agent-version-2100"></a>Az ügynök 2.1.0.0-s verziója
Az alábbi kibocsátási megjegyzések az Azure File Sync ügynök 2.1.0.0-s verziójára vonatkoznak, amely 2018. február 28-án jelent meg. Ezek a megjegyzések 2.0.11.0-s verzió kibocsátási megjegyzéseinek kiegészítéséül szolgálnak.

Ez a kiadás az alábbi újításokat tartalmazza:
- A fürtök feladatátvételének kezelésére vonatkozó fejlesztések.
- Rétegzett fájlok megbízható kezelésére vonatkozó fejlesztések.
- Az ügynök olyan tartományvezérlő gépeken való telepítésének támogatása, amelyek Windows Server 2008 R2 tartományi környezetben lettek felvéve.
- Javítva a kiadásban: túlzott mennyiségű diagnosztikai adat létrejöttének megelőzése sok fájlt tartalmazó kiszolgálók esetén.
- A munkamenethibák kezelésére vonatkozó fejlesztések.
- A fájlátviteli hibák kezelésére vonatkozó fejlesztések.
- Módosítva a kiadásban: a felhőrétegzés alapértelmezett futtatási időköze mostantól 1 óra, ha engedélyezve van a kiszolgálóvégponton. 
- Ideiglenes blokkolási hiba: az Azure File Sync- (társzinkronizálási szolgáltatási) erőforrások új Azure-előfizetésre történő áthelyezése.

## <a name="agent-version-20110"></a>Az ügynök 2.0.11.0-s verziója
Az alábbi kibocsátási megjegyzések az Azure File Sync ügynök 2.0.11.0-s verziójára vonatkoznak (2018. február 9-én jelent meg). 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Telepítése és konfigurálása az Azure File Sync ügynök Windows Serverrel kapcsolatos további információkért lásd: [Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md) és [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md).

- Az ügynöktelepítési csomagot (MSI) emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök a Windows Server Core vagy a Nano Server üzemelő példányain nem támogatott.
- Az ügynök csak a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynökhöz legalább 2 GB fizikai memóriára van szükség.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információkért lásd: [hibaelhárítása az Azure File Sync](storage-sync-files-troubleshoot.md).
- Ez a kiadás a DFS-R támogatásával bővült. További információt a [tervezési útmutatóban](storage-sync-files-planning.md#distributed-file-system-dfs) talál.
- Ne használja a Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrőit vagy egyéb fájlszűrőket. A fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha blokkolnak egyes fájlokat.
- A regisztrált kiszolgálók duplikálása (a virtuális gép klónozását is beleértve) váratlan eredményekhez vezethet. Előfordulhat, hogy a szinkronizálás sosem szerveződik át.
- Az adatdeduplikáció és a felhőrétegzés ugyanazon a köteten nem támogatott.
 
### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- A 2048 karakternél hosszabb elérési utak.
- A biztonsági leíró tulajdonosi hozzáférés-vezérlési lista (DACL) része, ha az 2 KB-nál nagyobb. (Ez csak akkor probléma, ha több mint 40 hozzáférés-vezérlési bejegyzés (ACE) van egyetlen elemen.)
- Biztonsági leíró naplózáshoz használt rendszerhozzáférés-vezérlési lista (SACL) része.
- Kiterjesztett attribútumok.
- Alternatív adatstreamek.
- Újraelemzési pontok.
- Rögzített hivatkozások.
- A tömörítés (ha kiszolgálófájlon van beállítva) nem marad meg, amikor a módosításokat erre a fájlra szinkronizálja más végpontokból.
- Az EFS (vagy más felhasználói módú) titkosítással titkosított összes fájl, amely meggátolja, hogy a szolgáltatás beolvassa az adatokat. 
    
    > [!Note]  
    > Az Azure File Sync mindig titkosítja az átvitt adatokat. Az adatok inaktív állapotban is mindig titkosítva vannak az Azure-ban.
 
### <a name="server-endpoints"></a>Kiszolgálóvégpontok
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- Kiszolgálóvégpontok nem lehetnek a rendszerköteten. A C:\MyFolder például nem elfogadható elérési út, ha a C:\MyFolder nem csatlakoztatási pont.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ez a kiadás annak támogatásával bővült, hogy a szinkronizálás gyökere egy kötet gyökerénél legyen.
- Ne tárolja operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálóvégponton.
- Ebben a kiadásban új események lettek hozzáadva a felhőrétegzés összes futásidejének követéséhez (9016-os eseményazonosító), a feltöltési folyamat szinkronizálásához (9302-es eseményazonosító) és a nem szinkronizált fájlokhoz (9900-as eseményazonosító).
- Továbbfejlesztett ebben a kiadásban: 
- Jelentős mértékben nőtt a DR névtér gyors szinkronizálási teljesítménye.
- Nagy számú (több mint 10 000) könyvtár törlése nem kell a v2 * kötegekben történik.
 
### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Az előző verzióhoz képest az új fájlokat 1 órán belül rétegzi a rendszer a rétegzési szabályzat beállításainak megfelelően (ez korábban 32 óra volt). PowerShell-parancsmagot biztosítunk az igény szerinti rétegezéshez. Ezen parancsmag segítségével hatékonyabban értékelheti ki a rétegzést anélkül, hogy várnia kellene a háttérfolyamatokra.
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.
- Az előző verzióhoz képest a fájlok mostantól rétegezett fájlokként tölthetők le más kiszolgálókra, ha a fájl új vagy már rétegezett fájl volt.

## <a name="agent-version-1100"></a>Az ügynök 1.1.0.0-s verziója
A következő kibocsátási megjegyzések az Azure File Sync ügynök 1.1.0.0-s verziójára vonatkoznak (2017. szeptember 9-én kiadott, kezdeti előzetes verzió). 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Telepítése és konfigurálása az Azure File Sync ügynök Windows Serverrel kapcsolatos további információkért lásd: [Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md) és [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md).

- Az ügynöktelepítési csomagot (MSI) emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök a Windows Server Core vagy a Nano Server üzemelő példányain nem támogatott.
- Az ügynök csak a Windows Server 2016 és 2012 R2 rendszeren támogatott.
- Az ügynökhöz legalább 2 GB fizikai memóriára van szükség.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információkért lásd: [hibaelhárítása az Azure File Sync](storage-sync-files-troubleshoot.md).
- Ne használja az FSRM fájlszűrőit vagy egyéb fájlszűrőket. A fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha blokkolnak egyes fájlokat.
- A regisztrált kiszolgálók duplikálása (a virtuális gép klónozását is beleértve) váratlan eredményekhez vezethet. Előfordulhat, hogy a szinkronizálás sosem szerveződik át.
- Az adatdeduplikáció és a felhőrétegzés ugyanazon a köteten nem támogatott.
 
### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- A 2048 karakternél hosszabb elérési utak.
- A biztonsági leíró DACL része, ha 2 KB-nál nagyobb. (Ez csak akkor probléma, ha több mint 40 hozzáférés-vezérlési bejegyzés van egyetlen elemen.)
- Biztonsági leíró naplózáshoz használt SACL része.
- Kiterjesztett attribútumok.
- Alternatív adatstreamek.
- Újraelemzési pontok.
- Rögzített hivatkozások.
- A tömörítés (ha kiszolgálófájlon van beállítva) nem marad meg, amikor a módosításokat erre a fájlra szinkronizálja más végpontokból.
- Az EFS (vagy más felhasználói módú) titkosítással titkosított összes fájl, amely meggátolja, hogy a szolgáltatás beolvassa az adatokat. 
    
    > [!Note]  
    > Az Azure File Sync mindig titkosítja az átvitt adatokat. Az adatok inaktív állapotban is mindig titkosítva vannak az Azure-ban.
 
### <a name="server-endpoints"></a>Kiszolgálóvégpontok
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- Kiszolgálóvégpontok nem lehetnek a rendszerköteten. A C:\MyFolder például nem elfogadható elérési út, ha a C:\MyFolder nem csatlakoztatási pont.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, CSV-kkel nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ha egyszerre nagy mennyiségű címtárat töröl egy kiszolgálóról (több mint 10 000-et), az szinkronizálási hibákat okozhat. 10 000 címtárasnál kisebb kötegekben törölje a címtárakat. A következő köteg törlése előtt győződjön meg arról, hogy a törlési műveletek sikeresen szinkronizálva lettek.
- A kötetek gyökerénél lévő kiszolgálói végpont jelenleg nem támogatott.
- Ne tárolja operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálóvégponton.
 
### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- A fájlok megfelelő visszahívásának biztosítása érdekében előfordulhat, hogy a rendszer akár 32 óráig nem rétegzi automatikusan az új vagy módosított fájlokat. Ebbe a folyamatba beletartozik az új kiszolgálói végpontok konfigurálása utáni első rétegzés. PowerShell-parancsmagot biztosítunk az igény szerinti rétegezéshez. Ezen parancsmag segítségével hatékonyabban értékelheti ki a rétegzést anélkül, hogy várnia kellene a háttérfolyamatokra.
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.
