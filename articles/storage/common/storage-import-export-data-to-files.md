---
title: Adatok átvitele az Azure import/export használatával Azure Filesba | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre importálási feladatokat a Azure Portal az adatok Azure Filesba történő átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: cbff2cbed37a4cff91116596f1c20dc3d170cae2
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513484"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Az Azure Import/Export szolgáltatás használata az adatok Azure Filesba történő importálásához

Ez a cikk részletesen ismerteti, hogyan használható az Azure import/export szolgáltatás a nagy mennyiségű információ biztonságos importálásához a Azure Filesba. Az adatimportáláshoz a szolgáltatáshoz olyan támogatott lemezmeghajtók szállítására van szükség, amelyek az Ön adatait tartalmazzák az Azure-adatközpontokban.  

Az import/export szolgáltatás csak Azure Files importálását támogatja az Azure Storage-ba. A Azure Files exportálása nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehoz egy importálási feladatot az adatok Azure Filesba való átviteléhez, gondosan tekintse át és hajtsa végre az alábbi előfeltételek listáját. A következőket kell tennie:

- Az import/export szolgáltatással való használatra aktív Azure-előfizetéssel kell rendelkeznie.
- Rendelkeznie kell legalább egy Azure Storage-fiókkal. Tekintse meg a [támogatott Storage-fiókok és tárolási típusok listáját az importálási/exportálási szolgáltatáshoz](storage-import-export-requirements.md). További információ az új Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](storage-account-create.md).
- Megfelelő számú lemezzel rendelkezik a [támogatott típusok](storage-import-export-requirements.md#supported-disks)közül.
- Egy [támogatott operációsrendszer-verzióval](storage-import-export-requirements.md#supported-operating-systems)rendelkező Windows rendszerre van telepítve.
- [Töltse le a WAImportExport 2-es verzióját](https://aka.ms/waiev2) a Windows rendszerre. Bontsa ki az alapértelmezett mappát `waimportexport` . Például: `C:\WaImportExport`.
- Van egy FedEx/DHL-fiókja. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, lépjen kapcsolatba Azure Data Box Operations csapatával a következő címen: `adbops@microsoft.com` .  
    - A fióknak érvényesnek kell lennie, egyensúlyt kell tartalmaznia, és vissza kell adni a szállítási képességeket.
    - Nyomkövetési szám létrehozása az exportálási feladatokhoz.
    - Minden feladatnak külön nyomkövetési számmal kell rendelkeznie. Nem támogatott, hogy több feladatnak is ugyanaz legyen a nyomkövetési száma.
    - Ha nem rendelkezik Carrier-fiókkal, lépjen a következőre:
        - [Hozzon létre egy FedEx-fiókot](https://www.fedex.com/en-us/create-account.html), vagy
        - [Hozzon létre egy DHL-fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>1. lépés: a meghajtók előkészítése

Ez a lépés egy naplófájlt hoz létre. A naplófájl olyan alapvető adatokat tárol, mint például a meghajtó sorozatszáma, a titkosítási kulcs és a Storage-fiók adatai.

A meghajtók előkészítéséhez végezze el a következő lépéseket.

1. A lemezmeghajtók összekapcsolása a Windows rendszerű SATA-összekötők használatával.
2. Hozzon létre egyetlen NTFS-kötetet az egyes meghajtókon. Rendeljen meghajtóbetűjelet a kötethez. Ne használja a csatolási.
3. Módosítsa a *dataset.csv* fájlt abban a gyökérkönyvtárban, ahol az eszköz található. Attól függően, hogy egy fájlt vagy mappát vagy mindkettőt szeretne importálni, vegyen fel bejegyzéseket az alábbi példához hasonló *dataset.csv* fájlban.  

   - **Fájl importálása**: a következő példában a másolandó adat az F: meghajtón található. A fájl *MyFile1.txt* a rendszer a *MyAzureFileshare1*gyökerébe másolja. Ha a *MyAzureFileshare1* nem létezik, az Azure Storage-fiókban jön létre. A mappa szerkezete megmarad.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Mappa importálása**: a *MyFolder2* alatt lévő összes fájl és mappa rekurzív módon másolódik a fájlmegosztás. A mappa szerkezete megmarad.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Az importált mappákhoz vagy fájlokhoz tartozó fájlokban több bejegyzés is elvégezhető.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     További információ [az ADATKÉSZLET CSV-fájljának előkészítéséről](storage-import-export-tool-preparing-hard-drives-import.md).


4. Módosítsa a *driveset.csv* fájlt abban a gyökérkönyvtárban, ahol az eszköz található. Adja hozzá az alábbi példákhoz hasonló bejegyzéseket a *driveset.csv* fájlban. A driveset-fájl tartalmazza a lemezek listáját és a hozzá tartozó meghajtóbetűjeleket, így az eszköz megfelelően kiválaszthatja az előkészített lemezek listáját.

    Ez a példa azt feltételezi, hogy két lemez van csatlakoztatva, és alapszintű NTFS-kötetek G:\ és H:\ létrehozva. A H:\is nem titkosított, amíg a G: már titkosítva van. Az eszköz formázza és titkosítja a H:\ tároló lemezt. csak (és nem G: \) ).

   - **Nem titkosított lemez esetén**: a *titkosítás* megadásával engedélyezheti a BitLocker titkosítást a lemezen.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Egy már titkosított lemez esetén**: adja meg a *AlreadyEncrypted* , és adja meg a BitLocker-kulcsot.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Több bejegyzés is elvégezhető a több meghajtóhoz tartozó fájlokban. További információ [a DRIVESET CSV-fájl előkészítéséről](storage-import-export-tool-preparing-hard-drives-import.md).

5. Az `PrepImport` adatlemezre másolással és előkészítéssel megadhatja az adatátvitelt. Ahhoz, hogy az első másolási munkamenet a címtárakat és/vagy fájlokat egy új másolási munkamenettel másolja, futtassa a következő parancsot:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Alább látható egy importálási példa.

       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```

6. A (z) paraméterrel megadott nevű naplófájl jön `/j:` létre a parancssor minden futtatásához. Minden előkészíteni kívánt meghajtóhoz tartozik egy naplófájl, amelyet az importálási feladatok létrehozásakor fel kell tölteni. A rendszer nem dolgozza fel a naplófájlok nélküli meghajtókat.

    > [!IMPORTANT]
    > - A lemez-előkészítés befejezése után ne módosítsa a lemezmeghajtón vagy a naplófájlban lévőket.

További mintákért lépjen a [minták a Journal-fájlokhoz](#samples-for-journal-files)elemre.

## <a name="step-2-create-an-import-job"></a>2. lépés: importálási feladatok létrehozása

Az alábbi lépések végrehajtásával hozzon létre egy importálási feladatot a Azure Portal.
1. Jelentkezzen be a következőre: https://portal.azure.com/ .
2. Lépjen az **összes szolgáltatás > Storage > importálási/exportálási feladatok lehetőségre**.

    ![Importálási/exportálási ugrás](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kattintson az **importálási/exportálási feladatok létrehozása**lehetőségre.

    ![Kattintson az Importálás/exportálás feladata elemre.](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Az **alapjaiban**:

    - Válassza **az Importálás az Azure-ba**lehetőséget.
    - Adjon meg egy leíró nevet az importálási feladatokhoz. Ezzel a névvel követheti nyomon a feladatokat, miközben azok folyamatban vannak, és ha azok befejeződtek.
        -  Ez a név csak kisbetűket, számokat, kötőjeleket és aláhúzást tartalmazhat.
        -  A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközt.
    - Válasszon egy előfizetést.
    - Válasszon ki egy erőforráscsoportot.

        ![Importálási feladatok létrehozása – 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. A **feladatok részletei**:

    - Töltse fel az előző 1. lépésben létrehozott naplófájlokat [: Készítse elő a meghajtókat](#step-1-prepare-the-drives).
    - Válassza ki azt a Storage-fiókot, amelybe be kívánja importálni az adatimportálást.
    - A lemorzsolódási helye automatikusan kitöltődik a kiválasztott Storage-fiók régiója alapján.

       ![Importálási feladatok létrehozása – 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. **Visszaszállítási adatok**:

    - Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, válasszon ki egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba Azure Data Box operatív csapatával a `adbops@microsoft.com` használni kívánt szolgáltatóra vonatkozó információkkal.
    - Adjon meg egy érvényes, a szállítóval létrehozott számlaszámot. A Microsoft ezt a fiókot használja a meghajtók visszaszállításához az importálási feladatok befejezését követően.
    - Adjon meg egy teljes és érvényes nevet, telefont, e-mailt, utcanév-címet, várost, irányítószámot, államot/régiót és országot/régiót.

        > [!TIP]
        > E-mail-cím egyetlen felhasználóhoz való megadása helyett adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon, még akkor is, ha a rendszergazda elhagyja.

       ![Importálási feladatok létrehozása – 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. Az **összegzésben**:

    - Adja meg az Azure-adatközpont szállítási címet a lemezek szállításához az Azure-ba. Győződjön meg arról, hogy a feladatok neve és a teljes cím szerepel a szállítási címkén.
    - Az importálási feladatok létrehozásának befejezéséhez kattintson **az OK** gombra.

        ![Importálási feladatok létrehozása – 4. lépés](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3. lépés: a meghajtók szállítása az Azure-adatközpontba

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4. lépés: a feladatok frissítése a követési adatokkal

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5. lépés: az adatok feltöltésének ellenőrzése az Azure-ban

A feladatot nyomon követheti befejezésre. A feladatok befejezése után ellenőrizze, hogy az adatok fel lettek-e töltve az Azure-ba. Csak a feltöltés sikeres ellenőrzése után törölje a helyszíni adatok törlését.

## <a name="samples-for-journal-files"></a>Naplófájlok mintái

**További meghajtók hozzáadásához**hozzon létre egy új driveset-fájlt, és futtassa az alábbi parancsot az alábbiak szerint.

Ha a további másolási munkameneteket a *InitialDriveset. csv* fájlban megadott lemezmeghajtók között szeretné megadni, adjon meg egy új driveset *. csv* fájlt, és adja meg a paraméter értékét `AdditionalDriveSet` . Használja **ugyanazt a naplófájl** -nevet, és adjon meg egy **új munkamenet-azonosítót**. A AdditionalDriveset CSV-fájl formátuma megegyezik a InitialDriveSet formátumával.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Alább látható egy importálási példa.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Ha további adatfájlokat szeretne hozzáadni ugyanahhoz a driveset, használja a PrepImport parancsot a további másolási munkamenetek számára további fájlok/könyvtár másolásához.

Ha a későbbi másolási munkameneteket a *InitialDriveset.csv* fájlban megadott merevlemez-meghajtókra szeretné másolni, adja meg **ugyanazt a naplónevet** , és adjon meg egy **új munkamenet-azonosítót**. nincs szükség a Storage-fiók kulcsainak megadására.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Alább látható egy importálási példa.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Következő lépések

* [A feladatok és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)
