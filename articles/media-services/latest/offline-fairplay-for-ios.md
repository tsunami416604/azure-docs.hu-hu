---
title: HLS-tartalmak az offline Apple fairplay által – Azure védelme |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt, és bemutatja, hogyan használhatja az Azure Media Services dinamikusan titkosítani a HTTP Live Streaming (HLS) az Apple fairplay által a kapcsolat nélküli módban.
services: media-services
keywords: Offline állapotban van, HLS és DRM, FairPlay Streaming (FPS), iOS 10-es
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 25bc7798853d350139a7802eaad68d52a1d7d99f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834812"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay Streaming iOS-hez 

 Az Azure Media Services jól megtervezett készletét nyújtja [content protection szolgáltatás](https://azure.microsoft.com/services/media-services/content-protection/) pontosan rögzítik:

- A Microsoft PlayReady
- A Google Widevine
- Az Apple FairPlay
- AES-128 titkosítást

Digitális jogkezelési (technológia DRM) / tartalom Advanced Encryption Standard (AES) titkosítást dinamikusan történik a különböző adatfolyam-továbbítási protokollok kérésre. DRM-licenckiszolgáló/AES visszafejtési kulcskézbesítési szolgáltatások is által biztosított Media Services.

Amellett, hogy online folyamatos átvitelre különböző adatfolyam-továbbítási protokollok tartalmának védelme, kapcsolat nélküli módban, a védett tartalomhoz az is gyakran által kért szolgáltatása. Offline mód támogatása a következő esetekben van szükség:

* Lejátszás, amikor az internetes kapcsolat nem érhető el, mint például utazás közben.
* Néhány tartalomszolgáltatók előfordulhat, hogy ne engedélyezze a DRM-licenckézbesítés túl egy ország szegély. Ha felhasználókat szeretne tekintse meg a tartalom országán kívül utazás közben, az offline letöltési van szükség.
* Egyes országokban internet rendelkezésre állási és/vagy a sávszélesség korlátozva. Előfordulhat, hogy a felhasználók megadhatják tudni tekintse meg a megoldást, amely elég nagy a megfelelő élményt a tartalom letöltéséhez először. Ebben az esetben a probléma általában nem hálózati rendelkezésre állását, de korlátozott a sávszélesség. Over-the-top (OTT) / online videoplatformok (OVP) szolgáltatók kapcsolat nélküli módban támogatást kérhet.

Ez a cikk ismerteti az iOS 10 vagy újabb rendszerű eszközök célzó FairPlay Streaming (FPS) kapcsolat nélküli módban támogatása. Ez a szolgáltatás egyéb Apple-platformoknál, például a watchOS, tvOS vagy Safari macOS rendszeren nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Az iOS 10-es és újabb eszközön a fairplay rendszerhez megvalósítása offline DRM: előtt

* Tekintse át az online tartalom védelmét a fairplay rendszerhez: 

    - [Apple FairPlay-licenc követelményeit és konfigurációját](fairplay-license-overview.md)
    - [DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata](protect-with-drm.md)
    - Egy .NET-minta, amely tartalmazza az online FPS streamelési konfiguráció: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Szerezze be a FPS SDK-t az Apple Developer Network. A FPS SDK két összetevőkből áll:

    - A FPS Server SDK-t a biztonsági modul (KSM), ügyfél-minták, specifikáció és tesztelési vektorok készletét tartalmazza.
    - A FPS központi telepítési csomagot, amely tartalmazza a D függvény specifikációját, és útmutatást a FPS tanúsítványt, a specifikus titkos kulcs és az alkalmazás titkos kulcsát. Az Apple a FPS központi telepítési csomag csak a licencelt tartalomszolgáltatók kapcsolatos problémák.
* Klónozás https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Módosítsa a kódot kell [titkosítása a .NET használatával DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) FairPlay-konfiguráció hozzáadásához.  

## <a name="configure-content-protection-in-azure-media-services"></a>A tartalomvédelem konfigurálása az Azure Media Servicesben

Az a [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metódus, tegye a következőket:

Állítsa vissza a kódot, amely a FairPlay a házirend-beállítást konfigurálja:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Ezenkívül állítsa vissza a kódot, amely hozzáadja CBCS ContentKeyPolicyOption ContentKeyPolicyOptions listája

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Offline üzemmód engedélyezése

Offline üzemmód engedélyezése, hozzon létre egy egyéni StreamingPolicy és a egy StreamingLocator létrehozásakor a változó nevét használja [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Most már a Media Services-fiók van konfigurálva, hogy az offline FairPlay-licenc.

## <a name="sample-ios-player"></a>Példa iOS-lejátszó

A rendszer csak az iOS 10-es és újabb verziói FPS kapcsolat nélküli módban támogatja. Dokumentum és mintát FPS kapcsolat nélküli módban, a Képkocka kiszolgálói SDK (3.0-s vagy újabb verzió) tartalmazza. FPS kiszolgálói SDK (3.0-s vagy újabb verzió) kifejezetten, a kapcsolódó kapcsolat nélküli módban a következő két elemet tartalmazza:

* Dokumentum tartalma: "Offline FairPlay Streaming lejátszás és a HTTP Live Streaming." Apple-től 2016. szeptember 14-én. FPS Server SDK 4.0-s verziója Ez a dokumentum egyesítve a fő FPS dokumentumot.
* Kódminta: HLSCatalog minta (az Apple FPS Server SDK része) a \FairPlay Streamelési Server SDK verziója 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ FPS offline mód. A HLSCatalog mintaalkalmazás kapcsolat nélküli módban funkciók megvalósításához használja a következő kód fájlokat:

    - A kódfájl AssetPersistenceManager.swift: AssetPersistenceManager Ez a minta azt mutatja be, a fő osztály hogyan:

        - Letöltése HLS-adatfolyamok, kezelése, például az API-k használt elindításához és a letöltéseket és eszközök ki meglévő eszközök törlése.
        - A letöltési előrehaladásának figyeléséhez.
    - AssetListTableViewController.swift és AssetListTableViewCell.swift kódfájlok: AssetListTableViewController Ez a minta fő felületet. Ez a minta lejátszása, letöltése, törlése vagy a letöltés megszakítása használható eszközök listáját tartalmazza. 

Ezek a lépések bemutatják, hogyan állítható be a futó iOS-lejátszó. Ha FPS Server SDK verziója 4.0.1 HLSCatalog mintából elkezdené, győződjön meg a következő kód módosítása:

A metódus megvalósításához HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` az alábbi kód használatával. Legyen "drmUr" egy változót, a HLS URL-cím rendelve.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

A metódus megvalósításához HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, `requestApplicationCertificate()`. Ez a megvalósítás attól függ, hogy az eszköz a tanúsítvány (csak a nyilvános kulcs) beágyazása, vagy a tanúsítványt a weben üzemeltetéséhez. Az alábbi megvalósításra szerepel a vizsgálati minták üzemeltetett alkalmazás tanúsítványt használja. Egy változó, amely tartalmazza az alkalmazás tanúsítványának URL-címe legyen "certUrl".

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

A végső integrált teszthez mind a Videó URL-címe, és a tanúsítvány URL-címe tartalmazza a szakasz "Integrált Test."

HLSCatalog\Shared\Resources\Streams.plist vegye fel a vizsgált Videó URL-CÍMÉT. A tartalom kulcs azonosítója, az skd protokoll egyedi értéket a FairPlay licenc licenckérési URL-cím használata.

![Offline FairPlay iOS alkalmazás-adatfolyamok](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Használja a saját tesztelési Videó URL-címe, FairPlay-licenc licenckérési URL-cím és alkalmazás tanúsítvány URL-címe, ha rendelkezik a szükséges beállítása. Vagy a következő szakaszban, teszt mintákat tartalmazó továbbra is.

## <a name="integrated-test"></a>Integrált teszt

A Media Services három teszt mintát a következő három forgatókönyv terjed ki:

* Védett, videó, hang és alternatív hangsávra FPS
* Védett, videó és hang, de nincs másik hangsávot FPS
* Védett, csak a videó és hang nélkül FPS

Ezeket a mintákat, annak [ez – bemutató webhely](https://aka.ms/poc#22), az Azure web app szolgáltatásban üzemeltetett a megfelelő alkalmazás-tanúsítvánnyal.
A 3-as verziója vagy a 4. verziójú minta FPS Server SDK fő lejátszási lista tartalmazza az alternatív hang-, ha a kapcsolat nélküli üzemmódban lejátszás hang csak. Ezért kell eltávolítania az alternatív hang. A második és harmadik minták felsorolt más szóval a korábban működnek online és offline módban. A felsorolt mintát először tölt hang csak kapcsolat nélküli üzemmódban során közben online streamelés megfelelően működik.

## <a name="faq"></a>GYIK

A következő gyakori kérdések hibaelhárítási segítséget nyújtanak:

- **Miért csak hang játssza le a nem videó azonban offline üzemmódban?** Ez a viselkedés úgy tűnik, hogy a mintaalkalmazás az elvárt. Ha egy másik hangsávot iOS 10-es, mind a másodlagos hangsávot iOS 11-et az alapértelmezett kapcsolat nélküli üzemmódban (Ez az eset a HLS Protokollhoz) található. Ez a viselkedés kompenzálják FPS kapcsolat nélküli módban, távolítsa el a másodlagos hangsávot az adatfolyamból. A Media Services ehhez a dinamikusjegyzék-szűrő hozzáadása "csak = false." Más szóval a HLS URL-címet a .ism/manifest(format=m3u8-aapl,audio-only=false) végződik. 
- **Miért, továbbra is játssza le a hang, videó nélkül csak kapcsolat nélküli üzemmódban után adhatok hozzá a csak hangfájlt tartalmazó = false?** Attól függően, a tartalomkézbesítési hálózat (CDN) gyorsítótár fő tervezési a tartalom előfordulhat, hogy gyorsítótárazza. A gyorsítótár kiürítése.
- **FPS offline üzemmód is támogatott az iOS 11 iOS 10-es mellett?** Igen. Az iOS 10-es és az iOS 11-et FPS kapcsolat nélküli módban támogatott.
- **Miért nem találom a dokumentum "Offline lejátszási a FairPlay Streaming és HTTP Live Streaming" FPS Server SDK-ban?** FPS Server SDK 4-es verzió, mivel ez a dokumentum egyesítve volt a "FairPlay Streaming programozási útmutatója."
- **Mi az a letöltött/offline fájlszerkezet az iOS-eszközökön?** IOS-eszközön a letöltött fájl struktúra a következő képernyőképhez hasonlóan néz ki. A `_keys` a mappa tárolja FPS licencek a letöltött minden licenc szolgáltatáshoz állomás egy tároló-fájllal. A `.movpkg` a mappa tárolja az audio- és videotartalmakhoz. Az első mappát, amelynek neve követ egy numerikus kötőjellel végződik videótartalmak tartalmazza. A numerikus érték a PeakBandwidth, a videó beállításkészletben. A második mappát, amelynek neve követ 0 kötőjellel végződik hanganyaga tartalmazza. A harmadik, "Data" nevű mappát tartalmaz a fő lista FPS tartalmat. Végül boot.xml nyújt a teljes leírását a `.movpkg` mappa tartalmát. 

![Offline FairPlay iOS minta app fájlstruktúra](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Boot.xml mintafájl:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>További lépések

Az [AES-128 használatával történő védelem](protect-with-aes128.md) ismertetése
