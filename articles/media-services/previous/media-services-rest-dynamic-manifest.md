---
title: Szűrők létrehozása az Azure Media Services REST API-val | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogyan hozhat létre szűrőket, hogy az ügyfél használhassa őket az adatfolyam adott szakaszainak streameléséhez. A Media Services dinamikus jegyzékeket hoz létre a szelektív streamelés eléréséhez.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewr: cenkdin
ms.openlocfilehash: b778ad8c59cf51f92584cd3590f7d99244f37b2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774961"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Szűrők létrehozása az Azure Media Services REST API-jával 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [Többi](media-services-rest-dynamic-manifest.md)
> 
> 

A 2.17-es kiadástól kezdve a Media Services lehetővé teszi, hogy szűrőket határozzon meg az eszközökhöz. Ezek a szűrők olyan kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek számára, hogy olyan műveleteket válasszanak, mint például: a videónak csak egy részét játssza le (a teljes videó lejátszása helyett), vagy csak a hang- és videointerpretációk egy részét adja meg, amelyet az ügyfél eszköze kezelni tud (ahelyett, hogy az eszközhöz társított összes interpretáció). Az eszközök szűrése archiválva van a **dinamikus jegyzékeken**keresztül, amelyek az ügyfél kérésére jönnek létre a videó adott szűrő(k) alapján történő streamelésére.

A szűrőkkel és a dinamikus jegyzékfájlokkal kapcsolatos további információkért lásd: [Dinamikus jegyzékek – áttekintés.](media-services-dynamic-manifest-overview.md)

Ez a cikk bemutatja, hogyan hozhat létre, frissítheti és törölheti a SZŰRŐKET A REST API-k használatával. 

## <a name="types-used-to-create-filters"></a>Szűrők létrehozásához használt típusok
A szűrők létrehozásakor a következő típusok at használjuk:  

* [Szűrő](https://docs.microsoft.com/rest/api/media/operations/filter)
* [Eszközszűrő](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect és FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Amikor a Media Services entitásait éri el, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz való csatlakozásról az [Access the Azure Media Services API azure AD-hitelesítéssel című témakörben](media-services-use-aad-auth-to-access-ams-api.md)talál további információt. 

## <a name="create-filters"></a>Szűrők létrehozása
### <a name="create-global-filters"></a>Globális szűrők létrehozása
Globális szűrő létrehozásához használja a következő HTTP-kérelmeket:  

#### <a name="http-request"></a>HTTP-kérés
Kérelemfejlécek

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host:media.windows.net 

A kérés törzse 

    {  
       "Name":"GlobalFilter",
       "PresentationTimeRange":{  
          "StartTimestamp":"0",
          "EndTimestamp":"9223372036854775807",
          "PresentationWindowDuration":"12000000000",
          "LiveBackoffDuration":"0",
          "Timescale":"10000000"
       },
       "Tracks":[  
          {  
             "PropertyConditions":
                  [  
                {  
                   "Property":"Type",
                   "Value":"audio",
                   "Operator":"Equal"
                },
                {  
                   "Property":"Bitrate",
                   "Value":"0-2147483647",
                   "Operator":"Equal"
                }
             ]
          }
       ]
    }




#### <a name="http-response"></a>HTTP-válasz
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>Helyi eszközszűrők létrehozása
Helyi AssetFilter létrehozásához használja a következő HTTP-kérelmeket:  

#### <a name="http-request"></a>HTTP-kérés
Kérelemfejlécek

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net  

A kérés törzse 

    {   
       "Name":"AssetFilter", 
       "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
       "PresentationTimeRange":{   
          "StartTimestamp":"0", 
          "EndTimestamp":"9223372036854775807", 
          "PresentationWindowDuration":"12000000000", 
          "LiveBackoffDuration":"0", 
          "Timescale":"10000000" 
       }, 
       "Tracks":[   
          {   
             "PropertyConditions": 
                  [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

#### <a name="http-response"></a>HTTP-válasz
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>Listaszűrők
### <a name="get-all-global-filters-in-the-ams-account"></a>Az összes globális **szűrő beszereznie**az AMS-fiókban
A szűrők listázásához használja a következő HTTP-kérelmeket: 

#### <a name="http-request"></a>HTTP-kérés
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Eszközhöz társított **AssetFilter-ek**beszerzése
#### <a name="http-request"></a>HTTP-kérés
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>AssetFilter **AssetFilter** beszerzése az azonosítója alapján
#### <a name="http-request"></a>HTTP-kérés
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Szűrők frissítése
A PATCH, PUT vagy MERGE használatával frissítheti a szűrőt új tulajdonságértékekkel.  Ezekről a műveletekről további információt a [PATCH, PUT, MERGE című](https://msdn.microsoft.com/library/dd541276.aspx)témakörben talál.

Ha frissíti a szűrőt, akár két percet is igénybe vehet, amíg a streamelési végpont frissíti a szabályokat. Ha a tartalmat ezzel a szűrővel szolgálták ki (proxykban és CDN-gyorsítótárakban gyorsítótárazva), a szűrő frissítése a lejátszó meghibásodását eredményezheti. A szűrő frissítése után törölje a gyorsítótárat. Ha ez a beállítás nem lehetséges, fontolja meg egy másik szűrő használatát.  

### <a name="update-global-filters"></a>Globális szűrők frissítése
Globális szűrő frissítéséhez használja a következő HTTP-kérelmeket: 

#### <a name="http-request"></a>HTTP-kérés
Fejlécek kérése: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 
    Content-Length: 384

Kérés törzse: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

### <a name="update-local-assetfilters"></a>Helyi eszközszűrők frissítése
Helyi szűrő frissítéséhez használja a következő HTTP-kérelmeket: 

#### <a name="http-request"></a>HTTP-kérés
Fejlécek kérése: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

Kérés törzse: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 


## <a name="delete-filters"></a>Szűrők törlése
### <a name="delete-global-filters"></a>Globális szűrők törlése
Globális szűrő törléséhez használja a következő HTTP-kérelmeket:

#### <a name="http-request"></a>HTTP-kérés
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.19 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Helyi eszközszűrők törlése
Helyi Eszközszűrő törléséhez használja a következő HTTP-kérelmeket:

#### <a name="http-request"></a>HTTP-kérés
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Szűrőket használó streamelési URL-ek létrehozása
Az eszközök közzétételéről és kézbesítésének módjáról a [Tartalom kézbesítése az ügyfeleknek – áttekintés című témakörben olvashat.](media-services-deliver-content-overview.md)

Az alábbi példák bemutatják, hogyan adhat hozzá szűrőket a streamelési URL-címekhez.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Dinamikus jegyzékek – áttekintés](media-services-dynamic-manifest-overview.md)

