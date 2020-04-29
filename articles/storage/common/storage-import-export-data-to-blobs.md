---
title: Az Azure import/export használata az adatok Azure-Blobokra történő átviteléhez | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre importálási és exportálási feladatokat Azure Portal az Azure-Blobokra irányuló és onnan érkező adatok átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 570c663861361a19190f6fb5d608b6aa029a0885
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80282494"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Az Azure import/export szolgáltatás használata az Azure-ba való adatimportálásra Blob Storage

Ez a cikk részletesen ismerteti, hogyan használható az Azure import/export szolgáltatás a nagy mennyiségű, az Azure Blob Storage-ba való biztonságos importáláshoz. Az Azure-Blobokra történő adatimportáláshoz a szolgáltatáshoz az adatait tartalmazó titkosított lemezmeghajtókat kell szállítani egy Azure-adatközpontba.  

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehoz egy importálási feladatot az adatok Azure Blob Storageba való átviteléhez, gondosan tekintse át és végezze el az alábbi, a szolgáltatásra vonatkozó előfeltételeket tartalmazó listát.
A következőket kell tennie:

* Az import/export szolgáltatáshoz használható aktív Azure-előfizetéssel rendelkezik.
* Rendelkeznie kell legalább egy Azure Storage-fiókkal tárolóval. Tekintse meg a [támogatott Storage-fiókok és tárolási típusok listáját az importálási/exportálási szolgáltatáshoz](storage-import-export-requirements.md).
  * További információ az új Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](storage-account-create.md).
  * A Storage-tárolóval kapcsolatos információkért nyissa meg a Storage-tároló [létrehozása](../blobs/storage-quickstart-blobs-portal.md#create-a-container)című témakört.
* Megfelelő számú lemezzel rendelkezik a [támogatott típusok](storage-import-export-requirements.md#supported-disks)közül.
* Egy [támogatott operációsrendszer-verzióval](storage-import-export-requirements.md#supported-operating-systems)rendelkező Windows rendszerre van telepítve.
* Engedélyezze a BitLockert a Windows rendszeren. Lásd: [a BitLocker engedélyezése](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Töltse le a legújabb WAImportExport 1-es verzióját](https://www.microsoft.com/download/details.aspx?id=42659) a Windows rendszerre. Az eszköz legújabb verziója olyan biztonsági frissítésekkel rendelkezik, amelyek lehetővé teszik a BitLocker-kulcs külső oltalmazójának és a frissített feloldási mód funkciójának használatát.

  * Bontsa ki az alapértelmezett `waimportexportv1`mappát. Például: `C:\WaImportExportV1`.
* Van egy FedEx/DHL-fiókja. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, lépjen kapcsolatba Azure Data Box Operations `adbops@microsoft.com`csapatával a következő címen:.  
  * A fióknak érvényesnek kell lennie, egyensúlyt kell tartalmaznia, és vissza kell adni a szállítási képességeket.
  * Nyomkövetési szám létrehozása az exportálási feladatokhoz.
  * Minden feladattípusnak külön nyomkövetési számmal kell rendelkeznie. Nem támogatott több feladat ugyanazzal a követési számmal.
  * Ha nem rendelkezik Carrier-fiókkal, lépjen a következőre:
    * [Hozzon létre egy FedEx-fiókot](https://www.fedex.com/en-us/create-account.html), vagy
    * [Hozzon létre egy DHL-fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>1. lépés: a meghajtók előkészítése

Ez a lépés egy naplófájlt hoz létre. A naplófájl olyan alapvető adatokat tárol, mint például a meghajtó sorozatszáma, a titkosítási kulcs és a Storage-fiók adatai.

A meghajtók előkészítéséhez végezze el a következő lépéseket.

1. Csatlakoztathatja a lemezmeghajtókat a Windows rendszerhez a SATA-összekötők használatával.
2. Hozzon létre egyetlen NTFS-kötetet az egyes meghajtókon. Rendeljen meghajtóbetűjelet a kötethez. Ne használja a csatolási.
3. Engedélyezze a BitLocker-titkosítást az NTFS-köteten. Ha Windows Server rendszert használ, használja a [Windows server 2012 R2 rendszeren a BitLocker engedélyezésének](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)útmutatását.
4. Az Adatmásolás titkosított kötetre. A drag and drop vagy a Robocopy vagy bármely ilyen másolási eszköz használatával. A Journal-(*. jrn*) fájl ugyanabban a mappában jön létre, ahol az eszközt futtatja.

   Ha a meghajtó zárolva van, és fel kell oldania a meghajtó zárolását, a zárolás lépései a használati esettől függően eltérőek lehetnek.

   * Ha egy előre titkosított meghajtóhoz (WAImportExport eszközt nem használt a titkosításhoz) adott hozzá adatkészletet, a meghajtó zárolásának feloldásához használja az előugró ablakban a BitLocker-kulcsot (a megadott numerikus jelszót).

   * Ha a WAImportExport eszköz által titkosított meghajtóhoz adott meg egy olyan meghajtót, amelyet a következő paranccsal lehet feloldani a meghajtó zárolásának feloldásához:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Nyisson meg egy PowerShell-vagy parancssori ablakot rendszergazdai jogosultságokkal. Ha a könyvtárat a kibontott mappára szeretné módosítani, futtassa a következő parancsot:

    `cd C:\WaImportExportV1`
6. A meghajtó BitLocker-kulcsának beszerzéséhez futtassa a következő parancsot:

    `manage-bde -protectors -get <DriveLetter>:`
7. A lemez előkészítéséhez futtassa a következő parancsot. **Az adatmérettől függően ez több órát is igénybe vehet.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    A rendszer ugyanabban a mappában hozza létre a naplófájlt, ahol az eszközt futtatta. Két másik fájl is létrejön – egy *. XML* fájl (az eszközt futtató mappa) és egy *Drive-manifest. XML* fájl (a mappa, ahol az adat található).

    A használt paramétereket a következő táblázat ismerteti:

    |Beállítás  |Leírás  |
    |---------|---------|
    |/j     |A naplófájl neve, a. jrn kiterjesztéssel. Egy naplófájl jön létre egy meghajtón. Javasoljuk, hogy a lemez sorozatszámát használja a naplófájl neveként.         |
    |/ID     |A munkamenet-azonosító. A parancs minden példányához használjon egyedi munkamenet-számot.      |
    |/t:     |A szállítandó lemez meghajtóbetűjele. Például: meghajtó `D`.         |
    |BK     |A meghajtó BitLocker-kulcsa. A numerikus jelszava a kimenetből`manage-bde -protectors -get D:`      |
    |/srcdir:     |A szállítandó lemez meghajtóbetűjele, majd a `:\`. Például: `D:\`.         |
    |/dstdir:     |A cél tároló neve az Azure Storage-ban.         |
    |/blobtype:     |Ezzel a beállítással adható meg, hogy milyen típusú blobokat kíván importálni az alkalmazásba. A blokk Blobok esetében ez `BlockBlob` a és a Blobok esetében is `PageBlob`.         |
    |/skipwrite:     |Ez a beállítás azt határozza meg, hogy nincs szükség új adatmásolásra és a lemezen lévő meglévő adatfeldolgozásra.          |
    |/enablecontentmd5:     |Ha a beállítás engedélyezve van, biztosítja, hogy az MD5 kiszámítva `Content-md5` legyen, és az egyes Blobok tulajdonságként legyen beállítva. Ezt a lehetőséget csak akkor használja, ha a `Content-md5` mezőt az Azure-ba való feltöltés után szeretné használni. <br> Ez a beállítás nincs hatással az adatintegritás-ellenőrzésre (alapértelmezés szerint ez történik). A beállítással megnövelhető az adatok felhőbe való feltöltéséhez szükséges idő.          |
8. Ismételje meg az előző lépést minden egyes leszállításra szoruló lemez esetében. A rendszer a megadott névvel rendelkező naplófájlt hoz létre a parancssor minden futtatásához.

    > [!IMPORTANT]
    > * A Journal-fájllal együtt egy `<Journal file name>_DriveInfo_<Drive serial ID>.xml` fájl is jön létre ugyanabban a mappában, ahol az eszköz található. Ha a naplófájl túl nagy, a rendszer a naplófájl helyett a. xml fájlt használja.

## <a name="step-2-create-an-import-job"></a>2. lépés: importálási feladatok létrehozása

Az alábbi lépések végrehajtásával hozzon létre egy importálási feladatot a Azure Portal.

1. Jelentkezzen be https://portal.azure.com/a következőre:.
2. Lépjen az **összes szolgáltatás > Storage > importálási/exportálási feladatok lehetőségre**.

    ![Ugrás az importálási/exportálási feladatokra](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kattintson az **importálási/exportálási feladatok létrehozása**lehetőségre.

    ![Kattintson az importálási/exportálási feladatok létrehozása lehetőségre.](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Az **alapjaiban**:

   * Válassza **az Importálás az Azure-ba**lehetőséget.
   * Adjon meg egy leíró nevet az importálási feladatokhoz. A név használatával követheti nyomon a feladatok állapotát.
       * A név csak kisbetűket, számokat és kötőjeleket tartalmazhat.
       * A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközt.
   * Válasszon egy előfizetést.
   * Adjon meg vagy válasszon ki egy erőforráscsoportot.  

     ![Importálási feladatok létrehozása – 1. lépés](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. A **feladatok részletei**:

   * Töltse fel a meghajtó-előkészítési lépés során beszerzett meghajtó-napló fájljait. Ha `waimportexport.exe version1` használta, töltsön fel egy fájlt minden előkészített meghajtóra. Ha a naplófájl mérete meghaladja a 2 MB-ot, akkor használhatja a `<Journal file name>_DriveInfo_<Drive serial ID>.xml` fájlt is a Journal-fájllal.
   * Válassza ki a cél Storage-fiókot, ahol az adat található.
   * A lemorzsolódási helye automatikusan kitöltődik a kiválasztott Storage-fiók régiója alapján.

   ![Importálási feladatok létrehozása – 2. lépés](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. **Visszaszállítási adatok**:

   * Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, válasszon ki egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba Azure Data Box operatív csapatával `adbops@microsoft.com` a használni kívánt szolgáltatóra vonatkozó információkkal.
   * Adjon meg egy érvényes, a szállítóval létrehozott számlaszámot. A Microsoft ezt a fiókot használja a meghajtók visszaszállításához az importálási feladatok befejezését követően. Ha nem rendelkezik fiókkal, hozzon létre egy [FedEx](https://www.fedex.com/us/oadr/) vagy [DHL](https://www.dhl.com/) Carrier-fiókot.
   * Adjon meg egy teljes és érvényes nevet, telefont, e-mailt, utcanév-címet, várost, irányítószámot, államot/régiót és országot/régiót.

       > [!TIP]
       > E-mail-cím egyetlen felhasználóhoz való megadása helyett adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon, még akkor is, ha a rendszergazda elhagyja.

     ![Importálási feladatok létrehozása – 3. lépés](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. Az **összegzésben**:

   * Tekintse át az összegzésben megadott feladattal kapcsolatos információkat. Jegyezze fel a feladatok nevét és az Azure-adatközpontok szállítási címeit, hogy a lemezeket vissza lehessen szállítani az Azure-ba. Ezeket az információkat később a szállítási címkén lehet használni.
   * Az importálási feladatok létrehozásához kattintson **az OK** gombra.

     ![Importálási feladatok létrehozása – 4. lépés](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>3. lépés (nem kötelező): az ügyfél által felügyelt kulcs konfigurálása

Hagyja ki ezt a lépést, és folytassa a következő lépéssel, ha a Microsoft által felügyelt kulccsal szeretné védetté tenni a meghajtók BitLocker-kulcsait. Ha saját kulcsát szeretné beállítani a BitLocker-kulcs védeleméhez, kövesse az [ügyfél által felügyelt kulcsok konfigurálása az Azure-beli Importálás/exportálás Azure Key Vault az Azure Portal](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>4. lépés: a meghajtók szállítása

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>5. lépés: a feladatok frissítése a nyomkövetési adatokkal

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>6. lépés: az adatok feltöltésének ellenőrzése az Azure-ban

A feladatot nyomon követheti befejezésre. A feladatok befejezése után ellenőrizze, hogy az adatok fel lettek-e töltve az Azure-ba. Csak a feltöltés sikeres ellenőrzése után törölje a helyszíni adatok törlését.

## <a name="next-steps"></a>További lépések

* [A feladatok és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)
