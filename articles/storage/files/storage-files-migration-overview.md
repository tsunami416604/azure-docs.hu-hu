---
title: Migrálás Azure-fájlmegosztásokba
description: Ismerje meg az Azure-fájlmegosztásokba való áttelepítést, és keresse meg az áttelepítési útmutatót.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685991"
---
# <a name="migrate-to-azure-file-shares"></a>Migrálás Azure-fájlmegosztásokba

Ez a cikk az Azure-fájlmegosztásokba való migrálás alapvető szempontjait ismerteti.

Ez a cikk az áttelepítés alapjait és az áttelepítési útmutatók táblázatát tartalmazza. Ezek az útmutatók segítenek a fájlok Azure-fájlmegosztásokba való áthelyezésében. A segédvonalak az adatok helye és a (csak felhőalapú vagy hibrid) telepítési modellje alapján vannak rendszerezve.

## <a name="migration-basics"></a>Áttelepítési tudnivalók

Az Azure többféle felhőalapú tárhellyel rendelkezik. Az Azure-ba történő fájláttelepítés ek alapvető szempontja annak meghatározása, hogy melyik Azure-tárolási lehetőség megfelelő az adatokhoz.

[Az Azure-fájlmegosztások](storage-files-introduction.md) általános célú fájladatokhoz alkalmasak. Ezek az adatok minden olyan adatot tartalmaznak, amelyhez helyszíni SMB- vagy NFS-megosztást használ. Az [Azure File Sync](storage-sync-files-planning.md)segítségével gyorsítótáraszthatja több Azure-fájlmegosztás tartalmát a helyszíni Windows Server rendszert futtató kiszolgálókon.

Egy alkalmazás, amely jelenleg fut egy helyszíni kiszolgálón, fájlok tárolása egy Azure-fájlmegosztásban lehet egy jó választás. Áthelyezheti az alkalmazást az Azure-ba, és az Azure-fájlmegosztásokat megosztott tárterületként használhatja. Ebben a forgatókönyvben az Azure Disks is figyelembe [veheti.](../../virtual-machines/windows/managed-disks-overview.md)

Egyes felhőalapú alkalmazások nem függnek az SMB-től, a számítógép-helyi adathozzáféréstől vagy a megosztott hozzáféréstől. Az ilyen alkalmazások esetében az objektumtárolás, például az [Azure-blobok](../blobs/storage-blobs-overview.md) gyakran a legjobb választás.

A kulcs minden áttelepítés, hogy rögzítse az összes megfelelő fájlhűség, amikor a fájlokáthelyezése a jelenlegi tárolási hely az Azure-ba. Hogy az Azure storage-beállítás mennyi hűséget támogat, és mennyi re van szüksége a forgatókönyvnek, az azure-tárhely kiválasztásában is segítséget nyújt. Az általános célú fájladatok hagyományosan a fájl metaadataitól függnek. Előfordulhat, hogy az alkalmazásadatok nem.

A fájl két alapvető összetevője:

- **Adatfolyam**: A fájl adatfolyama tárolja a fájl tartalmát.
- **Fájl metaadatai**: A fájl metaadatai a következő alösszetevőket tartalmazják:
   * Fájlattribútumok, például írásvédettek
   * File permissions, which can be referred to as *NTFS permissions* or *file and folder ACLs*
   * Időbélyegek, különösen a létrehozása és az utolsó módosított időbélyegek
   * Alternatív adatfolyam, amely nagyobb mennyiségű nem szabványos tulajdonság tárolására szolgál

Az áttelepítésben a fájlhűség a következőképpen határozható meg:

- Tárolja az összes vonatkozó fájlinformációt a forráson.
- Fájlok átvitele az áttelepítési eszközzel.
- Tárolja a fájlokat az áttelepítés céltárolójában.

