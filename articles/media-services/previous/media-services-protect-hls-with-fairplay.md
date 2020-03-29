---
title: HlS-tartalom védelme a Microsoft PlayReady vagy az Apple FairPlay segítségével - Azure | Microsoft dokumentumok
description: Ez a témakör áttekintést ad, és bemutatja, hogyan használhatja az Azure Media Services segítségével a HTTP Live Streaming (HLS) tartalom dinamikus titkosítását az Apple FairPlay segítségével. Azt is bemutatja, hogyan használhatja a Media Services licenckézbesítési szolgáltatását fairplay-licencek ügyfeleknek történő kézbesítésére.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 873bc4ab5e435b91ff4400a39c92db0d0bb9baa8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968765"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>A HLS-tartalom védelme az Apple FairPlay vagy a Microsoft PlayReady segítségével

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).   > A Media Services v2-höz nem ad nak hozzá új szolgáltatásokat vagy funkciókat. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)
>

Az Azure Media Services lehetővé teszi a HTTP Live Streaming (HLS) tartalom dinamikus titkosítását a következő formátumok használatával:  

* **AES-128 boríték átlátszó kulcs**

    A teljes adattömb az **AES-128 CBC** mód használatával van titkosítva. Az adatfolyam visszafejtését az iOS és az OS X-lejátszó natívan támogatja. További információ: [Az AES-128 dinamikus titkosítás és kulcskézbesítési szolgáltatás használata.](media-services-protect-with-aes128.md)
* **Apple FairPlay**

    Az egyes video- és hangminták titkosítása **AES-128 CBC** módban történik. **A FairPlay Streaming** (FPS) integrálva van az eszköz operációs rendszereibe, natív támogatással az iOS és az Apple TV rendszeren. A Safari on OS X lehetővé teszi az FPS használatát a Titkosított adathordozó-bővítmények (EME) felület támogatásával.
* **Microsoft PlayReady**

Az alábbi képen a **HLS + FairPlay vagy playReady dinamikus titkosítási** munkafolyamat látható.

