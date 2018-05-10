---
title: Az Azure Backup - Offline biztonsági másolat a DPM és az Azure Backup Server |} Microsoft Docs
description: Ismerje meg, hogyan Azure Backup szolgáltatás lehetővé teszi az Azure Import/Export szolgáltatás használata a hálózati adatokat küldeni. Ez a cikk ismerteti a kezdeti biztonsági másolati adatokat kapcsolat nélküli összehangolása az Azure importálási exportálása szolgáltatás használatával.
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shivamg
editor: ''
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 5/8/2018
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: e3f7ae187bee8680fbff7e5c78c666a0bda7e48f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>A DPM és az Azure Backup Server offline biztonsági másolat munkafolyamat
Azure biztonsági mentés számos beépített hatékonyság, hálózati és tárolási költségek csökkentése során a kezdeti teljes biztonsági mentés az Azure-bA rendelkezik. Kezdeti teljes biztonsági mentés általában nagy adatmennyiségek átvitelét, és azt követő biztonsági mentéseket, amely csak az eltérések/növekményes átviteli képest nagyobb hálózati sávszélesség szükséges. Azure biztonsági mentés tömöríti a kezdeti biztonsági mentéseket. A folyamatot, amely offline összehangolása Azure biztonsági mentési lemezek is használhatók a tömörített kezdeti biztonsági másolati adatokat kapcsolat nélküli feltöltése az Azure-bA.

A kapcsolat nélküli összehangolása folyamat Azure biztonsági mentés szorosan integrálva van a [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md) , amely lehetővé teszi az adatok átvitele Azure lemezek használatával. Ha terabájt (több TB-nyi), amelyet a nagy késleltetésű és kis sávszélességű hálózaton keresztül kell átvinni kezdeti biztonsági másolati adatokat, a kapcsolat nélküli összehangolása munkafolyamat segítségével küldje el a kezdeti biztonsági másolatot egy vagy több merevlemezeken való egy Azure-adatközpontban. Ez a cikk áttekintése és tovább a munkafolyamat befejezése a System Center DPM és az Azure Backup Server részletek lépéseket tartalmazza.

> [!NOTE]
> Offline biztonsági másolat a Microsoft Azure Recovery Services (MARS) ügynök folyamata nem azonos azzal a System Center DPM és az Azure Backup Server. Az Offline biztonsági másolat használatával a MARS agent információkért lásd: [Ez a cikk](backup-azure-backup-import-export.md). Offline biztonsági másolat nem támogatott a rendszerállapot biztonsági mentéshez az Azure Backup-ügynök használatával történik. 
>

## <a name="overview"></a>Áttekintés
Azure biztonsági mentés és az Azure Import/Export offline összehangolása alkalmas használata egyszerű offline állapotba az adatok feltöltése az Azure-bA lemezek használatával. Az Offline biztonsági mentés folyamata a következő lépésekből áll:

> [!div class="checklist"]
> * A biztonsági mentési adatok helyett a hálózaton keresztül küldött beíródik egy *átmeneti helyre*
> * Az adatok a *átmeneti helyre* majd írni egy vagy több SATA lemezek használatával a *AzureOfflineBackupDiskPrep* segédprogram
> * Az Azure importálási feladat automatikusan hozta létre a segédprogram
> * A SATA meghajtókat a legközelebbi Azure-adatközpontban majd küldött
> * Az Azure a biztonsági mentési adatok feltöltése után Azure biztonsági mentési másolja át a biztonsági mentési adatok a biztonsági mentési tárolóba, és a növekményes biztonsági mentés van ütemezve.

## <a name="supported-configurations"></a>Támogatott konfigurációk 
Offline biztonsági másolat az Azure Backup szolgáltatás, amely külső helyszíni biztonsági másolatok adatait a helyszíni a Microsoft Cloud összes üzembe helyezési modell esetén támogatott. Ez magában foglalja

