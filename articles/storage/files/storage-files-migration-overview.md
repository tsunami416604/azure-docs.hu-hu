---
title: Áttelepítés az Azure-fájlmegosztásokra
description: Ismerje meg az Azure-fájlmegosztásokba való áttelepítést, és keresse meg az áttelepítési útmutatót.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247316"
---
# <a name="migrate-to-azure-file-shares"></a>Áttelepítés az Azure-fájlmegosztásokra

Ez a cikk az Azure-fájlmegosztásokba való migrálás alapvető szempontjait ismerteti.

Az áttelepítés alapjai mellett ez a cikk a meglévő, személyre szabott áttelepítési útmutatók listáját is tartalmazza. Ezek az áttelepítési útmutatók segítségével áthelyezheti a fájlokat az Azure-fájlmegosztásokba, és az adatok mai helye és a következő telepítési modell (csak felhőbeli vagy hibrid) szerint vannak rendezve.

## <a name="migration-basics"></a>Áttelepítési tudnivalók

Az Azure-ban több különböző típusú felhőalapú tárhely érhető el. A fájlok Azure-ba való migrálásának alapvető szempontja annak meghatározása, hogy melyik Azure-tárolási lehetőség megfelelő az adatokhoz.

Az Azure-fájlmegosztások kiválóan alkalmas általános célú fájladatokhoz. Tényleg bármit, amit a helyszíni SMB vagy NFS megosztáshoz használ. Az [Azure File Sync](storage-sync-files-planning.md)segítségével szükség esetén gyorsítótárazhatja több Azure-fájlmegosztás tartalmát több helyszíni Windows-kiszolgálón.

Ha egy alkalmazás jelenleg fut egy helyszíni kiszolgálón, majd a fájlok tárolása az Azure-fájlmegosztások lehet az Ön számára, az alkalmazástól függően. Az azure-beli futtatáshoz szüntesse meg az alkalmazást, és az Azure-fájlmegosztásokat megosztott tárolóként használhatja. Ebben a forgatókönyvben az Azure Disks is figyelembe [veheti.](../../virtual-machines/windows/managed-disks-overview.md) A felhőalapú alkalmazások, amelyek nem függnek az SMB vagy a számítógép-helyi hozzáférést az adatokhoz vagy a megosztott hozzáférés, objektum tárolása, például az [Azure blobok](../blobs/storage-blobs-overview.md), gyakran a legjobb választás.

A kulcs minden áttelepítés, hogy rögzítse az összes megfelelő fájlhűség, amikor a fájlok áttelepítése a jelenlegi tárolási hely az Azure-ba. A megfelelő Azure-tárhely kiválasztásának segítése az azure storage-beállítás által támogatott hűség és a forgatókönyv által igényelt szempontja is. Az általános célú fájladatok hagyományosan a fájl metaadataitól függnek. Előfordulhat, hogy az alkalmazásadatok nem. A fájlnak két alapvető összetevője van:

- **Adatfolyam**: A fájl adatfolyama tárolja a fájl tartalmát.
- **Fájl metaadatai**: A fájl metaadatai több alösszetevőből állnak:
   * Fájlattribútumok: Például írásvédettek.
   * Fájlengedélyek: *NTFS-engedélyeknek* vagy *fájl- és mappa hozzáférés-megfeleltetési engedélyeknek nevezik.*
   * Időbélyegek: Leginkább a *create-* és *utolsó módosított* időbélyegek.
   * Alternatív adatfolyam: Nagyobb mennyiségű nem szabványos tulajdonság tárolására szolgál.

A fájlhűség ezért az áttelepítés során úgy határozható meg, mint a forrásösszes vonatkozó fájlinformáció tárolásának, az áttelepítési eszközzel való átvitelképességének és az áttelepítés céltárolóján való tárolásának lehetősége.

