---
title: Azure Import/Export használatával az adatok átviteléhez az Azure Fileshoz |} Microsoft Docs
description: Megtudhatja, hogyan importálási feladatok létrehozására az Azure portálon adatok átviteléhez az Azure Fileshoz.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 4349b471f960e7844511c473bffcd2177a34e055
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660883"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Adatok importálása az Azure Fileshoz Azure Import/Export szolgáltatás használata

Ez a cikk részletes útmutatást nyújt az Azure Import/Export szolgáltatás használatával biztonságosan rendszerbe való importálás nagy mennyiségű adat Azure fájlokat. Adatok importálása a szolgáltatás megköveteli, hogy küldje el az adatokat, hogy egy Azure-adatközpontban tartalmazó támogatott lemezmeghajtókat.  

Az Import/Export szolgáltatás támogatja csak importálása Azure fájlok az Azure Storage. Azure-fájlok exportálása nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Az importálási feladat adatok átviteléhez az Azure-fájlok létrehozása előtt gondosan tekintse át, és végezze el az alábbi listán szereplő előfeltételek. A következők szükségesek:

- Import/Export szolgáltatás használata az Azure előfizetéssel rendelkezik.
- Rendelkeznie kell legalább egy Azure Storage-fiók. A lista [storage-fiókok és a tárolási típusok támogatott Import/Export szolgáltatás](storage-import-export-requirements.md). Új tárfiók létrehozásával kapcsolatos további információkért lásd: [a Storage-fiók létrehozása](storage-create-storage-account.md#create-a-storage-account).
- A lemezek elegendő számú [támogatott típusok](storage-import-export-requirements.md#supported-disks). 
- Windows operációs rendszert futtató rendelkezik egy [támogatott operációsrendszer-verzió](storage-import-export-requirements.md#supported-operating-systems).
- [Töltse le a 2-es WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) a Windows rendszeren. Bontsa ki az alapértelmezett mappába `waimportexport`. Például: `C:\WaImportExport`.


## <a name="step-1-prepare-the-drives"></a>1. lépés: Felkészülés a meghajtók

Ebben a lépésben létrehoz egy napló fájlt. A napló fájl például meghajtó sorozatszáma, a titkosítási kulcs és a tárfiókadatok alapvető információkat tárolja.

Hajtsa végre az alábbi lépések végrehajtásával készítse elő a meghajtók.

1. A lemezmeghajtók csatlakozni a Windows rendszer SATA összekötők használatával.
2. Minden olyan meghajtó egy NTFS-kötet létrehozása A meghajtóbetűjelet rendelje a köteten. Ne használjon akkor csatlakozási.
3. Módosítsa a *dataset.csv* fájl a gyökérmappában található, amelyen telepítve van. Attól függően, hogy importálása egy fájl vagy mappa vagy mindkettőt, adja hozzá a bejegyzést a *dataset.csv* fájlt az alábbi példák hasonló.  

    - **A fájl importálásához**: a következő példában az adatok másolása a C: meghajtón található. A fájl *MyFile1.txt* gyökérmappájában a rendszer átmásolja a *MyAzureFileshare1*. Ha a *MyAzureFileshare1* nem létezik, az Azure Storage-fiók létrehozták. Gyökérmappa-szerkezetében megmarad.

        ```
            BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
        ```
    - **A mappa importálása**: összes fájlt és mappát *MyFolder2* rekurzív módon másolja a fájlmegosztási. Gyökérmappa-szerkezetében megmarad.

        ```
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
        ```
    Több bejegyzés is végezhető mappáját vagy fájlját importált megfelelő ugyanabban a fájlban. 

        ```
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            "F:\MyFolder3\MyFile3.txt","MyAzureFileshare2/",file,rename,"None",None 
            
        ```
    További információ [a dataset CSV-fájl előkészítése](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Módosítsa a *driveset.csv* fájl a gyökérmappában található, amelyen telepítve van. Adja hozzá a bejegyzést a *driveset.csv* fájlt az alábbi példák hasonló. A driveset fájl rendelkezik a megfelelő meghajtóbetűjeleket és lemezek listáját, úgy, hogy az eszköz megfelelően ki tudja választani készüljön lemezek listáját.

    Ez a példa feltételezi, hogy két lemezek vannak csatolva hozzá, és alapvető NTFS-kötet G:\ és H:\ jönnek létre. H:\is G: már titkosítva van nem titkosítja. Az eszköz formázza, és titkosítja a lemez, amelyen csak H:\ (és nem G:\).

    - **Nem titkosított lemezek esetében**: Adja meg *titkosítása* engedélyezheti a BitLocker titkosítást a lemezen.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        H,Format,SilentMode,Encrypt,
        ```
    
    - **A lemez már titkosított**: Adja meg *AlreadyEncrypted* és adja meg a BitLocker-kulcsot.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
        ```

    Több bejegyzés is végezhető el több meghajtó megfelelő ugyanabban a fájlban. További információ [driveset CSV-fájl előkészítése](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5.  Használja a `PrepImport` lehetőség, hogy másolja, és készítse elő az adatokat a meghajtón. Az első másolás munkamenet könyvtárak és/vagy egy új példány munkamenet-fájlok másolása futtassa a következő parancsot:

        ```
        .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
        ```

    Importálás példát alább láthatók.
  
        ```
        .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
        ```
 
6. A megadott nevű naplófájl `/j:` paraméter, a parancssor minden Futtatás jön létre. Minden olyan meghajtó előkészíti a napló fájlt fel kell tölteni, az importálási feladat létrehozásakor rendelkezik. -Meghajtók nélkül napló fájlok nincsenek feldolgozva.

    > [!IMPORTANT]
    > - Ne módosítsa a merevlemez-meghajtók vagy a napló fájlban lévő adatok lemezét befejezése után.

További példák Ugrás [Adatbázisnapló-fájlok minták](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>2. lépés: Az importálási feladat létrehozása 

A következő lépésekkel importálási feladat létrehozása az Azure portálon.
1. Jelentkezzen be https://portal.azure.com/.
2. Ugrás a **minden szolgáltatás > tárolás > importálási/exportálási feladatok**. 

    ![Ugrás az importálási/exportálási](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kattintson a **létrehozás importálási/exportálási feladatok**.

    ![Kattintson az importálási/exportálási feladatok](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. A **alapjai**:

    - Válassza ki **az Azure importálási**.
    - Adjon meg egy leíró nevet az importálási feladatnak. Ez a név segítségével nyomon követheti a feladatok, amikor folyamatban van, és azok befejezése után.
        -  A név csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket tartalmazhat.
        -  A nevének betűvel kell kezdődnie, és nem tartalmazhat szóközt. 
    - Válasszon egy előfizetést.
    - Válasszon erőforráscsoportot. 

        ![Hozzon létre importálási feladat - 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. A **feladat részletei**:
    
    - Az előző során létrehozott Adatbázisnapló-fájlok feltöltése [1. lépés: készítse elő a meghajtók](#step-1-prepare-the-drives). 
    - Válassza ki a tárfiók, amelyek az adatok importálva. 
    - A gyűjtőtár helyre automatikusan a kiválasztott tárfiók régió alapján van feltöltve.
   
       ![Hozzon létre importálási feladat - 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. A **szállítási információ vissza**:

    - A legördülő listából válassza ki a szolgáltatói.
    - Adjon meg egy érvényes vivőjel-szám, amely adott szolgáltatónként hozott létre. Microsoft küldje el azt a meghajtókat, az importálási feladat befejezése után ezt a fiókot használja. 
    - Adjon teljes és érvényes kapcsolattartójának a neve, telefon, e-mail, utca, házszám, város, zip, az állam/megye ország vagy régió.

       ![Hozzon létre importálási feladat - 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. Az a **összegzés**:

    - Adja meg a szállítási cím szállítási lemezek vissza az Azure-bA az Azure-adatközpontban. Győződjön meg arról, hogy a feladat neve és a teljes címe szerepelnek a szállítási címkén.
    - Kattintson a **OK** importálási feladat létrehozásának befejezéséhez.

        ![Hozzon létre importálási feladat - 4. lépés](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3. lépés: Küldje el a meghajtókat, az Azure-adatközpontban 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4. lépés: A feladat frissíti a nyomkövetési adatokat

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="samples-for-journal-files"></a>A napló fájlok minták

A **további meghajtók hozzáadása**, hozzon létre egy új driveset fájlt, és futtassa a parancsot az alábbi. 

A különböző, mint a megadott merevlemez-meghajtók későbbi másolási munkamenetek *InitialDriveset .csv* fájlt, adja meg egy új driveset *.csv* fájlt, és adja meg a paraméter értékeként `AdditionalDriveSet`. Használja a **azonos naplófájl** nevet, és adjon meg egy **új munkamenet-azonosító**. Ugyanaz, mint a InitialDriveSet formátum AdditionalDriveset CSV-fájl formátuma.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Importálás példát alább láthatók.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


További adatok hozzáadása a azonos driveset, a paranccsal PrepImport későbbi másolási munkamenetek másolása további fájlokat vagy könyvtár.

Az azonos merevlemez-meghajtókra megadott későbbi másolási munkamenetek *InitialDriveset.csv* fájlt, adja meg a **azonos naplófájl** nevet, és adjon meg egy **új munkamenet-azonosító**; nincs szükség arra, hogy a tárfiók kulcsára.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Importálás példát alább láthatók.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>További lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelményeinek áttekintése](storage-import-export-requirements.md)


