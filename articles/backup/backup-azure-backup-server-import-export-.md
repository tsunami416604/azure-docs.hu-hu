---
title: Az Azure Backup – online biztonsági mentést a DPM és az Azure Backup Server
description: Ismerje meg, hogyan Azure Backup lehetővé teszi, hogy a hálózatról az Azure Import/Export szolgáltatással adatokat. Ez a cikk ismerteti, hogy a kapcsolat nélküli beültetés a kezdeti biztonsági mentési adatok az Azure importálás exportálás szolgáltatás használatával.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: saurse
ms.openlocfilehash: 01b90d6bb18addd6a0235101f86b9d51953cc096
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818557"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>A DPM és az Azure Backup Server offline biztonsági mentés munkafolyamata
Az Azure Backup rendelkezik, amely a hálózati és tárolási költségek csökkentése az Azure-ban adatok kezdeti teljes biztonsági mentés során számos beépített hatékonyság. Kezdeti teljes biztonsági mentés általában nagy mennyiségű adat átvitele, és azt követő biztonsági mentéseket, hogy csak a változásokat/szalagnak transfer képest nagyobb hálózati sávszélesség szükséges. Az Azure Backup tömöríti a kezdeti biztonsági mentés. Kapcsolat nélküli beültetéssel is a folyamatot az Azure Backup lemez is használható a tömörített kezdeti biztonsági mentési adatok offline feltöltése az Azure-bA.

A kapcsolat nélküli beültetéssel is folyamat az Azure Backup szolgáltatás szorosan integrálva van a [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md) , amely lehetővé teszi az adatok átviteléhez az Azure-bA lemezek használatával. Ha terabájt (TB-osra bővül) a kezdeti biztonsági mentési adatok, amelyek nagy késésű és alacsony sávszélességű hálózaton keresztül kell átvinni, használhatja a kapcsolat nélküli beültetéssel is munkafolyamat tehetnek a kezdeti biztonsági másolatot a legalább egy merevlemez-meghajtók egy Azure-adatközpontba. Ez a cikk áttekintése és a további részletek lépéseket végrehajtani ezt a munkafolyamatot a System Center DPM és az Azure Backup Server.

> [!NOTE]
> A folyamat a Microsoft Azure Recovery Services-(MARS) ügynök Offline biztonsági mentés nem azonos azzal a System Center DPM és az Azure Backup Server. Offline biztonsági mentés az MARS-ügynök és a további információkért lásd: [Ez a cikk](backup-azure-backup-import-export.md). Offline biztonsági mentés nem támogatott a rendszerállapot biztonsági másolatokat az Azure Backup ügynök használatával történik. 
>

## <a name="overview"></a>Áttekintés
Az Azure Backup és az Azure Import/Export offline áttöltést képesség használata egyszerű offline az adatok feltöltése az Azure-bA lemezek használatával. Az Offline biztonsági mentés folyamata az alábbi lépésekből áll:

> [!div class="checklist"]
> * A biztonsági mentési adatok helyett a hálózaton keresztül küldött íródik a *előkészítési helyét*
> * Az adatok a *előkészítési helyét* majd egy vagy több SATA lemezek használatával írt a *AzureOfflineBackupDiskPrep* segédprogram
> * A segédprogram automatikusan hoz létre az Azure importálási feladat
> * A SATA meghajtókat majd érkeznek a legközelebbi Azure-adatközpont
> * Miután befejeződött a feltöltés, a biztonsági mentési adatok az Azure-ba, az Azure Backup a biztonsági mentési adatot másol a biztonsági mentési tár, és a növekményes biztonsági mentések.

## <a name="supported-configurations"></a>Támogatott konfigurációk 
Offline biztonsági másolat az Azure Backup, telephelyen kívüli biztonsági mentési adatait a helyszíni a Microsoft Cloud minden üzembehelyezési modell esetén támogatott. Ez magában foglalja

