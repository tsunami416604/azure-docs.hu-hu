---
title: Azure Backup – offline biztonsági mentés a DPM és a Azure Backup Server
description: A Azure Backup lehetővé teszi, hogy az Azure import/export szolgáltatással a hálózaton kívülről küldjön adatküldést. Ez a cikk a DPM és a Azure Backup Server offline biztonsági mentési munkafolyamatát ismerteti (MABS).
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/08/2018
ms.author: dacurwin
ms.openlocfilehash: 0763cbd4345dca39f37b77a0f3d991a7d77e30c4
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074308"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Offline – a DPM és a Azure Backup Server biztonsági mentési munkafolyamata

A Azure Backup számos beépített hatékonyságot biztosít, amelyek a hálózati és tárolási költségeket az Azure-ba irányuló összes adat kezdeti teljes biztonsági mentése során megtakarítják. A kezdeti teljes biztonsági mentések általában nagy mennyiségű adat átvitelét igénylik, és nagyobb hálózati sávszélességet igényelnek, ha az azokat követő biztonsági mentések során csak a különbözeteket/növekményeket továbbítják. Azure Backup tömöríti a kezdeti biztonsági mentéseket. Az offline kivetés folyamatán keresztül a Azure Backup lemezek használatával feltölthetik a tömörített kezdeti biztonsági mentési adatok az Azure-ba.

Azure Backup kapcsolat nélküli előkészítési folyamata szorosan integrálva van az [Azure import/export szolgáltatással](../storage/common/storage-import-export-service.md) , amely lehetővé teszi adatok átvitelét az Azure-ba lemezek használatával. Ha terabájt (TBs) típusú kezdeti biztonsági mentési adat szükséges, amelyet nagy késésű és alacsony sávszélességű hálózaton kell átvinni, az offline előkészítési munkafolyamattal egy vagy több merevlemezen is elküldheti az Azure-adatközpontba a kezdeti biztonsági másolatot. Ez a cikk áttekintést nyújt a munkafolyamatot a System Center DPM és a Azure Backup Server számára elvégező részletes lépésekről.

> [!NOTE]
> A Microsoft Azure Recovery Services-(MARS-) ügynök offline biztonsági mentésének folyamata különbözik a System Center DPM és Azure Backup Servertól. További információ a MARS-ügynökkel való offline biztonsági mentés használatáról: [ebben a cikkben](backup-azure-backup-import-export.md). Az offline biztonsági mentés nem támogatott a rendszerállapot biztonsági mentéséhez a Azure Backup ügynök használatával.
>

## <a name="overview"></a>Áttekintés

A Azure Backup és az Azure import/export kapcsolat nélküli kicsomagolási funkciójának használatával egyszerűen tölthetők fel az adatok az Azure-ba a lemezekkel. Az offline biztonsági mentési folyamat az alábbi lépéseket foglalja magában:

> [!div class="checklist"]
>
> * A biztonsági mentési adat a hálózaton keresztüli küldés helyett *átmeneti helyre* íródik
> * A rendszer ezután az *átmeneti helyen* lévő, a *AzureOfflineBackupDiskPrep* segédprogrammal egy vagy több SATA-lemezre ír.
> * A segédprogram automatikusan létrehoz egy Azure-beli importálási feladatot
> * Ezután a rendszer elküldi a SATA-meghajtókat a legközelebbi Azure-adatközpontba
> * Miután befejeződött a biztonsági mentési adatok feltöltése az Azure-ba, Azure Backup másolja a biztonsági mentési adatokat a Backup-tárolóba, és a növekményes biztonsági mentések ütemezve lesznek.

## <a name="supported-configurations"></a>Támogatott konfigurációk

Az offline biztonsági mentés a Azure Backup összes olyan üzemi modellje esetében támogatott, amely a helyszíni biztonsági mentési adatokból a Microsoft Cloud. Ez magában foglalja

> [!div class="checklist"]
>
> * Fájlok és mappák biztonsági mentése a Microsoft Azure Recovery Services-(MARS-) ügynökkel vagy a Azure Backup-ügynökkel.
> * Összes munkaterhelés és fájl biztonsági mentése a System Center Data Protection Manager (SC DPM) szolgáltatással
> * Az összes számítási feladat és fájl biztonsági mentése Microsoft Azure Backup-kiszolgálóval

