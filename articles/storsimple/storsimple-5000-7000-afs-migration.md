---
title: Adatok áttelepítése a StorSimple 5000-7000 sorozatból az Azure File Sync-be| Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként telepíthetők át az adatok a StorSimple 5000/7000 sorozatból az Azure File Sync (AFS) szolgáltatásba.
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
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65150739"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Adatok áttelepítése a StorSimple 5000-7000 sorozatból az Azure File Sync szolgáltatásba

> [!IMPORTANT]
> 2019. július 9-én a StorSimple 5000/7000 sorozat a támogatási (EOS) státusz megszűnése felé tart. Azt javasoljuk, hogy a StorSimple 5000/7000 sorozatú ügyfelek a dokumentumban ismertetett alternatívák egyikére térjenek át.

Az adatáttelepítés az adatok egyik tárolóhelyről a másikra történő áthelyezésének folyamata. Ez azt jelenti, hogy a szervezet aktuális adatait pontosan le kell másolni az egyik eszközről egy másik eszközre – lehetőleg az aktív alkalmazások megszakítása vagy letiltása nélkül –, majd át kell irányítani az összes bemeneti/kimeneti (I/O) tevékenységet az új eszközre. 

A StorSimple 5000 és 7000 sorozatú tárolóeszközök 2019 júliusában érik el a szolgáltatás végét. Ez azt jelenti, hogy a Microsoft 2019 júliusa után már nem fogja tudni támogatni a StorSimple 5000/7000 sorozat hardverét és szoftverét. Az ilyen eszközöket használó ügyfeleknek át kell telepíteniük StorSimple-adataikat más hibrid tárolási megoldásokra az Azure-ban. Ez a cikk a StorSimple 5000/7000 sorozatú eszközökről az Azure File Sync (AFS) szolgáltatásba történő áttelepítését ismerteti.

## <a name="intended-audience"></a>Célközönség

Ez a cikk a StorSimple 5000/7000 sorozatú eszközök adatközpontban történő üzembe helyezéséért és kezeléséért felelős informatikai szakemberek és tudásmunkások számára készült. A StorSimple-eszközeiket fájlkiszolgálói munkaterheléshez használó ügyfelek (Windows Server rendszerrel) különösen vonzónak találhatják ezt az áttelepítési útvonalat. Ha úgy gondolja, hogy az Azure File Sync funkciói jól működnek a szervezet számára, akkor ez a cikk segít megérteni, hogyan helyezheti át ezeket a megoldásokat a StorSimple-ből.

## <a name="migration-considerations"></a>Migrálási szempontok

Ez a folyamat azoknak az ügyfeleknek működik, akik StorSimple-kötethasználatával konfiguráltak Windows-fájlmegosztást a tároláshoz. Adatok áttelepítése a StorSimple 5000/7000-ről az Azure File Sync-be azt jelenti, hogy a fájlmegosztáshelyét [kiszolgálói végpontra konvertálja,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) majd a helyileg csatlakoztatott meghajtókat használja egy másik végpontként, amely ezután az új hely lesz. 

Az AFS-re való áttérés során a következő pontokat kell figyelembe venni:

