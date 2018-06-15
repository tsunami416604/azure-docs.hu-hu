---
title: Tartalom a Microsoft PlayReady vagy Apple FairPlay - Azure HLS védelme |} Microsoft Docs
description: Ez a témakör áttekintést nyújt, és dinamikusan titkosítani a HTTP-Live Streaming (HLS) Apple FairPlay az Azure Media Services használatát ismerteti. Azt is bemutatja, hogyan használja a Media Services licenctovábbítási szolgáltatása képes biztosítani a FairPlay-licenc ügyfelek számára.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 91f117c3b1b166a069b93c238380140f19e49280
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
ms.locfileid: "29738422"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>A tartalom Apple FairPlay vagy a Microsoft PlayReady HLS védelme
Az Azure Media Services lehetővé teszi, hogy dinamikusan titkosítani a HTTP-Live Streaming (HLS) a következő formátum használatával:  

* **AES-128 boríték tiszta kulcsot**

    A teljes rendszer használatával titkosítja a **AES-128 CBC** mód. Az adatfolyam visszafejtése natív módon támogatott iOS és OS X-lejátszót. További információkért lásd: [AES-128 használata a dinamikus titkosítás és a kulcs kézbesítési szolgáltatás](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Az egyes video- és minták segítségével lettek titkosítva a **AES-128 CBC** mód. **FairPlay Streaming** (FPS) integrálva van az eszköz-operációsrendszerek, iOS és az Apple TV natív támogatását. OS x Safari FPS használatával a titkosított adathordozó-bővítmények (EME) felület támogatása lehetővé teszi.
* **Microsoft PlayReady**

Az alábbi képen látható a **HLS + FairPlay vagy PlayReady a dinamikus titkosítás** munkafolyamat.

![A dinamikus titkosítás munkafolyamat diagramja](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Ez a cikk bemutatja, hogyan használja a Media Services dinamikus titkosítást az Apple FairPlay HLS tartalmaihoz. Azt is bemutatja, hogyan használja a Media Services licenctovábbítási szolgáltatása képes biztosítani a FairPlay-licenc ügyfelek számára.

> [!NOTE]
> Ha szeretné titkosítani a HLS a PlayReady, szeretné hozzon létre egy közös tartalomkulcsot, és rendelje hozzá azt az objektumot. Azt is konfigurálnia kell a tartalomkulcs hitelesítési szabályzatának, a [segítségével PlayReady a dynamic common encryption](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Követelmények és szempontok

A következőkre szükség FairPlay titkosított HLS továbbítására, és képes biztosítani a FairPlay-licenc Media Services használata közben:

  * Egy Azure-fiók. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Egy Media Services-fiók. Szeretne létrehozni egyet, lásd: [hozzon létre egy Azure Media Services-fiók az Azure portál használatával](media-services-portal-create-account.md).
  * A regisztráció [Apple fejlesztési Program](https://developer.apple.com/).
  * Apple szükséges a tartalom tulajdonosa az beszerzése a [központi telepítési csomag](https://developer.apple.com/contact/fps/). Adja meg, hogy már megvalósította a kulcs biztonsági modul (KSM) a Media Services, és a végső FPS csomag kérelmet. A végső FPS csomagban hitelesítésszolgáltató létrehozása, és szerezze be a titkos kulcs (?) utasításokat is. Kérje meg FairPlay konfigurálására használt.
  * Az Azure Media Services .NET SDK-verzió **3.6.0** vagy újabb.

A következő műveleteket kell állítani a Media Services kulcs kézbesítési oldalon:

  * **Alkalmazás Cert (AC)**: egy .pfx-fájl, amely tartalmazza a titkos kulcsot. A fájl létrehozásához, és a titkosítás jelszóval.

       A kulcs továbbítási szabályzatban konfigurálásakor meg kell adnia, hogy a jelszó és a .pfx fájl Base64 formátumban.

      A következő lépések azt ismertetik, hogyan FairPlay .pfx tanúsítványfájl létrehozására:

    1. A https://slproweb.com/products/Win32OpenSSL.html OpenSSL telepíteni.

        Nyissa meg azt a mappát, amelyben a FairPlay tanúsítvány és egyéb Apple kézbesítette fájlokat is.
    2. Futtassa az alábbi parancsot a parancssorból. A .cer fájl konvertál egy .pem fájlt.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509-der tájékoztatja-a FairPlay.cer-FairPlay-out.pem kimenő
    3. Futtassa az alábbi parancsot a parancssorból. A titkos kulcsot tartalmazó .pfx fájlba alakítja a .pem fájl. OpenSSL majd felkéri, a .pfx fájlhoz tartozó jelszót.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-exportálása - kimenő FairPlay-out.pfx-inkey privatekey.pem-FairPlay-out.pem - passin file:privatekey-pem-pass.txt a
  * **Alkalmazás Cert jelszó**: A jelszót a .pfx fájl létrehozásához.
  * **Alkalmazás Cert jelszó azonosítója**: a jelszó, hogyan azok feltölteni más Media Services kulcsok hasonló kell feltöltenie. Használja a **ContentKeyType.FairPlayPfxPassword** Felsorolásérték lekérni a Media Services-azonosító. Ez akkor szükséges a kulcs kézbesítési házirend-beállításként belül használja.
  * **IV**: egy véletlenszerű érték 16 bájt. Meg kell egyeznie a iv az adategység továbbítási házirendjét. Ön hozza létre a iv, és mindkét helyen elhelyezi: az adategység továbbítási házirendjét és a kulcs kézbesítési házirend-beállításként.
  * **Kérje meg**: Ez a kulcs érkezik, ha a hitelesítésszolgáltató létrehozásakor az Apple fejlesztői portáljáról használatával. Minden fejlesztési csapat kap egy egyedi kérje meg. A KÉRJEN másolatának mentése, és tárolja biztonságos helyen. Kérje meg beállítása később a Media Services FairPlayAsk kell.
  * **Kérje meg azonosító**: Ez az azonosító kapjuk meg, kérje meg a Media Services feltöltésekor. Kérje meg a kell feltöltenie az **ContentKeyType.FairPlayAsk** számbavételi érték. Ennek eredményeképpen a Media Services Azonosítót ad vissza, és azt kell használni, ha a beállítás a kulcs kézbesítési házirend-beállításként.

A következő műveleteket kell beállítania a FPS ügyféloldali:

  * **Alkalmazás Cert (AC)**: egy.cer/.der fájl, amely tartalmazza a nyilvános kulcsot, amely néhány hasznos titkosítására használja az operációs rendszer. A Media Services kell tudnia, vagy mert a Windows Media Player van szükség. A kulcs kézbesítési szolgáltatás visszafejti a megfelelő titkos kulccsal.

FairPlay titkosított adatfolyam lejátszását, első lekérni egy valódi kérje meg, és majd a valódi tanúsítvány jön létre. A folyamat minden három részből hoz létre:

  * .der fájl
  * .pfx file
  * a .pfx jelszavát

A következő ügyfelek támogatják a HLS **AES-128 CBC** titkosítási: OS X, az Apple TV IOS Safari böngésző.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>FairPlay dinamikus titkosítás és licenc licenctovábbítási szolgáltatások konfigurálása
Az alábbi lépések általános FairPlay a eszközök védelmére, a Media Services licenctovábbítási szolgáltatása segítségével, valamint a dinamikus titkosítás használatával.

1. Hozzon létre egy objektumot, és töltse fel bele a fájlokat.
2. Kódolja a fájlt tartalmazó objektumot az adaptív sávszélességű MP4 típusú beállításkészlettel.
3. Hozzon létre egy tartalomkulcsot, és társítsa a kódolt objektumhoz.  
4. Konfigurálja a tartalomkulcs hitelesítési szabályzatát. Adja meg az alábbiakat:

   * A kézbesítési módszert (ebben az esetben az FairPlay).
   * FairPlay házirend-beállítások konfigurálása. FairPlay konfigurálásával kapcsolatos részletekért lásd: a **ConfigureFairPlayPolicyOptions()** módszer az alábbi minta.

     > [!NOTE]
     > Általában célszerű FairPlay házirend beállításainak konfigurálása csak egyszer, mert csak egy hitelesítő és egy KÉRJEN egy készletét van.
     >
     >
   * Korlátozások (nyitott vagy token).
   * A kulcs kézbesítési típust, amely meghatározza, hogyan a kulcs kézbesítve lenne az ügyfél-specifikus adatait.
5. Konfigurálja az adategység továbbítási házirendjét. A továbbítási szabályzat konfigurációjához tartalmazza:

   * A továbbítási protokoll (HLS).
   * A dinamikus titkosítás (közös CBC titkosítás) típusú.
   * A licenc licenckérési URL-cím.

     > [!NOTE]
     > Ha azt szeretné, hogy FairPlay és egy másik digitális tartalomvédelmi (DRM) rendszeren titkosított adatfolyam, hogy külön továbbítási házirendjeit konfigurálnia:
     >
     > * Egy IAssetDeliveryPolicy konfigurálása a dinamikus adaptív Streameléshez HTTP (DASH) a Common Encryption (CENC) (PlayReady + Widevine), és a PlayReady Smooth keresztül
     > * Egy másik IAssetDeliveryPolicy HLS FairPlay konfigurálása
     >
     >
6. Hozzon létre egy OnDemand-lokátort a streamelési URL-cím lekéréséhez.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>FairPlay kulcs kézbesítési player alkalmazások használatát
Az IOS SDK player alkalmazásokat fejleszthet. Nem fogja tudni FairPlay tartalom lejátszása, be kell megvalósítani a licenc exchange protokoll. Ez a protokoll Apple nincs megadva. Esetén a minden alkalmazás kulcs kézbesítési kérelem küldése. A Media Services FairPlay kulcs kézbesítési szolgáltatás vár a SPC lesz üzenetként www-form-url kódolt feladás egy vagy több, a következő formában:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Az Azure Media Player FairPlay lejátszását támogatja. Lásd: [Azure Media Player dokumentáció](https://amp.azure.net/libs/amp/latest/docs/index.html) további tájékoztatást talál.
>
>

## <a name="streaming-urls"></a>Streamelési URL-ek
Ha az objektum egynél több DRM lett titkosítva, egy titkosítási címke használjon az adatfolyam-továbbítási URL-cím: (formátum = "m3u8-aapl" titkosítási = "xxx").

A következők érvényesek:

* Csak nulla vagy egy titkosítási típus adható meg.
* A titkosítási típus nem kell az URL-cím adható meg, ha csak egy titkosítási alkalmazta az eszközhöz.
* A titkosítási típus-és nagybetűket.
* A következő titkosítási típusok adhatók meg:  
  * **cenc**: Common encryption (PlayReady vagy Widevine)
  * **cbcs-aapl**: FairPlay
  * **CBC**: AES envelope titkosítás

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 
2. Adja hozzá a következő elemeket az app.config fájlban megadott **appSettings** szakaszhoz:

    ```xml
            <add key="Issuer" value="http://testacs.com"/>
            <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Példa

A következő példa bemutatja teszi a Media Services használatát a FairPlay titkosított tartalom. Ez a funkció az Azure Media Services SDK bevezetett a .NET-hez 3.6.0 verziója. 

Írja felül a Program.cs fájlban található kódot az itt látható kóddal.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

Módosítsa úgy a változókat, hogy a bemeneti fájlok tárolásához Ön által használt mappákra mutassanak.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

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


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

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

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
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
```

## <a name="next-steps-media-services-learning-paths"></a>Következő lépések: Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
