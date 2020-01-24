---
title: Identitás-szolgáltatók konfigurálása (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan használhatja a különböző hatóságokat, például a B2C-t, a szuverén felhőket és a vendég felhasználókat az iOS-és macOS-MSAL.
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: aaef7c4c0e2fa295e6e20976df5cce1523a70c43
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697778"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Útmutató: az iOS-és macOS-MSAL konfigurálása különböző identitás-szolgáltatók használatához

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az iOS és macOS rendszerhez készült Microsoft Authentication Library alkalmazást (MSAL) különböző, például a Azure Active Directory (Azure AD), a vállalat és a fogyasztó (B2C), a szuverén felhők és a vendég felhasználók számára.  Ebben a cikkben általában úgy gondolja, hogy a szolgáltatót identitás-szolgáltatóként tekinti.

## <a name="default-authority-configuration"></a>Alapértelmezett szolgáltató konfigurációja

a `MSALPublicClientApplication` a `https://login.microsoftonline.com/common`alapértelmezett szolgáltatói URL-címével van konfigurálva, amely a legtöbb Azure Active Directory (HRE) forgatókönyvhöz megfelelő. Hacsak nem végez speciális forgatókönyveket, például a nemzeti felhőket, vagy a B2C-vel dolgozik, nem kell módosítania.

> [!NOTE]
> A Active Directory összevonási szolgáltatások (AD FS) as Identity Provider (ADFS) szolgáltatással való modern hitelesítés nem támogatott (lásd: [ADFS for Developers](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) for details). Az ADFS-t az összevonás támogatja.

## <a name="change-the-default-authority"></a>Az alapértelmezett szolgáltató módosítása

Bizonyos helyzetekben, például a vállalatok közötti (B2C) megoldásban előfordulhat, hogy módosítania kell az alapértelmezett szolgáltatót.

### <a name="b2c"></a>B2C

A B2C-vel való együttműködéshez a [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) más szolgáltatói konfigurációt igényel. A MSAL egy szolgáltatói URL-formátumot is felismer a B2C-ként. A B2C-hatóság felismert formátuma `https://<host>/tfp/<tenant>/<policy>`, például `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`. Más támogatott B2C-szolgáltatói URL-címeket is használhat, ha a szolgáltatót B2C-szolgáltatóként deklarálja.

A B2C tetszőleges URL-formátumának támogatásához a `MSALB2CAuthority` tetszőleges URL-címmel állítható be, például a következőképpen:

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

Az alapértelmezett B2C-szolgáltatói formátumot nem használó B2C-hatóságokat ismert hatóságként kell deklarálni.

Adja hozzá az összes különböző B2C-szolgáltatót az ismert hatóságok listájához, még akkor is, ha a hatóságok csak a szabályzatban különböznek.

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

Ha az alkalmazás új szabályzatot kér, a szolgáltatói URL-címet módosítani kell, mert a szolgáltatói URL-cím különbözik az egyes házirendek esetében. 

A B2C-alkalmazások konfigurálásához állítsa be a `@property MSALAuthority *authority`t a `MSALPublicClientApplicationConfig` `MSALB2CAuthority`-példányát a `MSALPublicClientApplication`létrehozása előtt, a következőhöz hasonlóan:

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

Ha az alkalmazás szuverén felhőben fut, előfordulhat, hogy módosítania kell a szolgáltatói URL-címet a `MSALPublicClientApplication`ban. A következő példa a szolgáltatói URL-címet állítja be a német HRE-felhővel való együttműködéshez:

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

Előfordulhat, hogy különböző hatóköröket kell átadnia az egyes szuverén felhőknek. A küldeni kívánt hatókörök a használt erőforrástól függenek. Előfordulhat például, hogy a `"https://graph.microsoft.com/user.read"`t a globális felhőben használja, és `"https://graph.microsoft.de/user.read"` a német felhőben.

### <a name="signing-a-user-into-a-specific-tenant"></a>Felhasználó aláírása egy adott bérlőn

Ha a szolgáltatói URL-cím `"login.microsoftonline.com/common"`értékre van állítva, a felhasználó bekerül a saját bérlőbe. Előfordulhat azonban, hogy egyes alkalmazásoknak egy másik bérlőbe kell írniuk a felhasználót, és egyes alkalmazások csak egyetlen Bérlővel működnek.

A felhasználó egy adott bérlőbe való aláírásához konfigurálja `MSALPublicClientApplication` egy adott szolgáltatóval. Példa:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Az alábbiakban bemutatjuk, hogyan lehet a felhasználókat egy adott bérlőhöz aláírni:

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

### <a name="msalauthority"></a>MSALAuthority

A `MSALAuthority` osztály a MSAL-szolgáltató osztályainak alap absztrakt osztálya. Ne próbálja létrehozni a példányát `alloc` vagy `new`használatával. Ehelyett hozzon létre egy alosztályt közvetlenül (`MSALAADAuthority`, `MSALB2CAuthority`), vagy a Factory metódus `authorityWithURL:error:` használatával hozzon létre alosztályokat a szolgáltatói URL-cím használatával.

A `url` tulajdonság használatával normalizált szolgáltatói URL-címet kaphat. A nem a hatóság részét képező további paraméterek és elérési utak összetevői vagy töredékei nem lesznek a visszaadott normalizált szolgáltatói URL-címben.

A következő alosztályok `MSALAuthority`, amelyek a szolgáltatótól függően hozhatók létre.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` egy HRE-szolgáltatót jelöl. A szolgáltatói URL-címnek a következő formátumban kell lennie, ahol a `<port>` megadása nem kötelező: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` a B2C-hatóságot jelöli. Alapértelmezés szerint a B2C-szolgáltatói URL-címnek a következő formátumban kell lennie, ahol a `<port>` nem kötelező: `https://<host>:<port>/tfp/<tenant>/<policy>`. A MSAL azonban más, tetszőleges B2C-szolgáltatói formátumokat is támogat.

## <a name="next-steps"></a>Következő lépések

További információ a [hitelesítési folyamatokról és az alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md)
