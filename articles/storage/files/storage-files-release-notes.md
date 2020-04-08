---
title: Kibocsátási megjegyzések az Azure File Sync ügynökhöz | Microsoft dokumentumok
description: Kibocsátási megjegyzések az Azure File Sync ügynök.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 578d00d4bd65b3ffbfb6cdac439762344604e6b8
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804877"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Az Azure File Sync ügynök kibocsátási megjegyzései
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. A Windows Server-telepítéseket az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
Az Azure File Sync ügynök alábbi verziói támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | status |
|----|----------------------|--------------|------------------|
| 2019. decemberi frissítési összeg - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019. december 12.December 12.December 12, 2019 | Támogatott |
| V9-es kiadás - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019. december 2., 2019 | Támogatott |
| V8-as kiadás - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019. október 8.October 8, 2019 | Támogatott |
| 2019. júliusi frissítési összeg - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019. július 24.July 24, 2019 | Támogatott |
| 2019. júliusi frissítési összeg - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019. július 12. | Támogatott |
| V7-es kiadás - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 2019. június 19.June 19, 2019 | Támogatott |
| 2019. júniusi frissítési összeg - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 2019. június 27.June 27, 2019 | Támogatott - Agent verzió lejár április 21, 2020 |
| 2019. júniusi frissítési összeg - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 2019. június 13.June 13, 2019 | Támogatott - Agent verzió lejár április 21, 2020 |
| 2019. májusi frissítési összeg - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 2019. május 7.May 7, 2019 | Támogatott - Agent verzió lejár április 21, 2020 |
| V6-os kiadás - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 2019. április 21.April 21, 2019 | Támogatott - Agent verzió lejár április 21, 2020 |
| V5 kiadás | 5.0.2.0 - 5.2.0.0 | N/A | Nem támogatott - Az ügyintézőverziók 2020. |
| V4 kiadás | 4.0.1.0 - 4.3.0.0 | N/A | Nem támogatott - Az ügynökverziók 2019. november 6-án jártak le |
| V3 kiadás | 3.1.0.0 - 3.4.0.0 | N/A | Nem támogatott - Az ügyintézőverziók 2019. augusztus 19-én jártak le |
| GA előtti ügynökök | 1.1.0.0 - 3.0.13.0 | N/A | Nem támogatott - Az ügynökverziók 2018. |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-9100"></a>Ügynök 9.1.0.0-s verziója
Az alábbi kiadási megjegyzések a 2019. Ezek a megjegyzések a 9.0.0.0 verzióhoz felsorolt kibocsátási megjegyzéseken kívül találhatók.

A kiadásban javított probléma:  
- A szinkronizálás az alábbi hibák egyikével sikertelen, miután az Azure File Sync ügynök 9.0-s verziójára frissített:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Ügynök 9.0.0.0-s verziója
A következő kiadási megjegyzések az Azure File Sync ügynök 9.0.0.0-s verziójához tartoznak (2019. december 2-án jelent meg).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított fejlesztések és problémák

- Önkiszolgáló visszaállítás támogatása
    - A felhasználók most már visszaállíthatják fájljaikat az előző verzió funkció val. A v9-es kiadás előtt az előző verziófunkció nem támogatott olyan köteteken, amelyeken engedélyezve volt a felhőrétegezés. Ezt a funkciót minden kötethez külön-külön kell engedélyezni, amelyen a felhőrétegezés engedélyezve van. További információk:  
[Önkiszolgáló visszaállítás a korábbi verziókon és a VSS (Kötet árnyékmásolata szolgáltatás) szolgáltatáson keresztül.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service) 
 
- Nagyobb fájlmegosztási méretek támogatása 
    - Az Azure File Sync mostantól legfeljebb 64 TiB és 100 millió fájlt támogat egyetlen, szinkronizáló névtérben.  
 
