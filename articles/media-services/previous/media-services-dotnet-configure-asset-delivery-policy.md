---
title: Eszközkézbesítési házirendek konfigurálása .NET SDK | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan konfigurálhat különböző eszközkézbesítési szabályzatokat az Azure Media Services .NET SDK használatával.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ab3c40ee408498453bb137c63c440d980b0b7255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974512"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Eszközkézbesítési házirendek konfigurálása .NET SDK-val
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Áttekintés
Ha titkosított eszközöket kíván kézbesíteni, a Media Services tartalomkézbesítési munkafolyamatának egyik lépése az eszközök kézbesítési szabályzatainak konfigurálása. Az eszközkézbesítési szabályzat megmondja a Media Services-nek, hogyan szeretné az eszköz kézbesítését: melyik streamelési protokollba kell dinamikusan csomagolni az eszközt (például MPEG DASH, HLS, Smooth Streaming vagy az összes), függetlenül attól, hogy dinamikusan szeretné-e titkosítani az ön eszközét és módját (boríték vagy közös titkosítás).

Ez a cikk ismerteti, hogy miért és hogyan hozhat létre és konfigurálhat eszközkézbesítési szabályzatokat.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett**, **Leállítva** állapotú streamvégpontot a fiókhoz. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
>A dinamikus csomagolás és a dinamikus titkosítás használatához az eszköznek adaptív sávszélességű MP4-eket vagy adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia.

Ugyanazon eszközre különböző szabályzatokat alkalmazhat. Alkalmazhatpéldául PlayReady titkosítást a Smooth Streaming és a AES Envelope titkosításra az MPEG DASH és a HLS titkosításra. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

Ha egy tároló titkosított eszközt szeretne kézbesíteni, konfigurálnia kell az eszköz kézbesítési szabályzatát. Az eszköz streamelése előtt a streamelési kiszolgáló eltávolítja a tárolási titkosítást, és a megadott kézbesítési szabályzat használatával streameli a tartalmat. Ha például az eszközt advanced encryption standard (AES) borítéktitkosítási kulccsal titkosíthatja, állítsa a házirend típusát **DynamicEnvelopeEncryption**értékre. A tárolótitkosítás eltávolításához és az eszköz titkosításának törléséhez állítsa a házirend típusát **NoDynamicEncryption**értékre. A házirendtípusok konfigurálását bemutató példák következnek.

Attól függően, hogy hogyan konfigurálja az eszközkézbesítési házirendet, dinamikusan csomagolhatja, titkosíthatja és streamelheti a következő streamelési protokollokat: Smooth Streaming, HLS és MPEG DASH.

Az alábbi lista a Simított, A HLS és a DASH streameléséhez használt formátumokat tartalmazza.

Sima streaming:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

Hls:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Megfontolandó szempontok
* Az AssetDeliveryPolicy törlése előtt törölje az eszközhöz társított összes streamelési lokátort. Később új streamelési lokátorokat hozhat létre, ha szükséges, egy új AssetDeliveryPolicy.You can later create new stream locators, if desired, with a new AssetDeliveryPolicy.
* Nem hozható létre streamelési lokátor egy tárolótitkosított eszközön, ha nincs beállítva eszközkézbesítési házirend.  Ha az eszköz nincs titkosítva a tároló, a rendszer lehetővé teszi, hogy hozzon létre egy lokátort, és az eszköz streamelése a tiszta eszköz kézbesítési szabályzat nélkül.
* Egyetlen eszközhöz több eszközkézbesítési szabályzatis rendelkezhet, de csak egy módot adhat meg egy adott AssetDeliveryProtocol kezelésének.  Ez azt jelenti, hogy ha két kézbesítési házirendet próbál összekapcsolni, amelyek meghatározzák az AssetDeliveryProtocol.SmoothStreaming protokollt, amely hibát eredményez, mert a rendszer nem tudja, melyiket szeretné alkalmazni, amikor egy ügyfél sima streamelési kérelmet tesz.
* Ha egy meglévő streamelési lokátorral rendelkező eszközzel rendelkezik, nem kapcsolhat új szabályzatot az eszközhöz (leválaszthat egy meglévő szabályzatot az eszközről, vagy frissítheti az eszközhöz társított kézbesítési szabályzatot).  Először el kell távolítania a streamelési lokátort, módosítania kell a szabályzatokat, majd újra létre kell hoznia a streamelési lokátort.  Használhatja ugyanazt a lokátorAzonosítót, amikor újra létrehozza a streamelési lokátort, de győződjön meg arról, hogy nem okoz problémát az ügyfelek számára, mivel a tartalom gyorsítótárazható az eredeti vagy egy alsóbb rétegbeli CDN.