Az áttelepítés zökkenőmentes működésének biztosítása érdekében azonosítsa [az igényeinek leginkább megfelelő másolási eszközt,](#migration-toolbox) és párosítsa a tárolási célt a forráshoz.

Figyelembe véve az előző adatokat, láthatja, hogy az Azure-ban az általános célú fájlok céltárolója [az Azure-fájlmegosztások.](storage-files-introduction.md)

Az Azure-blobokban lévő objektumtárolással ellentétben az Azure-fájlmegosztásnkívül tárolhatja a fájl metaadatait. Az Azure-fájlmegosztások megőrzik a fájl- és mappahierarchiát, az attribútumokat és az engedélyeket is. Az NTFS-engedélyek fájlokon és mappákon tárolhatók, mert azok a helyszínen találhatók.

Az Active Directory egyik felhasználója, amely a helyszíni tartományvezérlőjük, natív módon hozzáférhet egy Azure-fájlmegosztáshoz. Így lehet az Azure Active Directory tartományi szolgáltatások (Azure AD DS) felhasználója is. Each uses their current identity to get access based on share permissions and on file and folder ACLs. Ez a viselkedés hasonló a helyszíni fájlmegosztáshoz csatlakozó felhasználóhoz.

Az alternatív adatfolyam a fájlhűség elsődleges aspektusa, amely jelenleg nem tárolható egy Azure-fájlmegosztásban lévő fájlon. Az Azure File Sync használat közben a helyszíni állapotban marad meg.

További információ az [Azure AD-hitelesítésről](storage-files-identity-auth-active-directory-enable.md) és az [Azure AD DS-hitelesítésről](storage-files-identity-auth-active-directory-domain-service-enable.md) az Azure-fájlmegosztásokhoz.

## <a name="migration-guides"></a>Migrálási útmutatók

Az alábbi táblázat részletes áttelepítési útmutatókat sorol fel.

A táblázat használata:

1. Keresse meg annak a forrásrendszernek a sorát, amelyen a fájlok jelenleg vannak tárolva.

1. Válasszon az alábbi célok közül:

   - Hibrid telepítés az Azure File Sync használatával az Azure-fájlmegosztások tartalmának helyszíni gyorsítótárazására
   - Azure-fájlmegosztások a felhőben

   Válassza ki a kívánt céloszlopot.

1. A forrás és a cél metszéspontjában a táblázatcella felsorolja az elérhető áttelepítési eseteket. Válassza ki azt, hogy közvetlenül csatolja a részletes áttelepítési útmutatóhoz.

A hivatkozás nélküli forgatókönyv még nem rendelkezik közzétett áttelepítési útmutatóval. Időnként ellenőrizze ezt a táblázatot a frissítésekért. Az új útmutatók akkor jelennek meg, amikor elérhetővé válikk.

| Forrás | Cél: </br>Hibrid telepítés | Cél: </br>Csak felhőalapú telepítés |
|:---|:--|:--|
| | Szerszámkombináció:| Szerszámkombináció: |
| Windows Server 2012 R2 és újabb | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure Fájlszinkronizálás és Azure-adatmező](storage-sync-offline-data-transfer.md)</li><li>Azure fájlszinkronizálási és tárolási áttelepítési szolgáltatás</li></ul> | <ul><li>Azure File Sync</li><li>Azure fájlszinkronizálás és adatmező</li><li>Azure fájlszinkronizálási és tárolási áttelepítési szolgáltatás</li><li>Robocopy</li></ul> |
| Windows Server 2012 és korábbi | <ul><li>Azure fájlszinkronizálás és adatmező</li><li>Azure fájlszinkronizálási és tárolási áttelepítési szolgáltatás</li></ul> | <ul><li>Azure fájlszinkronizálási és tárolási áttelepítési szolgáltatás</li><li>Robocopy</li></ul> |
| Hálózathoz csatlakoztatott tároló (NAS) | <ul><li>[Azure fájlszinkronizálás és RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux vagy Samba | <ul><li>[Azure fájlszinkronizálás és RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 vagy StorSimple Cloud Appliance 8600 | <ul><li>[Azure File Sync és StorSimple cloud appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple felhőberendezés 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Áttelepítési eszköztár

### <a name="file-copy-tools"></a>Fájlmásolási eszközök

A Microsoft és mások számos fájlmásolási eszközt kínálnak. Az áttelepítési forgatókönyvhöz megfelelő eszköz kiválasztásához figyelembe kell vennie az alábbi alapvető kérdéseket:

* Támogatja az eszköz a fájlmásolás forrás- és célhelyét?

* Támogatja az eszköz a hálózati elérési utat vagy az elérhető protokollokat (például REST, SMB vagy NFS) a forrás- és a céltárolási helyek között?

* Megőrzi az eszköz a forrás- és célhelyek által támogatott szükséges fájlhűséget?

    Bizonyos esetekben a céltároló nem támogatja ugyanazt a hűséget, mint a forrás. Ha a céltároló elegendő az igényeinek megfelelően, az eszköznek csak a cél fájlhűségi képességeinek kell egyeznie.

* Rendelkezik az eszköz olyan funkciókkal, amelyek lehetővé teszik, hogy beleférjen az áttelepítési stratégiába?

    Fontolja meg például, hogy az eszköz lehetővé teszi-e az állásidő minimalizálását.
    
    Ha egy eszköz támogatja a forrás tükrözésének lehetőségét a célhoz, gyakran többször is futtathatja ugyanazon a forráson és célon, miközben a forrás elérhető marad.

    Az eszköz első futtatásakor az adatok nagy részét másolja. Ez a kezdeti futtatás tarthat egy darabig. Gyakran tovább tart, mint amennyit az adatforrás üzleti folyamatokhoz való offline állapotba hoz.

    Ha egy forrást tükröz egy célhoz (mint a **robocopy /MIR),** az eszközt újra futtathatja ugyanazon a forráson és célon. A futtatás sokkal gyorsabb, mert csak az előző futtatás után végrehajtott forrásmódosításokat kell szállítania. A másolási eszköz ily módon való újrafuttatása jelentősen csökkentheti az állásidőt.

Az alábbi táblázat a Microsoft-eszközöket és azok azure-fájlmegosztásokra való jelenlegi alkalmasságát sorolja be:

| Ajánlott | Eszköz | Az Azure-fájlmegosztások támogatása | A fájlhűség megőrzése |
| :-: | :-- | :---- | :---- |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Támogatott. Az Azure-fájlmegosztások hálózati meghajtóként csatlakoztathatók. | Teljes hűséggel.* |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Natívmódon integrálva az Azure fájlmegosztásokba. | Teljes hűséggel.* |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Tárolóáttelepítési szolgáltatás | Közvetve támogatott. Az Azure-fájlmegosztások csatlakoztathatók hálózati meghajtóként az SMS-célkiszolgálókon. | Teljes hűséggel.* |
|![Nem teljesen ajánlott](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | Támogatott. | Nem másolja a metaadatokat. [A Data Box használható az Azure File Sync segítségével.](storage-sync-offline-data-transfer.md) |
|![Nem ajánlott](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Támogatott. | Nem másolja a metaadatokat. |
|![Nem ajánlott](media/storage-files-migration-overview/circle-red-x.png)| Azure Storage Explorer | Támogatott. | Nem másolja a metaadatokat. |
|![Nem ajánlott](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Támogatott. | Nem másolja a metaadatokat. |
|||||

*\*Teljes hűség: megfelel az Azure fájlmegosztási képességeinek, vagy meghaladja azt.*

### <a name="migration-helper-tools"></a>Áttelepítési segítő eszközök

Ez a szakasz az áttelepítések megtervezését és futtatását segítő eszközöket ismerteti.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy a Microsoft Corporation-től

RoboCopy az egyik eszköz leginkább alkalmazható fájl áttelepítések. Jön részeként a Windows. A fő [RoboCopy dokumentáció](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) hasznos forrás az eszköz számos lehetőség.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize a JAM Software GmbH-tól

Az Azure File Sync elsősorban az elemek (fájlok és mappák) számával, és nem a teljes tárterület mennyiségével méretezhető. A TreeSize eszköz lehetővé teszi a Windows Server-köteteken lévő elemek számának meghatározását.

Az eszköz segítségével az Azure File Sync központi telepítése előtt hozhat létre [perspektívát.](storage-sync-files-deployment-guide.md) Azt is használhatja, ha a felhőrétegezés üzembe helyezés után van elfoglalva. Ebben az esetben láthatja az elemek számát, és hogy mely könyvtárak használják a leggyakrabban a kiszolgáló gyorsítótárát.

Az eszköz tesztelt verziója a 4.4.1-es verzió. Kompatibilis a felhőalapú fájlokkal. Az eszköz normál működése során nem fogja visszahívni a rétegzett fájlokat.

## <a name="next-steps"></a>További lépések

1. Hozzon létre egy tervet, amelyhez az Azure-fájlmegosztások (csak felhőalapú vagy hibrid) üzembe helyezését szeretné.
1. Tekintse át az elérhető áttelepítési útmutatók listáját, és keresse meg az Azure-fájlmegosztások forrásának és üzembe helyezésének megfelelő részletes útmutatót.

Az alábbiakban a cikkben említett Azure Files technológiákról olvashat bővebben:

* [Azure fájlmegosztás – áttekintés](storage-files-introduction.md)
* [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
* [Azure File Sync: Felhőrétegezés](storage-sync-cloud-tiering.md)
