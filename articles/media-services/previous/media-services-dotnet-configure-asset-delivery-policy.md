---
title: Adategység-kézbesítési házirendek konfigurálása a .NET SDK-val |} A Microsoft Docs
description: Ez a témakör bemutatja a különböző adategység-kézbesítési házirendek konfigurálása az Azure Media Services .NET SDK-t.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: cfowler
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/05/2018
ms.author: juliako
ms.openlocfilehash: d96ed28be2fcb3941591854662f9aa20faf04e5d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810159"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Adategység-kézbesítési házirendek konfigurálása a .NET SDK-val
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Áttekintés
Ha azt tervezi, titkosított kézbesítési eszközök, az egyik a Media Services content delivery munkafolyamat lépései kézbesítési házirendek az eszközök konfigurálja. Az objektumtovábbítási szabályzat arra utasítja a Media Services az objektum kézbesítendő módját: az adatfolyam-továbbítási protokoll kell az eszközintelligencia dinamikusan csomagolható (például MPEG DASH, HLS, Smooth Streaming, vagy az összes), dinamikusan titkosítani szeretné-e az objektumot, és hogyan (boríték vagy common encryption).

Ez a cikk ismerteti, miért és hogyan hozhat létre és konfigurálja az adategység továbbítási házirendjeit.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett**, **Leállítva** állapotú streamvégpontot a fiókhoz. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
>Is használhatja a dinamikus csomagolás és a dinamikus titkosítás, az eszköz tartalmaznia kell egy adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá készletét.

Adott objektum sikerült különböző szabályzatok vonatkoznak. PlayReady-titkosítás például MPEG DASH vagy HLS, Smooth Streaming az AES Envelope pedig titkosítás tudta alkalmazni. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

Ha azt szeretné, hogy a tárolási titkosított eszköz, konfigurálnia kell az adategység továbbítási házirendjét. Az eszközintelligencia továbbítható, mielőtt a streamelési kiszolgáló eltávolítja a tárolás titkosítása, és adatfolyamként elküldi a tartalmát a megadott objektumtovábbítási szabályzat használatával. Például, hogy az eszköz Advanced Encryption Standard (AES) boríték titkosítási kulccsal titkosított, állítsa be ezt a házirendtípus **DynamicEnvelopeEncryption**. Távolítsa el a storage-titkosítás, illetve streamelni az eszköz a titkosítatlan, állítsa be a házirend típusát **NoDynamicEncryption**. Az alábbi példák azt mutatják be, ezek a házirend-típusainak konfigurálása.

Attól függően, hogy hogyan konfigurálhat az adategység továbbítási házirendjét dinamikusan csomag, titkosítása, és beállíthatja a következő adatfolyam-továbbítási protokollok adatfolyam: Smooth Streaming, HLS és MPEG DASH.

Az alábbi lista tartalmazza a formátumok streamelésére, Smooth, HLS és kötőjel használható.

Smooth Streaming:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

HLS:

{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Megfontolandó szempontok
* Mielőtt törölné a AssetDeliveryPolicy, törölje a streamelési lokátorok az eszközhöz társított összes. Új streamelési lokátorok, később szükség esetén egy új AssetDeliveryPolicy hozhat létre.
* A streamelési lokátorok nem hozható létre egy tárolási titkosított eszköz nem állít be objektumtovábbítási szabályzatot beállításánál nem.  Ha az eszköz nincs titkosítva a storage, a rendszer lehetővé teszi, hozzon létre egy keresőt, illetve streamelni az eszköz a titkosítatlan objektumtovábbítási szabályzat nélkül.
* Társított egyetlen eszköz több adategység továbbítási házirendjeit rendelkezhet, de csak egyik módja egy adott AssetDeliveryProtocol kezelni lehet megadni.  Tehát ha azelőtt próbál két kézbesítési házirendek által megadott hibát eredményez, mivel a rendszer nem tudja, amely egy azt szeretné, hogy a alkalmazni, ha egy ügyfél kérést küld Smooth Streaming AssetDeliveryProtocol.SmoothStreaming protokolltól hivatkozásra.
* Ha egy eszköz rendelkezik egy meglévő streamelési lokátort, egy új szabályzatot, nem sikerült összekapcsolni az eszközhöz (akkor is egy meglévő szabályzatot, az eszköz leválasztása vagy frissítése az eszközhöz társított továbbítási szabályzatban).  Először szüksége van, távolítsa el a streamelési lokátort, állíthatja a házirendeket, és hozza létre újra a streamelési lokátort.  Az azonos locatorId a streamelési lokátorok hozza létre újra, de győződjön meg arról, hogy nem okozhat problémát az ügyfelek óta a gyorsítótárazható tartalom, a nulla vagy egy alsóbb rétegbeli CDN által használható.

## <a name="clear-asset-delivery-policy"></a>Objektumtovábbítási szabályzat törlése

A következő **ConfigureClearAssetDeliveryPolicy** metódus nem vonatkoznak a dinamikus titkosítás, és az adatfolyam az alábbi protokollok biztosításához adja meg:  MPEG DASH, HLS és Smooth Streaming protokollokat. Előfordulhat, hogy szeretné alkalmazza a szabályzatot, a storage titkosított eszközökre.

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor kapcsolatos tudnivalókat lásd: a [AssetDeliveryPolicy meghatározásakor típusok](#types) szakaszban.

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
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption asset delivery policy

A következő **CreateAssetDeliveryPolicy** metódus hoz létre a **AssetDeliveryPolicy** megfelelően van konfigurálva a alkalmazni a dynamic common encryption (**DynamicCommonEncryption**) (egyéb protokollok le lesz tiltva a streaming) smooth streaming protokollhoz. A módszer két paraméter szükséges: **Eszköz** (az eszköz szeretné a alkalmazni a kézbesítési szabályzat) és **IContentKey** (a tartalomkulcsot, a **CommonEncryption** típus, további információkért lásd: [Tartalomkulcs létrehozása](media-services-dotnet-create-contentkey.md#common_contentkey)).

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor kapcsolatos tudnivalókat lásd: a [AssetDeliveryPolicy meghatározásakor típusok](#types) szakaszban.

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

Az Azure Media Services is lehetővé teszi, hogy adja hozzá a Widevine titkosítása. A következő példa bemutatja, mind a PlayReady, mind a Widevine ad hozzá az adategység továbbítási házirendjét.

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
> A Widevine titkosításakor csak tudná DASH segítségével. Győződjön meg arról, ha meg szeretné adni az objektumtovábbítási protokoll DASH.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption állít be objektumtovábbítási szabályzatot
A következő **CreateAssetDeliveryPolicy** metódus hoz létre a **AssetDeliveryPolicy** megfelelően van konfigurálva a alkalmazni dinamikus boríték-titkosítást (**DynamicEnvelopeEncryption**) Smooth Streaming, HLS és DASH protokollhoz (Ha úgy dönt, hogy adjon meg néhány protokoll, akkor a rendszer letiltja a streamelési). A módszer két paraméter szükséges: **Eszköz** (az eszköz szeretné a alkalmazni a kézbesítési szabályzat) és **IContentKey** (a tartalomkulcsot, a **EnvelopeEncryption** típus, további információkért lásd: [Tartalomkulcs létrehozása](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Milyen értékeket is megadhat egy AssetDeliveryPolicy létrehozásakor kapcsolatos tudnivalókat lásd: a [AssetDeliveryPolicy meghatározásakor típusok](#types) szakaszban.   

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

## <a id="types"></a>AssetDeliveryPolicy meghatározásakor típusok

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

A következő felsorolás ismerteti értékeket is megadhatja a kézbesítési szabályzat típusú eszközök esetén.  
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

A következő felsorolás ismerteti segítségével konfigurálja a kézbesítési módszert az ügyfél a tartalom kulcs értékeket.
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
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

A következő felsorolás ismerteti konfigurálása olvashatók be objektumtovábbítási szabályzat a konkrét konfigurációs kulcsokat és megadható értékeket.
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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