1. Az Azure Files jelenleg 5 TB/megosztási korlátozással rendelkezik. Ez a korlátozás az Azure File Sync használatával több Azure-fájlmegosztáson keresztül elosztva érhető el. További információkért tekintse át az [Azure Files üzembe helyezésének adatnövekedési mintáját.](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
2. Ez az áttelepítés letölti a teljes elsődleges adatkészletet egy helyszíni eszközre, ahogy az adatmásolás a helyszíni eszközről történik. Győződjön meg arról, hogy elegendő sávszélességgel rendelkezik az átvitel hez.
3. Ez a folyamat nem őrzi meg a már létrehozott pillanatképeket. Csak az elsődleges adatokat telepíti át. A folyamat nem őrzi meg a kapcsolódó sávszélesség-sablonokat vagy biztonsági mentési házirendeket sem. [Az Azure Backup segítségével](https://docs.microsoft.com/azure/backup/backup-azure-files) állítsa be a biztonsági mentési szabályzatok után az adatok áttelepítése az Azure-fájlmegosztáson.
4. A StorSimple első féltől származó hardvert biztosít. Az Azure Files/Azure File Sync szolgáltatással azonban a saját helyi Windows Server-hardvert használja helyi gyorsítótárként. Győződjön meg arról, hogy elegendő tárolókapacitással rendelkezik ahhoz, hogy a választott adatkészlet helyi szinten maradjon. A rétegezésről és a szükséges szabad terület céljának beállításáról az Azure File Sync telepítésekor tekintse [át a kiszolgálóvégpont létrehozásának](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal)módját. 
5. Tekintse át az [Azure File Sync díjszabását,](https://azure.microsoft.com/pricing/details/storage/files/) mivel az a StorSimple-től függően változik. Az AFS nem rendelkezik deduplikációval és tömörítéssel, mint a StorSimple.

## <a name="migration-prerequisites"></a>Áttelepítési előfeltételek

Itt megtalálja a régi 5000-es vagy 7000-es sorozatú eszköz áttelepítési előfeltételeit az Azure File Sync szolgáltatásba. Mielőtt elkezdené, győződjön meg róla, hogy:

- Hozzáférés a StorSimple 5000/7000 sorozatú eszközhöz, amely a 2.1.1.518-as vagy újabb verziót futtatja. A korábbi verziók nem támogatottak. A StorSimple-eszköz webes felhasználói felületének jobb felső sarkában meg kell jelenítenie a futó szoftververziót.  
    Ha a készüléken nem fut a 2.1.1.518-as verzió, frissítse a rendszert a szükséges minimális verzióra. A részletes útmutatásért olvassa el [A rendszer frissítése a 2.1.1.518-as vagy 2.1.518-as fájlra](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)című információt.
- Ellenőrizze, hogy vannak-e aktív biztonsági mentési feladatok, amelyek a forráseszközön futnak. Ez magában foglalja a storsimple adatvédelmi konzol gazdagép en a feladatokat. Várja meg, amíg az aktuális feladatok befejeződnek. 
- Hozzáférés a StorSimple 5000-7000 sorozatú eszközhöz csatlakoztatott Windows Server-állomáshoz. Az állomásnak az Azure File Sync együttműködési verziójában leírtak szerint támogatott Windows Server-verziót kell [futtatnia.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)
- A StorSimple kötetek csatlakoztatva vannak az állomásra, és fájlmegosztásokat tartalmaznak.
- Az állomás elegendő helyi tárolóval rendelkezik a helyileg gyorsítótárazott adatok tárolásához.
- Tulajdonosszintű hozzáférés az Azure-előfizetéshez, amelyet az Azure File Sync üzembe helyezéséhez fog használni. Problémák léphetnek fel, amikor felhővégpontot hoz létre a szinkronizálási csoport számára, ha nem rendelkezik tulajdonosi vagy rendszergazdai szintű engedélyekkel.
- Hozzáférés egy [általános célú v2-es tárfiókhoz](https://docs.microsoft.com/azure/storage/common/storage-account-overview) egy Azure-fájlmegosztással, amelyhez szinkronizálni szeretne. További információ: [Tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - [Azure-fájlmegosztás létrehozása.](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)

## <a name="migration-process"></a>Migrálási folyamat

Az adatok áttelepítése a StorSimple 5000-7000 rendszerről az AFS szolgáltatásba két lépésből áll:
1.  Replikálja az adatokat a helyszíni fájlkiszolgálóról, ahol a StorSimple kötetek vannak csatlakoztatva egy Azure Files megosztásra.  A replikáció a telepített AFS-ügynökön keresztül történik.
2.  Válassza le a StorSimple eszközt. A helyi lemezek ezután helyi gyorsítótárként működnek.

### <a name="migration-steps"></a>A migrálás lépései

Hajtsa végre a következő lépéseket a StorSimple-köteteken konfigurált Windows-fájlmegosztás azure-fájlszinkronizálási megosztásra való áttelepítéséhez. 
1.  Hajtsa végre ezeket a lépéseket ugyanazon a Windows Server-állomáson, ahol a StorSimple kötetek csatlakoztatva vannak, vagy használjon egy másik rendszert. 
    - [Készítse elő a Windows Server t az Azure File Sync szolgáltatással való használatra.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync)
    - [Telepítse az Azure File Sync ügynököt.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent)
    - [Telepítse a Storage Sync szolgáltatást.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service) 
    - [Regisztrálja a Windows Server t tárolási szinkronizálási szolgáltatással.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service) 
    - [Hozzon létre egy szinkronizálási csoportot és egy felhővégpontot.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint) Szinkronizálási csoportokat kell kidolgozni minden olyan Windows-fájlmegosztáshoz, amelyet át kell telepíteni az állomásról.
    - [Kiszolgálóvégpont létrehozása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Adja meg az elérési utat a fájlmegosztási adatokat tartalmazó StorSimple kötet elérési útjaként. Ha például a StorSimple `J`kötet meghajtó, és `J:/<myafsshare>`az adatok a rendszerben találhatók, akkor adja hozzá ezt az elérési utat kiszolgálóvégpontként. Hagyja a **rétegezést** **letiltva.**
2.  Várjon, amíg a fájlkiszolgáló szinkronizálása befejeződik. Egy adott szinkronizálási csoport minden kiszolgálója esetében győződjön meg akövetkezőkről:
    - Az utoljára megkísérelt szinkronizálás időbélyegei mind a feltöltés, mind a letöltés esetében frissek.
    - Az állapot zöld mind a feltöltés, mind a letöltés hez.
    - A **Szinkronizálási tevékenység** nagyon kevés vagy egyáltalán nem tartalmaz szinkronizálandó fájlt.
    - A **fájlok nem szinkronizálja** a 0 mind a feltöltési és letöltési.
    Ha többet szeretne tudni arról, hogy mikor fejeződött be a kiszolgáló szinkronizálása, látogasson el az [Azure File Sync hibaelhárítása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other)című témakörbe. A szinkronizálás az adatok méretétől és sávszélességétől függően több órát vagy napot is igénybe vehet. Miután a szinkronizálás befejeződött, az összes adat biztonságosan az Azure-fájlmegosztás. 
3.  Nyissa meg a StorSimple kötetek megosztásait. Jelöljön ki egy megosztást, kattintson a jobb gombbal, és válassza **a Tulajdonságok parancsot.** Jegyezze fel a megosztási engedélyeket a **Biztonság csoportban.** Ezeket az engedélyeket manuálisan kell alkalmazni az új megosztásra a későbbi lépésben.
4.  Attól függően, hogy ugyanazt a Windows Server-állomást vagy egy másik atahost használja- e, a következő lépések eltérőek lesznek.

    Hagyja ki ezt a lépést, és folytassa a következő lépéssel, ha másik Windows Server-állomást használ. Ha ugyanazt a Windows File Server for AFS-t használja, akkor néhány perc leállást fog tapasztalni. 
    - **Állásidő indítása** - Törölje az *1F lépésben*létrehozott kiszolgálóvégpontot. 
    - Hozzon létre egy új kiszolgálóvégpontot azzal az elérési úttal, ahol az adatokat a jövőben is el szeretné végezni.
    - Miután a kiszolgáló végpontja kifogástalan állapotúként jelenik meg (ez néhány percet is igénybe vehet), az adatok az új helyen jelennek meg. Most már beállíthatja, hogy a Windows Server állomás az új helyről származó fájlokat szolgáljon ki. - **Az állásidő véget ér.**
5.  Ha egy másik Windows File Server for Azure File Sync, akkor nem fog tapasztalni semmilyen leállás. 
    - Adjon hozzá egy másik kiszolgálóvégpontot a helyi tároló elérési útját, amelyet a StorSimple-eszköz helyett gyorsítótárként szeretne használni. 
    - Néhány perc alatt láthatja a fájlokat az új kiszolgálón. Bármikor átválthat a StorSimple eszközről erre az új helyre a gazdagépen.

    > [!TIP] 
    > Fontolja meg az új fájlmegosztás konfigurálását ugyanazzal a névvel és elérési úttal, mint amelyet helyettesít, hogy minimalizálja a fennakadást. DfS-N használata esetén előfordulhat, hogy módosítania kell a konfigurációt.
6.  A *3.*

Ha bármilyen problémát tapasztal az adatáttelepítés során, forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) 



## <a name="next-steps"></a>További lépések

Ha az AFS nem a megfelelő megoldás az Ön számára, olvassa el, hogyan telepítheti át az [adatokat egy StorSimple 5000-7000 sorozatból egy 8000-es sorozatú eszközre.](storsimple-8000-migrate-from-5000-7000.md)

