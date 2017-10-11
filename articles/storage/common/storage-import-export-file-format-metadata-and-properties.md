---
title: "Az Azure Import/Export metaadatok és a Tulajdonságok fájlformátum |} Microsoft Docs"
description: "Megtudhatja, hogyan adhatja meg a metaadatok és egy vagy több blobot, az importálás során vagy exportálja a feladat tulajdonságait."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3f728ad94cdcbd32092b677f11a737ae91376720
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Az Azure Import/Export szolgáltatás metaadatok és a Tulajdonságok fájlformátum
Megadhat egy vagy több blobot tulajdonságai és metaadatok egy importálási feladat vagy exportálási feladat részeként. Metaadatok és az importálási feladat részeként létrehozott BLOB tulajdonságainak beállításához adja meg az importálandó adatokat tartalmazó merevlemez-meghajtóról metaadatok vagy tulajdonságok fájlba. Exportálási feladat metaadatok és a Tulajdonságok írja metaadatok vagy tulajdonságok fájlba, amely tartalmazza a visszaküldött merevlemez-meghajtóra.  
  
## <a name="metadata-file-format"></a>Metaadatok fájlformátum  
A metaadatfájl formátuma a következő:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML-elem.|Típus|Leírás|  
|-----------------|----------|-----------------|  
|`Metadata`|Legfelső szintű elem|A metaadat-fájl gyökérelemének.|  
|`metadata-name`|Karakterlánc|Választható. Az XML-elem a BLOB metaadatait nevét adja meg, és az értékét a metadata beállítás értékét adja meg.|  
  
## <a name="properties-file-format"></a>Tulajdonságok fájlformátum  
A tulajdonságok fájl formátuma a következő:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML-elem.|Típus|Leírás|  
|-----------------|----------|-----------------|  
|`Properties`|Legfelső szintű elem|A tulajdonságok fájl gyökérelemének.|  
|`Last-Modified`|Karakterlánc|Választható. Az utolsó módosítás dátuma a BLOB. Az exportálási feladatok csak.|  
|`Etag`|Karakterlánc|Választható. A blob ETag érték. Az exportálási feladatok csak.|  
|`Content-Length`|Karakterlánc|Választható. A blob bájtban kifejezett mérete. Az exportálási feladatok csak.|  
|`Content-Type`|Karakterlánc|Választható. A blob tartalomtípusa.|  
|`Content-MD5`|Karakterlánc|Választható. A blob MD5 kivonatoló.|  
|`Content-Encoding`|Karakterlánc|Választható. A blob tartalma kódolást.|  
|`Content-Language`|Karakterlánc|Választható. A blob tartalom kívánt nyelvét.|  
|`Cache-Control`|Karakterlánc|Választható. A blob vezérlő gyorsítótár-karakterlánc.|  

## <a name="next-steps"></a>Következő lépések

Lásd: [blob tulajdonságainak beállítása](/rest/api/storageservices/set-blob-properties), [beállítása Blob metaadatai](/rest/api/storageservices/set-blob-metadata), és [beállítás és lekérése közben tulajdonságait és a metaadatok blob-erőforrások](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) a blob metaadatai és tulajdonságok beállításával kapcsolatos részletes szabályok.
