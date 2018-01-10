---
title: "Az Azure Backup - Offline biztonsági másolat vagy az Azure Import/Export szolgáltatás használatával kezdeti összehangolása |} Microsoft Docs"
description: "Ismerje meg, hogyan Azure Backup szolgáltatás lehetővé teszi az Azure Import/Export szolgáltatás használata a hálózati adatokat küldeni. Ez a cikk ismerteti a kezdeti biztonsági másolati adatokat kapcsolat nélküli összehangolása az Azure importálási exportálása szolgáltatás használatával."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/18/2017
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 32a48a34711a7f053a74e103deb6853150de3903
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Offline biztonsági mentési munkafolyamat az Azure Backupban
Azure biztonsági mentés számos beépített hatékonyság, hálózati és tárolási költségek csökkentése során a kezdeti teljes biztonsági mentés az Azure-bA rendelkezik. Kezdeti teljes biztonsági mentés általában nagy adatmennyiségek átvitelét, és azt követő biztonsági mentéseket, amely csak az eltérések/növekményes átviteli képest nagyobb hálózati sávszélesség szükséges. Azure biztonsági mentés tömöríti a kezdeti biztonsági mentéseket. A folyamatot, amely offline összehangolása Azure biztonsági mentési lemezek is használhatók a tömörített kezdeti biztonsági másolati adatokat kapcsolat nélküli feltöltése az Azure-bA.  

A kapcsolat nélküli összehangolása folyamat Azure biztonsági mentés szorosan integrálva van a [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md) , amely lehetővé teszi az adatok átvitele Azure lemezek használatával. Ha terabájt (több TB-nyi), amelyet a nagy késleltetésű és kis sávszélességű hálózaton keresztül kell átvinni kezdeti biztonsági másolati adatokat, a kapcsolat nélküli összehangolása munkafolyamat segítségével küldje el a kezdeti biztonsági másolatot egy vagy több merevlemezeken való egy Azure-adatközpontban. Ez a cikk áttekintést a lépéseket, amelyek a munkafolyamat befejezéséhez.

## <a name="overview"></a>Áttekintés
Azure biztonsági mentés és az Azure Import/Export offline összehangolása alkalmas használata egyszerű offline állapotba az adatok feltöltése az Azure-bA lemezek használatával. Ahelyett, hogy a kezdeti teljes másolat átvitele a hálózaton keresztül, a biztonsági mentési adatokat írni egy *átmeneti helyre*. Az Azure Import/Export eszköz használatával az ideiglenes helyet a Másolás befejezése után ezek az adatok beíródik egy vagy több SATA meghajtókat, az adatok mennyiségétől függően. Ezek a meghajtók végül szállított a legközelebbi Azure-adatközpontban.

