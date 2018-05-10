---
title: Konfigurálása az adategység továbbítási házirendjeit Media Services REST API használatával |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan konfigurálhatja a különböző adategység továbbítási házirendjeit Media Services REST API használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: d6f18363cceaf279d92ada77f52d39b7f1d12f65
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="configuring-asset-delivery-policies"></a>Adategység továbbítási házirendjeit konfigurálása
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Ha azt tervezi, hogy dinamikusan titkosított eszközökre, a Media Services-továbbítási munkafolyamat lépésben továbbítási házirendjeit eszközök konfigurálását végzi. Hogyan szeretné az eszköz kézbesítendő közli az adategység továbbítási házirendjét Media Services: be kell az eszköz dinamikusan csomagolható (például MPEG DASH, HLS, Smooth Streaming, vagy az összes), az eszköz dinamikusan titkosítani szeretné-e, és hogy melyik adatfolyam-protokoll (boríték vagy közös titkosítási).

Ez a témakör ismerteti, miért és hogyan hozza létre és konfigurálja az adategység továbbítási házirendjeit.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
>Is hogy fogja tudni használni a dinamikus csomagolás és a dinamikus titkosítás az objektumot kell foglal magában adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá.

Eltérő házirendek a azonos eszközhöz alkalmazhat. Például PlayReady-titkosítás beállíthat MPEG DASH vagy HLS, Smooth Streaming és AES Envelope titkosítás. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

Ha egy tárolási titkosított eszköz kézbesíteni szeretné, konfigurálnia kell az adategység továbbítási házirendjét. Mielőtt az eszköz továbbítható, a streamelési kiszolgáló a tárolás titkosítása eltávolítja, és az adatfolyamokat, a tartalom a megadott objektumtovábbítási szabályzat segítségével. Például az Advanced Encryption Standard (AES) boríték titkosítási kulccsal titkosított objektumot, hogy állítsa a házirend típusát **DynamicEnvelopeEncryption**. Tárolás titkosítása és adatfolyamként küldje el az eszköz szövegként, állítsa be a házirend típusát **NoDynamicEncryption**. Az alábbi példák, amelyek bemutatják, hogyan konfigurálhatja ezeket a házirend-típusainak.

Attól függően, hogyan konfigurálja az adategység továbbítási házirendjét lehetővé válik a dinamikus csomag dinamikusan titkosítani és adatfolyamként küldje el a következő adatfolyam-továbbítási protokollok: Smooth Streaming, HLS, MPEG DASH-streameket.

Az alábbi listában láthatók a formátumok adatfolyam Smooth, HLS, DASH segítségével.

Smooth Streaming:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

HLS:

