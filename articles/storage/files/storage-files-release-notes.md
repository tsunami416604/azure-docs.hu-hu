---
title: Az Azure File Sync ügynök (előzetes verzió) kibocsátási megjegyzései | Microsoft Docs
description: Kibocsátási megjegyzések az Azure fájl Sync-ügynök (előzetes verzió).
services: storage
author: wmgries
manager: aungoo
ms.service: storage
ms.topic: article
ms.date: 05/31/2018
ms.author: wgries
ms.openlocfilehash: 946311c42088d3a5840eb35387c8a552d3d5d70f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34735644"
---
# <a name="release-notes-for-the-azure-file-sync-agent-preview"></a>Az Azure File Sync ügynök (előzetes verzió) kibocsátási megjegyzései
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. A Windows Server-telepítéseket az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
Az Azure File Sync ügynök alábbi verziói támogatottak:

| Kiadási fázisú | Az ügynök verziószáma | Kiadási dátum | status |
|----|----------------------|--------------|------------------|
| Frissítse a 2. régiója | 3.0.12.0 | 2018. május 22. | Támogatott (ajánlott verzió) |
| Kumulatív. áprilisi frissítés | 2.3.0.0 | 2018. május 8. | Támogatott |
| Március kumulatív frissítés | 2.2.0.0 | 2018. március 12. | Támogatott |
| Kumulatív frissítés. február | 2.1.0.0 | 2018. február 28. | Támogatott |
| 1 frissítés | 2.0.11.0 | 2018. február 8. | Támogatott |
| Január kumulatív frissítés | 1.4.0.0 | 2018. január 8. | Támogatott |
| Novemberében kiadott kumulatív frissítés | 1.3.0.0 | 2017. november 30. | Támogatott |
| . Októberi kumulatív frissítés | 1.2.0.0 | 2017. október 31. | Támogatott |
| Kezdeti előzetes kiadás | 1.1.0.0 | 2017. szeptember 26. | Támogatott |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-30120"></a>Ügynök verziója 3.0.12.0
Az alábbi kibocsátási megjegyzések a 3.0.12.0 az Azure fájl Sync-ügynök verziója (kiadott 2018. május 22.) vonatkoznak.

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Az Azure File Sync ügynök Windows Serverrel való telepítéséről és konfigurálásáról szóló további információért lásd [az Azure File Sync (előzetes verzió) üzembe helyezésének megtervezéséről](storage-sync-files-planning.md) és [az Azure File Sync (előzetes verzió) üzembe helyezéséről](storage-sync-files-deployment-guide.md) szóló témakört.

- Az ügynök telepítési csomag (rendszergazda) emelt szintű engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Windows Server Core vagy a Nano Server központi telepítési beállítások.
- Az ügynök csak a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynökhöz legalább 2 GB fizikai memóriára van szükség.
- A tároló szinkronizálási ügynök (FileSyncSvc) szolgáltatás nem támogatja a kiszolgáló végpontok, amely rendelkezik a kötet információkat (SVI) rendszerkönyvtár tömörített köteten található. Ez a konfiguráció váratlan eredményekhez vezethet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információkat [az Azure File Sync (előzetes verzió) hibaelhárításával](storage-sync-files-troubleshoot.md) foglalkozó cikkben talál.
- Ne használja a Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrőit vagy egyéb fájlszűrőket. A fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha blokkolnak egyes fájlokat.
- A sysprep futtatása az Azure fájl Sync-ügynök van telepítve a kiszolgálón nem támogatott, és váratlan eredményekhez vezethet. Az ügynök telepítése és a kiszolgáló regisztrálása a kiszolgálói lemezkép telepítése és a sysprep minitelepítő befejezése után kell végrehajtani.
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
- Felhő rétegezéséhez nem támogatott a rendszerköteten. Kiszolgáló-végpont létrehozása a rendszerköteten, tiltsa le a felhő rétegezéséhez, amikor a kiszolgáló-végpont létrehozása.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálóvégponton.
- Rétegzett fájlok használhatatlanná válik, ha a fájlok nem előtt tartani a kiszolgáló végpont törlése előtt.
 
### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.

## <a name="agent-version-2300"></a>Ügynök verziója 2.3.0.0
Az alábbi kibocsátási megjegyzések a 2.3.0.0 az Azure fájlszinkronizálás ügynök verziója, amely a 2018 előfordulhat, hogy 8 vonatkoznak. Ezek a megjegyzések 2.0.11.0-s verzió kibocsátási megjegyzéseinek kiegészítéséül szolgálnak.

Ebben a kiadásban a következő javításokat tartalmazza:
- Frissítések esetén nem távolítja el a felhő rétegezési szűrő-illesztőprogram lefagyhat.
- Szinkronizálási teljesítmény csökkenhet nagy mennyiségű fájlban történő szinkronizálásakor.

## <a name="agent-version-2200"></a>Ügynök verziója 2.2.0.0
Az alábbi kibocsátási megjegyzések 2.2.0.0 a Azure fájl Sync-ügynök verziója, amely a 2018. március 12. a rendszer.  Ezek a kibocsátási megjegyzésekben 2.1.0.0 és 2.0.11.0 felsorolt mellett azonban

Az egyes ügyfelek v2.1.0.0 telepítése sikertelen lesz a FileSyncSvc nem leállítása miatt. Ez a frissítés megoldja ezt a problémát.

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
Az Azure File Sync ügynök Windows Serverrel való telepítéséről és konfigurálásáról szóló további információért lásd [az Azure File Sync (előzetes verzió) üzembe helyezésének megtervezéséről](storage-sync-files-planning.md) és [az Azure File Sync (előzetes verzió) üzembe helyezéséről](storage-sync-files-deployment-guide.md) szóló témakört.

- Az ügynöktelepítési csomagot (MSI) emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök a Windows Server Core vagy a Nano Server üzemelő példányain nem támogatott.
- Az ügynök csak a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynökhöz legalább 2 GB fizikai memóriára van szükség.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információkat [az Azure File Sync (előzetes verzió) hibaelhárításával](storage-sync-files-troubleshoot.md) foglalkozó cikkben talál.
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
- Gyors vész-Helyreállítási névtér szinkronizálási növelhető a teljesítmény, jelentős mértékben.
- Sok (több mint 10 000) könyvtár törlése nem kell a v2 * kötegekben kell elvégezni.
 
### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Az előző verzióhoz képest az új fájlokat 1 órán belül rétegzi a rendszer a rétegzési szabályzat beállításainak megfelelően (ez korábban 32 óra volt). PowerShell-parancsmagot biztosítunk az igény szerinti rétegezéshez. Ezen parancsmag segítségével hatékonyabban értékelheti ki a rétegzést anélkül, hogy várnia kellene a háttérfolyamatokra.
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.
- Az előző verzióhoz képest a fájlok mostantól rétegezett fájlokként tölthetők le más kiszolgálókra, ha a fájl új vagy már rétegezett fájl volt.

## <a name="agent-version-1100"></a>Az ügynök 1.1.0.0-s verziója
A következő kibocsátási megjegyzések az Azure File Sync ügynök 1.1.0.0-s verziójára vonatkoznak (2017. szeptember 9-én kiadott, kezdeti előzetes verzió). 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Az Azure File Sync ügynök Windows Serverrel való telepítéséről és konfigurálásáról szóló további információért lásd [az Azure File Sync (előzetes verzió) üzembe helyezésének megtervezéséről](storage-sync-files-planning.md) és [az Azure File Sync (előzetes verzió) üzembe helyezéséről](storage-sync-files-deployment-guide.md) szóló témakört.

- Az ügynöktelepítési csomagot (MSI) emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök a Windows Server Core vagy a Nano Server üzemelő példányain nem támogatott.
- Az ügynök csak a Windows Server 2016 és 2012 R2 rendszeren támogatott.
- Az ügynökhöz legalább 2 GB fizikai memóriára van szükség.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információkat [az Azure File Sync (előzetes verzió) hibaelhárításával](storage-sync-files-troubleshoot.md) foglalkozó cikkben talál.
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