A [2016 augusztusától Azure biztonsági mentés (és újabb) frissítése](http://go.microsoft.com/fwlink/?LinkID=229525) magában foglalja a *Azure lemez előkészítő eszköz*, AzureOfflineBackupDiskPrep, nevű, amelyek:

* Segít a meghajtók előkészítése az Azure Import az Azure Import/Export eszköz használatával.
* Hozza létre automatikusan az Azure importálási feladattal az Azure Import/Export szolgáltatás a a [Azure-portálon](https://ms.portal.azure.com).

Az Azure a biztonsági mentési adatok feltöltése után Azure biztonsági mentési másolja át a biztonsági mentési adatok a biztonsági mentési tárolóba, és a növekményes biztonsági mentés van ütemezve.

> [!NOTE]
> Az Azure lemez előkészítő eszköz használatához győződjön meg arról, hogy az Azure biztonsági mentés (vagy újabb) 2016 augusztusától frissítést telepített, és a munkafolyamat az összes lépés elvégzését. Azure biztonsági mentés egy régebbi verzióját használja, ha a SATA-meghajtó előkészítheti az Azure Import/Export eszköz használatával részletes, ez a cikk későbbi szakaszokban.
>
>

## <a name="prerequisites"></a>Előfeltételek
* [Ismerkedjen meg az Azure Import/Export munkafolyamat](../storage/common/storage-import-export-service.md).
* A munkafolyamat kezdeményezése, előtt ellenőrizze a következőket:
  * Létrejött az Azure Backup-tárolóban.
  * Letöltött tárolói hitelesítő adatokat.
  * Az Azure Backup szolgáltatás ügynöke telepítve van a Windows Server és Windows-ügyfélen vagy a System Center Data Protection Manager-kiszolgálón, és a számítógép regisztrálva van az Azure Backup-tárolóban.
* [A fájl Azure közzétételi beállítások letöltése](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) használni kívánt biztonsági másolatot az adatairól a számítógépen.
* Készítsen elő egy ideiglenes helyet, amely lehet, hogy egy hálózati megosztásra vagy további meghajtó a számítógépen. Az ideiglenes helyet az átmeneti tároló, és a munkafolyamat ideiglenesen során használt. Győződjön meg arról, hogy rendelkezik-e elég szabad lemezterület ahhoz, hogy a kezdeti másolatot az ideiglenes helyet. Például ha próbál 500 GB-os fájlkiszolgáló biztonsági mentése, győződjön meg arról, hogy az átmeneti területre legalább 500 GB. (Tömörítés miatt a kisebb méretű használja.)
* Győződjön meg arról, hogy támogatott meghajtó használ. 2,5 hüvelykes csak SSD vagy 2.5-ös vagy 3,5 hüvelykes SATA II/III belső merevlemez-meghajtók támogatottak az Import/Export szolgáltatás való használatra. Merevlemez-meghajtókat is használhat legfeljebb 10 TB. Ellenőrizze a [Azure Import/Export szolgáltatás dokumentációja](../storage/common/storage-import-export-service.md#hard-disk-drives) meghajtók, a szolgáltatás által támogatott legújabb készlete esetében.
* A BitLocker engedélyezése a számítógépen, amelyre a SATA-meghajtó író csatlakoztatva van.
* [Töltse le az Azure Import/Export eszköz](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) arra a számítógépre, amelyre a SATA-meghajtó író csatlakoztatva van. Ez a lépés nincs szükség, ha már letöltötte és az Azure biztonsági mentés (vagy újabb) 2016 augusztusától frissítés telepítése.

## <a name="workflow"></a>Munkafolyamat
Ebben a szakaszban található információk segítséget, hogy az adatok egy Azure-adatközpontban kézbesített, és az Azure-tárhelyre feltöltött, végezze el az offline biztonsági másolat munkafolyamat. Ha az importálás szolgáltatás vagy a folyamat minden szempontját kérdése van, tekintse meg a [importálási áttekintés](../storage/common/storage-import-export-service.md) korábban hivatkozott dokumentációját.

### <a name="initiate-offline-backup"></a>Offline biztonsági mentése
1. Ha a biztonsági mentés ütemezése, megjelenik a következő képernyő (a Windows Server, a Windows ügyfél vagy a System Center Data Protection Manager).

    ![Importálás képernyőn](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Ez a megfelelő képernyőn a System Center Data Protection Manager: <br/>
    ![A DPM importálás képernyőn](./media/backup-azure-backup-import-export/dpmoffline.png)

    Leírja, hogy a bemeneti adatok a következőképpen történik:

    * **Átmeneti hely**:, amelyhez a kezdeti biztonsági másolatot írása ideiglenes tárolási helyét. Ez lehet egy hálózati megosztásra vagy a helyi számítógépen. A másolási számítógép és a forrásszámítógép nem egyezik, azt javasoljuk, hogy megadja a teljes elérési útja az átmeneti tárolási helyét.
    * **Az Azure importálási feladat nevének**: mely Azure Import alapján szolgáltatás és az Azure Backup követésére küldött adatok továbbítása lemezeken Azure egyedi nevét.
    * **Az Azure közzétételi beállítási**: az XML-fájl, amely tartalmazza az előfizetési profillal kapcsolatos információk. Az előfizetéshez társított biztonságos hitelesítő adatokat is tartalmaz. Is [töltse le a fájlt](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade). Adja meg a közzétételi beállítások fájlja helyi elérési útja.
    * **Az Azure előfizetés-azonosító**: az Azure-előfizetési Azonosítót, az előfizetés, ahonnan az Azure importálási feladatnak kezdeményezni szeretné. Ha több Azure-előfizetéssel rendelkezik, az importálási feladat társítani kívánt az előfizetés Azonosítóját használnia.
    * **Az Azure Storage-fiók**: A tárfiókot az Azure importálási feladattal társított Azure-előfizetésben.
    * **Az Azure Storage-tároló**: a cél tárolási blob, ahol ez a feladat adatok importálása az Azure storage-fiók nevét.

    > [!NOTE]
    > Ha regisztrálta a kiszolgáló egy az Azure Recovery Services-tárolónak a [Azure-portálon](https://portal.azure.com) a biztonsági mentések és a nem Cloud Solution Provider (CSP) előfizetés, létrehozhat egy tárfiókot az Azure-portálon és az offline biztonsági másolat munkafolyamat a használni.
    >
    >

     Mentse ezt az információt, mert adja meg újra a következő lépéseket kell. Csak a *átmeneti helyre* szükség, ha az Azure lemez előkészítő eszköz segítségével készítse elő a lemezeket.    
2. A munkafolyamat befejezéséhez, majd válassza ki **biztonsági másolat készítése most** kezdeményezheti az offline biztonsági másolatot az Azure biztonsági mentési felügyeleti konzolon. A kezdeti biztonsági mentés írása az átmeneti területre, ez a lépés részeként.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/backupnow.png)

    A System Center Data Protection Manager megfelelő munkafolyamat befejezéséhez kattintson a jobb gombbal a **védelmi csoport**, majd válassza a **helyreállítási pont létrehozása** lehetőséget. Ezután válassza ki a **Online védelem** lehetőséget.

    ![Most már a DPM biztonsági mentése](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    A művelet befejezése után az ideiglenes helyet készen áll a lemez előkészítéséhez használható.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>A SATA-meghajtó előkészítése és az Azure importálási feladat létrehozása az Azure lemez előkészítő eszköz használatával
Az Azure lemez előkészítő eszköz érhető el a Recovery Services Agent telepítési könyvtára (2016 augusztusától frissítése és újabb) a következő elérési úton.

   *\Microsoft* *azure* *helyreállítási* *szolgáltatások* * Agent\Utils\*

1. Keresse fel a könyvtárat, és másolja a **AzureOfflineBackupDiskPrep** könyvtárhoz, elő kell készíteni a meghajtó csatlakoztatva vannak másolási számítógépre. Ellenőrizze, hogy a Másolás számítógép tekintetében a következő:

    * A másolási számítógép érhető el az ideiglenes helyet a kapcsolat nélküli összehangolása munkafolyamat azonos hálózati elérési útját, amely lett megadva a **kezdeményezni az offline biztonsági másolat** munkafolyamat.
    * A BitLocker engedélyezve van a számítógépen.
    * A számítógép hozzáférhessen az Azure-portálon.

    Ha szükséges, a másolási számítógép lehet ugyanaz, mint a forrásoldali számítógép.
2. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen az Azure lemez előkészítő eszköz directory könyvtár az aktuális, és futtassa a következő parancsot:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | Paraméter | Leírás |
    | --- | --- |
    | %s&lt;*átmeneti hely elérési útja*&gt; |Kötelező bemeneti, amellyel a megadott átmeneti tárolási helyének elérési útját adja meg a **kezdeményezni az offline biztonsági másolat** munkafolyamat. |
    | p:&lt;*PublishSettingsFile elérési útja*&gt; |Nem kötelező bemeneti, amellyel elérési útját adja meg a **Azure közzétételi beállítási** megadott fájl a **kezdeményezni az offline biztonsági másolat** munkafolyamat. |

    > [!NOTE]
    > A &lt;elérési útját PublishSettingFile&gt; érték megadása kötelező, ha a Másolás számítógép és a forrásszámítógép nem ugyanaz.
    >
    >

    A parancs futtatásakor az eszköz kéri az Azure importálási feladat, amely megfelel a meghajtókat, elő kell készíteni a kijelölést. Ha csak egy egyetlen importálási feladat helyéhez társítva a megadott átmeneti, ahogy az alábbi képernyő jelenik meg.

    ![Az Azure lemez előkészítő eszköz bemeneti](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Adja meg a meghajtóbetűjel, a záró kettőspont nélkül a csatlakoztatott lemezt, amelyet szeretne az Azure-bA átviteli előkészítése. A meghajtó, amikor a rendszer kéri a formázáshoz erősítse meg a műveletet.

    Az eszköz majd megkezdi a lemezt a a biztonsági mentési adatok előkészítéséhez. Szükség lehet további lemezeket, amikor a rendszer kéri az eszközzel, abban az esetben, ha a megadott lemezén nincs elég hely a biztonsági mentési adatok csatlakoztatni. <br/>

    Az eszköz sikeres végrehajtásának végén megadott egy vagy több lemezt az Azure-bA szállítási el kell készíteni. Emellett az importálási feladat nevű során megadott a **kezdeményezni az offline biztonsági másolat** munkafolyamat az Azure portálon jön létre. Végül az eszköz jelenít meg, a Azure adatközpontba, ahol a lemezek kell szállítani a szállítási cím és a hivatkozásra kattintva keresse meg az importálási feladat az Azure portálon.

    ![Teljes Azure lemez előkészítése](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Küldje el a lemezeket, az eszköz a megadott címre, és a jövőben követési számának.<br/>

5. Amikor a hivatkozásra, amely az eszköz jelenik meg, megjelenik az Azure storage-fiók, amelyet a a **kezdeményezni az offline biztonsági másolat** munkafolyamat. Itt tekintheti meg az újonnan létrehozott importálási feladatnak a **IMPORT/EXPORT** a tárfiók fülre.

    ![Létrehozott importálási feladat](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. Kattintson a **szállítási információ** frissíti a kapcsolattartási adatait, az alábbi képernyőn látható az oldal alján. A Microsoft ezeket az adatokat használva küldje el azt a lemezeket, az importálási feladat befejezése után.

    ![Kapcsolattartási adatok](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. Adja meg a szállítási részleteit a következő képernyőn. Adja meg a **kézbesítési szolgáltatónként** és **követési szám** , felel meg a lemezeket, az Azure adatközpontba szállítási szereplő adatokat.

    ![Szállítási adatai](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>A munkafolyamat befejezése
Az importálási feladat befejezése után a tárfiókban lévő kezdeti biztonsági másolati adatokat érhető el. A Recovery Services Agent ügynököt, majd másolja az adatokat erről a fiókról a mentési tárolóhoz vagy a Recovery Services használó, amelyik kell alkalmazni. A következő ütemezett mentési időt az Azure Backup szolgáltatás ügynöke keresztül a kezdeti biztonsági másolatot a növekményes biztonsági mentést hajt végre.

> [!NOTE]
> Az alábbi szakaszok a korábbi Azure biztonsági mentés az Azure lemez előkészítő eszköz hozzáféréssel nem rendelkező felhasználóira alkalmazni.
>
>

### <a name="prepare-a-sata-drive"></a>A SATA-meghajtó előkészítése
1. Töltse le a [Microsoft Azure Import/Export eszköz](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) a másolási számítógépre. Győződjön meg arról, hogy elérhető a számítógépen, amelyen futtassa a következő parancsokat, hogy az átmeneti tárolási helyét. Ha szükséges, a másolási számítógép lehet ugyanaz, mint a forrásoldali számítógép.

2. Bontsa ki a WAImportExport.zip fájlt. Futtassa a WAImportExport eszközt, amely formázza a SATA-meghajtó, a SATA-meghajtóra ír, a biztonsági mentési adatokat, és titkosítja azokat. A következő parancs futtatása előtt győződjön meg arról, hogy a BitLocker engedélyezve van-e a számítógépen. <br/>

    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`

    > [!NOTE]
    > Ha az Azure biztonsági mentés (vagy újabb) 2016 augusztusától frissítést telepített, gondoskodjon arról, hogy a megadott átmeneti hely ugyanaz, mint a **biztonsági másolat készítése most** képernyőn és AIB és talál Blob fájlokat tartalmazza.
    >
    >

| Paraméter | Leírás |
| --- | --- |
| /j: <*JournalFile*> |A naplófájl elérési útja. Minden olyan meghajtó meg kell adni pontosan egy napló fájlt. A naplófájl nem a cél-meghajtón kell lennie. A napló fájlnévkiterjesztés .jrn, és ez a parancs futtatásának részeként jön létre. |
| /ID: <*munkamenet-azonosító*> |A munkamenet-azonosító egy másolás munkamenet azonosítja. A pontos egy megszakított másolási munkamenet-helyreállítás szolgál. Másolás munkamenetben másolt fájlok nevű után a munkamenet-azonosító a célként megadott meghajtón tárolják. |
| /SK: <*StorageAccountKey*> |A tárfiók, amelyre az adatok importálása a fiókkulcs. A kulcsot kell azonosnak lennie, mint a biztonsági mentési házirend, védelmi csoport létrehozásakor megadott. |
| / BlobType |A blob típusú. Ez a munkafolyamat sikeres lesz, ha csak **PageBlob** van megadva. Ez a lehetőség nem alapértelmezett, és a parancs kell említeni. |
| / t: <*TargetDriveLetter*> |A meghajtóbetűjelet, a cél merevlemez a jelenlegi példány munkamenetre záró kettőspont nélkül. |
| Format |Formázza a beállítás. Adja meg ezt a paramétert, ha a meghajtó kell formázni; Ellenkező esetben hagyja ki ezt az. Az eszköz formázza a meghajtón, mielőtt a rendszer kérni fogja a konzolról egy megerősítő üzenet. A megerősítő letiltásához, adja meg a /silentmode paramétert. |
| / titkosítása |A beállítás a meghajtó titkosítására. Adja meg ezt a paramétert, a meghajtón a BitLocker még nincs titkosítva, és a eszköz által titkosítani kell. Ha a meghajtó már titkosítása a BitLocker kihagyja ezt a paramétert, adja meg a /bk paramétert, és adja meg a meglévő BitLocker-kulcsot. Ha a Format paraméter megadása esetén is meg kell adnia a / paraméter titkosítása. |
| /srcdir: <*SourceDirectory*> |A célmeghajtó átmásolni a fájlokat tartalmazó forráskönyvtárat. Győződjön meg arról, hogy a megadott könyvtárnév rendelkezik-e egy teljes, hanem relatív elérési utat. |
| /dstdir: <*DestinationBlobVirtualDirectory*> |A könyvtár elérési útja a cél virtuális az Azure storage-fiók. Győződjön meg arról, ha megadja a cél virtuális könyvtárak vagy blobok használhatnak a érvényes tároló. Ne feledje, hogy a tároló nevének kisbetűnek kell lennie.  A tároló neve, amelyet a biztonsági mentési házirend, védelmi csoport létrehozása során megadott kell lennie. |

> [!NOTE]
> A napló fájl a teljes információt a munkafolyamat WAImportExport mappában jön létre. Ezt a fájlt az importálási feladat létrehozásakor az Azure portálon kell.
>
>

  ![A PowerShell eredménye](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>Az importálási feladat létrehozása az Azure portálon
1. Keresse fel a storage-fiókot a [Azure-portálon](https://ms.portal.azure.com/), kattintson a **Import/Export**, majd **importálási feladat létrehozása** feladat panelen.

    ![Importálási/exportálási fülre az Azure-portálon](./media/backup-azure-backup-import-export/azureportal.png)

2. Az 1. lépésben a varázsló azt jelzi, hogy előkészítette a meghajtót, és hogy van-e a meghajtó naplófájl.

3. 2. lépése a varázslót adja meg az importálási feladat felelős személy kapcsolattartási adatait.

4. A 3. lépésben az előző szakaszban beszerzett meghajtó Adatbázisnapló-fájlok feltöltése.

5. A 4. lépésben adjon meg egy leíró nevet a biztonsági mentési házirend, védelmi csoport létrehozása során megadott importálási feladat. A beírt név csak kisbetűket, számokat, kötőjeleket tartalmazhat, és aláhúzásjeleket tartalmazhat, betűvel kell kezdődnie, és nem tartalmazhat szóközt. A név az Ön által segítségével nyomon követheti a feladatok, amikor folyamatban van, és azok befejezése után.

6. Ezután válassza ki a datacenter régió a listából. A datacenter régió azt jelzi, az adatközpontok és a címet, amelyre kell küldje el a csomagot.

    ![Válassza ki a datacenter régió](./media/backup-azure-backup-import-export/dc.png)

7. 5. lépésben a visszatérési szolgáltatónként válasszon a listából, és adja meg a szolgáltatója. Microsoft küldje el azt a meghajtók, az importálási feladat befejezése után ezt a fiókot használja.

8. Küldje el a lemezt, és adja meg a szállítási állapotának nyomon követési számát. A lemez megérkeznek az adatközpontban, miután a tárolási fiók másolás, és állapota frissül.

    !["Befejezve" állapota](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>A munkafolyamat befejezése
Miután a kezdeti biztonsági másolati adatokat a tárfiókban lévő érhető el, a Microsoft Azure Recovery Services Agent ügynököt az adatok tartalmának másolása a fiók a mentési tárolóba vagy Recovery Services-tároló, amelyik megfelelő. A következő biztonsági mentési ütemezést az Azure Backup szolgáltatás ügynöke hajtja végre a növekményes biztonsági mentés alatt a kezdeti biztonsági másolatot.

## <a name="next-steps"></a>További lépések
* Az Azure Import/Export munkafolyamat kapcsolatos kérdéseket, tekintse meg a [adatok átviteléhez a Blob storage a Microsoft Azure Import/Export szolgáltatás használata](../storage/common/storage-import-export-service.md).
* Tekintse át az offline biztonsági másolat részt, az Azure biztonsági mentési [gyakran ismételt kérdések](backup-azure-backup-faq.md) a munkafolyamat kapcsolatos kérdéseivel.
