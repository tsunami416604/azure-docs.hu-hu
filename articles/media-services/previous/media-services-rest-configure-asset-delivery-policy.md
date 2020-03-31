---
title: Eszközkézbesítési házirendek konfigurálása a Media Services REST API használatával | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan konfigurálhatja a különböző eszközkézbesítési szabályzatokat a Media Services REST API használatával.
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
ms.openlocfilehash: 09f0371bc189fcf7b25ec3261e2e1f5eaf1892ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194490"
---
# <a name="configuring-asset-delivery-policies"></a>Eszközkézbesítési házirendek konfigurálása
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Ha dinamikusan titkosított eszközöket kíván biztosítani, a Media Services tartalomkézbesítési munkafolyamatának egyik lépése az eszközök kézbesítési szabályzatainak konfigurálása. Az eszközkézbesítési szabályzat megmondja a Media Services-nek, hogyan szeretné az eszköz kézbesítését: melyik streamelési protokollba kell dinamikusan csomagolni az eszközt (például MPEG DASH, HLS, Smooth Streaming vagy az összes), függetlenül attól, hogy dinamikusan szeretné-e titkosítani az ön eszközét és módját (boríték vagy közös titkosítás).

Ez a témakör ismerteti, hogy miért és hogyan hozhat létre és konfigurálhat eszközkézbesítési szabályzatokat.

> [!NOTE]
> Amikor az AMS-fiók jön létre egy **alapértelmezett** streamelési végpont ot a fiók **leállított** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
> A dinamikus csomagolás és a dinamikus titkosítás használatához az eszköznek adaptív sávszélességű MP4-eket vagy adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia.

Ugyanazon eszközre különböző szabályzatokat alkalmazhat. Alkalmazhatpéldául PlayReady titkosítást a Smooth Streaming és a AES Envelope titkosításra az MPEG DASH és a HLS titkosításra. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

Ha egy tároló titkosított eszközt szeretne kézbesíteni, konfigurálnia kell az eszköz kézbesítési szabályzatát. Az eszköz streamelése előtt a streamelési kiszolgáló eltávolítja a tárolási titkosítást, és a megadott kézbesítési szabályzat használatával streameli a tartalmat. Ha például az eszközt advanced encryption standard (AES) borítéktitkosítási kulccsal titkosíthatja, állítsa a házirend típusát **DynamicEnvelopeEncryption**értékre. A tárolótitkosítás eltávolításához és az eszköz titkosításának törléséhez állítsa a házirend típusát **NoDynamicEncryption**értékre. A házirendtípusok konfigurálását bemutató példák következnek.

Attól függően, hogy hogyan konfigurálja az eszközkézbesítési házirendet, dinamikusan csomagolhatja, dinamikusan titkosíthatja és streamelheti a következő streamelési protokollokat: Smooth Streaming, HLS, MPEG DASH streamelések.

Az alábbi lista a Smooth, HLS, DASH streameléséhez használt formátumokat tartalmazza.

Sima streaming:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

Hls:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=mpd-time-csf)


További információk az objektumok közzétételéről és a streamelési URL-cím létrehozásáról: [Build a streaming URL](media-services-deliver-streaming-content.md) (Streamelési URL-cím létrehozása).

## <a name="considerations"></a>Megfontolandó szempontok
* Egy eszközhöz társított AssetDeliveryPolicy nem törölhető, amíg az adott eszközhöz létezik ondemand (streamelési) lokátor. A javaslat az, hogy távolítsa el a szabályzatot az eszközből a szabályzat törlése előtt.
* Nem hozható létre streamelési lokátor egy tárolótitkosított eszközön, ha nincs beállítva eszközkézbesítési házirend.  Ha az eszköz nincs titkosítva a tároló, a rendszer lehetővé teszi, hogy hozzon létre egy lokátort, és az eszköz streamelése a tiszta eszköz kézbesítési szabályzat nélkül.
* Egyetlen eszközhöz több eszközkézbesítési szabályzatis rendelkezhet, de csak egy módot adhat meg egy adott AssetDeliveryProtocol kezelésének.  Ez azt jelenti, hogy ha két kézbesítési házirendet próbál összekapcsolni, amelyek meghatározzák az AssetDeliveryProtocol.SmoothStreaming protokollt, amely hibát eredményez, mert a rendszer nem tudja, melyiket szeretné alkalmazni, amikor egy ügyfél sima streamelési kérelmet tesz.
* Ha egy meglévő streamelési lokátorral rendelkező eszközzel rendelkezik, nem csatolhat új szabályzatot az eszközhöz, nem választhat le egy meglévő szabályzatot az eszközről, és nem frissítheti az eszközhöz társított kézbesítési szabályzatot.  Először el kell távolítania a streamelési lokátort, módosítania kell a szabályzatokat, majd újra létre kell hoznia a streamelési lokátort.  Használhatja ugyanazt a lokátorAzonosítót, amikor újra létrehozza a streamelési lokátort, de győződjön meg arról, hogy nem okoz problémát az ügyfelek számára, mivel a tartalom gyorsítótárazható az eredeti vagy egy alsóbb rétegbeli CDN.

> [!NOTE]
> 
> Amikor a Media Services entitásait éri el, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz való csatlakozásról az [Access the Azure Media Services API azure AD-hitelesítéssel című témakörben](media-services-use-aad-auth-to-access-ams-api.md)talál további információt. 

