---
title: Használja a többi Azure Media Services tartalom közzététele
description: Megtudhatja, hogyan hozhat létre egy keresőt a streamelési URL-cím létrehozásához használt. A kód REST API-t használja.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: 8385dedd494c0cef968cb869ded3e92ce213da5e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790369"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Használja a többi Azure Media Services tartalom közzététele
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

Egy adaptív sávszélességű MP4 típusú beállításkészlettel egy adatfolyam-továbbítási OnDemand-kereső létrehozásával, és a streamelési URL-cím összeállítása is adatfolyam. A [egy eszköz kódolás](media-services-rest-encode-asset.md) a cikk bemutatja, hogyan kódolása egy adaptív sávszélességű MP4 állítsa be. Ha a tartalom titkosított, objektumtovábbítási szabályzat konfigurálása (leírtak szerint [ez](media-services-rest-configure-asset-delivery-policy.md) cikk) egy lokátor létrehozása előtt. 

OnDemand-lokátor segítségével is MP4-fájlokat fokozatosan letölthető mutató URL-címek létrehozása.  

Ez a cikk bemutatja, hogyan hozzon létre egy OnDemand-lokátor tegye közzé az adategységet, és egy Smooth, MPEG DASH vagy HLS streamelési URL-címek létrehozása céljából. Azt is bemutatja, működés közbeni hozhat létre a progresszív letöltési URL-címeket.

A [következő](#types) szakasz az a számbavételi típusok, amelynek értékeket fogja használni a többi hívásokat jeleníti meg.   

> [!NOTE]
> A Media Services entitások elérésekor be kell meghatározott fejlécmezők és értékek a HTTP-kérelmekre. További információkért lásd: [a Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz kapcsolódáshoz információkért lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Sikeres csatlakozást követően https://media.windows.net, kapni fog egy másik Media Services URI megadása 301 átirányítást. Meg kell nyitnia az új URI későbbi hívásokat.

## <a name="create-an-ondemand-streaming-locator"></a>Hozzon létre egy OnDemand-lokátor
Az OnDemand-lokátor létrehozása és URL-címek lekérése, meg kell tegye a következőket:

1. Ha a tartalom titkosított, adja meg a hozzáférési házirendek.
2. Hozzon létre egy OnDemand-lokátor.
3. Ha azt tervezi, hogy adatfolyamként küldje el, beolvasása a folyamatos átviteli jegyzékfájl (.ism) az eszközt. 
   
   Ha azt tervezi, fokozatosan letölteni, beolvasása az eszköz a MP4-fájlok nevét. 
4. A jegyzékfájl vagy MP4-fájlok összeállítása a URL-címek. 
5. Nem streamelési lokátorok létrehozásához használja az AccessPolicy, amely tartalmazza az írási és törlési jogosultságot.

### <a name="create-an-access-policy"></a>Hozzáférési házirend létrehozása

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Az azonos házirend-azonosító akkor használja, ha mindig használja az ugyanazon nap / hozzáférési engedélyek, például a lokátorokat, amelyek célja, hogy továbbra is érvényben hosszú ideje (nem feltöltés házirendek) házirendek. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

A kérelem:

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

### <a name="create-an-ondemand-streaming-locator"></a>Hozzon létre egy OnDemand-lokátor
A megadott eszköz és az eszköz házirend lokátor létrehozása.

A kérelem:

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

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"http://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"http://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Build a streaming URL-címek
Használja a **elérési** értéket adott vissza a lokátor hozhat létre a Smooth, HLS és MPEG DASH URL-címek létrehozása után. 

Smooth Streaming: **elérési** + a jegyzékfájl neve + "/ manifest"

Példa:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **elérési** + a jegyzékfájl neve + "/ manifest(format=m3u8-aapl)"

Példa:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


KÖTŐJEL: **elérési** + a jegyzékfájl neve + "/ manifest(format=mpd-time-csf)"

Példa:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Progresszív letöltési URL-címeket összeállítása
Használja a **elérési** értéket adott vissza a progresszív letöltési URL-cím létrehozásához a lokátor létrehozása után.   

URL-címe: **elérési** + eszköz mp4 fájlnév

Példa:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

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

