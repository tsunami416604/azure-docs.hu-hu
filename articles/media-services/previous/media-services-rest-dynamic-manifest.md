---
title: Szűrők létrehozása az Azure Media Services REST API-val |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, így az ügyfél használhatja őket stream konkrét szakaszokra datového proudu. A Media Services dinamikus jegyzékek érdekében, ami a szelektív hoz létre.
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
ms.author: juliako;cenkdin
ms.openlocfilehash: fc72bad81896d786bfe040632b7f6a61f775b6f1
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294745"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Szűrők létrehozása az Azure Media Services REST API-val 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

2.17 kiadástól kezdve, a Media Services lehetővé teszi az eszközök szűrőket határozhat meg. Ezeket a szűrőket, amelyek lehetővé teszik az ügyfelek úgy dönteni, hogy többek között a kiszolgálóoldali szabályok: lejátszási csak egy részét (helyett egész videó lejátszásának), videó vagy hang- és verzió, amely a felhasználói eszköz képes kezelni (nem pedig csak egy részhalmazát adja meg az összes a beállításkészletben az eszközhöz társított). Ez a szűrés a eszközök archivált keresztül **dinamikus Manifest**, amelyek létrejönnek a videó továbbításához a felhasználói kérésre megadott szűrő(k) alapján.

Részletesebb szűrők és dinamikus Manifest kapcsolatos információkért lásd: [dinamikus jegyzékfájlok áttekintése](media-services-dynamic-manifest-overview.md).

Ez a cikk bemutatja, hogyan lehet REST API-k segítségével létrehozása, frissítése és törlése a szűrőket. 

## <a name="types-used-to-create-filters"></a>Szűrők létrehozásához használt típusok
A következő típusok használhatók a szűrők létrehozásakor:  

* [Szűrő](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect és FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> A Media Services entitások elérésekor a be kell állítani a HTTP-kérelmekre a meghatározott fejlécmezők és értékek. További információkért lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-t kapcsolódás információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Szűrők létrehozása
### <a name="create-global-filters"></a>Hozzon létre globális szűrők
Globális szűrő létrehozásához használja a következő HTTP-kérelmeket:  

#### <a name="http-request"></a>HTTP-kérelem
Kérelem fejlécei

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
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

### <a name="create-local-assetfilters"></a>Helyi AssetFilters létrehozása
Hozzon létre egy helyi AssetFilter, használja a következő HTTP-kérelmeket:  

#### <a name="http-request"></a>HTTP-kérelem
Kérelem fejlécei

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
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

## <a name="list-filters"></a>Szűrők megjelenítése
### <a name="get-all-global-filters-in-the-ams-account"></a>Első minden globális **szűrő**lévő az AMS-fiók
Szűrők listája, használja a következő HTTP-kérelmek: 

#### <a name="http-request"></a>HTTP-kérelem
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Első **AssetFilter**az eszközhöz társított s
#### <a name="http-request"></a>HTTP-kérelem
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Get- **AssetFilter** az azonosítója alapján
#### <a name="http-request"></a>HTTP-kérelem
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Frissítse a szűrőket
Használja a javítás, PUT vagy egyesítési szűrő frissítése az új tulajdonságértékeket.  Ezek a műveletek kapcsolatos további információkért lásd: [PUT PATCH, EGYESÍTÉSE](https://msdn.microsoft.com/library/dd541276.aspx).

Frissíti egy szűrőt, ha streamvégponton szabályok frissítése akár két percet is igénybe vehet. Ha a tartalom kiszolgálása szűrővel (és a proxyk és a CDN gyorsítótárazza a gyorsítótárak), player hibák frissítése ezzel a szűrővel eredményezhet. A gyorsítótár törléséhez a szűrő frissítése után. Ha ezt a beállítást nem lehetséges, fontolja meg egy másik szűrővel.  

### <a name="update-global-filters"></a>Frissítse a globális szűrőket
Globális szűrő frissítéséhez használja a következő HTTP-kérelmeket: 

#### <a name="http-request"></a>HTTP-kérelem
Kérelem fejlécei: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 
    Content-Length: 384

Kérelem törzse: 

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

### <a name="update-local-assetfilters"></a>Helyi AssetFilters frissítése
A helyi szűrőt frissítéséhez használja a következő HTTP-kérelmeket: 

#### <a name="http-request"></a>HTTP-kérelem
Kérelem fejlécei: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

Kérelem törzse: 

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

#### <a name="http-request"></a>HTTP-kérelem
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.17 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Helyi AssetFilters törlése
Egy helyi AssetFilter törléséhez használja a következő HTTP-kérelmeket:

#### <a name="http-request"></a>HTTP-kérelem
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Build a streaming URL-címeket, a szűrők használata
Hogyan tehet közzé, és az eszközök a további információkért lásd: [tartalom továbbítása az ügyfelek áttekintése](media-services-deliver-content-overview.md).

Az alábbi példák bemutatják, hogyan szűrők felvétele a streamelési URL-címeket.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[A dinamikus jegyzékek áttekintése](media-services-dynamic-manifest-overview.md)

