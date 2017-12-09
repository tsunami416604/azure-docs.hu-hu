---
title: "Szűrők létrehozása az Azure Media Services REST API-t |} Microsoft Docs"
description: "Ez a témakör ismerteti, az ügyfél használhassa őket adott szakaszaival adatfolyam adatfolyam-szűrők létrehozásához. A Media Services dinamikus jegyzékfájlokban eléréséhez a szelektív streaming hoz létre."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako;cenkdin
ms.openlocfilehash: 98df3b6592ed865fc0eb4b942d298b26e930365f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Szűrők létrehozásakor az Azure Media Services REST API-n
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

2.17 kiadástól kezdve a Media Services lehetővé teszi az eszközök szűrőit. Ezek a szűrők, amelyek lehetővé teszik a felhasználói számára többek között a megteheti a kiszolgálóoldali szabályok: lejátszás videó (és nem a teljes videó lejátszása) részt vagy a hang- és interpretációk, amelyet a felhasználói eszköz kezelni tud (ahelyett, hogy csak egy részét minden a interpretációk társított adategységet). Ez a szűrés a eszközök archivált keresztül **dinamikus Manifest**khoz, az ügyfél kérésre videó adatfolyam jönnek létre a megadott szűrő alapján.

Részletesebb szűrőket és dinamikus Manifest kapcsolatos adatokat, lásd: [dinamikus jelentkezik áttekintése](media-services-dynamic-manifest-overview.md).

Ez a cikk bemutatja, hogyan használható a REST API-k létrehozása, frissítése és törlése szűrők. 

## <a name="types-used-to-create-filters"></a>-Szűrők létrehozásához használt
A következő típusok használhatók szűrők létrehozásakor:  

* [Szűrő](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect és FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

>[!NOTE]

>A Media Services entitások elérésekor be kell meghatározott fejlécmezők és értékek a HTTP-kérelmekre. További információkért lásd: [a Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz kapcsolódáshoz információkért lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Szűrők létrehozása
### <a name="create-global-filters"></a>Globális szűrők létrehozása
Globális szűrőként létrehozásához használja a következő HTTP-kérelmek:  

#### <a name="http-request"></a>HTTP-kérelem
Kérelem fejlécei

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host:media.windows.net 

Kérelem törzse 

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
Egy helyi AssetFilter létrehozásához használja a következő HTTP-kérelmek:  

#### <a name="http-request"></a>HTTP-kérelem
Kérelem fejlécei

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net  

Kérelem törzse 

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
### <a name="get-all-global-filters-in-the-ams-account"></a>Minden globális beolvasása **szűrő**az AMS-fiók létrehozása
A szűrők listában használja a következő HTTP-kérelmek: 

#### <a name="http-request"></a>HTTP-kérelem
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.17 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Első **AssetFilter**egy eszközhöz társított s
#### <a name="http-request"></a>HTTP-kérelem
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Első egy **AssetFilter** az azonosítója alapján
#### <a name="http-request"></a>HTTP-kérelem
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Frissítés szűrők
Ezen javítás, PUT vagy egyesítési szűrő frissíthető új tulajdonság értékével.  További információ ezekről a műveletekről: [javítás, PUT, EGYESÍTSE](http://msdn.microsoft.com/library/dd541276.aspx).

Ha frissíti a szűrőt, akár két percet streamvégpont frissítéséhez a szabályok is igénybe vehet. Ha a tartalom állítása és kiszolgálása között szűrővel (és proxyk és a CDN a gyorsítótárba helyezett gyorsítótárak), player hibák frissítése a szűrő eredményezhet. A gyorsítótár kiürítése után a szűrő frissítése. Ha ezt a beállítást nem lehetséges, érdemes lehet egy másik szűrőt.  

### <a name="update-global-filters"></a>Globális szűrők frissítése
Globális szűrőként frissítéséhez használja a következő HTTP-kérelmek: 

#### <a name="http-request"></a>HTTP-kérelem
Kérelem fejlécei: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 
    Content-Length: 384

A kérelem törzse: 

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
A helyi szűrőt frissítéséhez használja a következő HTTP-kérelmek: 

#### <a name="http-request"></a>HTTP-kérelem
Kérelem fejlécei: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

A kérelem törzse: 

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
Globális szűrőként törléséhez használja a következő HTTP-kérelmek:

#### <a name="http-request"></a>HTTP-kérelem
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.17 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Helyi AssetFilters törlése
Egy helyi AssetFilter törléséhez használja a következő HTTP-kérelmek:

#### <a name="http-request"></a>HTTP-kérelem
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.17 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Build a streaming URL-szűrők használata
Információ közzétételére, és az eszközök: [ügyfelek áttekintés a tartalom továbbítása](media-services-deliver-content-overview.md).

A következő példák bemutatják, hogyan szűrők hozzáadása a streamelési URL-címeket.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP élő adatfolyam-továbbítási (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP élő adatfolyam-továbbítási (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Dinamikus jegyzékfájlokban áttekintése](media-services-dynamic-manifest-overview.md)

