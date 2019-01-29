---
title: Az Azure Import/Export exportálási feladat - v1 meghajtóhasználat |} A Microsoft Docs
description: Ismerje meg, hogyan nézhető meg a kiválasztott exportálási feladatokhoz az Azure Import/Export szolgáltatás blobok listáját.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 48c78f4c3332f28cc37aea0c663976ce7c2d489b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170946"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Meghajtóhasználat előnézete exportálási feladatokhoz
Exportálási feladat létrehozása, előtt kell válasszon olyan exportálandó blobok. A Microsoft Azure Import/Export szolgáltatás lehetővé teszi, hogy használja a blob elérési utak listája vagy a blob-címelőtagokat, amelyek a kiválasztott blobokat.  
  
Ezután meg kell határoznia, hány meghajtót kell küldenie. Az Import/Export eszköz biztosít a `PreviewExport` parancsot az előzetes verzióra meghajtóinak használatát választotta, a blobok a meghajtók méretének alapján fog használni.

## <a name="command-line-parameters"></a>Parancssori paraméterek

A következő paraméterek használata esetén használhatja a `PreviewExport` parancsot az Import/Export eszköz.

|Parancssori paraméter|Leírás|  
|--------------------------|-----------------|  
|**/ logdir:**< LogDirectory\>|Választható. A naplózási könyvtár. Ez a könyvtár részletes naplófájlok lesz írva. Ha nincs naplókönyvtár van megadva, a naplózási könyvtár az aktuális könyvtárban lesz.|  
|**/sn:**<StorageAccountName\>|Kötelező. A storage-fiók neve az exportálási feladatot.|  
|**/sk:**<StorageAccountKey\>|Kötelező, csak ha nincs megadva egy SAS-tárolót. A fiókkulcs a storage-fiókját az exportálási feladatot.|  
|**/csas:**<ContainerSas\>|Szükséges, csak ha nincs megadva a tárfiók kulcsára. A tároló SAS az exportálási feladatot exportálandó blobok listázásához.|  
|**/ExportBlobListFile:**<ExportBlobListFile\>|Kötelező. Az XML-fájl elérési útja blob elérési útjának listáját tartalmazó fájl, vagy a blob elérési útja előtagok exportálható a blobok számára. A használt fájlformátum a `BlobListBlobPath` eleme a [Put feladat](/rest/api/storageimportexport/jobs) az Import/Export szolgáltatás REST API-művelet.|  
|**/DriveSize:**<DriveSize\>|Kötelező. Exportálási feladatokhoz használható meghajtók méretének *például*, 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Parancssori példa

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
  
## <a name="next-steps"></a>További lépések

* [Az Azure Import/Export eszköz leírása](../storage-import-export-tool-how-to-v1.md)