- Adatdeduplikáció támogatása a Server 2019 rendszeren 
    - Az adatdeduplikáció mostantól a Windows Server 2019 felhőrétegezése is támogatott. A felhőrétegezéssel rendelkező kötetek adatdeduplikációjának támogatásához telepíteni kell a [Windows KB4520062](https://support.microsoft.com/help/4520062) frissítését. 
 
- Továbbfejlesztett minimális fájlméret a fájl tól a rétegig 
    - A fájl-réteg minimális fájlmérete mostantól a fájlrendszer fürtjének méretétől függ (a fájlrendszer fürtméretének kétszerese). Alapértelmezés szerint például az NTFS fájlrendszer fürtjének mérete 4 KB, az eredményül kapott minimális fájlméret a réteghez tartozó fájl esetében 8 KB. 
 
- Hálózati kapcsolat tesztparancsmagja 
    - Az Azure File Sync konfiguráció részeként több szolgáltatásvégpontot kell kapcsolatba lépni. Mindegyiknek saját DNS-neve van, amelyet a kiszolgálónak elérhetőnek kell lennie. Ezek az URL-címek arra a régióra is vonatkoznak, ahhoz a régióhoz, ahhoz a régióhoz, ahol a kiszolgáló regisztrálva van. A kiszolgáló regisztrálása után a kapcsolati tesztparancsmag (PowerShell és A kiszolgáló regisztrációs segédprogramja) használható a kiszolgálóra jellemző összes URL-címével folytatott kommunikáció tesztelésére. Ez a parancsmag segíthet a hibaelhárításban, ha a hiányos kommunikáció megakadályozza, hogy a kiszolgáló teljes mértékben együttműködjön az Azure File Sync szolgáltatással, és használható a proxy- és tűzfal-konfigurációk finomhangolására.  
 
        A hálózati kapcsolati teszt futtatásához futtassa a következő PowerShell-parancsokat: 
 
        Importmodul :C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- A kiszolgálóvégpont javításának eltávolítása a felhőrétegezés engedélyezésével 
    - Mint korábban, a kiszolgálói végpont eltávolítása nem eredményezi az Azure-fájlmegosztásban lévő fájlok eltávolítását. A helyi kiszolgálón lévő újraelemzési pontok viselkedése azonban megváltozott. Az újraelemzési pontok (a kiszolgálón nem helyi fájlokra mutató mutatók) a kiszolgálóvégpont eltávolításakor törlődnek. A teljesen gyorsítótárazott fájlok a kiszolgálón maradnak. Ez a fejlesztés az [árva rétegzett fájlok](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) elkerülése érdekében történt a kiszolgálóvégpont eltávolításakor. Ha újra létrehozza a kiszolgálóvégpontot, a rétegzett fájlok újraelemzési pontjai újra létrejönnek a kiszolgálón.  
 
- Teljesítmény- és megbízhatósági fejlesztések 
    - Csökkentett visszahívási hibák. A visszahívás immár automatikusan módosul a hálózati sávszélesség alapján. 
    - Jobb letöltési teljesítmény, ha új kiszolgálót ad hozzá egy szinkronizálási csoporthoz. 
    - A csökkentett fájlok szinkronizálása kényszerütközések miatt. 
    - A fájlok nem tudnak rétegeződni, vagy bizonyos esetekben váratlanul visszahívják őket, ha a kiszolgáló végpontjának elérési útja kötetcsatlakoztatási pont.
    
### <a name="evaluation-tool"></a>Kiértékelő eszköz
Az Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel az Azure File Sync kiértékelő eszközzel. Ez az eszköz egy Azure PowerShell-parancsmag, amely ellenőrzi a fájlrendszer és az adatkészlet lehetséges problémáit, például a nem támogatott karaktereket vagy egy nem támogatott operációs rendszer verzióját. A telepítési és használati útmutatót a tervezési útmutató [Értékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszában találja. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Az Azure File Sync ügynök Windows Server rel történő telepítéséről és konfigurálásáról az [Azure-fájlszinkronizálás telepítésének megtervezése](storage-sync-files-planning.md) és [az Azure File Sync telepítése című](storage-sync-files-deployment-guide.md)témakörben talál további információt.

- Az ügynöktelepítő csomagot emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Nano Server telepítési lehetőség.
- Az ügynök csak Windows Server 2019, Windows Server 2016 és Windows Server 2012 R2 rendszerben támogatott.
- Az ügynöknek legalább 2 GiB memóriára van szüksége. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja a rendszerköteten tömörített rendszerköteten található kiszolgálóvégpontokat. Ez a konfiguráció nem várt eredményekhez vezet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információt az [Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)című témakörben talál.
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrői végtelen szinkronizálási hibákat okozhatnak, ha a fájlképernyő miatt blokkolva vannak a fájlok.
- A sysprep futtatása olyan kiszolgálón, amelyen telepítve van az Azure File Sync ügynök, nem támogatott, és nem várt eredményekhez vezethet. Az Azure File Sync ügynök kell telepíteni a kiszolgáló lemezkép telepítése és befejezése sysprep mini-setup.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karakterekkel rendelkező fájlok. A nem támogatott karakterek listáját a [Hibaelhárítási útmutató](storage-sync-files-troubleshoot.md#handling-unsupported-characters) ban talál.
- Pontokkal végződő fájlok vagy könyvtárak.
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
 
### <a name="server-endpoint"></a>Kiszolgálóvégpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A rétegzett fájlok elérhetetlenné válnak, ha a kiszolgáló végpontjának törlése előtt nem hívják vissza a fájlokat. A fájlokhoz való hozzáférés visszaállításához hozza létre újra a kiszolgálóvégpontot. Ha 30 nap telt el a kiszolgálóvégpont törlése óta, vagy ha a felhővégpontot törölték, a vissza nem hívott rétegzett fájlok nem használhatók. További információ: [Rétegzett fájlok nem érhetők el a kiszolgálón a kiszolgálóvégpont törlése után](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja az operációs rendszert vagy az alkalmazáslapozó fájlt kiszolgálóvégponti helyen.
- A portálkiszolgáló neve nem frissül, ha a kiszolgálót átnevezi.

### <a name="cloud-endpoint"></a>Felhővégpont
- Az Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Azonban az Azure-fájlmegosztáson végrehajtott módosításokat először egy Azure File Sync változásészlelési feladatnak kell felderítenie. A változásészlelési feladat 24 óránként egyszer egy felhővégponthoz történik. Az Azure-fájlmegosztásban módosított fájlok azonnali szinkronizálásához az [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag segítségével manuálisan kezdeményezheti az Azure-fájlmegosztás változásainak észlelését. Ezenkívül az Azure-fájlmegosztáson a REST protokollon keresztül végzett módosítások nem frissítik az SMB utolsó módosítási idejét, és nem tekintik a szinkronizálás általi módosításnak.
- A storage sync szolgáltatás és/vagy a tárfiók áthelyezhető egy másik erőforráscsoportba vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha a tárfiók át lett helyezve, meg kell adnia a hibrid fájlszinkronizálási szolgáltatás nak a tárfiókhoz való hozzáférését [(lásd: Győződjön meg arról, hogy az Azure File Sync hozzáfér a tárfiókhoz).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Az Azure File Sync nem támogatja az előfizetés áthelyezését egy másik Azure AD-bérlőre.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Ha robocopy használatával másol fájlokat, a /MIR kapcsolóval őrizze meg a fájl időbélyegeit. Ez biztosítja, hogy a régebbi fájlok rétegzett hamarabb, mint a közelmúltban elért fájlokat.
- Előfordulhat, hogy a fájlok nem adnak réteget, ha a pagefile.sys fájl olyan köteten található, amelyen engedélyezve van a felhőrétegezés. A pagefile.sys fájlnak olyan köteten kell lennie, amelynek le van tiltva a felhőrétegezése.

## <a name="agent-version-8000"></a>Ügynök 8.0.0.0-s verziója
A következő kiadási megjegyzések az Azure File Sync ügynök 8.0.0.0-s verziójához tartoznak (2019. október 8-án jelent meg).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított fejlesztések és problémák

- Teljesítmény-fejlesztések visszaállítása
    - Gyorsabb helyreállítási idő az Azure Backup on keresztül történő helyreállításhoz. A visszaállított fájlok sokkal gyorsabban szinkronizálódnak az Azure File Sync kiszolgálóira. 
- Továbbfejlesztett felhőrétegezési portálélmény  
    - Ha olyan rétegzett fájlokat tartalmaz, amelyek visszahívása sikertelen, most megtekintheti a kiszolgáló végponttulajdonságainak visszahívási hibáit. Emellett a kiszolgálóvégpont állapota most antól hibát és kockázatcsökkentési lépéseket jelenít meg, ha a felhőrétegezésszűrő-illesztőprogram nincs betöltve a kiszolgálón.
- Egyszerűbb ügynöktelepítés
    - Az Az\AzureRM PowerShell-modul már nem szükséges a kiszolgáló regisztrálásához, így a telepítés egyszerűbb é és gyors.
- Egyéb teljesítmény- és megbízhatósági fejlesztések

### <a name="evaluation-tool"></a>Kiértékelő eszköz
Az Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel az Azure File Sync kiértékelő eszközzel. Ez az eszköz egy Azure PowerShell-parancsmag, amely ellenőrzi a fájlrendszer és az adatkészlet lehetséges problémáit, például a nem támogatott karaktereket vagy egy nem támogatott operációs rendszer verzióját. A telepítési és használati útmutatót a tervezési útmutató [Értékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszában találja. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Az Azure File Sync ügynök Windows Server rel történő telepítéséről és konfigurálásáról az [Azure-fájlszinkronizálás telepítésének megtervezése](storage-sync-files-planning.md) és [az Azure File Sync telepítése című](storage-sync-files-deployment-guide.md)témakörben talál további információt.

- Az ügynöktelepítő csomagot emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Nano Server telepítési lehetőség.
- Az ügynök csak Windows Server 2019, Windows Server 2016 és Windows Server 2012 R2 rendszerben támogatott.
- Az ügynöknek legalább 2 GiB memóriára van szüksége. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja a rendszerköteten tömörített rendszerköteten található kiszolgálóvégpontokat. Ez a konfiguráció nem várt eredményekhez vezet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információt az [Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)című témakörben talál.
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrői végtelen szinkronizálási hibákat okozhatnak, ha a fájlképernyő miatt blokkolva vannak a fájlok.
- A sysprep futtatása olyan kiszolgálón, amelyen telepítve van az Azure File Sync ügynök, nem támogatott, és nem várt eredményekhez vezethet. Az Azure File Sync ügynök kell telepíteni a kiszolgáló lemezkép telepítése és befejezése sysprep mini-setup.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karakterekkel rendelkező fájlok. A nem támogatott karakterek listáját a [Hibaelhárítási útmutató](storage-sync-files-troubleshoot.md#handling-unsupported-characters) ban talál.
- Pontokkal végződő fájlok vagy könyvtárak.
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
 
### <a name="server-endpoint"></a>Kiszolgálóvégpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A rétegzett fájlok elérhetetlenné válnak, ha a kiszolgáló végpontjának törlése előtt nem hívják vissza a fájlokat. A fájlokhoz való hozzáférés visszaállításához hozza létre újra a kiszolgálóvégpontot. Ha 30 nap telt el a kiszolgálóvégpont törlése óta, vagy ha a felhővégpontot törölték, a vissza nem hívott rétegzett fájlok nem használhatók. További információ: [Rétegzett fájlok nem érhetők el a kiszolgálón a kiszolgálóvégpont törlése után](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja az operációs rendszert vagy az alkalmazáslapozó fájlt kiszolgálóvégponti helyen.
- A portálkiszolgáló neve nem frissül, ha a kiszolgálót átnevezi.

### <a name="cloud-endpoint"></a>Felhővégpont
- Az Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Azonban az Azure-fájlmegosztáson végrehajtott módosításokat először egy Azure File Sync változásészlelési feladatnak kell felderítenie. A változásészlelési feladat 24 óránként egyszer egy felhővégponthoz történik. Az Azure-fájlmegosztásban módosított fájlok azonnali szinkronizálásához az [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag segítségével manuálisan kezdeményezheti az Azure-fájlmegosztás változásainak észlelését. Ezenkívül az Azure-fájlmegosztáson a REST protokollon keresztül végzett módosítások nem frissítik az SMB utolsó módosítási idejét, és nem tekintik a szinkronizálás általi módosításnak.
- A storage sync szolgáltatás és/vagy a tárfiók áthelyezhető egy másik erőforráscsoportba vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha a tárfiók át lett helyezve, meg kell adnia a hibrid fájlszinkronizálási szolgáltatás nak a tárfiókhoz való hozzáférését [(lásd: Győződjön meg arról, hogy az Azure File Sync hozzáfér a tárfiókhoz).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Az Azure File Sync nem támogatja az előfizetés áthelyezését egy másik Azure AD-bérlőre.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Ha robocopy használatával másol fájlokat, a /MIR kapcsolóval őrizze meg a fájl időbélyegeit. Ez biztosítja, hogy a régebbi fájlok rétegzett hamarabb, mint a közelmúltban elért fájlokat.

## <a name="agent-version-7200"></a>Ügynök 7.2.0.0-s verziója
Az alábbi kiadási megjegyzések a 2019. Ezek a megjegyzések a 7.0.0.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül találhatók.

Az ebben a kiadásban javított problémák listája:  
- A Storage Sync Agent (FileSyncSvc) összeomlik, ha a proxykonfiguráció null értékű.
- A kiszolgálóvégpont elindítja a BCDR-t (hiba: 0x80c80257 - ECS_E_BCDR_IN_PROGRESS), ha a kiszolgálón több végpont neve megegyezik.
- A felhőrétegezés megbízhatóságának javítása.

## <a name="agent-version-7100"></a>Ügynök 7.1.0.0-s verziója
Az alábbi kiadási megjegyzések a 2019. Ezek a megjegyzések a 7.0.0.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül találhatók.

Az ebben a kiadásban javított problémák listája:  
- Windows Server 2012 R2 rendszerben lassú a kiszolgálóvégponti hely elérése vagy böngészése SMB-n keresztül. 
- Megnövekedett CPU-kihasználtság az Azure File Sync v6 ügynök telepítése után.
- Felhőrétegezés telemetriai fejlesztések.
- A felhőrétegezés és -szinkronizálás egyéb megbízhatósági fejlesztései.

## <a name="agent-version-7000"></a>Ügynök 7.0.0.0-s verziója
A következő kiadási megjegyzések az Azure File Sync ügynök 7.0.0.0-s verziójához tartoznak (2019. június 19-én jelent meg).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított fejlesztések és problémák

- Nagyobb fájlmegosztási méretek támogatása
    - A nagyobb Azure-fájlmegosztások előzetes verziójával növeljük a fájlszinkronizálás támogatási korlátait is. Ebben az első lépésben az Azure File Sync mostantól legfeljebb 25 TB-os és 50 millió fájlt támogat egyetlen, szinkronizálási névtérben. A nagy fájlmegosztási előnézetre való https://aka.ms/azurefilesatscalesurveyjelentkezéshez töltse ki ezt az űrlapot. 
- Tűzfal és virtuális hálózati beállítás támogatása tárfiókokon
    - Az Azure File Sync mostantól támogatja a tűzfal és a virtuális hálózati beállítás a tárfiókok. Ha a központi telepítést a tűzfal- és a virtuálishálózati beállítással való együttműködésre szeretné beállítani, olvassa el a [Tűzfal és a virtuális hálózati beállítások konfigurálása című témakört.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
- PowerShell-parancsmag az Azure-fájlmegosztásban módosított fájlok azonnali szinkronizálásához
    - Az Azure-fájlmegosztásban módosított fájlok azonnali szinkronizálásához az Invoke-AzStorageSyncChangeDetection PowerShell-parancsmag segítségével manuálisan kezdeményezheti az Azure-fájlmegosztás változásainak észlelését. Ez a parancsmag olyan esetekben készült, ahol valamilyen automatizált folyamat módosítja az Azure-fájlmegosztást, vagy a módosításokat egy rendszergazda végzi (például fájlok és könyvtárak áthelyezése a megosztásba). A végfelhasználói módosítások, a javaslat az, hogy telepítse az Azure File Sync ügynök egy IaaS virtuális gép, és a végfelhasználók számára a fájlmegosztás az IaaS virtuális gép. Így minden módosítás gyorsan szinkronizálható más ügynökökkel anélkül, hogy az Invoke-AzStorageSyncChangeDetection parancsmag használata szükséges lenne. További információ: [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) dokumentáció.
- Továbbfejlesztett portálélmény, ha nem szinkronizált fájlokkal találkozik
    - Ha olyan fájljai vannak, amelyek szinkronizálása sikertelen, most antól különbséget teszünk a portálon lévő átmeneti és állandó hibák között. Az átmeneti hibák általában rendszergazdai beavatkozás nélkül oldódnak meg. A jelenleg használt fájlok például nem szinkronizálódnak, amíg a fájlleíró be nem zárul. Az állandó hibák esetén mostantól megmutatjuk az egyes hibák által érintett fájlok számát. Az állandó hibaszám a szinkronizálási csoport összes kiszolgálói végpontjának szinkronizálását nem tartalmazó oszlopokban is megjelenik.
- Továbbfejlesztett Azure Backup fájlszintű visszaállítás
    - Az Azure Backup használatával visszaállított egyes fájlok észlelése és szinkronizálása a kiszolgáló végpontjával gyorsabb.
- Továbbfejlesztett felhőrétegezés-visszahívási parancsmag megbízhatósága 
    - Az Invoke-StorageSyncFileRecall parancsmag mostantól lehetővé teszi az ügyfelek számára, hogy fájlonkénti újrapróbálkozások számát és a robocopy-hoz hasonló fájl-újrapróbálkozási késleltetést adjanak meg. Korábban ez a parancsmag egy adott elérési út alatt, véletlenszerű sorrendben visszahívta az összes rétegzett fájlt. Az új -Order paraméterrel ez a parancsmag először a legforróbb adatokat hívja vissza, és tiszteletben tartja a felhőrétegezési házirendet (ne hívja fel a hívásvisszaadást, ha a dátumházirend teljesül, vagy a kötetszabad terület teljesül; amelyik előbb megtörténik).
- Csak a TLS 1.2 támogatása (a TLS 1.0 és az 1.1 le van tiltva)
    - Az Azure File Sync mostantól csak a TLS 1.0-s és 1.1-es letiltással rendelkező kiszolgálókon támogatja a TLS 1.2 használatát. A fejlesztés előtt a kiszolgáló regisztrációja sikertelen lenne, ha a TLS 1.0 és az 1.1 le lenne tiltva a kiszolgálón.
- Egyéb teljesítmény- és megbízhatósági fejlesztések a szinkronizáláshoz és a felhőrétegezéshez
    - Ebben a kiadásban számos megbízhatósági és teljesítménybeli fejlesztés található. Ezek közül néhány célja, hogy a felhőrétegezés hatékonyabbés az Azure File Sync egésze jobban működjön azokban a helyzetekben, amikor van egy sávszélesség-szabályozási ütemezés beállítása.

### <a name="evaluation-tool"></a>Kiértékelő eszköz
Az Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel az Azure File Sync kiértékelő eszközzel. Ez az eszköz egy Azure PowerShell-parancsmag, amely ellenőrzi a fájlrendszer és az adatkészlet lehetséges problémáit, például a nem támogatott karaktereket vagy egy nem támogatott operációs rendszer verzióját. A telepítési és használati útmutatót a tervezési útmutató [Értékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszában találja. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Az Azure File Sync ügynök Windows Server rel történő telepítéséről és konfigurálásáról az [Azure-fájlszinkronizálás telepítésének megtervezése](storage-sync-files-planning.md) és [az Azure File Sync telepítése című](storage-sync-files-deployment-guide.md)témakörben talál további információt.

- Az ügynöktelepítő csomagot emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Nano Server telepítési lehetőség.
- Az ügynök csak Windows Server 2019, Windows Server 2016 és Windows Server 2012 R2 rendszerben támogatott.
- Az ügynöknek legalább 2 GiB memóriára van szüksége. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja a rendszerköteten tömörített rendszerköteten található kiszolgálóvégpontokat. Ez a konfiguráció nem várt eredményekhez vezet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információt az [Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)című témakörben talál.
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrői végtelen szinkronizálási hibákat okozhatnak, ha a fájlképernyő miatt blokkolva vannak a fájlok.
- A sysprep futtatása olyan kiszolgálón, amelyen telepítve van az Azure File Sync ügynök, nem támogatott, és nem várt eredményekhez vezethet. Az Azure File Sync ügynök kell telepíteni a kiszolgáló lemezkép telepítése és befejezése sysprep mini-setup.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karakterekkel rendelkező fájlok. A nem támogatott karakterek listáját a [Hibaelhárítási útmutató](storage-sync-files-troubleshoot.md#handling-unsupported-characters) ban talál.
- Pontokkal végződő fájlok vagy könyvtárak.
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
 
### <a name="server-endpoint"></a>Kiszolgálóvégpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A rétegzett fájlok elérhetetlenné válnak, ha a kiszolgáló végpontjának törlése előtt nem hívják vissza a fájlokat. A fájlokhoz való hozzáférés visszaállításához hozza létre újra a kiszolgálóvégpontot. Ha 30 nap telt el a kiszolgálóvégpont törlése óta, vagy ha a felhővégpontot törölték, a vissza nem hívott rétegzett fájlok nem használhatók.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja az operációs rendszert vagy az alkalmazáslapozó fájlt kiszolgálóvégponti helyen.
- A portálkiszolgáló neve nem frissül, ha a kiszolgálót átnevezi.

### <a name="cloud-endpoint"></a>Felhővégpont
- Az Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Azonban az Azure-fájlmegosztáson végrehajtott módosításokat először egy Azure File Sync változásészlelési feladatnak kell felderítenie. A változásészlelési feladat 24 óránként egyszer egy felhővégponthoz történik. Ezenkívül az Azure-fájlmegosztáson a REST protokollon keresztül végzett módosítások nem frissítik az SMB utolsó módosítási idejét, és nem tekintik a szinkronizálás általi módosításnak.
- A storage sync szolgáltatás és/vagy a tárfiók áthelyezhető egy másik erőforráscsoportba vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha a tárfiók át lett helyezve, meg kell adnia a hibrid fájlszinkronizálási szolgáltatás nak a tárfiókhoz való hozzáférését [(lásd: Győződjön meg arról, hogy az Azure File Sync hozzáfér a tárfiókhoz).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Az Azure File Sync nem támogatja az előfizetés áthelyezését egy másik Azure AD-bérlőre.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Ha robocopy használatával másol fájlokat, a /MIR kapcsolóval őrizze meg a fájl időbélyegeit. Ez biztosítja, hogy a régebbi fájlok rétegzett hamarabb, mint a közelmúltban elért fájlokat.

## <a name="agent-version-6300"></a>Ügynök 6.3.0.0-s verziója
Az alábbi kiadási megjegyzések az Azure File Sync ügynök 2019. Ezek a megjegyzések a 6.0.0.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül találhatók.

Az ebben a kiadásban javított problémák listája:  
- Windows Server 2012 R2 rendszerben lassú a kiszolgálóvégpont im.-kiszolgálói helyének elérése vagy böngészése SMB-n keresztül 
- Megnövekedett CPU-kihasználtság az Azure File Sync v6 ügynök telepítése után
- Felhőrétegezéstelemetriai fejlesztések

## <a name="agent-version-6200"></a>Ügynök 6.2.0.0-s verziója
Az alábbi kiadási megjegyzések az Azure File Sync ügynök 2019. Ezek a megjegyzések a 6.0.0.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül találhatók.

Az ebben a kiadásban javított problémák listája:  
- Kiszolgálói végpont létrehozása után magas CPU-használat fordulhat elő, amikor a háttér-visszahívás fájlokat tölt le a kiszolgálóra
- A szinkronizálási és a felhőrétegezési műveletek hiba ECS_E_SERVER_CREDENTIAL_NEEDED jogkivonat lejárata miatt sikertelenek lehetnek
- A fájl visszahívása sikertelen lehet, ha a fájl letöltéséhez használható URL-cím fenntartott karaktereket tartalmaz 

## <a name="agent-version-6100"></a>Ügynök 6.1.0.0-s verziója
Az alábbi kiadási megjegyzések a 2019. Ezek a megjegyzések a 6.0.0.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül találhatók.

Az ebben a kiadásban javított problémák listája:  
- A Windows Felügyeleti központ nem jeleníti meg az ügynök verzió- és kiszolgálóvégpont-konfigurációját azokon a kiszolgálókon, amelyeken telepítve van az Azure File Sync agent 6.0-s verziója.

## <a name="agent-version-6000"></a>Ügynök 6.0.0.0-s verziója
A következő kiadási megjegyzések az Azure File Sync ügynök 6.0.0.0-s verziójához tartoznak (2019. április 22-én jelent meg).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított fejlesztések és problémák

- Ügynök automatikus frissítéstámogatása
  - Hallottuk a visszajelzést, és hozzáadott egy automatikus frissítési funkció az Azure File Sync kiszolgálóügynök. További információ: [Azure File Sync agent update policy](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Az Azure fájlmegosztási ACL-k támogatása
  - Az Azure File Sync mindig is támogatta az ACL-ek szinkronizálását a kiszolgálóvégpontok között, de az ACL-ek nem voltak szinkronizálva a felhővégpontkal (Azure-fájlmegosztás). Ez a kiadás támogatja az ACL-k szinkronizálását a kiszolgáló és a felhő végpontjai között.
- Párhuzamos feltöltési és letöltési szinkronizálási munkamenetek kiszolgálóvégponthoz 
  - A kiszolgálóvégpontok mostantól támogatják a fájlok egyidejű feltöltését és letöltését. Nincs több várakozás a letöltés befejezéséhez, így a fájlok feltölthetők az Azure-fájlmegosztásra. 
- Új felhőrétegezési parancsmagok a kötet és a rétegezési állapot leküzdéséhez
  - Két új, kiszolgáló-helyi PowerShell-parancsmag mostmár használható a felhőrétegezési és fájl-visszahívási információk beszerzése. A kiszolgálón két eseménycsatornáról származó naplózási adatokat tesznek elérhetővé:
    - Get-StorageSyncFileTieringResult felsorolja az összes fájlt és azok elérési útjait, amelyek nem rétegzett, és jelentést tesz az oka.
    - Get-StorageSyncFileRecallResult jelenti az összes fájl-visszaírási eseményt. Felsorolja az összes visszahívott fájlt és annak elérési útját, valamint a visszahívás sikerét vagy hibáját.
  - Alapértelmezés szerint mindkét eseménycsatorna egyenként legfeljebb 1 MB-ot tárolhat – az eseménycsatorna méretének növelésével növelheti a jelentett fájlok mennyiségét.
- FiPS mód támogatása
  - Az Azure File Sync most antól támogatja a FIPS-mód engedélyezését azokon a kiszolgálókon, amelyeken telepítve van az Azure File Sync ügynök.
    - A FIPS-mód kiszolgálón való engedélyezése előtt telepítse az Azure File Sync agent és [packagemanagement modult](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) a kiszolgálóra. Ha a FIPS már engedélyezve van a kiszolgálón, [manuálisan töltse le](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) a [PackageManagement modult](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) a kiszolgálóra.
- Vegyes megbízhatósági fejlesztések a felhőrétegezéshez és -szinkronizáláshoz

### <a name="evaluation-tool"></a>Kiértékelő eszköz
Az Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel az Azure File Sync kiértékelő eszközzel. Ez az eszköz egy Azure PowerShell-parancsmag, amely ellenőrzi a fájlrendszer és az adatkészlet lehetséges problémáit, például a nem támogatott karaktereket vagy egy nem támogatott operációs rendszer verzióját. A telepítési és használati útmutatót a tervezési útmutató [Értékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszában találja. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Az Azure File Sync ügynök Windows Server rel történő telepítéséről és konfigurálásáról az [Azure-fájlszinkronizálás telepítésének megtervezése](storage-sync-files-planning.md) és [az Azure File Sync telepítése című](storage-sync-files-deployment-guide.md)témakörben talál további információt.

- Az ügynöktelepítő csomagot emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Nano Server telepítési lehetőség.
- Az ügynök csak Windows Server 2019, Windows Server 2016 és Windows Server 2012 R2 rendszerben támogatott.
- Az ügynöknek legalább 2 GiB memóriára van szüksége. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja a rendszerköteten tömörített rendszerköteten található kiszolgálóvégpontokat. Ez a konfiguráció nem várt eredményekhez vezet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információt az [Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)című témakörben talál.
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrői végtelen szinkronizálási hibákat okozhatnak, ha a fájlképernyő miatt blokkolva vannak a fájlok.
- A sysprep futtatása olyan kiszolgálón, amelyen telepítve van az Azure File Sync ügynök, nem támogatott, és nem várt eredményekhez vezethet. Az Azure File Sync ügynök kell telepíteni a kiszolgáló lemezkép telepítése és befejezése sysprep mini-setup.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karakterekkel rendelkező fájlok. A nem támogatott karakterek listáját a [Hibaelhárítási útmutató](storage-sync-files-troubleshoot.md#handling-unsupported-characters) ban talál.
- Pontokkal végződő fájlok vagy könyvtárak.
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
 
### <a name="server-endpoint"></a>Kiszolgálóvégpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A rétegzett fájlok elérhetetlenné válnak, ha a kiszolgáló végpontjának törlése előtt nem hívják vissza a fájlokat. A fájlokhoz való hozzáférés visszaállításához hozza létre újra a kiszolgálóvégpontot. Ha 30 nap telt el a kiszolgálóvégpont törlése óta, vagy ha a felhővégpontot törölték, a vissza nem hívott rétegzett fájlok nem használhatók.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja az operációs rendszert vagy az alkalmazáslapozó fájlt kiszolgálóvégponti helyen.
- A portálkiszolgáló neve nem frissül, ha a kiszolgálót átnevezi.

### <a name="cloud-endpoint"></a>Felhővégpont
- Az Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Azonban az Azure-fájlmegosztáson végrehajtott módosításokat először egy Azure File Sync változásészlelési feladatnak kell felderítenie. A változásészlelési feladat 24 óránként egyszer egy felhővégponthoz történik. Ezenkívül az Azure-fájlmegosztáson a REST protokollon keresztül végzett módosítások nem frissítik az SMB utolsó módosítási idejét, és nem tekintik a szinkronizálás általi módosításnak.
- A storage sync szolgáltatás és/vagy a tárfiók áthelyezhető egy másik erőforráscsoportba vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha a tárfiók át lett helyezve, meg kell adnia a hibrid fájlszinkronizálási szolgáltatás nak a tárfiókhoz való hozzáférését [(lásd: Győződjön meg arról, hogy az Azure File Sync hozzáfér a tárfiókhoz).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Az Azure File Sync nem támogatja az előfizetés áthelyezését egy másik Azure AD-bérlőre.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Ha robocopy használatával másol fájlokat, a /MIR kapcsolóval őrizze meg a fájl időbélyegeit. Ez biztosítja, hogy a régebbi fájlok rétegzett hamarabb, mint a közelmúltban elért fájlokat.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.

## <a name="agent-version-5200"></a>Ügynök 5.2.0.0-s verziója
Az alábbi kiadási megjegyzések az Azure File Sync ügynök 2019. Ezek a megjegyzések az 5.0.2.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül találhatók.

Az ebben a kiadásban javított problémák listája:  
- Megbízhatósági fejlesztések az offline adatátvitelhez és az adatátvitel újraindításához
- Telemetriai fejlesztések szinkronizálása

## <a name="agent-version-5100"></a>Ügynök 5.1.0.0-s verziója
Az alábbi kiadási megjegyzések a 2019. Ezek a megjegyzések az 5.0.2.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül találhatók.

Az ebben a kiadásban javított problémák listája:  
- Előfordulhat, hogy a fájlok szinkronizálása nem sikerül a 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) hibával, ha a kiszolgálón a módosítás imitoreszálása sikertelen
- Ha egy szinkronizálási munkamenet vagy fájl 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE) hibát kap, a szinkronizálás most újra megpróbálja a műveletet
- Előfordulhat, hogy a fájlok szinkronizálása nem sikerül a következő hiba esetén: 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Nagy memóriahasználat léphet fel fájlok visszahívásakor
- Felhőrétegezéstelemetriai fejlesztések 

## <a name="agent-version-5020"></a>Ügynök 5.0.2.0-s verziója
Az alábbi kiadási megjegyzések az Azure File Sync ügynök 5.0.2.0-s verziójához tartoznak (2019. február 12-én jelent meg).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított fejlesztések és problémák

- Az Azure Government felhőtámogatása
  - Az Azure Government felhő előzetes verziójú támogatását is hozzáadtuk. Ehhez fehérlistára tett előfizetésre és speciális ügynökletöltésre van szükség a Microsofttól. Ahhoz, hogy hozzáférjen az előnézethez, kérjük, írjon nekünk közvetlenül a . [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)
- Adatdeduplikáció támogatása
    - Az adatdeduplikáció már teljes mértékben támogatott, ha a felhőréteg engedélyezve van a Windows Server 2016 és a Windows Server 2019 rendszerben. A deduplikáció engedélyezése a felhőrétegezés engedélyezésével lehetővé teszi, hogy több fájlt gyorsítótárazhat a helyszínen anélkül, hogy további tárhelyet biztosítana.
- Offline adatátvitel támogatása (pl. a Data Box-on keresztül)
    - Könnyedén áttelepítheti a nagy mennyiségű adatot az Azure File Sync-be bármilyen módon, amelyet választhat. Kiválaszthatja az Azure Data Box, AzCopy és még a külső áttelepítési szolgáltatások. A Data Box esetében nem kell nagy sávszélességet használnia ahhoz, hogy adatait az Azure-ba bejuttathassa – egyszerűen küldje el oda! További információ: [Offline Adatátviteli dokumentumok](https://aka.ms/AFS/OfflineDataTransfer).
- Jobb szinkronizálási teljesítmény
    - Előfordulhat, hogy az ugyanazon a köteten több kiszolgálói végponttal rendelkező ügyfelek lassú szinkronizálási teljesítményt tapasztaltak a kiadás előtt. Az Azure File Sync egy ideiglenes VSS-pillanatképet hoz létre naponta egyszer a kiszolgálón a megnyitott leírókkal tartalmazó fájlok szinkronizálásához. A Szinkronizálás mostantól több kiszolgálói végpont szinkronizálását támogatja egy köteten, ha a VSS szinkronizálási munkamenet aktív. Nincs több várakozás a VSS szinkronizálási munkamenet befejezésére, hogy a szinkronizálás folytatódjon a kötet más kiszolgálói végpontjain.
- Továbbfejlesztett figyelés a portálon
    - A storage sync service portálon a diagramok a következők megtekintéséhez lettek hozzáadva:
        - Szinkronizált fájlok száma
        - Az átvitt adatok mérete
        - A nem szinkronizált fájlok száma
        - Visszahívott adatok mérete
        - Kiszolgáló kapcsolatának állapota
    - További információ: [Monitor Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Jobb méretezhetőség és megbízhatóság
    - A könyvtárban lévő fájlrendszer-objektumok (könyvtárak és fájlok) maximális száma 1 000 000-re nőtt. Az előző limit 200 000 volt.
    - A szinkronizálás az adatátvitel takarása helyett az újraküldést próbálja meg, amikor nagy fájlok átvitele megszakad 

### <a name="evaluation-tool"></a>Kiértékelő eszköz
Az Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel az Azure File Sync kiértékelő eszközzel. Ez az eszköz egy Azure PowerShell-parancsmag, amely ellenőrzi a fájlrendszer és az adatkészlet lehetséges problémáit, például a nem támogatott karaktereket vagy egy nem támogatott operációs rendszer verzióját. A telepítési és használati útmutatót a tervezési útmutató [Értékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszában találja. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Az Azure File Sync ügynök Windows Server rel történő telepítéséről és konfigurálásáról az [Azure-fájlszinkronizálás telepítésének megtervezése](storage-sync-files-planning.md) és [az Azure File Sync telepítése című](storage-sync-files-deployment-guide.md)témakörben talál további információt.

- Az ügynöktelepítő csomagot emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Windows Server Core vagy a Nano Server telepítési beállításaiban.
- Az ügynök csak Windows Server 2019, Windows Server 2016 és Windows Server 2012 R2 rendszerben támogatott.
- Az ügynöknek legalább 2 GiB memóriára van szüksége. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja a rendszerköteten tömörített rendszerköteten található kiszolgálóvégpontokat. Ez a konfiguráció nem várt eredményekhez vezet.
- A FIPS mód nem támogatott, ezért le kell tiltani. 

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információt az [Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)című témakörben talál.
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrői végtelen szinkronizálási hibákat okozhatnak, ha a fájlképernyő miatt blokkolva vannak a fájlok.
- A sysprep futtatása olyan kiszolgálón, amelyen telepítve van az Azure File Sync ügynök, nem támogatott, és nem várt eredményekhez vezethet. Az Azure File Sync ügynök kell telepíteni a kiszolgáló lemezkép telepítése és befejezése sysprep mini-setup.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karakterekkel rendelkező fájlok. A nem támogatott karakterek listáját a [Hibaelhárítási útmutató](storage-sync-files-troubleshoot.md#handling-unsupported-characters) ban talál.
- Pontokkal végződő fájlok vagy könyvtárak.
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
 
### <a name="server-endpoint"></a>Kiszolgálóvégpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A rétegzett fájlok elérhetetlenné válnak, ha a kiszolgáló végpontjának törlése előtt nem hívják vissza a fájlokat. A fájlokhoz való hozzáférés visszaállításához hozza létre újra a kiszolgálóvégpontot. Ha 30 nap telt el a kiszolgálóvégpont törlése óta, vagy ha a felhővégpontot törölték, a vissza nem hívott rétegzett fájlok nem használhatók.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja az operációs rendszert vagy az alkalmazáslapozó fájlt kiszolgálóvégponti helyen.
- A portálkiszolgáló neve nem frissül, ha a kiszolgálót átnevezi.

### <a name="cloud-endpoint"></a>Felhővégpont
- Az Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Azonban az Azure-fájlmegosztáson végrehajtott módosításokat először egy Azure File Sync változásészlelési feladatnak kell felderítenie. A változásészlelési feladat 24 óránként egyszer egy felhővégponthoz történik. Ezenkívül az Azure-fájlmegosztáson a REST protokollon keresztül végzett módosítások nem frissítik az SMB utolsó módosítási idejét, és nem tekintik a szinkronizálás általi módosításnak.
- A storage sync szolgáltatás és/vagy a tárfiók áthelyezhető egy másik erőforráscsoportba vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha a tárfiók át lett helyezve, meg kell adnia a hibrid fájlszinkronizálási szolgáltatás nak a tárfiókhoz való hozzáférését [(lásd: Győződjön meg arról, hogy az Azure File Sync hozzáfér a tárfiókhoz).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Az Azure File Sync nem támogatja az előfizetés áthelyezését egy másik Azure AD-bérlőre.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Ha robocopy használatával másol fájlokat, a /MIR kapcsolóval őrizze meg a fájl időbélyegeit. Ez biztosítja, hogy a régebbi fájlok rétegzett hamarabb, mint a közelmúltban elért fájlokat.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.