{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


További információk az objektumok közzétételéről és a streamelési URL-cím létrehozásáról: [Build a streaming URL](media-services-deliver-streaming-content.md) (Streamelési URL-cím létrehozása).

## <a name="considerations"></a>Megfontolandó szempontok
* Egy AssetDeliveryPolicy társított egy eszköz, amíg az eszköz számára, hogy létezik egy (adatfolyam) OnDemand-kereső nem törölhető. Az ajánljuk, hogy a házirend törlése előtt távolítsa el a házirend az eszköz.
* A streamelési lokátorok létrehozásához egy tárolási titkosított eszköz nem hozható létre, ha nincs objektumtovábbítási szabályzat beállítása.  Ha az eszköz nem alkalmaz, a rendszer tájékoztatja egy kereső létrehozása és adatfolyamként küldje el az eszköz nélkül objektumtovábbítási szabályzat szövegként.
* Több adategység továbbítási házirendjeit egyetlen eszköz társított is rendelkezik, de csak egyik módja egy adott AssetDeliveryProtocol kezelni lehet megadni.  Tehát ha próbál-e a csatolás két továbbítási házirendjeit, adja meg a AssetDeliveryProtocol.SmoothStreaming protokoll, amely hibát eredményez, mert a rendszer nem tudja, melyik úgy, hogy alkalmazza, ha egy ügyfél egy Smooth Streaming-kérelmet küld.
* Ha egy eszköz rendelkezik egy meglévő streamelési locator, nem egy új házirendet csatolása az eszközhöz, megszünteti az eszköz a meglévő házirend, és nem frissíthetők a továbbítási szabályzatban az eszközhöz társított.  Először azt kell távolítsa el a streamelési locator, állíthatja a házirendeket, és hozza létre a streamelési lokátort.  Az azonos locatorId segítségével használhatja, ha a streamelési locator hozza létre újból, de győződjön meg arról, hogy nem problémákat okozhat az ügyfelek tartalmat a forrás vagy egy alárendelt CDN gyorsítótárazható óta.

>[!NOTE]

>A Media Services entitások elérésekor be kell meghatározott fejlécmezők és értékek a HTTP-kérelmekre. További információkért lásd: [a Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz kapcsolódáshoz információkért lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Objektumtovábbítási szabályzat törlése
### <a id="create_asset_delivery_policy"></a>Objektumtovábbítási szabályzat létrehozása
A következő HTTP-kérést hoz létre, amely meghatározza a dinamikus titkosítás nem alkalmazandó, és a következő protokoll egyik adatfolyam továbbítására objektumtovábbítási szabályzat: MPEG DASH, HLS vagy Smooth Streaming protokollokat. 

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor, témakörben olvashat a [AssetDeliveryPolicy meghatározásakor használhatja típusok](#types) szakasz.   

A kérelem:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Válasz:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a id="link_asset_with_asset_delivery_policy"></a>Kapcsolat eszköz az adategység továbbítási házirendjét
A következő HTTP-kérelem az adategység továbbítási házirendjét, hogy a megadott eszköz hivatkozásokat tartalmaz.

A kérelem:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Válasz:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Objektumtovábbítási szabályzat DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>A EnvelopeEncryption típusú tartalomkulcs létrehozása és csatolása az eszközhöz
DynamicEnvelopeEncryption objektumtovábbítási szabályzat megadása esetén ügyeljen arra, hogy az eszköz kapcsolódik egy tartalomkulcsot a EnvelopeEncryption típusú kell. További információkért lásd: [tartalomkulcs létrehozása](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Kézbesítési URL-cím beszerzése
A megadott kézbesítési módszert a tartalom kulcs az előző lépésben létrehozott kézbesítési URL beolvasása. Egy ügyfél használ a visszaadott URL-cím kérése az AES-kulccsal, vagy a PlayReady licenc lejátszásához ahhoz a védett tartalomhoz.

Adja meg az URL-címének a HTTP-kérelem törzse beolvasása. Véd a tartalmaknak a PlayReady, a Media Services PlayReady licenc licenckérési URL-cím kérése 1 használ a keyDeliveryType: {"keyDeliveryType": 1}. Véd-e a tartalom a boríték titkosított, egy kulcs-licenckérési URL-cím kérése az keyDeliveryType 2 megadásával: {"keyDeliveryType": 2}.

A kérelem:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

Válasz:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### <a name="create-asset-delivery-policy"></a>Objektumtovábbítási szabályzat létrehozása
A következő HTTP-kérést hoz létre a **AssetDeliveryPolicy** dinamikus boríték titkosítási alkalmazandó konfigurált (**DynamicEnvelopeEncryption**) számára a **HLS** protokoll (ebben a példában egyéb protokollok le lesz tiltva streaming). 

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor, témakörben olvashat a [AssetDeliveryPolicy meghatározásakor használhatja típusok](#types) szakasz.   

A kérelem:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


Válasz:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### <a name="link-asset-with-asset-delivery-policy"></a>Kapcsolat eszköz az adategység továbbítási házirendjét
Lásd: [hivatkozás eszköz az adategység továbbítási házirendjét](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Objektumtovábbítási szabályzat DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>A CommonEncryption típusú tartalomkulcs létrehozása és csatolása az eszközhöz
DynamicCommonEncryption objektumtovábbítási szabályzat megadása esetén ügyeljen arra, hogy az eszköz kapcsolódik egy tartalomkulcsot a CommonEncryption típusú kell. További információkért lásd: [tartalomkulcs létrehozása](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Kézbesítési URL-cím beszerzése
Kézbesítési URL beolvasása a PlayReady kézbesítési módszert a tartalom az előző lépésben létrehozott kulcs. Egy ügyfél a védett tartalmak lejátszásához sorrendben PlayReady licencet lekérni a visszaadott URL-címet használ. További információkért lásd: [kézbesítési URL-cím beszerzése](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Objektumtovábbítási szabályzat létrehozása
A következő HTTP-kérést hoz létre a **AssetDeliveryPolicy** konfigurált alkalmazni a dynamic common encryption (**DynamicCommonEncryption**) számára a **Smooth Streaming**protokoll (ebben a példában egyéb protokollok le lesz tiltva streaming). 

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor, témakörben olvashat a [AssetDeliveryPolicy meghatározásakor használhatja típusok](#types) szakasz.   

A kérelem:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Ha a tartalom Widevine DRM segítségével védeni kívánt, frissítse az AssetDeliveryConfiguration értékeket használatára (amely értéke a 7) WidevineLicenseAcquisitionUrl, és adjon meg egy licenctovábbítási szolgáltatása URL-CÍMÉT. A következő AMS-partnereket segítségével Widevine-licencek segítségével: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Példa: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Widevine titkosításakor csak lenne DASH használatával küldött. Győződjön meg arról, ha meg szeretné adni az objektumtovábbítási protokoll kötőjel (2).
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Kapcsolat eszköz az adategység továbbítási házirendjét
Lásd: [hivatkozás eszköz az adategység továbbítási házirendjét](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Típusok AssetDeliveryPolicy definiálásakor használja

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

A következő felsorolás ismerteti értékeket adhatja meg az objektumtovábbítási protokoll.

    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

A következő felsorolás ismerteti, állíthatja be a kézbesítési házirend típusú értékeket.  

    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

A következő felsorolás ismerteti a segítségével konfigurálhatja a kézbesítési módszert az ügyfél a tartalom kulcs értékeket.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

A következő felsorolás ismerteti a kulcsok segítségével kéri le a meghatározott konfigurációját objektumtovábbítási szabályzat konfigurálása és értékeket.

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

