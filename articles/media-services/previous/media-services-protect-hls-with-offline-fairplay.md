---
title: A HLS-tartalom védelme offline Apple FairPlay - Azure | Microsoft dokumentumok
description: Ez a témakör áttekintést ad, és bemutatja, hogyan használhatja az Azure Media Services segítségével a HTTP Live Streaming (HLS) tartalom dinamikus titkosítását az Apple FairPlay szolgáltatással offline módban.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 1644c00aea8eefa78550c8d0238dbedab0378492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968698"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay-streamelés iOS-hez 

> [!div class="op_single_selector" title1="Válassza ki a Media Services használt verzióját:"]
> * [3-as verzió](../latest/offline-fairplay-for-ios.md)
> * [2-es verzió](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Az Azure Media Services jól megtervezett [tartalomvédelmi szolgáltatásokat](https://azure.microsoft.com/services/media-services/content-protection/) kínál, amelyek a következőkre terjednek ki:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128-titkosítás

A digitális jogkezelés (DRM)/Advanced Encryption Standard (AES) titkosítása dinamikusan történik kérésre a különböző streamelési protokollok esetében. A DRM-licencet/Az AES visszafejtési kulcskézbesítési szolgáltatásokat a Media Services is biztosítja.

A különböző streamelési protokollokon keresztül iszomjú tartalmak védelme mellett a védett tartalmak offline módja is gyakran kért funkció. Offline módú támogatás szükséges a következő esetekben:

* Lejátszás, ha nem áll rendelkezésre internetkapcsolat, például utazás közben.
* Előfordulhat, hogy egyes tartalomszolgáltatók lehetővé teszik a DRM-licenc kézbesítését az ország/régió határain túl. Ha a felhasználók az országon/régión kívül rekednek, offline letöltésre van szükség.
* Egyes országokban/régiókban az internet elérhetősége és/vagy sávszélessége továbbra is korlátozott. A felhasználók először úgy dönthetnek, hogy letöltik a tartalmat olyan felbontásban, amely elég magas ahhoz, hogy kielégítő vizuális élményt nyújtson. Ebben az esetben a probléma általában nem a hálózat rendelkezésre állása, hanem a korlátozott hálózati sávszélesség. Over-the-top (OTT)/online video platform (OVP) szolgáltatók offline módban támogatást kérnek.

Ez a cikk a FairPlay Streaming (FPS) offline módú támogatásával foglalkozik, amely az iOS 10-es vagy újabb rendszert futtató eszközöket célozza meg. Ez a funkció más Apple-platformokon, például a watchOS, a tvOS vagy a Safari esetében nem támogatott macOS rendszeren.

## <a name="preliminary-steps"></a>Előzetes lépések
Mielőtt offline DRM-et valósítana meg a FairPlay hez iOS 10+ eszközön:

* Ismerkedjen meg a FairPlay online tartalomvédelmével. További információt az alábbi cikkekben és mintákban talál:

    - [Az Apple FairPlay Streaming for Azure Media Services általánosan elérhető](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [A HLS-tartalom védelme az Apple FairPlay vagy a Microsoft PlayReady segítségével](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Példa az online FPS streaming](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Szerezze be az FPS SDK-t az Apple Developer Network-től. Az FPS SDK két összetevőből áll:

    - Az FPS Server SDK, amely tartalmazza a kulcsbiztonsági modul (KSM), ügyfélminták, a specifikáció, és egy sor teszt vektorok.
    - Az FPS telepítési csomag, amely tartalmazza a D függvény specifikációját, valamint az FPS-tanúsítvány, az ügyfélspecifikus személyes kulcs és az alkalmazástitkos kulcs létrehozására vonatkozó utasításokat. Az Apple csak licencelt tartalomszolgáltatóknak bocsátja ki az FPS telepítési csomagot.

## <a name="configuration-in-media-services"></a>Konfiguráció a Media Services szolgáltatásban
FpS offline módú konfigurációa a [Media Services .NET SDK-n](https://www.nuget.org/packages/windowsazure.mediaservices)keresztül, használja a Media Services .NET SDK 4.0.0.4-es vagy újabb verzióját, amely biztosítja az FPS offline mód konfigurálásához szükséges API-t.
Az online módú FPS tartalomvédelem konfigurálásához is szüksége van a munkakódra. Miután megszerezte az FPS online módú tartalomvédelmének konfigurálásához szükséges kódot, csak a következő két módosításra van szükség.

## <a name="code-change-in-the-fairplay-configuration"></a>Kódváltozás a FairPlay konfigurációban
Az első változás az, hogy meghatározza a "offline mód engedélyezése" logikai, úgynevezett objDRMSettings.EnableOfflineMode, amely igaz, ha engedélyezi a kapcsolat nélküli DRM forgatókönyv. Ettől a mutatótól függően módosítsa a következőt a FairPlay konfiguráción:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Kódmódosítás az eszközkézbesítési házirend konfigurációjában
A második változás a harmadik kulcs hozzáadása a Dictionary<AssetDeliveryPolicyConfigurationKey karakterlánchoz,>.
AssetDeliveryPolicyConfigurationKey hozzáadása az itt látható módon:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Ezt a lépést követően az Dictionary_AssetDeliveryPolicyConfigurationKey FPS eszközkézbesítési házirendben> karakterlánc <a következő három bejegyzést tartalmazza:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl vagy AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, olyan tényezőktől függően, mint például az fps KSM/key server használt, és hogy ugyanazt az eszközkézbesítési házirendet több eszközön használja-e fel újra
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Media Services-fiókja most úgy van beállítva, hogy offline FairPlay licenceket biztosítson.

## <a name="sample-ios-player"></a>Példa iOS-lejátszóra
Az FPS offline módú támogatása csak az iOS 10-es és újabb verzióiban érhető el. Az FPS Server SDK (3.0-s vagy újabb verzió) tartalmazza a dokumentumot és a mintát az FPS offline módhoz. Pontosabban, FPS Server SDK (3.0-s vagy újabb verzió) tartalmazza a következő két elem kapcsolatos offline módban:

* Dokumentum: "Offline lejátszás FairPlay streameléssel és HTTP Live Streaming szolgáltatással." Apple, 2016. Az FPS Server SDK 4.0-s verziójában ez a dokumentum egyesül a fő FPS-dokumentummal.
* Mintakód: HLSCatalog minta az FPS offline módhoz a \FairPlay Streaming Server SDK 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ verziójában. A HLSCatalog mintaalkalmazásban a következő kódfájlok at használják az offline módú szolgáltatások megvalósításához:

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

![Offline FairPlay iOS Alkalmazásstreamek](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Használja saját tesztvideó-URL-jét, FairPlay licencbeszerzési URL-jét és alkalmazástanúsítvány-URL-jét, ha be van állítva. Vagy folytathatja a következő szakaszt, amely tesztmintákat tartalmaz.

## <a name="integrated-test"></a>Integrált vizsgálat
A Media Services három tesztmintaa a következő három forgatókönyvre terjed ki:

* FPS védett, video-, hang- és alternatív hangsávval
* FPS védett, a videó és audió, de nincs alternatív hangsáv
* FPS védett, csak videóval és hang nélkül

Ezeket a mintákat ezen a [bemutató helyen](https://aka.ms/poc#22)találja, a megfelelő alkalmazástanúsítványt egy Azure-webalkalmazásban üzemeltetve.
Az FPS Server SDK 3-as vagy 4-es verziójával, ha a fő lejátszási lista alternatív hangot tartalmaz, offline módban csak a hangot játssza le. Ezért meg kell szalag az alternatív hang. Más szóval, a második és a harmadik felsorolt minták korábban online és offline módban működnek. A felsorolt minta először csak offline módban játssza le a hangot, míg az online adatfolyam megfelelően működik.

## <a name="faq"></a>GYIK
A következő gyakori kérdések segítséget nyújtanak a hibaelhárításhoz:

- **Miért csak a hang játszható le, de a videó offline módban nem?** Ez a viselkedés úgy tűnik, hogy a mintaalkalmazás kialakítása. Ha offline módban egy másik hangsáv is jelen van (ez a HLS esetében is előfordul), akkor az iOS 10 és az iOS 11 alapértelmezés szerint az alternatív hangsávra van. Az FPS offline mód dalának kompenzálása érdekében távolítsa el az alternatív hangsávot az adatfolyamból. Ehhez a Media Services, adja hozzá a dinamikus jegyzékfájl szűrő "audio-only=false." Más szóval a HLS URL-címe .ism/manifest(format=m3u8-aapl,audio-only=false) végződik. 
- **Miért csak videó nélkül játssza le a hangot offline módban, miután hozzáadtam a csak hang=hamis hangot?** A tartalomkézbesítési hálózat (CDN) gyorsítótárkulcs-kialakításától függően előfordulhat, hogy a tartalom gyorsítótárba kerül. A gyorsítótár kiürítése.
- **Az FPS offline mód az iOS 11-en is támogatott az iOS 10 mellett?** Igen. Az FPS offline mód támogatott az iOS 10 és az iOS 11 rendszerben.
- **Miért nem találom az "Offline lejátszás FairPlay streameléssel és HTTP Live Streaming tel" dokumentumot az FPS Server SDK-ban?** Az FPS Server SDK 4-es verziója óta ez a dokumentum beolvadt a "FairPlay Streaming Programming Guide"-ba.
- **Mit jelent az utolsó paraméter az FPS offline üzemmódban a következő API-ban?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Az API dokumentációját a [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration metódus című](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet)témakörben találja. A paraméter az offline bérlés időtartamát jelöli, az óra pedig az egység.
- **Mi a letöltött/offline fájlstruktúra az iOS-eszközökön?** Az iOS-eszközön letöltött fájlstruktúra a következő képernyőképhez hasonlóan néz ki. A `_keys` mappa tárolja a letöltött FPS-licenceket, minden licencszolgáltatás-állomáshoz egy tárolófájltartozik. A `.movpkg` mappa hang- és videotartalmat tárol. Az első olyan mappa, amelynek neve kötőjellel végződik, majd numerikus, videotartalmat tartalmaz. A numerikus érték a videointerpretációk PeakBandwidth értéke. A második mappa, amelynek neve kötőjellel végződik, majd 0-val, hangtartalmat tartalmaz. A harmadik mappa neve "Adatok" tartalmazza a fő lejátszási listát az FPS tartalmat. Végül a boot.xml fájl teljes `.movpkg` körű leírást ad a mappa tartalmáról. 

![Offline FairPlay iOS mintaalkalmazásfájl-struktúra](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Mintaboot.xml fájl:
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

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="summary"></a>Összefoglalás
Ez a dokumentum a következő lépéseket és információkat tartalmazza, amelyek segítségével az FPS offline módban implementálható:

* A Media Services .NET API-n keresztüli Media Services tartalomvédelmi konfigurációja konfigurálja a dinamikus FairPlay titkosítást és a FairPlay licenckézbesítést a Media Services szolgáltatásban.
* Az FPS Server SDK mintáján alapuló iOS-lejátszó olyan iOS-lejátszót állít be, amely képes fps-tartalmaklejátszására online streamelési vagy offline módban.
* Minta FPS videók tesztelésére használják offline módban és az online streaming.
* A GYIK választ ad az FPS offline móddal kapcsolatos kérdésekre.

## <a name="next-steps"></a>További lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]