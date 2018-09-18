---
title: Telepítse át az adatokat a StorSimple 5000 – 7000 sorozatú Azure File Sync |} A Microsoft Docs
description: Ismerteti, hogyan telepítheti át adatait a StorSimple 5000/7000 sorozat az Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: aad5b2d2e451f33a3f4f365fa0702eef5d698e4e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729163"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Az Azure File Sync át adatokat a StorSimple 5000-7000-es sorozat

> [!IMPORTANT]
> A 2019. július 31-ig. a StorSimple 5000/7000 sorozat megszűnik a támogatás (EOS) állapotát. Azt javasoljuk, hogy a StorSimple 5000/7000 sorozat ügyfelek áttelepítése a a dokumentumban ismertetett alternatív megoldások egyikét.

Adatok áttelepítése az a folyamat, amely adatokat helyez át egy tárolási helye a másikra. Ez magában foglalja, így a szervezet aktuális adatokat egy eszközről egy másik eszközre pontos másolatát, lehetőleg nélkül megszakítása vagy aktív alkalmazások letiltása – és az összes bemeneti/kimeneti (I/O) tevékenység majd átirányítása az új eszköz. 

StorSimple 5000 és 7000 sorozatú eszközöket fog véget ért a szolgáltatás a július 2019. Ez azt jelenti, hogy a Microsoft már nem lesz képes támogatni a hardver- és StorSimple 5000/7000 sorozat július 2019 után. Ezeket az eszközöket használó ügyfeleink végezzen áttelepítést a StorSimple data más hibrid tárolási megoldások az Azure-ban. Ez a cikk ismerteti az adatok StorSimple 5000/7000 sorozatú eszközről az Azure File Sync (AFS).

## <a name="intended-audience"></a>Célközönség

Ez a cikk informatikai (IT) szakemberek és információszervező alkalmazottak a üzembe helyezése és kezelése a StorSimple 5000/7000 sorozat eszközök a felhőkörnyezetekből felelős szól. Az ügyfelek a StorSimple-eszközeik használatával (a Windows Server) fájl kiszolgálói munkaterhelések esetében ez az áttelepítési elérési tapasztalhatja különösen viszont. Ha úgy véli, hogy a szervezet számára jól működnek a funkciók az Azure File Sync, majd ez a cikk segít megérteni, hogyan helyezheti át a StorSimple-ből ezen megoldások.

## <a name="migration-considerations"></a>Az áttelepítés szempontjai

Erről a folyamatról ügyfelek esetében, akik egy Windows fájlmegosztást a Storage használata a StorSimple-kötet konfigurálása. A StorSimple 5000/7000 migrál adatokat az Azure File Sync magában foglalja a konvertálás a fájlmegosztási helyet egy [kiszolgálói végpont](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) és helyileg használatával csatlakoztatott meghajtók az új helye lesz majd egy másik végpontként. 

AFS való áthelyezésekor a következő szempontokat kell figyelembe venni:

1. Az Azure Files jelenleg rendelkezik egy 5 TB, illetve megosztási korlátozása. Ez a korlátozás az adatok több Azure-fájlmegosztások elosztva az Azure File Sync használatával oldható meg. További információkért tekintse át a [adatmintát növekedés az Azure Files üzembe helyezésének](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Az áttelepítés a teljes elsődleges adatkészlet a helyi eszközön letölti az adatok másolását a helyi eszközről történik. Győződjön meg arról, hogy rendelkezik-e elegendő sávszélesség, az átvitel befogadásához.
3. Ez a folyamat nem őrzi meg a már létrehozott pillanatképeket. Csak az elsődleges adatok telepíti át. A folyamat emellett nem őrzi meg a kapcsolódó sávszélesség-sablonokat és a biztonsági mentési szabályzatok. [Azure Backup használata](https://docs.microsoft.com/azure/backup/backup-azure-files) állíthatja be a biztonsági mentési szabályzatok után az adatok áttelepítése az Azure-fájl megosztása.
4. A StorSimple első hardverek biztosít. Azonban az Azure-fájlok vagy az Azure File Sync, használja a saját helyi Windows Server hardveres, a helyi gyorsítótárat. Biztosítania kell, hogy a storage kapacitása elegendő, hogy az adatkészlet, a kiválasztott helyi. A rétegezést és a beállítás szükséges szabad terület cél további információért tekintse át, hogyan [kiszolgálói végpont létrehozása az Azure File Sync üzembe helyezésekor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Tekintse át a [díjszabása az Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) módon változik, a StorSimple-ből. AFS nem rendelkezik a deduplikáció és a StorSimple mint tömörítés.

## <a name="migration-prerequisites"></a>Áttelepítési Előfeltételek

Itt található az Azure File Sync az örökölt 5000 és 7000 sorozatú eszköz áttelepítési előfeltételek. Mielőtt elkezdené, győződjön meg arról, hogy:

- Hozzáférés a StorSimple 5000/7000 sorozat eszköz futó szoftver verziója v2.1.1.518 vagy újabb. Korábbi verziók nem támogatottak. A jobb felső sarokban a webes felhasználói Felületét a StorSimple-eszköz, amelyen fut a szoftververzió kell megjelenítenie.  
    Ha az eszköz nem fut a v2.1.1.518, frissítsen a rendszer szükséges minimális verziója. Részletes útmutatásért tekintse meg [a rendszer frissítése a v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Ellenőrizze, hogy bármely eszköz futó aktív biztonsági mentési feladatok. Ez magában foglalja a StorSimple Data Protection konzol gazdagépen a feladatokat. Várjon, amíg a jelenlegi feladatok végrehajtásához. 
- A StorSimple 5000-7000-es sorozatú eszköz csatlakozik egy Windows Server-állomás a hozzáférést. A gazdagép futnia kell egy támogatott Windows Server-verzió leírtak szerint [Azure File Sync együttműködési](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- StorSimple-kötetek csatlakoztatva vannak a gazdagépen, és fájlmegosztásokat tartalmaznak.
- A gazdagép rendelkezik a megfelelő helyi tároló a helyi gyorsítótárban tárolt adatok tárolásához.
- Tulajdonos szintű hozzáférés az Azure-előfizetést, amely segítségével az Azure File Sync üzembe helyezése. Problémákat tapasztalhat a felhőbeli végpont a szinkronizálási csoport létrehozásakor, ha nem rendelkezik tulajdonosi vagy a rendszergazdai szintű engedélyekkel.
- A hozzáférést egy [általános célú v2-tárfiók](https://docs.microsoft.com/azure/storage/common/storage-account-overview) egy szinkronizálni kívánt Azure-fájlmegosztást. További információkért lásd: [hozzon létre egy tárfiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
 - Hogyan [hozzon létre egy Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share#create-file-share-through-the-azure-portal).

## <a name="migration-process"></a>Áttelepítési folyamat

Adatok áttelepítése a storsimple 5000-7000-es, AFS lépésből áll:
1.  Replikálja az adatokat a helyszíni fájlkiszolgálóról, ha csatlakoztatva vannak a StorSimple-köteteket egy Azure-fájlok megosztása.  Replikálás telepítése AFS ügynök keresztül történik.
2.  Szakítsa meg a StorSimple-eszköz. A helyi lemezeket, majd bérlőként eljárva a helyi gyorsítótárat.

### <a name="migration-steps"></a>Migrálási lépések

A következő lépésekkel migrálása az Azure File Sync megosztások a StorSimple-kötetek konfigurálni a Windows fájlmegosztás. 
1.  Hajtsa végre ezeket a lépéseket ugyanazon a gazdagépen a Windows Server, a StorSimple-kötetek csatlakoztatva vannak, vagy használjon egy másik rendszer. 
    - [Készítse elő a Windows Server használata az Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Az Azure File Sync ügynök telepítése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [A Storage Sync service telepítése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Regisztrálja a Windows Server Storage Sync service](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Szinkronizálási csoport és a felhőbeli végpont létrehozása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Szinkronizálási csoportok kell arról, hogy minden Windows fájlmegosztás, amely a gazdagépről kell áttelepíteni.
    - [Kiszolgálói végpont létrehozása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). A StorSimple-kötet, amely tartalmazza a fájladatok megosztás elérési útját adja meg az elérési utat. Például, ha a StorSimple-kötet meghajtó `J`, és az adatok blobtárolóban találhatók `J:/<myafsshare>`, majd adja hozzá ezt az elérési utat, a kiszolgálói végpont. Hagyja a **Rétegezést** , **letiltott**.
2.  Várjon, amíg a file server szinkronizálás. Egy adott szinkronizálási csoport minden egyes kiszolgáló esetén ellenőrizze, hogy:
    - A feltöltési és letöltési utolsó kísérlet történt szinkronizálás időbélyegei legutóbbi.
    - Állapota zöld, a fel- és letöltést.
    - A **szinkronizálási tevékenység** látható nagyon kevés vagy a hátralévő szinkronizálási fájlokat.
    - A **nem szinkronizálja a fájlokat** feltöltési és letöltési 0.
    Ha a kiszolgáló szinkronizálása befejeződött-e további információért látogasson el [hibaelhárítása az Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). A szinkronizálás több óráig is eltarthat, nap, az adatok mérete és a sávszélesség. A szinkronizálás befejeződése után minden adatát van biztonságosan Azure-fájlmegosztást. 
3.  Nyissa meg a StorSimple-köteteket a megosztásokat. Válassza ki a megosztást, kattintson a jobb gombbal, és válassza **tulajdonságok**. Vegye figyelembe a megosztási engedélyek alapján **biztonsági**. Ezeket az engedélyeket kell manuálisan kell alkalmazni az új megosztásra a későbbi lépésben.
4.  Ha használja a Windows Server ugyanazon a gazdagépen vagy egy másik eltérő lesz a következő lépéseket.

    Kihagyhatja ezt a lépést, és folytassa a következő lépés egy másik Windows Server-állomás használata. Az azonos Windows-fájlkiszolgáló AFS használ, ha néhány perc alatt az állásidő most fog tapasztalni. 
    - **A leállás akkor kezdődik** –, amelyet a kiszolgálói végpont törlése *. lépés: 1F*. 
    - Hozzon létre egy új kiszolgálói végpontok az elérési utat, ahol azt szeretné, hogy az adatok tárolási bevezetése előre.
    - Miután a kiszolgálói végpont állapota kifogástalan (Ez eltarthat néhány percig), látni fogja az adatokat az új helyen. Most konfigurálhatja a Windows Server-gazdagép, az új helyen levő fájlok kiszolgálása érdekében. - **Állásidő vége**.
5.  Ha egy másik Windows-fájlkiszolgáló használ az Azure File Sync, ezután nem fog tapasztalni okoz állásidőt. 
    - Adjon hozzá egy másik kiszolgálói végpont elérési útját a helyi tároló, azaz használata helyett a StorSimple-eszköz gyorsítótárként felkészült. 
    - Néhány perc alatt az új kiszolgálón lévő fájlok látni fogja. Szabadon győződjön meg arról, a Váltás az új helyet a gazdagépen a StorSimple-eszközről, bármikor.

    > [!TIP] 
    > Fontolja meg, az egyetlen informatikai cseréje, hogy ugyanazt a nevet és ugyanazt az utat az Új fájlmegosztás konfigurálása. Ha használja a DFS-N, ez előfordulhat, hogy módosítsa a beállításokat.
6.  Konfigurálja újra a megosztási engedélyeket, amint a *3. lépés*.

Ha problémákat tapasztal az adatáttelepítés során, [forduljon a Microsoft támogatási](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>További lépések

Ha nem a megfelelő megoldás az, AFS, megtudhatja, hogyan [telepítse át az adatokat a storsimple 5000-7000-es sorozat a 8000-es sorozatú eszköz](storsimple-8000-migrate-from-5000-7000.md).

