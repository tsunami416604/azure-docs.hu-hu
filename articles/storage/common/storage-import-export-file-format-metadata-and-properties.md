---
title: Az Azure Import/Export metaadat- és tulajdonságfájljainak formátuma |} A Microsoft Docs
description: Ismerje meg, hogyan adja meg a metaadatok és a egy vagy több blobot, amelyek részei az importálási vagy exportálási feladatot tulajdonságait.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 5a886244b43ad006a95e9be0350d9c69fd987ad9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526232"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Az Azure Import/Export szolgáltatás metaadat- és tulajdonságfájljainak formátuma
Metaadatok és a egy vagy több blobot tulajdonságait is adja meg az importálási feladat vagy exportálási feladat részeként. Metaadatok és az importálási feladat részeként létrehozott blobok tulajdonságainak beállításához adja meg a metaadatok vagy tulajdonságok fájlt importálni kívánt adatokat tartalmazó merevlemez. Exportálási feladatokhoz metaadat- és tulajdonságfájljainak írt metaadatok vagy tulajdonságok fájlba, amely tartalmazza a merevlemezen, vissza.  
  
## <a name="metadata-file-format"></a>Metaadatait tartalmazó fájl formátuma  
A metaadatfájl formátuma a következő:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML-elem|Típus|Leírás|  
|-----------------|----------|-----------------|  
|`Metadata`|Legfelső szintű elem|A metaadatfájl gyökérelem.|  
|`metadata-name`|Sztring|Választható. Az XML-elem metaadatait a BLOB nevét adja meg, és az érték határozza meg, a metaadatok beállítás értékét.|  
  
## <a name="properties-file-format"></a>Tulajdonságok fájlformátum  
Egy tulajdonságfájljainak formátuma a következő:  
  
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
  
|XML-elem|Típus|Leírás|  
|-----------------|----------|-----------------|  
|`Properties`|Legfelső szintű elem|A tulajdonságok fájl gyökérelem.|  
|`Last-Modified`|Sztring|Választható. Utolsó módosítás időpontja a BLOB. Csak export-feladatok.|  
|`Etag`|Sztring|Választható. A blob ETag-érték. Csak export-feladatok.|  
|`Content-Length`|Sztring|Választható. A blob (bájt) mérete. Csak export-feladatok.|  
|`Content-Type`|Sztring|Választható. A blob tartalmának típusa.|  
|`Content-MD5`|Sztring|Választható. A blob MD5-kivonat.|  
|`Content-Encoding`|Sztring|Választható. A blob tartalma kódolást.|  
|`Content-Language`|Sztring|Választható. A blob tartalom nyelve.|  
|`Cache-Control`|Sztring|Választható. A blob vezérlő cache-karakterlánc.|  

## <a name="next-steps"></a>További lépések

Lásd: [blob tulajdonságainak megadása](/rest/api/storageservices/set-blob-properties), [állítsa Blob metaadatainak](/rest/api/storageservices/set-blob-metadata), és [beállítása és lekérése során tulajdonságok és metaadatok a blob-erőforrások](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) beállítás blob metaadatainak kapcsolatos részletes szabályok és tulajdonságait.