Annak érdekében, hogy az áttelepítés a lehető leggördülékenyebben haladjon, azonosítsa [az igényeinek leginkább megfelelő másolási eszközt,](#migration-toolbox) és egyezzen meg a tárolási célakkal a forrással.

Figyelembe véve az előző adatokat, világossá válik, hogy mi a céltároló általános célú fájlok az Azure-ban: [Azure fájlmegosztások.](storage-files-introduction.md) Az Azure-blobokban lévő objektumtárolással összehasonlítva a fájlmetaadatok natív módon tárolhatók egy Azure-fájlmegosztásban lévő fájlokon.

Az Azure-fájlmegosztások megőrzik a fájl- és mappahierarchiát is. Továbbá:
* Az NTFS-engedélyek a helyszíni fájlokban és mappákban tárolhatók.
* Az AD-felhasználók (vagy az Azure AD DS-felhasználók) natív módon hozzáférhetnek egy Azure-fájlmegosztáshoz. 
    A jelenlegi identitásukat használják, és a megosztási engedélyek, valamint a fájl- és mappahozzáférés-hozzáférés alapján kapnak hozzáférést. A viselkedés nem eltérően, amikor a felhasználók csatlakoznak egy helyszíni fájlmegosztás.
*  Az alternatív adatfolyam a fájlhűség elsődleges szempontja, amely jelenleg nem tárolható egy Azure-fájlmegosztásban lévő fájlon.
   Az Azure File Sync esetén a helyszíni állapotban marad meg.

* [További információ az Azure-fájlmegosztások AD-hitelesítéséről](storage-files-identity-auth-active-directory-enable.md)
* [További információ az Azure Active Directory tartományi szolgáltatások (AAD DS) Azure-fájlmegosztásokhitelesítéséről](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Migrálási útmutatók

Az alábbi táblázat részletes áttelepítési útmutatókat sorol fel.

Navigáljon a következő módon:
1. Keresse meg annak a forrásrendszernek a sorát, amelyen a fájlok jelenleg vannak tárolva.
2. Döntse el, hogy egy hibrid telepítést céloz-e meg, ahol az Azure File Sync használatával gyorsítótárazza egy vagy több Azure-fájlmegosztás tartalmát a helyszínen, vagy ha közvetlenül a felhőben szeretné használni az Azure-fájlmegosztásokat. Válassza ki a döntést tükröző céloszlopot.
3. A forrás és a cél metszéspontjában a táblázatcella felsorolja az elérhető áttelepítési eseteket. Válassza ki az egyiket, ha közvetlenül szeretne kapcsolódni a részletes áttelepítési útmutatóhoz.

Egy hivatkozás nélküli forgatókönyv még nem rendelkezik közzétett áttelepítési útmutatóval. Időnként ellenőrizze ezt a táblázatot a frissítésekért. Az új útmutatókat akkor tesszük közzé, ha rendelkezésre állnak.

| Forrás | Cél: </br>Hibrid telepítés | Cél: </br>Csak felhőalapú telepítés |
|:---|:--|:--|
| | Szerszámkombináció:| Szerszámkombináció: |
| Windows Server 2012 R2 és újabb | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure Fájlszinkronizálás + DataBox](storage-sync-offline-data-transfer.md)</li><li>Tárolóáttelepítési szolgáltatás + Azure-fájlszinkronizálás</li></ul> | <ul><li>Azure File Sync</li><li>Azure Fájlszinkronizálás + DataBox</li><li>Tárolóáttelepítési szolgáltatás + Azure-fájlszinkronizálás</li><li>Robocopy</li></ul> |
| Windows Server 2012 és régebbi | <ul><li>Azure Fájlszinkronizálás + DataBox</li><li>Tárolóáttelepítési szolgáltatás + Azure-fájlszinkronizálás</li></ul> | <ul><li>Tárolóáttelepítési szolgáltatás + Azure-fájlszinkronizálás</li><li>Robocopy</li></ul> |
| Hálózati tároló (NAS) | <ul><li>[Azure Fájlszinkronizálás + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux / Samba | <ul><li>[RoboCopy + Azure fájlszinkronizálás](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| StorSimple 8100 / 8600 | <ul><li>[Azure File Sync + 8020 virtuális készülék](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Áttelepítési eszköztár

### <a name="file-copy-tools"></a>Fájlmásolási eszközök

Számos Microsoft és nem Microsoft fájlmásolási eszköz áll rendelkezésre. Az áttelepítési forgatókönyvhöz megfelelő fájlmásolási eszköz kiválasztásához három alapvető kérdést kell figyelembe vennie:

* Támogatja a másolási eszköz egy adott fájlpéldány forrását és célhelyét? 
    * Támogatja a hálózati elérési utat és/vagy az elérhető protokollokat (például REST/SMB/NFS) a forrás- és céltárolási helyekre és a céltárolóhelyekről?
* Megőrzi a másolási eszköz a forrás/cél hely által támogatott szükséges fájlhűséget? Bizonyos esetekben a céltároló nem támogatja ugyanazt a hűséget, mint a forrás. Már elhozta azt a döntést, hogy a céltároló elegendő az igényeinek megfelelően, ezért a másolási eszköznek csak a célfájl hűségképességeinek kell egyeznie.
* Rendelkezik a másolási eszköz olyan funkciókkal, amelyek illeszkednek az áttelepítési stratégiámba? 
    * Fontolja meg például, hogy rendelkezik-e olyan beállításokkal, amelyek lehetővé teszik az állásidő minimalizálását. Egy jó kérdés, hogy kérje a következő: Tudom futtatni ezt a példányt többször ugyanazon, a felhasználók aktívan elérhető helyen? Ha igen, jelentősen csökkentheti az állásidő mennyiségét. Hasonlítsa össze egy olyan helyzettel, amikor a másolatot csak akkor lehet elindítani, ha a forrás nem változik, hogy garantálja a teljes másolatot.

Az alábbi táblázat a Microsoft-eszközöket és azok azure-fájlmegosztásokra való jelenlegi alkalmasságát sorolja be:

| Ajánlott | Eszköz | Támogatja az Azure-fájlmegosztásokat | Megőrzi a fájl hűségét |
| :-: | :-- | :---- | :---- |
|![Igen, ajánlott.](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Támogatott. Az Azure-fájlmegosztások hálózati meghajtóként csatlakoztathatók. | Teljes hűség* |
|![Igen, ajánlott.](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Natívmódon integrálva az Azure fájlmegosztásokba. | Teljes hűség* |
|![Igen, ajánlott.](media/storage-files-migration-overview/circle-green-checkmark.png)| Tárolóáttelepítési szolgáltatás (SMS) | Közvetve támogatott. Az Azure-fájlmegosztások csatlakoztathatók hálózati meghajtóként egy SMS-célkiszolgálón. | Teljes hűség* |
|![Nem teljesen ajánlott.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure DataBox | Támogatott. | Nem másolja a metaadatokat. [Használható az Azure File Sync szolgáltatással együtt.](storage-sync-offline-data-transfer.md) |
|![Nem ajánlott.](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Támogatott. | Nem másolja a metaadatokat. |
|![Nem ajánlott.](media/storage-files-migration-overview/circle-red-x.png)| Azure Storage Explorer | Támogatott. | Nem másolja a metaadatokat. |
|![Nem ajánlott.](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Támogatott. | Nem másolja a metaadatokat. |
|||||

*\*Teljes hűség: megfelel vagy meghaladja az Azure fájlmegosztási képességeit.*

### <a name="migration-helper-tools"></a>Áttelepítési segítő eszközök

Ez a szakasz az áttelepítések megtervezését és végrehajtását segítő eszközöket sorolja fel.

* **RoboCopy, a Microsoft Corporation-től**

    Az egyik legalkalmazhatóbb másolási eszköz a fájláttelepítéshez, a Microsoft Windows része. Mivel a sok lehetőség ebben az eszközben, a fő [RoboCopy dokumentáció](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) hasznos forrás.

* **TreeSize, a JAM Software GmbH-tól**

    Az Azure File Sync elsősorban az elemek (fájlok és mappák) számával, és kevésbé a teljes TiB-összeggel méretezhető. Az eszköz segítségével meghatározhatja a Windows Server-köteteken lévő fájlok és mappák számát. Továbbá használható egy perspektíva létrehozása előtt az [Azure File Sync központi telepítése](storage-sync-files-deployment-guide.md) előtt - de azt követően is, ha a felhőrétegezés van elfoglalva, és szeretné látni nem csak az elemek száma, hanem amelyben a könyvtárakat a kiszolgáló gyorsítótára a leggyakrabban használják.
    Ez az eszköz (tesztelt 4.4.1-es verzió) kompatibilis a felhőrétegzett fájlokkal. Normál működése során nem fogja visszahívni a rétegzett fájlokat.


## <a name="next-steps"></a>További lépések

1. Hozzon létre egy tervet, amely az Azure-fájlmegosztások (csak felhőalapú vagy hibrid) üzembe helyezéséhez törekszik.
2. Tekintse át az elérhető áttelepítési útmutatók listáját, és keresse meg az Azure-fájlmegosztások forrásának és üzembe helyezésének megfelelő részletes útmutatót.

A cikkben említett Azure Files-technológiákról további információ áll rendelkezésre:

* [Azure fájlmegosztás – áttekintés](storage-files-introduction.md)
* [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
* [Azure File Sync: Felhőrétegezés](storage-sync-cloud-tiering.md)
