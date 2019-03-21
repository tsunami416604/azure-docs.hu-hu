---
title: A Media Services REST API használatával adategység-kézbesítési házirendek konfigurálása |} A Microsoft Docs
description: Ez a témakör bemutatja a Media Services REST API használatával különböző adategység-kézbesítési házirendek konfigurálása.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 5e4e565b0b5272de19458617a9c4bd3509907cce
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295725"
---
# <a name="configuring-asset-delivery-policies"></a>Adategység-kézbesítési házirendek konfigurálása
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Ha azt tervezi, hogy dinamikusan titkosított eszközökre, a Media Services-továbbítási munkafolyamat lépésben konfigurálja kézbesítési házirendek az eszközök. Az objektumtovábbítási szabályzat arra utasítja a Media Services az objektum kézbesítendő módját: az adatfolyam-továbbítási protokoll kell az eszközintelligencia dinamikusan csomagolható (például MPEG DASH, HLS, Smooth Streaming, vagy az összes), dinamikusan titkosítani szeretné-e az objektumot, és hogyan (boríték vagy common encryption).

Ez a témakör ismerteti, miért és hogyan hozhat létre és konfigurálja az adategység továbbítási házirendjeit.

> [!NOTE]
> Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
> Is használhatja a dinamikus csomagolás és a dinamikus titkosítás, az eszköz tartalmaznia kell egy adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá készletét.

Adott objektum sikerült különböző szabályzatok vonatkoznak. PlayReady-titkosítás például MPEG DASH vagy HLS, Smooth Streaming az AES Envelope pedig titkosítás tudta alkalmazni. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

Ha azt szeretné, hogy a tárolási titkosított eszköz, konfigurálnia kell az adategység továbbítási házirendjét. Az eszközintelligencia továbbítható, mielőtt a streamelési kiszolgáló eltávolítja a tárolás titkosítása, és adatfolyamként elküldi a tartalmát a megadott objektumtovábbítási szabályzat használatával. Például, hogy az eszköz Advanced Encryption Standard (AES) boríték titkosítási kulccsal titkosított, állítsa be ezt a házirendtípus **DynamicEnvelopeEncryption**. Távolítsa el a storage-titkosítás, illetve streamelni az eszköz a titkosítatlan, állítsa be a házirend típusát **NoDynamicEncryption**. Az alábbi példák azt mutatják be, ezek a házirend-típusainak konfigurálása.

Attól függően, hogyan konfigurálja az adategység továbbítási házirendjét válik dinamikus csomagolásához, dinamikusan titkosítja, és adatfolyam a következő adatfolyam-továbbítási protokollok: Smooth Streaming, HLS, MPEG-DASH-streameket.

Az alábbi lista tartalmazza a formátumok használatával stream Smooth, HLS, DASH.

Smooth Streaming:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

HLS:

