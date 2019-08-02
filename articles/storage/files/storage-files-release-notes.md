---
title: Kibocsátási megjegyzések a Azure File Sync-ügynökhöz | Microsoft Docs
description: A Azure File Sync ügynök kibocsátási megjegyzései.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 7/24/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: ad0927c551f331a3a6f719797dae7598961d78c1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721670"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>A Azure File Sync ügynök kibocsátási megjegyzései
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. A Windows Server-telepítéseket az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
Az Azure File Sync ügynök alábbi verziói támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadás dátuma | Állapot |
|----|----------------------|--------------|------------------|
| 2019. júliusi kumulatív frissítés – [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019. július 24. | Támogatott – [Berepülés](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#automatic-agent-lifecycle-management) |
| 2019. júliusi kumulatív frissítés – [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019. július 12. | Támogatott |
| V7-kiadás – [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | Június 19., 2019 | Támogatott |
| 2019. júniusi kumulatív frissítés – [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 2019. június 27. | Támogatott |
| 2019. júniusi kumulatív frissítés – [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | Június 13., 2019 | Támogatott |
| 2019. május kumulatív frissítés – [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 2019. május 7. | Támogatott |
| V6-os kiadás – [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 2019. április 21. | Támogatott |
| 2019. április kumulatív frissítés – [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 2019. április 4. | Támogatott |
| Március 2019 kumulatív frissítés – [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | Március 7., 2019 | Támogatott |
| V5-kiadás – [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | Február 12., 2019 | Támogatott |
| 2019. januári kumulatív frissítés – [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | Január 14., 2019 | Támogatott |
| 2018. decemberi kumulatív frissítés – [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 2018. december 10. | Támogatott |
| December 2018 kumulatív frissítés | 4.1.0.0 | 2018. december 4. | Támogatott |
| V4-kiadás | 4.0.1.0 | November 13., 2018 | Támogatott |
| Kumulatív frissítés (2018) | 3.3.0.0 | 2018. szeptember 24. | Támogatott – az ügynök verziószáma 2019. július 19-én lejár |
| Kumulatív frissítés 2018 augusztus | 3.2.0.0 | 2018. augusztus 15. | Támogatott – az ügynök verziószáma 2019. július 19-én lejár |
| Általános elérhetőség | 3.1.0.0 | Július 19., 2018 | Támogatott – az ügynök verziószáma 2019. július 19-én lejár |
| Lejárt ügynökök | 1.1.0.0 – 3.0.13.0 | – | Nem támogatott – az ügynök verziói lejártak 2018 október 1-jén |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-7200"></a>Ügynök verziója 7.2.0.0
A következő kiadási megjegyzések a Azure File Sync ügynök verziójának 7.2.0.0 vonatkoznak, amely 2019. július 24-én jelent meg. Ezek a megjegyzések a 7.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- A Storage Sync Agent (FileSyncSvc) összeomlik, ha a proxy konfigurációja null értékű.
- A kiszolgálói végpont elindítja a BCDR (hiba: 0x80c80257-ECS_E_BCDR_IN_PROGRESS), ha a kiszolgálón több végpont neve azonos.
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
    - A nagyobb Azure-fájlmegosztás előzetes verziójával a fájl-szinkronizálás támogatásának korlátai is növekednek. Ebben az első lépésben Azure File Sync mostantól támogatja a 25TB-és 50million-fájlokat egyetlen, szinkronizált névtérben. A nagyméretű fájlmegosztás előzetes verziójának alkalmazásához töltse ki ezt az űrlapot https://aka.ms/azurefilesatscalesurvey. 
- Továbbfejlesztett Azure Backup fájl szintű visszaállítás
    - A rendszer mostantól a Azure Backup használatával visszaállított egyedi fájlokat észleli és szinkronizálja a kiszolgálói végponttal.
- Továbbfejlesztett felhő-rétegek visszahívás-parancsmagjának megbízhatósága 
    - A Cloud rétegű visszahívás-parancsmag (Call-StorageSyncFileRecall) mostantól támogatja a fájl-újrapróbálkozások számát és az újrapróbálkozási késleltetést, hasonlóan a robocopyhoz.
- Csak TLS 1,2-támogatás (a TLS 1,0 és 1,1 le van tiltva)
    - A Azure File Sync mostantól támogatja a TLS 1,2 használatát csak olyan kiszolgálókon, amelyeken a TLS 1,0 és a 1,1 le van tiltva. A fejlesztés előtt a kiszolgáló regisztrálása meghiúsul, ha a TLS 1,0 és a 1,1 le lett tiltva a kiszolgálón.
- A szinkronizálás és a felhőalapú rétegek különböző teljesítmény-és megbízhatósági fejlesztése
    - Ebben a kiadásban számos megbízhatósági és teljesítménybeli újdonság van. Némelyikük úgy van megcélozva, hogy hatékonyabbá tegye a felhőt, és a Azure File Sync egészében jobban működjön, ha sávszélesség-szabályozási ütemterv van beállítva.

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató kiértékelési [eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

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
- Nem támogatott karaktereket tartalmazó fájlok. A [](storage-sync-files-troubleshoot.md#handling-unsupported-characters) nem támogatott karakterek listáját a hibaelhárítási útmutatóban találja.
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
- A felhő-rétegek nem támogatottak a rendszerköteten. Ha kiszolgálói végpontot szeretne létrehozni a rendszerköteten, tiltsa le a Felhőbeli rétegek létrehozását a kiszolgálói végpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)-fiókhoz).

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
- A szinkronizálási és a felhő-előfizetési műveletek sikertelenek lehetnek a ECS_E_SERVER_CREDENTIAL_NEEDED miatti hibák miatt
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
    - A FIPS-mód a kiszolgálón való engedélyezése előtt telepítse a Azure File Sync Agent és a [PackageManagement modult](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) a kiszolgálóra. Ha az FIPS már engedélyezve van a kiszolgálón, [töltse le manuálisan](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) a [PackageManagement modult](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) a kiszolgálóra.
- A felhőalapú rétegek és a szinkronizálás különféle megbízhatósági fejlesztése

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató kiértékelési [eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

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
- Nem támogatott karaktereket tartalmazó fájlok. A [](storage-sync-files-troubleshoot.md#handling-unsupported-characters) nem támogatott karakterek listáját a hibaelhárítási útmutatóban találja.
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
- A felhő-rétegek nem támogatottak a rendszerköteten. Ha kiszolgálói végpontot szeretne létrehozni a rendszerköteten, tiltsa le a Felhőbeli rétegek létrehozását a kiszolgálói végpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)-fiókhoz).

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
- Előfordulhat, hogy a fájlok nem szinkronizálhatók a 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED), ha a módosítás enumerálása sikertelen a kiszolgálón
- Ha egy szinkronizálási munkamenet vagy fájl 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE) kap, akkor a szinkronizálás most újra megpróbálkozik a művelettel.
- Előfordulhat, hogy a fájlok nem szinkronizálhatók a következő 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- A fájlok visszahívásakor előfordulhat, hogy a nagy memóriahasználat fordul elő
- A felhő-rétegek telemetria fejlesztése 

## <a name="agent-version-5020"></a>Ügynök verziója 5.0.2.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 5.0.2.0 vonatkoznak (2019. február 12.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- A Azure Government Cloud támogatása
  - Bővítettük a Azure Government-felhő előzetes verziójának támogatását. Ehhez egy fehér listára vonatkozó előfizetésre és egy speciális, a Microsofttól letölthető ügynökre van szükség. Ha hozzáférést szeretne kapni az előzetes verzióhoz, küldjön nekünk e [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)-mailt közvetlenül a következő címen:.
- Az deduplikálás támogatása
    - Az deduplikálás mostantól teljes mértékben támogatott a Windows Server 2016 és a Windows Server 2019 rendszeren engedélyezve van a felhőalapú rétegek használata. Ha engedélyezni szeretné a deduplikálás szolgáltatást egy olyan köteten, amelyen engedélyezve van a felhőalapú réteg, a további tárhelyek kiépítésének hiányában további fájlokat is gyorsítótárazhat a helyszínen.
- Az offline adatátvitel támogatása (például Data Boxon keresztül)
    - Egyszerűen áttelepíthet nagy mennyiségű adatmennyiséget a Azure File Syncba bármilyen módon. Kiválaszthatja Azure Data Box, AzCopy és akár harmadik féltől származó áttelepítési szolgáltatást is. Nem kell nagy mennyiségű sávszélességet használnia az Azure-ba való adatgyűjtéshez Data Box esetén – egyszerűen küldje el! További információ: [Offline adatátvitel dokumentumok](https://aka.ms/AFS/OfflineDataTransfer).
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
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató kiértékelési [eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

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
- Nem támogatott karaktereket tartalmazó fájlok. A [](storage-sync-files-troubleshoot.md#handling-unsupported-characters) nem támogatott karakterek listáját a hibaelhárítási útmutatóban találja.
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
- A felhő-rétegek nem támogatottak a rendszerköteten. Ha kiszolgálói végpontot szeretne létrehozni a rendszerköteten, tiltsa le a Felhőbeli rétegek létrehozását a kiszolgálói végpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)-fiókhoz).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.

## <a name="agent-version-4300"></a>Ügynök verziója 4.3.0.0
A következő kiadási megjegyzések a Azure File Sync ügynök verziójának a 2019. január 14-én kiadott 4.3.0.0 vonatkoznak. Ezek a megjegyzések a 4.0.1.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- A fájlok a Azure File Sync ügynök 4. x verzióra való frissítése után nem lesznek leképezve.
- A AfsUpdater. exe mostantól támogatott a Windows Server 2019 rendszeren.
- A szinkronizálás egyéb megbízhatósági fejlesztése. 

## <a name="agent-version-4200"></a>Ügynök verziója 4.2.0.0
A következő kiadási megjegyzések a Azure File Sync-ügynöknek a 2018-es december 10-én kiadott verziójának 4.2.0.0 vonatkoznak. Ezek a megjegyzések a 4.0.1.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- Hiba történt a 0x3B leállításakor vagy leállításakor, ha a rendszer VSS-pillanatképet hoz létre 0x1E.  
- Memóriavesztés léphet fel, ha engedélyezve van a felhőalapú rétegek használata.  

## <a name="agent-version-4100"></a>Ügynök verziója 4.1.0.0
A következő kiadási megjegyzések a Azure File Sync ügynök verziójának 4.1.0.0 vonatkoznak, amely 2018. december 4-én jelent meg. Ezek a megjegyzések a 4.0.1.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:  
- Előfordulhat, hogy a kiszolgáló nem válaszol a Felhőbeli adatszivárgás miatt.  
- Az ügynök telepítése a következő hibával meghiúsul: 1921-es hiba. Nem lehetett leállítani a (z) "Storage Sync Agent" (FileSyncSvc) szolgáltatást.  Ellenőrizze, hogy rendelkezik-e megfelelő jogosultsággal a rendszerszolgáltatások leállításához.  
- A Storage Sync Agent (FileSyncSvc) szolgáltatás összeomlást okozhat, ha magas a memória használata.  
- A felhőalapú rétegek és a szinkronizálás különféle megbízhatósági fejlesztése.

## <a name="agent-version-4010"></a>Ügynök verziója 4.0.1.0
A következő kibocsátási megjegyzések az Azure File Sync-ügynök verziójának 4.0.1.0 vonatkoznak (kiadás: november 13., 2018).

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató kiértékelési [eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Windows Server Core vagy a nano Server telepítési lehetőségein.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.
- Az FIPS mód nem támogatott, és le kell tiltani. 
- Hiba történt a 0x3B leállításakor vagy leállításakor, ha a rendszer VSS-pillanatképet hoz létre 0x1E.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, nem támogatott, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.
- Az adatdeduplikáció és a felhőrétegzés ugyanazon a köteten nem támogatott.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A [](storage-sync-files-troubleshoot.md#handling-unsupported-characters) nem támogatott karakterek listáját a hibaelhárítási útmutatóban találja.
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
- A felhő-rétegek nem támogatottak a rendszerköteten. Ha kiszolgálói végpontot szeretne létrehozni a rendszerköteten, tiltsa le a Felhőbeli rétegek létrehozását a kiszolgálói végpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)-fiókhoz).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- A dátum-alapú felhő-előállítási házirend beállításával adhatja meg, hogy mely fájlok legyenek gyorsítótárazva, ha a megadott számú napon keresztül éri el a rendszer. További információ: a [felhőalapú rétegek áttekintése](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.

## <a name="agent-version-3300"></a>Ügynök verziója 3.3.0.0
A következő kiadási megjegyzések a Azure File Sync ügynök verziójának 3.3.0.0 vonatkoznak, amely 2018. szeptember 24-én jelent meg. Ezek a megjegyzések a 3.1.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

A jelen kiadásban rögzített problémák listája:
- A regisztrált kiszolgáló állapota "offline" állapotban jelenik meg, miután a Azure File Sync ügynök frissítve lett a 3,1-es vagy a 3,2-es verzióra.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás összeomlik a hosszú elérési úttal rendelkező fájlok miatt.
- A kiszolgáló regisztrálása a következő hibával meghiúsul: Nem tölthető be a Klasz. AFS. StorageSyncProtocol. v3 fájl vagy szerelvény.

## <a name="agent-version-3200"></a>Ügynök verziója 3.2.0.0
A következő kiadási megjegyzések a Azure File Sync ügynök verziójának 3.2.0.0 vonatkoznak, amely 2018. augusztus 15-ig jelent meg. Ezek a megjegyzések a 3.1.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

Ez a kiadás a következő javítást tartalmazza:
- A szinkronizálás a memória szivárgása miatt nem sikerül (0x8007000e).

## <a name="agent-version-3100"></a>Ügynök verziója 3.1.0.0
A következő kibocsátási megjegyzések az Azure File Sync-ügynök verziójának 3.1.0.0 vonatkoznak (a kiadás 2018. július 19.).

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató kiértékelési [eszköz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Windows Server Core vagy a nano Server telepítési lehetőségein.
- Az ügynök csak a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynökhöz legalább 2 GB fizikai memóriára van szükség.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.
- Az FIPS mód nem támogatott, és le kell tiltani. 

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- Ne használja a Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrőit vagy egyéb fájlszűrőket. A fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha blokkolnak egyes fájlokat.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, nem támogatott, és nem várt eredményekhez vezethet. Az ügynök telepítése és a kiszolgáló regisztrálása a kiszolgáló rendszerképének telepítése és a Sysprep mini-Setup befejezése után történik.
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
- A többcsoportos fájlok használhatatlanná válnak, ha a fájlok nem lettek meghívva a kiszolgálói végpont törlése előtt.
- A felhő-rétegek nem támogatottak a rendszerköteten. Ha kiszolgálói végpontot szeretne létrehozni a rendszerköteten, tiltsa le a Felhőbeli rétegek létrehozását a kiszolgálói végpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálóvégponton.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)-fiókhoz).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.
