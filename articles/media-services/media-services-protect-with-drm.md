---
title: "PlayReady és/vagy Widevine Dynamic Common Encryption használata | Microsoft Docs"
description: "A Microsoft Azure Media Services lehetővé teszi, hogy MPEG-DASH, Smooth Streaming vagy HTTP-Live-Streaming (HLS) típusú streamjeit Microsoft PlayReady DRM-védelemmel lássa el. Ezenfelül Widevine DRM-védelemmel ellátott DASH-továbbítást is kínál. Ez a témakör bemutatja, hogyan állíthat be dinamikus titkosítást a PlayReady vagy a Widevine DRM segítségével."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 59c0b46015b3d112d17dd79a2a4bfd3b3165dfba


---
# <a name="using-playready-andor-widevine-dynamic-common-encryption"></a>A PlayReady és/vagy Widevine Dynamic Common Encryption titkosítás használata
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-drm.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

A Microsoft Azure Media Services lehetővé teszi, hogy MPEG-DASH, Smooth Streaming vagy HTTP-Live-Streaming (HLS) típusú streamjeit [Microsoft PlayReady DRM-védelemmel](https://www.microsoft.com/playready/overview/) lássa el. Ezenfelül arra is lehetőséget kínál, hogy titkosított DASH-streameket továbbítson Widevine DRM-licencek segítségével. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik. Az AssetDeliveryConfiguration Widevine használatára történő beállításához használja az [AMS .NET SDK-t](https://www.nuget.org/packages/windowsazure.mediaservices/) (a 3.5.1-es vagy újabb verziót), vagy a REST API-t.

A Media Services része egy szolgáltatás, amelynek segítségével PlayReady vagy Widevine DRM-licenceket továbbíthat. A Media Services ezenfelül API-kat is tartalmaz, amelyek segítségével beállíthatja azokat a jogokat és korlátozásokat, amelyeket szeretne betartatni a PlayReady vagy a Widevine DRM-futtatókörnyezettel, amikor egy felhasználó védett tartalmakat játszik le. Amikor a felhasználók DRM-védelemmel rendelkező tartalmat kérnek, a lejátszóalkalmazás licencet kér az AMS-licencelési szolgáltatástól.  Az AMS-licencelési szolgáltatás akkor adja meg a licencet, ha a kérelmező felhasználó megkapta a megfelelő jogosultságokat. A PlayReady- vagy Widevine-licencek tartalmazzák a feloldási kulcsot, amelynek segítségével az ügyféllejátszó képes feloldani a titkosítást, majd streamelni a kért tartalmakat.

A Widevine-licencek továbbításának támogatásához a következő AMS-partnereket is használhatja: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) vagy [castLabs](http://castlabs.com/company/partners/azure/). További információk: integráció az [Axinom](media-services-axinom-integration.md) és a [castLabs](media-services-castlabs-integration.md) rendszerekkel.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelyek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások. A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. A Media Services a [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) és a [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) formátumú tokeneket támogatja. További információk: A tartalomkulcs hitelesítési szabályzatának konfigurálása.

A dinamikus titkosítás által nyújtott előnyök kihasználásához többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektummal kell rendelkeznie. Ezenfelül be kell állítania az objektumhoz tartozó továbbítási szabályzatokat is (ennek módját a témakör későbbi részében írjuk le). Ezt követően az igényalapú streamelési kiszolgáló a streamelési URL-címben megadott formátumnak megfelelően gondoskodik arról, hogy a rendszer a kiválasztott protokollal továbbítsa a streamet. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services elkészíti és kiszolgálja az ügyféltől érkező kéréseknek megfelelő HTTP-választ.

Ez a témakör azon fejlesztők számára lehet hasznos, akik többféle DRM-mel (például PlayReady és Widevine) védett médiafájlok továbbításával foglalkoznak. A témakör leírja, hogyan konfigurálhatja a PlayReady-licenctovábbítási szolgáltatásra vonatkozó szabályzatokat úgy, hogy csak az arra jogosult ügyfelek kaphassák meg a PlayReady- és Widevine-licenceket. Ezenfelül azt is bemutatja, hogyan használja a dinamikus titkosítás funkciót a PlayReady vagy a Widevine DRM-mel a DASH-en keresztül.

> [!NOTE]
> A dinamikus titkosítás használatának megkezdéséhez be kell szereznie legalább egy skálázási egységet (avagy streamelési egységet). További információk: [How to Scale a Media Service](media-services-portal-manage-streaming-endpoints.md) (Médiaszolgáltatás skálázása).
> 
> 

## <a name="download-sample"></a>Minta letöltése
A cikkben leírt mintát [innen](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) töltheti le.

## <a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>A Dynamic Common Encryption és DRM-licenctovábbítási szolgáltatások konfigurálása
A következőkben általános lépéseket olvashat, amelyeket el kell végeznie ahhoz, hogy PlayReady-védelemmel lássa el objektumait a Media Services licenctovábbítási szolgáltatása, valamint a dinamikus titkosítás használata mellett.

1. Hozzon létre egy objektumot, és töltse fel a fájlokat az objektumba.
2. Kódolja a fájlt tartalmazó objektumot az adaptív sávszélességű MP4 típusú beállításkészlettel.
3. Hozzon létre egy tartalomkulcsot, majd társítsa a kódolt objektumhoz. A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítási kulcsát.
4. Konfigurálja a tartalomkulcs hitelesítési szabályzatát. Ahhoz, hogy az ügyfél megkaphassa a tartalomkulcsot, Önnek be kell állítania a tartalomkulcs-hitelesítési szabályzatot, amelynek az ügyfélnek meg kell felelnie.

A tartalomkulcs-hitelesítési szabályzat létrehozásakor a következőket kell beállítania: továbbítási módszer (PlayReady vagy Widevine), korlátozások (nyitott vagy token), valamint azon információk, amelyek azt határozzák meg, hogy a rendszer hogyan továbbítja a kulcsot az ügyfélnek ([PlayReady-](media-services-playready-license-template-overview.md) vagy [Widevine-](media-services-widevine-license-template-overview.md)licencsablon).

1. Konfigurálja az objektum továbbítási szabályzatát. A továbbítási szabályzat konfigurációjához a következők tartoznak: továbbítási protokoll (lehet például MPEG DASH, HLS, HDS, Smooth Streaming vagy mindegyik), a dinamikus titkosítás típusa (lehet például Common Encryption) és a PlayReady- vagy Widevine-licenckérési URL-cím.

Az adott objektum különböző protokolljaira akár eltérő szabályzatokat is alkalmazhat. Beállíthatja például, hogy a PlayReady-titkosítás csak a Smooth/DASH-re vonatkozzon, az AES Envelope pedig csak a HLS-re. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

1. Hozzon létre egy OnDemand-lokátort a streamelési URL-cím lekéréséhez.

A témakör végén teljes .NET típusú példát talál.

Az alábbi képen a fentiekben leírt munkafolyamatot láthatja. Itt a tokenes hitelesítést használtuk.

![Védelem biztosítása a PlayReadyvel](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

A témakör további részében részletes magyarázatokat, kódmintákat és olyan témakörökre mutató hivatkozásokat talál, amelyek segítenek elérni a fent leírt célokat.

## <a name="current-limitations"></a>Aktuális korlátozások
Objektumtovábbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell az ahhoz tartozó lokátort (ha van), majd létre kell hoznia egy új lokátort.

Az Azure Media Services szolgáltatással végzett Widevine-titkosításra vonatkozó korlátozások: a funkció jelenleg nem támogatja több tartalomkulcs használatát.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>Objektum létrehozása, majd fájlok feltöltése az objektumba
A videók kezeléséhez, kódolásához és streameléséhez először fel kell töltenie tartalmait a Microsoft Azure Media Services szolgáltatásba. A feltöltést követően tartalmai a biztonságos felhőtárhelyre kerülnek további feldolgozás és streamelés céljából.

További információk: [Upload Files into a Media Services account](media-services-dotnet-upload-files.md) (Fájlok feltöltése a Media Services-fiókba).

## <a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>A fájlt tartalmazó objektum kódolása az adaptív sávszélességű MP4 típusú beállításkészlettel
A dinamikus titkosítás segítségével mindössze egy többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektumot kell létrehoznia. Ezt követően az igényalapú streamelési kiszolgáló a jegyzékfájlban és a töredékkérésben megadott formátumnak megfelelően gondoskodik arról, hogy a rendszer a kiválasztott protokollal biztosítsa a streamet az Ön számára. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ. További információkért lásd a [Dynamic Packaging Overview](media-services-dynamic-packaging-overview.md) (A dinamikus becsomagolás áttekintése) című témakört.

A kódolással kapcsolatos utasításokért lásd: [How to encode an asset using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) (Objektum kódolása a Media Encoder Standard használatával).

## <a name="a-idcreatecontentkeyacreate-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Tartalomkulcs létrehozása és társítása a kódolt objektumhoz
A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítására használható kulcsot.

További információk: [Create content key](media-services-dotnet-create-contentkey.md) (Tartalomkulcs létrehozása).

## <a name="a-idconfigurekeyauthpolicyaconfigure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>A tartalomkulcs engedélyezési házirendjének konfigurálása
A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. Ahhoz, hogy az ügyfél (a lejátszó) megkaphassa a kulcsot, Önnek be kell állítania a tartalomkulcs-hitelesítési szabályzatot, amelynek az ügyfélnek meg kell felelnie. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások.

További információk: [A tartalomkulcs hitelesítési szabályzatának létrehozása](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

## <a name="a-idconfigureassetdeliverypolicyaconfigure-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Objektumtovábbítási szabályzat konfigurálása
Konfigurálja az objektum továbbítási szabályzatát. Az objektumtovábbítási szabályzat konfigurálásához többek között az alábbiak tartoznak:

* A DRM-licenckérési URL-cím. 
* Az objektumtovábbítási protokoll (például MPEG DASH, HLS, HDS, Smooth Streaming vagy mindegyik). 
* A dinamikus titkosítás típusa (ebben az esetben Common Encrpytion). 

További információk: [Objektumtovábbítási szabályzat konfigurálása](media-services-rest-configure-asset-delivery-policy.md).

## <a name="a-idcreatelocatoracreate-an-ondemand-streaming-locator-in-order-to-get-a-streaming-url"></a><a id="create_locator"></a>OnDemand-lokátor létrehozása a streamelési URL-cím lekérése érdekében
A felhasználók rendelkezésére kell bocsátania a Smooth, DASH vagy HLS streamelési URL-címét.

> [!NOTE]
> Objektumtovábbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell az ahhoz tartozó meglévő lokátort (ha van), majd létre kell hoznia egy új lokátort.
> 
> 

További információk az objektumok közzétételéről és a streamelési URL-cím létrehozásáról: [Build a streaming URL](media-services-deliver-streaming-content.md) (Streamelési URL-cím létrehozása).

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése
Kérje le a kulcshitelesítési szabályzatban használt jogkivonat-korlátozásoknak megfelelő tesztjogkivonatot.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);


A stream kipróbálásához használja az [AMS-lejátszót](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="a-idexampleaexample"></a><a id="example"></a>Példa
Az alábbi mintában azokat a funkciókat mutatjuk be, amelyeket az Azure Media Services SDK for .Net 3.5.2-es verziója vezetett be (azaz a funkciót, amelynek segítségével Widevine-licencsablon állítható be, majd a Widevine-licenc lekérhető az Azure Media Services szolgáltatásból). A csomag telepítéséhez az alábbi Nuget-csomagparancsot használtuk:

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Hozzon létre egy új konzolos projektet.
2. A NuGet segítségével telepítse, majd adja hozzá az Azure Media Services .NET SDK-t.
3. Adja hozzá a további referenciákat: System.Configuration.
4. Adja hozzá a fióknevet és a kulcsadatokat tartalmazó konfigurációs fájlt:
   
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
   
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
   
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>
5. Szerezzen be legalább egy streamelési egységet a tartalom továbbításához használni kívánt streamvégpontra. További információkért lásd a [streamvégpontok konfigurálását bemutató részt](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).
6. Írja felül a Program.cs fájlban található kódot az itt látható kóddal.
   
    Módosítsa úgy a változókat, hogy a bemeneti fájlok tárolásához Ön által használt mappákra mutassanak.
   
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
   
        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
   
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
   
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
   
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
   
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
   
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
   
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
   
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
   
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
   
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
                    Console.WriteLine();
   
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
   
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
   
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
   
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
   
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
   
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
   
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
   
                    Console.ReadLine();
                }

                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }

                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

                    Console.WriteLine("Created assetFile {0}", assetFile.Name);

                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);

                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

                    Console.WriteLine("Upload {0}", assetFile.Name);

                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);

                    locator.Delete();
                    policy.Delete();

                    return inputAsset;
                }

                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";

                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));

                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),     AssetCreationOptions.StorageEncrypted);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }


                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {

                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);

                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);

                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);

                    return key;
                }

                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {

                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy          

                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
                    };

                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);

                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("", 
                            ContentKeyDeliveryType.Widevine, 
                            restrictions, WidevineLicenseTemplate);

                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;


                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }

                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();

                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                    };

                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);

                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);

                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;

                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;

                    return tokenTemplateString;
                }

                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }

                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.

                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                    //It contains a field for a custom data string between the license server and the application 
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users. 
                    //It contains the data on the content key in the license and any rights or restrictions to be 
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client) 
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;

                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                    // It grants the user the ability to playback the content subject to the zero or more restrictions 
                    // configured in the license and on the PlayRight itself (for playback specific policy). 
                    // Much of the policy on the PlayRight has to do with output restrictions 
                    // which control the types of outputs that the content can be played over and 
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                    //then the DRM runtime will only allow the video to be displayed over digital outputs 
                    //(analog video outputs won’t be allowed to pass the content).

                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                    // If the output protections are configured too restrictive, 
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

                    responseTemplate.LicenseTemplates.Add(licenseTemplate);

                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }


                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };

                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }

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
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

                        };

                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);


                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);

                }

                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {

                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 

                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();

                    // Create a 30-day readonly access policy. 
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);

                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));

                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }

                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }

                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];

                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }

                    return returnValue;
                }
            }
        }


## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[CENC with Multi-DRM and Access Control](media-services-cenc-with-multidrm-access-control.md) (CENC többszörös DRM-mel és hozzáférés-vezérléssel)

[Configure Widevine packaging with AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services) (Widevine-csomagolás konfigurálása az AMS-szel)

[Announcing Google Widevine license delivery services in Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/) (A Google Widevine-licenctovábbítási szolgáltatás megjelenése az Azure Media Services-ben)




<!--HONumber=Nov16_HO2-->


