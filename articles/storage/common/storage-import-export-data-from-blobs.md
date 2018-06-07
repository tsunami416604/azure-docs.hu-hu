---
title: Azure Import/Export segítségével exportál adatokat az Azure BLOB |} Microsoft Docs
description: Ismerje meg az exportálási feladat létrehozása az adatok átvitele Azure BLOB az Azure portálon.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: eb41708c7446b3139758678c9247ffbb11da8b40
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660871"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Az Azure Import/Export szolgáltatás segítségével exportál adatokat az Azure Blob-tároló
Ez a cikk részletes útmutatást nyújt az Azure Import/Export szolgáltatás használatával biztonságosan nagy mennyiségű adat exportálása az Azure Blob Storage tárolóban. A szolgáltatás megköveteli, hogy küldje el az Azure adatközpontba üres meghajtókon. A szolgáltatás adatokat exportál a tárfiók a meghajtók, és ezután érhető el a meghajtók vissza.

## <a name="prerequisites"></a>Előfeltételek

Azure Blob Storage adatokat továbbít exportálási feladat létrehozása előtt gondosan tekintse át, és végezze el az alábbi listán szereplő Előfeltételek ezt a szolgáltatást. A következők szükségesek:

- Az Azure előfizetéssel rendelkezik, amely az Import/Export szolgáltatás használható.
- Rendelkeznie kell legalább egy Azure Storage-fiók. A lista [storage-fiókok és a tárolási típusok támogatott Import/Export szolgáltatás](storage-import-export-requirements.md). Új tárfiók létrehozásával kapcsolatos további információkért lásd: [a Storage-fiók létrehozása](storage-create-storage-account.md#create-a-storage-account).
- A lemezek elegendő számú [támogatott típusok](storage-import-export-requirements.md#supported-disks).

## <a name="step-1-create-an-export-job"></a>1. lépés: Exportálási feladat létrehozása

A következő lépésekkel exportálási feladat létrehozása az Azure portálon.

1. Jelentkezzen be https://portal.azure.com/.
2. Ugrás a **minden szolgáltatás > tárolás > importálási/exportálási feladatok**. 

    ![Ugrás az importálási/exportálási feladatok](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Kattintson a **létrehozás importálási/exportálási feladatok**.

    ![Kattintson az importálási/exportálási feladatok](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. A **alapjai**:
    
    - Válassza ki **Azure exportálása**. 
    - Adjon meg egy leíró nevet az exportálási feladat. Úgy dönt, hogy a folyamat előrehaladását a feladatok a nevet használja. 
        - A név csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket tartalmazhat.
        - A nevének betűvel kell kezdődnie, és nem tartalmazhat szóközt. 
    - Válasszon egy előfizetést.
    - Adja meg, vagy válasszon egy erőforráscsoportot.

        ![Alapvető beállítások](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. A **feladat részletei**:

    - Válassza ki a tárfiókot, ahol az exportálandó adatokat található. 
    - A gyűjtőtár helyre automatikusan a kiválasztott tárfiók régió alapján van feltöltve. 
    - Adja meg a blobadatokat importáljon a tárfiókba exportálja az üres meghajtó vagy a meghajtók kíván. 
    - Válassza ki a **minden** blob a tárfiókban lévő adatokat.
    
         ![Minden](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Megadhatja, mely tárolók és blobok exportálása.
        - **Adhatja meg az exportálandó blob**: használja a **Equal To** választó. Adja meg a relatív elérési útját a blob, a tároló neve kezdve. Használjon *$root* adhatja meg a gyökérszintű tárolóban.
        - **Adja meg az összes BLOB előtagja kezdve a**: használja a **kezdődik** választó. Adja meg az előtagot, kezdve a perjel '/'. Az előtag lehet, hogy az előtag a tároló neve, a teljes tároló neve vagy a teljes tároló neve követi a blob nevének előtagja. Ezen a képernyőfelvételen látható módon meg kell adnia a blob elérési utak a feldolgozás során hibák elkerülése érdekében formátuma érvénytelen. További információkért lásd: [érvényes blob elérési utak példái](#examples-of-valid-blob-paths). 
   
           ![Kijelölt elemek exportálása tárolók és blobok](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - A blob listáját tartalmazó fájl segítségével exportálhatja.

        ![A blob listáját tartalmazó fájl exportálása](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Ha a blob exportálható adatmásolás során van használatban, Azure Import/Export szolgáltatás pillanatképet készít a blob, és másolja át a pillanatkép.
 

4. A **szállítási információ vissza**:

    - Válassza ki a szolgáltatói a legördülő listából.
    - Adjon meg egy érvényes vivőjel-szám, amely adott szolgáltatónként hozott létre. Microsoft küldje el azt a meghajtókat, az importálási feladat befejezése után ezt a fiókot használja. 
    - Adjon teljes és érvényes kapcsolattartójának a neve, telefon, e-mail, utca, házszám, város, zip, az állam/megye ország vagy régió.
   
5. A **összegzés**:

    - Tekintse át a feladat.
    - Megjegyzés: a feladat nevét és a megadott Azure-adatközpontban szállítási címet szállítási lemezt az Azure-bA. 
    - Kattintson a **OK** exportálási feladat létrehozásának befejezéséhez.

## <a name="step-2-ship-the-drives"></a>2. lépés: Küldje el a meghajtók

Ha nem tudja, hány meghajtót van szüksége, lépjen a [ellenőrizze, hány meghajtót](#check-the-number-of-drives). Ha tudja, hány meghajtót, folytassa a meghajtók szolgáltatástól.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3. lépés: A feladat frissíti a nyomkövetési adatokat

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>4. lépés: A lemezek fogadása
Amikor az irányítópulton jelzi a folyamat befejeződik, a lemez szállítanak, és a nyomon követési számot a szállítási érhető el a portálon.

1. Miután megkapta a meghajtók exportált adatok, a meghajtó feloldásához a BitLocker-kulcsok beszerzése kell. Ugrás az exportálási feladat az Azure portálon. Kattintson a **Import/Export** fülre. 
2. Válassza ki, és kattintson az exportálás sikertelen feladat a listából. Ugrás a **BitLocker-kulcsok** , és másolja a kulcsokat.
   
   ![BitLocker-kulcsok exportálási feladat megtekintése](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. A BitLocker-kulcsok használata a lemezek feloldásához.

Az Exportálás befejeződik. Most törölheti a feladat, vagy azt automatikusan lekérdezi 90 nap után törlődnek.


## <a name="check-the-number-of-drives"></a>Ellenőrizze a meghajtók száma

Ez *választható* lépés segítséget nyújt az exportálási feladat szükséges meghajtókat számát határozza meg. Hajtsa végre ezt a lépést a Windows operációs rendszert futtató egy [támogatott operációsrendszer-verzió](storage-import-export-requirements.md#supported-operating-systems).

1. [Töltse le a WAImportExport 1-es verziójú](https://www.microsoft.com/en-us/download/details.aspx?id=42659) a Windows rendszeren. 
2. Bontsa ki az alapértelmezett mappába `waimportexportv1`. Például: `C:\WaImportExportV1`.
3. Nyissa meg a PowerShell vagy a parancssori ablakot rendszergazdai jogosultságokkal. A tömörítetlen mappába könyvtár módosításához futtassa a következő parancsot:
    
    `cd C:\WaImportExportV1`

4. A kijelölt blobok szükséges lemezek számát ellenőrzéséhez futtassa a következő parancsot:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    A paraméterek a következő táblázat ismerteti:
    
    |Parancssori paraméter|Leírás|  
    |--------------------------|-----------------|  
    |**/ logdir:**|Választható. A naplózási könyvtár. Ez a könyvtár részletes naplófájlok kerülnek. Ha nincs megadva, a naplózási könyvtár lesz az aktuális könyvtár.|  
    |**/sn:**|Kötelező. Az exportálási feladat a tárfiók nevét.|  
    |**/SK:**|Akkor kötelező, ha a tároló SAS nincs megadva. A fiók az exportálási feladat a tárfiók kulcsa.|  
    |**/csas:**|Akkor kötelező, ha a tárfiók kulcsa nincs megadva. A tároló SAS felsoroló a blobok az exportálási feladat exportálni.|  
    |**/ ExportBlobListFile:**|Kötelező. Az XML-fájl elérési útja blob elérési utak listája tartalmazó fájlt, vagy a blob-elérési út-előtagokat a blobok exportálható. A használt fájlformátum a `BlobListBlobPath` eleme a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) művelet az Import/Export szolgáltatás REST API-t.|  
    |**/ DriveSize:**|Kötelező. Az exportálási feladat, használható meghajtók méretének *pl.*, 500 GB, 1,5 TB.|  

    Lásd: egy [a PreviewExport parancs például](#example-of-previewexport-command).
 
5. Ellenőrizze, hogy akkor is olvasására vagy írására a Exportálás feladathoz szállítják meghajtókat.

### <a name="example-of-previewexport-command"></a>PreviewExport parancs – példa

A következő példa bemutatja a `PreviewExport` parancs:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
A blob lista exportfájl blob nevének és tartalmazhat blob-előtagok, ahogy az itt látható:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Az Azure Import/Export eszköz felsorolja az összes BLOB exportálható figyelembe véve a szükséges terhelés csomag azokat a megadott méretű meghajtókat hogyan számítja ki, majd a blobok és a meghajtó használati információk tárolásához szükséges meghajtók száma becslése.  
  
Íme egy példa a kimenetet, és nincs megadva tájékoztató naplófájlokat:  
  
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

## <a name="examples-of-valid-blob-paths"></a>Példa érvényes blob elérési utak

Az alábbi táblázat példákat érvényes blob elérési utak közül:
   
   | Kiválasztó | BLOB elérési út | Leírás |
   | --- | --- | --- |
   | Kezdődik |/ |Exportálja a tárfiókban lévő összes BLOB |
   | Kezdődik |/$root / |A gyökérszintű tárolóban lévő összes blobok exportálása |
   | Kezdődik |/Book |Exportálja az összes BLOB a tárolóban előtaggal kezdődő **könyv** |
   | Kezdődik |/Music/ |Exportálja a tárolóban lévő blobok összes **zene** |
   | Kezdődik |/ zene/szerelem |Exportálja a tárolóban lévő összes blobok **zene** előtaggal rendelkező karaktersorral **kedvelt** |
   | Egyenlő |$root/logo.bmp |Exportálja a blob **logo.bmp** a gyökérszintű tárolóban |
   | Egyenlő |Videos/Story.mp4 |Exportálja a blob **story.mp4** tárolóban **videók** |

## <a name="next-steps"></a>További lépések

* [A feladat és a meghajtó állapotának megtekintése](storage-import-export-view-drive-status.md)
* [Importálási/exportálási követelményeinek áttekintése](storage-import-export-requirements.md)


