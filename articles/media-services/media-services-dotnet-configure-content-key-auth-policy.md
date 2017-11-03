---
title: "Konfigurálja a tartalomkulcs-hitelesítési házirend Media Services .NET SDK használatával |} Microsoft Docs"
description: "Útmutató: a Media Services .NET SDK használatával tartalomkulcs hitelesítési szabályzatának konfigurálása."
services: media-services
documentationcenter: 
author: Mingfeiy
manager: cfowler
editor: 
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 75dd9107dca215a0b31db3d44bada69210fe9ac6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>A dinamikus titkosítás: konfigurálja a tartalomkulcs-hitelesítési házirend
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
A Microsoft Azure Media Services lehetővé teszi, hogy MPEG-DASH, Smooth Streaming vagy HTTP-Live-Streaming (HLS) streamjeit az Advanced Encryption Standard (AES) (a 128 bites titkosítási kulcsok használatával) vagy [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS azt is lehetővé teszi, hogy a Widevine DRM titkosított DASH-streameket. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

A Media Services is biztosít a **kulcs/Licenctovábbítási szolgáltatása** , amelyekről az ügyfelek úgy szerezheti be AES-kulcsok vagy a PlayReady vagy Widevine-licencek számára, hogy a titkosított tartalmat.

Ha azt szeretné, a Media Services az objektum titkosítására, hozzá kell rendelni egy titkosítási kulcsot (**CommonEncryption** vagy **EnvelopeEncryption**) az eszközt (leírtak [Itt](media-services-dotnet-create-contentkey.md)) és engedélyezési házirendeket, a kulcs is konfigurálhatja, (ebben a cikkben leírtak).

Ha adatfolyam egy player kérelem érkezik, a Media Services megadott kulcsot használja az dinamikusan titkosítani az AES vagy DRM titkosítással. Az adatfolyam visszafejtése, a Windows Media player a kulcs kézbesítési szolgáltatás fog igényelni a kulcsot. Döntse el, hogy a felhasználó jogosult-e a kulcs eléréséhez, hogy a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-hitelesítési házirend rendelkezhet egy vagy több engedélyezési korlátozás: **nyissa meg a** vagy **token** korlátozás. A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. A Media Services tokeneket támogatja a **Simple Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) formátumú és **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) formátumban.

A Media Services nem nyújt Secure Token szolgáltatásokat. Hozzon létre egy egyéni STS, vagy probléma jogkivonatokat a Microsoft Azure ACS kihasználja. Az STS be kell állítani a megadott kulcs és a probléma jogcímek a lexikális elem korlátozás konfigurációs (ebben a cikkben leírtak) megadott aláírt jogkivonat létrehozásához. A Media Services kulcs kézbesítési szolgáltatás a titkosítási kulcs visszatér az ügyfélhez, ha a jogkivonat érvényes, és a jogkivonatában lévő jogcímeket megegyezzenek a tartalomkulcsot.

További információkért lásd:

[JWT jogkivonat hitelesítési](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Azure Media Services OWIN MVC-alapú alkalmazások integrálása az Azure Active Directoryban, és korlátozhatja a JWT jogcímei alapján kulcs tartalomkézbesítési](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Azure ACS használatával probléma jogkivonatok](http://mingfeiy.com/acs-with-key-services).

### <a name="some-considerations-apply"></a>Vegye figyelembe a következőket:
* Az AMS-fiók létrehozásakor egy **alapértelmezett** adatfolyam-továbbítási végpontra a fiókja hozzáadódik a **leállítva** állapotát. Start streaming a tartalmat, és használja fel a dinamikus csomagolás és a dinamikus titkosítás, az adatfolyam-továbbítási végpontjának tartalmaznia kell a **futtató** állapotát. 
* Az eszköz tartalmaznia kell egy adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá készletét. További információkért lásd: [kódolása egy eszköz](media-services-encode-asset.md).
* Töltse fel, és használja a kódolásához **AssetCreationOptions.StorageEncrypted** lehetőséget.
* Ha azt tervezi, szeretné, hogy több tartalomkulcs, amelyek ugyanazt a házirend-konfigurációt igényelnek, erősen ajánlott egyetlen engedélyezési házirend létrehozása, és újra felhasználhatja több tartalomkulcs.
* A kulcs kézbesítési szolgáltatás 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és a kapcsolódó objektumok (házirend-beállítások és korlátozásai).  Ha létrehoz egy ContentKeyAuthorizationPolicy, és adja meg, hogy a "Token" korlátozás, tesztelje azt, és majd a házirend "Megnyitás" korlátozás, előtt a házirend vált, a "Megnyitás" verziójának a házirend nagyjából 15 percig tart.
* Objektumtovábbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell az ahhoz tartozó meglévő lokátort (ha van), majd létre kell hoznia egy új lokátort.
* Jelenleg nem titkosítható progresszív letöltés.

## <a name="aes-128-dynamic-encryption"></a>AES-128, a dinamikus titkosítás
### <a name="open-restriction"></a>Nyissa meg a szoftverkorlátozó
Nyissa meg a szoftverkorlátozó azt jelenti, hogy a rendszer számára, akik egy kulcs kérést fog továbbítani a kulcsot. Ez a korlátozás tesztelési célokra hasznos lehet.

Az alábbi példa létrehoz egy megnyitott engedélyezési szabályzatot, és hozzáadja azt a tartalomkulcsot.

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

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }


### <a name="token-restriction"></a>Token korlátozása
Ez a szakasz ismerteti, hogyan hozhat létre a tartalomkulcs-hitelesítési szabályzatot, és társíthatja a tartalomkulcs. Az engedélyezési házirendben ismerteti, milyen hitelesítési követelményeket kell teljesíteni, annak meghatározásához, hogy a felhasználó jogosult-e a kulcs (például biztosítja a "ellenőrzési kulcsot" lista tartalmaz, amely a token aláírt, a kulcs) fogadására.

A token korlátozás beállítás konfigurálásához szüksége XML megadásával írhatja le a jogkivonatot engedélyezési követelményeihez. A token korlátozás konfigurációs XML-t meg kell felelnie a következő XML-séma.

#### <a id="schema"></a>Token korlátozás séma
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
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

Konfigurálásakor a **token** korlátozott házirend, meg kell adnia az elsődleges ** ellenőrző kulcs **, **kibocsátó** és **célközönség** paraméterek. A ** elsődleges hitelesítési kulcs ** tartalmazza a kulcsot, amely a token aláírt, **kibocsátó** a biztonságos jogkivonat szolgáltatás, amely kibocsátja a jogkivonatot. A **célközönség** (más néven **hatókör**) ismerteti, a célt a token vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket. 

Használata esetén **Media Services SDK for .NET**, használhatja a **TokenRestrictionTemplate** osztályra, hogy a korlátozás-token létrehozásához.
Az alábbi példa házirendet hoz létre engedélyezési jogkivonat korlátozás. Ebben a példában az ügyfélnek ehhez már van egy jogkivonatot, amely tartalmazza: aláírási kulcs (VerificationKey), a jogkivonat-kibocsátó és a szükséges jogcímeket.

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

        // Add ContentKeyAutorizationPolicy to ContentKey
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

#### <a id="test"></a>Teszt jogkivonat
Ahhoz, hogy a teszt jogkivonat alapján, amelyet a kulcshitelesítési szabályzatban használt jogkivonat korlátozása, tegye a következőket.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


## <a name="playready-dynamic-encryption"></a>PlayReady dinamikus titkosítás
Media Services lehetővé teszi, hogy konfigurálja a jogokat és korlátozásokat, amelyeket szeretne a PlayReady DRM futási időben kényszeríteni lejátszásához védett tartalmakat a felhasználó közben. 

A PlayReady a tartalmak védelmére, ha az egyikét meg kell adni a hitelesítési házirend-e egy XML-karakterlánc, amely meghatározza a [PlayReady licencsablon](media-services-playready-license-template-overview.md). A Media Services SDK for .NET a **PlayReadyLicenseResponseTemplate** és **PlayReadyLicenseTemplate** határozza meg a PlayReady Licencsablon osztályok nyújtanak segítséget.

[Ez a témakör](media-services-protect-with-drm.md) jeleníti meg a tartalom titkosítása **PlayReady** és **Widevine**.

### <a name="open-restriction"></a>Nyissa meg a szoftverkorlátozó
Nyissa meg a szoftverkorlátozó azt jelenti, hogy a rendszer számára, akik egy kulcs kérést fog továbbítani a kulcsot. Ez a korlátozás tesztelési célokra hasznos lehet.

Az alábbi példa létrehoz egy megnyitott engedélyezési szabályzatot, és hozzáadja azt a tartalomkulcsot.

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

### <a name="token-restriction"></a>Token korlátozása
A token korlátozás beállítás konfigurálásához szüksége XML megadásával írhatja le a jogkivonatot engedélyezési követelményeihez. A token korlátozás konfigurációs XML meg kell felelnie az XML-séma látható [ez](#schema) szakasz.

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

        // Add ContentKeyAutorizationPolicy to ContentKey
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


A hitelesítési házirend találhat használt token korlátozása alapján teszt tokenjének beolvasása [ez](#test) szakasz. 

## <a id="types"></a>Típusok ContentKeyAuthorizationPolicy definiálásakor használja
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

### <a id="TokenType"></a>TokenType
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Következő lépés
Most, hogy konfigurálta a tartalomkulcs hitelesítési szabályzatának, navigáljon a [objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md) témakör.

