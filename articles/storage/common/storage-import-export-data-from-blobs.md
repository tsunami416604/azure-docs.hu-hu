---
title: Adatok exportálása az Azure Blobokból az Azure-blobokból származó adatok exportálásához | Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre exportálási feladatokat az Azure Portalon az Azure Blobs-ból származó adatok átviteléhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: a5afa6439caa6b7c1572447e3b212f3357bf296a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282511"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Az Azure Import/Export szolgáltatás használata adatok Azure Blob-tárolóból való exportálására

Ez a cikk lépésenként bemutatja, hogyan használhatja az Azure import/export szolgáltatást nagy mennyiségű adat biztonságos exportálására az Azure Blob storage-ból. A szolgáltatás megköveteli, hogy az üres meghajtók szállítása az Azure adatközpontba. A szolgáltatás exportálja az adatokat a tárfiókból a meghajtókra, majd visszaküldi a meghajtókat.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehozna egy exportálási feladatot az adatok Azure Blob Storage-ból való átviteléhez, alaposan tekintse át és töltse ki a szolgáltatás előfeltételeinek alábbi listáját.
A következőket kell tennie:

- Rendelkezik egy aktív Azure-előfizetéssel, amely használható az importálási/exportálási szolgáltatáshoz.
- Legalább egy Azure Storage-fiókkal rendelkezik. Tekintse meg az [Importálási/Exportálás szolgáltatás támogatott tárfiókjainak és tárolásitípusainak](storage-import-export-requirements.md)listáját. Az új tárfiók létrehozásáról a [Tárfiók létrehozása című témakörben talál](storage-account-create.md)további információt.
- Megfelelő számú [támogatott](storage-import-export-requirements.md#supported-disks)típusú lemezzel rendelkezik.
- Van FedEx/DHL-számlája. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, lépjen `adbops@microsoft.com`kapcsolatba az Azure Data Box Operations csapatával a.
  - A számlának érvényesnek kell lennie, egyenleggel kell rendelkeznie, és visszaszállítási képességekkel kell rendelkeznie.
  - Követési szám létrehozása az exportálási feladathoz.
  - Minden feladatnak külön nyomon követési számmal kell rendelkeznie. Több feladat azonos követési számmal nem támogatott.
  - Ha nem rendelkezik szolgáltatói fiókkal, nyissa meg a következő t:
    - [FedEX-fiók létrehozása](https://www.fedex.com/en-us/create-account.html), vagy
    - [Hozzon létre egy DHL-fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>1. lépés: Exportálási feladat létrehozása

Hajtsa végre az alábbi lépéseket egy exportálási feladat létrehozásához az Azure Portalon.

1. Jelentkezzen be https://portal.azure.com/a rendszerbe.
2. Nyissa meg **a Minden szolgáltatás > Storage > Importálási/exportálási feladatok at.**

    ![Ugrás az Importálási/exportálási feladatokra](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Kattintson **az Importálási/exportálási feladat létrehozása gombra.**

    ![Kattintson az Importálási/exportálási feladatra](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. Az **alapok:**

    - Válassza **az Exportálás az Azure-ból**lehetőséget.
    - Adja meg az exportálási feladat leíró nevét. A feladatok előrehaladásának nyomon követéséhez használja a választott nevet.
        - A név csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket tartalmazhat.
        - A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközöket.
    - Válasszon egy előfizetést.
    - Erőforráscsoport megadása vagy kijelölése.

        ![Alapvető beállítások](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. A **Munka köre**:

    - Válassza ki azt a tárfiókot, ahol az exportálandó adatok találhatók. A tartózkodási helyhez közeli tárfiókot használjon.
    - A kitárolási hely automatikusan kitöltődik a kiválasztott tárfiók régiója alapján.
    - Adja meg a blob adatokat szeretne exportálni a tárfiókból az üres meghajtóra vagy meghajtókra.
    - Válassza az összes blobadat **exportálása** a tárfiókban.

         ![Az összes exportálása](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Megadhatja, hogy mely tárolókat és blobokat exportálja.
        - **Exportálandó blob megadása**: Használja az **Egyenlő a választót.** Adja meg a blob relatív elérési útját, a tároló nevével kezdve. A gyökértároló megadásához *használja $root.*
        - **Az előtaggal kezdődő összes blob megadása:** Használja a **Starts With** választógombot. Adja meg az előtagot, amely a'/' elővágással kezdődik. Az előtag lehet a tároló nevének előtagja, a teljes tároló neve vagy a teljes tárolóneve, amelyet a blobnév előtagja követ. Meg kell adnia a blob elérési útjait érvényes formátumban a feldolgozás során fellépő hibák elkerülése érdekében, ahogy az ezen a képernyőképen látható. További [információ: Példák érvényes blob elérési utakra.](#examples-of-valid-blob-paths)

           ![Kijelölt tárolók és blobok exportálása](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Exportálható a bloblista fájlból.

        ![Exportálás bloblistafájlból](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  

   > [!NOTE]
   > Ha az exportálandó blob az adatmásolás során használatban van, az Azure importálási/exportálási szolgáltatás pillanatképet készít a blobról, és másolja a pillanatképet.

6. Cserébe **szállítási információk:**

    - Válassza ki a szolgáltatót a legördülő listából. Ha a FedEx/DHL-től eltérő szolgáltatót szeretne használni, válasszon egy meglévő lehetőséget a legördülő menüből. Lépjen kapcsolatba az Azure `adbops@microsoft.com` Data Box Operations csapatával a használni kívánt szolgáltatóval kapcsolatos információkkal.
    - Adja meg az adott szolgáltatóval létrehozott érvényes szállítmányozói számlaszámot. A Microsoft ezzel a fiókkal szállítja vissza a meghajtókat, amint az exportálási feladat befejeződött.
    - Adja meg a kapcsolattartó teljes és érvényes nevét, telefonszámát, e-mail címét, lakcímét, városát, irányítószámát, államát/tartományát és országát/régióját.

        > [!TIP]
        > Ahelyett, hogy egyetlen felhasználó e-mail címét adna meg, adjon meg egy csoportos e-mailt. Ez biztosítja, hogy értesítést kapjon akkor is, ha egy rendszergazda kilép.

7. **Összefoglalva:**

    - Tekintse át a feladat részleteit.
    - Jegyezze fel a feladat nevét, és adja meg az Azure datacenter szállítási címét lemezek Azure-ba szállításához.

        > [!NOTE]
        > A lemezeket mindig küldje el az Azure Portalon megjegyezte adatközpontba. Ha a lemezeket nem a megfelelő adatközpontba szállítják, a feladat feldolgozása nem történik meg.

    - Kattintson **az OK** gombra a feladatlétrehozásának befejezéséhez.

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>2. lépés: A meghajtók szállítása

Ha nem tudja, hogy hány meghajtóra van szüksége, nyissa meg [a Meghajtók számának ellenőrzése című.](#check-the-number-of-drives) Ha tudja a meghajtók számát, folytassa a meghajtók szállításával.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3. lépés: A feladat frissítése nyomon követési információkkal

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>4. lépés: A lemezek fogadása

Amikor az irányítópult jelenti, hogy a feladat befejeződött, a lemezeket kiszállítják Önnek, és a szállítmány fuvarlevélszáma elérhető a portálon.

1. Miután megkapta a meghajtókat az exportált adatokkal, be kell szereznie a BitLocker kulcsokat a meghajtók zárolásának feloldásához. Nyissa meg az exportálási feladatot az Azure Portalon. Kattintson **az Importálás/exportálás** fülre.
2. Jelölje ki és kattintson az exportálási feladatra a listából. Nyissa meg a **Titkosítás gombot,** és másolja a kulcsokat.

   ![BitLocker-kulcsok megtekintése exportálási feladathoz](./media/storage-import-export-service/export-job-bitlocker-keys-02.png)

3. A lemezek zárolásának feloldásához használja a BitLocker-kulcsokat.

Az exportálás befejeződött.

## <a name="step-5-unlock-the-disks"></a>5. lépés: A lemezek zárolásának feloldása

Ha a WAImportExport eszköz 1.4.0.300-as verzióját használja, a következő paranccsal oldja fel a meghajtó zárolását:

    `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`  

Ha az eszköz korábbi verzióit használja, a BitLocker párbeszédpanelen oldja fel a meghajtó zárolását.

Ekkor törölheti vagy elhagyhatja a feladatot. A feladatok 90 nap után automatikusan törlődnek.

## <a name="check-the-number-of-drives"></a>A meghajtók számának ellenőrzése

Ez *a választható* lépés segít meghatározni az exportálási feladathoz szükséges meghajtók számát. Hajtsa végre ezt a lépést [támogatott operációsrendszer-verziót](storage-import-export-requirements.md#supported-operating-systems)futtató Windows rendszeren.

1. [Töltse le a WAImportExport 1-es verzióját](https://www.microsoft.com/download/details.aspx?id=42659) a Windows rendszerre.
2. Csomagoljon ki az `waimportexportv1`alapértelmezett mappába . Például: `C:\WaImportExportV1`.
3. Nyisson meg egy rendszergazdai jogosultságokkal rendelkező PowerShell- vagy parancssori ablakot. Ha a könyvtárat a kibontott mappára szeretné módosítani, futtassa a következő parancsot:

    `cd C:\WaImportExportV1`

4. A kijelölt blobokhoz szükséges lemezek számának ellenőrzéséhez futtassa a következő parancsot:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    A paramétereket a következő táblázat ismerteti:

    |Parancssori paraméter|Leírás|  
    |--------------------------|-----------------|  
    |**/logdir:**|Választható. A naplókönyvtár. A részletes naplófájlok ebbe a könyvtárba kerülnek. Ha nincs megadva, az aktuális könyvtár lesz a naplókönyvtár.|  
    |**/sn:**|Kötelező. Az exportálási feladat tárfiókjának neve.|  
    |**/sk:**|Csak akkor szükséges, ha nincs megadva tárolóSAS. Az exportálási feladat tárfiókjának fiókkulcsa.|  
    |**/csas:**|Csak akkor szükséges, ha nincs megadva tárfiókkulcs. A tároló SAS az exportálási feladatban exportálandó blobok listázásához.|  
    |**/ExportBlobListFile:**|Kötelező. Az exportálandó blobok elérési útjait vagy blobelérési út-előtagok listáját tartalmazó XML-fájl elérési útjainak elérési útját tartalmazó elérési utat. Az importálási/exportálási `BlobListBlobPath` szolgáltatás REST [API-jának Put Job](/rest/api/storageimportexport/jobs) műveletében használt fájlformátum.|  
    |**/DriveSize:**|Kötelező. Az exportálási feladathoz használandó meghajtók mérete, *pl.* 500 GB, 1,5 TB.|  

    Lásd [a PreviewExport parancs példáját.](#example-of-previewexport-command)

5. Ellenőrizze, hogy tud-e olvasni/írni az exportálási feladathoz szállított meghajtókra.

### <a name="example-of-previewexport-command"></a>Példa a PreviewExport parancs használatára

A következő példa `PreviewExport` bemutatja a parancsot:  

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```  

Az exportálási bloblistafájl blobneveket és blob-előtagokat tartalmazhat, ahogy az itt látható:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Az Azure importálási/exportálási eszköz felsorolja az összes exportálandó blobokat, és kiszámítja, hogyan csomagolhatja őket a megadott méretű meghajtókba, figyelembe véve a szükséges többletterhelést, majd megbecsüli a blobok és a meghajtó használati adatainak tárolására szükséges meghajtók számát.  

Íme egy példa a kimenetre, a tájékoztató naplók kimaradt:  

```powershell
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  

Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Példák érvényes blobelérési utakra

Az alábbi táblázat példákat mutat be érvényes blobelérési utakra:

   | Szelektor | Blob elérési útja | Leírás |
   | --- | --- | --- |
   | Kezdődik |/ |A tárfiókban lévő összes blob exportálása |
   | Kezdődik |/$root/ |A gyökértárolóban lévő összes blob exportálása |
   | Kezdődik |/könyv |Az **előtagkönyvvel** kezdődő tárolókban lévő összes blob exportálása |
   | Kezdődik |/zene/ |Az összes blob exportálása **tárolózenében** |
   | Kezdődik |/zene/szerelem |Az összes blob exportálása a **tárolózene,amely** előtag **szeretettel** kezdődik |
   | Egyenlő |$root/logo.bmp |Exportálás, a blob **logo.bmp** a gyökértárolóban |
   | Egyenlő |videók/story.mp4 |Blob **story.mp4** exportálása **tárolóvideókban** |

## <a name="next-steps"></a>További lépések

- [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
- [Importálási/exportálási követelmények áttekintése](storage-import-export-requirements.md)
