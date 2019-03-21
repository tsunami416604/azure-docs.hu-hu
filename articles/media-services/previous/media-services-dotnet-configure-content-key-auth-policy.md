---
title: Konfigurálja a tartalomkulcs-hitelesítési házirend a Media Services .NET SDK használatával |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálja a tartalomkulcs-engedélyezési házirend a Media Services .NET SDK használatával.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako;mingfeiy
ms.openlocfilehash: d949bf02e9b629b9c525f488f9692ad09dcede4a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57846905"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>A dinamikus titkosítás: A tartalomkulcs-hitelesítési szabályzat konfigurálása
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 Az Azure Media Services segítségével MPEG-DASH, Smooth Streaming vagy HTTP Live Streaming (HLS), 128 bites titkosítási kulcsok használatával az az Advanced Encryption Standard (AES) védett Streamek biztosításához vagy [PlayReady digitális jogkezelési (technológia DRM) ](https://www.microsoft.com/playready/overview/). A Media Services emellett eljuttatható Widevine DRM-mel az titkosított DASH-streameket. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

Media Services egy kulcs/licenckézbesítési szolgáltatást, amelyből az ügyfelek szerezheti be az AES-kulcsok vagy a titkosított tartalom lejátszása PlayReady/Widevine-licencek is biztosít.

Ha azt szeretné, hogy a Media Services az objektum titkosítására, hozzá kell rendelni egy titkosítási kulcsot (CommonEncryption vagy EnvelopeEncryption) az eszközt. További információkért lásd: [Tartalomkulcsok létrehozása .NET-tel](media-services-dotnet-create-contentkey.md). Azt is konfigurálnia kell engedélyezési házirendek, a kulcs (ebben a cikkben leírt).

Adatfolyam-lejátszó kér, amikor a Media Services a megadott kulcs használatával dinamikusan titkosítani az AES vagy DRM-titkosítás segítségével. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult kulcs lekérése, a szolgáltatás kiértékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat. A lehetőségek a következők nyitott vagy jogkivonat korlátozás. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services jogkivonatokat támogatja az egyszerű webes jogkivonat ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) formátum és a JSON Web Token ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) formátumban.

A Media Services STS nem biztosít. Hozzon létre egy egyéni STS, vagy probléma jogkivonatok Azure Access Control Service szolgáltatást használja. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációját (ebben a cikkben leírt) megadott aláírt jogkivonat létrehozásához. Ha a jogkivonat érvényes, és a jogkivonat jogcímeiben megfelelnek a tartalomkulcs, a Media Services kulcstovábbítást a titkosítási kulcsot az ügyfélnek ad vissza.

További információkért tekintse át a következő cikkeket:

- [JWT jogkivonat-hitelesítés](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Az Azure Media Services OWIN MVC-alapú alkalmazások integrálása az Azure Active Directory és a JWT jogcímszolgáltatói alapján kulcs tartalomkézbesítési korlátozása](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Bizonyos szempontokat kell figyelembe venni
* A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom streamelésének megkezdéséhez, és a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpont kell "Fut" állapotban. 
* Az objektumnak tartalmaznia kell egy adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá készletét. További információkért lásd: [kódolása](media-services-encode-asset.md).
* Töltse fel, és az objektumok kódolása a AssetCreationOptions.StorageEncrypted lehetőség használatával.
* Ha azt tervezi, szeretné, hogy több tartalomkulcs, amelyek a ugyanazon házirend-konfigurációt igényelnek, azt javasoljuk, hogy hozzon létre egy egyetlen engedélyezési házirendet, és újra felhasználhatja a több tartalomkulcs.
* A kulcstovábbítást 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és a kapcsolódó objektumok (házirend-beállításokat, és korlátozásai). Hozzon létre ContentKeyAuthorizationPolicy, és adja meg, hogy a jogkivonat korlátozás, tesztelheti, és frissíti a szabályzatot a megnyitott korlátozás. Ez a folyamat a szabályzat nyitott verziójának a házirend-kapcsolók nagyjából 15 percet vesz igénybe.
* Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.
* Jelenleg nem titkosítható a progresszív letöltést.
* A Media Services streaming endpoint előzetes válaszként, mint a helyettesítő karaktert a CORS "Access-Control-Allow-Origin" fejléc értékének beállítása "\*". Ez az érték nagyszerűen működik a legtöbb szereplő, köztük az Azure Media Player Roku és JWPlayer és mások. Azonban nem működnek bizonyos lejátszók dashjs használó, mert a hitelesítő adatok módban "include", a saját dashjs XMLHttpRequest nem teszi lehetővé a helyettesítő karaktert tartalmazó "\*" "Access-Control-Allow-Origin" értéket. Ezt a korlátozást a dashjs megkerüléséhez Ha az ügyfél egy egyetlen tartományból a Media Services megadhatja, hogy a tartomány az előzetes válaszfejléc. Segítségért nyisson meg egy támogatási jegyet az Azure Portalon keresztül.

## <a name="aes-128-dynamic-encryption"></a>AES-128, a dinamikus titkosítás
### <a name="open-restriction"></a>Nyissa meg a korlátozás
Nyissa meg a korlátozás azt jelenti, hogy a rendszer továbbítja a kulcsot bárki számára legfontosabb kérést küld. Ez a korlátozás tesztelési célokra hasznos lehet.

Az alábbi példa létrehoz egy megnyitott engedélyezési szabályzatot, és hozzáadja azt a tartalomkulcs:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Token korlátozás
Ez a szakasz ismerteti, hogyan hozhat létre a tartalomkulcs-hitelesítési szabályzatot, és társítsa a tartalomkulcs. Az engedélyezési házirend ismerteti, milyen engedélyezési követelményeinek teljesülnie kell meghatározni, ha a felhasználó jogosult kapják meg a kulcsot. Például tartalmaz ellenőrzési kulcslistájába, amely a token írták-e a kulcsot?

A token korlátozás beállítás konfigurálásához szüksége XML a jogkivonat hitelesítési követelmények leírása. A token korlátozás konfigurációs XML meg kell felelnie a következő XML-séma:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
Amikor a jogkivonattal korlátozott szabályzatokat konfigurál, az elsődleges ellenőrzőkulcs, a kibocsátó és a célközönség paramétereket kell megadnia. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e. A nem az STS-ben a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcstovábbítást ellenőrzi, hogy ezeket az értékeket a jogkivonat egyezik a sablonban szereplő értékeket.

A Media Services SDK for .NET használatakor TokenRestrictionTemplate osztály használhatja a korlátozás-token létrehozásához.
Az alábbi példa egy token korlátozás hoz létre az engedélyezési házirend. Ebben a példában az ügyfél e aláíró kulcs (VerificationKey), a jogkivonat kibocsátója és a szükséges jogcímeket tartalmazó jogkivonatot.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

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
```
#### <a name="test-token"></a>Teszt jogkivonat
Egy teszt jogkivonat alapján, amelyet a kulcshitelesítési szabályzatban használt jogkivonat korlátozása lekéréséhez tegye a következőket:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>A dinamikus PlayReady-titkosítás
Media Services segítségével konfigurálhatja a jogokat és korlátozásokat, amelyeket szeretne a PlayReady DRM futtatási kényszerítése, ha egy felhasználó megpróbál játssza le a védett tartalmak. 

Ha Ön tartalmai védelmét a PlayReady, a dolgokat, meg kell adnia az engedélyezési házirendben egyik XML-karakterlánc, amely meghatározza a [PlayReady-licencsablon](media-services-playready-license-template-overview.md). A Media Services SDK for .NET a PlayReadyLicenseResponseTemplate és PlayReadyLicenseTemplate osztályok segítségével meghatározhatja a PlayReady-licencsablon.

A PlayReady és Widevine tartalmak kezelésével kapcsolatos információkért lásd: [használja a PlayReady és/vagy Widevine dynamic common Encryption titkosítás](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Nyissa meg a korlátozás
Nyissa meg a korlátozás azt jelenti, hogy a rendszer továbbítja a kulcsot bárki számára legfontosabb kérést küld. Ez a korlátozás tesztelési célokra hasznos lehet.

Az alábbi példa létrehoz egy megnyitott engedélyezési szabályzatot, és hozzáadja azt a tartalomkulcs:

```csharp
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

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Token korlátozás
A token korlátozás beállítás konfigurálásához szüksége XML a jogkivonat hitelesítési követelmények leírása. A token korlátozás konfigurációs XML meg kell felelnie az XML-séma, a "Token korlátozás schema" szakaszban látható.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

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
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
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
```

A kulcshitelesítési szabályzatban használt jogkivonat korlátozás alapján teszt jogkivonat beszerzéséhez tekintse meg a "[teszt jogkivonat](#test-token)" szakaszban. 

## <a id="types"></a>ContentKeyAuthorizationPolicy meghatározása során használt típusok
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>TokenType

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
Most, hogy konfigurálta a tartalomkulcs hitelesítési szabályzatának, lásd: [objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md).

