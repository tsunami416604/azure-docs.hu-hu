---
title: A DPM és az Azure Backup Server kapcsolat nélküli biztonsági mentése
description: Az Azure Backup segítségével adatokat küldhet a hálózatról az Azure importálási/exportálási szolgáltatás használatával. Ez a cikk ismerteti a DPM és az Azure Backup Server offline biztonsági mentési munkafolyamatát.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197060"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Offline biztonsági mentési munkafolyamat a DPM és az Azure Backup Server számára

Az Azure Backup számos beépített hatékonyságot biztosít, amelyek hálózati és tárolási költségeket takarítanak meg az Azure-ba történő adatok első teljes biztonsági mentése során. A kezdeti teljes biztonsági mentések általában nagy mennyiségű adatot továbbítanak, és nagyobb hálózati sávszélességet igényelnek a későbbi biztonsági mentésekhoz képest, amelyek csak a különbözeteket/növekményeket továbbítják. Az Azure Backup tömöríti a kezdeti biztonsági mentéseket. Az offline vetés folyamata során az Azure Backup lemezek használatával töltheti fel a tömörített kezdeti biztonsági mentési adatokat offline az Azure-ba.

Az Azure Backup offline vetési folyamata szorosan integrálva van az [Azure importálási/exportálási szolgáltatásával.](../storage/common/storage-import-export-service.md) Ezzel a szolgáltatással lemezek használatával adatokat vihet át az Azure-ba. Ha terabájtokkal (TB-k) rendelkezik a kezdeti biztonsági mentési adatokból, amelyeket nagy késésű és alacsony sávszélességű hálózaton keresztül kell átvinni, az offline vetési munkafolyamat segítségével szállíthatja a kezdeti biztonsági másolatot egy vagy több merevlemezen egy Azure-adatközpontba. Ez a cikk áttekintést és további lépéseket tartalmaz, amelyek befejezik ezt a munkafolyamatot a System Center Data Protection Manager (DPM) és a Microsoft Azure Backup Server (MABS) számára.

> [!NOTE]
> A Microsoft Azure Recovery Services (MARS) ügynök offline biztonsági mentésének folyamata különbözik a DPM és a MABS szolgáltatástól. Az offline biztonsági mentés MARS-ügynökkel való használatáról az [Azure Backup offline biztonsági mentési munkafolyamata című témakörben talál](backup-azure-backup-import-export.md)további információt. Offline biztonsági mentés nem támogatott a rendszerállapot biztonsági mentések az Azure backup ügynök használatával.
>

## <a name="overview"></a>Áttekintés

Az Azure Backup és az Azure Import/Export szolgáltatás offline vetési képességével egyszerűen feltöltheti az adatokat offline állapotba az Azure-ba lemezek használatával. Az offline biztonsági mentési folyamat a következő lépéseket foglalja magában:

> [!div class="checklist"]
>
> * A biztonsági mentési adatok egy átmeneti helyre kerülnek, ahelyett, hogy a hálózaton keresztül küldenék.
> * Az átmeneti helyen lévő adatokat ezután egy vagy több SATA-lemezre írja a rendszer az *AzureOfflineBackupDiskPrep* segédprogrammal.
> * Az Azure importálási feladat automatikusan létre a segédprogram.
> * A SATA-meghajtók ezután elküldésre kerülnek a legközelebbi Azure-adatközpontba.
> * Miután a biztonsági mentési adatok feltöltése az Azure-ba befejeződött, az Azure Backup átmásolja a biztonsági mentési adatokat a biztonsági mentési tárolóba, és a növekményes biztonsági mentések vannak ütemezve.

## <a name="supported-configurations"></a>Támogatott konfigurációk

Offline biztonsági mentés támogatott az Azure Backup összes üzembe helyezési modellje számára, amelyek a helyszíni adatokról a Microsoft felhőbe történő biztonsági mentését támogatják. Ezek a modellek a következők:

> [!div class="checklist"]
>
> * Fájlok és mappák biztonsági mentése a MARS-ügynökkel vagy az Azure biztonsági másolat ügynökével.
> * Az összes számítási feladat és fájl biztonsági mentése a DPM-mel.
> * Az összes számítási feladat és fájl biztonsági mentése a MABS-szel.

## <a name="prerequisites"></a>Előfeltételek

