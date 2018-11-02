---
title: Az Azure Import/Export segítségével exportál adatokat az Azure-Blobok |} A Microsoft Docs
description: Ismerje meg, hogyan export-feladatok létrehozása az Azure Portalon adatok átviteléhez az Azure-Blobokból.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/17/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 60a92e35f1408d944e8091be4945c53abe3791d1
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50754052"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Adatok exportálása az Azure Blob storage-ból az Azure Import/Export szolgáltatás használata
Ebben a cikkben részletes útmutatás az Azure Import/Export szolgáltatás használatával nagy mennyiségű adat biztonságosan exportálása az Azure Blob storage-ból. A szolgáltatás megköveteli, hogy az Azure-adatközpontba üres meghajtókon szállításra. A szolgáltatás adatokat exportál a tárfiók a meghajtók, és vissza a meghajtók majd tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

Viheti át az adatokat az Azure Blob Storage exportálási feladat létrehozása, előtt gondosan tekintse át, és hajtsa végre az alábbi listában szereplő Előfeltételek ezt a szolgáltatást. Tegye a következőket:

- Egy aktív Azure-előfizetéssel rendelkezik az Import/Export szolgáltatás használható.
- Legalább egy Azure Storage-fiók rendelkezik. Listájának megtekintéséhez [az Import/Export szolgáltatás által támogatott storage-fiókok és a tárolási típusok](storage-import-export-requirements.md). Új tárfiók létrehozásával kapcsolatos információkért lásd: [Storage-fiók létrehozása](storage-quickstart-create-account.md).
- A lemezek elegendő számú [támogatott típusok](storage-import-export-requirements.md#supported-disks).
- FedEx/DHL fiókkal rendelkeznie.  
    - A fiók érvényesnek kell lennie, kell rendelkeznie az egyenleg és visszaszállítási képességek kell rendelkeznie.
    - Követési szám az exportálási feladat létrehozása.
    - Minden feladat rendelkeznie kell egy külön követési szám. Több feladat egy követési szám nem támogatottak. 
    - Ha nem rendelkezik egy szállítmányozói fiókjára, Ugrás:
        - [Hozzon létre egy FedEX fiókot](https://www.fedex.com/en-us/create-account.html), vagy 
        - [Hozzon létre egy DHL fiókot](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>1. lépés: Exportálási feladat létrehozása

A következő lépésekkel exportálási feladat létrehozása az Azure Portalon.

1. Jelentkezzen be https://portal.azure.com/.
2. Lépjen a **minden szolgáltatás > tárolás > importálási/exportálási feladatok**. 

    ![Ugrás az importálási és exportálási feladatokhoz](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Kattintson a **importálási/exportálási feladat létrehozása**.

    ![Kattintson az importálási/exportálási feladat](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. A **alapjai**:
    
    - Válassza ki **exportálása az Azure-ból**. 
    - Adjon meg egy leíró nevet az exportálási feladatot. Úgy dönt, hogy a folyamat előrehaladását a feladatok nevet használja. 
        - A név csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket tartalmazhat.
        - A névnek betűvel kell kezdődnie, és nem tartalmazhat szóközt. 
    - Válasszon egy előfizetést.
    - Adja meg, vagy válasszon ki egy erőforráscsoportot.

        ![Alapvető beállítások](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. A **feladat részletei**:

    - Válassza ki a tárfiókot, ahol az adatok exportálásának találhatók. Közel elhelyezkedő egy storage-fiókot használni.
    - Dropoff helyét a rendszer automatikusan kitölti a kiválasztott tárfiók a régióhoz igazodik. 
    - Adja meg a kívánt exportálja az Ön tárfiókjából az üres meghajtó vagy meghajtókat Blobadatok. 
    - Válassza ki a **minden kiviteli** a tárfiókban lévő blobadatokat.
    
         ![Összes exportálása](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Megadhatja, hogy mely tárolók és blobok exportálása.
        - **Exportálandó blobok megadásához**: használja a **Equal To** választó. Adja meg a blob, a tároló nevét kezdetű relatív elérési útját. Használat *$root* , adja meg a gyökérszintű tárolóban.
        - **Adja meg az összes BLOB előtaggal kezdődik,**: használja a **kezdődik** választó. Adja meg az előtag, kezdve a perjellel (/). Az előtag lehet az előtag a tároló nevét, a teljes körű neve vagy a teljes körű neve követ a blobnév előtagja. Ezen a képernyőfelvételen látható módon meg kell adnia a blob elérési útjának érvényes formátumban a feldolgozás során hibák elkerülése érdekében. További információkért lásd: [érvényes blob elérési útjának példái](#examples-of-valid-blob-paths). 
   
           ![Kiválasztott tárolók és blobok exportálása](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - A bloblistafájlból exportálhatja.

        ![Exportálás bloblistafájlból](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Adatmásolás során a blob exportálható van használatban, ha az Azure Import/Export szolgáltatás pillanatképet készít a blob, és átmásolja a pillanatképet.
 

4. A **szállítási adatok visszaadása**:

    - A legördülő listából válassza ki a szolgáltató.
    - Adjon meg egy érvényes Szállítmányozó számlaszáma, amelyek a szolgáltató létrehozta. A Microsoft ezt a fiókot használja, a meghajtók vissza tehetnek az importálási feladat befejeződése után. 
    - Adja meg a kész, érvényes ügyfél nevét, telefonszám, e-mail, utca, házszám, város, zip, államot/megyét és ország/régió.

        > [!TIP] 
        > Helyett adjon meg egy e-mail címet, egy-egy felhasználóhoz, adjon meg egy csoport e-mail-címet. Ez biztosítja, értesítések fogadására, még akkor is, ha egy rendszergazda hagyja.
   
5. A **összefoglalás**:

    - Tekintse át a feladat részleteit.
    - Jegyezze fel a feladat neve és a szállítási cím a szállítási lemezeket az Azure-ban megadott Azure-adatközpontban. 

        > [!NOTE] 
        > A lemezek elküldése mindig az adatközpontba, jelezve, az Azure Portalon. A lemezek a nem megfelelő adatközpontba tartalmazza a szükséges, ha a feladat nem fogja feldolgozni.

    - Kattintson a **OK** exportálási feladat létrehozásának befejezéséhez.

## <a name="step-2-ship-the-drives"></a>2. lépés: A meghajtók szállításra

Ha nem tudja, hány meghajtót van szüksége, lépjen a [ellenőrizze, hány meghajtót](#check-the-number-of-drives). Ha tudja, hány meghajtót, folytassa a meghajtók szállításra.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3. lépés: A feladat frissítése a nyomkövetési adatokat

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>4. lépés: A lemezek fogadása
Amikor az irányítópulton jelenti, hogy a feladat befejeződött, a lemezek szállítjuk, és a nyomkövetési azonosító szám a szállításra való előkészítését érhető el a portálon.

1. Miután megkapta az exportált adatokat tartalmazó meghajtókra, kell a meghajtók zárolásának feloldásához a BitLocker-kulcsok beolvasása. Nyissa meg az exportálási feladatot az Azure Portalon. Kattintson a **Import/Export** fülre. 
2. Válassza ki, majd kattintson az exportálási feladatot a listából. Lépjen a **BitLocker-kulcsok** , és másolja a kulcsokat.
   
   ![BitLocker-kulcsok exportálási feladat megtekintése](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. A BitLocker-kulcsok használata a lemez zárolásának feloldásához.

Az exportálás befejeződött. Jelenleg is törli a feladatot, vagy automatikusan lekéri törölték 90 nap után.


## <a name="check-the-number-of-drives"></a>Ellenőrizze, hány meghajtót

Ez *választható* . lépés: azt határozza meg, hány meghajtót az exportálási feladat végrehajtásához szükséges segít. Hajtsa végre ezt a lépést a Windows a rendszert egy [támogatott operációsrendszer-verzió](storage-import-export-requirements.md#supported-operating-systems).

1. [Töltse le az 1. verziójának WAImportExport](https://www.microsoft.com/en-us/download/details.aspx?id=42659) a Windows rendszeren. 
2. Csomagolja ki az alapértelmezett mappába kívánja `waimportexportv1`. Például: `C:\WaImportExportV1`.
3. Nyissa meg a PowerShell vagy a parancssori ablakot rendszergazdai jogosultságokkal. A kicsomagolt mappába könyvtár módosításához futtassa a következő parancsot:
    
    `cd C:\WaImportExportV1`

4. A kiválasztott blobokat szükséges lemezek száma ellenőrzéséhez futtassa a következő parancsot:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    A paraméterek a következő táblázat ismerteti:
    
    |Parancssori paraméter|Leírás|  
    |--------------------------|-----------------|  
    |**/ logdir:**|Választható. A naplózási könyvtár. Ez a könyvtár részletes naplófájlok kerüljenek. Ha nincs megadva, az aktuális könyvtár a naplózási könyvtár része lesz.|  
    |**/sn:**|Kötelező. A storage-fiók neve az exportálási feladatot.|  
    |**/SK:**|Csak akkor szükséges, ha nincs megadva egy SAS-tárolót. A fiókkulcs a storage-fiókját az exportálási feladatot.|  
    |**/csas:**|Csak akkor szükséges, ha nincs megadva a tárfiók kulcsára. A tároló SAS az exportálási feladatot exportálandó blobok listázásához.|  
    |**/ ExportBlobListFile:**|Kötelező. Az XML-fájl elérési útja blob elérési útjának listáját tartalmazó fájl, vagy a blob elérési útja előtagok exportálható a blobok számára. A használt fájlformátum a `BlobListBlobPath` eleme a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) az Import/Export szolgáltatás REST API-művelet.|  
    |**/ DriveSize:**|Kötelező. Exportálási feladatokhoz használható meghajtók méretének *például*, 500 GB, 1,5 TB.|  

    Tekintse meg az [példa a PreviewExport parancs](#example-of-previewexport-command).
 
5. Ellenőrizze, hogy Ön is olvasására vagy írására irányuló exportálási feladat közzéteendő meghajtókat.

### <a name="example-of-previewexport-command"></a>A parancs PreviewExport – példa

A következő példa bemutatja a `PreviewExport` parancsot:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Az exportálási bloblistafájl előfordulhat, hogy tartalmazza a blob nevét és a blob-előtagjait, ahogy az itt látható:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Az Azure Import/Export eszköz felsorolja az összes BLOB exportálható, bármilyen szükséges terhelést figyelembe véve, hogy azokat a megadott méretű meghajtókat, hogyan számítja ki, majd becslése a blobok, valamint a meghajtó használati adatok tárolásához szükséges meghajtók száma.  
  
Íme egy példa a kimenetre tájékoztató naplókkal nincs megadva:  
  
```  
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

## <a name="examples-of-valid-blob-paths"></a>Érvényes blob elérési útjának példái

Az alábbi táblázat érvényes blob elérési útjának példái láthatók:
   
   | Kiválasztó | BLOB elérési útja | Leírás |
   | --- | --- | --- |
   | Ezzel kezdődik |/ |Exportálja az összes BLOB storage-fiókban |
   | Ezzel kezdődik |/$root / |Exportálja a gyökérszintű tárolóban lévő összes BLOB |
   | Ezzel kezdődik |/Book |Minden olyan előtaggal kezdődő tárolóban lévő összes BLOB exportálja **könyv** |
   | Ezzel kezdődik |/Music/ |Exportálja a tárolóban lévő összes BLOB **zene** |
   | Ezzel kezdődik |/ zene/szeretetére |Exportálja a tárolóban lévő összes BLOB **zene** előtaggal kezdődik, amely **kedvelt** |
   | Egyenlő |$root/logo.bmp |Export blob **logo.bmp** a gyökérszintű tárolóban |
   | Egyenlő |Videos/Story.mp4 |Export blob **story.mp4** tárolóban **videók** |

## <a name="next-steps"></a>További lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelményeinek áttekintése](storage-import-export-requirements.md)


