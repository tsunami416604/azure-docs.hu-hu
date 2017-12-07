---
title: "HLS offline Apple FairPlay - Azure tartalom védelméhez |} Microsoft Docs"
description: "Ez a témakör áttekintést nyújt, és bemutatja, hogyan Azure Media Services segítségével dinamikusan titkosítani a HTTP-Live Streaming (HLS) az Apple FairPlay offline módban."
services: media-services
keywords: "Kapcsolat nélküli, HLS, DRM, FairPlay Streaming (FPS), iOS 10"
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: bf5828ecd6b6bd2e862c4d7709014ecac47c6be0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="offline-fairplay-streaming"></a>Adatfolyam-kapcsolat nélküli FairPlay
A Microsoft Azure Media Services számos tetszetős [védelmi szolgáltatások tartalom](https://azure.microsoft.com/services/media-services/content-protection/), amely:
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128 titkosítást

Tartalom DRM/AES titkosítási dinamikusan történik a különböző adatfolyam-továbbítási protokollok kérésre. DRM licenc/AES visszafejtési kulcs licenctovábbítási szolgáltatások is Azure Media Services által biztosított.

Amellett, hogy az online adatfolyamként történő keresztül különböző adatfolyam-továbbítási protokollok tartalomvédelemről, a védett tartalom kapcsolat nélküli módban az is gyakran kért szolgáltatása. Kapcsolat nélküli módban támogatja a következő forgatókönyvek esetén van szükség:
1. A lejátszás Internet kapcsolat esetén nem érhető el, például közben;
2. Néhány tartalomszolgáltatók DRM licenc kézbesítési ország szegély túl lehet letiltása. Ha egy felhasználó kíván tartalom megtekintése a külföldön utazás közben, kapcsolat nélküli letöltési van szükség.
3. Egyes országokban Internet rendelkezésre állási és/vagy a sávszélesség nem mindig korlátozott. Előfordulhat, hogy a felhasználók eldönthetik nézheti felbontása kielégítő megtekintését a tartalom letöltéséhez először. Ebben az esetben gyakrabban, a probléma nem a hálózat rendelkezésre állásának, ahelyett, hogy korlátozott hálózati sávszélesség. OTT/OVP szolgáltatók arra utasítja a kapcsolat nélküli módban támogatásához.

Ez a cikk FairPlay Streaming (FPS) kapcsolat nélküli módban használatát, 10 vagy újabb iOS rendszerű eszközök célzó vesszük alapul. Ez a funkció nem támogatott macOS más Apple platformok például watchOS, tvOS vagy Safari.

## <a name="preliminary-steps"></a>Első lépések
Kapcsolat nélküli DRM megvalósításához FairPlay 10 + iOS-eszközön, mielőtt első kell:
1. Ismerkedjen meg FairPlay online tartalmak védelmét. Ez részletesen is ismertetjük a következő cikkek minták:
- [Azure Media Services általánosan elérhető Streaming FairPlay Apple](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
- [A tartalom Apple FairPlay vagy a Microsoft PlayReady HLS védelme](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [Az online FPS adatfolyamként történő minta](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)
2. Szerezze be a FPS SDK Apple Developer hálózatról. FPS SDK két összetevőkből áll:
- FPS Server SDK-t, amely tartalmaz KSM (kulcs biztonsági modul), a ügyfél minták, a specifikáció és a teszt veszélyének; készlete
- FPS központi telepítési csomag, amely a D függvény, valamint leírja, hogyan a FPS tanúsítványt, a specifikus titkos kulcs és az alkalmazás titkos kulcs (KÉRJEN) specifikáció tartalmazza. Apple bocsát ki FPS központi telepítési csomag csak a licencelt tartalomszolgáltatók.

## <a name="configuration-in-azure-media-services"></a>Az Azure Media Services konfigurációs
FPS kapcsolat nélküli módban konfiguráció keresztül [Azure Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices), kell használnia az Azure Media Services .NET SDK-v. 4.0.0.4, vagy később, amely biztosítja, hogy FPS kapcsolat nélküli módban konfigurálja a szükséges API-t.
A fenti előfeltételek jelöltük feltételezzük, hogy van működő kódjának online módban FPS tartalomvédelem konfigurálásáról. Miután a tartalom védelmének online módban FPS beállítása kódját, csak akkor kell a következő két módosításokat.

## <a name="code-change-in-fairplay-configuration"></a>FairPlay konfigurációban kód módosítása
Is határozza meg az IGAZ, ha engedélyezve van a kapcsolat nélküli DRM-forgatókönyv egy "engedélyezése kapcsolat nélküli módba" logikai, hívott objDRMSettings.EnableOfflineMode. Attól függően, hogy ez a jelző azt az alábbi módosítást a FairPlay konfiguráció:

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

## <a name="code-change-in-asset-delivery-policy-configuration"></a>Az eszköz továbbítási szabályzat konfigurációjához kód módosítása
A második módosításnak a célja a harmadik kulcs a szótárban szótár < AssetDeliveryPolicyConfigurationKey, karakterlánc > vegyen fel.
A harmadik AssetDeliveryPolicyConfigurationKey hozzá kell adni az alábbiak szerint: 
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

Ez a lépés után a szótár < AssetDeliveryPolicyConfigurationKey, karakterlánc > FPS objektumtovábbítási szabályzat az alábbi három bejegyzéseket fogja tartalmazni:
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl vagy AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl használt FPS KSM/kulcs kiszolgáló, és hogy szeretnénk az azonos eszköz kézbesítési újból például tényezőktől függően Több eszköz között házirend
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

A media services-fiókkal van konfigurálva offline FairPlay-licenc-i.

## <a name="sample-ios-player"></a>A minta iOS Player
Először azt kell vegye figyelembe, hogy FPS offline mód támogatása csak az iOS 10 és újabb verziói. Igazolnia kell kapnia FPS Server SDK (3.0-s verzió vagy újabb) tartalmazó dokumentumok és minta FPS offline módban. Pontosabban, a FPS Server SDK (3.0-s verzió vagy újabb) kapcsolat nélküli üzemmódban kapcsolódik a következő két elemet tartalmaz:
1. A dokumentum: Kapcsolat nélküli lejátszás FairPlay adatfolyam- és HTTP élő adatfolyam. Apple, 9/14/2016. A FPS Server SDK v 4.0-s verzióját a dokumentum a fő FPS adatfolyam doc egyesített.
2. Példakód: kapcsolat nélküli módban FPS \FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ HLSCatalog minta. A HLSCatalog mintaalkalmazást a következő kód fájlok különösen a kapcsolat nélküli módban szolgáltatások:
- AssetPersistenceManager.swift kódfájl: AssetPersistenceManager Ez a példa azt mutatja be, a fő osztály
    - Letöltése HLS-adatfolyamok, például az API-k és letöltés megszakítása törlése a felhasználó eszközén; ki meglévő eszközök kezelése
    - Útmutató letöltési előrehaladásának figyeléséhez.
- A kód fájlok AssetListTableViewController.swift és AssetListTableViewCell.swift: AssetListTableViewController Ez a minta a fő felületet. A minta lejátszása, töltse le, törölheti, vagy szakítsa meg a letöltési eszközök listájának biztosít. 

Az alábbiakban egy futó iOS player beállításával kapcsolatos részletes lépéseket. Tegyük fel, a HLSCatalog mintából FPS Server SDK v 4.0.1 megkezdése.  A következő kód módosításokat kell:

A HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, valósítja meg a `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` a következő kóddal: legyen drmUr egy változóhoz, a HLS-streamelési URL-címet hozzárendelni.

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

A HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, valósítja meg a `requestApplicationCertificate()`. Ez a megvalósítás attól függ, hogy a tanúsítvány (csak a nyilvános kulcs) beágyazása az eszköz vagy a tanúsítvány a webhely üzemeltetésére. Alább az üzemeltetett alkalmazás tanúsítvánnyal, a vizsgálati minták használt megvalósítása. Az alkalmazás tanúsítványának URL-CÍMÉT tartalmazó változó lehet certUrl segítségével.

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

A végső integrált teszteléséhez Videó URL és a tanúsítvány URL-címe fognak adni a "Integrált teszt" szakaszában.

HLSCatalog\Shared\Resources\Streams.plist, adja hozzá a teszt Videó URL és a tartalomkulcs-azonosító használjuk FairPlay-licenc licenckérési URL-cím skd protokoll egyedi értékkel.

![Kapcsolat nélküli FairPlay iOS App adatfolyamok](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

A teszt Videó URL, a FairPlay-licenc licenckérési URL-cím és a tanúsítvány URL-címe használhatja a saját, ha őket beállítani, vagy továbbra is a következő szakaszban vizsgálati minták tartalmazó.

## <a name="integrated-test"></a>Integrált tesztelése
Azure Media Services, amely a következő három forgatókönyvekhez három vizsgálati minták hoztak létre:
1.  FPS védi, video-, hang- és alternatív hang követése;
2.  Védett, videót, hangot, de nincs másik hang követése; FPS
3.  FPS védelme, a videó csak, hang nélkül.

Ezeket a mintákat található ezzel [bemutató hely](http://aka.ms/poc#22), a megfelelő tanúsítvánnyal alkalmazás Azure-webalkalmazás tárolva.
A Microsoft észrevette, hogy FPS Server SDK v3 vagy v4 példával, ha egy fő lista tartalmaz alternatív hang-, kapcsolat nélküli üzemmódban lejátszás hang csak. Ezért igazolnia kell a másodlagos hang sáv. Más szóval a három minta fent, (2) és (3) között működik online és offline módban. De hangfájl lejátszása közben online adatfolyam működő kapcsolat nélküli módban során csak jól (1) lesz.

## <a name="faq"></a>GYIK
Néhány gyakori kérdést talál a hibaelhárítási:
- **Miért nem csak hang play, de nincs kép offline üzemmódban?** Ez a viselkedés úgy tűnik, hogy úgy lett kialakítva, a mintaalkalmazást. Ha másik hang nyomon követése jelentenek a kapcsolat nélküli módban, mindkét iOS 10 során (amely a helyzet HLS), és iOS 11 rendszer alternatív hang nyomon követése az alapértelmezett. Ez a viselkedés kártalanítja FPS kapcsolat nélküli módban, távolítsa el a másodlagos hang nyomon követése az adatfolyamból kell. Ehhez az Azure Media Services ügyféloldali is egyszerűen hozzáadjuk a dinamikus jegyzék szűrő "csak = false." Ez azt jelenti a HLS URL-címének .ism/manifest(format=m3u8-aapl,audio-only=false) multiplicitású lenne. 
- **Miért azt még játssza le a hang videó nélkül csak kapcsolat nélküli üzemmódban után csak hozzáadott = false?** Attól függően, hogy a CDN gyorsítótár tervezési a tartalom azokat gyorsítótárazni. A gyorsítótár kiürítése kell.
- **FPS offline üzemmód is támogatott iOS 11 iOS 10 mellett?** Igen, FPS offline mód iOS 10-es és iOS 11 is támogatott.
- **Miért nem található FPS Server SDK a dokumentum Offline FairPlay adatfolyam- és HTTP Live Streaming lejátszás?** FPS Server SDK v 4, mert ez a dokumentum egyesített FairPlay Streaming programozási útmutató dokumentumot.
- **Mit nem az utolsó paraméter alakítson ki a következő API-t a kapcsolat nélküli módban FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

Ez az API dokumentációjában található [Itt](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). A paraméter jelöli a egységként óra offline bérleti időtartama.
- **Mi az az iOS-eszközökön a letöltött vagy offline állapotban fájlstruktúra?** A letöltött fájl struktúra iOS-eszközön a következőképpen néz (képernyőkép) alatt. `_keys`mappában tárolja letöltött FPS licencek egy tárolófájl minden licenc szolgáltatás állomás számára. `.movpkg`a mappa tárolja a hang-és videotartalmakhoz. Az első mappa egy numerikus követ kötőjellel végződő nevű videotartalom tartalmazza. A numerikus értéke a videó interpretációk "PeakBandwidth". 0 követ kötőjellel végződő nevű második mappa tartalmát tartalmazza. A harmadik "Data" nevű mappát a fő lista FPS tartalmat tartalmazza. Boot.XML teljes leírását itt `.movpkg` mappa tartalma (lásd lent boot.xml mintafájl).

![Kapcsolat nélküli FairPlay iOS Sample App fájlstruktúra](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

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

## <a name="summary"></a>Összefoglalás
Ebben a dokumentumban adtunk a részletes lépéseket és az adatok megvalósításának FPS kapcsolat nélküli módban, beleértve:
1. Az Azure Media Services tartalomvédelem konfigurációs AMS .NET API-n keresztül esetén. Ezzel a dinamikus FairPlay titkosítási és FairPlay-licenc kézbesítési AMS.
2. iOS player az Apple FPS Server SDK minta alapján. Ez lenne állítson be egy játszhatja FPS iOS player tartalom vagy online adatfolyam-továbbítási vagy kapcsolat nélküli üzemmódban.
3. FPS Mintavideók kapcsolat nélküli módban és online streaming teszteléséhez.
4. Gyakori kérdések FPS kapcsolat nélküli módban.