{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


További információk az objektumok közzétételéről és a streamelési URL-cím létrehozásáról: [Build a streaming URL](media-services-deliver-streaming-content.md) (Streamelési URL-cím létrehozása).

## <a name="considerations"></a>Megfontolandó szempontok
* Egy adategység társított, amíg az eszköz létezik (Stream) OnDemand-lokátort AssetDeliveryPolicy nem törölhető. A javaslat, hogy a házirend törlése előtt távolítsa el a szabályzatot az eszköz.
* A streamelési lokátorok nem hozható létre egy tárolási titkosított eszköz nem állít be objektumtovábbítási szabályzatot beállításánál nem.  Ha az eszköz nincs titkosítva a storage, a rendszer lehetővé teszi, hozzon létre egy keresőt, illetve streamelni az eszköz a titkosítatlan objektumtovábbítási szabályzat nélkül.
* Társított egyetlen eszköz több adategység továbbítási házirendjeit rendelkezhet, de csak egyik módja egy adott AssetDeliveryProtocol kezelni lehet megadni.  Tehát ha azelőtt próbál két kézbesítési házirendek által megadott hibát eredményez, mivel a rendszer nem tudja, amely egy azt szeretné, hogy a alkalmazni, ha egy ügyfél kérést küld Smooth Streaming AssetDeliveryProtocol.SmoothStreaming protokolltól hivatkozásra.
* Ha egy eszköz rendelkezik egy meglévő streamelési lokátort, nem egy új szabályzat csatolása az objektumot, egy meglévő szabályzatot, az eszköz leválasztása, vagy frissítése az eszközhöz társított továbbítási szabályzatban.  Először szüksége van, távolítsa el a streamelési lokátort, állíthatja a házirendeket, és hozza létre újra a streamelési lokátort.  Az azonos locatorId a streamelési lokátorok hozza létre újra, de győződjön meg arról, hogy nem okozhat problémát az ügyfelek óta a gyorsítótárazható tartalom, a nulla vagy egy alsóbb rétegbeli CDN által használható.

> [!NOTE]
> 
> A Media Services entitások elérésekor a be kell állítani a HTTP-kérelmekre a meghatározott fejlécmezők és értékek. További információkért lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-t kapcsolódás információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Objektumtovábbítási szabályzat törlése
### <a id="create_asset_delivery_policy"></a>Objektumtovábbítási szabályzat létrehozása
A következő HTTP-kérést hoz létre, amelyben megadja, hogy nem alkalmazza a dinamikus titkosítás, hogy az adatfolyam az alábbi protokollok objektumtovábbítási szabályzat:  MPEG DASH, HLS és Smooth Streaming protokollokat. 

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor kapcsolatos tudnivalókat lásd: a [AssetDeliveryPolicy meghatározásakor típusok](#types) szakaszban.   

Kérés:

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

### <a id="link_asset_with_asset_delivery_policy"></a>Az adategység továbbítási házirendjét hivatkozás eszköz
A következő HTTP-kérelem az adategység továbbítási házirendjét, hogy a megadott eszköz hivatkozik.

Kérés:

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


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption állít be objektumtovábbítási szabályzatot
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>A EnvelopeEncryption típusú tartalomkulcs létrehozása és csatolása az eszközhöz
Objektumtovábbítási szabályzat DynamicEnvelopeEncryption megadásakor ügyeljen arra, hogy az eszköz egy tartalomkulcsot EnvelopeEncryption típusú mutató hivatkozás kell. További információkért lásd: [Tartalomkulcs létrehozása](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Kézbesítési URL-Címének lekéréséhez
A kézbesítési URL beolvasása a megadott kézbesítési módszert a tartalom kulcs az előző lépésben létrehozott. Ügyfél használ a visszaadott URL-cím kérése az AES-kulccsal, vagy a PlayReady licenc lejátszási ahhoz a védett tartalomhoz.

Adja meg az URL-CÍMÉT úgy szerezheti be a HTTP-kérelem törzse a típusát. Ha a védett tartalom PlayReady, a Media Services PlayReady licenc licenckérési URL-cím kérése a keyDeliveryType használja a 1: {"keyDeliveryType": 1}. A boríték-titkosítást tartalom védelme, egy fő licenckérési URL-cím kérése 2 keyDeliveryType megadásával: {"keyDeliveryType": 2}.

Kérés:

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
A következő HTTP-kérést hoz létre a **AssetDeliveryPolicy** megfelelően van konfigurálva a alkalmazni dinamikus boríték-titkosítást (**DynamicEnvelopeEncryption**), a **HLS** protokoll (ebben a példában egyéb protokollok le lesz tiltva a streaming). 

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor kapcsolatos tudnivalókat lásd: a [AssetDeliveryPolicy meghatározásakor típusok](#types) szakaszban.   

Kérés:

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


### <a name="link-asset-with-asset-delivery-policy"></a>Az adategység továbbítási házirendjét hivatkozás eszköz
Lásd: [hivatkozás eszköz az adategység továbbítási házirendjét](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption asset delivery policy
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>A CommonEncryption típusú tartalomkulcs létrehozása és csatolása az eszközhöz
Objektumtovábbítási szabályzat DynamicCommonEncryption megadásakor ügyeljen arra, hogy az eszköz egy tartalomkulcsot CommonEncryption típusú mutató hivatkozás kell. További információkért lásd: [Tartalomkulcs létrehozása](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Kézbesítési URL-Címének lekéréséhez
URL beolvasása a kézbesítési a PlayReady kézbesítési módszert a tartalom kulcs az előző lépésben létrehozott. Egy ügyfél használja a visszaadott URL-cím kérése érdekében, hogy a védett tartalmak lejátszás PlayReady-licenc. További információkért lásd: [kézbesítési URL-cím lekérése](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Objektumtovábbítási szabályzat létrehozása
A következő HTTP-kérést hoz létre a **AssetDeliveryPolicy** megfelelően van konfigurálva a alkalmazni a dynamic common encryption (**DynamicCommonEncryption**), a **Smooth Streaming**protokoll (ebben a példában egyéb protokollok le lesz tiltva a streaming). 

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor kapcsolatos tudnivalókat lásd: a [AssetDeliveryPolicy meghatározásakor típusok](#types) szakaszban.   

Kérés:

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


Ha a tartalom a Widevine DRM-mel védeni kívánt, frissítse a AssetDeliveryConfiguration WidevineLicenseAcquisitionUrl (amely 7 érték szerepel-e) használata, és adja meg az URL-címét egy szolgáltatásra vonatkozó. Widevine-licencek biztosításához a következő AMS-partnereket is használja: [Az Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Példa: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> A Widevine titkosításakor csak tudná DASH segítségével. Győződjön meg arról, ha meg szeretné adni az objektumtovábbítási protokoll DASH (2).
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Az adategység továbbítási házirendjét hivatkozás eszköz
Lásd: [hivatkozás eszköz az adategység továbbítási házirendjét](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>AssetDeliveryPolicy meghatározásakor típusok

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

A következő felsorolás ismerteti értékeket is megadhatja a kézbesítési szabályzat típusú eszközök esetén.  

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

A következő felsorolás ismerteti segítségével konfigurálja a kézbesítési módszert az ügyfél a tartalom kulcs értékeket.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

A következő felsorolás ismerteti konfigurálása olvashatók be objektumtovábbítási szabályzat a konkrét konfigurációs kulcsokat és megadható értékeket.

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