## <a name="prerequisites"></a>Előfeltételek

Az offline biztonsági mentési munkafolyamat kezdeményezése előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek.

* A rendszer létrehozta [Recovery Services](backup-azure-recovery-services-vault-overview.md) -tárolót. Ha létre szeretne hozni egyet, tekintse meg a cikk lépéseit [.](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Azure Backup ügynök vagy Azure Backup Server vagy SC DPM telepítve van a Windows Server-vagy Windows-ügyfélre, ha van ilyen, és a számítógép regisztrálva van a Recovery Services-tárolóban. Győződjön meg arról, hogy a Azure Backup csak a [legújabb verzióját](https://go.microsoft.com/fwlink/?linkid=229525) használja.
* [Töltse le az Azure publish Settings fájlt](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) azon a számítógépen, amelyről biztonsági másolatot kíván készíteni az adatokról. Az előfizetés, amelyről letölti a közzétételi beállítások fájlját, különbözhet az Recovery Services-tárolót tartalmazó előfizetéstől. Ha az előfizetés szuverén Azure-felhőben található, akkor az Azure publish Settings fájl letöltéséhez használja az alábbi hivatkozásokat.

    | Szuverén felhő régiója | Az Azure közzétételi beállítások fájljának hivatkozása |
    | --- | --- |
    | Egyesült Államok | [Hivatkozás](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kína | [Hivatkozás](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Egy *klasszikus* üzemi modellel rendelkező Azure Storage-fiók lett létrehozva abban az előfizetésben, amelyből letöltötte a közzétételi beállítások fájlját az alábbi ábrán látható módon:

  ![Klasszikus Storage-fiók létrehozása](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* A rendszer létrehoz egy átmeneti helyet, amely lehet hálózati megosztás vagy a számítógép belső vagy külső meghajtója, amely elegendő lemezterülettel rendelkezik a kezdeti másolat tárolásához. Ha például egy 500 GB-os fájlkiszolgáló biztonsági mentését kísérli meg, győződjön meg arról, hogy az átmeneti körzet legalább 500 GB. (A tömörítés miatt kisebb mennyiség van használatban.)
* Az Azure-ba küldendő lemezek tekintetében ügyeljen arra, hogy a rendszer csak 2,5 hüvelykes SSD-t, illetve 2,5 hüvelykes vagy 3,5 hüvelykes SATA II/III-alapú merevlemezt használ. A merevlemezeket akár 10 TB-ig is használhatja. Az [Azure import/export szolgáltatás dokumentációjában](../storage/common/storage-import-export-requirements.md#supported-hardware) keresse meg a szolgáltatás által támogatott meghajtók legújabb készletét.
* A SATA-meghajtóknak csatlakozniuk kell egy számítógéphez (ez a *másolási számítógép*), ahonnan a biztonsági mentési adatok másolata az *átmeneti helyről* a SATA-meghajtókra történik. Győződjön meg arról, hogy a BitLocker engedélyezve van a *másolási számítógépen*

## <a name="workflow"></a>Munkafolyamat

Az ebben a szakaszban található információk segítségével elvégezheti az offline biztonsági mentés munkafolyamatát, így az adatok továbbíthatók egy Azure-adatközpontba, és feltölthetők az Azure Storage-ba. Ha kérdése van az importálási szolgáltatással vagy a folyamat bármely aspektusával kapcsolatban, tekintse meg a korábban hivatkozott [importálási szolgáltatás áttekintése](../storage/common/storage-import-export-service.md) dokumentációt.

### <a name="initiate-offline-backup"></a>Offline biztonsági mentés indítása

1. Amikor ütemezett biztonsági mentést készít, a következő képernyő jelenik meg (Windows Server, Windows Client vagy System Center Data Protection Manager).

    ![Importálási képernyő](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    A System Center Data Protection Manager megfelelő képernyője: <br/>
    ![SC DPM és Azure Backup Server importálási képernyője](./media/backup-azure-backup-import-export/dpmoffline.png)

    A bemenetek leírása a következő:

   * **Átmeneti hely**: az az ideiglenes tárolási hely, ahová a rendszer a kezdeti biztonsági másolatot írja. Előfordulhat, hogy az előkészítési hely egy hálózati megosztáson vagy egy helyi számítógépen található. Ha a számítógép és a forrásszámítógép másolása eltérő, javasoljuk, hogy az előkészítési hely teljes hálózati elérési útját határozza meg.
   * **Azure importálási feladattípus**: az az egyedi név, amellyel az Azure import service és Azure Backup nyomon követheti a lemezeken továbbított adatok átvitelét az Azure-ba.
   * **Azure közzétételi beállítások**: adja meg a közzétételi beállítások fájljának helyi elérési útját.
   * **Azure-előfizetés azonosítója**: annak az előfizetésnek az Azure-ELŐfizetési azonosítója, amelyből letöltötte az Azure közzétételi beállítások fájlját.
   * **Azure Storage-fiók**: az Azure-közzétételi beállítási fájlhoz társított Azure-előfizetéshez tartozó Storage-fiók neve.
   * **Azure Storage-tároló**: annak az Azure Storage-fióknak a neve, amelybe a biztonsági mentési adatmennyiséget importálja.

     Mentse az előkészítési *helyet* és az *Azure-beli importálási feladatot* , amelyet a lemez előkészítésekor szükséges.  

2. Fejezze be a munkafolyamatot, és indítsa el az offline biztonsági mentést, kattintson a **biztonsági mentés most** lehetőségre a Azure Backup ügynök felügyeleti konzolján. Ennek a lépésnek a részeként a kezdeti biztonsági mentés az átmeneti területre íródik.

    ![Biztonsági mentés most](./media/backup-azure-backup-import-export/backupnow.png)

    A megfelelő munkafolyamat a System Center Data Protection Manager vagy Azure Backup-kiszolgálón való végrehajtásához kattintson a jobb gombbal a **védelmi csoportra**, majd válassza a **helyreállítási pont létrehozása** lehetőséget. Ezután válassza az **online védelem** lehetőséget.

    ![SC DPM és Azure Backup-kiszolgáló biztonsági mentése most](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    A művelet befejeződése után az előkészítési hely készen áll a lemez-előkészítésre.

    ![Biztonsági mentés állapota](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>A SATA-meghajtók előkészítése és a szállítás az Azure-ba

A *AzureOfflineBackupDiskPrep* segédprogram a legközelebbi Azure-adatközpontba eljuttatott SATA-meghajtók előkészítésére szolgál. Ez a segédprogram a Recovery Services ügynök telepítési könyvtárában érhető el a következő elérési úton:

*\\Microsoft Azure Recovery Services ügynök\\utils\\*

1. Nyissa meg a könyvtárat, és másolja a **AzureOfflineBackupDiskPrep** könyvtárat arra a másolási számítógépre, amelyen az előkészíteni kívánt SATA-meghajtók csatlakoztatva vannak. Ügyeljen a következőkre a másolási számítógép tekintetében:

   * A másolási számítógép elérheti az offline tesztelési munkafolyamat előkészítési helyét ugyanazzal a hálózati elérési úttal, amelyet az **Offline biztonsági mentési munkafolyamat kezdeményezése** című rész tartalmaz.
   * A BitLocker engedélyezve van a másolási számítógépen.
   * A másolási számítógép elérheti a Azure Portal.

     Ha szükséges, a másolási számítógép lehet ugyanaz, mint a forrásszámítógép.

     > [!IMPORTANT]
     > Ha a forrásoldali számítógép virtuális gép, akkor a másolási számítógépként kötelező egy másik fizikai kiszolgáló vagy ügyfélszámítógép használata.

2. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen a *AzureOfflineBackupDiskPrep* segédprogram címtárával az aktuális könyvtárként, és futtassa a következő parancsot:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Paraméter | Leírás |
    | --- | --- |
    | s:&lt;*átmeneti hely elérési útja*&gt; |A kötelező bemenet, amely a **kapcsolat nélküli biztonsági mentési** munkafolyamatban megadott átmeneti hely elérési útjának megadására szolgál. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; |Opcionális bemenet, amely a **kapcsolat nélküli biztonsági mentési** munkafolyamatban megadott **Azure közzétételi beállítási** fájl elérési útjának megadására szolgál. |

    > [!NOTE]
    > A AzurePublishSettingFile&gt; érték &lt;elérési útja kötelező, ha a számítógép és a forrásszámítógép másolása eltérő.
    >
    >

    A parancs futtatásakor a segédprogram kéri az Azure importálási feladatoknak a kiválasztását, amely megfelel az előkészíteni kívánt meghajtóknak. Ha csak egyetlen importálási feladattípus van társítva a megadott átmeneti helyhez, a következőhöz hasonló képernyő jelenik meg.

    ![Azure Disk-előkészítő eszköz bemenete](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Adja meg a meghajtóbetűjelet az Azure-ba való átvitelre előkészíteni kívánt csatlakoztatott lemez záró kettőspontja nélkül. Ha a rendszer kéri, adja meg a meghajtó formázásának megerősítését.

    Az eszköz ezután megkezdi a lemez előkészítését és a biztonsági mentési adatok másolását. Előfordulhat, hogy további lemezeket kell csatlakoztatnia, ha az eszköz erre kéri, ha a megadott lemez nem rendelkezik elegendő hellyel a biztonsági mentési információkhoz. <br/>

    Az eszköz sikeres végrehajtása után egy vagy több megadott lemez készen áll az Azure-ba történő szállításra. Emellett az Azure-ban létrejön egy importálási művelet, amely az **Offline biztonsági mentési munkafolyamat kezdeményezése** során megadott névvel rendelkezik. Végül az eszköz megjeleníti a szállítási címet arra az Azure-adatközpontba, ahol a lemezeket el kell szállítani.

    ![Az Azure Disk-előkészítés befejeződött](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. A parancs végrehajtásának végén a következő módon is megjelenik a szállítási adatok frissítése lehetőség:

    ![Szállítási adatok frissítése lehetőség](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. A részleteket azonnal megadhatja. Az eszköz végigvezeti Önt egy adatsorozatot érintő folyamaton. Ha azonban nem rendelkezik olyan információkkal, mint a követési szám vagy a szállítással kapcsolatos egyéb adatok, akkor befejezheti a munkamenetet. A szállítási adatok későbbi frissítésének lépéseit ebben a cikkben találja.

6. Küldje le a lemezeket az eszköz által biztosított címnek, és tartsa meg a nyomon követési számot későbbi használatra.

   > [!IMPORTANT]
   > Két Azure-beli importálási feladat nem rendelkezhet ugyanazzal a követési számmal. Győződjön meg arról, hogy egyetlen Azure-beli importálási feladatban a segédprogram által készített meghajtók egyetlen csomagban vannak elküldve, és hogy a csomaghoz egyetlen egyedi nyomkövetési szám tartozik. Ne egyesítse a **különböző** Azure-beli importálási feladatok részeként előkészített meghajtókat egyetlen csomagban.

7. Ha rendelkezik a követési számmal kapcsolatos információval, nyissa meg a forrásszámítógépen, amely az importálási feladatok befejezésére vár, és futtassa a következő parancsot egy emelt szintű parancssorban az *AzureOfflineBackupDiskPrep* segédprogram címtárával az aktuális könyvtárként:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   A következő parancsot igény szerint futtathatja egy másik számítógépről, például a *másolási számítógépről*, a *AzureOfflineBackupDiskPrep* segédprogram címtárával az aktuális könyvtárként:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Paraméter | Leírás |
    | --- | --- |
    | u | Egy Azure-beli importálási feladathoz tartozó szállítási adatok frissítéséhez használt kötelező bemenet |
    | s:&lt;*átmeneti hely elérési útja*&gt; | Kötelező bevitel, ha a parancs nem fut a forrásszámítógépen. Annak az átmeneti helynek az elérési útját adja meg, amelyet az **Offline biztonsági mentési munkafolyamat kezdeményezése** során megadott. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; | Kötelező bevitel, ha a parancs nem fut a forrásszámítógépen. A **kapcsolat nélküli biztonsági mentési** munkafolyamatban megadott **Azure közzétételi beállítási** fájl elérési útjának megadására szolgál. |

    A segédprogram automatikusan észleli azt az importálási feladatot, amelyet a forrásszámítógép várakozik, vagy az átmeneti helyhez társított importálási feladatok, ha a parancs egy másik számítógépen fut. Ezután a következő módon frissítheti a szállítási adatokat egy adatsorozaton keresztül:

    ![Szállítási adatok megadása](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

8. Az összes bemenet megadása után alaposan tekintse át a részleteket, és véglegesítse a megadott szállítási adatokat az *Igen*érték beírásával.

    ![Szállítási információk áttekintése](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

9. A szállítási adatok sikeres frissítésekor a segédprogram egy helyi helyet biztosít, ahol az Ön által megadott szállítási adatokat a lent látható módon tárolja a rendszer.

    ![Szállítási információk tárolása](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a meghajtók két héten belül elérik az Azure-adatközpontot a szállítási információk *AzureOfflineBackupDiskPrep* segédprogrammal történő biztosításával. Ennek elmulasztása miatt a meghajtókat nem lehet feldolgozni.  

A fenti lépések elvégzése után az Azure-adatközpont készen áll arra, hogy megkapja a meghajtókat, és feldolgozza őket a biztonsági mentési adatoknak a meghajtóról a létrehozott klasszikus típusú Azure Storage-fiókba történő átviteléhez.

### <a name="time-to-process-the-drives"></a>A meghajtók feldolgozásának ideje

Az Azure importálási feladatok feldolgozásához szükséges idő a különböző tényezőktől függ, például a szállítási idő, a feladattípus, a másolt adatok típusa és mérete, valamint a megadott lemezek mérete. Az Azure import/export szolgáltatás nem rendelkezik SLA-val, de a lemezek fogadása után a szolgáltatás arra törekszik, hogy az Azure Storage-fiókba 7 – 10 nap múlva elvégezze a biztonsági másolatok másolását. A következő szakasz részletesen ismerteti az Azure importálási feladatok állapotának figyelését.

### <a name="monitoring-azure-import-job-status"></a>Az Azure-beli importálási feladatok állapotának figyelése

Amíg a meghajtók továbbítása vagy az Azure-adatközpont átmásolásra kerül a Storage-fiókba, a Azure Backup ügynök vagy SC DPM vagy a Azure Backup Server konzol a forrásszámítógépen a következő feladatot jeleníti meg az ütemezett biztonsági mentésekhez.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Az importálási feladat állapotának megtekintéséhez kövesse az alábbi lépéseket.

1. Nyisson meg egy rendszergazda jogú parancssort a forrásoldali számítógépen, és futtassa a következő parancsot:

     `AzureOfflineBackupDiskPrep.exe u:`

2. A kimenet az importálási feladatok aktuális állapotát jeleníti meg az alábbi ábrán látható módon:

    ![Importálási feladatok állapotának ellenőrzése](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Az Azure importálási feladatának különböző állapotával kapcsolatos további információkért tekintse meg [ezt a cikket.](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>A munkafolyamat befejezése

Az importálási feladatok befejeződése után a kezdeti biztonsági mentési adatok elérhetők a Storage-fiókban. A következő ütemezett biztonsági mentés időpontjában az Azure Backup az alábbi módon másolja át az adatok tartalmát a Storage-fiókból a Recovery Services-tárba:

   ![Adatok másolása Recovery Services tárba](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

A következő ütemezett biztonsági mentés időpontjában a Azure Backup növekményes biztonsági mentést hajt végre a kezdeti biztonsági másolaton.

## <a name="next-steps"></a>További lépések

* Az Azure importálási/exportálási munkafolyamatával kapcsolatos bármilyen kérdés esetén tekintse át az [adatok blob Storage-ba való átvitelére szolgáló Microsoft Azure import/export szolgáltatás használatát](../storage/common/storage-import-export-service.md)ismertető témakört.
* A munkafolyamattal kapcsolatos kérdésekért tekintse meg a Azure Backup- [GYIK](backup-azure-backup-faq.md) offline biztonsági mentését ismertető szakaszt.
