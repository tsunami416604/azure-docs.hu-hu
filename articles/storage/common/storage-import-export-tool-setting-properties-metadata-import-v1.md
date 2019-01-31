---
title: Tulajdonságok és metaadatok használatával az Azure Import/Export - v1 beállítása |} A Microsoft Docs
description: Ismerje meg, hogyan adja meg a tulajdonságok és metaadatok állítható be a cél blobok az Azure Import/Export eszköz futtatásakor a meghajtók előkészítéséhez. Az Import/Export eszköz v1 utal.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 66ae7045cfb94ec70f3b14046af736f784b88ea6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471844"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Tulajdonságok és metaadatok beállítása az importálási folyamat során
A meghajtók előkészítése a Microsoft Azure Import/Export eszköz futtatásakor a tulajdonságok és metaadatok állítható be a cél blobokat is megadhat. Kövesse az alábbi lépéseket:  
  
1.  A blob tulajdonságainak beállításához hozzon létre egy szövegfájlt, a helyi számítógépen, amely meghatározza a nevét és értékeit.  
  
2.  Állítsa be a blob metaadatai, hozzon létre egy szövegfájlt a helyi számítógépen, amely meghatározza a metaadatok neveit és értékeit.  
  
3.  A teljes elérési útja át egyik vagy mindkét ezeket a fájlokat az Azure Import/Export eszköz részeként a `PrepImport` műveletet.  
  
> [!NOTE]
>  Ha a tulajdonságok és metaadatok fájlokat egy másolási munkamenet részeként adja meg, ezeket a tulajdonságokat vagy metaadatok vannak beállítva a minden egyes blob másolási munkamenet részeként importált. Adjon meg különböző tulajdonságok és metaadatok egyes importált blobok szeretne, ha szüksége különböző tulajdonságok vagy metaadat-fájlokat egy külön példányt munkamenet létrehozásához.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>A fájlt adja meg a Blob tulajdonságai  
Adja meg a blob tulajdonságai, hozzon létre egy helyi szövegfájlt, és tartalmazza az XML-t a tulajdonságnevek elemeket, és tulajdonságértékeket értékekként határozza meg. Íme egy példa, amely meghatározza az egyes tulajdonságértékeket:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Mentse a fájlt egy helyi helyre, például `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Adja meg a Blob metaadatainak egy szövegfájlba  
Hasonlóképpen adja meg a blob metaadatai, hozzon létre a helyi szövegfájl, amely meghatározza a metaadatneveknek egyedieknek elemek, valamint metaadatértékeket értékekként. Íme egy példa, amely meghatározza az egyes metaadatértékeket:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Mentse a fájlt egy helyi helyre, például `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>A tulajdonságok és metaadatok fájlokat is beleértve másolási munkamenet létrehozása  
Az Azure Import/Export eszköz előkészítése az importálási feladat futtatásakor adja meg a parancssor használatával tulajdonságok fájlt a `PropertyFile` paraméter. A metaadat-fájlt a parancssor használatával adja meg a `/MetadataFile` paraméter. Íme egy példa, amely mindkét fájlokat:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás metaadat- és tulajdonságfájljainak formátuma](../storage-import-export-file-format-metadata-and-properties.md)
