---
title: Adatok azure-blobokba való átvitelének használata az Azure-importálással/exportálással | Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre importálási és exportálási feladatokat az Azure Portalon az Azure Blobs-ba és onnan történő adatátvitelhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 570c663861361a19190f6fb5d608b6aa029a0885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282494"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Adatok importálása az Azure Blob Storage-ba az Azure Import/Export szolgáltatással

Ez a cikk lépésenként bemutatja, hogyan használhatja az Azure import/export szolgáltatást nagy mennyiségű adat biztonságos importálásához az Azure Blob storage-ba. Az Adatok Azure Blobs-ba importálásához a szolgáltatás megköveteli, hogy az adatokat tartalmazó titkosított lemezmeghajtókat szállítson egy Azure-adatközpontba.  

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehozna egy importálási feladatot az adatok Azure Blob Storage-ba való átviteléhez, alaposan tekintse át és töltse ki a szolgáltatás előfeltételeinek alábbi listáját.
A következőket kell tennie:

* Rendelkezik egy aktív Azure-előfizetéssel, amely használható az importálási/exportálási szolgáltatáshoz.
* Legalább egy Azure Storage-fiók egy tárolótárolóval rendelkezik. Tekintse meg az [Importálási/Exportálás szolgáltatás támogatott tárfiókjainak és tárolásitípusainak](storage-import-export-requirements.md)listáját.
  * Az új tárfiók létrehozásáról a [Tárfiók létrehozása című témakörben talál](storage-account-create.md)további információt.
  * A tárolótárolóval kapcsolatos információkért látogasson el [a Tároló létrehozása című terület](../blobs/storage-quickstart-blobs-portal.md#create-a-container)című.
* Megfelelő számú [támogatott](storage-import-export-requirements.md#supported-disks)típusú lemezzel rendelkezik.
* [Támogatott operációsrendszer-verziót](storage-import-export-requirements.md#supported-operating-systems)futtató Windows rendszerrel kell rendelkeznie.
* Engedélyezze a BitLocker szolgáltatást a Windows rendszeren. [Lásd: A BitLocker engedélyezése](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Töltse le a legújabb WAImportExport 1-es verziót](https://www.microsoft.com/download/details.aspx?id=42659) a Windows rendszerre. Az eszköz legújabb verziója rendelkezik biztonsági frissítésekkel, amelyek lehetővé teszik a BitLocker kulcs külső védőjét és a frissített feloldási mód funkciót.

  * Csomagoljon ki az `waimportexportv1`alapértelmezett mappába . Például: `C:\WaImportExportV1`.
* Van FedEx/DHL-számlája. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, lépjen `adbops@microsoft.com`kapcsolatba az Azure Data Box Operations csapatával a.  
  * A számlának érvényesnek kell lennie, egyenleggel kell rendelkeznie, és visszaszállítási képességekkel kell rendelkeznie.
  * Követési szám létrehozása az exportálási feladathoz.
  * Minden feladatnak külön nyomon követési számmal kell rendelkeznie. Több feladat azonos követési számmal nem támogatott.
  * Ha nem rendelkezik szolgáltatói fiókkal, nyissa meg a következő t:
    * [FedEX-fiók létrehozása](https://www.fedex.com/en-us/create-account.html), vagy
    * [Hozzon létre egy DHL-fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>1. lépés: A meghajtók előkészítése

Ez a lépés naplófájlt hoz létre. A naplófájl olyan alapvető információkat tárol, mint a meghajtó sorozatszáma, a titkosítási kulcs és a tárfiók adatai.

Hajtsa végre az alábbi lépéseket a meghajtók előkészítéséhez.

1. Csatlakoztassa lemezmeghajtóit a Windows rendszerhez sata csatlakozókon keresztül.
2. Hozzon létre egyetlen NTFS-kötetet minden meghajtón. Meghajtóbetűjel hozzárendelése a kötethez. Ne használjon rögzítési pontokat.
3. Engedélyezze a BitLocker titkosítást az NTFS-köteten. Ha Windows Server rendszert használ, kövesse a [BitLocker engedélyezése Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)rendszeren című útmutató utasításait.
4. Adatok másolása titkosított kötetre. Használja drag and drop vagy Robocopy vagy bármely ilyen másolási eszköz. A naplófájl (*.jrn*) ugyanabban a mappában jön létre, amelyben az eszközt futtatja.

   Ha a meghajtó zárolva van, és fel kell oldania a meghajtó zárolását, a feloldáslépései a használati egésztől függően eltérőek lehetnek.

   * Ha előre titkosított meghajtóhoz adott adatokat (a WAImportExport eszközt nem használták titkosításra), használja a bitlocker kulcsot (a megadott numerikus jelszót) a felugró ablakban a meghajtó zárolásának feloldásához.

   * Ha a WAImportExport eszközzel titkosított meghajtóhoz adott adatokat, a következő paranccsal oldja fel a meghajtó zárolását:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Nyisson meg egy rendszergazdai jogosultságokkal rendelkező PowerShell- vagy parancssori ablakot. Ha a könyvtárat a kibontott mappára szeretné módosítani, futtassa a következő parancsot:

    `cd C:\WaImportExportV1`
6. A meghajtó BitLocker kulcsának beírásához futtassa a következő parancsot:

    `manage-bde -protectors -get <DriveLetter>:`
7. A lemez előkészítéséhez futtassa a következő parancsot. **Az adatok méretétől függően ez több órát vagy napot is igénybe vehet.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    A naplófájl ugyanabban a mappában jön létre, amelyben az eszközt futtatta. Két másik fájl is létrejön - egy *.xml* fájl (mappa, ahol futtatja az eszközt) és egy *drive-manifest.xml* fájl (mappa, ahol az adatok találhatók).

    Az alkalmazott paramétereket a következő táblázat ismerteti:

    |Beállítás  |Leírás  |
    |---------|---------|
    |/j:     |A naplófájl neve.jrn kiterjesztéssel. A naplófájl meghajtónként jön létre. Javasoljuk, hogy a lemez sorozatszámát használja naplófájlnévként.         |
    |/id:     |A munkamenet-azonosító. A parancs minden példányához használjon egyedi munkamenetszámot.      |
    |/t:     |A szállítandó lemez meghajtóbetűjele. Például a `D`meghajtó .         |
    |/bk:     |A meghajtó BitLocker kulcsa. A numerikus jelszót kimeneti`manage-bde -protectors -get D:`      |
    |/srcdir:     |A szállítandó lemez meghajtóbetűjele, `:\`amelyet a követ. Például: `D:\`.         |
    |/dstdir:     |A céltároló neve az Azure Storage-ban.         |
    |/blobtype:     |Ez a beállítás határozza meg, hogy milyen típusú blobok az adatokat importálni szeretné. A blokkblobok esetében `BlockBlob` ez, és az `PageBlob`oldalblobok esetében ez a .         |
    |/skipwrite:     |Elő kell készíteni azt a beállítást, amely megadja, hogy nincs szükség új adatok másolására és a lemezen lévő adatokra.          |
    |/enablecontentmd5:     |A beállítás, ha engedélyezve van, biztosítja, hogy `Content-md5` az MD5 kiszámítása és beállítása tulajdonságként minden blobon. Csak akkor használja ezt a `Content-md5` beállítást, ha az adatok Azure-ba való feltöltése után szeretné használni a mezőt. <br> Ez a beállítás nincs hatással az adatintegritás ellenőrzésére (amely alapértelmezés szerint történik). A beállítás növeli az adatok felhőbe való feltöltéséhez szükséges időt.          |
8. Ismételje meg az előző lépést minden egyes kiszállítandó lemezesetében. A megadott névvel rendelkező naplófájl a parancssor minden egyes futtatásához létrejön.

    > [!IMPORTANT]
    > * A naplófájllal együtt `<Journal file name>_DriveInfo_<Drive serial ID>.xml` egy fájl is létrejön ugyanabban a mappában, ahol az eszköz található. A naplófájl helyett az .xml fájl használatos, ha a naplófájl túl nagy.

## <a name="step-2-create-an-import-job"></a>2. lépés: Importálási feladat létrehozása

Az alábbi lépések végrehajtásával hozzon létre egy importálási feladatot az Azure Portalon.

1. Jelentkezzen be https://portal.azure.com/a rendszerbe.
2. Nyissa meg **a Minden szolgáltatás > Storage > Importálási/exportálási feladatok at.**

    ![Ugrás az Importálási/exportálási feladatokra](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kattintson **az Importálási/exportálási feladat létrehozása gombra.**

    ![Kattintson az Importálási/exportálási feladat parancsra.](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Az **alapok:**

   * Válassza **az Importálás az Azure-ba**lehetőséget.
   * Adja meg az importálási feladat leíró nevét. A név segítségével nyomon követheti a feladatok előrehaladását.
       * A név csak kisbetűket, számokat és kötőjeleket tartalmazhat.
       * A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközöket.
   * Válasszon egy előfizetést.
   * Erőforráscsoport megadása vagy kijelölése.  

     ![Importálási feladat létrehozása – 1.](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. A **Munka köre**:

   * Töltse fel a meghajtó előkészítése lépés során kapott meghajtónapló-fájlokat. Ha `waimportexport.exe version1` használták, töltsön fel egy fájlt minden egyes elkészített meghajtóhoz. Ha a naplófájl mérete meghaladja a 2 `<Journal file name>_DriveInfo_<Drive serial ID>.xml` MB-ot, akkor használhatja a naplófájllal létrehozott fájlt is.
   * Válassza ki azt a céltárfiókot, ahol az adatok hol fognak elraktárul állni.
   * A kitárolási hely automatikusan kitöltődik a kiválasztott tárfiók régiója alapján.

   ![Importálási feladat létrehozása - 2.](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. Cserébe **szállítási információk:**

   * Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, válasszon egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba az Azure `adbops@microsoft.com` Data Box Operations csapatával a használni kívánt szolgáltatóval kapcsolatos információkkal.
   * Adja meg az adott szolgáltatóval létrehozott érvényes szállítmányozói számlaszámot. A Microsoft ezzel a fiókkal szállítja vissza a meghajtókat, amint az importálási feladat befejeződött. Ha nem rendelkezik számlaszámmal, hozzon létre [FedEx](https://www.fedex.com/us/oadr/) vagy [DHL](https://www.dhl.com/) szolgáltatói fiókot.
   * Adja meg a kapcsolattartó teljes és érvényes nevét, telefonszámát, e-mail címét, lakcímét, városát, irányítószámát, államát/tartományát és országát/régióját.

       > [!TIP]
       > Ahelyett, hogy egyetlen felhasználó e-mail címét adna meg, adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon akkor is, ha egy rendszergazda kilép.

     ![Importálási feladat létrehozása - 3.](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. Az **összefoglalóban:**

   * Tekintse át az összefoglalóban megadott projektinformációkat. Jegyezze fel a feladat nevét és az Azure datacenter szállítási címét a lemezek et az Azure-ba. Ezt az információt a szállítási címke későbbi részében használjuk fel.
   * Az importálási feladat létrehozásához kattintson az **OK** gombra.

     ![Importálási feladat létrehozása - 4.](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>3. lépés (nem kötelező): Ügyfél által kezelt kulcs konfigurálása

Hagyja ki ezt a lépést, és folytassa a következő lépéssel, ha a Microsoft által kezelt kulccsal szeretné védeni a BitLocker-kulcsokat a meghajtókszámára. Ha saját kulcsot szeretne konfigurálni a BitLocker-kulcs védelmére, kövesse az [Ügyfél által felügyelt kulcsok konfigurálása az Azure Key Vault for Azure Import/Export](storage-import-export-encryption-key-portal.md) című útmutató utasításait az Azure Portalon

## <a name="step-4-ship-the-drives"></a>4. lépés: A meghajtók szállítása

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>5. lépés: A feladat frissítése nyomon követési információkkal

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>6. lépés: Adatok feltöltésének ellenőrzése az Azure-ba

Kövesse nyomon a feladatot a befejezésig. Miután a feladat befejeződött, ellenőrizze, hogy az adatok feltöltve az Azure-ba. A helyszíni adatok törlése csak azt követően, hogy meggyőződött arról, hogy a feltöltés sikeres volt.

## <a name="next-steps"></a>További lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)