> [!div class="checklist"]
> * Biztonsági mentési fájlok és mappák, valamint a Microsoft Azure Recovery Services (MARS) ügynöke, vagy az Azure Backup szolgáltatás ügynöke. 
> * Az összes fájlt a System Center Data Protection Manager (SC DPM) és munkaterhelések biztonsági mentése 
> * A Microsoft Azure Backup Server munkaterhelések és a fájlok biztonsági mentése <br/>

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a következő előfeltételek teljesülnek-e a kapcsolat nélküli biztonsági mentés munkafolyamat kezdeményezése előtt
* A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) létrejött. Szeretne létrehozni egyet, tekintse meg a lépéseket [Ez a cikk](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Az Azure Backup szolgáltatás ügynökének vagy Azure Backup Server vagy SC DPM telepítve van, értelemszerűen vagy Windows Server és Windows ügyfél és a számítógép regisztrálva van a Recovery Services-tároló. Győződjön meg arról, hogy csak a [Azure Backup szolgáltatás legújabb verziójára](https://go.microsoft.com/fwlink/?linkid=229525) szolgál. 
* [Töltse le az Azure közzététele beállításfájl](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) használni kívánt biztonsági másolatot az adatairól a számítógépen. Az előfizetés, amelyen a közzétételi beállítások fájlja letöltése az előfizetést, amely tartalmazza a Recovery Services-tároló eltérő lehet. Ha az előfizetés állami Azure-Felhőkhöz, majd használja az alábbi hivatkozások a Azure Közzététel beállításai fájl letöltéséhez.

    | Szuverén felhő régió | Az Azure közzétételi beállítások hivatkozás |
    | --- | --- |
    | Egyesült Államok | [Hivatkozás](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kína | [Hivatkozás](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Az Azure Storage-fiók *klasszikus* telepítési modell létrehozását az előfizetés letöltésére a közzétételi beállítások fájlja alább látható módon: 

 ![A klasszikus storage-fiók létrehozása](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Egy ideiglenes helyet, amely lehet, hogy egy hálózati megosztásra vagy bármely további meghajtó a számítógépen, belső vagy külső, a kezdeti másolat tárolásához elegendő lemezterülettel rendelkező jön létre. Például ha próbál 500 GB-os fájlkiszolgáló biztonsági mentése, győződjön meg arról, hogy az átmeneti területre legalább 500 GB. (Tömörítés miatt a kisebb méretű használja.)
* Ben elérhető Azure elküldendő lemezeket győződjön meg arról, hogy, amely csak 2,5 hüvelyk SSD vagy 2.5-es vagy 3,5 hüvelykes SATA II/III belső merevlemezek használhatók. Merevlemez-meghajtókat is használhat legfeljebb 10 TB. Ellenőrizze a [Azure Import/Export szolgáltatás dokumentációja](../storage/common/storage-import-export-service.md#hard-disk-drives) meghajtók, a szolgáltatás által támogatott legújabb készlete esetében.
* A SATA-meghajtók rendelkeznek-e egy számítógéphez kell csatlakoztatni (néven egy *másolási számítógép*), amelyről származó biztonsági mentési adatok másolása az *átmeneti helyre* számára a SATA meghajtókat történik. Győződjön meg arról, hogy a Bitlocker engedélyezve van a *másolási számítógép* 

## <a name="workflow"></a>Munkafolyamat
Ebben a szakaszban található információk segítséget, hogy az adatok egy Azure-adatközpontban kézbesített, és az Azure-tárhelyre feltöltött, végezze el az offline biztonsági másolat munkafolyamat. Ha az importálás szolgáltatás vagy a folyamat minden szempontját kérdése van, tekintse meg a [importálási áttekintés](../storage/common/storage-import-export-service.md) korábban hivatkozott dokumentációját.

### <a name="initiate-offline-backup"></a>Offline biztonsági mentése
1. Ha a biztonsági mentés ütemezése, megjelenik a következő képernyő (a Windows Server, a Windows ügyfél vagy a System Center Data Protection Manager).

    ![Importálás képernyőn](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Ez a megfelelő képernyőn a System Center Data Protection Manager: <br/>
    ![SC DPM és az Azure Backup server importálás képernyőn](./media/backup-azure-backup-import-export/dpmoffline.png)

    Leírja, hogy a bemeneti adatok a következőképpen történik:

    * **Átmeneti hely**:, amelyhez a kezdeti biztonsági másolatot írása ideiglenes tárolási helyét. Átmeneti hely lehet egy hálózati megosztásra vagy a helyi számítógépen. A másolási számítógép és a forrásszámítógép nem egyezik, azt javasoljuk, hogy megadja a teljes elérési útja az átmeneti tárolási helyét.
    * **Az Azure importálási feladat nevének**: mely Azure Import alapján szolgáltatás és az Azure Backup követésére küldött adatok továbbítása lemezeken Azure egyedi nevét.
    * **Az Azure közzétételi beállítási**: Adja meg a közzétételi beállítások fájlja helyi elérési útja.
    * **Az Azure előfizetés-azonosító**: az Azure előfizetés-azonosító az előfizetést, amelybe letöltötte az Azure közzététele beállításfájl. 
    * **Az Azure Storage-fiók**: a storage-fiókot az Azure közzététele beállításfájl társított Azure-előfizetés nevét.
    * **Az Azure Storage-tároló**: a cél tárolási blob, ahol a biztonsági mentési adatok importálása az Azure storage-fiók nevét.

     Mentse a *átmeneti helyre* és a *Azure importálási feladat nevének* megadott, a lemezek való előkészítéséhez szükséges.  
     
2. A munkafolyamat befejezéséhez, és az offline biztonsági másolat másolás indításához kattintson a **biztonsági másolat készítése most** az Azure Backup agent felügyeleti konzolon. A kezdeti biztonsági mentés írása az átmeneti területre, ez a lépés részeként.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/backupnow.png)

    A System Center Data Protection Manager vagy az Azure Backup server megfelelő munkafolyamat befejezéséhez kattintson a jobb gombbal a **védelmi csoport**, majd válassza a **helyreállítási pont létrehozása** lehetőséget. Ezután válassza ki a **Online védelem** lehetőséget.

    ![SC DPM és az Azure Backup server biztonsági másolat készítése](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    A művelet befejezése után az ideiglenes helyet készen áll a lemez előkészítéséhez használható.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Készítse elő a SATA meghajtókat, és küldje el az Azure-bA
A *AzureOfflineBackupDiskPrep* segédprogram segítségével készítse elő a SATA meghajtókat a legközelebbi Azure Datacenter küldött. Ezt a segédprogramot telepítési könyvtárában a Recovery Services Agent ügynököt a következő útvonalon érhető el:

   *\Microsoft* *azure* *helyreállítási* *szolgáltatások* * Agent\Utils\*

1. Keresse fel a könyvtárat, és másolja a **AzureOfflineBackupDiskPrep** könyvtárhoz, amelyiken a SATA meghajtókat készüljön csatlakoztatott másolási számítógépre. Ellenőrizze, hogy a Másolás számítógép-ben elérhető a következő:

    * A másolási számítógép érhető el az ideiglenes helyet a kapcsolat nélküli összehangolása munkafolyamat azonos hálózati elérési útját, amely lett megadva a **kezdeményezni az offline biztonsági másolat** munkafolyamat.
    * A BitLocker engedélyezve van a Másolás számítógépen.
    * A másolási számítógép hozzáférhessen az Azure-portálon.

    Ha szükséges, a másolási számítógép lehet ugyanaz, mint a forrásoldali számítógép. 
    
    > [!IMPORTANT] 
    > A forrásoldali számítógépen egy virtuális gépet, majd esetén kötelezően meg kell használni egy másik fizikai kiszolgálón vagy ügyfélszámítógépen. a másolási számítógépként.
    
    
2. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen a *AzureOfflineBackupDiskPrep* segédprogram könyvtárba helyezze, mint az aktuális könyvtárra, és futtassa az alábbi parancsot:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Paraméter | Leírás |
    | --- | --- |
    | %s&lt;*átmeneti hely elérési útja*&gt; |Kötelező bemeneti, amellyel a megadott átmeneti tárolási helyének elérési útját adja meg a **kezdeményezni az offline biztonsági másolat** munkafolyamat. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; |Nem kötelező bemeneti, amellyel elérési útját adja meg a **Azure közzétételi beállítási** megadott fájl a **kezdeményezni az offline biztonsági másolat** munkafolyamat. |

    > [!NOTE]
    > A &lt;elérési útját AzurePublishSettingFile&gt; érték megadása kötelező, ha a Másolás számítógép és a forrásszámítógép nem ugyanaz.
    >
    >

    A parancs futtatásakor a segédprogram kéri az Azure importálási feladat, amely megfelel a meghajtókat, elő kell készíteni a kijelölést. Ha csak egy egyetlen importálási feladat helyéhez társítva a megadott átmeneti, ahogy az alábbi képernyő jelenik meg.

    ![Az Azure lemez előkészítő eszköz bemeneti](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Adja meg a meghajtóbetűjel, a záró kettőspont nélkül a csatlakoztatott lemezt, amelyet szeretne az Azure-bA átviteli előkészítése. A meghajtó, amikor a rendszer kéri a formázáshoz erősítse meg a műveletet.

    Az eszköz majd megkezdi a lemez és a biztonsági mentési adatok másolása előkészítéséhez. Szükség lehet további lemezeket, amikor a rendszer kéri az eszközzel, abban az esetben, ha a megadott lemezén nincs elég hely a biztonsági mentési adatok csatlakoztatni. <br/>

    Az eszköz sikeres végrehajtásának végén megadott egy vagy több lemezt az Azure-bA szállítási el kell készíteni. Emellett az importálási feladat nevű során megadott a **kezdeményezni az offline biztonsági másolat** munkafolyamat jön létre az Azure-ban. Végül az eszköz a Azure adatközpontba, ahol a lemezek kell szállítani jelenít meg a szállítási cím.

    ![Teljes Azure lemez előkészítése](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. A parancs végrehajtásának végén is megjelenik a lehetőséget, ha szállítási adatokat alább látható módon:

    ![Szállítási adatok beállítás frissítése](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. A részletek a azonnal adhatja meg. Az eszköz végigvezeti a folyamat bemenetek sorozata. Ha nem rendelkezik információkat, például száma vagy egyéb szállítási kapcsolatos részletek nyomon követése, azonban is a munkamenet befejezéséhez. A szállítási részleteit később frissítésének lépései ebben a cikkben találhatók. 

6. Küldje el a lemezeket, az eszköz a megadott címre, és a jövőben követési számának.

   > [!IMPORTANT] 
   > Két Azure importálási feladat követési ugyanannyi rendelkezhet. Győződjön meg arról, hogy egyetlen Azure importálási feladat alatt az eszköz által készített meghajtók szállítják együtt, egyetlen csomag, és hogy van-e a csomag egyetlen egyedi nyilvántartási szám. Ne vonja össze a meghajtók részeként készített **különböző** Azure importálási feladatok csomagban. 

5. Ha rendelkezik a nyomkövetési kapcsolatos információk, nyissa meg a forrásoldali számítógép, amely importálási feladat befejezésére vár, és futtassa a következő parancsot egy rendszergazda jogú parancssort a *AzureOfflineBackupDiskPrep* segédprogram könyvtárba helyezze, mint a aktuális könyvtárhoz: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Opcionálisan futtathatja a következő parancsot egy olyan számítógépről, mint a *másolási számítógép*, a *AzureOfflineBackupDiskPrep* segédprogram könyvtárba helyezze, mint az aktuális könyvtárban:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Paraméter | Leírás |
    | --- | --- |
    | U: | Kötelező bemeneti frissítheti az Azure importálási feladat naplóküldése részletei |
    | %s&lt;*átmeneti hely elérési útja*&gt; | Kötelező bemeneti, ha a parancs nem fut a forrásszámítógépen. A megadott átmeneti tárolási helyének elérési útját biztosítható a **kezdeményezni az offline biztonsági másolat** munkafolyamat. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; | Kötelező bemeneti, ha a parancs nem fut a forrásszámítógépen. Biztosítja a elérési útját a **Azure közzétételi beállítási** megadott fájl a **kezdeményezni az offline biztonsági másolat** munkafolyamat. |
    
    A segédprogram automatikusan észleli az importálási feladat, amely a forrásoldali számítógép vár a, vagy az importálási feladatok társított az ideiglenes helyet, ha a parancs fut egy másik számítógépre. Ezután lehetővé teszi a bemenetek végig szállítási adatainak frissítése a lent látható módon: 
    
    ![Szállítási adatok megadása](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Ha a bemeneti adatok érhetők el, gondosan ellenőrizze a részleteket, és a szállítási adatait, írja be a megadott véglegesítése *Igen*. 

    ![Tekintse át a szállítási adatok](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. A szállítási adatait sikeresen frissítéseket, a segédprogram biztosít egy helyi mappába az Ön által megadott szállítási részletei alább látható módon tároló 

    ![Szállítási adatok tárolása](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Győződjön meg arról, hogy a meghajtó eléri-e az Azure-adatközpontban biztosító a szállítási az adatokat a két héten belül a *AzureOfflineBackupDiskPrep* segédprogramot. Ennek hiányában a meghajtók, nem kerülnek feldolgozásra eredményezhet.  

Miután végzett a fenti lépéseket, az Azure-adatközpontban készen áll a meghajtók fogadni és további dolgozza fel őket a biztonsági mentési adatok átvitele a meghajtókat a létrehozott klasszikus típusú Azure storage-fiók. 

### <a name="time-to-process-the-drives"></a>A meghajtók feldolgozási időtartama 
Mennyi ideig tart a folyamat, például a szállítási idő különböző tényezőktől függően változik az Azure importálási feladat sikertelen feladat-típus, típusa és másolásának adatok méretétől és a megadott lemez méretét. Az Azure Import/Export szolgáltatás nem rendelkezik egy SLA-t, de a lemezek fogadása után a szolgáltatás nagy hangsúlyt fektet 7 – 10 nap végezze el a biztonsági mentési adatok másolása az Azure storage-fiókjába. A következő szakasz részletesen az Azure importálási feladat állapotának figyeléséről. 

### <a name="monitoring-azure-import-job-status"></a>Azure importálási feladat állapotának figyelése
Míg a meghajtók az átvitel során, illetve az Azure-adatközpontban kell másolni a tárfiókot, az Azure Backup szolgáltatás ügynökének vagy SC DPM vagy az Azure biztonsági mentés a forrásszámítógépen konzolját állapotát jeleníti meg a következő feladat az ütemezett biztonsági mentések. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Az alábbi lépésekkel, az importálási feladat állapotának ellenőrzéséhez. 
1. Nyisson meg egy rendszergazda jogú parancssort a forrásszámítógépen, és futtassa a következő parancsot:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  A kimenet az importálási feladat aktuális állapotát jeleníti meg az alább látható módon: 

    ![Importálási feladat állapotának ellenőrzése](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Az Azure importálási feladatnak a különböző állapotok további információkért lásd: [Ez a cikk](../storage/common/storage-import-export-service.md#how-does-the-azure-importexport-service-work)

### <a name="complete-the-workflow"></a>A munkafolyamat befejezése
Az importálási feladat befejezése után a tárfiókban lévő kezdeti biztonsági másolati adatokat érhető el. A következő ütemezett biztonsági mentés idején Azure biztonsági mentés átmásolja az adatokat a tartalmát a tárfiók a Recovery Services-tároló alább látható módon: 

   ![Adat másolása az Recovery Services-tároló](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

A következő ütemezett biztonsági mentés idején Azure Backup szolgáltatás hajtja végre a növekményes biztonsági mentés keresztül a kezdeti biztonsági másolatot.

## <a name="next-steps"></a>További lépések
* Az Azure Import/Export munkafolyamat kapcsolatos kérdéseket, tekintse meg a [adatok átviteléhez a Blob storage a Microsoft Azure Import/Export szolgáltatás használata](../storage/common/storage-import-export-service.md).
* Tekintse át az offline biztonsági másolat részt, az Azure biztonsági mentési [gyakran ismételt kérdések](backup-azure-backup-faq.md) a munkafolyamat kapcsolatos kérdéseivel.
