---
title: Az Azure import/export használata az adatok Azure-Blobokra történő átviteléhez | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre importálási és exportálási feladatokat Azure Portal az Azure-Blobokra irányuló és onnan érkező adatok átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: cab9d309d052acca493e112965c8477a325d8c88
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75944757"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Az Azure import/export szolgáltatás használata az Azure-ba való adatimportálásra Blob Storage

Ez a cikk részletesen ismerteti, hogyan használható az Azure import/export szolgáltatás a nagy mennyiségű, az Azure Blob Storage-ba való biztonságos importáláshoz. Az Azure-Blobokra történő adatimportáláshoz a szolgáltatáshoz az adatait tartalmazó titkosított lemezmeghajtókat kell szállítani egy Azure-adatközpontba.  

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehoz egy importálási feladatot az adatok Azure Blob Storageba való átviteléhez, gondosan tekintse át és végezze el az alábbi, a szolgáltatásra vonatkozó előfeltételeket tartalmazó listát. A következőket kell tennie:

- Az import/export szolgáltatáshoz használható aktív Azure-előfizetéssel rendelkezik.
- Rendelkeznie kell legalább egy Azure Storage-fiókkal tárolóval. Tekintse meg a [támogatott Storage-fiókok és tárolási típusok listáját az importálási/exportálási szolgáltatáshoz](storage-import-export-requirements.md). 
    - További információ az új Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](storage-quickstart-create-account.md). 
    - A Storage-tárolóval kapcsolatos információkért nyissa meg a Storage-tároló [létrehozása](../blobs/storage-quickstart-blobs-portal.md#create-a-container)című témakört.
- Megfelelő számú lemezzel rendelkezik a [támogatott típusok](storage-import-export-requirements.md#supported-disks)közül. 
- Egy [támogatott operációsrendszer-verzióval](storage-import-export-requirements.md#supported-operating-systems)rendelkező Windows rendszerre van telepítve. 
- Engedélyezze a BitLockert a Windows rendszeren. Lásd: [a BitLocker engedélyezése](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Töltse le a WAImportExport 1-es verzióját](https://www.microsoft.com/download/details.aspx?id=42659) a Windows rendszerre. Bontsa ki az alapértelmezett mappát `waimportexportv1`. Például: `C:\WaImportExportV1`.
- Van egy FedEx/DHL-fiókja. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, vegye fel a kapcsolatot Azure Data Box operatív csapatával a következő címen: `adbops@microsoft.com`.  
    - A fióknak érvényesnek kell lennie, egyensúlyt kell tartalmaznia, és vissza kell adni a szállítási képességeket.
    - Nyomkövetési szám létrehozása az exportálási feladatokhoz.
    - Minden feladattípusnak külön nyomkövetési számmal kell rendelkeznie. Nem támogatott több feladat ugyanazzal a követési számmal.
    - Ha nem rendelkezik Carrier-fiókkal, lépjen a következőre:
        - [Hozzon létre egy FedEx-fiókot](https://www.fedex.com/en-us/create-account.html), vagy 
        - [Hozzon létre egy DHL-fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>1\. lépés: a meghajtók előkészítése

Ez a lépés egy naplófájlt hoz létre. A naplófájl olyan alapvető adatokat tárol, mint például a meghajtó sorozatszáma, a titkosítási kulcs és a Storage-fiók adatai. 

A meghajtók előkészítéséhez végezze el a következő lépéseket.

1.  Csatlakoztathatja a lemezmeghajtókat a Windows rendszerhez a SATA-összekötők használatával.
1.  Hozzon létre egyetlen NTFS-kötetet az egyes meghajtókon. Rendeljen meghajtóbetűjelet a kötethez. Ne használja a csatolási.
2.  Engedélyezze a BitLocker-titkosítást az NTFS-köteten. Ha Windows Server rendszert használ, használja a [Windows server 2012 R2 rendszeren a BitLocker engedélyezésének](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)útmutatását.
3.  Az Adatmásolás titkosított kötetre. A drag and drop vagy a Robocopy vagy bármely ilyen másolási eszköz használatával.
4.  Nyisson meg egy PowerShell-vagy parancssori ablakot rendszergazdai jogosultságokkal. Ha a könyvtárat a kibontott mappára szeretné módosítani, futtassa a következő parancsot:
    
    `cd C:\WaImportExportV1`
5.  A meghajtó BitLocker-kulcsának beszerzéséhez futtassa a következő parancsot:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  A lemez előkészítéséhez futtassa a következő parancsot. **Az adatmérettől függően ez több órát is igénybe vehet.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite 
    ```
    A rendszer ugyanabban a mappában hozza létre a naplófájlt, ahol az eszközt futtatta. Két másik fájl is létrejön – egy *. XML* fájl (az eszközt futtató mappa) és egy *Drive-manifest. XML* fájl (a mappa, ahol az adat található).
    
    A használt paramétereket a következő táblázat ismerteti:

    |Lehetőség  |Leírás  |
    |---------|---------|
    |/j     |A naplófájl neve, a. jrn kiterjesztéssel. Egy naplófájl jön létre egy meghajtón. Javasoljuk, hogy a lemez sorozatszámát használja a naplófájl neveként.         |
    |/ID     |A munkamenet-azonosító. A parancs minden példányához használjon egyedi munkamenet-számot.      |
    |/t:     |A szállítandó lemez meghajtóbetűjele. Például: meghajtó `D`.         |
    |BK     |A meghajtó BitLocker-kulcsa. A `manage-bde -protectors -get D:` kimenetének numerikus jelszava      |
    |/srcdir:     |A szállítandó lemez meghajtóbetűjele, majd `:\`. Például: `D:\`.         |
    |/dstdir:     |A cél tároló neve az Azure Storage-ban.         |
    |/blobtype:     |Ezzel a beállítással adható meg, hogy milyen típusú blobokat kíván importálni az alkalmazásba. A blokk Blobok esetében ez `BlockBlob`, és az oldal Blobok esetében `PageBlob`.         |
    |/skipwrite:     |Ez a beállítás azt határozza meg, hogy nincs szükség új adatmásolásra és a lemezen lévő meglévő adatfeldolgozásra.          |
    |/enablecontentmd5:     |Ha a beállítás engedélyezve van, biztosítja, hogy az MD5 kiszámításra kerüljön, és az egyes blobokon `Content-md5` tulajdonságként legyen beállítva. Ezt a lehetőséget csak akkor használja, ha a `Content-md5` mezőt szeretné használni az Azure-ba való feltöltés után. <br> Ez a beállítás nincs hatással az adatintegritás-ellenőrzésre (alapértelmezés szerint ez történik). A beállítással megnövelhető az adatok felhőbe való feltöltéséhez szükséges idő.          |
7. Ismételje meg az előző lépést minden egyes leszállításra szoruló lemez esetében. A rendszer a megadott névvel rendelkező naplófájlt hoz létre a parancssor minden futtatásához.
    
    > [!IMPORTANT]
    > - A Journal-fájllal együtt a `<Journal file name>_DriveInfo_<Drive serial ID>.xml` fájl is ugyanabban a mappában jön létre, ahol az eszköz található. Ha a naplófájl túl nagy, a rendszer a naplófájl helyett a. xml fájlt használja. 

## <a name="step-2-create-an-import-job"></a>2\. lépés: importálási feladatok létrehozása

Az alábbi lépések végrehajtásával hozzon létre egy importálási feladatot a Azure Portal.

1. Jelentkezzen be https://portal.azure.com/ ra.
2. Lépjen az **összes szolgáltatás > Storage > importálási/exportálási feladatok lehetőségre**. 
    
    ![Ugrás az importálási/exportálási feladatokra](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kattintson az **importálási/exportálási feladatok létrehozása**lehetőségre.

    ![Kattintson az importálási/exportálási feladatok létrehozása lehetőségre.](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Az **alapjaiban**:

   - Válassza **az Importálás az Azure-ba**lehetőséget.
   - Adjon meg egy leíró nevet az importálási feladatokhoz. A név használatával követheti nyomon a feladatok állapotát.
       - A név csak kisbetűket, számokat és kötőjeleket tartalmazhat.
       - A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközt.
   - Válasszon előfizetést.
   - Adjon meg vagy válasszon ki egy erőforráscsoportot.  

     ![Importálási feladatok létrehozása – 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. A **feladatok részletei**:

    - Töltse fel a meghajtó-előkészítési lépés során beszerzett meghajtó-napló fájljait. Ha `waimportexport.exe version1` volt használatban, töltsön fel egy fájlt minden előkészített meghajtóra. Ha a naplófájl mérete meghaladja a 2 MB-ot, akkor a `<Journal file name>_DriveInfo_<Drive serial ID>.xml` is létrehozhatja a naplófájl használatával. 
    - Válassza ki a cél Storage-fiókot, ahol az adat található. 
    - A lemorzsolódási helye automatikusan kitöltődik a kiválasztott Storage-fiók régiója alapján.
   
   ![Importálási feladatok létrehozása – 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. **Visszaszállítási adatok**:

   - Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, válasszon ki egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba Azure Data Box operatív csapatával a `adbops@microsoft.com` címen a használni kívánt szolgáltatóval kapcsolatos információkkal.
   - Adjon meg egy érvényes, a szállítóval létrehozott számlaszámot. A Microsoft ezt a fiókot használja a meghajtók visszaszállításához az importálási feladatok befejezését követően. Ha nem rendelkezik fiókkal, hozzon létre egy [FedEx](https://www.fedex.com/us/oadr/) vagy [DHL](https://www.dhl.com/) Carrier-fiókot.
   - Adjon meg egy teljes és érvényes nevet, telefont, e-mailt, utcanév-címet, várost, irányítószámot, államot/régiót és országot/régiót. 
        
       > [!TIP] 
       > E-mail-cím egyetlen felhasználóhoz való megadása helyett adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon, még akkor is, ha a rendszergazda elhagyja.

     ![Importálási feladatok létrehozása – 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. Az **összegzésben**:

   - Tekintse át az összegzésben megadott feladattal kapcsolatos információkat. Jegyezze fel a feladatok nevét és az Azure-adatközpontok szállítási címeit, hogy a lemezeket vissza lehessen szállítani az Azure-ba. Ezeket az információkat később a szállítási címkén lehet használni.
   - Az importálási feladatok létrehozásához kattintson **az OK** gombra.

     ![Importálási feladatok létrehozása – 4. lépés](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>3\. lépés: a meghajtók szállítása 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>4\. lépés: a feladatok frissítése a követési adatokkal

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5\. lépés: az adatok feltöltésének ellenőrzése az Azure-ban

A feladatot nyomon követheti befejezésre. A feladatok befejezése után ellenőrizze, hogy az adatok fel lettek-e töltve az Azure-ba. Csak a feltöltés sikeres ellenőrzése után törölje a helyszíni adatok törlését.

## <a name="next-steps"></a>Következő lépések

* [A feladatok és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)