Az offline biztonsági mentési munkafolyamat megkezdése előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Létrehozott egy [helyreállítási szolgáltatások tárolója.](backup-azure-recovery-services-vault-overview.md) Létrehozásához kövesse a [Helyreállítási szolgáltatások tárolólétrehozása című](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) rész lépéseit.
* Egy Azure backup ügynök vagy MABS vagy DPM telepítve van a Windows Server vagy a Windows-ügyfél, adott esetben, és a számítógép regisztrálva van a Recovery Services tárolójában. Győződjön meg arról, hogy csak az [Azure Backup legújabb verzióját](https://go.microsoft.com/fwlink/?linkid=229525) használja.
* [Töltse le az Azure közzétételi beállításfájlját](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) arra a számítógépre, amelyről az adatok biztonsági másolatot szeretne. Az az előfizetés, amelyből letölti a közzétételi beállításfájlt, eltérhet a Recovery Services-tárolót tartalmazó előfizetéstől. Ha az előfizetés e szuverén Azure-felhők, majd használja az alábbi hivatkozásokat, ha szükséges az Azure közzétételi beállításfájl letöltéséhez.

    | Szuverén felhőrégió | Az Azure közzétételi beállításfájljának hivatkozása |
    | --- | --- |
    | Egyesült Államok | [Hivatkozás](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kína | [Hivatkozás](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Egy Azure-tárfiók a Resource Manager telepítési modell jött létre az előfizetés, amelyből letöltötte a közzétételi beállításfájlt.

  ![Tárfiók létrehozása az Erőforrás-kezelő fejlesztésével](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* Létrejön egy átmeneti hely, amely lehet hálózati megosztás vagy a számítógép bármely további meghajtója, belső vagy külső, és elegendő lemezterület áll a kezdeti példány tárolására. Ha például egy 500 GB-os fájlkiszolgálóról szeretne biztonsági másolatot tenni, győződjön meg arról, hogy az átmeneti terület legalább 500 GB. (A tömörítés miatt kisebb mennyiséget használ.)
* Az Azure-ba küldött lemezek esetében győződjön meg arról, hogy csak 2,5 hüvelykes SSD vagy 2,5 hüvelykes vagy 3,5 hüvelykes SATA II/III belső merevlemezek használatosak. Legfeljebb 10 TB-os merevlemezt használhat. Ellenőrizze az [Azure import/export szolgáltatás dokumentációjában](../storage/common/storage-import-export-requirements.md#supported-hardware) a szolgáltatás által támogatott legújabb meghajtókat.
* A SATA-meghajtókat olyan számítógéphez *(másolt számítógéphez)* kell csatlakoztatni, ahonnan a biztonsági mentési adatok másolata az átmeneti helyről a SATA-meghajtókra történik. Győződjön meg arról, hogy a BitLocker engedélyezve van a másolószámítógépen.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>A kiszolgáló előkészítése az offline biztonsági mentési folyamatra

>[!NOTE]
> Ha nem találja a felsorolt segédprogramok, például *az AzureOfflineBackupCertGen.exe*, a AskAzureBackupTeam@microsoft.com marsügynök telepítése, írjon, hogy hozzáférjen hozzájuk.

* Nyisson meg egy rendszergazda jogú parancssort a kiszolgálón, és futtassa a következő parancsot:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Az eszköz létrehoz egy Azure Offline Backup Active Directory alkalmazást, ha nem létezik.

    Ha egy alkalmazás már létezik, ez a végrehajtható kéri, hogy manuálisan töltse fel a tanúsítványt az alkalmazása a bérlőben. Az ebben a [szakaszban](#manually-upload-an-offline-backup-certificate) leírt lépéseket követve manuálisan töltheti fel a tanúsítványt az alkalmazásba.

* Az *AzureOfflineBackup.exe* eszköz *offlineapplicationparams.xml* fájlt hoz létre. Másolja a fájlt a kiszolgálóra MABS vagy DPM segítségével.
* Telepítse a [legújabb MARS-ügynököt](https://aka.ms/azurebackup_agent) a DPM-példányra vagy az Azure Backup kiszolgálóra.
* Regisztrálja a kiszolgálót az Azure-ba.
* Futtassa az alábbi parancsot:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* Az előző parancs `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`létrehozza a fájlt .

## <a name="manually-upload-an-offline-backup-certificate"></a>Kapcsolat nélküli biztonsági másolat manuális feltöltése

Az alábbi lépésekkel manuálisan töltse fel az offline biztonsági mentési tanúsítványt egy korábban létrehozott Azure Active Directory-alkalmazásoffline biztonsági mentésre.

1. Jelentkezzen be az Azure portálra.
1. Nyissa meg az **Azure Active Directory** > **alkalmazásregisztrációit.**
1. A **Saját alkalmazások** lapon keresse meg a `AzureOfflineBackup _<Azure User Id`megjelenítendő névformátumú alkalmazást.

    ![Alkalmazás megkeresése a Saját alkalmazások lapon](./media/backup-azure-backup-import-export/owned-applications.png)

1. Válassza ki az alkalmazást. A bal oldali ablaktábla **Kezelés csoportban** nyissa meg **a Tanúsítványok & titkos kulcsok**című.
1. Ellenőrizze, hogy vannak-e már meglévő tanúsítványok vagy nyilvános kulcsok. Ha nincs ilyen, nyugodtan törölheti az alkalmazást az alkalmazás **Áttekintés** lapján a **Törlés** gombra kattintva. Ezután próbálkozzon újra a kiszolgáló [kapcsolat nélküli biztonsági mentési folyamatra való előkészítésével,](#prepare-the-server-for-the-offline-backup-process) majd hagyja ki a következő lépéseket. Ellenkező esetben továbbra is kövesse ezeket a lépéseket a DPM-példányból vagy az Azure Backup kiszolgálóról, ahol offline biztonsági mentést szeretne konfigurálni.
1. Válassza a **Számítógép-tanúsítványalkalmazás** > **kezelése Személyes** lap lehetőséget. `CB_AzureADCertforOfflineSeeding_<ResourceId>`
1. Jelölje ki a tanúsítványt, kattintson a jobb gombbal **az Összes feladat parancsra,** majd válassza az **Exportálás**parancsot titkos kulcs nélkül a .cer formátumban.
1. Nyissa meg az Azure offline biztonsági mentési alkalmazást az Azure Portalon.
1. Válassza **a** > Tanúsítványok kezelése **& titkos kulcsok** > **feltöltése tanúsítványt.** Töltse fel az előző lépésben exportált tanúsítványt.

    ![A tanúsítvány feltöltése](./media/backup-azure-backup-import-export/upload-certificate.png)

1. A kiszolgálón nyissa meg a rendszerleíró adatbázist úgy, hogy beírja a **regeditet** a futtatási ablakba.
1. Nyissa meg a Rendszerleíró adatbázis *Számítógép\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*című bejegyzést.
1. Kattintson a jobb gombbal **a CloudBackupProvider** `AzureADAppCertThumbprint_<Azure User Id>`elemre, és adjon hozzá egy új karakterláncértéket a nevével.

    >[!NOTE]
    > Az Azure felhasználói azonosítójának megkereséséhez tegye az alábbi lépéseket:
    >
    >* Az Azure-hoz csatlakoztatott PowerShell, futtassa a `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` parancsot.
    >* Nyissa meg a `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`rendszerleíró adatbázis elérési útját .

1. Kattintson a jobb gombbal az előző lépésben megadott karakterláncra, és válassza a **Módosítás parancsot.** Az értékben adja meg a 7. Ezután kattintson az **OK** gombra.
1. Az ujjlenyomat értékének leigazolásához kattintson duplán a tanúsítványra. Válassza a **Részletek** lapot, és görgessen lefelé, amíg meg nem jelenik az ujjlenyomatmező. Válassza **az Ujjlenyomat**lehetőséget, és másolja az értéket.

    ![Érték másolása az ujjlenyomatmezőből](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. Folytassa a [Munkafolyamat](#workflow) szakaszsal az offline biztonsági mentési folyamat folytatásához.

## <a name="workflow"></a>Munkafolyamat

Az ebben a szakaszban található információk segítségével befejezheti az offline biztonsági mentési munkafolyamatot, így az adatok egy Azure-adatközpontba kézbesíthetők és feltölthetők az Azure Storage-ba. Ha kérdése van az importálási szolgáltatással vagy a folyamat bármely aspektusával kapcsolatban, olvassa el a korábban hivatkozott [Import szolgáltatás áttekintő dokumentációját.](../storage/common/storage-import-export-service.md)

### <a name="initiate-offline-backup"></a>Kapcsolat nélküli biztonsági mentés kezdeményezése

1. Biztonsági mentés ütemezésekor a következő lap jelenik meg a Windows Server rendszerben, egy Windows-ügyfélben vagy dpm-ben.

    ![Lap importálása](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Itt a megfelelő oldal a DPM-ben. <br/>
    
    ![A DPM és az Azure Backup Server importálási lapja](./media/backup-azure-backup-import-export/dpmoffline.png)

    A kitöltett mezők a következők:

   * **Átmeneti hely**: Az az ideiglenes tárolóhely, ahhoz az ideiglenes tárolóhelyhez, ahhoz a kezdeti biztonsági másolathoz. Lehet, hogy az átmeneti hely hálózati megosztáson vagy helyi számítógépen található. Ha a másoló számítógép és a forrásszámítógép eltérő, adja meg az átmeneti hely teljes hálózati elérési útját.
   * **Azure közzétételi beállítások:** A közzétételi beállításfájl helyi elérési útja.
   * **Azure importálási feladat neve:** Az az egyedi név, amellyel az Azure import/export szolgáltatás és az Azure Backup nyomon követi a lemezeken küldött adatok azure-ba.
   * **Azure-előfizetés-azonosító:** Az Azure-előfizetés-azonosító az előfizetés, ahonnan letöltötte az Azure közzétételi beállításfájlt.
   * **Azure Storage-fiók:** A tárfiók neve az Azure-előfizetéshez társított Azure közzétételi beállításfájl.
   * **Azure Storage Container:** A céltárblob neve az Azure storage-fiókban, ahol a biztonsági mentési adatok importálása történik.

   Mentse az **átmeneti helyet** és az Azure **import feladatnév** megadott adatait. Elő kell készíteni a lemezeket.

1. Fejezze be a munkafolyamatot. Az offline biztonsági másolat elindításához válassza a **Biztonsági mentés most** lehetőséget az Azure Backup Agent felügyeleti konzolon. A kezdeti biztonsági mentés ebben a lépésben az átmeneti területre kerül.

    ![Biztonsági mentés most](./media/backup-azure-backup-import-export/backupnow.png)

    A megfelelő munkafolyamat dpm-ben vagy az Azure Backup Serverben való befejezéséhez kattintson a jobb gombbal a **Védelmi csoportra.** Válassza a **Helyreállítási pont létrehozása** lehetőséget. Ezután válassza az **Online védelem** lehetőséget.

    ![DPM és MABS biztonsági másolatot kell kapnia](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    A művelet befejezése után az átmeneti hely készen áll a lemez előkészítéséhez való használatra.

    ![Biztonsági mentés folyamata](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA-meghajtók előkészítése és szállítás az Azure-ba

Az *AzureOfflineBackupDiskPrep* segédprogram a legközelebbi Azure-adatközpontba küldött SATA-meghajtók előkészítésére szolgál. Ez a segédprogram a Helyreállítási szolgáltatások ügynökének telepítési könyvtárában érhető el a következő elérési úton:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Nyissa meg a könyvtárat, és másolja az *AzureOfflineBackupDiskPrep* könyvtárat egy olyan másolószámítógépre, amelyen az előkészítendő SATA-meghajtók csatlakoznak. Győződjön meg arról, hogy:

   * A másolószámítógép az "Offline biztonsági mentés kezdeményezése" szakaszban a munkafolyamatban megadott hálózati elérési úttal érheti el az offline vetési munkafolyamat átmeneti helyét.
   * A BitLocker engedélyezve van a másolószámítógépen.
   * A másolószámítógép hozzáférhet az Azure Portalhoz. Szükség esetén a másolószámítógép megegyezhet a forrásszámítógéppel.

     > [!IMPORTANT]
     > Ha a forrásszámítógép virtuális gép, akkor egy másik fizikai kiszolgálót vagy ügyfélgépet kell használni másolószámítógépként.

1. Nyisson meg egy rendszergazda jogú parancssort a másolt számítógépen, amelyen az *AzureOfflineBackupDiskPrep* segédprogram könyvtára az aktuális könyvtár. Futtassa az alábbi parancsot:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Paraméter | Leírás |
    | --- | --- |
    | s:&lt;*Átmeneti hely elérési útja*&gt; |Ez a kötelező bemenet a "Kapcsolat nélküli biztonsági mentés kezdeményezése" szakaszban a munkafolyamatban megadott átmeneti hely elérési útját biztosítja. |
    | p:&lt;*Közzétételi beállítások fájl elérési útja*&gt; |Ez a választható bemenet az Azure közzétételi beállításfájlelérési útjának biztosításához használható, amelyet a "Kapcsolat nélküli biztonsági mentés kezdeményezése" szakaszban a munkafolyamatban megadott. |

    > [!NOTE]
    > Az &lt;AzurePublishSettingFile&gt; elérési útja kötelező, ha a másoló számítógép és a forrásszámítógép eltérő.
    >
    >

    A parancs futtatásakor a segédprogram kéri az Azure importálási feladat kiválasztását, amely megfelel az előkell készíteni meghajtók. Ha csak egyetlen importálási feladat van társítva a megadott átmeneti helyhez, akkor egy ehhez hasonló oldal jelenik meg.

    ![Az Azure lemezelőkészítő eszköz bemenete](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Adja meg a meghajtó betűjelét az Azure-ba való átvitelre előkészíteni kívánt csatlakoztatott lemez záró kettőspontja nélkül. Amikor a rendszer kéri, erősítse meg a meghajtó formázását.

    Az eszköz ezután megkezdi a lemez előkészítését és a biztonsági mentési adatok másolását. Előfordulhat, hogy az eszköz által kért rákérdezéskor további lemezeket kell csatolnia, ha a megadott lemeznem rendelkezik elegendő hellyel a biztonsági mentési adatok számára. <br/>

    Az eszköz sikeres befejezése után egy vagy több megadott lemez előkészítve lesz az Azure-ba való szállításra. Az "Offline biztonsági mentés kezdeményezése" szakaszban a munkafolyamat során megadott névvel rendelkező importálási feladat is létrejön az Azure-ban. Végül az eszköz megjeleníti a szállítási címet az Azure-adatközpontba, ahol a lemezeket kell szállítani.

    ![Az Azure lemez előkészítése befejeződött](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. A parancs végrehajtásának végén megjelenik a szállítási információk frissítésének lehetősége is.

    ![Szállítási információk frissítési lehetőség](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. A részleteket azonnal megadhatja. Az eszköz végigvezeti a folyamaton, amely egy sor bemenetet foglal magában. Ha nem rendelkezik olyan információkkal, mint például a fuvarlevélszám vagy a szállítással kapcsolatos egyéb adatok, akkor befejezheti a munkamenetet. A szállítási részletek későbbi frissítésének lépéseit ez a cikk tartalmazza.

1. A lemezeket az eszköz által megadott címre szállítsa. Őrizd meg a nyomon követési számot későbbi használatra.

   > [!IMPORTANT]
   > Nincs két Azure-importálási feladat azonos követési számmal. Győződjön meg arról, hogy a segédprogram által egyetlen Azure-importálási feladat keretében készített meghajtókat egyetlen csomagban szállítják együtt, és hogy a csomaghoz egyetlen egyedi nyomon követési szám van. Ne kombinálja a különböző Azure-importálási feladatok részeként egyetlen csomagban készített meghajtókat.

1. Ha rendelkezik a nyomon követési számadataival, lépjen a forrásszámítógéphez, amely az importálási feladat befejezésére vár. Futtassa a következő parancsot egy rendszergazda jogú parancssorban az *AzureOfflineBackupDiskPrep* segédprogram könyvtárával az aktuális könyvtárként.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   A következő parancsot tetszés szerint futtathatja egy másik számítógépről, például a másolószámítógépről, és az *AzureOfflineBackupDiskPrep* segédprogram könyvtára az aktuális könyvtár.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Paraméter | Leírás |
    | --- | --- |
    | U: | Ez a kötelező bemenet egy Azure-importálási feladat szállítási részleteinek frissítésére szolgál. |
    | s:&lt;*Átmeneti hely elérési útja*&gt; | Ez a kötelező bevitel akkor használatos, ha a parancs nem fut a forrásszámítógépen. Arra szolgál, hogy megadja az "Offline biztonsági mentés kezdeményezése" szakaszban a munkafolyamatban megadott átmeneti hely elérési útját. |
    | p:&lt;*Közzétételi beállítások fájl elérési útja*&gt; | Ez a kötelező bevitel akkor használatos, ha a parancs nem fut a forrásszámítógépen. Az Azure közzétételi beállításfájljának elérési útját biztosítja, amelyet a munkafolyamatban a "Kapcsolat nélküli biztonsági mentés kezdeményezése" szakaszban adott meg. |

    A segédprogram automatikusan észleli azt az importálási feladatot, amelyen a forrásszámítógép várakozik, vagy az átmeneti helyhez társított importálási feladatokat, amikor a parancsot egy másik számítógépen futtatja. Ezután lehetővé teszi a szállítási információk frissítését egy sor bemeneten keresztül.

    ![Szállítási adatok megadása](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. Miután az összes bemenet megvan adva, alaposan tekintse át a részleteket, és véglegesítse a megadott szállítási információkat az **igen**.

    ![Szállítási információk áttekintése](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. A szállítási adatok sikeres frissítése után a segédprogram helyi helyet ad meg, ahol a megadott szállítási adatokat tárolják.

    ![Szállítási információk tárolása](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a meghajtók az *AzureOfflineBackupDiskPrep* segédprogram használatával két héten belül elérik az Azure-adatközpontot. Ennek elmulasztása a meghajtók feldolgozásának elmaradását eredményezheti. 

Miután befejezte az előző lépéseket, az Azure-adatközpont készen áll a meghajtók fogadására, és további feldolgozási azok a meghajtókról a meghajtókról a klasszikus típusú Azure storage-fiók hoz létre.

### <a name="time-to-process-the-drives"></a>A meghajtók feldolgozásának ideje

Az Azure-importálási feladat feldolgozásához szükséges idő változó. A feldolgozási idő olyan tényezőktől függ, mint a szállítási idő, a feladat típusa, a másolt adatok típusa és mérete, valamint a rendelkezésre bocsátott lemezek mérete. Az Azure importálási/exportálási szolgáltatás nem rendelkezik SLA-val. A lemezek fogadása után a szolgáltatás arra törekszik, hogy 7–10 napon belül befejezze a biztonsági mentési adatok másolatát az Azure storage-fiókjába. A következő szakasz ismerteti, hogyan figyelheti az Azure importálási feladat állapotát.

### <a name="monitor-azure-import-job-status"></a>Az Azure importálási feladatállapotának figyelése

Amíg a meghajtók átvitel alatt vannak, vagy az Azure-adatközpontban a tárfiókba másolva, az Azure Backup Agent vagy a DPM vagy a MABS konzol a forrásszámítógépen a következő feladatállapotot jeleníti meg az ütemezett biztonsági mentések esetében:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Az importálási feladat állapotának ellenőrzése:

1. Nyisson meg egy rendszergazda jogú parancssort a forrásszámítógépen, és futtassa a következő parancsot:

     `AzureOfflineBackupDiskPrep.exe u:`

1. A kimenet az importálási feladat aktuális állapotát mutatja.

    ![Az importálási feladat állapotának ellenőrzése](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Az Azure importálási feladat különböző állapotairól [az Azure importálási/exportálási feladatok állapotának megtekintése](../storage/common/storage-import-export-view-drive-status.md)című témakörben talál további információt.

### <a name="finish-the-workflow"></a>A munkafolyamat befejezése

Az importálási feladat befejezése után a kezdeti biztonsági mentési adatok elérhetők a tárfiókban. A következő ütemezett biztonsági mentés időpontjában az Azure Backup átmásolja az adatok tartalmát a tárfiókból a Recovery Services-tárolóba.

   ![Adatok másolása a Recovery Services tárolóba](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

A következő ütemezett biztonsági mentés időpontjában az Azure Backup növekményes biztonsági mentést hajt végre a kezdeti biztonsági másolaton keresztül.

## <a name="next-steps"></a>További lépések

* Az Azure importálási/exportálási szolgáltatásmunkafolyamatával kapcsolatos kérdéseiről [a Microsoft Azure importálási/exportálási szolgáltatáshasználata a Blob storage-ba történő átvitele](../storage/common/storage-import-export-service.md)című témakörben nyújt fel.
