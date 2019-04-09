---
title: Az Azure Import/Export használatával az adatok átviteléhez az Azure Files |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az importálási feladatokhoz az Azure Files adatok átviteléhez az Azure Portalon.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 28026a429643c62434ddfd7591126169857a7371
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265634"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Adatok importálása az Azure Files Azure Import/Export szolgáltatás használata

Ebben a cikkben részletes útmutatás az Azure Import/Export szolgáltatás biztonságosan nagy mennyiségű adatot importálni az Azure Files használatával. Adatok importálása a szolgáltatás megköveteli, hogy az adatok az Azure-adatközpont tartalmazó támogatott lemezmeghajtók szállításra.  

Az Import/Export szolgáltatás támogatja az importálás kizárólag az Azure Files Azure Storage-bA. Az Azure-fájlok exportálása nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Adatok átviteléhez az Azure Files importálási feladat létrehozása, előtt gondosan tekintse át, és hajtsa végre az alábbi listában szereplő előfeltételek. Tegye a következőket:

- Import/Export szolgáltatás használata egy aktív Azure-előfizetéssel rendelkezik.
- Legalább egy Azure Storage-fiók rendelkezik. Listájának megtekintéséhez [az Import/Export szolgáltatás által támogatott storage-fiókok és a tárolási típusok](storage-import-export-requirements.md). Új tárfiók létrehozásával kapcsolatos információkért lásd: [Storage-fiók létrehozása](storage-quickstart-create-account.md).
- A lemezek elegendő számú [támogatott típusok](storage-import-export-requirements.md#supported-disks). 
- A Windows rendszert futtató egy [támogatott operációsrendszer-verzió](storage-import-export-requirements.md#supported-operating-systems).
- [Töltse le a 2. verzió WAImportExport](https://aka.ms/waiev2) a Windows rendszeren. Csomagolja ki az alapértelmezett mappába kívánja `waimportexport`. Például: `C:\WaImportExport`.
- FedEx/DHL fiókkal rendelkeznie. Ha szeretne egy szolgáltató eltérő FedEx/DHL használja, forduljon a az Azure Data Box műveletek csapatának `adbops@microsoft.com`.  
    - A fiók érvényesnek kell lennie, kell rendelkeznie az egyenleg és visszaszállítási képességek kell rendelkeznie.
    - Követési szám az exportálási feladat létrehozása.
    - Minden feladat rendelkeznie kell egy külön követési szám. Több feladat egy követési szám nem támogatottak.
    - Ha nem rendelkezik egy szállítmányozói fiókjára, Ugrás:
        - [Hozzon létre egy FedEX fiókot](https://www.fedex.com/en-us/create-account.html), vagy 
        - [Hozzon létre egy DHL fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>1. lépés: A meghajtó előkészítése

Ebben a lépésben létrehoz egy naplót fájlt. A naplófájl alapvető információkat, például a meghajtó sorozatszáma, a titkosítási kulcsot és a storage-fiók adatait tárolja.

Hajtsa végre az alábbi lépések végrehajtásával készítse elő a meghajtókat.

1. A lemezmeghajtók kapcsolódni a Windows rendszer SATA-összekötők használatával.
2. Hozzon létre egy NTFS-kötetet egyes meghajtókon. A kötet rendeljen hozzá meghajtóbetűjelet. Ne használjon akkor csatlakozási.
3. Módosítsa a *dataset.csv* fájl a gyökérmappában található, ahol az eszköz található. Attól függően, hogy importálása egy fájl vagy mappa vagy mindkettő, a tételek hozzáadása a *dataset.csv* fájlt a következő példákhoz hasonló.  

   - **Fájl importálása**: A következő példában a C: meghajtón található adatokat. A fájl *MyFile1.txt* gyökerében másolja a *MyAzureFileshare1*. Ha a *MyAzureFileshare1* nem létezik, az Azure Storage-fiókot létrehozták. Gyökérmappa-szerkezetében változatlan marad.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
       ```
   - **Egy mappa importálása**: Minden fájl és mappa alatt *MyFolder2* rekurzív módon másolja fájlmegosztás. Gyökérmappa-szerkezetében változatlan marad.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
       ```
     Több bejegyzés lehet kapcsolódni a megfelelő mappákat vagy fájlokat importált ugyanebben a fájlban. 

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
       ```
     Tudjon meg többet [az adatkészlet CSV-fájl előkészítése](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Módosítsa a *driveset.csv* fájl a gyökérmappában található, ahol az eszköz található. Adjon hozzá bejegyzéseket az *driveset.csv* fájlt a következő példákhoz hasonló. A driveset fájl rendelkezik a megfelelő meghajtóbetűjeleket és lemezek listáját, úgy, hogy az eszköz megfelelően válassza ki a készüljön fel a lemezek listája.

    Ez a példa feltételezi, hogy két lemezekkel rendelkezik, és alapszintű NTFS-kötet G:\ és H:\ jönnek létre. H:\is nincs titkosítva, miközben G: már titkosítva van. Az eszköz formázza és titkosítja a lemezen, amelyen csak a H:\ (és nem G:\).

   - **Nem titkosított lemezek esetében**: Adja meg *titkosítása* ahhoz, hogy a lemez BitLocker-titkosítást.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```
    
   - **Titkosított lemezek esetében**: Adja meg *AlreadyEncrypted* , és adja meg a BitLocker-kulcsot.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Több bejegyzés is végezhető el több meghajtó megfelelő ugyanebben a fájlban. Tudjon meg többet [driveset CSV-fájl előkészítése](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5. Használja a `PrepImport` másolja, és előkészíti az adatokat a meghajtón. Az első másolás munkamenet könyvtárak és/vagy egy új példányt munkamenettel fájlokat másolni futtassa a következő parancsot:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Importálás példát alább látható.
  
       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```
 
6. A megadott nevű naplófájl `/j:` paraméter, minden egyes futtatáskor a parancssor jön létre. Minden olyan meghajtó előkészítése rendelkezik egy journal-fájlt, amely fel kell tölteni az importálási feladat létrehozásakor. Meghajtók fájlok feldolgozása nem napló nélkül.

    > [!IMPORTANT]
    > - Ne módosítsa a lemezmeghajtókat vagy a naplófájl található adatokat lemezre előkészítés befejezése után.

További példákat talál [-minták az adatbázisnapló-fájlok](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>2. lépés: Importálási feladat létrehozása 

A következő lépésekkel importálási feladat létrehozása az Azure Portalon.
1. Jelentkezzen be https://portal.azure.com/.
2. Lépjen a **minden szolgáltatás > tárolás > importálási/exportálási feladatok**. 

    ![Ugrás az Import/export](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kattintson a **importálási/exportálási feladat létrehozása**.

    ![Kattintson az importálási/exportálási feladat](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. A **alapjai**:

    - Válassza ki **Azure-ba való importálásuk**.
    - Adjon meg egy leíró nevet az importálási feladatot. Ez a név segítségével nyomon követheti a feladatok, amikor a folyamatban, és miután a rendszer.
        -  Ez a név csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket tartalmazhat.
        -  A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközt. 
    - Válasszon egy előfizetést.
    - Válasszon erőforráscsoportot. 

        ![Importálási feladat létrehozása – 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. A **feladat részletei**:
    
    - A naplófájlok az előző során létrehozott feltöltése [1. lépés: Készítse elő a meghajtók](#step-1-prepare-the-drives). 
    - Válassza ki a tárfiókot, amely az adatokat importálja a rendszer. 
    - Dropoff helyét a rendszer automatikusan kitölti a kiválasztott tárfiók a régióhoz igazodik.
   
       ![Importálási feladat létrehozása – 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. A **szállítási adatok visszaadása**:

    - A legördülő listából válassza ki a szolgáltató. Ha szeretne egy szolgáltató eltérő FedEx/DHL használja, válasszon egy meglévő lehetőséget a legördülő listából. A csapat kapcsolattartási Azure Data Box Operations `adbops@microsoft.com` együtt tervezi használni a szállítmányozó vonatkozó információkat.
    - Adjon meg egy érvényes Szállítmányozó számlaszáma, amelyek a szolgáltató létrehozta. A Microsoft ezt a fiókot használja, a meghajtók vissza tehetnek az importálási feladat befejeződése után. 
    - Adja meg a kész, érvényes ügyfél nevét, telefonszám, e-mail, utca, házszám, város, zip, államot/megyét és ország/régió.

        > [!TIP] 
        > Helyett adjon meg egy e-mail címet, egy-egy felhasználóhoz, adjon meg egy csoport e-mail-címet. Ez biztosítja, értesítések fogadására, még akkor is, ha egy rendszergazda hagyja.

       ![Importálási feladat létrehozása – 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. Az a **összefoglalás**:

    - Adja meg a szállítási cím a szállítási lemezeket az Azure-bA az Azure-adatközpontba. Győződjön meg arról, hogy a feladat nevét és a teljes cím a szállítási címkét a szerepelnek.
    - Kattintson a **OK** importálási feladat létrehozásának befejezéséhez.

        ![Importálási feladat létrehozása – 4. lépés](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3. lépés: Az Azure-adatközpontba a meghajtók szállításra 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4. lépés: A nyomkövetési adatokat a feladat frissítése

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5. lépés: Az Azure-ba történő adatfeltöltés ellenőrzése

Nyomon követheti a feladat befejezését. A feladat befejeződése után győződjön meg arról, hogy az adatok feltöltötte-e az Azure-bA. Törli a helyszíni adatokat, csak azt követően, ha megbizonyosodott arról, hogy a feltöltés sikeres volt.

## <a name="samples-for-journal-files"></a>Naplófájl-minták

A **további meghajtók hozzáadása**, hozzon létre egy új driveset fájlt, és futtassa a parancsot az alábbiak szerint. 

Az ezt követő másolási munkamenetek a különböző merevlemez-meghajtók, mint a megadott *InitialDriveset .csv* fájlt, adja meg egy új driveset *.csv* fájlt, és adja meg a paraméter értékeként `AdditionalDriveSet`. Használja a **azonos naplófájl** nevet, és adjon meg egy **új munkamenet-azonosító**. Ugyanaz, mint a InitialDriveSet formátum AdditionalDriveset CSV-fájl formátuma.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Importálás példát alább látható.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


További adatok hozzáadása az azonos driveset, a paranccsal PrepImport másolás későbbi munkamenet során további fájlokat/címtár másolásához.

Másolás későbbi munkamenet során az azonos merevlemez-meghajtókra megadott *InitialDriveset.csv* fájlt, adja meg a **azonos naplófájl** nevet, és adjon meg egy **új munkamenet-azonosító**; Hiba esetén nem kell adnia a tárfiók-kulcsot.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Importálás példát alább látható.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>További lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelményeinek áttekintése](storage-import-export-requirements.md)