## <a name="clear-asset-delivery-policy"></a>Eszközkézbesítési szabályzat törlése

A következő **ConfigureClearAssetDeliveryPolicy** metódus határozza meg, hogy ne alkalmazza a dinamikus titkosítást, és az adatfolyamot a következő protokollok bármelyikében kézbesítse: MPEG DASH, HLS és Smooth Streaming protokollok. Előfordulhat, hogy szeretné alkalmazni ezt a szabályzatot a tároló titkosított eszközök.

Az AssetDeliveryPolicy létrehozásakor megadható értékekről az [AssetDeliveryPolicy definiálásakor használt típusok](#types) szakaszban talál tájékoztatást.

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption eszközkézbesítési házirend

A következő **CreateAssetDeliveryPolicy** metódus létrehozza az **AssetDeliveryPolicy metódust,** amely úgy van beállítva, hogy dinamikus közös titkosítást **(DynamicCommonEncryption)** alkalmazzon egy sima streamelési protokollra (más protokollok lejátszása le lesz tiltva). A módszer két paramétert vesz igénybe: **eszköz** (az eszköz, amelyre alkalmazni szeretné a kézbesítési házirendet) és **IContentKey** (a **CommonEncryption** típus tartalomkulcsa, további információkért lásd: [Tartalomkulcs létrehozása](media-services-dotnet-create-contentkey.md#common_contentkey)).

Az AssetDeliveryPolicy létrehozásakor megadható értékekről az [AssetDeliveryPolicy definiálásakor használt típusok](#types) szakaszban talál tájékoztatást.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Az Azure Media Services is lehetővé teszi, hogy widevine titkosítást. A következő példa bemutatja mind a PlayReady, mind a Widevine hozzáadása az eszközkézbesítési szabályzathoz.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> A Widevine titkosításakor csak a DASH használatával lehet kézbesíteni. Győződjön meg arról, hogy a DASH értéket adja meg az eszközkézbesítési protokollban.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption eszközkézbesítési házirend
A következő **CreateAssetDeliveryPolicy** metódus létrehozza az **AssetDeliveryPolicy metódust,** amely úgy van beállítva, hogy dinamikus borítéktitkosítást **(DynamicEnvelopeEncryption)** alkalmazzon a sima streamelési, HLS- és DASH-protokollokra (ha úgy dönt, hogy nem ad meg bizonyos protokollokat, akkor azok nem jelennek meg a streamelésből). A módszer két paramétert vesz igénybe: **eszköz** (az eszköz, amelyre alkalmazni szeretné a kézbesítési házirendet) és **IContentKey** (a **ContentKey** tartalomkulcsa, további információkért lásd: [Tartalomkulcs létrehozása](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Az AssetDeliveryPolicy létrehozásakor megadható értékekről az [AssetDeliveryPolicy definiálásakor használt típusok](#types) szakaszban talál tájékoztatást.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Az AssetDeliveryPolicy definiálásakor használt típusok

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol protokoll

A következő felsoraaz ondó ismerteti az eszközkézbesítési protokollhoz beállítható értékeket.

```csharp
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
```
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType típus

A következő felsoraazon értékeket ismerteti, amelyeket az eszközkézbesítési házirend típusához állíthat be.  
```csharp
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
```
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType típus

A következő felsoraazon értékeket ismerteti, amelyekkel konfigurálhatja a tartalomkulcs kézbesítési módját az ügyfélnek.
  ```csharp  
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
```
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

A következő felsora) az eszközkézbesítési házirend adott konfigurációjának lekért kulcsainak konfigurálásához beállítható értékeket ismerteti.
```csharp
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
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

