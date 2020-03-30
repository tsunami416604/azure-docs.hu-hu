---
title: Adatok Azure-fájlokba való átvitelének használata az Azure-importálással/exportálással | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre importálási feladatokat az Azure Portalon az Azure Files adatok átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 201d0c0a545c5ba7ae1bb0b5e119f7acb1ae362f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268300"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Az Azure Import/Export szolgáltatás használata az adatok Azure Filesba történő importálásához

Ez a cikk lépésenként bemutatja, hogyan használhatja az Azure import/export szolgáltatást nagy mennyiségű adat biztonságos importálásához az Azure Files-ba. Adatok importálásához a szolgáltatás megköveteli, hogy az adatokat tartalmazó támogatott lemezmeghajtókat egy Azure-adatközpontba szállítsa.  

Az importálási/exportálási szolgáltatás csak az Azure-fájlok importálását támogatja az Azure Storage-ba. Az Azure-fájlok exportálása nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehozna egy importálási feladatot az adatok Azure Files-ba való átviteléhez, alaposan tekintse át és töltse ki az előfeltételek alábbi listáját. A következőket kell tennie:

- Aktív Azure-előfizetéssel kell használnia az Importálás/exportálás szolgáltatással.
- Legalább egy Azure Storage-fiókkal rendelkezik. Tekintse meg az [Importálási/Exportálás szolgáltatás támogatott tárfiókjainak és tárolásitípusainak](storage-import-export-requirements.md)listáját. Az új tárfiók létrehozásáról a [Tárfiók létrehozása című témakörben talál](storage-account-create.md)további információt.
- Megfelelő számú [támogatott](storage-import-export-requirements.md#supported-disks)típusú lemezzel rendelkezik.
- [Támogatott operációsrendszer-verziót](storage-import-export-requirements.md#supported-operating-systems)futtató Windows rendszerrel kell rendelkeznie.
- [Töltse le a WAImportExport 2-es verzióját](https://aka.ms/waiev2) a Windows rendszerre. Csomagoljon ki az `waimportexport`alapértelmezett mappába . Például: `C:\WaImportExport`.
- Van FedEx/DHL-számlája. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, lépjen `adbops@microsoft.com`kapcsolatba az Azure Data Box Operations csapatával a.  
    - A számlának érvényesnek kell lennie, egyenleggel kell rendelkeznie, és visszaszállítási képességekkel kell rendelkeznie.
    - Követési szám létrehozása az exportálási feladathoz.
    - Minden feladatnak külön nyomon követési számmal kell rendelkeznie. Több feladat azonos követési számmal nem támogatott.
    - Ha nem rendelkezik szolgáltatói fiókkal, nyissa meg a következő t:
        - [FedEX-fiók létrehozása](https://www.fedex.com/en-us/create-account.html), vagy
        - [Hozzon létre egy DHL-fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>1. lépés: A meghajtók előkészítése

Ez a lépés naplófájlt hoz létre. A naplófájl olyan alapvető információkat tárol, mint a meghajtó sorozatszáma, a titkosítási kulcs és a tárfiók adatai.

Hajtsa végre az alábbi lépéseket a meghajtók előkészítéséhez.

1. Csatlakoztassa lemezmeghajtóinkat a Windows rendszerhez SATA csatlakozókon keresztül.
2. Hozzon létre egyetlen NTFS-kötetet minden meghajtón. Meghajtóbetűjel hozzárendelése a kötethez. Ne használjon rögzítési pontokat.
3. Módosítsa a *dataset.csv* fájlt abban a gyökérmappában, amelyben az eszköz található. Attól függően, hogy fájlt vagy mappát vagy mindkettőt szeretne-e importálni, a *dataset.csv* fájlba az alábbi példákhoz hasonló bejegyzéseket adhat hozzá.  

   - **Fájl importálása**: A következő példában a másolandó adatok az F: meghajtóban találhatók. A *MyFile1.txt* fájl a *MyAzureFileshare1 gyökérébe*kerül. Ha a *MyAzureFileshare1* nem létezik, az Azure Storage-fiókban jön létre. A mappastruktúra megmarad.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Mappa importálása**: A *MyFolder2* mappában található összes fájlt és mappát rekurzívmódon másolja a fájlmegosztásra. A mappastruktúra megmarad.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Ugyanabban a fájlban több bejegyzés is ellátható, amelyek az importált mappáknak vagy fájloknak felelnek meg.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     További információ [a CSV-fájl adathalmazának előkészítéséről.](storage-import-export-tool-preparing-hard-drives-import.md)


4. Módosítsa a *driveset.csv* fájlt abban a gyökérmappában, amelyben az eszköz található. Az alábbi példákhoz hasonló bejegyzéseket adhat a *driveset.csv* fájlhoz. A meghajtókészlet-fájl tartalmazza a lemezek listáját és a megfelelő meghajtóbetűjeleket, hogy az eszköz helyesen választhassa ki az előkészítendő lemezek listáját.

    Ez a példa feltételezi, hogy két lemez van csatlakoztatva, és az alapvető NTFS-kötetek G:\ és H:\ létrejönnek. A H:\nincs titkosítva, amíg a G: már titkosítva van. Az eszköz formázza és titkosítja a H:\ csak (és nem\)G: .

   - **Nem titkosított lemez esetén**: Adja meg a *Titkosítás megadását* a BitLocker titkosítás engedélyezéséhez a lemezen.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Már titkosított lemez esetén**: Adja meg a Mártitkosított a *lemezt,* és adja meg a BitLocker kulcsot.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Több bejegyzés is eladható ugyanabban a fájlban, amely több meghajtónak felel meg. További információ [a meghajtócsv-fájl előkészítéséről](storage-import-export-tool-preparing-hard-drives-import.md).

5. Ezzel `PrepImport` a beállítással másolhatja és előkészítheti az adatokat a lemezmeghajtóra. Ahhoz, hogy az első másolási munkamenet könyvtárakat és/vagy fájlokat másoljon új másolási munkamenettel, futtassa a következő parancsot:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Az importálási példa alább látható.

       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```

6. A parancssor minden egyes `/j:` futtatásához létrejön egy paraméterrel megadott névvel ellátott naplófájl. Minden előkészített meghajtóhoz van egy naplófájl, amelyet az importálási feladat létrehozásakor fel kell tölteni. A naplófájlok nélküli meghajtók feldolgozása nem történt meg.

    > [!IMPORTANT]
    > - A lemezelőkészítésének befejezése után ne módosítsa a lemezmeghajtókon vagy a naplófájlban lévő adatokat.

További mintákért látogasson el [a Minták a naplófájlokhoz című részre.](#samples-for-journal-files)

## <a name="step-2-create-an-import-job"></a>2. lépés: Importálási feladat létrehozása

Az alábbi lépések végrehajtásával hozzon létre egy importálási feladatot az Azure Portalon.
1. Jelentkezzen be https://portal.azure.com/a rendszerbe.
2. Nyissa meg **a Minden szolgáltatás > Storage > Importálási/exportálási feladatok at.**

    ![Ugrás az Importálás/exportálás ra](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kattintson **az Importálási/exportálási feladat létrehozása gombra.**

    ![Kattintson az Importálási/exportálási feladatra](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Az **alapok:**

    - Válassza **az Importálás az Azure-ba**lehetőséget.
    - Adja meg az importálási feladat leíró nevét. Ezzel a névvel nyomon követheti a feladatokat, amíg azok folyamatban vannak, és miután befejezték.
        -  Ez a név csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket tartalmazhat.
        -  A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközöket.
    - Válasszon egy előfizetést.
    - Válasszon ki egy erőforráscsoportot.

        ![Importálási feladat létrehozása – 1.](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. A **Munka köre**:

    - Töltse fel az előző [1.](#step-1-prepare-the-drives)
    - Válassza ki azt a tárfiókot, amelybe az adatokat importálja.
    - A kitárolási hely automatikusan kitöltődik a kiválasztott tárfiók régiója alapján.

       ![Importálási feladat létrehozása - 2.](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. Cserébe **szállítási információk:**

    - Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, válasszon egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba az Azure `adbops@microsoft.com` Data Box Operations csapatával a használni kívánt szolgáltatóval kapcsolatos információkkal.
    - Adja meg az adott szolgáltatóval létrehozott érvényes szállítmányozói számlaszámot. A Microsoft ezzel a fiókkal szállítja vissza a meghajtókat, amint az importálási feladat befejeződött.
    - Adja meg a kapcsolattartó teljes és érvényes nevét, telefonszámát, e-mail címét, lakcímét, városát, irányítószámát, államát/tartományát és országát/régióját.

        > [!TIP]
        > Ahelyett, hogy egyetlen felhasználó e-mail címét adna meg, adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon akkor is, ha egy rendszergazda kilép.

       ![Importálási feladat létrehozása - 3.](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. Az **összefoglalóban:**

    - Adja meg az Azure-adatközpont szállítási címét a lemezek Azure-ba való visszaküldéséhez. Győződjön meg arról, hogy a feladat neve és a teljes cím szerepel a szállítási címkén.
    - Kattintson **az OK** gombra a feladatlétrehozásának befejezéséhez.

        ![Importálási feladat létrehozása - 4.](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3. lépés: A meghajtók szállítása az Azure adatközpontba

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4. lépés: A feladat frissítése nyomon követési információkkal

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5. lépés: Adatok feltöltésének ellenőrzése az Azure-ba

Kövesse nyomon a feladatot a befejezésig. Miután a feladat befejeződött, ellenőrizze, hogy az adatok feltöltve az Azure-ba. A helyszíni adatok törlése csak azt követően, hogy meggyőződött arról, hogy a feltöltés sikeres volt.

## <a name="samples-for-journal-files"></a>Naplófájlok mintái

**További meghajtók hozzáadásához**hozzon létre egy új meghajtókészlet-fájlt, és futtassa a parancsot az alábbiak szerint.

Az *InitialDriveset .csv* fájlban megadottnál több lemezmeghajtóra való további másolási munkamenetek esetén adjon meg egy `AdditionalDriveSet`új *.csv* meghajtókészlet-fájlt, és adja meg a paraméter értékén. Használja **ugyanazt** a naplófájlnevet, és adjon meg **egy új munkamenet-azonosítót**. Az AdditionalDriveset CSV fájl formátuma megegyezik az InitialDriveSet formátummal.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Az importálási példa alább látható.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Ha további adatokat szeretne hozzáadni ugyanahhoz a meghajtókészlethez, használja a PrepImportálás parancsot a további másolási munkamenetekhez további fájlok/könyvtárak másolásához.

Az *InitialDriveset.csv* fájlban megadott merevlemez-meghajtók további másolási munkameneteihez adja meg ugyanazt a **naplófájlnevet,** és adjon meg egy **új munkamenet-azonosítót**; nincs szükség a tárfiók kulcsának megadására.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Az importálási példa alább látható.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>További lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)
