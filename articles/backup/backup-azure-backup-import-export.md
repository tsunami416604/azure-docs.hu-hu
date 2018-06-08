---
title: Az Azure Backup - Offline biztonsági másolat vagy a kezdeti összehangolása az Azure Import/Export szolgáltatás használata
description: Ismerje meg, hogyan Azure Backup szolgáltatás lehetővé teszi az Azure Import/Export szolgáltatás használata a hálózati adatokat küldeni. Ez a cikk ismerteti a kezdeti biztonsági másolati adatokat kapcsolat nélküli összehangolása az Azure importálási exportálása szolgáltatás használatával.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: 5ef44ccf87bc5e40b57dc7fc997c9a827c93484b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831455"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Offline biztonsági mentési munkafolyamat az Azure Backupban
Azure biztonsági mentés számos beépített hatékonyság, hálózati és tárolási költségek csökkentése során a kezdeti teljes biztonsági mentés az Azure-bA rendelkezik. Kezdeti teljes biztonsági mentés általában nagy adatmennyiségek átvitelét, és azt követő biztonsági mentéseket, amely csak az eltérések/növekményes átviteli képest nagyobb hálózati sávszélesség szükséges. A folyamatot, amely offline összehangolása Azure biztonsági mentési lemezek is használhatók az offline biztonsági mentési adatokat feltölteni az Azure-bA.

