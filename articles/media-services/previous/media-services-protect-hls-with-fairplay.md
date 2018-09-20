---
title: HLS-tartalom a Microsoft PlayReady- vagy Apple FairPlay - Azure védelme |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt, és bemutatja, hogyan használható az Azure Media Services dinamikusan titkosítani a HTTP Live Streaming (HLS) az Apple fairplay által. Azt is bemutatja, hogyan használja a Media Services licenctovábbítási szolgáltatása FairPlay-licenc továbbítására az ügyfelek számára.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: juliako
ms.openlocfilehash: 32f3f4fd3f4f299c9b084ab8604b56ea70e639a4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368228"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>HLS-tartalmak az Apple fairplay által vagy a Microsoft PlayReady védelme
Az Azure Media Services lehetővé teszi, hogy dinamikusan titkosítani a HTTP Live Streaming (HLS) használatával a következő formátumok:  

* **A boríték AES-128 titkosítatlan kulcs**

    A teljes adattömbök használatával titkosítja a **AES-128 CBC** mód. A titkosítást a Stream natív módon támogatott iOS és OS X lejátszási szerint. További információkért lásd: [használó AES-128, a dinamikus titkosítás és kulcstovábbítást](media-services-protect-with-aes128.md).
* **Az Apple FairPlay**

    Az egyes video- és minták segítségével lettek titkosítva a **AES-128 CBC** mód. **FairPlay Streaming** (FPS) integrálva van az eszköz operációs rendszerének támogatást biztosít az iOS és az Apple TV. OS x-en a Safari FPS az Encrypted Media Extensions (EME) felületen támogatási segítségével teszi lehetővé.
* **A Microsoft PlayReady**

Az alábbi képen látható a **HLS + FairPlay vagy a PlayReady, a dinamikus titkosítás** munkafolyamat.

