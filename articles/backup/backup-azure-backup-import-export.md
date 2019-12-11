---
title: Offline biztonsági mentés az importálási/exportálási szolgáltatással
description: Ismerje meg, hogy a Azure Backup lehetővé teszi, hogy az Azure import/export szolgáltatással a hálózatról küldjön adatküldést. Ez a cikk ismerteti a kezdeti biztonsági mentési információknak az Azure import export szolgáltatással történő offline előkészítését.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 47d4c4fb63c2aa0e2944456048b06070e235f012
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997360"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Offline biztonsági mentési munkafolyamat az Azure Backupban

A Azure Backup számos beépített hatékonyságot biztosít, amelyek a hálózati és tárolási költségeket az Azure-ba irányuló összes adat kezdeti teljes biztonsági mentése során megtakarítják. A kezdeti teljes biztonsági mentések általában nagy mennyiségű adat átvitelét igénylik, és nagyobb hálózati sávszélességet igényelnek, ha az azokat követő biztonsági mentések során csak a különbözeteket/növekményeket továbbítják. Az offline előkészítési folyamat során a Azure Backup lemezek használatával töltheti fel az offline biztonsági mentési adatok az Azure-ba.

A Azure Backup offline előkészítési folyamat szorosan integrálva van az [Azure import/export szolgáltatással](../storage/common/storage-import-export-service.md) , amely lehetővé teszi a kezdeti biztonsági mentési adatok átvitelét az Azure-ba lemezek használatával. Ha terabájt (TBs) típusú kezdeti biztonsági mentési adat szükséges, amelyet nagy késésű és alacsony sávszélességű hálózaton kell átvinni, az offline előkészítési munkafolyamattal elküldheti a kezdeti biztonsági másolatot egy vagy több merevlemezre egy Azure-adatközpontba. A következő ábra áttekintést nyújt a munkafolyamat lépéseiről.

  ![az offline importálás munkafolyamat-folyamatának áttekintése](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Az offline biztonsági mentési folyamat az alábbi lépéseket foglalja magában:

1. A biztonsági mentési adatok hálózaton keresztüli elküldése helyett a biztonsági mentési adatokat egy átmeneti helyre kell írni.
2. A AzureOfflineBackupDiskPrep segédprogrammal írhatja be az előkészítési helyen található adattípust egy vagy több SATA-lemezre.
3. Az előkészítő munka részeként a AzureOfflineBackupDiskPrep segédprogram létrehoz egy Azure importálási feladatot. Küldje el a SATA-meghajtókat a legközelebbi Azure-adatközpontba, és hivatkozzon az importálási feladatokra a tevékenységek összekapcsolásához.
4. Az Azure-adatközpontban a lemezeken lévő adattárakat egy Azure Storage-fiókba másolja a rendszer.
5. Azure Backup a biztonsági mentési adatokat a Storage-fiókból a Recovery Services-tárolóba másolja, és a növekményes biztonsági mentések ütemezve lesznek.

## <a name="supported-configurations"></a>Támogatott konfigurációk

A következő Azure Backup szolgáltatások vagy munkaterhelések támogatják az offline biztonsági mentés használatát.

> [!div class="checklist"]
>
> * Fájlok és mappák biztonsági mentése a Microsoft Azure Recovery Services-(MARS-) ügynökkel, más néven Azure Backup ügynök.
> * Összes munkaterhelés és fájl biztonsági mentése a System Center Data Protection Manager (SC DPM) szolgáltatással
> * Az összes számítási feladat és fájl biztonsági mentése Microsoft Azure Backup-kiszolgálóval
 
   > [!NOTE]
   > Az offline biztonsági mentés nem támogatott a rendszerállapot biztonsági mentéséhez a Azure Backup ügynök használatával.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Előfeltételek

  > [!NOTE]
  > A következő előfeltételek és munkafolyamatok csak a fájlok és mappák offline biztonsági mentésére vonatkoznak a [legújabb Mars-ügynök](https://aka.ms/azurebackup_agent)használatával. A System Center DPM vagy a Azure Backup Server használatával a számítási feladatokhoz kapcsolódó offline biztonsági mentések végrehajtásához tekintse meg [ezt a cikket](backup-azure-backup-server-import-export-.md).

Az offline biztonsági mentési munkafolyamat elindítása előtt végezze el a következő előfeltételeket:

* Hozzon létre egy [Recovery Services](backup-azure-recovery-services-vault-overview.md)-tárolót. Tár létrehozásához tekintse meg a cikk lépéseit [.](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Győződjön meg arról, hogy csak a [Azure Backup ügynök legújabb verziója](https://aka.ms/azurebackup_agent) van telepítve a Windows Server-vagy Windows-ügyfélre, és a számítógép regisztrálva van a Recovery Services-tárolóban.
* Azure PowerShell 3.7.0 van szükség a Azure Backup ügynököt futtató számítógépen. Javasoljuk, hogy töltse le és [telepítse a Azure PowerShell 3.7.0 verzióját](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* A Azure Backup ügynököt futtató számítógépen ellenőrizze, hogy a Microsoft Edge vagy az Internet Explorer 11 telepítve van-e, és hogy a JavaScript engedélyezve van-e.
* Hozzon létre egy Azure Storage-fiókot a Recovery Services-tárolóval megegyező előfizetésben.
* Győződjön meg arról, hogy rendelkezik a [szükséges engedélyekkel](../active-directory/develop/howto-create-service-principal-portal.md) a Azure Active Directory alkalmazás létrehozásához. Az offline biztonsági mentési munkafolyamat létrehoz egy Azure Active Directory alkalmazást az Azure Storage-fiókhoz társított előfizetésben. Az alkalmazás célja, hogy az offline biztonsági mentési munkafolyamathoz szükséges biztonságos és hatókörrel rendelkező Azure Backup biztosítson az Azure importálási szolgáltatáshoz.
* Regisztrálja a Microsoft. ImportExport erőforrás-szolgáltatót az Azure Storage-fiókot tartalmazó előfizetéssel. Az erőforrás-szolgáltató regisztrálása:
    1. A főmenüben kattintson az **előfizetések**elemre.
    2. Ha több előfizetésre is előfizetett, válassza ki azt az előfizetést, amelyet az offline biztonsági mentéshez használ. Ha csak egy előfizetést használ, megjelenik az előfizetése.
    3. Az előfizetés menüben kattintson az **erőforrás-szolgáltatók** elemre a szolgáltatók listájának megtekintéséhez.
    4. A szolgáltatók listájában görgessen le a Microsoft. ImportExport. Ha az állapot NotRegistered, kattintson a **regisztráció**elemre.
    ![az erőforrás-szolgáltató regisztrálása](./media/backup-azure-backup-import-export/registerimportexport.png)
* A rendszer létrehoz egy átmeneti helyet, amely lehet hálózati megosztás vagy a számítógép belső vagy külső meghajtója, amely elegendő lemezterülettel rendelkezik a kezdeti másolat tárolásához. Ha például egy 500 GB-os fájlkiszolgáló biztonsági mentését kísérli meg, győződjön meg arról, hogy az átmeneti körzet legalább 500 GB. (A tömörítés miatt kisebb mennyiség van használatban.)
* Ha lemezeket küld az Azure-ba, csak 2,5 hüvelykes SSD-t, illetve 2,5 hüvelykes vagy 3,5 hüvelykes SATA II/III merevlemez-meghajtót használjon. A merevlemezeket akár 10 TB-ig is használhatja. Az [Azure import/export szolgáltatás dokumentációjában](../storage/common/storage-import-export-requirements.md#supported-hardware) keresse meg a szolgáltatás által támogatott meghajtók legújabb készletét.
* A SATA-meghajtóknak csatlakoztatva kell lennie egy számítógéphez (ez a *másolási számítógép*), ahonnan a biztonsági mentési adatok másolata az *átmeneti helyről* a SATA-meghajtókra történik. Győződjön meg arról, hogy a BitLocker engedélyezve van a *másolási számítógépen*.

## <a name="workflow"></a>Munkafolyamat

Ez a szakasz az offline biztonsági mentési munkafolyamatot ismerteti, hogy az adatai továbbíthatók legyenek egy Azure-adatközpontba, és fel legyenek töltve az Azure Storage szolgáltatásba. Ha kérdése van az importálási szolgáltatással vagy a folyamat bármely aspektusával kapcsolatban, tekintse meg az [importálási szolgáltatás – áttekintés dokumentációját](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Offline biztonsági mentés indítása

1. Ha a MARS-ügynökön ütemezett biztonsági mentést végez, a következő képernyő jelenik meg.

    ![Importálási képernyő](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   A bemenetek leírása a következő:

    * **Átmeneti hely**: az az ideiglenes tárolási hely, ahová a rendszer a kezdeti biztonsági másolatot írja. Előfordulhat, hogy az előkészítési hely egy hálózati megosztáson vagy egy helyi számítógépen található. Ha a számítógép és a forrásszámítógép másolása eltérő, javasoljuk, hogy az előkészítési hely teljes hálózati elérési útját határozza meg.
    * **Azure Resource Manager Storage-fiók**: a Resource Manager típusú Storage-fiók (általános célú v1 vagy általános célú v2) neve bármely Azure-előfizetésben.
    * **Azure Storage-tároló**: annak az Azure Storage-fióknak a neve, amelybe a biztonsági mentési adatmennyiséget importálja, mielőtt a rendszer átmásolja a Recovery Services-tárolóba.
    * **Azure-előfizetés azonosítója**: annak az Azure-előfizetésnek az azonosítója, amelyhez az Azure Storage-fiókot létrehozták.
    * **Azure importálási feladattípus**: az az egyedi név, amellyel az Azure import service és Azure Backup nyomon követheti a lemezeken továbbított adatok átvitelét az Azure-ba.
  
   Adja meg a bemeneteket a képernyőn, majd kattintson a **tovább**gombra. Mentse a megadott *átmeneti helyet* és az *Azure importálási feladatának nevét*, mivel ezek az információk szükségesek a lemezek előkészítéséhez.

2. Ha a rendszer kéri, jelentkezzen be az Azure-előfizetésbe. Be kell jelentkeznie, hogy Azure Backup létre tudja hozni a Azure Active Directory alkalmazást, és meg kell adnia a szükséges engedélyeket az Azure import szolgáltatás eléréséhez.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/azurelogin.png)

3. Fejezze be a munkafolyamatot, és a Azure Backup ügynök konzolján kattintson a **biztonsági mentés most**lehetőségre.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/backupnow.png)

4. A varázsló Megerősítés lapján kattintson a **biztonsági mentés**gombra. A kezdeti biztonsági mentés a telepítés részeként íródik az átmeneti területre.

   ![Erősítse meg, hogy most már készen áll a biztonsági mentésre](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    A művelet befejeződése után az előkészítési hely készen áll a lemez-előkészítésre.

   ![Biztonsági mentés](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>A SATA-meghajtók előkészítése és a szállítás az Azure-ba

A *AzureOfflineBackupDiskPrep* segédprogram előkészíti a legközelebbi Azure-adatközpontba küldendő SATA-meghajtókat. Ez a segédprogram a Azure Backup Agent telepítési könyvtárában érhető el (a következő elérési úton):

    *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Nyissa meg a könyvtárat, és másolja a **AzureOfflineBackupDiskPrep** könyvtárat egy másik számítógépre, AMELYEN a SATA-meghajtók csatlakoznak. A csatlakoztatott SATA meghajtókkal rendelkező számítógépen ügyeljen a következőre:

   * A másolási számítógép elérheti az offline tesztelési munkafolyamat előkészítési helyét ugyanazzal a hálózati elérési úttal, amelyet az **Offline biztonsági mentési munkafolyamat kezdeményezése** című rész tartalmaz.
   * A BitLocker engedélyezve van a másolási számítógépen.
   * Azure PowerShell 3.7.0 telepítve van.
   * A legújabb kompatibilis böngészők (Microsoft Edge vagy Internet Explorer 11) telepítve vannak, és a JavaScript engedélyezve van.
   * A másolási számítógép elérheti a Azure Portal. Ha szükséges, a másolási számítógép lehet ugyanaz, mint a forrásszámítógép.

     > [!IMPORTANT]
     > Ha a forrásszámítógép virtuális gép, akkor a számítógépnek egy másik fizikai kiszolgálónak vagy ügyfélszámítógépnek kell lennie a forrásszámítógépen.

2. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen a *AzureOfflineBackupDiskPrep* segédprogram címtárával az aktuális könyvtárként, és futtassa a következő parancsot:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Paraméter | Leírás |
    | --- | --- |
    | s:&lt;*átmeneti hely elérési útja*&gt; |A kötelező bemenet, amely a **kapcsolat nélküli biztonsági mentési** munkafolyamatban megadott átmeneti hely elérési útjának megadására szolgál. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; |Opcionális bemenet, amely a **kapcsolat nélküli biztonsági mentési** munkafolyamatban megadott **Azure közzétételi beállítási** fájl elérési útjának megadására szolgál. |

    A parancs futtatásakor a segédprogram kéri az Azure importálási feladatoknak a kiválasztását, amely megfelel az előkészíteni kívánt meghajtóknak. Ha csak egyetlen importálási feladattípus van társítva a megadott átmeneti helyhez, a következőhöz hasonló képernyő jelenik meg.

    ![Azure Disk-előkészítő eszköz bemenete](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Adja meg a meghajtóbetűjelet az Azure-ba való átvitelre előkészíteni kívánt csatlakoztatott lemez záró kettőspontja nélkül.
4. Ha a rendszer kéri, adja meg a meghajtó formázásának megerősítését.
5. A rendszer felszólítja, hogy jelentkezzen be az Azure-előfizetésbe. Adja meg a hitelesítő adatait.

    ![Azure Disk-előkészítő eszköz bemenete](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Az eszköz ezután megkezdi a lemez előkészítését és a biztonsági mentési adatok másolását. Előfordulhat, hogy további lemezeket kell csatlakoztatnia, ha az eszköz erre kéri, ha a megadott lemez nem rendelkezik elegendő hellyel a biztonsági mentési információkhoz. <br/>

    Az eszköz sikeres végrehajtása végén a parancssor három információt nyújt:
   1. Egy vagy több megadott lemez készen áll az Azure-ba történő szállításra.
   2. A rendszer megerősíti, hogy az importálási feladatot létrehozták. Az importálási feladatokhoz a megadott nevet kell használnia.
   3. Az eszköz az Azure-adatközponthoz tartozó szállítási címet jeleníti meg.

      ![Az Azure Disk-előkészítés befejeződött](./media/backup-azure-backup-import-export/console2.png)<br/>

6. A parancs végrehajtásának végén frissítheti a szállítási adatokat.

7. Küldje le a lemezeket az eszköz által biztosított címnek, és tartsa meg a nyomon követési számot későbbi használatra.

   > [!IMPORTANT]
   > Két Azure-beli importálási feladat nem rendelkezhet ugyanazzal a követési számmal. Győződjön meg arról, hogy egyetlen Azure-beli importálási feladatban a segédprogram által készített meghajtók egyetlen csomagban vannak elküldve, és hogy a csomaghoz egyetlen egyedi nyomkövetési szám tartozik. Ne egyesítse a különálló Azure-beli importálási feladatok részeként előkészített meghajtókat egyetlen csomagban.

## <a name="update-shipping-details-on-the-azure-import-job"></a>A szállítási adatok frissítése az Azure importálási feladatban

Az alábbi eljárás az Azure importálási feladat szállítási adatait frissíti. Ez az információ a következőkről tartalmaz részleteket:

* a lemezeket az Azure-ba kézbesítő szállító neve
* a lemezek visszaszállítási adatainak visszaküldése

1. Jelentkezzen be az Azure-előfizetésbe.
2. A főmenüben kattintson a **minden szolgáltatás** elemre, majd a minden szolgáltatás párbeszédpanelen írja be az importálás értéket. Amikor megjelenik az **importálási/exportálási feladatok**, kattintson rá.
    ![szállítási adatok megadása](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Megnyílik az **importálási/exportálási feladatok** menü, és megjelenik a kiválasztott előfizetés összes importálási/exportálási feladatának listája.

3. Ha több előfizetéssel rendelkezik, ügyeljen arra, hogy kiválassza a biztonsági másolatok importálásához használt előfizetést. Ezután válassza ki az újonnan létrehozott importálási feladatot a részletek megnyitásához.

    ![Szállítási információk áttekintése](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Az importálási feladathoz tartozó beállítások menüben kattintson a **szállítási adatok kezelése** lehetőségre, és adja meg a visszaszállítás részleteit.

    ![Szállítási információk tárolása](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Ha a szállítási szolgáltató nyomon követi a nyomkövetési számot, kattintson a szalagcímre az Azure importálási feladatok áttekintése lapon, és adja meg a következő adatokat:

   > [!IMPORTANT]
   > Győződjön meg arról, hogy az Azure importálási feladat létrehozásától számított két héten belül frissítve legyenek a szolgáltató adatai és a nyomkövetési azonosító szám. Ha két héten belül nem tudja ellenőrizni ezeket az adatokat, a rendszer törli a feladatot, és nem dolgozza fel a meghajtókat.

   ![Szállítási információk tárolása](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Szállítási információk tárolása](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>A meghajtók feldolgozásának ideje

Az Azure importálási feladatok feldolgozásához szükséges idő a különböző tényezőktől függ, például a szállítási idő, a feladattípus, a másolt adatok típusa és mérete, valamint a megadott lemezek mérete. Az Azure import/export szolgáltatás nem rendelkezik SLA-val, de a lemezek fogadása után a szolgáltatás arra törekszik, hogy az Azure Storage-fiókba 7 – 10 nap múlva elvégezze a biztonsági másolatok másolását.

### <a name="monitoring-azure-import-job-status"></a>Az Azure-beli importálási feladatok állapotának figyelése

Az importálási feladat állapotát a Azure Portal figyelheti az **importálási/exportálási feladatok** lapra, és kiválaszthatja a feladatát. Az importálási feladatok állapotával kapcsolatos további információkért tekintse meg a [tároló importálása exportálási szolgáltatásról](../storage/common/storage-import-export-service.md) szóló cikket.

### <a name="complete-the-workflow"></a>A munkafolyamat befejezése

Az importálási feladatok sikeres befejezését követően a kezdeti biztonsági mentési adatok elérhetők a Storage-fiókban. A következő ütemezett biztonsági mentés időpontjában a Azure Backup átmásolja az adatok tartalmát a Storage-fiókból a Recovery Services-tárolóba az alábbi ábrán látható módon:

   ![Adatok másolása Recovery Services tárba](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

A következő ütemezett biztonsági mentés időpontjában a Azure Backup növekményes biztonsági mentést hajt végre.

### <a name="cleaning-up-resources"></a>Erőforrások törlése

A kezdeti biztonsági mentés befejezését követően biztonságosan törölheti az Azure Storage-tárolóba importált és az átmeneti helyen tárolt biztonsági mentési adataikat.

## <a name="next-steps"></a>Következő lépések

* Az Azure importálási/exportálási munkafolyamatával kapcsolatos bármilyen kérdés esetén tekintse át az [adatok blob Storage-ba való átvitelére szolgáló Microsoft Azure import/export szolgáltatás használatát](../storage/common/storage-import-export-service.md)ismertető témakört.

