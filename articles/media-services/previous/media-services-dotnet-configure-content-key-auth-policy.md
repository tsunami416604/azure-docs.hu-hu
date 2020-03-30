---
title: Tartalomkulcs-engedélyezési házirend konfigurálása a Media Services .NET SDK | Microsoft dokumentumok
description: Megtudhatja, hogy miként konfigurálhat engedélyezési házirendet egy tartalomkulcshoz a Media Services .NET SDK használatával.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 58d52cd194ca4391c61f2477189984273df1198a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251205"
---
# <a name="configure-a-content-key-authorization-policy"></a>Tartalomkulcs-engedélyezési házirend konfigurálása

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 Az Azure Media Services segítségével MPEG-DASH, Smooth Streaming és HTTP Live Streaming (HLS) streameket biztosíthat a speciális titkosítási szabvány (AES) használatával 128 bites titkosítási kulcsok vagy [PlayReady digitális jogkezelés (DRM)](https://www.microsoft.com/playready/overview/)használatával. A Media Services segítségével a Widevine DRM-mel titkosított DASH-adatfolyamokat is szállíthatja. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

A Media Services egy kulcs-/licenckézbesítési szolgáltatást is biztosít, amelyből az ügyfelek AES-kulcsokat vagy PlayReady/Widevine licenceket szerezhetnek a titkosított tartalom lejátszásához.

Ha azt szeretné, hogy a Media Services titkosítson egy eszközt, hozzá kell rendelnie egy titkosítási kulcsot (CommonEncryption vagy EnvelopeEncryption) az eszközhöz. További információt a [ContentKeys létrehozása a .NET-tel című témakörben talál.](media-services-dotnet-create-contentkey.md) A kulcs engedélyezési házirendjeit is konfigurálnia kell (a jelen cikkben leírtak szerint).

Amikor egy lejátszó adatfolyamot kér, a Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat AES vagy DRM titkosítással. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs beakése, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-engedélyezési házirendhez egy vagy több engedélyezési korlátozás vonatkozhat. A beállítások nyitott vagy token korlátozás. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services támogatja az egyszerű webes token[(SWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)és a JSON Web Token[(JWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)formátumú tokeneket.

A Media Services nem biztosít STS-t. Létrehozhat egy egyéni STS-t, vagy használhatja az Azure Access Control Service-t jogkivonatok kiállításához. Az STS-t úgy kell konfigurálni, hogy hozzon létre egy jogkivonatot a megadott kulccsal, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket (a jelen cikkben leírtak szerint). Ha a jogkivonat érvényes, és a jogkivonatban lévő jogcímek megegyeznek a tartalomkulcshoz konfigurált jogcímekkel, a Media Services-kulcskézbesítési szolgáltatás visszaadja a titkosítási kulcsot az ügyfélnek.

További információkért tekintse át a következő cikkeket:

- [JWT token hitelesítése](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrálja az Azure Media Services OWMedia MVC-alapú alkalmazást az Azure Active Directoryval, és korlátozza a tartalomkulcs kézbesítését a JWT-jogcímek alapján](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Néhány szempont vonatkozik
* A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom streamelésének megkezdéséhez és a dinamikus csomagolás és a dinamikus titkosítás előnyeinek kihasználásához a streamelési végpontnak "Futás" állapotban kell lennie. 
* Az eszköznek adaptív sávszélességű MP4-fájlokat vagy adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia. További információt az [Eszköz kódolása](media-services-encode-asset.md)című témakörben talál.
* Töltse fel és kódolja az eszközöket az AssetCreationOptions.StorageEncrypted beállítás használatával.
* Ha azt tervezi, hogy több tartalomkulcsok, amelyek ugyanazt a házirend-konfigurációt, azt javasoljuk, hogy hozzon létre egy engedélyezési szabályzatot, és újra több tartalomkulcsok.
* A kulcskézbesítési szolgáltatás 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és a hozzá kapcsolódó objektumokat (házirend-beállítások és korlátozások). Létrehozhat ContentKeyAuthorizationPolicy-et, és megadhatja, hogy tokenkorlátozást használjon, tesztelje, majd frissítse a házirendet a nyílt korlátozásra. Ez a folyamat nagyjából 15 percet vesz igénybe, mielőtt a házirend átvált a házirend nyílt verziójára.
* Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.
* Jelenleg nem lehet titkosítani a progresszív letöltéseket.
* A Media Services streamelési végpontja a CORS "Access-Control-Allow-Origin" fejlécének\*értékét helyettesítő karakterként " állítja be. Ez az érték jól működik a legtöbb játékos, beleértve az Azure Media Player, Roku és JWPlayer, és mások. Azonban néhány játékos, amely dashjs nem működik, mert a hitelesítő adatok mód beállítása "include", XMLHttpRequest saját dashjs nem teszi lehetővé a helyettesítő "\*", mint az értéke "Access-Control-Allow-Origin". A dashjs ezen korlátozás ának megkerüléseként, ha az ügyfelet egyetlen tartományból üzemelteti, a Media Services megadhatja az adott tartományt az elővizsgálati válasz fejlécében. Ha segítségre van szüksége, nyisson meg egy támogatási jegyet az Azure Portalon keresztül.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dinamikus titkosítás
### <a name="open-restriction"></a>Nyílt korlátozás
A nyílt korlátozás azt jelenti, hogy a rendszer mindenkinek átadja a kulcsot, aki kulcskérést tesz. Ez a korlátozás tesztelési célokra hasznos lehet.

A következő példa létrehoz egy nyílt engedélyezési házirendet, és hozzáadja a tartalomkulcshoz:
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

### <a name="token-restriction"></a>Token korlátozása
Ez a szakasz azt ismerteti, hogyan hozhat létre tartalomkulcs-engedélyezési házirendet, és hogyan társíthatja azt a tartalomkulcshoz. Az engedélyezési házirend leírja, hogy milyen engedélyezési követelményeknek kell megfelelni annak megállapításához, hogy a felhasználó jogosult-e a kulcs fogadására. Az ellenőrzőkulcs-lista például tartalmazza azt a kulcsot, amelyhez a jogkivonatot aláírták?

A tokenkorlátozási beállítás konfigurálásához XML-t kell használnia a jogkivonat engedélyezési követelményeinek leírásához. A tokenkorlátozás konfigurációs XML-fájljának meg kell felelnie a következő XML-sémának:
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
A jogkivonat-korlátozott házirend konfigurálásakor meg kell adnia az elsődleges ellenőrzési kulcsot, a kibocsátót és a közönség paramétereit. Az elsődleges ellenőrzési kulcs tartalmazza azt a kulcsot, amelyhez a jogkivonat ot aláírták. A kibocsátó az STS, amely kiadja a jogkivonatot. A közönség (más néven hatókör) a jogkivonat vagy az erőforrás szándékát írja le, amelyhez a jogkivonat engedélyezi a hozzáférést. A Media Services-kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a jogkivonatban megegyeznek-e a sablonban lévő értékekkel.

Ha a Media Services SDK-t használja a .NET-hez, a TokenRestrictionTemplate osztály segítségével létrehozhatja a korlátozási jogkivonatot.
A következő példa létrehoz egy hitelesítési szabályzatot jogkivonat-korlátozással. Ebben a példában az ügyfélnek be kell mutatnia egy jogkivonatot, amely egy aláíró kulcsot (VerificationKey), egy jogkivonat-kibocsátót és a szükséges jogcímeket tartalmaz.
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
#### <a name="test-token"></a>Teszt token
Ha a kulcsengedélyezési házirendhez használt jogkivonat-korlátozáson alapuló tesztjogkivonatot szeretne beszerezni, tegye a következőket:
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

## <a name="playready-dynamic-encryption"></a>PlayReady dinamikus titkosítás
A Media Services segítségével konfigurálhatja azokat a jogokat és korlátozásokat, amelyeket a PlayReady DRM futásidejének érvényesítenie kell, amikor a felhasználó védett tartalmat próbál lejátszani. 

Amikor a PlayReady segítségével védi a tartalmat, az engedélyezési házirendben megkell adnia az egyik elemet egy XML-karakterlánc, amely meghatározza a [PlayReady licencsablont.](media-services-playready-license-template-overview.md) A Media Services SDK for .NET, a PlayReadyLicenseResponseTemplate és A PlayReadyLicenseTemplate osztályok segítenek meghatározni a PlayReady licencsablon.

A tartalom PlayReady és Widevine használatával történő titkosításáról a [PlayReady és/vagy a Widevine dinamikus gyakori titkosításának használata című](media-services-protect-with-playready-widevine.md)témakörben olvashat.

### <a name="open-restriction"></a>Nyílt korlátozás
A nyílt korlátozás azt jelenti, hogy a rendszer mindenkinek átadja a kulcsot, aki kulcskérést tesz. Ez a korlátozás tesztelési célokra hasznos lehet.

A következő példa létrehoz egy nyílt engedélyezési házirendet, és hozzáadja a tartalomkulcshoz:

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

### <a name="token-restriction"></a>Token korlátozása
A tokenkorlátozási beállítás konfigurálásához XML-t kell használnia a jogkivonat engedélyezési követelményeinek leírásához. A tokenkorlátozás konfigurációs XML-fájljának meg kell felelnie a "Token korlátozási séma" szakaszban látható XML-sémának.

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

A kulcsengedélyezési szabályzathoz használt jogkivonat-korlátozáson alapuló tesztjogkivonat lekéréséhez tekintse meg a "[Tesztjogkivonat](#test-token)" című szakaszt. 

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>A ContentKeyAuthorizationPolicy definiálásakor használt típusok
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType (ContentKeyRestrictionType)

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType típus

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a name="tokentype"></a><a id="TokenType"></a>TokenType (TokenType)

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
Most, hogy konfigurálta a tartalomkulcs engedélyezési házirendjét, olvassa el [az Eszközkézbesítési házirend konfigurálása című témakört.](media-services-dotnet-configure-asset-delivery-policy.md)

