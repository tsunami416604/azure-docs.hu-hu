---
title: Kibocsátási megjegyzések a Azure File Sync-ügynökhöz | Microsoft Docs
description: A Azure File Sync ügynök kibocsátási megjegyzései.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 6/26/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 54a7f3f50de27747ab15f6895ebfb4f65faf5fdf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85484060"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Az Azure File Sync ügynök kibocsátási megjegyzései
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. A Windows Server-telepítéseket az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
Az Azure File Sync ügynök alábbi verziói támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | Állapot |
|----|----------------------|--------------|------------------|
| V 10.1 kiadás – [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 2020. június 5. | Támogatott – Berepülés |
| 2020. május kumulatív frissítés – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | Május 19., 2020 | Támogatott |
| V10-es kiadás – [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 2020. április 9. | Támogatott |
| 2019. decemberi kumulatív frissítés – [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019. december 12. | Támogatott |
| V9-es kiadás – [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019. december 2. | Támogatott |
| V8-as kiadás – [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019. október 8. | Támogatott |
| 2019. júliusi kumulatív frissítés – [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019. július 24. | Támogatott – az ügynök verziószáma 2020. szeptember 1-jén lejár |
| 2019. júliusi kumulatív frissítés – [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019. július 12. | Támogatott – az ügynök verziószáma 2020. szeptember 1-jén lejár |
| V7-kiadás – [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | Június 19., 2019 | Támogatott – az ügynök verziószáma 2020. szeptember 1-jén lejár |
| V6-os kiadás | 6.0.0.0 – 6.3.0.0 | N.A. | Nem támogatott – az ügynök verziószáma 2020. április 21-én lejárt |
| V5-kiadás | 5.0.2.0 – 5.2.0.0 | N.A. | Nem támogatott – az ügynök verziói a 2020. március 18-án lejártak. |
| V4-kiadás | 4.0.1.0 – 4.3.0.0 | N.A. | Nem támogatott – az ügynök verziói 2019 november 6-án lejártak |
| V3 kiadás | 3.1.0.0 – 3.4.0.0 | N.A. | Nem támogatott – az ügynök verziói lejártak a 2019-es augusztus 19-én |
| Pre-GA ügynökök | 1.1.0.0 – 3.0.13.0 | N.A. | Nem támogatott – az ügynök verziói lejártak 2018 október 1-jén |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10100"></a>Ügynök verziója 10.1.0.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 10.1.0.0, amely 2020. június 5-én jelent meg. Ezek a megjegyzések a 10.0.0.0 és a 10.0.2.0 verzióhoz tartozó kibocsátási megjegyzéseken kívül is szerepelnek.

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Azure Private Endpoint-támogatás
    - A Storage Sync szolgáltatás felé irányuló forgalom szinkronizálása most már egy privát végpontra is elküldhető. Ez lehetővé teszi, hogy a bújtatás ExpressRoute vagy VPN-kapcsolaton keresztül történjen. További információ: [Azure file Sync hálózati végpontok konfigurálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-networking-endpoints).
- A fájlok szinkronizált metrikája mostantól a folyamat végén is megjeleníti az előrehaladást, nem pedig a végponton.
- Az ügynök telepítésének, a felhő-rétegek, a szinkronizálás és a telemetria különböző megbízhatósági fejlesztése

## <a name="agent-version-10020"></a>Ügynök verziója 10.0.2.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 10.0.2.0 vonatkoznak, amely 2020. május 19-én jelent meg. Ezek a megjegyzések a 10.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

Ebben a kiadásban rögzített probléma:  
- A Storage Sync Agent (FileSyncSvc) a Azure File Sync v10-ügynök telepítése után gyakran összeomlik.

> [!Note]  
>Ezt a kiadást nem olyan kiszolgálók indították, amelyek automatikusan frissülnek, amikor új verzió válik elérhetővé. A frissítés telepítéséhez használja Microsoft Update vagy Microsoft Update katalógust (lásd: [KB4522412](https://support.microsoft.com/help/4522412) for Installation instructions).

## <a name="agent-version-10000"></a>Ügynök verziója 10.0.0.0
A következő kibocsátási megjegyzések az Azure File Sync-ügynök verziójának 10.0.0.0 vonatkoznak (2020. április 9.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Továbbfejlesztett szinkronizálási folyamat a portálon
    - A V10-es ügynök kiadásával a Azure Portal hamarosan megkezdi a futó szinkronizálási munkamenet típusának megjelenítését. Például kezdeti letöltés, normál letöltés, háttér-visszahívás (gyors vész-helyreállítási esetek) és hasonló. 

- Továbbfejlesztett felhőalapú többplatformos portál
    - Ha olyan fájlokkal rendelkezik, amelyek nem képesek a rétegek vagy a visszahívás megadására, most megtekintheti a rétegek hibáit a kiszolgálói végpont tulajdonságaiban.
    - A Felhőbeli végpontok további információi elérhetők a kiszolgálói végpontok számára:
        - Helyi gyorsítótár mérete
        - Gyorsítótár-használat hatékonysága
        - A felhőre vonatkozó szabályok részletei: kötet mérete, jelenlegi szabad terület, vagy a legrégebbi fájl utolsó elérési ideje a helyi gyorsítótárban.
    - Ezek a módosítások a kezdeti V10-es ügynök kiadása után röviddel a Azure Portalre kerülnek.

- A Storage Sync szolgáltatás és/vagy a Storage-fiók másik Azure Active Directory (HRE) bérlőbe való áthelyezésének támogatása
    - Azure File Sync mostantól támogatja a Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezését egy másik erőforráscsoport, előfizetés vagy Azure AD-bérlő számára.
    
- A teljesítmény és a megbízhatóság különféle fejlesztése
    - Az Azure-fájlmegosztás észlelésének módosítása sikertelen lehet, ha a virtuális hálózat (VNET) és a tűzfalszabályok konfigurálva vannak a Storage-fiókban.
    - Csökkentett memóriahasználat a visszahíváshoz társítva. 
    - Jobb teljesítmény a [Meghívási-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) parancsmag használatakor.
    - Egyéb egyéb megbízhatósági változások. 
    
### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató [kiértékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a nano Server-telepítés esetén.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A nem támogatott karakterek listáját a [hibaelhárítási útmutatóban](storage-sync-files-troubleshoot.md#handling-unsupported-characters) találja.
- Egy ponttal lezáró fájlok vagy könyvtárak.
- A 2048 karakternél hosszabb elérési utak.
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
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a [Meghívási AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoport, előfizetés vagy Azure AD-bérlőbe. A Storage Sync szolgáltatás vagy a Storage-fiók áthelyezése után meg kell adnia a Microsoft. StorageSync alkalmazás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Felhőbeli végpont létrehozásakor a Storage Sync szolgáltatásnak és a Storage-fióknak ugyanabban az Azure AD-bérlőben kell lennie. A Felhőbeli végpont létrehozása után a Storage Sync szolgáltatás és a Storage-fiók áthelyezhető a különböző Azure AD-bérlők között.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.

## <a name="agent-version-9100"></a>Ügynök verziója 9.1.0.0
Az alábbi kibocsátási megjegyzések a 2019-es december 12-én kiadott Azure File Sync-ügynök verziójának 9.1.0.0 vonatkoznak. Ezek a megjegyzések a 9.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

Ebben a kiadásban rögzített probléma:  
- A szinkronizálás a következő hibák egyikével meghiúsult a Azure File Sync Agent 9,0-es verzióra való frissítés után:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Ügynök verziója 9.0.0.0
A következő kibocsátási megjegyzések az Azure File Sync-ügynök verziójának 9.0.0.0 vonatkoznak (2019. december 2.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Önkiszolgáló visszaállítás támogatása
    - A felhasználók mostantól visszaállíthatják a fájljaikat az előző verzió funkció használatával. A V9-es kiadás előtt a korábbi verzió funkció nem támogatott olyan köteteken, amelyeken engedélyezve van a felhőalapú rétegek használata. Ezt a funkciót külön kell engedélyezni az egyes köteteken, amelyeken engedélyezve van egy, a felhőalapú rétegek használatát engedélyező végpont. További információk:  
Önkiszolgáló [Visszaállítás az előző verziók és a VSS (Kötet árnyékmásolata szolgáltatás) használatával](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Nagyobb fájlmegosztás-méretek támogatása 
    - Azure File Sync mostantól legfeljebb 64TiB és 100 000 000 fájlt támogat egyetlen, szinkronizált névtérben.  
 
- Az deduplikálás támogatása a 2019-es kiszolgálón 
    - Az deduplikálás mostantól támogatott a Windows Server 2019-on futó felhőalapú rétegek esetén. Ahhoz, hogy támogassa az deduplikált köteteken a felhőalapú rétegek, telepíteni kell a Windows Update [KB4520062](https://support.microsoft.com/help/4520062) . 
 
- Magasabb fájlméret a fájl számára a szinthez 
    - A fájlnak a réteghez való minimális mérete mostantól a fájlrendszer fürtjének méretétől függ (a fájlrendszer fürt méretének kétszerese). Alapértelmezés szerint például az NTFS fájlrendszerű fürt mérete 4KB, a fájlhoz tartozó minimális fájlméret pedig 8 kb. 
 
- Hálózati kapcsolat tesztelése parancsmag 
    - Azure File Sync konfiguráció részeként több szolgáltatás-végpontot is kapcsolatba kell lépnie. Mindegyiknek saját DNS-névvel kell rendelkeznie, amelynek a kiszolgálónak elérhetőnek kell lennie. Ezek az URL-címek arra a régióra is jellemzőek, amelyhez a kiszolgáló regisztrálva van. A kiszolgáló regisztrálását követően a kapcsolati teszt parancsmag (PowerShell és kiszolgáló regisztrálása) használható a kiszolgálóhoz tartozó összes URL-címmel folytatott kommunikáció tesztelésére. Ez a parancsmag segít elhárítani a hiányos kommunikációt, ami megakadályozza, hogy a kiszolgáló teljes mértékben működjön a Azure File Sync, és használható a proxy és a tűzfal konfigurációinak finomhangolására.  
 
        A hálózati kapcsolati teszt futtatásához futtassa a következő PowerShell-parancsokat: 
 
        "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll" modul importálása  
        Teszt – StorageSyncNetworkConnectivity
 
- A kiszolgálói végpontok javításának eltávolítása, ha engedélyezve van a felhőalapú rétegek használata 
    - Ahogy korábban is, a kiszolgálói végpont eltávolítása nem eredményezi a fájlok eltávolítását az Azure-fájlmegosztás során. A helyi kiszolgálón található újraelemzési pontok viselkedése azonban megváltozott. A kiszolgálói végpontok eltávolításakor a rendszer törli az újraelemzési pontokat (a kiszolgálón nem helyi fájlok mutatóit). A teljes gyorsítótárban lévő fájlok a kiszolgálón maradnak. Ezzel a fejlesztéssel megakadályozható, hogy a rendszer az [árva rétegekből álló fájlokat](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) a kiszolgálói végpontok eltávolításakor megakadályozza. Ha a kiszolgálói végpontot újra létrehozza, a rendszer újból létrehozza a lépcsőzetes fájlok újraelemzési pontjait a kiszolgálón.  
 
- Teljesítmény-és megbízhatósági változások 
    - Csökkentett visszahívási hibák. A visszahívás mérete mostantól automatikusan igazodik a hálózati sávszélesség alapján. 
    - Továbbfejlesztett letöltési teljesítmény új kiszolgáló szinkronizálási csoportba való felvételekor. 
    - A korlátozási ütközések miatt nem szinkronizált fájlok csökkentése. 
    - A fájlok sikertelenek lesznek, vagy bizonyos helyzetekben váratlanul visszahívhatók, ha a kiszolgálói végpont elérési útja egy kötet csatlakoztatási pontja.
    
### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató [kiértékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a nano Server-telepítés esetén.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A nem támogatott karakterek listáját a [hibaelhárítási útmutatóban](storage-sync-files-troubleshoot.md#handling-unsupported-characters) találja.
- Egy ponttal lezáró fájlok vagy könyvtárak.
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
- A többcsoportos fájlok elérhetetlenné válnak, ha a rendszer nem hívja meg a fájlokat a kiszolgálói végpont törlése előtt. A fájlok elérésének visszaállításához hozza létre újra a kiszolgálói végpontot. Ha a kiszolgáló-végpont törlése vagy a Felhőbeli végpont törlése óta 30 nap telt el, akkor a nem visszanevezett, többelemű fájlok használhatatlanná válhatnak. További információért lásd a kiszolgálói [végpont törlése után a kiszolgálón nem érhetők el a lépcsőzetes fájlok](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a [Meghívási AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.
- Ha a pagefile.sys olyan köteten található, amelynél engedélyezve van a felhőalapú rétegek használata, előfordulhat, hogy a fájlok sikertelenek lehetnek. A pagefile.sys egy olyan köteten kell lennie, amely a felhőalapú rétegek letiltásával rendelkezik.

## <a name="agent-version-8000"></a>Ügynök verziója 8.0.0.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 8.0.0.0 vonatkoznak (2019. október 8.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Teljesítmény-javító funkciók visszaállítása
    - A helyreállítás gyorsabb helyreállítási ideje Azure Backupon keresztül. A visszaállított fájlok sokkal gyorsabban szinkronizálva lesznek Azure File Sync kiszolgálókkal. 
- Továbbfejlesztett felhőalapú többplatformos portál  
    - Ha olyan lépcsőzetes fájlokkal rendelkezik, amelyek nem tudnak visszahívni, most megtekintheti a visszahívási hibákat a kiszolgálói végpont tulajdonságaiban. Emellett a kiszolgálói végpont állapota mostantól hibát és enyhítő lépéseket jelez, ha a Felhőbeli rétegű szűrő illesztőprogramja nincs betöltve a kiszolgálón.
- Egyszerűbb ügynök telepítése
    - A Az\AzureRM PowerShell-modul már nem szükséges a kiszolgáló regisztrálásához, amely egyszerűbbé és gyors telepítést tesz szükségessé.
- A teljesítmény és a megbízhatóság különféle fejlesztése

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató [kiértékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a nano Server-telepítés esetén.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A nem támogatott karakterek listáját a [hibaelhárítási útmutatóban](storage-sync-files-troubleshoot.md#handling-unsupported-characters) találja.
- Egy ponttal lezáró fájlok vagy könyvtárak.
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
- A többcsoportos fájlok elérhetetlenné válnak, ha a rendszer nem hívja meg a fájlokat a kiszolgálói végpont törlése előtt. A fájlok elérésének visszaállításához hozza létre újra a kiszolgálói végpontot. Ha a kiszolgáló-végpont törlése vagy a Felhőbeli végpont törlése óta 30 nap telt el, akkor a nem visszanevezett, többelemű fájlok használhatatlanná válhatnak. További információért lásd a kiszolgálói [végpont törlése után a kiszolgálón nem érhetők el a lépcsőzetes fájlok](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a [Meghívási AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.

## <a name="agent-version-7200"></a>Ügynök verziója 7.2.0.0
A következő kiadási megjegyzések a Azure File Sync ügynök verziójának 7.2.0.0 vonatkoznak, amely 2019. július 24-én jelent meg. Ezek a megjegyzések a 7.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- A Storage Sync Agent (FileSyncSvc) összeomlik, ha a proxy konfigurációja null értékű.
- A kiszolgálói végpont elindítja a BCDR (hiba: 0x80c80257-ECS_E_BCDR_IN_PROGRESS), ha a kiszolgálón több végpontnak is ugyanaz a neve.
- A felhő-rétegek megbízhatóságának fejlesztése.

## <a name="agent-version-7100"></a>Ügynök verziója 7.1.0.0
A következő kibocsátási megjegyzések a Azure File Sync-ügynök verziójának 7.1.0.0 vonatkoznak, amely 2019. július 12-én jelent meg. Ezek a megjegyzések a 7.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- A kiszolgálói végpontok SMB-kapcsolaton keresztüli elérésének vagy tallózásának lassúnak kell lennie a Windows Server 2012 R2 rendszeren. 
- Nagyobb CPU-kihasználtság a Azure File Sync v6-ügynök telepítése után.
- A felhő-rétegek telemetria fejlesztése.
- A felhőalapú rétegek és a szinkronizálás különféle megbízhatósági fejlesztése.

## <a name="agent-version-7000"></a>Ügynök verziója 7.0.0.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 7.0.0.0 vonatkoznak (2019. június 19.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Nagyobb fájlmegosztás-méretek támogatása
    - A nagyobb Azure-fájlmegosztás előzetes verziójával a fájl-szinkronizálás támogatásának korlátai is növekednek. Ebben az első lépésben Azure File Sync mostantól legfeljebb 25 TB és 50 000 000 fájlt támogat egyetlen, szinkronizálási névtérben. A nagyméretű fájlmegosztás előzetes verziójának alkalmazásához töltse ki ezt az űrlapot https://aka.ms/azurefilesatscalesurvey . 
- A tűzfal és a virtuális hálózat beállításainak támogatása a Storage-fiókoknál
    - Azure File Sync mostantól támogatja a Storage-fiókok tűzfal-és virtuális hálózati beállításait. A tűzfal és a virtuális hálózat beállításának konfigurálásához tekintse meg a [tűzfal és a virtuális hálózat beállításainak konfigurálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)című témakört.
- PowerShell-parancsmag a fájlok azonnali szinkronizálásához az Azure-fájlmegosztás esetében
    - Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a Meghívási AzStorageSyncChangeDetection PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Ez a parancsmag olyan forgatókönyvekhez készült, amelyekben bizonyos típusú automatizált folyamatok módosítják az Azure-fájlmegosztást, vagy ha a rendszergazda módosítja a módosításokat (például fájlokat és címtárakat helyez át a megosztásba). A végfelhasználói változások esetében javasoljuk, hogy telepítse a Azure File Sync ügynököt egy IaaS virtuális gépre, és a végfelhasználók hozzáférhessenek a fájlmegosztást a IaaS virtuális gépen keresztül. Így az összes módosítás gyorsan szinkronizálva lesz más ügynökökkel anélkül, hogy a Meghívási-AzStorageSyncChangeDetection parancsmagot kellene használnia. További információért lásd a [Meghívási AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) dokumentációját.
- Továbbfejlesztett portál-élmény, ha olyan fájlokat észlel, amelyek nem szinkronizálhatók
    - Ha vannak olyan fájlok, amelyek nem szinkronizálhatók, mostantól különbséget teszünk az átmeneti és az állandó hibák között a portálon. Az átmeneti hibák általában a rendszergazda beavatkozása nélkül oldhatók meg. Például egy jelenleg használatban lévő fájl nem fog szinkronizálni, amíg a fájlleíró be nem zárul. Állandó hibák esetén az egyes hibák által érintett fájlok száma látható. Az állandó hibák száma a szinkronizálási csoportban lévő összes kiszolgáló-végpont oszlopának nem szinkronizált fájljaiban is megjelenik.
- Továbbfejlesztett Azure Backup fájl szintű visszaállítás
    - A rendszer mostantól a Azure Backup használatával visszaállított egyedi fájlokat észleli és szinkronizálja a kiszolgálói végponttal.
- Továbbfejlesztett felhő-rétegek visszahívás-parancsmagjának megbízhatósága 
    - A meghívó-StorageSyncFileRecall parancsmag mostantól lehetővé teszi az ügyfelek számára a fájl-újrapróbálkozások számát és a fájlok újrapróbálkozási késleltetését a Robocopy esetében. Korábban ez a parancsmag a megadott elérési úton lévő összes rétegű fájlt felidézi véletlenszerű sorrendben. A New-Order paraméterrel ez a parancsmag először felidézi a legforróbb adatmennyiséget, és tiszteletben tartja a felhő-előállítási szabályzatot (ha a házirend teljesül, vagy a kötet szabad területe teljesül).
- Csak TLS 1,2-támogatás (a TLS 1,0 és 1,1 le van tiltva)
    - A Azure File Sync mostantól csak a TLS 1,0 és a 1,1 letiltott kiszolgálókon támogatja a TLS 1,2 használatát. A fejlesztés előtt a kiszolgáló regisztrálása meghiúsul, ha a TLS 1,0 és a 1,1 le lett tiltva a kiszolgálón.
- A szinkronizálás és a felhőalapú rétegek különböző teljesítmény-és megbízhatósági fejlesztése
    - Ebben a kiadásban számos megbízhatósági és teljesítménybeli újdonság van. Némelyikük úgy van megcélozva, hogy hatékonyabbá tegye a felhőt, és a Azure File Sync egészében jobban működjön, ha sávszélesség-szabályozási ütemterv van beállítva.

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató [kiértékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a nano Server-telepítés esetén.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A nem támogatott karakterek listáját a [hibaelhárítási útmutatóban](storage-sync-files-troubleshoot.md#handling-unsupported-characters) találja.
- Egy ponttal lezáró fájlok vagy könyvtárak.
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
- A többcsoportos fájlok elérhetetlenné válnak, ha a rendszer nem hívja meg a fájlokat a kiszolgálói végpont törlése előtt. A fájlok elérésének visszaállításához hozza létre újra a kiszolgálói végpontot. Ha a kiszolgáló-végpont törlése vagy a Felhőbeli végpont törlése óta 30 nap telt el, akkor a nem visszanevezett, többelemű fájlok használhatatlanná válhatnak.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.

## <a name="agent-version-6300"></a>Ügynök verziója 6.3.0.0
A következő kibocsátási megjegyzések a Azure File Sync-ügynök verziójának 6.3.0.0, amely 2019. június 27-én jelent meg. Ezek a megjegyzések a 6.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- A kiszolgálói végpontok SMB-kapcsolaton keresztüli elérésének vagy tallózásának lassúnak kell lennie a Windows Server 2012 R2 rendszeren 
- Nagyobb CPU-kihasználtság a Azure File Sync v6-ügynök telepítése után
- A felhő-rétegek telemetria fejlesztése

## <a name="agent-version-6200"></a>Ügynök verziója 6.2.0.0
A következő kibocsátási megjegyzések a Azure File Sync-ügynök verziójának 6.2.0.0 vonatkoznak, amely 2019. június 13-án jelent meg. Ezek a megjegyzések a 6.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- A kiszolgálói végpont létrehozása után a magas CPU-használat akkor fordulhat elő, ha a háttérben történő visszahívás a fájlok letöltését a kiszolgálóra
- A szinkronizálási és a felhő-előfizetési műveletek meghiúsulnak a jogkivonat lejárata miatti ECS_E_SERVER_CREDENTIAL_NEEDED hibával
- A fájl visszahívása sikertelen lehet, ha a fájl letöltésére szolgáló URL-cím foglalt karaktereket tartalmaz 

## <a name="agent-version-6100"></a>Ügynök verziója 6.1.0.0
A következő kibocsátási megjegyzések a 2019-es számú Azure File Sync ügynök verziójának 6.1.0.0 vonatkoznak. Ezek a megjegyzések a 6.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- A Windows felügyeleti központ nem tudja megjeleníteni az ügynök verziószámát és a kiszolgálói végpont konfigurációját azon kiszolgálókon, amelyeken telepítve van a Azure File Sync ügynök 6,0-es verziója.

## <a name="agent-version-6000"></a>Ügynök verziója 6.0.0.0
A következő kibocsátási megjegyzések az Azure File Sync-ügynök verziójának 6.0.0.0 vonatkoznak (2019. április 22.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Ügynök automatikus frissítésének támogatása
  - Meghallgatta a visszajelzését, és hozzáadta az automatikus frissítési szolgáltatást a Azure File Sync Server-ügynökhöz. További információ: [Azure file Sync ügynök frissítési szabályzata](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Azure file share ACL-ek támogatása
  - A Azure File Sync mindig támogatja a kiszolgálói végpontok közötti ACL-ek szinkronizálását, de az ACL-eket nem szinkronizálták a Felhőbeli végponttal (Azure-fájlmegosztás). Ez a kiadás támogatja a kiszolgálók és a Felhőbeli végpontok közötti ACL-ek szinkronizálását.
- Párhuzamos feltöltési és letöltési szinkronizálási munkamenetek egy kiszolgálói végponthoz 
  - A kiszolgálói végpontok már támogatják a fájlok egyidejű feltöltését és letöltését. Nincs többé várakozás a letöltés befejezésére, hogy a fájlok fel legyenek töltve az Azure-fájlmegosztásba. 
- Új felhő-rétegű parancsmagok a kötet-és a rétegek állapotának lekéréséhez
  - Mostantól két új, kiszolgáló-helyi PowerShell-parancsmag használható a felhőalapú rétegek és a fájlok visszahívási adatainak beszerzéséhez. A naplózási információkat a kiszolgáló két eseményének csatornáján teszik elérhetővé:
    - A Get-StorageSyncFileTieringResult felsorolja az összes fájlt és azok elérési útját, amelyek nem a többrétegű, hanem a jelentésekre vonatkozó okokat is felsorolják.
    - A Get-StorageSyncFileRecallResult jelentést készít az összes fájl-visszahívási eseményről. Felsorolja az összes visszahívott fájlt és annak elérési útját, valamint az adott visszahívás sikerességét vagy hibáját.
  - Alapértelmezés szerint mindkét esemény-csatorna akár 1 MB-ot is tárolhat – az Event Channel-méret növelésével növelheti a jelentett fájlok mennyiségét.
- FIPS-üzemmód támogatása
  - Azure File Sync mostantól támogatja a FIPS üzemmód engedélyezését azon kiszolgálókon, amelyeken telepítve van a Azure File Sync ügynök.
    - A FIPS-mód a kiszolgálón való engedélyezése előtt telepítse a Azure File Sync Agent és a [PackageManagement modult](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) a kiszolgálóra. Ha az FIPS már engedélyezve van a kiszolgálón, [töltse le manuálisan](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) a [PackageManagement modult](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) a kiszolgálóra.
- A felhőalapú rétegek és a szinkronizálás különféle megbízhatósági fejlesztése

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató [kiértékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a nano Server-telepítés esetén.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, nem támogatott, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A nem támogatott karakterek listáját a [hibaelhárítási útmutatóban](storage-sync-files-troubleshoot.md#handling-unsupported-characters) találja.
- Egy ponttal lezáró fájlok vagy könyvtárak.
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
- A többcsoportos fájlok elérhetetlenné válnak, ha a rendszer nem hívja meg a fájlokat a kiszolgálói végpont törlése előtt. A fájlok elérésének visszaállításához hozza létre újra a kiszolgálói végpontot. Ha a kiszolgáló-végpont törlése vagy a Felhőbeli végpont törlése óta 30 nap telt el, akkor a nem visszanevezett, többelemű fájlok használhatatlanná válhatnak.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.

## <a name="agent-version-5200"></a>Ügynök verziója 5.2.0.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 5.2.0.0 készültek, amely 2019. április 4-én jelent meg. Ezek a megjegyzések a 5.0.2.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- Megbízhatósági változások az offline adatátvitelhez és az adatátvitel folytatásához szükséges funkciók
- A telemetria szinkronizálásának fejlesztése

## <a name="agent-version-5100"></a>Ügynök verziója 5.1.0.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 5.1.0.0, amely 2019. március 7-én jelent meg. Ezek a megjegyzések a 5.0.2.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- Előfordulhat, hogy a fájlok nem tudnak szinkronizálni a 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED), ha a változás enumerálása sikertelen a kiszolgálón
- Ha egy szinkronizálási munkamenet vagy fájl 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE) kap, akkor a szinkronizálás most újra próbálkozik a művelettel.
- Előfordulhat, hogy a fájlok nem szinkronizálhatók a 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE).
- A fájlok visszahívásakor előfordulhat, hogy a nagy memóriahasználat fordul elő
- A felhő-rétegek telemetria fejlesztése 

## <a name="agent-version-5020"></a>Ügynök verziója 5.0.2.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 5.0.2.0 vonatkoznak (2019. február 12.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- A Azure Government Cloud támogatása
  - Bővítettük a Azure Government-felhő előzetes verziójának támogatását. Ehhez egy fehér listára vonatkozó előfizetésre és egy speciális, a Microsofttól letölthető ügynökre van szükség. Ha hozzáférést szeretne kapni az előzetes verzióhoz, küldjön nekünk e-mailt közvetlenül a következő címen: [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com) .
- Az deduplikálás támogatása
    - Az deduplikálás mostantól teljes mértékben támogatott a Windows Server 2016 és a Windows Server 2019 rendszeren engedélyezve van a felhőalapú rétegek használata. Ha engedélyezni szeretné a deduplikálás szolgáltatást egy olyan köteten, amelyen engedélyezve van a felhőalapú réteg, a további tárhelyek kiépítésének hiányában további fájlokat is gyorsítótárazhat a helyszínen.
- Az offline adatátvitel támogatása (például Data Boxon keresztül)
    - Egyszerűen áttelepíthet nagy mennyiségű adatmennyiséget a Azure File Syncba bármilyen módon. Kiválaszthatja a Azure Data Box, a AzCopy és a harmadik féltől származó áttelepítési szolgáltatásokat is. Nem kell nagy mennyiségű sávszélességet használnia az Azure-ba való adatgyűjtéshez Data Box esetén – egyszerűen küldje el! További információ: [Offline adatátvitel dokumentumok](https://aka.ms/AFS/OfflineDataTransfer).
- Továbbfejlesztett szinkronizálási teljesítmény
    - Előfordulhat, hogy az ugyanazon a köteten több kiszolgálói végponttal rendelkező ügyfelek lassú szinkronizálási teljesítményt észleltek a jelen kiadás előtt. Azure File Sync létrehoz egy ideiglenes VSS-pillanatképet naponta egyszer a kiszolgálón a megnyitott leíróval rendelkező fájlok szinkronizálásához. A szinkronizálás mostantól támogatja a köteteken szinkronizált több kiszolgálói végpontot, amikor a VSS-szinkronizálási munkamenet aktív. Nincs több várakozás a VSS-szinkronizálási munkamenet befejeződésére, hogy a szinkronizálás a köteten lévő többi kiszolgálói végponton is folytatódjon.
- Továbbfejlesztett monitorozás a portálon
    - A rendszer a Storage Sync Service portálon diagramokat adott hozzá a következő megtekintéséhez:
        - Szinkronizált fájlok száma
        - Átvitt adatátvitel mérete
        - Nem szinkronizált fájlok száma
        - Visszahívott adatmennyiség
        - Kiszolgáló kapcsolati állapota
    - További információért lásd: [Azure file Sync figyelése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Továbbfejlesztett méretezhetőség és megbízhatóság
    - A fájlrendszerbeli objektumok (könyvtárak és fájlok) maximális száma a címtárban 1 000 000-ra nőtt. Az előző korlát 200 000 volt.
    - A szinkronizálás megkísérli folytatni az adatátvitelt, és nem küldi újra, amikor a nagyméretű fájlok átvitele megszakad. 

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató [kiértékelési eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Windows Server Core vagy a nano Server telepítési lehetőségein.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.
- Az FIPS mód nem támogatott, és le kell tiltani. 

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, nem támogatott, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A nem támogatott karakterek listáját a [hibaelhárítási útmutatóban](storage-sync-files-troubleshoot.md#handling-unsupported-characters) találja.
- Egy ponttal lezáró fájlok vagy könyvtárak.
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
- A többcsoportos fájlok elérhetetlenné válnak, ha a rendszer nem hívja meg a fájlokat a kiszolgálói végpont törlése előtt. A fájlok elérésének visszaállításához hozza létre újra a kiszolgálói végpontot. Ha a kiszolgáló-végpont törlése vagy a Felhőbeli végpont törlése óta 30 nap telt el, akkor a nem visszanevezett, többelemű fájlok használhatatlanná válhatnak.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.
