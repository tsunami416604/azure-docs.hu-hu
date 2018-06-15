---
title: Előnézet-meghajtó használata az Azure Import/Export exportálási feladat - 1-es verzió |} Microsoft Docs
description: Útmutató az Azure Import/Export szolgáltatás exportálási feladat kiválasztott BLOB listáját.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 6ec74ae0b0931f3fed99a43f4f7e58f9d425b138
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873645"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Meghajtóhasználat előnézete exportálási feladatokhoz
Exportálási feladat létrehozása előtt válasszon olyan blobok exportálni szeretné. A Microsoft Azure Import/Export szolgáltatás lehetővé teszi egy blob elérési utak listája használja, vagy a blob-előtagok a választott blobok képviseli.  
  
Ezután meg kell határoznia, hány meghajtót kell elküldenie. Az Import/Export eszköz biztosít a `PreviewExport` parancs előzetes meghajtóinak használatát, a blobok választotta, a meghajtók mérete alapú használni fog.

## <a name="command-line-parameters"></a>Parancssori paraméterek

A következő paraméterek használata esetén használhatja a `PreviewExport` az Import/Export eszköz parancsot.

|Parancssori paraméter|Leírás|  
|--------------------------|-----------------|  
|**/ logdir:**< LogDirectory\>|Választható. A naplózási könyvtár. Ez a könyvtár részletes naplófájlok fog szerepelni. Ha nincs naplókönyvtár meg van adva, a naplózási könyvtár az aktuális könyvtár lesz.|  
|**/sn:**< StorageAccountName\>|Kötelező. Az exportálási feladat a tárfiók nevét.|  
|**/SK:**< StorageAccountKey\>|Csak ha nincs megadva a tároló SAS szükséges. A fiók az exportálási feladat a tárfiók kulcsa.|  
|**/csas:**< ContainerSas\>|Szükséges, csak ha nincs megadva a tárfiók kulcsára. A tároló SAS felsoroló a blobok az exportálási feladat exportálni.|  
|**/ ExportBlobListFile:**< ExportBlobListFile\>|Kötelező. Az XML-fájl elérési útja blob elérési utak listája tartalmazó fájlt, vagy a blob-elérési út-előtagokat a blobok exportálható. A használt fájlformátum a `BlobListBlobPath` eleme a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) művelet az Import/Export szolgáltatás REST API-t.|  
|**/ DriveSize:**< DriveSize\>|Kötelező. Az exportálási feladat, használható meghajtók méretének *pl.*, 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Parancssori – példa

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
  
## <a name="next-steps"></a>Következő lépések

* [Az Azure Import/Export eszköz referencia](../storage-import-export-tool-how-to-v1.md)