> [!div class="checklist"]
> * Biztonsági mentési fájlok és mappák, a Microsoft Azure Recovery Services-(MARS) ügynök vagy az Azure Backup ügynököt. 
> * Az összes számítási feladatok és a System Center Data Protection Manager (SC DPM) fájlok biztonsági mentése 
> * A Microsoft Azure Backup Server számítási feladatok és a fájlok biztonsági mentése <br/>

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy az alábbi előfeltételek teljesülnek-e, mielőtt elindítaná az Offline Backup munkafolyamat
* A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) létrejött. Hozzon létre egyet, tekintse meg a lépéseket a [Ez a cikk](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Az Azure Backup-ügynök vagy az Azure Backup Server vagy SC DPM telepítve van, amennyiben alkalmazhatók, vagy a Windows Server vagy Windows ügyfél és a számítógép regisztrálva van a Recovery Services-tárolóval. Győződjön meg arról, hogy csak a [legújabb verzióját az Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) szolgál. 
* [Az Azure közzétételi beállítási fájl letöltése](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) azon a számítógépen, amelyről biztonsági másolatot szeretne készíteni az adatokról. Az előfizetést, amelyen a közzétételi beállítások fájljának letöltése az előfizetést, amely tartalmazza a Recovery Services-tárolónak eltérő lehet. Ha az előfizetés Azure-felhők független, majd használja az alábbi hivatkozások az Azure közzétételi beállítási fájl letöltése.

    | Szuverén felhő régió | Az Azure közzétételi beállítások fájl hivatkozás |
    | --- | --- |
    | Egyesült Államok | [Hivatkozás](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kína | [Hivatkozás](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Egy Azure Storage-fiókkal *klasszikus* üzemi modell létrehozása az előfizetésben, ahonnan letöltötte a közzétételi beállítási fájl alább látható módon: 

 ![Egy klasszikus tárfiók létrehozása](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Egy előkészítési helyet, amely lehet egy hálózati megosztásra vagy bármely további meghajtó a számítógépen, belső vagy külső, a kezdeti másolat tárolásához elegendő lemezterület a jön létre. Például egy 500 GB-os fájlt kiszolgálóról biztonsági mentést próbál, ha arról, hogy az átmeneti területen legalább 500 GB-os. (A tömörítés miatt kisebb használt.)
* Érdemes az Azure-bA küldendő lemezeket győződjön meg arról, amelyek csak 2,5 hüvelyk SSD vagy 2,5-es vagy 3,5 hüvelykes SATA II. és III belső merevlemez-meghajtókat használ. Használhatja a merevlemez-meghajtók legfeljebb 10 TB. Ellenőrizze a [Azure Import/Export szolgáltatás dokumentációja](../storage/common/storage-import-export-requirements.md#supported-hardware) meghajtó, amely a szolgáltatás támogatja a legújabb csoporton.
* Egy számítógéphez kell csatlakoztatni kell a SATA meghajtókat (néven egy *másolási számítógép*), ahonnan a biztonsági mentési adatok másolatát a *előkészítési helyét* , a SATA meghajtókat történik. Győződjön meg arról, hogy a BitLocker engedélyezve van a *másolási számítógép* 

## <a name="workflow"></a>Munkafolyamat
Ebben a szakaszban található információk segítségével végezze el az offline biztonsági mentési munkafolyamat, hogy az adatok is lehet az Azure-adatközpont és az Azure Storage-bA feltöltött. Ha kérdései vannak az importálási szolgáltatás vagy a folyamat minden aspektusa, tekintse meg a [importálási szolgáltatás áttekintés](../storage/common/storage-import-export-service.md) korábban hivatkozott dokumentációját.

### <a name="initiate-offline-backup"></a>Offline biztonsági mentés kezdeményezéséhez
1. Amikor a biztonsági mentés ütemezése, láthatja az alábbi képernyőn (a Windows Server, a Windows ügyfél vagy a System Center Data Protection Manager).

    ![Importálási képernyőn](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Itt látható a megfelelő képernyőn a System Center Data Protection Manager: <br/>
    ![SC DPM és az Azure Backup server importálási képernyőn](./media/backup-azure-backup-import-export/dpmoffline.png)

    A leírás a bemeneti adatok a következőképpen történik:

    * **Átmeneti hely**: Az ideiglenes tárolási hely, amelyhez a kezdeti biztonsági másolatot íródik. Átmeneti hely lehet egy hálózati megosztásra vagy a helyi számítógépen. Ha a másolási számítógép és a forrásoldali számítógép eltérő, javasoljuk, hogy a teljes elérési útja az előkészítési hely megadása.
    * **Azure-beli importálási feladat neve**: Az egyedi név szerint mely Azure Import szolgáltatás és az Azure Backup nyomon követése az elküldött adatok átvitelét az lemezeket az Azure-bA.
    * **Az Azure közzétételi beállítások**: Adja meg a közzétételi beállítások fájljának helyi elérési útja.
    * **Azure-előfizetés azonosítója**: Az előfizetés regisztrációját az Azure közzétételi beállítási fájl letöltött Azure előfizetés-azonosítója. 
    * **Azure Storage Account**: Az Azure-előfizetésben az Azure közzétételi beállítási fájl társított storage-fiók neve.
    * **Az Azure Storage-tároló**: Az Azure storage-fiók, amelybe importálják a biztonsági mentési adatokat a rendeltetési tárolási BLOB neve.

     Mentse a *előkészítési helyét* és a *Azure importálási feladat nevének* , a lemezek előkészítéséhez szükség van a megadott.  
     
2. Végezze el a munkafolyamatot, és elindította a offline biztonsági másolat, kattintson a **azonnali biztonsági mentés** az Azure Backup ügynök felügyeleti konzolon. A kezdeti biztonsági mentés az átmeneti területre, ez a lépés részeként van megírva.

    ![Biztonsági másolat készítése](./media/backup-azure-backup-import-export/backupnow.png)

    A System Center Data Protection Manager vagy az Azure Backup server megfelelő munkafolyamat befejezéséhez kattintson a jobb gombbal a **védelmi csoport**, majd válassza a **helyreállítási pont létrehozása** lehetőséget. Ezután válassza ki a **az Online védelem** lehetőséget.

    ![SC DPM és az Azure Backup server biztonsági másolat készítése](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    A művelet befejezése után az előkészítési hely készen áll a lemez előkészítéséhez használható.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA meghajtókat előkészítjük, és elküldjük az Azure-bA
A *AzureOfflineBackupDiskPrep* segédprogram a SATA meghajtókat a legközelebbi Azure-adatközpontba küldött előkészítéséhez használható. Ez a segédprogram telepítési könyvtárában a Recovery Services-ügynök a következő elérési úton érhető el:

   *\Microsoft* *azure* *helyreállítási* *szolgáltatások* * Agent\Utils\*

1. Nyissa meg a könyvtárat, és másolja a **AzureOfflineBackupDiskPrep** másolási számítógépre, amelyen a SATA meghajtókat, készüljön fel a csatlakoztatott mappa. Ellenőrizze, hogy a másolási számítógép meg a következőket:

    * A másolási számítógép érhető el az előkészítési hely offline áttöltést munkafolyamat hálózati elérési útját lett megadva a **offline biztonsági mentés kezdeményezéséhez** munkafolyamat.
    * A BitLocker engedélyezve van a Másolás számítógépen.
    * A másolási számítógép hozzáférhessen az Azure Portalon.

    Szükség esetén a másolási számítógép lehet ugyanaz, mint a forrásszámítógépen. 
    
    > [!IMPORTANT] 
    > Ha a forrásoldali számítógép egy virtuális gépet, majd azt megadása kötelező a másolási számítógépen egy másik fizikai kiszolgáló vagy ügyfélgép használandó.
    
    
2. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen, amelyen a *AzureOfflineBackupDiskPrep* segédprogram könyvtárba az aktuális könyvtárban, majd futtassa a következő parancsot:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Paraméter | Leírás |
    | --- | --- |
    | %s:&lt;*átmeneti hely elérési útja*&gt; |Kötelező bemeneti, amellyel a megadott átmeneti tárolási helyének elérési útját adja meg a **offline biztonsági mentés kezdeményezéséhez** munkafolyamat. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; |Nem kötelező bemenő, amellyel elérési útját adja meg a **Azure közzétételi beállítások** a megadott fájl a **offline biztonsági mentés kezdeményezéséhez** munkafolyamat. |

    > [!NOTE]
    > A &lt;elérési útját AzurePublishSettingFile&gt; értéket kötelező, ha a másolási számítógép és a forrásoldali számítógép különböző.
    >
    >

    A parancs futtatásakor a a segédprogram az Azure Import-feladattal, amely megfelel a meghajtók, elő kell készíteni a kijelölt kérések. Ha csak egy egyetlen importálási feladat a megadott átmeneti hely társítva, akkor a következő egy hasonló képernyő jelenik meg.

    ![Az Azure lemez előkészítő eszköz bemenet](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Adja meg a záró kettőspont nélkül a meghajtó betűjelét a csatlakoztatott lemezt, amelyet szeretne előkészítése az átvitelre az Azure-bA. A formázáshoz a meghajtó, amikor a rendszer kéri, erősítse meg a műveletet.

    Az eszköz ezután elkezdi előkészítéséhez, a lemez és a biztonsági mentési adatok másolását. Szükség lehet további lemezt, amikor a rendszer kéri, az eszköz abban az esetben, ha a megadott lemezen nincs elég hely a biztonsági mentési adatok tárolására. <br/>

    Az eszköz sikeres végrehajtásának végén egy vagy több lemezt, amely a megadott készen áll a szállítási címhez tartozó Azure-bA. Emellett a név az importálási feladat során megadott az **offline biztonsági mentés kezdeményezéséhez** munkafolyamat jön létre az Azure-ban. Végül az eszköz megjeleníti a szállítási cím az Azure-adatközpontba a lemezek amelyeknél szállításra.

    ![Az Azure disk előkészítése befejeződött](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. A parancs végrehajtásának végén is láthatja a lehetőséget, szállítási információkat alább látható módon:

    ![Szállítási adatok beállítás frissítése](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. A részleteket adhat meg azonnal. Az eszköz végigvezeti a folyamat eseteken bemenetei között. Azonban ha nem rendelkezik további információkat, például nyomon követése, szám vagy egyéb szállítási kapcsolatos részleteket, fejezheti be a munkamenet. Szállítási adatok később frissíteni a lépéseket ebben a cikkben találhatók. 

6. Az eszköz a megadott címre szállítsa és az a későbbiekben a nyomkövetési azonosító szám.

   > [!IMPORTANT] 
   > Nincs két Azure importálási feladat a azonos nyomkövetési azonosító szám lehet. Győződjön meg arról, hogy meghajtók, a segédprogram alatt egy Azure importálási feladat által készített szállítják együtt, egyetlen csomagban, és hogy van-e a csomag egyetlen egyedi követési szám. Ne vonja össze a meghajtókat részeként készített **különböző** Azure importálási feladatokhoz, egyetlen csomagban. 

5. Ha rendelkezik a követési kapcsolatos információk, nyissa meg a forrásoldali számítógép, amely az importálási feladat befejezésére vár, és futtassa a következő parancsot egy rendszergazda jogú parancssort a *AzureOfflineBackupDiskPrep* segédprogram könyvtárat a aktuális címtár: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Igény szerint futtathatja a következő parancsot egy másik számítógépről például a *másolási számítógép*, a *AzureOfflineBackupDiskPrep* segédprogram könyvtárba az aktuális könyvtárban:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Paraméter | Leírás |
    | --- | --- |
    | u: | Kötelező bemeneti frissítheti a szállítási adatait az Azure importálási feladat |
    | %s:&lt;*átmeneti hely elérési útja*&gt; | Kötelező bemeneti, ha a parancs nem fut a forrásoldali számítógépen. Az átmeneti helyen, a megadott elérési útja biztosítható a **offline biztonsági mentés kezdeményezéséhez** munkafolyamat. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; | Kötelező bemeneti, ha a parancs nem fut a forrásoldali számítógépen. Biztosítja a elérési útját a **Azure közzétételi beállítások** a megadott fájl a **offline biztonsági mentés kezdeményezéséhez** munkafolyamat. |
    
    A segédprogram automatikusan észleli az importálási feladatot, amely a forrásoldali számítógép vár a, vagy az importálási feladatokhoz társított az átmeneti helyen, a parancs egy másik számítógépen való futtatásakor. Ezután lehetővé teszi a bemenetek végigvezeti a szállítási adatait frissíteni a lent látható módon: 
    
    ![Szállítási adatok megadása](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Miután az összes bemeneti vannak megadva, gondosan tekintse át a részleteket, és véglegesítse a szállítási adatok írja be a megadott *Igen*. 

    ![Tekintse át a szállítási adatok](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. A szállítási adatok sikeresen frissítéseket, a segédprogram is kínál az Ön által megadott szállítási adatok tárolására alább látható módon egy helyi helyre 

    ![Szállítási adatok tárolása](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Győződjön meg arról, hogy a meghajtó eléri-e az Azure-adatközpontban, így a szállítási címhez tartozó az adatokat a két héten belül a *AzureOfflineBackupDiskPrep* segédprogramot. Ezt a meghajtók nem dolgozza fel a eredményezhet.  

Miután elvégezte a fenti lépéseket, a meghajtók fogadni és további feldolgozni azokat a biztonsági mentési adatok átvitele a meghajtók klasszikus típusú Azure storage-fiókban létrehozott készen áll az Azure-adatközpontban. 

### <a name="time-to-process-the-drives"></a>A meghajtók folyamatnak 
Mennyi ideig tart a folyamat különféle tényezők, például a szállítási időt, függően változik, az Azure importálási feladat feladat-típus, típusa és az adatok másolását mérete és a megadott lemezek mérete. Az Azure Import/Export szolgáltatás nem biztosítunk SLA-t, de a lemezek vannak fogadását követően a szolgáltatás nagy hangsúlyt fektet a 7-10 napra végezze el a biztonsági mentési adatok másolása az Azure storage-fiókba. Ez a szakasz ismerteti az Azure-beli importálási feladat állapotának monitorozásához. 

### <a name="monitoring-azure-import-job-status"></a>Az Azure importálási feladat állapotának figyelése
A meghajtókat, amelyek közül az átvitel során, vagy az Azure-adatközpontban kell másolni a storage-fiókot, az Azure Backup ügynököt vagy SC DPM vagy az Azure Backup server konzol a forrásoldali számítógépen állapotát jeleníti meg a következő feladat a ütemezett biztonsági mentésekhez. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Importálási feladat állapotát az alábbi lépésekkel. 
1. Nyissa meg a forrásoldali számítógépen egy rendszergazda jogú parancssort, és futtassa a következő parancsot:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  A kimenet az importálási feladat aktuális állapotát jeleníti meg az alább látható módon: 

    ![Importálási feladat állapotának ellenőrzése](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

A különböző állapotok az Azure importálási feladat további információkért lásd: [Ez a cikk](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Fejezze be a munkafolyamat
Az importálási feladat befejezése után a tárfiókban lévő kezdeti biztonsági mentési adatok érhető el. A következő ütemezett biztonsági mentés idején az Azure backup másol a adatok tartalma a storage-fiókot a Recovery Services-tároló alább látható módon: 

   ![Recovery Services-tárolóba másolhatja az adatokat](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

A következő ütemezett biztonsági mentés idején az Azure Backup végrehajtja a növekményes biztonsági mentés a kezdeti biztonsági másolatot.

## <a name="next-steps"></a>További lépések
* Kérdései vannak az Azure Import/Export a munkafolyamat, tekintse meg [a Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob storage](../storage/common/storage-import-export-service.md).
* Az Azure Backup offline biztonsági mentés szakaszában [– gyakori kérdések](backup-azure-backup-faq.md) bármilyen kérdése van, arról a munkafolyamatról.
