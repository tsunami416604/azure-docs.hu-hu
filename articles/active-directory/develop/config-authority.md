---
title: Identitásszolgáltatók konfigurálása (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan használhatja a különböző hatóságokat, például a B2C-t, a szuverén felhőket és a vendégfelhasználókat az MSAL iOS és macOS rendszerhez.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085212"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Útmutató: Az MSAL konfigurálása iOS és macOS rendszerhez különböző identitásszolgáltatók használatára

Ez a cikk bemutatja, hogyan konfigurálhatja a Microsoft hitelesítési könyvtár alkalmazását iOS-hez és macOS-hez (MSAL) a különböző hatóságokhoz, például az Azure Active Directoryhoz (Azure AD), a vállalkozások és a fogyasztók (B2C), a szuverén felhők höz és a vendégfelhasználókhoz.  Ebben a cikkben általában úgy gondolhat egy hatóságra, mint identitásszolgáltatóra.

## <a name="default-authority-configuration"></a>Alapértelmezett hatóság-konfiguráció

`MSALPublicClientApplication`a rendszer alapértelmezett szolgáltatói `https://login.microsoftonline.com/common`URL-címével van konfigurálva, amely a legtöbb Azure Active Directory (AAD) forgatókönyvhöz alkalmas. Hanem valósítmeg olyan speciális forgatókönyveket, mint a nemzeti felhők, vagy nem dolgozik a B2C-vel, nem kell módosítania.

> [!NOTE]
> Az Active Directory összevonási szolgáltatások identitásszolgáltatóként (ADFS) való modern hitelesítése nem támogatott (a részleteket lásd [az ADFS for Developers című témakörben).](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) Az ADFS összevonással támogatott.

## <a name="change-the-default-authority"></a>Az alapértelmezett jogosultsági szint módosítása

Bizonyos esetekben, például a vállalkozások és a fogyasztók (B2C) esetén előfordulhat, hogy módosítania kell az alapértelmezett szolgáltatót.

### <a name="b2c"></a>B2C

A B2C használatához a [Microsoft authentication library (MSAL)](reference-v2-libraries.md) más hatóság-konfigurációt igényel. Az MSAL egy hatóság URL-formátumát önmagában B2C-ként ismeri fel. Az elismert B2C `https://<host>/tfp/<tenant>/<policy>`jogosultságformátum `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`például . Azonban bármely más támogatott B2C-szolgáltató URL-címét is használhatja, ha a jogosultságot b2c-hatóságként kifejezetten deklarálja.

A B2C tetszőleges URL-formátumának támogatásához tetszőleges URL-címmel `MSALB2CAuthority` állítható be, például:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Minden B2C-hatóságot, amely nem használja az alapértelmezett B2C jogosultságformátumot, ismert hatóságként kell deklarálni.

Minden egyes B2C-hatóságot fel kell felvenni az ismert hatóságok listájára, még akkor is, ha a hatóságok csak eltérő politikát folytatnak.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Amikor az alkalmazás új szabályzatot kér, a hatóság URL-címét módosítani kell, mert a hatóság URL-címe minden szabályzatban eltérő. 

B2C-alkalmazás konfigurálásához `@property MSALAuthority *authority` állítsa `MSALB2CAuthority` be `MSALPublicClientApplicationConfig` a következőhöz hasonló példányt a létrehozás `MSALPublicClientApplication`előtt:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Szuverén felhők

Ha az alkalmazás egy szuverén felhőben fut, előfordulhat, `MSALPublicClientApplication`hogy módosítania kell a hatóság URL-címét a ban. A következő példa beállítja a hatóság URL-címét, hogy működjön együtt a német AAD felhővel:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Előfordulhat, hogy minden egyes szuverén felhőhöz különböző hatóköröket kell átadnia. A küldő hatókörök a használt erőforrástól függnek. Használhatja például `"https://graph.microsoft.com/user.read"` a világ felhőjében és `"https://graph.microsoft.de/user.read"` a német felhőben.

### <a name="signing-a-user-into-a-specific-tenant"></a>Felhasználó aláírása egy adott bérlőbe

Ha a hatóság URL-címe `"login.microsoftonline.com/common"`be van állítva, a felhasználó be lesz jelentkezve az otthoni bérlőbe. Előfordulhat azonban, hogy egyes alkalmazásoknak egy másik bérlőbe kell aláírniuk a felhasználót, és egyes alkalmazások csak egyetlen bérlővel működnek.

Ha a felhasználót egy adott `MSALPublicClientApplication` bérlőbe szeretné aláírni, konfigurálja egy adott jogosultsággal. Példa:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Az alábbiakban bemutatjuk, hogyan írhat be egy felhasználót egy adott bérlőbe:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Támogatott hatóságok

### <a name="msalauthority"></a>MSALHatóság

Az `MSALAuthority` osztály az MSAL-hitelesítési osztályok alap absztrakt osztálya. Ne próbálja meg létrehozni a `alloc` példányát a vagy `new`használatával. Ehelyett hozzon létre egy alosztályt`MSALAADAuthority` `MSALB2CAuthority`közvetlenül ( , `authorityWithURL:error:` ), vagy használja a gyári módszert alosztályok létrehozásához egy hatóság URL-címét használva.

A `url` tulajdonság segítségével egy normalizált hatóság URL-címét kaphatja. További paraméterek és elérési út-összetevők vagy töredékek, amelyek nem részei a hatóság nem lesz a visszaadott normalizált hatóság URL-címét.

Az alábbiakban azokat `MSALAuthority` az alosztályokat sorathatja fel, amelyeket a használni kívánt hatóságtól függően hozhat meg.

### <a name="msalaadauthority"></a>MSALAADHatóság

`MSALAADAuthority`AAD-hatóságot képvisel. A hatóság url-jének a `<port>` következő formátumúnak kell lennie, ahol nem kötelező:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CHatóság

`MSALB2CAuthority`b2c hatóságot képvisel. Alapértelmezés szerint a B2C hatóság url-jének `<port>` a következő `https://<host>:<port>/tfp/<tenant>/<policy>`formátumban kell lennie, ahol nem kötelező: . Az MSAL azonban más önkényes B2C hatósági formátumokat is támogat.

## <a name="next-steps"></a>További lépések

További információ a [hitelesítési folyamatokról és az alkalmazásforgatókönyvekről](authentication-flows-app-scenarios.md)