Az Azure biztonsági mentési folyamat offline összehangolása szorosan integrálva van a [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md) , amely lehetővé teszi a kezdeti biztonsági mentési adatok átvitele Azure lemezek használatával. Ha terabájt (több TB-nyi), amelyet a nagy késleltetésű és kis sávszélességű hálózaton keresztül kell átvinni kezdeti biztonsági másolati adatokat, a kapcsolat nélküli összehangolása munkafolyamat segítségével küldje el a kezdeti biztonsági másolatot, legalább egy merevlemezeken, hogy egy Azure-adatközpontban. Az alábbi képen a munkafolyamat lépéseinek áttekintése.

  ![offline importálási munkafolyamat folyamatának áttekintése](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Az offline biztonsági mentési folyamat a következő lépésekből áll:

1. A biztonsági mentési adatok küldése a hálózaton keresztül, helyett egy átmeneti helyre a biztonsági mentési adatokat írni.
2. A AzureOfflineBackupDiskPrep segédprogrammal az átmeneti helyen egy vagy több SATA lemezek kell írni.
3. Az előkészítő munka részeként a AzureOfflineBackupDiskPrep segédprogram hoz létre egy Azure importálási feladattal. A SATA meghajtókat küldeni a legközelebbi Azure-adatközpontban, és a tevékenységek csatlakozni az importálási feladat hivatkozik.
4. Az Azure adatközpontjában az Azure-tárfiók átmásolja az adatokat a lemezeken.
5. Az Azure Backup a biztonsági mentési adatok átmásolja a tárfiók a Recovery Services-tároló, és a növekményes biztonsági mentés van ütemezve.

## <a name="supported-configurations"></a>Támogatott konfigurációk 
A következő Azure biztonsági mentés vagy munkaterhelések támogatja az Offline biztonsági mentés.

> [!div class="checklist"]
> * A fájlok és mappák, valamint a Microsoft Azure Recovery Services (MARS) ügynök, az Azure Backup szolgáltatás ügynöke más néven a biztonsági mentés. 
> * Az összes fájlt a System Center Data Protection Manager (SC DPM) és munkaterhelések biztonsági mentése 
> * A Microsoft Azure Backup Server munkaterhelések és a fájlok biztonsági mentése <br/>

   > [!NOTE]
   > Offline biztonsági másolat nem támogatott a rendszerállapot biztonsági mentéshez az Azure Backup-ügynök használatával történik. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Előfeltételek

  > [!NOTE]
  > A következő előfeltételek és a munkafolyamat alkalmazása csak Offline fájlok és mappák a biztonsági másolat a [MARS agent legújabb](https://aka.ms/azurebackup_agent). Offline biztonsági mentéshez a System Center DPM vagy az Azure Backup Server munkaterhelésekhez, tekintse meg [Ez a cikk](backup-azure-backup-server-import-export-.md). 

Az Offline biztonsági másolat munkafolyamat kezdeményezése, előtt hajtsa végre a következő előfeltételek teljesülését: 
* Hozzon létre egy [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md). Hozzon létre egy tárolót, tekintse meg a lépéseket [Ez a cikk](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Győződjön meg arról, hogy csak a [az Azure Backup szolgáltatás ügynökének legújabb verzióját](https://aka.ms/azurebackup_agent) telepítve van a Windows Server és Windows-ügyfél megfelelő és a számítógép regisztrálva van a Recovery Services-tároló.
* Az Azure PowerShell 3.7.0 Azure Backup-ügynököt futtató számítógépen kell végrehajtani. Javasoljuk, hogy töltse le és [Azure PowerShell telepítése a 3.7.0 verziója](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* A számítógépen futó Azure Backup szolgáltatás ügynöke, győződjön meg arról, hogy a Microsoft Edge, illetve az Internet Explorer 11 telepítve van, engedélyezve van a JavaScript használatának. 
* Egy Azure Storage-fiók létrehozása és a Recovery Services-tárolónak ugyanabban az előfizetésben. 
* Győződjön meg arról, hogy a [szükséges engedélyek](../azure-resource-manager/resource-group-create-service-principal-portal.md) az Azure Active Directory-alkalmazás létrehozásához. Az Offline biztonsági másolat munkafolyamat egy Azure Active Directory-alkalmazás az Azure Storage-fiókhoz tartozó előfizetést hoz létre. Az alkalmazás célja az Azure Backup hatókörön belüli és biztonságos hozzáférést biztosíthat a az Azure importálási szolgáltatáshoz, az Offline biztonsági másolat munkafolyamat szükséges. 
* A Microsoft.ImportExport erőforrás-szolgáltató regisztrálása az Azure Storage-fiók tartalmazó előfizetéssel. Az erőforrás-szolgáltató regisztrálása:
    1. A főmenü kattintson **előfizetések**.
    2. Ha több előfizetéssel előfizetett, válassza ki az előfizetést, az offline biztonsági mentés használata. Ha csak egy előfizetés használatára, majd az előfizetés jelenik meg.
    3. Az előfizetés menüjében kattintson **erőforrás-szolgáltató** szolgáltatók listájának megtekintéséhez.
    4. A szolgáltatók görgessen le Microsoft.ImportExport közül. Ha az állapot NotRegistered, kattintson a **regisztrálása**.
    ![az erőforrás-szolgáltató regisztrálása](./media/backup-azure-backup-import-export/registerimportexport.png)
* Egy ideiglenes helyet, amely lehet, hogy egy hálózati megosztásra vagy bármely további meghajtó a számítógépen, belső vagy külső, a kezdeti másolat tárolásához elegendő lemezterülettel rendelkező jön létre. Például ha próbál 500 GB-os fájlkiszolgáló biztonsági mentése, győződjön meg arról, hogy az átmeneti területre legalább 500 GB. (Tömörítés miatt a kisebb méretű használja.)
* Lemezek küld az Azure-ba, csak a 2,5 hüvelyk SSD vagy 2.5-es vagy 3,5 hüvelykes SATA II/III belső merevlemez-meghajtókat használnak. Merevlemez-meghajtókat is használhat legfeljebb 10 TB. Ellenőrizze a [Azure Import/Export szolgáltatás dokumentációja](../storage/common/storage-import-export-requirements.md#supported-hardware) meghajtók, a szolgáltatás által támogatott legújabb készlete esetében.
* A SATA meghajtókat kapcsolódnia kell a számítógép (néven egy *másolási számítógép*), amelyről származó biztonsági mentési adatok másolása az *átmeneti helyre* számára a SATA meghajtókat történik. Győződjön meg arról, hogy a Bitlocker engedélyezve van a *másolási számítógép*.

## <a name="workflow"></a>Munkafolyamat
Ez a szakasz ismerteti az offline biztonsági másolat munkafolyamat, hogy az adatok egy Azure-adatközpontban kézbesített, és Azure Storage feltöltve. Ha az importálás szolgáltatás vagy a folyamat minden szempontját kérdése van, tekintse meg a [szolgáltatás áttekintése dokumentációja importálása](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Offline biztonsági mentése
1. Ha a MARS Agent biztonsági mentés ütemezése, a következő képernyő jelenik meg.

    ![Importálás képernyőn](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

  Leírja, hogy a bemeneti adatok a következőképpen történik:

    * **Átmeneti hely**:, amelyhez a kezdeti biztonsági másolatot írása ideiglenes tárolási helyét. Átmeneti hely lehet egy hálózati megosztásra vagy a helyi számítógépen. A másolási számítógép és a forrásszámítógép nem egyezik, azt javasoljuk, hogy megadja a teljes elérési útja az átmeneti tárolási helyét.
    * **Az Azure Resource Manager tárolási fiókot**: az erőforrás-kezelő típus tárfiók bármely Azure-előfizetés nevét.
    * **Az Azure Storage-tároló**: a rendeltetési tárolási blob az Azure Storage-fiók, ahol a biztonsági mentési adatok importálása előtt másolni szeretné a Recovery Services-tárolónak a neve.
    * **Az Azure előfizetés-azonosító**: az azonosító, ahol az Azure Storage-fiók létrehozása az Azure-előfizetés.
    * **Az Azure importálási feladat nevének**: mely Azure Import alapján szolgáltatás és az Azure Backup követésére küldött adatok továbbítása lemezeken Azure egyedi nevét. 
  
  Adja meg az adatokat a képernyőn, és kattintson a **következő**. Mentse a megadott *átmeneti helyre* és a *Azure importálási feladat nevének*, mert a lemezek való előkészítéséhez szükség.

2. Amikor a rendszer kéri, jelentkezzen be az Azure-előfizetéshez. Be kell jelentkeznie, hogy az Azure biztonsági mentés az Azure Active Directory-alkalmazás létrehozása, és adja meg az Azure importálási szolgáltatás eléréséhez szükséges engedélyekkel.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/azurelogin.png)

3. A munkafolyamat befejezéséhez, majd kattintson az Azure Backup agent konzol **biztonsági másolat készítése most**.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/backupnow.png)

4. Kattintson a Jóváhagyás lap, a varázsló, **készítsen biztonsági másolatot**. A kezdeti biztonsági mentés írása az átmeneti területre, a telepítés részeként.

   ![Győződjön meg arról, hogy készen áll a biztonsági mentés most](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    A művelet befejezése után az ideiglenes helyet készen áll a lemez előkészítéséhez használható.

   ![Biztonsági mentés](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Készítse elő a SATA meghajtókat, és küldje el az Azure-bA
A *AzureOfflineBackupDiskPrep* segédprogram előkészíti a SATA meghajtókat a legközelebbi Azure-adatközpontban küldött. Ez a segédprogram érhető el az Azure Backup agent telepítési könyvtárában (a következő elérési út):

   *\Microsoft azure Recovery Services Agent\Utils\\*

1. Lépjen a könyvtár, másolja a **AzureOfflineBackupDiskPrep** könyvtárhoz, ahol a SATA meghajtókat csatlakoznak egy másik számítógépre. A csatlakoztatott SATA meghajtókat a számítógépen győződjön meg:

    * A másolási számítógép érhető el az ideiglenes helyet a kapcsolat nélküli összehangolása munkafolyamat azonos hálózati elérési útját, amely lett megadva a **kezdeményezni az offline biztonsági másolat** munkafolyamat.
    * A BitLocker engedélyezve van a Másolás számítógépen.
    * Az Azure PowerShell 3.7.0 telepítve van.
    * A legújabb kompatibilis böngészők (az Internet Explorer 11 vagy számított) telepítve vannak, és engedélyezve van a JavaScript használatának. 
    * A másolási számítógép hozzáférhessen az Azure-portálon. Ha szükséges, a másolási számítógép lehet ugyanaz, mint a forrásoldali számítógép.
    
    > [!IMPORTANT] 
    > Ha a forrásszámítógép egy virtuális gépet, majd a Másolás számítógép kell egy másik fizikai kiszolgálón vagy ügyfélszámítógépen. a forrás számítógépről.

2. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen a *AzureOfflineBackupDiskPrep* segédprogram könyvtárba helyezze, mint az aktuális könyvtárra, és futtassa az alábbi parancsot:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Paraméter | Leírás |
    | --- | --- |
    | %s&lt;*átmeneti hely elérési útja*&gt; |Kötelező bemeneti biztosítható a megadott átmeneti tárolási helyének elérési útját a **kezdeményezni az offline biztonsági másolat** munkafolyamat. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; |Nem kötelező bemeneti, amellyel elérési útját adja meg a **Azure közzétételi beállítási** megadott fájl a **kezdeményezni az offline biztonsági másolat** munkafolyamat. |

    A parancs futtatásakor a segédprogram kéri az Azure importálási feladat, amely megfelel a meghajtókat, elő kell készíteni a kijelölést. Ha csak egy egyetlen importálási feladat helyéhez társítva a megadott átmeneti, ahogy az alábbi képernyő jelenik meg.

    ![Az Azure lemez előkészítő eszköz bemeneti](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Adja meg a meghajtóbetűjel, a záró kettőspont nélkül a csatlakoztatott lemezt, amelyet szeretne az Azure-bA átviteli előkészítése. 
4. A meghajtó, amikor a rendszer kéri a formázáshoz erősítse meg a műveletet.
5. Jelentkezzen be az Azure-előfizetéshez kéri. Adja meg a hitelesítő adatokat.

    ![Az Azure lemez előkészítő eszköz bemeneti](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Az eszköz majd megkezdi a lemez és a biztonsági mentési adatok másolása előkészítéséhez. Szükség lehet további lemezeket, amikor a rendszer kéri az eszközzel, abban az esetben, ha a megadott lemezén nincs elég hely a biztonsági mentési adatok csatlakoztatni. <br/>

    Az eszköz sikeres végrehajtásának végén a parancssor három adatra biztosítja:
    1. Megadott egy vagy több lemezt a szállítási az Azure-bA készítenek. 
    2. Az importálási feladat létrejött megerősítő üzenet. Az importálási feladat a megadott nevet használja.
    3. Az eszköz jelenít meg a szállítási cím az az Azure-adatközpontban.

    ![Teljes Azure lemez előkészítése](./media/backup-azure-backup-import-export/console2.png)<br/>

6. A parancs végrehajtásának végén frissítheti a szállítási adatokat.

7. Küldje el a lemezeket, az eszköz a megadott címre, és a jövőben követési számának.

   > [!IMPORTANT] 
   > Két Azure importálási feladat követési ugyanannyi rendelkezhet. Győződjön meg arról, hogy egyetlen Azure importálási feladat alatt az eszköz által készített meghajtók szállítják együtt, egyetlen csomag, és hogy van-e a csomag egyetlen egyedi nyilvántartási szám. Ne vonja össze a csomagban külön Azure importálási feladat részeként készített meghajtókat.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Az Azure importálási feladatnak a szállítási részleteinek frissítése

Az alábbi eljárás frissíti az Azure importálási feladat naplóküldése részleteit. Ez az információ tartalmazza a kapcsolatban:
* a szolgáltatói, akik a lemezek kínál az Azure neve
* térjen vissza a lemezek szállítási részletei

1. Jelentkezzen be az Azure-előfizetéshez.
2. Kattintson a főmenühöz tartozik, **minden szolgáltatás** összes szolgáltatások párbeszédpanelen írja be az importálási. Amikor látja **importálási/exportálási feladatok**, kattintson rá.
    ![Szállítási adatok megadása](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    A listája **importálási/exportálási feladatok** menü nyílik meg, és a kijelölt előfizetés összes importálási/exportálási feladatok listáját. 

3. Ha több előfizetéssel rendelkezik, ügyeljen arra, hogy válassza ki az előfizetést, a biztonsági mentési adatok történő importálásához. Ezután válassza ki az újonnan létrehozott importálási feladat nyissa meg a hozzá tartozó részletek.

    ![Tekintse át a szállítási adatok](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Az importálási feladatnak a beállítások menüben kattintson a **szállítási információ kezelése** és adja meg a visszatérési szállítási részleteit.

    ![Szállítási adatok tárolása](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Ha a szállítási szolgáltatónként a követési számát, kattintson a szalagcím az Azure importálási feladat áttekintése lapon, és adja meg a következő adatokat:

   > [!IMPORTANT] 
   > Győződjön meg arról, hogy a vivőjel-információkat és követési szám frissítve lett, az Azure importálási feladat létrehozása két héten belül. Ez az információ két héten belül ellenőrzésének sikertelensége a törölni kívánt feladatot, és nem dolgozza fel a meghajtók eredményezhet.

   ![Szállítási adatok tárolása](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Szállítási adatok tárolása](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>A meghajtók feldolgozási időtartama 
Mennyi ideig tart a folyamat, például a szállítási idő különböző tényezőktől függően változik az Azure importálási feladat sikertelen feladat-típus, típusa és másolásának adatok méretétől és a megadott lemez méretét. Az Azure Import/Export szolgáltatás nem rendelkezik egy SLA-t, de a lemezek fogadása után a szolgáltatás nagy hangsúlyt fektet 7 – 10 nap végezze el a biztonsági mentési adatok másolása az Azure storage-fiókjába. 

### <a name="monitoring-azure-import-job-status"></a>Azure importálási feladat állapotának figyelése
Navigáljon az Azure-portálról importálási feladat állapotának figyelheti a **importálási/exportálási feladatok** lap, és ki a feladatot. Az importálási feladatok állapotát további információkért lásd: a [tárolási importálás exportálás szolgáltatás](../storage/common/storage-import-export-service.md) cikk.

### <a name="complete-the-workflow"></a>A munkafolyamat befejezése
Ha az importálási feladat sikeresen befejeződött, a tárfiókban lévő kezdeti biztonsági másolati adatokat érhető el. A következő ütemezett biztonsági mentés idején Azure biztonsági mentési átmásolja az adatokat a tartalmát a tárfiók a Recovery Services-tároló alább látható módon: 

   ![Adat másolása az Recovery Services-tároló](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

A következő ütemezett biztonsági mentés idején Azure Backup szolgáltatás hajtja végre a növekményes biztonsági mentés.

### <a name="cleaning-up-resources"></a>Erőforrások törlése
Ha befejeződött a kezdeti biztonsági másolatot, az adatok importálása az Azure-tárolót, és az átmeneti helyre a biztonsági mentési adatok biztonságosan törölhető.

## <a name="next-steps"></a>További lépések
* Az Azure Import/Export munkafolyamat kapcsolatos kérdéseket, tekintse meg a [adatok átviteléhez a Blob storage a Microsoft Azure Import/Export szolgáltatás használata](../storage/common/storage-import-export-service.md).
* Tekintse át az offline biztonsági másolat részt, az Azure biztonsági mentési [gyakran ismételt kérdések](backup-azure-backup-faq.md) a munkafolyamat kapcsolatos kérdéseivel.
