---
title: Az Azure Backup – online biztonsági mentést vagy a kezdeti beültetés az Azure Import/Export szolgáltatás használata
description: Ismerje meg, hogyan Azure Backup lehetővé teszi, hogy a hálózatról az Azure Import/Export szolgáltatással adatokat. Ez a cikk ismerteti, hogy a kapcsolat nélküli beültetés a kezdeti biztonsági mentési adatok az Azure importálás exportálás szolgáltatás használatával.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: 9d91ccd04ed06fb6c256a2d9911202d7df6d08a5
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188300"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Offline biztonsági mentési munkafolyamat az Azure Backupban
Az Azure Backup rendelkezik, amely a hálózati és tárolási költségek csökkentése az Azure-ban adatok kezdeti teljes biztonsági mentés során számos beépített hatékonyság. Kezdeti teljes biztonsági mentés általában nagy mennyiségű adat átvitele, és azt követő biztonsági mentéseket, hogy csak a változásokat/szalagnak transfer képest nagyobb hálózati sávszélesség szükséges. Kapcsolat nélküli beültetéssel is a folyamatot az Azure Backup lemez is használható az offline biztonsági mentési adatok feltöltése az Azure-bA.

Az Azure biztonsági mentési folyamat offline áttöltést szorosan integrálva van a [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md) , amely lehetővé teszi, hogy a kezdeti biztonsági mentési adatok átvitele az Azure-bA lemezek használatával. Ha nagy késésű és alacsony sávszélességű hálózaton keresztül kell átvinni kezdeti biztonsági mentési adatokat terabájt (TB-osra bővül), használhatja a kapcsolat nélküli beültetéssel is munkafolyamat tehetnek a kezdeti biztonsági másolatot, legalább egy rögzített meghajtók, egy Azure-adatközpontba. Az alábbi képen a munkafolyamat lépései áttekintést nyújt.

  ![offline importálási munkafolyamat folyamatának áttekintése](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Az offline biztonsági mentési folyamat az alábbi lépésekből áll:

1. Helyett a biztonsági mentési adatokat küld a hálózaton keresztül, egy átmeneti helyre a biztonsági mentési adatokat írni.
2. A AzureOfflineBackupDiskPrep segédprogram használatával szeretne adatokat írni az átmeneti helyen egy vagy több SATA lemezek.
3. Az előkészítő munka részeként a AzureOfflineBackupDiskPrep segédprogram az Azure importálási feladat hoz létre. Küldés a SATA meghajtókat a legközelebbi Azure-adatközpontba, és az importálási feladatokhoz való csatlakozáshoz a tevékenységek hivatkozni.
4. Az Azure adatközpont, az adatokat a lemezen másolja az Azure storage-fiókba.
5. Az Azure Backup biztonsági mentési adatok átmásolja a storage-fiókot a Recovery Services-tárolót, és a növekményes biztonsági mentés van ütemezve.

## <a name="supported-configurations"></a>Támogatott konfigurációk 
A következő Azure Backup szolgáltatásai, vagy a számítási feladatok támogatja az Offline biztonsági mentés használatát.

> [!div class="checklist"]
> * Biztonsági mentési fájlok és mappák, a Microsoft Azure Recovery Services-(MARS) ügynökkel, más néven az Azure Backup ügynököt. 
> * Az összes számítási feladatok és a System Center Data Protection Manager (SC DPM) fájlok biztonsági mentése 
> * A Microsoft Azure Backup Server számítási feladatok és a fájlok biztonsági mentése <br/>

   > [!NOTE]
   > Offline biztonsági mentés nem támogatott a rendszerállapot biztonsági másolatokat az Azure Backup ügynök használatával történik. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Előfeltételek

  > [!NOTE]
  > A következő előfeltételek és a alkalmazni csak Offline fájlok és mappák biztonsági másolat a [MARS-ügynök legújabb](https://aka.ms/azurebackup_agent). Offline biztonsági mentés végrehajtásához a System Center DPM vagy az Azure Backup Server számítási feladatok esetében, tekintse meg [Ez a cikk](backup-azure-backup-server-import-export-.md). 

Mielőtt elindítaná az Offline Backup munkafolyamat, hajtsa végre a következő előfeltételek vonatkoznak: 
* Hozzon létre egy [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md). Hozzon létre egy tárolót, tekintse meg a lépéseket a [Ez a cikk](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Győződjön meg arról, hogy csak a [az Azure Backup szolgáltatás ügynökének legújabb verzióját](https://aka.ms/azurebackup_agent) telepítve van a Windows Server vagy Windows-ügyfél, megfelelő és a számítógép regisztrálva van a Recovery Services-tárolóval.
* Az Azure PowerShell 3.7.0 Azure Backup ügynököt futtató számítógépen szükséges. Azt Javasoltjuk, le kell tölteni és [az Azure PowerShell telepítése az 3.7.0 verzióját](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Azure Backup ügynököt futtató számítógépen győződjön meg arról, hogy a Microsoft Edge vagy az Internet Explorer 11 van telepítve, és a JavaScript engedélyezve van. 
* Hozzon létre egy Azure Storage-fiókot és a Recovery Services-tárolónak ugyanabban az előfizetésben. 
* Ellenőrizze, hogy a [szükséges engedélyek](../active-directory/develop/howto-create-service-principal-portal.md) az Azure Active Directory-alkalmazás létrehozásához. Az Offline Backup munkafolyamat az Azure Active Directory-alkalmazás az Azure Storage-fiókhoz társított előfizetést hoz létre. Az alkalmazás az a célja, hogy az Azure Backup hatókörrel és a biztonságos hozzáférést biztosíthat az Azure importálási szolgáltatásba, az Offline biztonsági mentés munkafolyamathoz szükséges. 
* A Microsoft.ImportExport erőforrás-szolgáltató regisztrálása az előfizetéshez, amely tartalmazza az Azure Storage-fiók. Az erőforrás-szolgáltató regisztrálása:
    1. A fő menüjében kattintson **előfizetések**.
    2. Ha több előfizetéssel előfizetett, válassza ki az előfizetést, az offline biztonsági mentés használ. Ha csak egy előfizetést használ, akkor az előfizetés jelenik meg.
    3. Az előfizetés menüjében kattintson **erőforrás-szolgáltatók** szolgáltatók listájának megtekintéséhez.
    4. A szolgáltatók görgessen le a Microsoft.ImportExport listájában. Ha az állapot NotRegistered, kattintson a **regisztrálása**.
    ![az erőforrás-szolgáltató regisztrálása](./media/backup-azure-backup-import-export/registerimportexport.png)
* Egy előkészítési helyet, amely lehet egy hálózati megosztásra vagy bármely további meghajtó a számítógépen, belső vagy külső, a kezdeti másolat tárolásához elegendő lemezterület a jön létre. Például egy 500 GB-os fájlt kiszolgálóról biztonsági mentést próbál, ha arról, hogy az átmeneti területen legalább 500 GB-os. (A tömörítés miatt kisebb használt.)
* Amikor lemezeket küld az Azure-ba, használja csak 2,5 hüvelyk SSD, vagy 2,5-es vagy 3,5 hüvelykes SATA II. és III belső merevlemez-meghajtókat. Használhatja a merevlemez-meghajtók legfeljebb 10 TB. Ellenőrizze a [Azure Import/Export szolgáltatás dokumentációja](../storage/common/storage-import-export-requirements.md#supported-hardware) meghajtó, amely a szolgáltatás támogatja a legújabb csoporton.
* A SATA meghajtókat kapcsolódnia kell a számítógép (néven egy *másolási számítógép*), ahonnan a biztonsági mentési adatok másolatát a *előkészítési helyét* , a SATA meghajtókat történik. Győződjön meg arról, hogy a Bitlocker engedélyezve van a *másolási számítógép*.

## <a name="workflow"></a>Munkafolyamat
Ez a szakasz ismerteti az offline biztonsági mentési munkafolyamat, így az adatok is lehet az Azure-adatközpont és az Azure Storage-bA feltöltött. Ha kérdései vannak az importálási szolgáltatás vagy a folyamat minden aspektusa, tekintse meg a [importálása a service áttekintése dokumentációja](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Offline biztonsági mentés kezdeményezéséhez
1. Amikor beállít egy biztonsági másolatot a MARS-ügynök, az alábbi képernyőt látja.

    ![Importálási képernyőn](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

  A leírás a bemeneti adatok a következőképpen történik:

    * **Átmeneti hely**: Az ideiglenes tárolási hely, amelyhez a kezdeti biztonsági másolatot íródik. Átmeneti hely lehet egy hálózati megosztásra vagy a helyi számítógépen. Ha a másolási számítógép és a forrásoldali számítógép eltérő, javasoljuk, hogy a teljes elérési útja az előkészítési hely megadása.
    * **Az Azure Resource Manager-Tárfiókot**: A Resource Manager típusú bármely Azure-előfizetésében lévő tárfiók neve.
    * **Az Azure Storage-tároló**: A cél storage-blobból az Azure Storage-fiók, amelybe importálják a biztonsági mentési adatok másolását a Recovery Services-tároló előtt a neve.
    * **Azure-előfizetés azonosítója**: Ha az Azure Storage-fiók létrehozása az Azure-előfizetés azonosítója.
    * **Azure-beli importálási feladat neve**: Az egyedi név szerint mely Azure Import szolgáltatás és az Azure Backup nyomon követése az elküldött adatok átvitelét az lemezeket az Azure-bA. 
  
  Adja meg a bemeneti adatok a képernyőn, és kattintson a **tovább**. Mentse a megadott *átmeneti hely* és a *Azure importálási feladat nevének*, ahogy ezt az információt kötelező megadni a lemezek előkészítéséhez.

2. Amikor a rendszer kéri, jelentkezzen be az Azure-előfizetésében. Jelentkezzen be, hogy az Azure Backup az Azure Active Directory-alkalmazás létrehozása, és adja meg az Azure importálási szolgáltatás eléréséhez szükséges engedélyekkel.

    ![Biztonsági másolat készítése](./media/backup-azure-backup-import-export/azurelogin.png)

3. A munkafolyamat befejezéséhez, majd kattintson az Azure Backup-ügynök konzolján **azonnali biztonsági mentés**.

    ![Biztonsági másolat készítése](./media/backup-azure-backup-import-export/backupnow.png)

4. A varázsló a megerősítő oldalon kattintson a **biztonsági mentése**. A kezdeti biztonsági mentés az átmeneti területre a telepítés részeként van megírva.

   ![Győződjön meg arról, hogy készen áll a biztonsági mentés most](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    A művelet befejezése után az előkészítési hely készen áll a lemez előkészítéséhez használható.

   ![Biztonsági másolat készítése](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA meghajtókat előkészítjük, és elküldjük az Azure-bA
A *AzureOfflineBackupDiskPrep* segédprogram előkészíti a SATA meghajtókat a legközelebbi Azure-adatközpontba küldött. Ez a segédprogram az Azure Backup agent telepítési könyvtárában (a következő elérési út) érhető el:

   *\Microsoft azure Recovery Services Agent\Utils\\*

1. Nyissa meg a könyvtárat, másolja a **AzureOfflineBackupDiskPrep** könyvtárat, ahol a SATA meghajtókat csatlakoznak egy másik számítógépre. A számítógépen, amelyen a csatlakoztatott SATA meghajtókat biztosítja:

    * A másolási számítógép érhető el az előkészítési hely offline áttöltést munkafolyamat hálózati elérési útját lett megadva a **offline biztonsági mentés kezdeményezéséhez** munkafolyamat.
    * A BitLocker engedélyezve van a Másolás számítógépen.
    * Az Azure PowerShell 3.7.0 telepítve van.
    * A legújabb kompatibilis böngészők (a Microsoft Edge vagy az Internet Explorer 11) van telepítve, és a JavaScript engedélyezve van. 
    * A másolási számítógép hozzáférhessen az Azure Portalon. Szükség esetén a másolási számítógép lehet ugyanaz, mint a forrásszámítógépen.
    
    > [!IMPORTANT] 
    > Ha a forrásoldali számítógép egy virtuális gépet, majd a Másolás számítógép kell egy másik fizikai kiszolgáló vagy ügyfélgép a forrás számítógépről.

2. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen, amelyen a *AzureOfflineBackupDiskPrep* segédprogram könyvtárba az aktuális könyvtárban, majd futtassa a következő parancsot:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Paraméter | Leírás |
    | --- | --- |
    | %s:&lt;*átmeneti hely elérési útja*&gt; |Kötelező bemeneti biztosítható az átmeneti helyen, a megadott elérési útja a **offline biztonsági mentés kezdeményezéséhez** munkafolyamat. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; |Nem kötelező bemenő, amellyel elérési útját adja meg a **Azure közzétételi beállítások** a megadott fájl a **offline biztonsági mentés kezdeményezéséhez** munkafolyamat. |

    A parancs futtatásakor a a segédprogram az Azure Import-feladattal, amely megfelel a meghajtók, elő kell készíteni a kijelölt kérések. Ha csak egy egyetlen importálási feladat a megadott átmeneti hely társítva, akkor a következő egy hasonló képernyő jelenik meg.

    ![Az Azure lemez előkészítő eszköz bemenet](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Adja meg a záró kettőspont nélkül a meghajtó betűjelét a csatlakoztatott lemezt, amelyet szeretne előkészítése az átvitelre az Azure-bA. 
4. A formázáshoz a meghajtó, amikor a rendszer kéri, erősítse meg a műveletet.
5. Jelentkezzen be az Azure-előfizetés kéri. Adja meg a hitelesítő adatokat.

    ![Az Azure lemez előkészítő eszköz bemenet](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Az eszköz ezután elkezdi előkészítéséhez, a lemez és a biztonsági mentési adatok másolását. Szükség lehet további lemezt, amikor a rendszer kéri, az eszköz abban az esetben, ha a megadott lemezen nincs elég hely a biztonsági mentési adatok tárolására. <br/>

    Az eszköz sikeres végrehajtásának végén a parancssor használatával nyújt három információt:
    1. A megadott egy vagy több lemez készen áll a szállítási címhez tartozó Azure-bA. 
    2. Megkapta a megerősítést, hogy létrejött-e az importálási feladat. Az importálási feladat a megadott nevét használja.
    3. Az eszköz megjeleníti a szállítási cím az az Azure-adatközpontba.

    ![Az Azure disk előkészítése befejeződött](./media/backup-azure-backup-import-export/console2.png)<br/>

6. A parancs végrehajtásának végén a kézbesítési frissítheti.

7. Az eszköz a megadott címre szállítsa és az a későbbiekben a nyomkövetési azonosító szám.

   > [!IMPORTANT] 
   > Nincs két Azure importálási feladat a azonos nyomkövetési azonosító szám lehet. Győződjön meg arról, hogy meghajtók, a segédprogram alatt egy Azure Import-feladat által készített szállítják együtt, egyetlen csomagban, és hogy van-e a csomag egyetlen egyedi követési szám. Ne vonja össze a meghajtókat, egyetlen csomagban külön Azure Import-feladatok részeként készített.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Az Azure importálási feladat naplóküldése részleteinek frissítése

Az alábbi eljárás frissít az Azure importálási feladat naplóküldése részleteit. Ezt az információt kapcsolatos részleteket tartalmazza:
* a szolgáltató számára továbbítja az Azure a lemezek nevét
* a lemezek szállítását részleteit adja vissza

1. Jelentkezzen be az Azure-előfizetéshez.
2. A fő menüjében kattintson **minden szolgáltatás** és az összes szolgáltatás párbeszédpanelen írja be az importálási. Amikor látja **importálási/exportálási feladatok**, kattintson rá.
    ![Szállítási adatok megadása](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Listájának **importálási/exportálási feladatok** menü megnyílik, és megjelenik a lista összes importálási/exportálási feladat a kijelölt előfizetésben. 

3. Ha több előfizetéssel rendelkezik, mindenképpen jelölje ki a biztonsági mentési adatok importálásához használt előfizetés. Ezután válassza ki az újonnan létrehozott importálási feladat megnyissa annak részletes adatait.

    ![Tekintse át a szállítási adatok](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Kattintson a beállítások menüben az importálási feladat **visszaszállítási adatok kezelése** , és adja meg a visszaszállítási adatok megadása.

    ![Szállítási adatok tárolása](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Ha a szállítási szolgáltató a nyomkövetési azonosító szám, kattintson a szalagcímre az Azure importálási feladat Áttekintés lapon, és adja meg a következő adatokat:

   > [!IMPORTANT] 
   > Győződjön meg arról, hogy a szolgáltató információkat és követési szám frissítve lett az Azure-beli importálási feladat létrehozása két héten belül. Ellenőrizze az adatokat két héten belül nem sikerült a feladat törlése folyamatban van, és nem dolgozza fel a meghajtók eredményezhet.

   ![Szállítási adatok tárolása](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Szállítási adatok tárolása](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>A meghajtók folyamatnak 
Mennyi ideig tart a folyamat különféle tényezők, például a szállítási időt, függően változik, az Azure importálási feladat feladat-típus, típusa és az adatok másolását mérete és a megadott lemezek mérete. Az Azure Import/Export szolgáltatás nem biztosítunk SLA-t, de a lemezek vannak fogadását követően a szolgáltatás nagy hangsúlyt fektet a 7-10 napra végezze el a biztonsági mentési adatok másolása az Azure storage-fiókba. 

### <a name="monitoring-azure-import-job-status"></a>Az Azure importálási feladat állapotának figyelése
Figyelheti az Azure-portálról az importálási feladat állapotának a **importálási/exportálási feladatok** lapot, és ki a feladatot. Importálási feladat állapotát további információkért lásd: a [Storage Import exportálása szolgáltatás](../storage/common/storage-import-export-service.md) cikk.

### <a name="complete-the-workflow"></a>Fejezze be a munkafolyamat
Az importálási feladat sikeres befejezése után a tárfiókban lévő kezdeti biztonsági mentési adatok érhető el. A következő ütemezett biztonsági mentés idején Azure Backup másol a adatok tartalma a storage-fiókot a Recovery Services-tároló alább látható módon: 

   ![Recovery Services-tárolóba másolhatja az adatokat](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

A következő ütemezett biztonsági mentés idején Azure Backup hajt végre a növekményes biztonsági mentés.

### <a name="cleaning-up-resources"></a>Erőforrások törlése
A kezdeti biztonsági mentés befejezése után nyugodtan törölheti az adatok importálása az Azure Storage-tárolót és a biztonsági mentési adatok átmeneti helyen.

## <a name="next-steps"></a>További lépések
* Kérdései vannak az Azure Import/Export a munkafolyamat, tekintse meg [a Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob storage](../storage/common/storage-import-export-service.md).
* Az Azure Backup offline biztonsági mentés szakaszában [– gyakori kérdések](backup-azure-backup-faq.md) bármilyen kérdése van, arról a munkafolyamatról.
