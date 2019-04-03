---
title: Az Azure Import/Export használatával való adatátvitel Azure-Blobok |} A Microsoft Docs
description: Ismerje meg, hogyan importálás létrehozni és exportálni a feladatokat az Azure Portalon, és az Azure-Blobokból adatok átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/11/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: c7e78f89883e5cfc3fc8b9088c3ac0b3166682c7
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878179"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Az Azure Blob Storage-adatok importálása az Azure Import/Export szolgáltatás használata

Ebben a cikkben részletes útmutatás az Azure Import/Export szolgáltatás használata biztonságosan importálása az Azure Blob storage nagy mennyiségű adat. Az adatok importálása az Azure-Blobok, a szolgáltatás megköveteli, hogy az adatok az Azure-adatközpont tartalmazó titkosított meghajtók szállításra.  

## <a name="prerequisites"></a>Előfeltételek

Adatok átviteléhez az Azure Blob Storage-bA importálási feladat létrehozása, előtt gondosan tekintse át, és hajtsa végre az alábbi listában szereplő Előfeltételek ezt a szolgáltatást. Tegye a következőket:

- Egy aktív Azure-előfizetéssel rendelkezik az Import/Export szolgáltatás használható.
- Legalább egy tárolót az Azure Storage-fiók rendelkezik. Listájának megtekintéséhez [az Import/Export szolgáltatás által támogatott storage-fiókok és a tárolási típusok](storage-import-export-requirements.md). 
    - Új tárfiók létrehozásával kapcsolatos információkért lásd: [Storage-fiók létrehozása](storage-quickstart-create-account.md). 
    - Információk a storage-tárolóba, [hozzon létre egy storage-tárolóba](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- A lemezek elegendő számú [támogatott típusok](storage-import-export-requirements.md#supported-disks). 
- A Windows rendszert futtató egy [támogatott operációsrendszer-verzió](storage-import-export-requirements.md#supported-operating-systems). 
- Engedélyezi a Bitlockert a Windows rendszeren. Lásd: [BitLocker engedélyezésének módja](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Töltse le az 1. verziójának WAImportExport](https://aka.ms/waiev1) a Windows rendszeren. Csomagolja ki az alapértelmezett mappába kívánja `waimportexportv1`. Például: `C:\WaImportExportV1`.
- FedEx/DHL fiókkal rendelkeznie.  
    - A fiók érvényesnek kell lennie, kell rendelkeznie az egyenleg és visszaszállítási képességek kell rendelkeznie.
    - Követési szám az exportálási feladat létrehozása.
    - Minden feladat rendelkeznie kell egy külön követési szám. Több feladat egy követési szám nem támogatottak.
    - Ha nem rendelkezik egy szállítmányozói fiókjára, Ugrás:
        - [Hozzon létre egy FedEX fiókot](https://www.fedex.com/en-us/create-account.html), vagy 
        - [Hozzon létre egy DHL fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>1. lépés: A meghajtó előkészítése

Ebben a lépésben létrehoz egy naplót fájlt. A naplófájl alapvető információkat, például a meghajtó sorozatszáma, a titkosítási kulcsot és a storage-fiók adatait tárolja. 

Hajtsa végre az alábbi lépések végrehajtásával készítse elő a meghajtókat.

1.  A lemezmeghajtók kapcsolódni a Windows rendszer SATA-összekötők használatával.
1.  Hozzon létre egy NTFS-kötetet egyes meghajtókon. A kötet rendeljen hozzá meghajtóbetűjelet. Ne használjon akkor csatlakozási.
2.  A BitLocker titkosítást az NTFS-köteten. Ha a Windows Server rendszert használ, kövesse az utasításokat a [hogyan engedélyezheti a Bitlockert a Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Adatok másolása a titkosított kötetek. Húzza és dobja el vagy a Robocopy vagy bármely ilyen fájlmásoló eszközt használja.
4.  Nyissa meg a PowerShell vagy a parancssori ablakot rendszergazdai jogosultságokkal. A kicsomagolt mappába könyvtár módosításához futtassa a következő parancsot:
    
    `cd C:\WaImportExportV1`
5.  A BitLocker-kulcs a meghajtón futtassa a következő parancsot:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Készítse elő a lemezen, futtassa a következő parancsot. **Az adatok méretétől függően ez eltarthat néhány órát napra.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    Egy journal-fájl jön létre ugyanabban a mappában, ahol az eszközt futtatta. Két más fájlok is létrejönnek - egy *.xml* fájlt (a mappa, ahol futtatja az eszközt) és a egy *meghajtó-manifest.xml* fájlt (adatokat tartalmazó mappát).
    
    Használt paraméterek a következő táblázat ismerteti:

    |Beállítás  |Leírás  |
    |---------|---------|
    |/j:     |A napló .jrn kiterjesztésű fájl neve. A naplófájl meghajtónkénti jön létre. Azt javasoljuk, hogy a lemez sorozatszáma használjon a napló-fájl neve.         |
    |/ ID:     |A munkamenet-azonosítót. A parancs minden példánya esetében egyedi munkamenet több használ.      |
    |/sk:     |Az Azure Storage-fiókkulcs.         |
    |/t:     |A meghajtóbetűjel, a lemez szállításra. Ha például meghajtó `D`.         |
    |/bk:     |A meghajtó BitLocker-kulcsa. A numerikus jelszó kimenetéből: `manage-bde -protectors -get D:`      |
    |/srcdir:     |A lemez szállításra meghajtóbetűjelének követ `:\`. Például: `D:\`.         |
    |/dstdir:     |Az Azure Storage-ban a cél tároló neve.         |
    |/skipwrite:     |A beállítást, amely megadja, hogy nem szükséges átmásolni új adatokat és a lemezen található meglévő adatokat, hogy elő kell készíteni.          |
7. Ismételje meg minden egyes szállításra lemezt az előző lépésben. A megadott nevű napló fájl jön létre minden egyes futtatáskor a parancssor.
    
    > [!IMPORTANT]
    > - A naplófájl együtt egy `<Journal file name>_DriveInfo_<Drive serial ID>.xml` fájl is létrejön ugyanabban a mappában, amelyben az eszköz található. Az XML-fájl használja journal-fájl helyett a feladat létrehozásakor, ha a napló-fájl túl nagy. 

## <a name="step-2-create-an-import-job"></a>2. lépés: Importálási feladat létrehozása

A következő lépésekkel importálási feladat létrehozása az Azure Portalon.

1. Jelentkezzen be https://portal.azure.com/.
2. Lépjen a **minden szolgáltatás > tárolás > importálási/exportálási feladatok**. 
    
    ![Ugrás az importálási és exportálási feladatokhoz](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kattintson a **importálási/exportálási feladat létrehozása**.

    ![Kattintson a létrehozás importálási/exportálási feladat](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. A **alapjai**:

   - Válassza ki **Azure-ba való importálásuk**.
   - Adjon meg egy leíró nevet az importálási feladatot. A név használatával a feladatok előrehaladását úgy követheti nyomon.
       - A név csak kisbetűket, számokat és kötőjeleket tartalmazhat.
       - A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközt.
   - Válasszon egy előfizetést.
   - Adja meg, vagy válasszon ki egy erőforráscsoportot.  

     ![Importálási feladat létrehozása – 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. A **feladat részletei**:

    - Töltse fel a meghajtó Adatbázisnapló-fájlok a meghajtó előkészítési lépés során beszerzett. Ha `waimportexport.exe version1` volt használja, töltse fel minden meghajtó előkészített egy fájlt. Ha a napló mérete meghaladja a 2 MB, akkor használhatja a `<Journal file name>_DriveInfo_<Drive serial ID>.xml` is létrejön, a naplófájl. 
    - Válassza ki a cél tárfiók adatait tároló. 
    - Dropoff helyét a rendszer automatikusan kitölti a kiválasztott tárfiók a régióhoz igazodik.
   
   ![Importálási feladat létrehozása – 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. A **szállítási adatok visszaadása**:

   - A legördülő listából válassza ki a szolgáltató.
   - Adjon meg egy érvényes Szállítmányozó számlaszáma, amelyek a szolgáltató létrehozta. A Microsoft ezt a fiókot használja, a meghajtók vissza tehetnek az importálási feladat befejeződése után. Ha nincs egy fiók száma, hozzon létre egy [FedEx](https://www.fedex.com/us/oadr/) vagy [DHL](http://www.dhl.com/) Szállítmányozói fiókjára.
   - Adja meg a kész, érvényes ügyfél nevét, telefonszám, e-mail, utca, házszám, város, zip, államot/megyét és ország/régió. 
        
       > [!TIP] 
       > Helyett adjon meg egy e-mail címet, egy-egy felhasználóhoz, adjon meg egy csoport e-mail-címet. Ez biztosítja, értesítések fogadására, még akkor is, ha egy rendszergazda hagyja.

     ![Importálási feladat létrehozása – 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. Az a **összefoglalás**:

   - Tekintse át az összefoglalást a feladat információk. Jegyezze fel a feladat neve és a szállítási cím tehetnek a lemezeket az Azure-bA az Azure-adatközpontba. Ez az információ később használják a szállítási címkét.
   - Kattintson a **OK** az importálási feladat létrehozása.

     ![Importálási feladat létrehozása – 4. lépés](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>3. lépés: A meghajtók szállításra 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>4. lépés: A nyomkövetési adatokat a feladat frissítése

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5. lépés: Az Azure-ba történő adatfeltöltés ellenőrzése

Nyomon követheti a feladat befejezését. A feladat befejeződése után győződjön meg arról, hogy az adatok feltöltötte-e az Azure-bA. Törli a helyszíni adatokat, csak azt követően, ha megbizonyosodott arról, hogy a feltöltés sikeres volt.

## <a name="next-steps"></a>További lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelményeinek áttekintése](storage-import-export-requirements.md)


