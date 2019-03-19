---
title: REST használatával az Azure Media Services-tartalom közzététele
description: Ismerje meg, hogyan hozhat létre, amellyel streamelési lokátor. A kód a REST API-t használja.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: cfa3dd5c39626f209fdcba9df567d506cc768bec
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884018"
---
# <a name="publish-azure-media-services-content-using-rest"></a>REST használatával az Azure Media Services-tartalom közzététele 
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

Az adaptív sávszélességű MP4 típusú beállításkészlettel streamelési OnDemand-kereső létrehozásával, és a streamelési URL-cím létrehozásához streamelheti. A [adategység kódolása](media-services-rest-encode-asset.md) a cikk bemutatja, hogyan kódolandó egy adaptív sávszélességű MP4-beállítása. Objektumtovábbítási szabályzat konfigurálása, ha a tartalom titkosított, (leírtak szerint [ez](media-services-rest-configure-asset-delivery-policy.md) cikk) egy lokátor létrehozása előtt. 

OnDemand-lokátor segítségével hozhat létre MP4-fájlokat fokozatosan letölthető mutató URL-címeket is.  

Ez a cikk bemutatja, hogyan hozhat létre egy OnDemand-lokátor annak érdekében, hogy az objektum közzététele és a egy Smooth, MPEG DASH vagy HLS streamelési URL-címek hozhat létre. Azt is bemutatja, gyakori elérésű hozhat létre a progresszív letöltési URL-címeket.

A [következő](#types) szakasz bemutatja az enum típusok, amelynek az értékekkel a REST-hívásokat.   

> [!NOTE]
> A Media Services entitások elérésekor a be kell állítani a HTTP-kérelmekre a meghatározott fejlécmezők és értékek. További információkért lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-t kapcsolódás információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Miután sikeresen csatlakozott https://media.windows.net, kapni fog egy másik Media Services URI megadása 301 átirányítást. Meg kell győződnie, a későbbi hívások új URI-ra.

## <a name="create-an-ondemand-streaming-locator"></a>OnDemand-lokátor létrehozása
Az OnDemand-lokátor létrehozása és URL-címek lekérése, meg kell tegye a következőket:

1. Ha a tartalom titkosított, adja meg a hozzáférési házirend.
2. OnDemand-lokátor létrehozása.
3. Ha azt tervezi, adatfolyam, lekérése a streamelési jegyzékfájl (.ism) az eszközben. 
   
   Ha azt tervezi, fokozatosan letölteni, első MP4-fájlok az eszközben nevei. 
4. Hozhat létre az URL-címek az Alkalmazásjegyzék-fájl vagy MP4-fájlokat. 
5. Nem használ, amely tartalmazza az írási egy AccessPolicy a streamelési lokátorok létrehozásához vagy vonatkozó engedélyeinek törlése.

### <a name="create-an-access-policy"></a>Hozzáférési szabályzat létrehozása

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Az ugyanazon házirend-azonosító akkor használja, ha Ön mindig ugyanazokat a napokat / hozzáférési engedélyeket, például olyan továbbra is helyben hosszú ideje (nem feltöltött szabályzatokat) keresők szabályzatai. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

Kérés:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    Host: media.windows.net
    Content-Length: 68

    {"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

Válasz:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 311
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
    Server: Microsoft-IIS/8.5
    request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:52:09 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

### <a name="create-an-ondemand-streaming-locator"></a>OnDemand-lokátor létrehozása
A lokátor a megadott eszköz és az eszközintelligencia-szabályzat létrehozása.

Kérés:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    Host: media.windows.net
    Content-Length: 181

    {"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Válasz:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 637
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
    Server: Microsoft-IIS/8.5
    request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:58:37 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Build a streamelési URL-címek
Használja a **elérési út** értéket adott vissza a lokátor hozhat létre a Smooth, HLS és MPEG DASH URL-címek a létrehozása után. 

Smooth Streaming: **Elérési út** + jegyzékfájl neve + "/ jegyzékfájl"

példa:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Elérési út** + jegyzékfájl neve + "/ manifest(format=m3u8-aapl)"

példa:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Elérési út** + jegyzékfájl neve + "/ manifest(format=mpd-time-csf)"

példa:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>A progresszív letöltési URL-címek létrehozása
Használja a **elérési út** értéket adott vissza a progresszív letöltési URL-cím összeállítását a lokátor létrehozása után.   

URL-cím: **Elérési út** + eszköz fájlnév mp4

példa:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a id="types"></a>Enum típusok
    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[A Media Services REST API áttekintése](media-services-rest-how-to-use.md)

[Objektumtovábbítási szabályzat konfigurálása](media-services-rest-configure-asset-delivery-policy.md)

