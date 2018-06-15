---
title: Tulajdonságok és a metaadatok Azure Import/Export - 1-es verzió beállítása |} Microsoft Docs
description: Megtudhatja, hogyan adhatja meg az tulajdonságait és a metaadatok állítható be a cél BLOB az Azure Import/Export eszköz futtatásakor a meghajtók előkészítéséhez. A v1 az Import/Export eszköz vonatkozik.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873736"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Tulajdonságok és metaadatok beállítása az importálási folyamat során
A meghajtók előkészítése a Microsoft Azure Import/Export eszköz futtatásakor tulajdonságok és állítható be a cél BLOB metaadatait is megadhat. Kövesse az alábbi lépéseket:  
  
1.  Blob tulajdonságainak beállításához hozzon létre egy szövegfájlt, a helyi számítógépen, amely meghatározza a nevét és értékeit.  
  
2.  Állítsa be a blob metaadatai, hozzon létre egy szövegfájlt, a helyi számítógépen, amely meghatározza a metaadatok neveit és értékeit.  
  
3.  A teljes elérési útja át egyik vagy mindkét ezeket a fájlokat az Azure Import/Export eszköz részeként a `PrepImport` műveletet.  
  
> [!NOTE]
>  Amikor egy tulajdonságok vagy metaadat-fájlt ad meg egy másolási munkamenet részeként, ezeket a tulajdonságokat vagy metaadatok vannak állítva minden BLOB másolási munkamenet részeként importált. Adjon meg másik tulajdonságok vagy metaadatok egyes importált blobok szeretnénk, ha szüksége hozzon létre egy külön példányát munkamenetet más tulajdonságokkal vagy metaadat-fájlok.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Adja meg a Blob tulajdonságai szövegfájl  
Adja meg a blob tulajdonságai, hozzon létre egy helyi szövegfájlt, és tartalmazza, amely meghatározza a tulajdonság nevét, és tulajdonságértékeket értékként XML. Íme egy példa, amely meghatározza az egyes tulajdonságértékeket:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Mentse a fájlt egy helyi helyre, például a `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Adja meg a Blob metaadatai szövegfájl  
Hasonlóképpen adja meg a blob metaadatai, hozzon létre a helyi szövegfájl, amely meghatározza a metaadatok nevét, és metaadatok értékek értékként. Íme egy példa, amely néhány metaadatok értékeket adja meg:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Mentse a fájlt egy helyi helyre, például a `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Beleértve a Tulajdonságok vagy metaadat-fájlok másolása munkamenet létrehozása  
Az Azure Import/Export eszköz előkészítése az importálási feladat futtatásakor adja meg a parancssor használatával tulajdonságok fájlt a `PropertyFile` paraméter. Adja meg a metaadat-fájlt a parancssor használatával a `/MetadataFile` paraméter. Íme egy példa, amely mindkét fájlokat:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Következő lépések

* [Az Import/Export szolgáltatás metaadat- és tulajdonságfájljainak formátuma](../storage-import-export-file-format-metadata-and-properties.md)