![A dinamikus titkosítás munkafolyamatának ábrája](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Ez a cikk bemutatja, hogyan használja a Media Services dinamikus titkosítást az Apple FairPlay a HLS-tartalmak. Azt is bemutatja, hogyan használja a Media Services licenctovábbítási szolgáltatása FairPlay-licenc továbbítására az ügyfelek számára.

> [!NOTE]
> Ha szeretné is, a HLS-tartalmak playreadyvel, hozzon létre egy közös tartalomkulcsot, és társíthatja azt az objektumot szüksége. Emellett konfigurálnia kell a tartalomkulcs hitelesítési szabályzatának, leírtak szerint [PlayReady használatával a dynamic common encryption](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Követelmények és szempontok

A következőkre szükség, hogy a titkosított FairPlay HLS, és a FairPlay-licencek Media Services használata esetén:

  * Egy Azure-fiók. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Egy Media Services-fiók. Hozzon létre egyet, tekintse meg [hozzon létre egy Azure Media Services-fiók az Azure portal használatával](media-services-portal-create-account.md).
  * Regisztrálás a következővel [Apple fejlesztői programjában](https://developer.apple.com/).
  * Apple szükséges a tartalom tulajdonosa beszerzése a [központi telepítési csomag](https://developer.apple.com/contact/fps/). Adja meg, hogy már megvalósította a kulcs biztonsági modul (KSM) a Media Services használatával, és, hogy a kért a végső FPS csomag. Nincsenek a végső FPS csomag létrehozása a hitelesítésszolgáltató, és szerezze be a titkos kulcs (?) témakör utasításait. Kérje meg a FairPlay konfigurálása használhatja.
  * Az Azure Media Services .NET SDK-verzió **3.6.0** vagy újabb.

A Media Services kulcskézbesítési oldalon be kell állítani a az alábbiakat:

  * **Alkalmazás-tanúsítvány (AC)**: Ez az egy .pfx-fájlt, amely tartalmazza a titkos kulcsot. A fájl létrehozásához és a titkosítás, a jelszó.

       Egy kulcs objektumtovábbítási szabályzat konfigurálásakor meg kell adnia, hogy a jelszó és a .pfx-fájlt Base64 formátumban.

      Az alábbi lépések bemutatják, hogyan hozhat létre egy *.pfx formátumú tanúsítványfájlt a fairplay rendszerhez:

    1. Telepítse az OpenSSL https://slproweb.com/products/Win32OpenSSL.html.

        Lépjen abba a mappába, amelyeknél a FairPlay tanúsítvány és az Apple által biztosított egyéb fájlokat.
    2. Futtassa az alábbi parancsot a parancssorból. A .cer fájl konvertál egy .pem fájlra.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509-tájékoztatja der-a FairPlay.cer-FairPlay-out.pem ki
    3. Futtassa az alábbi parancsot a parancssorból. A titkos kulccsal is egy .pfx fájlba alakítja a .pem-fájlt. OpenSSL majd felkéri, a jelszót a .pfx fájl számára.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-exportálás - FairPlay-out.pfx ki-inkey privatekey.pem – a FairPlay-out.pem - passin file:privatekey-pem-pass.txt
  * **Alkalmazás tanúsítvány jelszava**: A jelszót a .pfx-fájl létrehozásához.
  * **Alkalmazás-Cert jelszó megadása**: fel kell töltenie a jelszót, és hogyan feltöltik más Media Services kulcs hasonló. Használja a **ContentKeyType.FairPlayPfxPassword** Felsorolásérték lekérni, a Media Services-azonosító. Ez az, hogy mit kell használniuk a kulcskézbesítési házirend-beállításként belül.
  * **IV**: Ez az 16 bájtos véletlenszerű értéket. Meg kell egyeznie a iv az adategység továbbítási házirendjét. Ön hozza létre a iv, és helyezze azt mindkét helyen: az adategység továbbítási házirendjét és a kulcskézbesítési házirend-beállításként.
  * **Kérje meg**: ezt a kulcsot érkezik, amikor tanúsítványt generál az Apple fejlesztői portál használatával. Minden egyes fejlesztési csapat kap egy egyedi kérje meg. Másolat készítése a kérje meg a, és tárolja biztonságos helyen. Kérje meg FairPlayAsk később a Media Services-t konfigurálhatja kell.
  * **Kérje meg azonosító**: Ez az azonosító kapjuk meg, kérje meg a Media Services szolgáltatásba való feltöltésekor. Fel kell töltenie használatával kérje meg a **ContentKeyType.FairPlayAsk** felsorolási érték. Ennek eredményeképpen a Media Services-Azonosítót ad vissza, és azt, hogy mit kell használni, amikor a kulcskézbesítési házirend beállítást.

A következőkre FPS ügyféloldali be kell állítania:

  * **Alkalmazás-tanúsítvány (AC)**: egy.cer/.der-fájl, amely tartalmazza a nyilvános kulcsot, amely néhány hasznos titkosításához használja az operációs rendszer. A Media Services kell ismernem, mert a a Windows Media Player. A kulcstovábbítást visszafejti a megfelelő titkos kulccsal.

A FairPlay titkosított stream lejátszás, első beolvasása egy valódi kérje meg, és ezután hozza létre a tanúsítványnak valódi. A folyamat hoz létre minden három részből áll:

  * .der fájl
  * .pfx file
  * a .pfx jelszava

A következő ügyfelek támogatja a HLS- **AES-128 CBC** titkosítási: az OS X, az Apple TV, iOS Safari böngésző.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>FairPlay a dinamikus titkosítás és a licenc licenctovábbítási szolgáltatások konfigurálása
A következőkben általános lépéseket a FairPlay eszközök védelmére, a Media Services licenctovábbítási szolgáltatása használatával, valamint a dinamikus titkosítás segítségével.

1. Hozzon létre egy objektumot, és töltse fel bele a fájlokat.
2. Kódolja a fájlt tartalmazó objektumot az adaptív sávszélességű MP4 típusú beállításkészlettel.
3. Hozzon létre egy tartalomkulcsot, és társítsa a kódolt objektumhoz.  
4. Konfigurálja a tartalomkulcs hitelesítési szabályzatát. Adja meg a következőket:

   * A kézbesítési módszert (ebben az esetben az FairPlay).
   * FairPlay házirend-beállítások konfigurálása. További információ a FairPlay konfigurálása: a **ConfigureFairPlayPolicyOptions()** metódus az alábbi példában.

     > [!NOTE]
     > Általában érdemes FairPlay házirend beállításainak konfigurálása csak egyszer, mert csak van egy tanúsítási és a egy kérje meg egy készletét.
     >
     >
   * Korlátozások (nyitott vagy jogkivonat).
   * A kulcskézbesítési típus, amely meghatározza, hogy a kulcsot a rendszer hogyan továbbítja az ügyfélnek-re vonatkozó információk.
5. Az objektumtovábbítási szabályzat konfigurálása. A továbbítási szabályzat konfigurációjának tartalmazza:

   * A továbbítási protokoll (HLS).
   * A dinamikus titkosítás típusa (közös CBC encryption).
   * A licenc licenckérési URL-cím.

     > [!NOTE]
     > Ha szeretne olyan adatfolyamra, amely a FairPlay és a egy másik digitális jogkezelési (DRM) rendszer titkosítva van, hogy külön kézbesítési házirendek konfigurálása:
     >
     > * Egy IAssetDeliveryPolicy dinamikus adaptív Streamelés konfigurálása HTTP (DASH) az általános titkosítás (CENC) (PlayReady és Widevine), valamint zökkenőmentes playreadyvel
     > * Egy másik IAssetDeliveryPolicy konfigurálása FairPlay a HLS Protokollhoz
     >
     >
6. Hozzon létre egy OnDemand-lokátort a streamelési URL-cím lekéréséhez.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Használja a FairPlay kulcskézbesítési lejátszó alkalmazások által
Az iOS SDK használatával is fejleszthet alkalmazásokat player. Hogy a FairPlay lejátszani, kell megvalósítani a licenc az exchange-protokollt. Ez a protokoll nem adott meg az Apple. Esetén a minden alkalmazás kulcskézbesítési kérelem küldése. A Media Services FairPlay kulcstovábbítást vár a SPC lesz, mint egy www-form-url kódolt üzenet közzététele, a következő formátumban:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Az Azure Media Player támogatja a FairPlay lejátszási. Lásd: [dokumentáció az Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) találhat további információt.
>
>

## <a name="streaming-urls"></a>Streamelési URL-ek
Ha egynél több DRM-az eszköz titkosítva van, a streamelési URL-címben egy titkosítási címke használja: (formátum = "m3u8-aapl" titkosítási = "xxx").

A következő szempontokat kell figyelembe venni:

* Csak nulla vagy egy titkosítási típus adható meg.
* A titkosítási típus nem kell az URL-cím adható meg, ha csak az eszköz egy titkosítási lett alkalmazva.
* A titkosítási típus megkülönbözteti a kis-és nagybetű nincs megkülönböztetve.
* A következő titkosítási típusok adható meg:  
  * **cenc**: általános titkosítás (PlayReady vagy Widevine)
  * **cbcs-aapl**: FairPlay
  * **CBC**: boríték AES-titkosítás

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 
2. Adja hozzá a következő elemeket az app.config fájlban megadott **appSettings** szakaszhoz:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Példa

A következő minta bemutatja, hogyan képes a FairPlay titkosított tartalmat a Media Services használatával. Ez a funkció az Azure Media Services SDK for .NET 3.6.0-ás jelent. 

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

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