## <a name="clear-asset-delivery-policy"></a>Eszközkézbesítési szabályzat törlése
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Eszközkézbesítési szabályzat létrehozása
A következő HTTP-kérelem létrehoz egy eszközkézbesítési szabályzatot, amely meghatározza, hogy ne alkalmazza a dinamikus titkosítást, és az adatfolyamot a következő protokollok bármelyikében kézbesítse: MPEG DASH, HLS és Smooth Streaming protokollok. 

Az AssetDeliveryPolicy létrehozásakor megadható értékekről az [AssetDeliveryPolicy definiálásakor használt típusok](#types) szakaszban talál tájékoztatást.   

Kérés:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Eszköz összekapcsolása az eszközkézbesítési szabályzattal
A következő HTTP-kérelem a megadott eszközt az eszközkézbesítési szabályzathoz kapcsolja.

Kérés:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Válasz:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption eszközkézbesítési házirend
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>EnvelopeEncryption típusú tartalomkulcs létrehozása és csatolása az eszközhöz
A DynamicEnvelopeEncryption kézbesítési házirend megadásakor győződjön meg arról, hogy az eszközt a EnvelopeEncryption típusú tartalomkulcshoz kapcsolja. További információ: [Tartalomkulcs létrehozása](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Szállítási URL-cím beszerezni
Az előző lépésben létrehozott tartalomkulcs megadott kézbesítési módjának kézbesítési URL-címe. Az ügyfél a visszaadott URL-címet használja egy AES-kulcs vagy PlayReady-licenc kéréséhez a védett tartalom lejátszásához.

Adja meg a HTTP-kérelem törzsében bekerülő URL-cím típusát. Ha a tartalmat a PlayReady segítségével védi, kérjen egy Media Services PlayReady licencbeszerzési URL-t a keyDeliveryType 1 használatával: {"keyDeliveryType":1}. Ha a tartalmat borítéktitkosítással védi, kérjen egy kulcsbeszerzési URL-címet a keyDeliveryType 2 megadásával: {"keyDeliveryType":2}.

Kérés:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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


### <a name="create-asset-delivery-policy"></a>Eszközkézbesítési szabályzat létrehozása
A következő HTTP-kérelem létrehozza az **AssetDeliveryPolicy-et,** amely úgy van beállítva, hogy dinamikus borítéktitkosítást **(DynamicEnvelopeEncryption)** alkalmazzon a **HLS** protokollra (ebben a példában más protokollok lejátszása le lesz tiltva a streamelésből). 

Az AssetDeliveryPolicy létrehozásakor megadható értékekről az [AssetDeliveryPolicy definiálásakor használt típusok](#types) szakaszban talál tájékoztatást.   

Kérés:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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


### <a name="link-asset-with-asset-delivery-policy"></a>Eszköz összekapcsolása az eszközkézbesítési szabályzattal
Lásd: [Eszköz összekapcsolása az eszközkézbesítési szabályzattal](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption eszközkézbesítési házirend
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>CommonEncryption típusú tartalomkulcs létrehozása és csatolása az eszközhöz
A DynamicCommonEncryption kézbesítési házirend megadásakor győződjön meg arról, hogy az eszközt a CommonEncryption típusú tartalomkulcshoz kapcsolja. További információ: [Tartalomkulcs létrehozása](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Kézbesítésurl-címének beszereznie
Az előző lépésben létrehozott tartalomkulcs PlayReady kézbesítési módjának kézbesítési URL-címe. Az ügyfél a visszaadott URL-címet használja a PlayReady-licenc kéréséhez a védett tartalom lejátszásához. További információt a [Kézbesítés URL-címének beszereznie](#get_delivery_url)című témakörben talál.

### <a name="create-asset-delivery-policy"></a>Eszközkézbesítési szabályzat létrehozása
A következő HTTP-kérelem létrehozza az **AssetDeliveryPolicy-et,** amely úgy van konfigurálva, hogy dinamikus közös titkosítást **(DynamicCommonEncryption)** alkalmazzon a **simított streamelési** protokollra (ebben a példában más protokollok lejátszása le lesz tiltva a streamelésből). 

Az AssetDeliveryPolicy létrehozásakor megadható értékekről az [AssetDeliveryPolicy definiálásakor használt típusok](#types) szakaszban talál tájékoztatást.   

Kérés:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Ha a tartalmat a Widevine DRM használatával szeretné védeni, frissítse az AssetDeliveryConfiguration értékeket a WidevineLicenseAcquisitionUrl használatához (amelynek értéke 7), és adja meg a licenckézbesítési szolgáltatás URL-címét. A következő AMS-partnerek segítségével biztosíthatja a Widevine-licenceket: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Példa: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> A Widevine titkosításakor csak a DASH használatával lehet kézbesíteni. Győződjön meg arról, hogy a DASH (2) értéket adja meg az eszközkézbesítési protokollban.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Eszköz összekapcsolása az eszközkézbesítési szabályzattal
Lásd: [Eszköz összekapcsolása az eszközkézbesítési szabályzattal](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Az AssetDeliveryPolicy definiálásakor használt típusok

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol protokoll

A következő felsoraaz ondó ismerteti az eszközkézbesítési protokollhoz beállítható értékeket.

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

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType típus

A következő felsoraazon értékeket ismerteti, amelyeket az eszközkézbesítési házirend típusához állíthat be.  

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

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType típus

A következő felsoraazon értékeket ismerteti, amelyekkel konfigurálhatja a tartalomkulcs kézbesítési módját az ügyfélnek.
    
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

A következő felsora) az eszközkézbesítési házirend adott konfigurációjának lekért kulcsainak konfigurálásához beállítható értékeket ismerteti.

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

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

