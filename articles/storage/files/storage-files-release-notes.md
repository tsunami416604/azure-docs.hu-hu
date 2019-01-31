---
title: Az Azure File Sync ügynök kibocsátási megjegyzései |} A Microsoft Docs
description: Az Azure File Sync ügynök kibocsátási megjegyzései.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 1/14/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 8f6eff1c2bd1490471256873339665ef1be92c6c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475040"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Az Azure File Sync ügynök kibocsátási megjegyzései
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. A Windows Server-telepítéseket az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
Az Azure File Sync ügynök alábbi verziói támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | status |
|----|----------------------|--------------|------------------|
| Január a 2019 -. kumulatív frissítés [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 2019. január 14. | Támogatott (ajánlott verzió) |
| 2018. decemberi kumulatív - [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 2018. december 10. | Támogatott |
| A 2018. december kumulatív frissítés | 4.1.0.0 | 2018. december 4. | Támogatott |
| V4 kiadás | 4.0.1.0 | 2018. november 13. | Támogatott |
| 2018 szeptember. kumulatív frissítés | 3.3.0.0 | 2018. szeptember 24. | Támogatott |
| 2018 augusztus. kumulatív frissítés | 3.2.0.0 | 2018. augusztus 15. | Támogatott |
| Általános elérhetőség | 3.1.0.0 | 2018. július 19. | Támogatott |
| Lejárt ügynökök | 1.1.0.0 - 3.0.13.0 | – | Ügynökök verzióinak lejárt nem támogatott – 2018. október 1. |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-4300"></a>Ügynök verziója 4.3.0.0
Az alábbi kibocsátási megjegyzések olyan verziójára 4.3.0.0 az Azure File Sync ügynök 2019. január 14. Ezek a megjegyzések kibocsátási megjegyzéseinek kiegészítéséül verzió 4.0.1.0 kívül vannak.

Ebben a kiadásban megoldott problémák listája:  
- Az Azure File Sync ügynök verzióra való frissítés után nem számítógépen rétegzett fájlok 4.x.
- AfsUpdater.exe mostantól támogatott a Windows Server verzióját 2019.
- Szinkronizálási szolgáltatás egyéb megbízhatóság fejlesztései. 

## <a name="agent-version-4200"></a>Ügynök verziója 4.2.0.0
Az alábbi kibocsátási megjegyzések olyan verziójának 4.2.0.0 az Azure File Sync ügynök kiadás dátuma: 2018. December 10. Ezek a megjegyzések kibocsátási megjegyzéseinek kiegészítéséül verzió 4.0.1.0 kívül vannak.

Ebben a kiadásban megoldott problémák listája:  
- Leállási hiba 0x3B vagy 0x1E leállási hiba fordulhat elő, amikor a VSS-pillanatkép jön létre.  
- Memóriavesztés fordulhat elő, amikor felhőbeli rétegezés engedélyezve van  

## <a name="agent-version-4100"></a>Ügynök verziója 4.1.0.0
A következő kiadási megjegyzések 4.1.0.0 az Azure File Sync ügynök verziója kiadás dátuma: 2018. December 4 vonatkoznak. Ezek a megjegyzések kibocsátási megjegyzéseinek kiegészítéséül verzió 4.0.1.0 kívül vannak.

Ebben a kiadásban megoldott problémák listája:  
- A kiszolgáló felhőbeli rétegezés memóriavesztés miatt válhat nem válaszol.  
- Ügynök telepítése meghiúsul a következő hibával: Hiba történt a 1921. Nem sikerült leállítani a szolgáltatást a "Storage szinkronizálási ügynök" (FileSyncSvc).  Győződjön meg arról, hogy van-e megfelelő jogosultsággal a rendszerszolgáltatások leállításához.  
- A Storage Sync-ügynök (FileSyncSvc) szolgáltatás összeomolhat, ha túl magas a memóriahasználat.  
- Vegyes megbízhatóság fejlesztései a felhőbeli rétegezés és szinkronizálása.

## <a name="agent-version-4010"></a>Ügynök verziója 4.0.1.0
A következő kiadási megjegyzések 4.0.1.0 az Azure File Sync ügynök verziója (kiadás dátuma: 2018. November 13.) vonatkoznak.

### <a name="evaluation-tool"></a>Kiértékelési eszközével
Az Azure File Sync üzembe helyezése előtt, ki kell értékelni a rendszer az Azure File Sync értékelési eszközzel kompatibilis-e. Ezt az eszközt az Azure PowerShell-parancsmagot, amely ellenőrzi a potenciális problémákat, a fájlrendszert és adatkészlethez, például a nem támogatott karaktereket vagy nem támogatott operációsrendszer-verzió. Telepítés és használat utasításokat: [Megoldásértékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) az útmutató a tervezési szakaszban. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Telepítése és konfigurálása az Azure File Sync ügynök Windows Serverrel kapcsolatos további információkért lásd: [Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md) és [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md).

- Az ügynöktelepítési csomagot emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök a Windows Server Core vagy Nano Server üzemelő példányain nem támogatott.
- Az ügynök csak Windows Server 2019, Windows Server 2016 és Windows Server 2012 R2 támogatott.
- Az ügynök legalább 2 GB memóriát igényel. Ha a kiszolgáló a dinamikus memória engedélyezve van a virtuális gépen fut, a virtuális gép memória egy minimális 2048 MiB kell konfigurálni.
- A Storage Sync-ügynök (FileSyncSvc) szolgáltatás nem támogatja a kiszolgálói végpontot, amely rendelkezik a rendszer mennyiségi információk (SVI) könyvtár tömörített köteten található. Ez a konfiguráció váratlan eredményekhez vezet.
- Leállási hiba 0x3B vagy 0x1E leállási hiba fordulhat elő, amikor a VSS-pillanatkép jön létre.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információkért lásd: [hibaelhárítása az Azure File Sync](storage-sync-files-troubleshoot.md).
- Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhat, ha a fájlok le vannak tiltva, a fájl képernyő miatt.
- A sysprep futtatása a kiszolgálón, amely az Azure File Sync-ügynök van telepítve, nem támogatott, és váratlan eredményekhez vezethet. Az Azure File Sync ügynök központi telepítése a server-rendszerképet, és a sysprep minitelepítő befejezése után kell telepíteni.
- Az adatdeduplikáció és a felhőrétegzés ugyanazon a köteten nem támogatott.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Fájlok nem támogatott karaktereket. Lásd: [hibaelhárítási útmutatója](storage-sync-files-troubleshoot.md#handling-unsupported-characters) nem támogatott karakterek listáját.
- A fájlok vagy könyvtárak végződhet.
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
- Rétegzett fájlok elérhetetlenné válik, ha a fájlok nem kell idézni a kiszolgálói végpont törlése előtt. Hozzáférés a fájlok visszaállításához hozza létre újra a kiszolgálói végpontot. Ha a 30 nap telt, mivel a kiszolgálói végpontot törölték, vagy a felhőbeli végpont törlése, a rétegzett fájlok, amelyek nem voltak idézni használhatatlan lesz.
- Felhőbeli rétegezés nem támogatott a rendszerköteten. Kiszolgálói végpont létrehozása a rendszerköteten, tiltsa le a felhőbeli rétegezés a kiszolgálói végpont létrehozása során.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpont helye belül.
- A kiszolgáló nevét a portál nem frissül, ha a kiszolgáló neve. A kiszolgáló nevét a portál frissítéséhez regisztrációját, és regisztrálja újra a kiszolgálót.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Az Azure File Sync közvetlenül támogatja az Azure-fájlmegosztás módosítását. Az Azure-fájlmegosztás végzett módosítások azonban először kell deríteni az az Azure File Sync módosítása észlelése. A módosítás észlelési feladat a felhőbeli végpont 24 óránként indul. Emellett az Azure-fájlmegosztások a REST protokoll használatával végrehajtott módosítások nem fogja frissíteni az SMB-utolsó módosítás időpontja, és nem látható, a módosítás az sync.
- A társzinkronizálási szolgáltatás és/vagy a storage-fiókot is áthelyezhető másik erőforráscsoportba vagy előfizetésbe meglévő Azure AD-bérlőn belül. Ha a tárfiókot, a hibrid File Sync szolgáltatásbeli hozzáférést a tárfiókhoz kell (lásd: [biztosítása érdekében az Azure File Sync hozzáfér a tárfiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Az Azure File Sync nem támogatja az előfizetés áthelyezése egy másik Azure AD-bérlővel.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Adja meg a fájlokat, ha elérhető a megadott számú napokon történt gyorsítótárazza a dátum-alapú felhőbeli rétegezés házirend-beállítás szolgál. További tudnivalókért lásd: [Felhőbeli Rétegezés áttekintése](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Másolja a fájlt a robocopy használatával, ha használja a/MIR lehetőséget a fájl időbélyegeket megőrzése érdekében. Ez biztosítja, elért fájlokat hamarabb számítógépen rétegzett régebbi fájlokat.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.

## <a name="agent-version-3300"></a>Ügynök verziója 3.3.0.0
A következő kiadási megjegyzések 3.3.0.0 az Azure File Sync ügynök verziója kiadás dátuma: 2018. szeptember 24 vonatkoznak. Ezek a megjegyzések kibocsátási megjegyzéseinek kiegészítéséül verzió 3.1.0.0-s kívül vannak.

Ebben a kiadásban megoldott problémák listája:
- Regisztrált kiszolgáló állapota "Offline jelenik meg" az Azure File Sync ügynök 3.1-es vagy 3.2-es verzióra való frissítése után.
- Tárolási szinkronizálási ügynök (FileSyncSvc) szolgáltatás összeomlik miatt olyan fájlra, amely elérési utakat.
- Kiszolgáló regisztrálása hibával meghiúsul: Nem sikerült betölteni fájl vagy a szerelvény Kailani.Afs.StorageSyncProtocol.V3.

## <a name="agent-version-3200"></a>Ügynök verziója 3.2.0.0
A következő kiadási megjegyzések 3.2.0.0 az Azure File Sync ügynök verziója kiadás dátuma: 2018. augusztus 15 vonatkoznak. Ezek a megjegyzések kibocsátási megjegyzéseinek kiegészítéséül verzió 3.1.0.0-s kívül vannak.

Ebben a kiadásban a következő javítás tartalmazza:
- Szinkronizálás meghiúsul, és kevés a memória (0x8007000e) miatt memóriavesztés

## <a name="agent-version-3100"></a>Ügynök verziója 3.1.0.0-s
A következő kiadási megjegyzések 3.1.0.0-s az Azure File Sync ügynök verziója (kiadás dátuma: 2018. július 19.) vonatkoznak.

### <a name="evaluation-tool"></a>Kiértékelési eszközével
Az Azure File Sync üzembe helyezése előtt, ki kell értékelni a rendszer az Azure File Sync értékelési eszközzel kompatibilis-e. Ezt az eszközt az Azure PowerShell-parancsmagot, amely ellenőrzi a potenciális problémákat, a fájlrendszert és adatkészlethez, például a nem támogatott karaktereket vagy nem támogatott operációsrendszer-verzió. Telepítés és használat utasításokat: [Megoldásértékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) az útmutató a tervezési szakaszban. 

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
- A társzinkronizálási szolgáltatás és/vagy a storage-fiókot is áthelyezhető másik erőforráscsoportba vagy előfizetésbe meglévő Azure AD-bérlőn belül. Ha a tárfiókot, a hibrid File Sync szolgáltatásbeli hozzáférést a tárfiókhoz kell (lásd: [biztosítása érdekében az Azure File Sync hozzáfér a tárfiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Az Azure File Sync nem támogatja az előfizetés áthelyezése egy másik Azure AD-bérlővel.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.
