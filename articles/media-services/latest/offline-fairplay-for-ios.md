---
title: Offline FairPlay streaming iOS-hez az Azure Media Services v3-as
description: Ez a témakör áttekintést ad, és bemutatja, hogyan használhatja az Azure Media Services segítségével a HTTP Live Streaming (HLS) tartalom dinamikus titkosítását az Apple FairPlay szolgáltatással offline módban.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
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
ms.openlocfilehash: 41893c2460ecb2d17e3893f867bc460105d57bbd
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887214"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline FairPlay streaming iOS-hez a Media Services v3-as

 Az Azure Media Services jól megtervezett [tartalomvédelmi szolgáltatásokat](https://azure.microsoft.com/services/media-services/content-protection/) kínál, amelyek a következőkre terjednek ki:

- Microsoft PlayReady
- Google Widevine
    
    A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.
- Apple FairPlay
- AES-128-titkosítás

A digitális jogkezelés (DRM)/Advanced Encryption Standard (AES) titkosítása dinamikusan történik kérésre a különböző streamelési protokollok esetében. A DRM-licencet/Az AES visszafejtési kulcskézbesítési szolgáltatásokat a Media Services is biztosítja.

A különböző streamelési protokollokon keresztül iszomjú tartalmak védelme mellett a védett tartalmak offline módja is gyakran kért funkció. Offline módú támogatás szükséges a következő esetekben:

* Lejátszás, ha nem áll rendelkezésre internetkapcsolat, például utazás közben.
* Előfordulhat, hogy egyes tartalomszolgáltatók lehetővé teszik a DRM-licenc kézbesítését az ország/régió határain túl. Ha a felhasználók az országon/régión kívül rekednek, offline letöltésre van szükség.
* Egyes országokban/régiókban az internet elérhetősége és/vagy sávszélessége továbbra is korlátozott. A felhasználók először úgy dönthetnek, hogy letöltik a tartalmat olyan felbontásban, amely elég magas ahhoz, hogy kielégítő vizuális élményt nyújtson. Ebben az esetben a probléma általában nem a hálózat rendelkezésre állása, hanem a korlátozott hálózati sávszélesség. Over-the-top (OTT)/online video platform (OVP) szolgáltatók offline módban támogatást kérnek.

Ez a cikk a FairPlay Streaming (FPS) offline módú támogatásával foglalkozik, amely az iOS 10-es vagy újabb rendszert futtató eszközöket célozza meg. Ez a funkció más Apple-platformokon, például a watchOS, a tvOS vagy a Safari esetében nem támogatott macOS rendszeren.

> [!NOTE]
> Az offline DRM-nek csak a tartalom letöltésekor kell fizetnie egyetlen licenckérelemért. A hibákat a rendszer nem számlázja.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt offline DRM-et valósítana meg a FairPlay hez iOS 10+ eszközön:

* Tekintse át az online tartalomvédelmet a FairPlay-hez: 

    - [Apple FairPlay-licencek követelményei és konfigurálása](fairplay-license-overview.md)
    - [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](protect-with-drm.md)
    - Az online FPS-adatfolyam konfigurációját tartalmazó .NET-minta: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Szerezze be az FPS SDK-t az Apple Developer Network-től. Az FPS SDK két összetevőből áll:

    - Az FPS Server SDK, amely tartalmazza a kulcsbiztonsági modul (KSM), ügyfélminták, a specifikáció, és egy sor teszt vektorok.
    - Az FPS telepítési csomag, amely tartalmazza a D függvény specifikációját, valamint az FPS-tanúsítvány, az ügyfélspecifikus személyes kulcs és az alkalmazástitkos kulcs létrehozására vonatkozó utasításokat. Az Apple csak licencelt tartalomszolgáltatóknak bocsátja ki az FPS telepítési csomagot.
* Klón https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    A FairPlay-konfigurációk hozzáadásához módosítania kell a kódot a [DRM titkosítása a DRM használatával a .NET használatával.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)  

## <a name="configure-content-protection-in-azure-media-services"></a>Tartalomvédelem konfigurálása az Azure Media Servicesszolgáltatásban

A [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metódusban tegye a következőket:

A FairPlay házirendbeállítását beállító kód megjegyzésének kiválasztása:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Is, uncomment a kódot, amely hozzáadja CBCS ContentKeyPolicyOption a listához ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Kapcsolat nélküli mód engedélyezése

Az offline mód engedélyezéséhez hozzon létre egy egyéni StreamingPolicy-et, és használja a nevét a StreamingLocator létrehozásakor [a CreateStreamingLocatorAsync alkalmazásban.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563)
 
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

Media Services-fiókja most úgy van beállítva, hogy offline FairPlay licenceket biztosítson.

## <a name="sample-ios-player"></a>Példa iOS-lejátszóra

Az FPS offline módú támogatása csak az iOS 10-es és újabb verzióiban érhető el. Az FPS Server SDK (3.0-s vagy újabb verzió) tartalmazza a dokumentumot és a mintát az FPS offline módhoz. Pontosabban, FPS Server SDK (3.0-s vagy újabb verzió) tartalmazza a következő két elem kapcsolatos offline módban:

* Dokumentum: "Offline lejátszás FairPlay streameléssel és HTTP Live Streaming szolgáltatással." Apple, 2016. Az FPS Server SDK 4.0-s verziójában ez a dokumentum egyesül a fő FPS-dokumentummal.
* Mintakód: HLSCatalog minta (az Apple FPS Server SDK része) az FPS offline üzemmódhoz a \FairPlay Streaming Server SDK 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ módban. A HLSCatalog mintaalkalmazásban a következő kódfájlok at használják az offline módú szolgáltatások megvalósításához:

    - AssetPersistenceManager.swift kódfájl: AssetPersistenceManager a fő osztály ebben a mintában, amely bemutatja, hogyan:

        - Kezelheti a HLS-adatfolyamok letöltését, például a letöltések indításához és megszakításához használt API-kat, valamint a meglévő eszközök eszközről való törlését.
        - A letöltés immára.
    - AssetListTableViewController.swift és AssetListTableViewCell.swift kódfájlok: AssetListTableViewController a fő felülete ennek a mintának. A minta által a letöltés lejátszásához, letöltéséhez, törléséhez vagy megszakításához használható eszközök listáját tartalmazza. 

Ezek a lépések bemutatják, hogyan állíthat be egy futó iOS-lejátszót. Feltéve, hogy az FPS Server SDK 4.0.1-es verziójában a HLSCatalog mintából indul, hajtsa végre a következő kódmódosításokat:

A HLSCatalog\Shared\Managers\ContentKeyDelegate.swift mappában `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` valósítsa meg a metódust a következő kód használatával. Legyen "drmUr" egy változó rendelt hls URL-t.

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

A HLSCatalog\Shared\Managers\ContentKeyDelegate.swift mappában `requestApplicationCertificate()`hajtsa végre a módszert. Ez a megvalósítás attól függ, hogy a tanúsítványt (csak nyilvános kulcsot) ágyazza-e be az eszközbe, vagy a weben üzemelteti-e a tanúsítványt. A következő implementáció a tesztmintákban használt üzemeltetett alkalmazástanúsítványt használja. Legyen a "certUrl" egy változó, amely tartalmazza az alkalmazástanúsítvány URL-címét.

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

A végső integrált teszthez mind a videó URL-címe, mind az alkalmazástanúsítvány URL-címe az "Integrált teszt" szakaszban található.

A HLSCatalog\Shared\Resources\Streams.plist mappában adja hozzá a tesztvideó URL-címét. A tartalomkulcs-azonosítóhoz használja a FairPlay licencbeszerzési URL-t, amelynek egyedi értéke az skd protokoll.

![Offline FairPlay iOS Alkalmazásstreamek](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Használja saját tesztvideó-URL-jét, FairPlay licencbeszerzési URL-jét és alkalmazástanúsítvány-URL-jét, ha be van állítva. Vagy folytathatja a következő szakaszt, amely tesztmintákat tartalmaz.

## <a name="integrated-test"></a>Integrált vizsgálat

A Media Services három tesztmintaa a következő három forgatókönyvre terjed ki:

* FPS védett, video-, hang- és alternatív hangsávval
* FPS védett, a videó és audió, de nincs alternatív hangsáv
* FPS védett, csak videóval és hang nélkül

Ezeket a mintákat ezen a [bemutató helyen](https://aka.ms/poc#22)találja, a megfelelő alkalmazástanúsítványt egy Azure-webalkalmazásban üzemeltetve.
Az FPS Server SDK 3-as vagy 4-es verziójával, ha a fő lejátszási lista alternatív hangot tartalmaz, offline módban csak a hangot játssza le. Ezért meg kell szalag az alternatív hang. Más szóval, a második és a harmadik felsorolt minták korábban online és offline módban működnek. A felsorolt minta először csak offline módban játssza le a hangot, míg az online adatfolyam megfelelően működik.

## <a name="faq"></a>GYIK

Tekintse meg [a gyakori kérdéseket, amelyek segítséget nyújtanak a hibaelhárításhoz.](frequently-asked-questions.md#why-does-only-audio-play-but-not-video-during-offline-mode)

## <a name="next-steps"></a>További lépések

Az [AES-128 használatával történő védelem](protect-with-aes128.md) ismertetése
