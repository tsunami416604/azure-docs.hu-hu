---
title: Adatok átviteléhez az Azure BLOB az Azure Import/Export használatával |} Microsoft Docs
description: Megtudhatja, hogyan importálási létrehozni és exportálni a feladatokat az Azure portál és az Azure BLOB adatok átviteléhez.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: fe9292459134972b44037a58235cdd817030a956
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660874"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Adatok importálása az Azure Blob Storage a Azure Import/Export szolgáltatás használata

Ez a cikk részletes útmutatást nyújt az Azure Import/Export szolgáltatás használatával biztonságosan importálása az Azure Blob storage nagy mennyiségű adat. Adatok importálása az Azure BLOB, hogy a szolgáltatás megköveteli, hogy küldje el az adatokat, hogy egy Azure-adatközpontban tartalmazó titkosított meghajtók.  

## <a name="prerequisites"></a>Előfeltételek

Az adatok átviteléhez az Azure Blob Storage importálási feladat létrehozása előtt gondosan tekintse át, és végezze el az alábbi listán szereplő Előfeltételek ezt a szolgáltatást. A következők szükségesek:

- Az Azure előfizetéssel rendelkezik, amely az Import/Export szolgáltatás használható.
- Rendelkezik legalább egy Azure Storage-fiókkal és tárolót. A lista [storage-fiókok és a tárolási típusok támogatott Import/Export szolgáltatás](storage-import-export-requirements.md). Új tárfiók létrehozásával kapcsolatos további információkért lásd: [a Storage-fiók létrehozása](storage-create-storage-account.md#create-a-storage-account). A tároló információkért látogasson el [hozzon létre egy tároló](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- A lemezek elegendő számú [támogatott típusok](storage-import-export-requirements.md#supported-disks). 
- Windows operációs rendszert futtató rendelkezik egy [támogatott operációsrendszer-verzió](storage-import-export-requirements.md#supported-operating-systems). 
- Engedélyezheti a Bitlockert a Windows rendszeren. Lásd: [BitLocker engedélyezése](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Töltse le a WAImportExport 1-es verziójú](https://www.microsoft.com/en-us/download/details.aspx?id=42659) a Windows rendszeren. Bontsa ki az alapértelmezett mappába `waimportexportv1`. Például: `C:\WaImportExportV1`.


## <a name="step-1-prepare-the-drives"></a>1. lépés: Felkészülés a meghajtók

Ebben a lépésben létrehoz egy napló fájlt. A napló fájl például meghajtó sorozatszáma, a titkosítási kulcs és a tárfiókadatok alapvető információkat tárolja. 

Hajtsa végre az alábbi lépések végrehajtásával készítse elő a meghajtók.

1.  A lemezmeghajtók csatlakozni a Windows rendszer SATA összekötők keresztül.
1.  Minden olyan meghajtó egy NTFS-kötet létrehozása A meghajtóbetűjelet rendelje a köteten. Ne használjon akkor csatlakozási.
2.  Engedélyezheti a BitLocker titkosítást az NTFS-köteten. Ha a Windows Server rendszert használ, kövesse az utasításokat, a [Windows Server 2012 R2 rendszeren a BitLocker engedélyezése](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Adatok másolása a titkosított kötetek. Használja a fogd és dobja el vagy Robocopy vagy ilyen másolási eszköz.
4.  Nyissa meg a PowerShell vagy a parancssori ablakot rendszergazdai jogosultságokkal. A tömörítetlen mappába könyvtár módosításához futtassa a következő parancsot:
    
    `cd C:\WaImportExportV1`
5.  Ahhoz, hogy a BitLocker-kulcsot a meghajtó, futtassa a következő parancsot:
    
    ` manage-bde -protectors -get <DriveLetter>: `
6.  A lemez előkészítéséhez a következő parancsot. **Az adatok méretétől függően ez több óráig is eltarthat nap.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    A napló fájl ugyanabba a mappába, ha az eszközt futtatta jön létre. Két más fájlok is létrejönnek - egy *.xml* fájlt (ha az eszközt futtatja mappa) és egy *meghajtó-manifest.xml* fájl (adatokat tároló mappát).
    
    A következő táblázat ismerteti a használt paraméterek:

    |Beállítás  |Leírás  |
    |---------|---------|
    |/j:     |A napló kiterjesztésű, a .jrn neve. A napló fájl meghajtónkénti jön létre. Azt javasoljuk, hogy használja-e a lemez sorozatszám napló fájlneve.         |
    |/ID:     |a munkamenet-azonosítót. A parancs minden egyes példányánál egyedi munkamenet több használ.      |
    |/SK:     |Az Azure-Tárfiók kulcsának.         |
    |/t:     |A meghajtó betűjelével szállítani a lemezen. Például meghajtó `D`.         |
    |/bk:     |A BitLocker kulcs a meghajtón. A numerikus jelszót a kimenetből ` manage-bde -protectors -get D: `      |
    |/srcdir:     |A meghajtóbetűjelet, a lemez szállítási követ `:\`. Például: `D:\`.         |
    |/dstdir:     |Az Azure Storage a cél tároló nevét.         |
    |/skipwrite:     |A lehetőség, amely megadja, hogy nem kell átmásolnia szükséges új adatok és a lemezen található meglévő adatokat egy előkészíteni.          |
7. Az előző lépés megismétlésével, amelyet a szállítási lemezek. A megadott nevű napló fájl jön létre minden a parancssor futtatása.
    
    > [!IMPORTANT]
    > - A naplófájl együtt egy `<Journal file name>_DriveInfo_<Drive serial ID>.xml` fájl is létrejön, ugyanabban a mappában, amelyen telepítve van. Az XML-fájl használja napló fájl helyett a feladat létrehozásakor, ha a napló fájl túl nagy. 

## <a name="step-2-create-an-import-job"></a>2. lépés: Az importálási feladat létrehozása

A következő lépésekkel importálási feladat létrehozása az Azure portálon.

1. Jelentkezzen be https://portal.azure.com/.
2. Ugrás a **minden szolgáltatás > tárolás > importálási/exportálási feladatok**. 
    
    ![Ugrás az importálási/exportálási feladatok](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kattintson a **létrehozás importálási/exportálási feladatok**.

    ![Kattintson a létrehozás importálási/exportálási feladatok](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. A **alapjai**:

    - Válassza ki **az Azure importálási**.
    - Adjon meg egy leíró nevet az importálási feladatnak. A név segítségével nyomon követheti a feladatok állapotát.
        - A név csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket tartalmazhat.
        - A nevének betűvel kell kezdődnie, és nem tartalmazhat szóközt.
    - Válasszon egy előfizetést.
    - Adja meg, vagy válasszon egy erőforráscsoportot.  

    ![Hozzon létre importálási feladat - 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. A **feladat részletei**:

    - Töltse fel a meghajtó Adatbázisnapló-fájlok a meghajtó előkészítési lépés során beolvasott. Ha `waimportexport.exe version1` lett használja, minden előkészített meghajtó egy fájl feltöltése. Ha a napló mérete meghaladja a 2 MB között, akkor is használhatja a `<Journal file name>_DriveInfo_<Drive serial ID>.xml` is létrejön, melynek a napló fájlt. 
    - Válassza ki a cél tárfiókkal adatokat tároló. 
    - A gyűjtőtár helyre automatikusan a kiválasztott tárfiók régió alapján van feltöltve.
   
   ![Hozzon létre importálási feladat - 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. A **szállítási információ vissza**:

    - Válassza ki a szolgáltatói a legördülő listából.
    - Adjon meg egy érvényes vivőjel-szám, amely adott szolgáltatónként hozott létre. Microsoft küldje el azt a meghajtókat, az importálási feladat befejezése után ezt a fiókot használja. Ha nincs egy fiók számát, hozzon létre egy [FedEx](http://www.fedex.com/us/oadr/) vagy [DHL](http://www.dhl.com/) vivőjel-fiók.
    - Adjon teljes és érvényes kapcsolattartójának a neve, telefon, e-mail, utca, házszám, város, zip, az állam/megye ország vagy régió.

    ![Hozzon létre importálási feladat - 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. Az a **összegzés**:

    - Tekintse át az összegzésben található a feladat információk. Jegyezze fel, a feladat nevét és a szállítási cím szállítási lemezek vissza az Azure-bA az Azure-adatközpontban. Az adatok később a szállítási címkén.
    - Kattintson a **OK** az importálási feladat létrehozásához.

    ![Hozzon létre importálási feladat - 4. lépés](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

## <a name="step-3-ship-the-drives"></a>3. lépés: Küldje el a meghajtók 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>4. lépés: A feladat frissíti a nyomkövetési adatokat

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="next-steps"></a>További lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelményeinek áttekintése](storage-import-export-requirements.md)