![Dinamikus titkosítási munkafolyamat diagramja](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Ez a cikk bemutatja, hogyan használhatja a Media Services szolgáltatást a HLS-tartalom dinamikus titkosítására az Apple FairPlay segítségével. Azt is bemutatja, hogyan használhatja a Media Services licenckézbesítési szolgáltatását fairplay-licencek ügyfeleknek történő kézbesítésére.

> [!NOTE]
> Ha azt is szeretné titkosítani a HLS-tartalmat a PlayReady-nel, létre kell hoznia egy közös tartalomkulcsot, és hozzá kell rendelnie az eszközhöz. A tartalomkulcs engedélyezési házirendjét is konfigurálnia kell a [PlayReady dinamikus közös titkosításának használata](media-services-protect-with-playready-widevine.md)című részben leírtak szerint.
>
>

## <a name="requirements-and-considerations"></a>Követelmények és megfontolások

A Media Services használata a FairPlay-szel titkosított HLS-szolgáltatás és a FairPlay-licencek biztosításához szükséges:

  * Egy Azure-fiók. További részletek az [Azure ingyenes próbaverziója.](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)
  * Egy Media Services-fiók. Hozzon létre egyet, [lásd: Hozzon létre egy Azure Media Services-fiókot az Azure Portalon.](media-services-portal-create-account.md)
  * Regisztráljon az [Apple Fejlesztési Programmal.](https://developer.apple.com/)
  * Az Apple megköveteli a tartalom tulajdonosát, hogy szerezze be a [telepítési csomagot](https://developer.apple.com/contact/fps/). Adja meg, hogy már megvalósította a Key Security Module (KSM) szolgáltatást a Media Services szolgáltatással, és hogy a végső FPS-csomagot kéri. Vannak utasítások a végső FPS csomagot generálni tanúsítás és megszerezni az application secret key (ASK). Az ASK segítségével konfigurálhatja a FairPlay-t.
  * Az Azure Media Services .NET SDK **3.6.0-s** vagy újabb verziója.

A Media Services kulcskézbesítési oldalán a következő dolgokat kell beállítani:

  * **App Cert (AC)**: Ez egy .pfx fájl, amely tartalmazza a személyes kulcsot. Ezt a fájlt létrehozza, és jelszóval titkosítja.

       A kulcskézbesítési házirend konfigurálásakor meg kell adnia ezt a jelszót és a .pfx fájlt Base64 formátumban.

      Az alábbi lépések bemutatják, hogyan hozhat létre .pfx tanúsítványfájlt a FairPlay számára:

    1. Telepítse az OpenSSL-t a programból. https://slproweb.com/products/Win32OpenSSL.html

        Nyissa meg azt a mappát, ahol a FairPlay tanúsítvány és az Apple által szállított egyéb fájlok találhatók.
    2. Futtassa az alábbi parancsot a parancssorból. Ezzel a .cer fájlt .pem fájllá alakítja.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. Futtassa az alábbi parancsot a parancssorból. Ezzel a .pem fájlt .pfx fájllá alakítja a személyes kulccsal. A .pfx fájl jelszavát ezután az OpenSSL kéri.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin fájl:privatekey-pem-pass.txt
  * **Alkalmazástanúsítvány-jelszó**: A .pfx fájl létrehozásának jelszava.
  * **Alkalmazástanúsítvány jelszóazonosítója**: A jelszót fel kell töltenie, hasonlóan ahhoz, ahogyan más Media Services-kulcsokat töltenek fel. Használja a **ContentKeyType.FairPlayPfxPassword** enum értéket a Media Services-azonosító lekért. Ez az, amit kell használni belül a legfontosabb kézbesítési házirend opciót.
  * **iv**: Ez egy véletlenszerű encika 16 bájt. Meg kell egyeznie a iv az eszköz szállítási politika. A iv- et hozza létre, és mindkét helyre elhelyezi: az eszközkézbesítési szabályzatot és a kulcskézbesítési házirend et.
  * **ASK**: Ez a kulcs akkor érkezik, amikor az Apple Developer portál on keresztül létrehozza a tanúsítványt. Minden fejlesztőcsapat egyedi ASK-ot kap. Mentse el az ASK egy példányát, és tárolja biztonságos helyen. Az ASK-ot később FairPlayAsk-ként kell konfigurálnia a Media Services szolgáltatáshoz.
  * **ASK ID**: Ez az azonosító akkor érhető el, amikor ask-ot tölt fel a Media Services szolgáltatásba. Az ASK fájlt a **ContentKeyType.FairPlayAsk** enum értékkel kell feltöltenie. Ennek eredményeképpen a Media Services-azonosító tért vissza, és ez az, amit fel kell használni a kulcs kézbesítési házirend beállításának beállításakor.

Az FPS ügyféloldalnak a következő dolgokat kell beállítania:

  * **Alkalmazás-tanúsítvány (AC)**: Ez egy .cer/.der fájl, amely tartalmazza a nyilvános kulcsot, amelyet az operációs rendszer használ bizonyos hasznos adatok titkosítására. A Media Services-nek tudnia kell róla, mert a lejátszónak szüksége van rá. A kulcskézbesítési szolgáltatás visszafejti azt a megfelelő személyes kulccsal.

FairPlay titkosított adatfolyam lejátszásához először szerezzen be egy valódi ASK-ot, majd hozzon létre egy valódi tanúsítványt. Ez a folyamat mindhárom részt létrehozza:

  * .der fájl
  * .pfx fájl
  * jelszót a .pfx

A következő ügyfelek támogatják a **HLS-t AES-128 CBC** titkosítással: Safari OS X rendszeren, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>A FairPlay dinamikus titkosítási és licenckézbesítési szolgáltatásainak konfigurálása
Az alábbiakban általános lépéseket taszunk fel az eszközök FairPlay használatával való védelméhez a Media Services licenckézbesítési szolgáltatásának használatával, valamint a dinamikus titkosítás használatával.

1. Hozzon létre egy objektumot, és töltse fel bele a fájlokat.
2. Kódolja a fájlt tartalmazó objektumot az adaptív sávszélességű MP4 típusú beállításkészlettel.
3. Hozzon létre egy tartalomkulcsot, és társítsa a kódolt objektumhoz.  
4. Konfigurálja a tartalomkulcs hitelesítési szabályzatát. Adja meg az alábbiakat:

   * A szállítási mód (ebben az esetben fairplay).
   * FairPlay házirend-beállítások konfigurációja. A FairPlay konfigurálásáról az alábbi mintában található **ConfigureFairPlayPolicyOptions()** metódusban olvashat részletesen.

     > [!NOTE]
     > Általában csak egyszer szeretné beállítani a FairPlay házirend-beállításait, mert csak egy minősítési és egy ASK készlete lesz.
     >
     >
   * Korlátozások (nyitott vagy token).
   * A kulcs kézbesítési típusára vonatkozó információk, amelyek meghatározzák, hogy a kulcs hogyan kerül átadásra az ügyfélhez.
5. Konfigurálja az eszközkézbesítési szabályzatot. A kézbesítési házirend konfigurációja a következőket tartalmazza:

   * A kézbesítési protokoll (HLS).
   * A dinamikus titkosítás típusa (közös CBC titkosítás).
   * A licencbeszerzés URL-címe.

     > [!NOTE]
     > Ha a FairPlay és egy másik digitális jogkezelési (DRM) rendszerrel titkosított adatfolyamot szeretne biztosítani, külön kézbesítési szabályzatokat kell konfigurálnia:
     >
     > * Egy IAssetDeliveryPolicy konfigurálni dinamikus adaptív streaming HTTP-n (DASH) a közös titkosítás (CENC) (PlayReady + Widevine), és a Sima PlayReady
     > * Egy másik IAssetDeliveryPolicy konfigurálni FairPlay a HLS
     >
     >
6. Hozzon létre egy OnDemand-lokátort a streamelési URL-cím lekéréséhez.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>FairPlay-kulcskézbesítés használata lejátszóalkalmazások szerint
Az iOS SDK használatával lejátszóalkalmazásokat fejleszthet. A FairPlay-tartalom lejátszásához végre kell hajtania a licenccsere protokollt. Ezt a protokollt az Apple nem adja meg. Az egyes alkalmazásoktól megkell tudni, hogyan küldhetnek kulcskézbesítési kérelmeket. A Media Services FairPlay kulcskézbesítési szolgáltatás a következő formában várja, hogy az Alkalmazási előírás www-form-url kódolású üzenetként érkezzen:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Az Azure Media Player támogatja a FairPlay lejátszást. További információt az [Azure Media Player dokumentációjában](https://amp.azure.net/libs/amp/latest/docs/index.html) talál.
>
>

## <a name="streaming-urls"></a>Streamelési URL-ek
Ha az eszköz egynél több DRM-mel volt titkosítva, használjon titkosítási címkét a streamelési URL-ben: (format='m3u8-aapl', encryption='xxx').

A következő szempontokat kell figyelembe venni:

* Csak nulla vagy egy titkosítási típus adható meg.
* A titkosítási típust nem kell megadni az URL-címben, ha csak egy titkosítást alkalmaztak az eszközre.
* A titkosítástípusa nem identik a kis- és nagybetűket.
* A következő titkosítási típusok adhatók meg:  
  * **cenc**: Gyakori titkosítás (PlayReady vagy Widevine)
  * **cbcs-aapl**: FairPlay
  * **CBC**: AES boríték titkosítás

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztői környezetet, és népesítse be az app.config fájlt a kapcsolatadataival, ahogy azt a Media Services fejlesztése a [.NET fájlban leírta.](media-services-dotnet-how-to-use.md) 
2. Adja hozzá a következő elemeket az app.config fájlban megadott **appSettings** szakaszhoz:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Példa

Az alábbi minta bemutatja, hogy a Media Services segítségével a FairPlay segítségével titkosított tartalmakat tud biztosítani. Ez a funkció a .NET 3.6.0-s verziójához készült Azure Media Services SDK-ban került bevezetésre. 

Írja felül a Program.cs fájlban található kódot az itt látható kóddal.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információt [ebben a cikkben](media-services-dotnet-manage-entities.md#limit-access-policies) talál.

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

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
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

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps-media-services-learning-paths"></a>Következő lépések: Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
