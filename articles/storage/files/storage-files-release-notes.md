---
title: "Az Azure File Sync ügynök kibocsátási megjegyzései | Microsoft Docs"
description: "Az Azure File Sync kibocsátási megjegyzései"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: tamram
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 74f926743713bfd71eb524fdc2794cd7e187166e
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
---
# <a name="azure-file-sync-agent-release-notes"></a>Az Azure File Sync ügynök kibocsátási megjegyzései
Az Azure File Sync (előzetes verzió) lehetővé teszi a szervezet Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy éri el, hogy átalakítja a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótáraivá. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
Az Azure File Sync az alábbi verziókat támogatja:

| Az ügynök verziószáma | Kiadási dátum | Támogatás vége: |
|----------------------|--------------|------------------|
| 2.0.11.0 | 2018-02-08 | Aktuális verzió |
| 1.1.0.0 | 2017-09-26 | 2018-07-30 |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-1100"></a>Az ügynök 1.1.0.0-s verziója
A következő kibocsátási megjegyzések az ügynök 1.1.0.0-s verziójára vonatkoznak, amely 2017. szeptember 9-én jelent meg. Ez a kiadás az Azure File Sync kezdeti előzetes verziójának felel meg!

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
Az Azure File Sync ügynök Windows Serverrel való telepítéséről és konfigurálásáról további információért lásd [az Azure File Sync (előzetes verzió) üzembe helyezésének megtervezésérő](storage-sync-files-planning.md) és [az Azure File Sync (előzetes verzió) üzembe helyezéséről](storage-sync-files-deployment-guide.md) szóló témakört.

- Az ügynöktelepítési csomagot (MSI) emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- A Windows Server Core vagy a Nano Server üzemelő példányain nem támogatott.
- Csak a Windows Server 2016-os és 2012 R2-es verzióiban támogatott.
- Az ügynökhöz legalább 2 GB fizikai memóriára van szükség.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot és nem mellőzik ezen fájlok tartalmának olvasását. További információkat [az Azure File Sync (előzetes verzió) hibaelhárításával](storage-sync-files-troubleshoot.md) foglalkozó cikkben talál.
- Ne használja a Fájlkiszolgálói erőforrás-kezelő fájlszűrőit vagy más fájlszűrőket, mert ezek végtelen szinkronizálási hibákat okozhatnak, amennyiben blokkolnak egyes fájlokat.
- Egy regisztrált kiszolgáló duplikálása (a virtuális gép klónozását is beleértve) váratlan eredményekhez vezethet (előfordulhat, hogy a szinkronizálás sosem konvergál).
- Az adatdeduplikáció és a felhőrétegzés ugyanazon a köteten nem támogatott.
 
### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- 2048 karakternél hosszabb elérési utak
- Biztonsági leíró DACL része, ha nagyobb, mint 2K (ez csak akkor probléma, ha több mint 40 hozzáférésvezérlői bejegyzés van egyetlen elemen)
- Biztonsági leíró SACL része (naplózáshoz használatos)
- Kiterjesztett attribútumok
- Alternatív adatstreamek
- Újraelemzési pontok
- Rögzített hivatkozások
- A tömörítés (ha kiszolgálófájlon van beállítva) nem marad meg, amikor a módosításokat erre a fájlra szinkronizálja más végpontokból
- Az EFS (vagy más felhasználói módú) titkosítással titkosított összes fájl, amely meggátolja, hogy a szolgáltatásunk beolvassa az adatokat 
    
    > [!Note]  
    > Az Azure File Sync mindig titkosítja az átvitt adatokat és az adatok inaktív állapotban is titkosíthatóak az Azure-ban.
 
### <a name="server-endpoints"></a>Kiszolgálóvégpontok
- A kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync egyelőre nem támogatja.
- A kiszolgálóvégpontok nem lehetnek a rendszerköteten (a C:\MyFolder például nem elfogadható elérési út, ha a C:\MyFolder nem csatlakoztatási pont).
- A hibatűrési célú fürtözés csak fürtözött lemezekkel támogatott, fürt megosztott köteteivel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem lehetnek beágyazottak, de ugyanazon a köteten egymás mellett szerepelhetnek.
- Ha egyszerre sok címtárat töröl egy kiszolgálóról (több mint 10 000-et), az szinkronizálási hibákat okozhat – 10 000 címtárasnál kisebb kötegekben törölje a címtárakat, és győződjön meg arról a következő köteg törlése előtt, hogy a törlési műveletek sikeresen szinkronizálva lettek.
- A kötetek gyökerénél nem támogatott.
- Ne tárolja operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálóvégponton.
 
### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- A fájlok megfelelő visszahívásának biztosítása érdekében előfordulhat, hogy a rendszer akár 32 óráig nem rétegzi automatikusan az új vagy módosított fájlokat, beleértve az új kiszolgálóvégpontok konfigurálása utáni első rétegzést is. Egy PowerShell-parancsmagot biztosítunk az igény szerinti rétegzéshez, hogy hatékonyabban értékelhesse ki a rétegzést anélkül, hogy meg kellene várnia a háttérfolyamatokat.
- Ha egy rétegzett fájlt a Robocopyval másik helyre másol, az eredményül kapott fájl nem lesz rétegzett, de előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Amikor SMB-ügyfélről tekint meg fájltulajdonságokat, a fájl metaadatainak SMB-gyorsítótárazása miatt úgy tűnhet, hogy az offline attribútum helytelenül van beállítva.