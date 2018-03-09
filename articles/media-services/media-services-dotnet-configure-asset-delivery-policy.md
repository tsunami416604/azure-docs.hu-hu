---
title: "Konfigurálja az adategység továbbítási házirendjeit .NET SDK-val |} Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan különböző adategység továbbítási házirendjeit konfigurálása az Azure Media Services .NET SDK-val."
services: media-services
documentationcenter: 
author: Mingfeiy
manager: cfowler
editor: 
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/05/2018
ms.author: juliako
ms.openlocfilehash: 8fe78d2d8cef1ee5484bcdcd83b00afe8e5b30ce
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurálja az adategység továbbítási házirendjeit .NET SDK-val
[!INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Áttekintés
Ha azt tervezi, kézbesítési titkosított eszközökre, a Media Services-továbbítási munkafolyamat lépésben továbbítási házirendjeit eszközök konfigurálását végzi. Hogyan szeretné az eszköz kézbesítendő közli az adategység továbbítási házirendjét Media Services: be kell az eszköz dinamikusan csomagolható (például MPEG DASH, HLS, Smooth Streaming, vagy az összes), az eszköz dinamikusan titkosítani szeretné-e, és hogy melyik adatfolyam-protokoll (boríték vagy közös titkosítási).

A cikk ismerteti, miért és hogyan hozza létre és konfigurálja az adategység továbbítási házirendjeit.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett**, **Leállítva** állapotú streamvégpontot a fiókhoz. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
>Is hogy fogja tudni használni a dinamikus csomagolás és a dinamikus titkosítás az objektumot kell foglal magában adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá.

Eltérő házirendek a azonos eszközhöz alkalmazhat. Például PlayReady-titkosítás beállíthat MPEG DASH vagy HLS, Smooth Streaming és AES Envelope titkosítás. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

Ha egy tárolási titkosított eszköz kézbesíteni szeretné, konfigurálnia kell az adategység továbbítási házirendjét. Mielőtt az eszköz továbbítható, a streamelési kiszolgáló a tárolás titkosítása eltávolítja, és az adatfolyamokat, a tartalom a megadott objektumtovábbítási szabályzat segítségével. Például az Advanced Encryption Standard (AES) boríték titkosítási kulccsal titkosított objektumot, hogy állítsa a házirend típusát **DynamicEnvelopeEncryption**. Tárolás titkosítása és adatfolyamként küldje el az eszköz szövegként, állítsa be a házirend típusát **NoDynamicEncryption**. Az alábbi példák, amelyek bemutatják, hogyan konfigurálhatja ezeket a házirend-típusainak.

Attól függően, hogy hogyan konfigurálja az adategység továbbítási házirendjét, akkor is dinamikusan csomag, titkosításához és adatfolyamként küldje el a következő adatfolyam-továbbítási protokollok: Smooth Streaming, HLS vagy MPEG DASH.

Az alábbi listában láthatók a formátumok Smooth, HLS és kötőjel adatfolyamként történő küldéséhez használható.

Smooth Streaming:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

HLS:

{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Megfontolandó szempontok
* Mielőtt törölné a AssetDeliveryPolicy, törölje a streamelési keresők az eszközhöz társított összes. Új streamelési Locator, később szükség esetén egy új AssetDeliveryPolicy hozhat létre.
* A streamelési lokátorok létrehozásához egy tárolási titkosított eszköz nem hozható létre, ha nincs objektumtovábbítási szabályzat beállítása.  Ha az eszköz nem alkalmaz, a rendszer tájékoztatja egy kereső létrehozása és adatfolyamként küldje el az eszköz nélkül objektumtovábbítási szabályzat szövegként.
* Több adategység továbbítási házirendjeit egyetlen eszköz társított is rendelkezik, de csak egyik módja egy adott AssetDeliveryProtocol kezelni lehet megadni.  Tehát ha próbál-e a csatolás két továbbítási házirendjeit, adja meg a AssetDeliveryProtocol.SmoothStreaming protokoll, amely hibát eredményez, mert a rendszer nem tudja, melyik úgy, hogy alkalmazza, ha egy ügyfél egy Smooth Streaming-kérelmet küld.
* Ha egy eszköz rendelkezik egy meglévő streamelési locator, egy új házirend az eszközre, ezért nem csatolható (megszünteti az eszköz a meglévő házirend, vagy frissítse a továbbítási szabályzatban az eszközhöz társított).  Először azt kell távolítsa el a streamelési locator, állíthatja a házirendeket, és hozza létre a streamelési lokátort.  Az azonos locatorId segítségével használhatja, ha a streamelési locator hozza létre újból, de győződjön meg arról, hogy nem problémákat okozhat az ügyfelek tartalmat a forrás vagy egy alárendelt CDN gyorsítótárazható óta.

## <a name="clear-asset-delivery-policy"></a>Objektumtovábbítási szabályzat törlése

A következő **ConfigureClearAssetDeliveryPolicy** módszer határozza meg, nem vonatkoznak a dinamikus titkosítás és a következő protokoll egyik adatfolyam továbbítására: MPEG DASH, HLS vagy Smooth Streaming protokollokat. A tárolás titkosítása ezt a házirendet alkalmazni kívánt.

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor, témakörben olvashat a [AssetDeliveryPolicy meghatározásakor használhatja típusok](#types) szakasz.

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
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Objektumtovábbítási szabályzat DynamicCommonEncryption

A következő **CreateAssetDeliveryPolicy** hoz létre a **AssetDeliveryPolicy** konfigurált alkalmazni a dynamic common encryption (**DynamicCommonEncryption**) egy smooth adatfolyam-továbbítási protokoll (egyéb protokollok le lesz tiltva streaming). A módszer két paramétereket fogadja: **eszköz** (az objektum továbbítási szabályzatát alkalmazni kívánt) és **IContentKey** (a tartalomkulcsot, a **CommonEncryption** típusa, a További információkért lásd: [tartalomkulcs létrehozása](media-services-dotnet-create-contentkey.md#common_contentkey)).

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor, témakörben olvashat a [AssetDeliveryPolicy meghatározásakor használhatja típusok](#types) szakasz.

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

Az Azure Media Services lehetővé teszi Widevine titkosítása. A következő példa bemutatja, mind a PlayReady, mind a Widevine felvenni kívánt az adategység továbbítási házirendjét.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
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
> Widevine titkosításakor csak lenne DASH használatával küldött. Győződjön meg arról, ha meg szeretné adni az objektumtovábbítási protokoll kötőjel.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Objektumtovábbítási szabályzat DynamicEnvelopeEncryption
A következő **CreateAssetDeliveryPolicy** hoz létre a **AssetDeliveryPolicy** dinamikus boríték titkosítási alkalmazandó konfigurált (**DynamicEnvelopeEncryption**) a Smooth Streaming, HLS vagy kötőjel protokollok (Ha úgy dönt, hogy nem adja meg az egyes protokollok, le lesznek tiltva a folyamatos átviteli). A módszer két paramétert fogad: **eszköz** (az objektum továbbítási szabályzatát alkalmazni kívánt) és **IContentKey** (a tartalomkulcsot, a **EnvelopeEncryption** típusa További információkért lásd: [tartalomkulcs létrehozása](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor, témakörben olvashat a [AssetDeliveryPolicy meghatározásakor használhatja típusok](#types) szakasz.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
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

## <a id="types"></a>Típusok AssetDeliveryPolicy definiálásakor használja

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

A következő felsorolás ismerteti értékeket adhatja meg az objektumtovábbítási protokoll.

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
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

A következő felsorolás ismerteti, állíthatja be a kézbesítési házirend típusú értékeket.  
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
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

A következő felsorolás ismerteti a segítségével konfigurálhatja a kézbesítési módszert az ügyfél a tartalom kulcs értékeket.
  ```csharp  
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
```
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

A következő felsorolás ismerteti a kulcsok segítségével kéri le a meghatározott konfigurációját objektumtovábbítási szabályzat konfigurálása és értékeket.
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
## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

