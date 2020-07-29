---
title: Adatok migrálása a StorSimple 5000-7000 sorozatból a Azure File Syncba | Microsoft Docs
description: Ismerteti, hogyan lehet adatok áttelepítését a StorSimple 5000/7000 sorozatból a Azure File Syncba (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 5d656fd8757580b8ce96acf168e92fc847d400ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85514065"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Adatok migrálása a StorSimple 5000-7000 sorozatból a Azure File Syncba

> [!IMPORTANT]
> 2019. július 9-én a StorSimple 5000/7000-sorozat eléri a támogatási (EOS) állapot végét. Javasoljuk, hogy a StorSimple 5000/7000 Series-ügyfelek a dokumentumban ismertetett alternatívák egyikére váltsanak át.

Az adatok áttelepítése az egyik tárolóhelyről a másikra történő áthelyezés folyamata. Ez azt eredményezi, hogy a szervezet aktuális adatainak pontos másolatát az egyik eszközről egy másik eszközre kell másolni – lehetőleg az aktív alkalmazások megszakítása vagy letiltása nélkül –, majd az összes bemeneti/kimeneti (I/O) tevékenység átirányítása az új eszközre. 

A StorSimple 5000 és az 7000 sorozatú tárolóeszközök a szolgáltatás végén lesznek elérhetők. július 2019. Ez azt jelenti, hogy a Microsoft már nem fogja tudni támogatni a StorSimple 5000/7000 sorozathoz tartozó hardvereket és szoftvereket július 2019. után. Az ezeket az eszközöket használó ügyfeleknek át kell telepíteniük a StorSimple-adataikat más, az Azure-beli hibrid tárolási megoldásokra. Ez a cikk az adatok áttelepítését ismerteti egy StorSimple 5000/7000 sorozatú eszközről Azure File Sync (AFS) rendszerre.

## <a name="intended-audience"></a>Célközönség

Ez a cikk olyan informatikai (IT) szakemberek és szakemberek számára készült, akik a StorSimple 5000/7000 Series-eszközök üzembe helyezéséhez és kezeléséhez felelősek a adatközpontot. A Fájlkiszolgálói számítási feladatokhoz (Windows Server) használó ügyfelek a StorSimple-eszközöket különösen vonzónak találják. Ha úgy véli, hogy a Azure File Sync szolgáltatásai jól működnek a szervezete számára, akkor ez a cikk segít megérteni, hogyan helyezhet át ezekre a megoldásokra a StorSimple.

## <a name="migration-considerations"></a>A migrálás szempontjai

Ez a folyamat azon ügyfelek esetében működik, akik a Windows fájlmegosztást egy StorSimple-kötet használatával konfigurálták a tároláshoz. Az adatok áttelepítése a StorSimple 5000/7000-ből Azure File Sync a fájlmegosztás helyének konvertálása egy [kiszolgálói végpontra](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) , majd helyileg csatlakoztatott meghajtók használata egy másik végpontként, amely ezután az új hely lesz. 

Az AFS-re való áttérés során a következő szempontokat kell figyelembe venni:

1. Azure Files jelenleg 5 TB/megosztás korlátozást tartalmaz. Ez a korlátozás a több Azure-fájlmegosztás között elosztott adatAzure File Sync használatával oldható fel. További információkért tekintse át az [adatmennyiség növekedési mintáját Azure Files központi telepítéshez](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Az áttelepítés során a rendszer letölti a teljes elsődleges adatkészletet egy helyszíni eszközre, mivel az adatok másolása a helyszíni eszközről történik. Gondoskodjon arról, hogy elegendő sávszélesség legyen az átvitelhez.
3. Ez a folyamat nem őrzi meg a már létrehozott pillanatképeket. Csak az elsődleges adatátvitelt telepíti át. A folyamat emellett nem őrzi meg a társított sávszélesség-sablonokat vagy a biztonsági mentési házirendeket. A [Azure Backup használatával](https://docs.microsoft.com/azure/backup/backup-azure-files) állíthatja be a biztonsági mentési házirendeket az Azure-fájlmegosztás során az adatáttelepítés után.
4. A StorSimple első féltől származó hardvert biztosít. Azure Files/Azure File Sync azonban a saját helyi Windows Server-hardverét használja helyi gyorsítótárként. Gondoskodnia kell arról, hogy elegendő tárolókapacitással rendelkezzen az Ön által választott helyi adathalmaz megőrzése érdekében. Ha további információt szeretne a rétegek és a szükséges szabad lemezterület beállításáról, tekintse át a [kiszolgálói végpont létrehozását a Azure file Sync telepítésekor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal)című témakört. 
5. Tekintse át a [Azure file Sync díjszabását](https://azure.microsoft.com/pricing/details/storage/files/) , mivel az a StorSimple-től eltér. Az AFS nem rendelkezik a deduplikálás és a tömörítés, például a StorSimple.

## <a name="migration-prerequisites"></a>Migrálás előfeltételei

Itt megtalálhatja az örökölt 5000-vagy 7000-es adatsorozat-eszköz áttelepítésének előfeltételeit Azure File Sync. Mielőtt elkezdené, győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Hozzáférés egy StorSimple 5000/7000 sorozatú eszközhöz, amely a v 2.1.1.518 vagy újabb verzióját futtatja. A korábbi verziók nem támogatottak. A StorSimple-eszköz webes FELÜLETének jobb felső sarkában meg kell jelennie a szoftvert futtató verziónak.  
    Ha az eszköz nem a v 2.1.1.518 fut, frissítse a rendszert a szükséges minimális verzióra. Részletes útmutatást a [rendszer frissítése a v 2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)című témakörben talál.
- Keresse meg a forrásoldali eszközön futó aktív biztonsági mentési feladatokat. Ez magában foglalja a StorSimple-adatvédelmi konzol gazdagépének feladatait. Várjon, amíg az aktuális feladatok befejeződik. 
- Hozzáférés egy, a StorSimple 5000-7000 Series eszközhöz csatlakoztatott Windows Server-gazdagéphez. A gazdagépnek egy támogatott Windows Server-verziót kell futtatnia [Azure file Sync együttműködési képesség](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)című cikkben leírtak szerint.
- A StorSimple-kötetek a gazdagépre vannak csatlakoztatva, és fájlmegosztást tartalmaznak.
- A gazdagép elegendő helyi tárterülettel rendelkezik a helyileg gyorsítótárazott adatai tárolásához.
- Tulajdonosi szintű hozzáférés a Azure File Sync telepítéséhez használni kívánt Azure-előfizetéshez. Ha nem rendelkezik tulajdonosi vagy rendszergazdai jogosultsággal, akkor problémák léphetnek fel, amikor Felhőbeli végpontot hoz létre a szinkronizálási csoport számára.
- Hozzáférés egy [általános célú v2 Storage-fiókhoz](https://docs.microsoft.com/azure/storage/common/storage-account-overview) egy Azure-fájlmegosztás használatával, amelyhez szinkronizálni kíván. További információ: [Tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Azure- [fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Migrálási folyamat

Az adatok migrálása az StorSimple 5000-7000-ből az AFS-be kétlépéses folyamat:
1.  Az adatok replikálása a helyszíni fájlkiszolgálón, ahol a StorSimple-kötetek egy Azure Files-megosztáshoz vannak csatlakoztatva.  A replikáció egy telepített AFS-ügynökön keresztül történik.
2.  Válassza le a StorSimple eszközt. A helyi lemezek ezután helyi gyorsítótárként működnek.

### <a name="migration-steps"></a>A migrálás lépései

A következő lépések végrehajtásával telepítse át a StorSimple-köteteken konfigurált Windows-fájlmegosztást egy Azure File Sync-megosztásra. 
1.  Hajtsa végre ezeket a lépéseket ugyanarra a Windows Server-gazdagépre, ahol a StorSimple-kötetek csatlakoztatva vannak, vagy egy másik rendszert használnak. 
    - [Készítse elő a Windows Servert a Azure file Sync használatával való használatra](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Telepítse a Azure file Sync ügynököt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Telepítse a Storage Sync szolgáltatást](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Regisztrálja a Windows Servert a Storage Sync szolgáltatással](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Hozzon létre egy szinkronizálási csoportot és egy Felhőbeli végpontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). A szinkronizálási csoportokat minden olyan Windows-fájlmegosztás esetében el kell végezni, amelyet át kell telepíteni a gazdagépről.
    - [Hozzon létre egy kiszolgálói végpontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Adja meg az elérési utat a fájlmegosztás adatait tartalmazó StorSimple kötet elérési útjával. Ha például a StorSimple kötet meghajtó `J` , és az adatai a ben találhatók `J:/<myafsshare>` , akkor adja hozzá ezt az elérési utat kiszolgálói végpontként. Hagyja **Letiltva a rétegek letiltását**. **Tiering**
2.  Várjon, amíg a fájlkiszolgáló szinkronizálása befejeződik. Az adott szinkronizálási csoport minden egyes kiszolgálójára vonatkozóan ügyeljen a következőre:
    - A legutóbbi szinkronizálásra és letöltésre irányuló legutóbbi szinkronizálás időbélyege nemrég történt.
    - Az állapot a feltöltéshez és a letöltéshez egyaránt zöld.
    - A **szinkronizálási tevékenység** nagyon keveset mutat, vagy a szinkronizálandó fájlok közül nem marad.
    - A **nem szinkronizált fájlok** a feltöltéshez és a letöltéshez egyaránt 0.
    A kiszolgáló-szinkronizálás befejezéséről a következő témakörben olvashat bővebben: [Azure file Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). A szinkronizálás több órát is igénybe vehet, az adatmérettől és a sávszélességtől függően. A szinkronizálás befejeződése után az összes adatai biztonságosan az Azure-fájlmegosztás részét jelentik. 
3.  Nyissa meg a StorSimple-kötetek megosztásait. Válasszon ki egy megosztást, kattintson a jobb gombbal, és válassza a **Tulajdonságok**lehetőséget. Jegyezze fel a megosztási engedélyeket a **Biztonság**területen. Ezeket az engedélyeket manuálisan kell alkalmazni az új megosztásra a későbbi lépésben.
4.  Attól függően, hogy ugyanazt a Windows Server-gazdagépet vagy más szolgáltatást használja, a következő lépések eltérőek lesznek.

    Hagyja ki ezt a lépést, és folytassa a következő lépéssel, ha másik Windows Server-gazdagépet használ. Ha ugyanezt a Windows-fájlkiszolgáló-t használja az AFS-hez, néhány perc leállást tapasztalhat. 
    - A **leállás elindul** – törölje a *1f lépésben*létrehozott kiszolgálói végpontot. 
    - Hozzon létre egy új kiszolgáló-végpontot azzal az elérési úttal, ahová az adatnak szüksége lesz.
    - Ha a kiszolgálói végpont Kifogástalan állapotba kerül (ez eltarthat néhány percig), akkor ebben az új helyen fogja látni az információt. Mostantól konfigurálhatja a Windows Server-gazdagépet, hogy az új helyről szolgálja ki a fájlokat. - A **leállás véget ér**.
5.  Ha egy másik Windows-fájlkiszolgálón használ Azure File Sync, akkor nem fog semmilyen állásidőt tapasztalni. 
    - Adjon hozzá egy másik kiszolgálói végpontot annak a helyi tárterületnek az elérési útjával, amelyet a StorSimple-eszköz helyett gyorsítótárként kíván használni. 
    - A fájlokat néhány perc alatt megtekintheti az új kiszolgálón. Bármikor elvégezheti a váltást a StorSimple-eszközről erre az új helyre a gazdagépen.

    > [!TIP] 
    > Ügyeljen arra, hogy az új fájlmegosztást ugyanazzal a névvel és azonos elérési úttal konfigurálja, mint amelyet a rendszer a megszakítás csökkentése érdekében. Ha a DFS-N-t használja, előfordulhat, hogy módosítania kell a konfigurációt.
6.  Konfigurálja újra a megosztási engedélyeket a *3. lépésben*feljegyzett módon.

Ha problémákat tapasztal az adatáttelepítés során, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>További lépések

Ha az AFS nem az Ön számára megfelelő megoldás, megismerheti, hogyan [telepíthet át egy StorSimple 5000-7000-sorozatból egy 8000 sorozatú eszközre az adatok áttelepítését](storsimple-8000-migrate-from-5000-7000.md).

