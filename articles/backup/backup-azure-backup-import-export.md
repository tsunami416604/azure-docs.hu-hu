---
title: Offline biztonsági mentés bemagvetése az Azure importálási/exportálási szolgáltatásával
description: Ismerje meg, hogyan használhatja az Azure Backup adatokat a hálózatról az Azure import/export szolgáltatás használatával. Ez a cikk ismerteti a kezdeti biztonsági mentési adatok offline vetése az Azure importálási/exportálási szolgáltatás használatával.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206758"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Offline biztonsági mentési munkafolyamat az Azure Backupban

Az Azure Backup számos beépített hatékonyságot biztosít, amelyek hálózati és tárolási költségeket takarítanak meg az Azure-ba történő adatok első teljes biztonsági mentése során. A kezdeti teljes biztonsági mentések általában nagy mennyiségű adatot továbbítanak, és nagyobb hálózati sávszélességet igényelnek a későbbi biztonsági mentésekhoz képest, amelyek csak a különbözeteket/növekményeket továbbítják. Az offline vetés folyamata során az Azure Backup lemezek használatával töltheti fel az offline biztonsági mentési adatokat az Azure-ba.

Az Azure Backup offline vetési folyamat szorosan integrálva van az [Azure import/export szolgáltatással.](../storage/common/storage-import-export-service.md) Ezzel a szolgáltatással továbbíthatja a kezdeti biztonsági mentési adatokat az Azure-ba lemezek használatával. Ha terabájtokkal (TB-k) rendelkezik a kezdeti biztonsági mentési adatokból, amelyeket nagy késésű és alacsony sávszélességű hálózaton keresztül kell átvinni, az offline vetési munkafolyamat segítségével szállíthatja a kezdeti biztonsági másolatot egy vagy több merevlemezre egy Azure-adatközpontba. Az alábbi kép áttekintést nyújt a munkafolyamat lépéseiről.

  ![Kapcsolat nélküli importálási munkafolyamat – áttekintés](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Az offline biztonsági mentési folyamat a következő lépésekkel jár:

1. Ahelyett, hogy a biztonsági mentési adatokat a hálózaton keresztül küldene, írja a biztonsági mentési adatokat egy átmeneti helyre.
1. Az *AzureOfflineBackupDiskPrep* segédprogrammal írja az adatokat az átmeneti helyen egy vagy több SATA lemezre.
1. Az előkészítő munka részeként az *AzureOfflineBackupPrep segédprogram* létrehoz egy Azure importálási feladatot. Küldje el a SATA-meghajtókat a legközelebbi Azure-adatközpontba, és hivatkozzon az importálási feladatra a tevékenységek összekapcsolásához.
1. Az Azure-adatközpontban a lemezeken lévő adatok egy Azure-tárfiókba másolva lesznek.
1. Az Azure Backup átmásolja a biztonsági mentési adatokat a tárfiókból a Recovery Services-tárolóba, és a növekményes biztonsági mentések ütemezve vannak.

## <a name="supported-configurations"></a>Támogatott konfigurációk

A következő Azure Backup-funkciók vagy -munkaterhelések támogatják az offline biztonsági mentés használatát:

> [!div class="checklist"]
>
> * Fájlok és mappák biztonsági másolata a Microsoft Azure Recovery Services (MARS) ügynökkel, más néven az Azure backup ügynökkel.
> * Az összes munkaterhelés és fájl biztonsági mentése a System Center Data Protection Manager (DPM) segítségével.
> * Az összes számítási feladat és fájl biztonsági másolata a Microsoft Azure Backup Server kiszolgálóval.
 
   > [!NOTE]
   > Offline biztonsági mentés nem támogatott a rendszerállapot biztonsági mentések az Azure backup ügynök használatával.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Előfeltételek

  > [!NOTE]
  > A következő előfeltételek és munkafolyamat csak a fájlok és mappák offline biztonsági mentésére vonatkoznak a [legújabb Azure Recovery Services Agent](https://aka.ms/azurebackup_agent)használatával. A Rendszerszintű dpm-vel vagy az Azure Backup Server rel kapcsolat nélküli biztonsági mentést a számítási feladatokhoz való kapcsolat nélküli biztonsági mentésről a [DPM és az Azure Backup Server offline biztonsági mentési munkafolyamata című témakörben található.](backup-azure-backup-server-import-export-.md)

Az offline biztonsági mentési munkafolyamat megkezdése előtt hajtsa végre a következő előfeltételeket:

* [Hozzon](backup-azure-recovery-services-vault-overview.md)létre egy helyreállítási szolgáltatások tároló . Tároló létrehozásához kövesse a [Helyreállítási szolgáltatások tárolójának létrehozása](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)című részben leírt lépéseket.
* Győződjön meg arról, hogy csak [az Azure backup ügynök legújabb verziója](https://aka.ms/azurebackup_agent) van telepítve a Windows Server vagy Windows ügyfél, adott esetben, és a számítógép regisztrálva van a Helyreállítási szolgáltatások tárolójában.
* Az Azure PowerShell 3.7.0-s szükséges az Azure backup ügynöket futtató számítógépen. Töltse le és [telepítse az Azure PowerShell 3.7.0-s verzióját.](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)
* Az Azure backup ügynöket futtató számítógépen győződjön meg arról, hogy a Microsoft Edge vagy az Internet Explorer 11 telepítve van, és a JavaScript engedélyezve van.
* Hozzon létre egy Azure-tárfiókot ugyanabban az előfizetésben, mint a Recovery Services-tároló.
* Győződjön meg arról, hogy rendelkezik az Azure Active Directory-alkalmazás létrehozásához [szükséges engedélyekkel.](../active-directory/develop/howto-create-service-principal-portal.md) Az offline biztonsági mentési munkafolyamat létrehoz egy Azure Active Directory-alkalmazást az Azure storage-fiókhoz társított előfizetésben. Az alkalmazás célja, hogy az Azure Backup biztonságos és hatókörrel rendelkező hozzáférést biztosítson az Azure importálási/exportálási szolgáltatáshoz, amely az offline biztonsági mentési munkafolyamathoz szükséges.
* Regisztrálja a *Microsoft.ImportExport* erőforrás-szolgáltatót az Azure-tárfiókot tartalmazó előfizetéssel. Az erőforrás-szolgáltató regisztrálása:
    1. A főmenüben válassza az **Előfizetések**lehetőséget.
    1. Ha több előfizetésre is előfizetett, válassza ki az offline biztonsági mentéshez használni kívánt előfizetést. Ha csak egy előfizetést használ, akkor megjelenik az előfizetése.
    1. Az előfizetés menüben válassza az **Erőforrás-szolgáltatók** lehetőséget a szolgáltatók listájának megtekintéséhez.
    1. A szolgáltatók listájában görgessen le a *Microsoft.ImportExport*. Ha az **Állapot** **nincs regisztrálva,** válassza a **Regisztráció**lehetőséget.

        ![Az erőforrás-szolgáltató regisztrálása](./media/backup-azure-backup-import-export/registerimportexport.png)

* Létrejön egy átmeneti hely, amely lehet hálózati megosztás vagy a számítógép bármely további meghajtója, belső vagy külső, és elegendő lemezterület áll a kezdeti példány tárolására. Ha például egy 500 GB-os fájlkiszolgálóról szeretne biztonsági másolatot tenni, győződjön meg arról, hogy az átmeneti terület legalább 500 GB. (A tömörítés miatt kisebb mennyiséget használ.)
* Amikor lemezeket küld az Azure-ba, csak 2,5 hüvelykes SSD-t vagy 2,5 hüvelykes vagy 3,5 hüvelykes SATA II/III belső merevlemezt használjon. Legfeljebb 10 TB-os merevlemezt használhat. Ellenőrizze az [Azure import/export szolgáltatás dokumentációjában](../storage/common/storage-import-export-requirements.md#supported-hardware) a szolgáltatás által támogatott legújabb meghajtókat.
* A SATA-meghajtókat olyan számítógéphez *(másolt számítógéphez)* kell csatlakoztatni, ahonnan a biztonsági mentési adatok másolata az átmeneti helyről a SATA-meghajtókra történik. Győződjön meg arról, hogy a BitLocker engedélyezve van a másolószámítógépen.

## <a name="workflow"></a>Munkafolyamat

Ez a szakasz ismerteti az offline biztonsági mentési munkafolyamatot, hogy az adatok egy Azure-adatközpontba szállíthatók és feltölthetők az Azure Storage-ba. Ha kérdése van az importálási szolgáltatással vagy a folyamat bármely aspektusával kapcsolatban, olvassa el az [Azure Import/Export szolgáltatás áttekintő dokumentációját.](../storage/common/storage-import-export-service.md)

## <a name="initiate-offline-backup"></a>Kapcsolat nélküli biztonsági mentés kezdeményezése

1. Amikor biztonsági másolatot ütemez a Helyreállítási szolgáltatások ügynökén, ezen a lapon jelenik meg.

    ![Lap importálása](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Válassza az **Átvitel a saját lemezekkel**lehetőséget .

    > [!NOTE]
    > Az Azure Data Box beállítás használatával a kezdeti biztonsági mentési adatok offline módban. Ez a beállítás a saját Azure-kompatibilis lemezek beszerzéséhez szükséges erőfeszítést menti. A Microsoft saját tulajdonú, biztonságos és illetéktelen Azure Data Box-eszközöket biztosítja, amelyekre a Helyreállítási szolgáltatások ügynöke közvetlenül írhat biztonsági mentési adatokat.

1. Válassza a **Tovább**lehetőséget, és gondosan töltse ki a mezőket.

    ![Adja meg a lemez adatait](./media/backup-azure-backup-import-export/your-disk-details.png)

   A kitöltett mezők a következők:

    * **Átmeneti hely**: Az az ideiglenes tárolóhely, ahhoz az ideiglenes tárolóhelyhez, ahhoz a kezdeti biztonsági másolathoz. Lehet, hogy az átmeneti hely hálózati megosztáson vagy helyi számítógépen található. Ha a másoló számítógép és a forrásszámítógép eltérő, adja meg az átmeneti hely teljes hálózati elérési útját.
    * **Azure Resource Manager storage-fiók:** A Resource Manager típusú tárfiók neve (általános célú v1 vagy általános célú v2) bármely Azure-előfizetésben.
    * **Azure Storage Container:** A céltároló blob neve az Azure storage-fiókban, ahol a biztonsági mentési adatok importálása a Recovery Services-tárolóba másolása előtt.
    * **Azure-előfizetés-azonosító:** Az Azure-előfizetés azonosítója, ahol az Azure storage-fiók jön létre.
    * **Azure importálási feladat neve:** Az az egyedi név, amellyel az Azure import/export szolgáltatás és az Azure Backup nyomon követi a lemezeken küldött adatok azure-ba.
  
   Miután kitöltötte a mezőket, válassza a **Tovább gombot.** Mentse az **átmeneti helyet** és az **Azure importálási feladat neve** adatokat. Elő kell készíteni a lemezeket.

1. Amikor a rendszer kéri, jelentkezzen be az Azure-előfizetésbe. Be kell jelentkeznie, hogy az Azure Backup létre tudja hozni az Azure Active Directory-alkalmazást. Adja meg az Azure importálási/exportálási szolgáltatás eléréséhez szükséges engedélyeket.

    ![Az Azure-előfizetés bejelentkezési lapja](./media/backup-azure-backup-import-export/azure-login.png)

1. Fejezze be a munkafolyamatot. Az Azure Backup Agent konzolon válassza **a Biztonsági mentés most**lehetőséget.

    ![Biztonsági másolatot( biztonsági másolatot)](./media/backup-azure-backup-import-export/backupnow.png)

1. A varázsló **megerősítést kérő** lapján válassza a **Biztonsági másolatok kiválasztása**lehetőséget. A kezdeti biztonsági mentés a beállítás részeként az átmeneti területre kerül.

   ![Annak ellenőrzése, hogy készen áll-e a biztonsági másolatok megerősítésére](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    A művelet befejezése után az átmeneti hely készen áll a lemez előkészítéséhez való használatra.

   ![Biztonsági másolatot készítő varázsló lapja](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA-meghajtók előkészítése és szállítás az Azure-ba

Az *AzureOfflineBackupDiskPrep* segédprogram előkészíti a legközelebbi Azure-adatközpontba küldött SATA-meghajtókat. Ez a segédprogram az Azure Backup Agent telepítési könyvtárában érhető el a következő elérési úton:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Nyissa meg a könyvtárat, és másolja az *AzureOfflineBackupDiskPrep* könyvtárat egy másik számítógépre, ahol a SATA-meghajtók csatlakoztatva vannak. A csatlakoztatott SATA-meghajtóval rendelkező számítógépen győződjön meg arról, hogy:

   * A másolószámítógép az "Offline biztonsági mentés kezdeményezése" szakaszban a munkafolyamatban megadott hálózati elérési úttal érheti el az offline vetési munkafolyamat átmeneti helyét.
   * A BitLocker engedélyezve van a másolószámítógépen.
   * Az Azure PowerShell 3.7.0 telepítve van.
   * A legújabb kompatibilis böngészők (Microsoft Edge vagy Internet Explorer 11) telepítve vannak, és a JavaScript engedélyezve van.
   * A másolószámítógép hozzáférhet az Azure Portalhoz. Szükség esetén a másolószámítógép megegyezhet a forrásszámítógéppel.

     > [!IMPORTANT]
     > Ha a forrásszámítógép virtuális gép, akkor a másolószámítógépnek a forrásszámítógéptől eltérő fizikai kiszolgálónak vagy ügyfélszámítógépnek kell lennie.

1. Nyisson meg egy rendszergazda jogú parancssort a másolt számítógépen, amelyen az *AzureOfflineBackupDiskPrep* segédprogram könyvtára az aktuális könyvtár. Futtassa az alábbi parancsot:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Paraméter | Leírás |
    | --- | --- |
    | s:&lt;*Átmeneti hely elérési útja*&gt; |Ez a kötelező bemenet a "Kapcsolat nélküli biztonsági mentés kezdeményezése" szakaszban a munkafolyamatban megadott átmeneti hely elérési útját biztosítja. |
    | p:&lt;*Közzétételi beállítások fájl elérési útja*&gt; |Ez a választható bemenet az Azure közzétételi beállításfájlelérési útjának biztosításához használható, amelyet a "Kapcsolat nélküli biztonsági mentés kezdeményezése" szakaszban a munkafolyamatban megadott. |

    A parancs futtatásakor a segédprogram kéri az Azure importálási feladat kiválasztását, amely megfelel az előkell készíteni meghajtók. Ha csak egyetlen importálási feladat van társítva a megadott átmeneti helyhez, akkor egy ehhez hasonló oldal jelenik meg.

    ![Az Azure lemezelőkészítő eszköz bemenete](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Adja meg a meghajtó betűjelét az Azure-ba való átvitelre előkészíteni kívánt csatlakoztatott lemez záró kettőspontja nélkül.
1. A rendszer a rendszer rekretinációjára vonatkozóan erősítse meg a meghajtó formázását.
1. A rendszer kéri, hogy jelentkezzen be az Azure-előfizetésébe. Adja meg hitelesítő adatait.

    ![Azure-előfizetésbe való bejelentkezés](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Az eszköz ezután megkezdi a lemez előkészítését és a biztonsági mentési adatok másolását. Előfordulhat, hogy az eszköz által kért rákérdezéskor további lemezeket kell csatolnia, ha a megadott lemeznem rendelkezik elegendő hellyel a biztonsági mentési adatok számára. <br/>

    Az eszköz sikeres végrehajtásának végén a parancssor három információt tartalmaz:

   1. Egy vagy több lemez, amelyet megadott, előkészítve van az Azure-ba való szállításhoz.
   1. Megerősítést kap az importálási feladat létrehozásáról. Az importálási feladat a megadott nevet használja.
   1. Az eszköz megjeleníti az Azure-adatközpont szállítási címét.

      ![Az Azure lemez előkészítése befejeződött](./media/backup-azure-backup-import-export/console2.png)<br/>

1. A parancs végrehajtásának végén frissítheti a szállítási információkat.

1. A lemezeket az eszköz által megadott címre szállítsa. Őrizd meg a nyomon követési számot későbbi használatra.

   > [!IMPORTANT]
   > Nincs két Azure-importálási feladat azonos követési számmal. Győződjön meg arról, hogy a segédprogram által egyetlen Azure-importálási feladat keretében készített meghajtókat egyetlen csomagban szállítják együtt, és hogy a csomaghoz egyetlen egyedi nyomon követési szám van. Ne kombinálja a különálló Azure-importálási feladatok részeként készített meghajtókat egyetlen csomagban.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Szállítási részletek frissítése az Azure importálási feladatához

Az alábbi eljárás frissíti az Azure importálási feladat szállítási részleteit. Ez az információ a következőkrészleteit tartalmazza:

* A lemezeket az Azure-ba kézbesítő szolgáltató neve.
* Adja vissza a lemezek szállítási adatait.

1. Jelentkezzen be Azure-előfizetésbe.
1. A főmenüben válassza a **Minden szolgáltatás lehetőséget.** A **Minden szolgáltatás** párbeszédpanelen adja meg az **Importálás párbeszédpanelt.** Ha megjelenik **az Importálás/exportálás feladatok,** jelölje ki azt.

    ![Szállítási adatok megadása](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Megnyílik **az Importálás/exportálási feladatok** menü, és megjelenik a kijelölt előfizetésösszes importálási/exportálási feladatlistája.

1. Ha több előfizetéssel rendelkezik, válassza ki a biztonsági mentési adatok importálásához használt előfizetést. Ezután válassza ki az újonnan létrehozott importálási feladatot a részletek megnyitásához.

    ![Szállítási információk áttekintése](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. Az importálási feladat **Beállítások** menüjében válassza a **Szállítási adatok kezelése**lehetőséget. Adja meg a visszáru szállítási adatait.

    ![Szállítási információk tárolása](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Ha rendelkezik a szállítmányozótól származó nyomon követési számmal, válassza ki a szalagcímet az Azure importálási feladat áttekintése oldalon, és adja meg a következő adatokat.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy az Azure importálási feladat létrehozásától számított két héten belül frissítve legyenek a szolgáltató adatai és a nyomkövetési azonosító szám. Ha két héten belül nem ellenőrzi ezt az információt, az a feladat törlését és a meghajtók feldolgozásának elmaradását eredményezheti.

   ![Nyomon követési információfrissítési riasztás](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Szállítmányozói információk és fuvarlevélszám](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>A meghajtók feldolgozásának ideje

Az Azure-importálási feladat feldolgozásához szükséges idő változó. A feldolgozási idő olyan tényezőkön alapul, mint a szállítási idő, a feladat típusa, a másolt adatok típusa és mérete, valamint a rendelkezésre bocsátott lemezek mérete. Az Azure importálási/exportálási szolgáltatás nem rendelkezik SLA-val. A lemezek fogadása után a szolgáltatás arra törekszik, hogy 7–10 napon belül töltse ki a biztonsági mentési adatok másolatát az Azure storage-fiókjába.

### <a name="monitor-azure-import-job-status"></a>Az Azure importálási feladatállapotának figyelése

Az importálási feladat állapotát az Azure Portalon figyelheti. Nyissa meg a **Feladatok importálása/exportálása** lapot, és válassza ki a feladatot. Az importálási feladatok állapotáról a [Mi az Azure importálási/exportálási szolgáltatása.](../storage/common/storage-import-export-service.md)

### <a name="finish-the-workflow"></a>A munkafolyamat befejezése

Miután az importálási feladat sikeresen befejeződött, a kezdeti biztonsági mentési adatok elérhetők a tárfiókban. A következő ütemezett biztonsági mentés időpontjában az Azure Backup átmásolja az adatok tartalmát a tárfiókból a Recovery Services-tárolóba.

   ![Adatok másolása a Recovery Services tárolóba](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

A következő ütemezett biztonsági mentés időpontjában az Azure Backup növekményes biztonsági mentést hajt végre.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután a kezdeti biztonsági mentés befejeződött, biztonságosan törölheti az Azure Storage-tárolóba importált adatokat és az átmeneti helyen lévő biztonsági mentési adatokat.

## <a name="next-steps"></a>További lépések

* Az Azure importálási/exportálási szolgáltatásmunkafolyamatával kapcsolatos kérdéseiről [a Microsoft Azure importálási/exportálási szolgáltatáshasználata a Blob storage-ba történő átvitele](../storage/common/storage-import-export-service.md)című témakörben nyújt fel.
