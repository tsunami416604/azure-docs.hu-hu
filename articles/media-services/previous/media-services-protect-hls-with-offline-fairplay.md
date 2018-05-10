---
title: HLS offline Apple FairPlay - Azure tartalom védelméhez |} Microsoft Docs
description: Ez a témakör áttekintést nyújt, és bemutatja, hogyan Azure Media Services segítségével dinamikusan titkosítani a HTTP-Live Streaming (HLS) az Apple FairPlay offline módban.
services: media-services
keywords: Kapcsolat nélküli, HLS, DRM, FairPlay Streaming (FPS), iOS 10
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
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: dc38772097dddb7c7135d55598373d7ab544f9ea
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="offline-fairplay-streaming-for-ios"></a>Kapcsolat nélküli FairPlay Streaming iOS-hez 
 Az Azure Media Services számos tetszetős [védelmi szolgáltatások tartalom](https://azure.microsoft.com/services/media-services/content-protection/) adott tartalma:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128 titkosítást

Digitális tartalomvédelmi (DRM) / tartalom Advanced Encryption Standard (AES) titkosítást dinamikusan történik a különböző adatfolyam-továbbítási protokollok kérésre. DRM licenc/AES visszafejtési kulcs licenctovábbítási szolgáltatások is által biztosított Media Services.

Amellett, hogy az online adatfolyamként történő keresztül különböző adatfolyam-továbbítási protokollok tartalomvédelemről, a védett tartalom kapcsolat nélküli módban az is gyakran kért szolgáltatása. Kapcsolat nélküli módban támogatja a következő forgatókönyvek esetén van szükség:

* A lejátszás során internetkapcsolat esetén nem érhető el, például utazás közben.
* Néhány tartalomszolgáltatók DRM licenc kézbesítési ország szegély túl lehet, hogy letiltása. Szeretnék tekintse meg a tartalom országán kívül utazás közben, kapcsolat nélküli letöltési van szükség.
* Egyes országokban internet rendelkezésre állási és/vagy a sávszélesség nem mindig korlátozott. Előfordulhat, hogy a felhasználók eldönthetik nézheti-felbontást eredményez, amely már elég magas kielégítő megtekintését a tartalom letöltéséhez először. Ebben az esetben a probléma általában nem hálózati rendelkezésre állását, de korlátozott hálózati sávszélesség. Over-az-felső (OTT) / online videó platform (OVP) szolgáltatók kapcsolat nélküli módban támogatási kérelem.

Ez a cikk a FairPlay Streaming (FPS) kapcsolat nélküli módban használatát, amelynek célpontja 10 vagy újabb iOS rendszerű eszközök vesszük alapul. Ez a szolgáltatás más Apple platformokon, például watchOS, tvOS vagy Safari macOS a nem támogatott.

## <a name="preliminary-steps"></a>Első lépések
10 + iOS-eszközön a FairPlay implementálásához offline DRM:

* Ismerkedjen meg FairPlay online tartalmak védelmét. További információkért tekintse meg a következő cikkek és minták:

    - [Általánosan elérhető az Azure Media Services Streaming FairPlay Apple](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [A tartalom Apple FairPlay vagy a Microsoft PlayReady HLS védelme](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Az online FPS adatfolyamként történő minta](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* A FPS SDK beszerzése az Apple Developer hálózatról. A FPS SDK két összetevőkből áll:

    - A FPS Server SDK csomagot, amely tartalmazza a kulcs biztonsági modul (KSM), a ügyfél minták, a specifikáció és a teszt vektorok készlete.
    - A FPS központi telepítési csomag, amely a D függvény-meghatározást tartalmaz, valamint leírja, hogyan a FPS tanúsítványt, a specifikus titkos kulcs és az alkalmazás titkos kulcs létrehozásához. Apple bocsát ki a FPS központi telepítési csomag csak a licencelt tartalomszolgáltatók.

## <a name="configuration-in-media-services"></a>A Media Services konfigurációs
FPS kapcsolat nélküli módban konfiguráció segítségével a [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices), a Media Services .NET SDK 4.0.0.4 verzióját használja, vagy később, amely biztosítja a szükséges API FPS offline mód konfigurálása.
Meg kell online módú FPS content protection konfigurálása a működő kódot is. Miután beszerezte a kód FPS online módú tartalmak védelmének konfigurálásához, csak a következő két módosításokat kell.

## <a name="code-change-in-the-fairplay-configuration"></a>FairPlay konfigurációjában kód módosítása
Az első módosítást, hogy adja meg az "enable kapcsolat nélküli módba" logikai érték, úgynevezett objDRMSettings.EnableOfflineMode, lehetővé teszi, hogy a kapcsolat nélküli DRM-forgatókönyv esetén true. Attól függően, hogy a mutató az alábbi módosítást kell FairPlay konfigurációját:

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

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Az eszköz továbbítási szabályzat konfigurációjához kód módosítása
A második módosításnak a célja a harmadik kulcs szótár < AssetDeliveryPolicyConfigurationKey, karakterlánc > vegyen fel.
Adja hozzá a AssetDeliveryPolicyConfigurationKey, ahogy az itt látható:
 
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

Elvégezte a lépést a < Dictionary_AssetDeliveryPolicyConfigurationKey > karakterláncot a FPS adategység továbbítási házirendjét a következő három bejegyzést tartalmaz:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl vagy AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, attól függően, hogy a FPS KSM/kiszolgáló használt tényezők és e használja fel az azonos eszköz kézbesítés több eszköz között házirend
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

A Media Services-fiók most már képes biztosítani a kapcsolat nélküli FairPlay-licenc van konfigurálva.

## <a name="sample-ios-player"></a>A minta iOS Player
A rendszer csak az iOS 10 és újabb verziói FPS kapcsolat nélküli módban támogatja. A FPS Server SDK (3.0-s vagy újabb verzió) tartalmaz a dokumentum és a minta FPS offline módban. FPS Server SDK (3.0-s vagy újabb verzió), a kapcsolat nélküli módban kapcsolatos következő két elemet tartalmazza:

* Dokumentum: "Offline lejátszás FairPlay adatfolyam- és HTTP élő adatfolyam-továbbítási." Apple, 2016 szeptemberétől 14. Ez a dokumentum FPS Server SDK 4.0-s verzióját, a fő FPS dokumentumhoz egyesített.
* Példakód: HLSCatalog mintát eredményez, amely a \FairPlay Streaming Server SDK verzió 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ FPS offline módban. A következő kód fájlok HLSCatalog mintaalkalmazás, kapcsolat nélküli módban funkciók végrehajtásához szerepelnek:

    - AssetPersistenceManager.swift kódfájl: AssetPersistenceManager a fő osztályban, ez a példa bemutatja, hogyan:

        - Kezelése letöltése HLS adatfolyamok, például az API-khoz használt indítása és szakítsa meg a letöltött fájl, és törölje a meglévő eszközök eszközök ki.
        - Letöltési előrehaladásának figyeléséhez.
    - A kód fájlok AssetListTableViewController.swift és AssetListTableViewCell.swift: AssetListTableViewController Ez a minta a fő felületet. A minta lejátszása, töltse le, törlése, vagy szakítsa meg a letöltési használható eszközök listája biztosít. 

Lépések bemutatják, hogyan állíthat be egy futó iOS player. Feltéve, hogy a HLSCatalog mintából FPS Server SDK verzió 4.0.1 indítása, a következő módosításokat kódot:

A HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, valósítja meg a `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` az alábbi kód használatával. A HLS URL-címhez hozzárendelt változó legyen "drmUr".

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

A HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, valósítja meg a `requestApplicationCertificate()`. Ez a megvalósítás attól függ, hogy a tanúsítvány (csak a nyilvános kulcs) beágyazása az eszköz vagy a tanúsítvány a webhely üzemeltetésére. Az alábbi a teszt mintában használt üzemeltetett alkalmazás tanúsítványt használ. Az alkalmazás tanúsítványának URL-CÍMÉT tartalmazó változó legyen "certUrl".

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

A végső integrált tesztjéhez, mind a Videó URL és a tanúsítvány URL-címe van a szakaszban megadott "Integrált Test."

HLSCatalog\Shared\Resources\Streams.plist adja hozzá a teszt Videó URL-CÍMÉT. A tartalom kulcs azonosítója, használja a FairPlay licenc licenckérési URL-cím a skd protokoll egyedi értékkel.

![Kapcsolat nélküli FairPlay iOS App adatfolyamok](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

A saját ellenőrző Videó URL-t, a FairPlay-licenc licenckérési URL-cím és a tanúsítvány URL-címe, használhat, ha azokat beállítása. Vagy továbbra is vizsgálati minták tartalmazza a következő szakaszban.

## <a name="integrated-test"></a>Integrált tesztelése
A Media Services vizsgálati mintát a következő három forgatókönyv terjed ki:

* FPS védi, video-, hang- és alternatív hang nyomon követése
* FPS védi, videó- és hang, de nincs másik hang nyomon követése
* Védett, csak a videó és nincsenek hang FPS

Ezeket a mintákat: található [bemutató webhely](http://aka.ms/poc#22), a megfelelő alkalmazás tanúsítvánnyal tárolt Azure-webalkalmazás.
A 3-as verziója vagy a FPS Server SDK minta 4-es verzióját Ha egy fő lista tartalmaz alternatív hang-, kapcsolat nélküli üzemmódban lejátszás hang csak. Ezért kell a másodlagos hang sáv. A második és harmadik minták felsorolt más szóval a korábban működnek online és offline módban. A felsorolt minta először játszik hang csak kapcsolat nélküli üzemmódban közben online streaming megfelelően működik.

## <a name="faq"></a>GYIK
A következő gyakori kérdések a hibaelhárítási segítséget nyújt:

- **Miért csak hang játssza le nem videó azonban offline üzemmódban?** Ez a viselkedés úgy tűnik, hogy úgy lett kialakítva, a mintaalkalmazást. Egy másik hang nyomon követése esetén (Ez az eset a HLS) jelenleg kapcsolat nélküli üzemmódban, iOS 10 és a másodlagos hang nyomon követendő iOS 11 alapértelmezett is. Ez a viselkedés kártalanítja FPS kapcsolat nélküli módban, távolítsa el a másodlagos hang nyomon követése az adatfolyamból. Ehhez a Media Services, a dinamikus manifest-szűrő hozzáadása "csak = false." Más szóval egy HLS URL-CÍMÉT .ism/manifest(format=m3u8-aapl,audio-only=false) végződik. 
- **Miért azt még játssza le a hang videó nélkül csak kapcsolat nélküli üzemmódban után csak hozzáadok = false?** Attól függően, hogy a tartalomkézbesítési hálózat (CDN) gyorsítótár tervezési előfordulhat, hogy azokat gyorsítótárazni a tartalmat. A gyorsítótár kiürítése.
- **FPS offline üzemmód is támogatott iOS 11 iOS 10 mellett?** Igen. Kapcsolat nélküli módban FPS iOS 10-es és iOS 11 esetén támogatott.
- **Miért nem található a FPS Server SDK a dokumentum "Offline lejátszás rendelkező FairPlay adatfolyam-és HTTP élő adatfolyam-továbbítási"?** FPS Server SDK 4-es verzióját, mert ez a dokumentum egyesítve volt a "FairPlay Streaming programozási útmutatója."
- **Mit nem az utolsó paraméter alakítson ki a következő API-t a kapcsolat nélküli módban FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Az API-JÁNAK dokumentációja, lásd: [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration metódus](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). A paraméter a kapcsolat nélküli bérleti óra a egységként időtartama jelöli.
- **Mi az az iOS-eszközökön a letöltött vagy offline állapotban fájlstruktúra?** A letöltött fájl struktúra iOS-eszközön az alábbi képernyőfelvételen tűnik. A `_keys` mappában tárolja letöltött FPS licencek, az egyes licenc szolgáltatásgazda egy tároló-fájllal. A `.movpkg` a mappa tárolja a hang-és videotartalmakhoz. Az első mappa egy numerikus követ kötőjellel végződő nevű videotartalom tartalmazza. A numerikus értéke a videó interpretációk PeakBandwidth. A második 0 követ kötőjellel végződő nevű mappa tartalmát tartalmazza. A harmadik "Data" nevű mappát a fő lista FPS tartalmat tartalmazza. Végezetül boot.xml teljes leírást ad a `.movpkg` mappa tartalmát. 

![Kapcsolat nélküli FairPlay iOS sample app fájlstruktúra](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Egy minta boot.xml fájlt:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
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

## <a name="summary"></a>Összegzés
Ez a dokumentum tartalmaz a következő lépéseket és információkat FPS kapcsolat nélküli módban végrehajtásához használhatja:

* A Media Services content védelmi konfigurációt a Media Services .NET API-n keresztül a dinamikus FairPlay titkosítás és a FairPlay-licenc kézbesítési a Media Services konfigurálása.
* Az iOS player az FPS Server SDK-ból a minta alapján állít be egy játszhatja FPS iOS player tartalom vagy online adatfolyam-továbbítási vagy kapcsolat nélküli üzemmódban.
* Mintavideók FPS kapcsolat nélküli módban, és online adatfolyamként történő továbbítását teszteléséhez vannak használva.
* A gyakran ismételt kérdések FPS kapcsolat nélküli módban kapcsolatos kérdésekre ad választ.
