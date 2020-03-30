---
title: SSO között ADAL & MSAL alkalmazások (iOS /macOS) - Microsoft identity platform | Azure
description: ''
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 2fbb6e837ae898daf4bc78d5cccc75660463e8a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085419"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Útmutató: SSO az ADAL és az MSAL alkalmazások között macOS és iOS rendszeren

Az iOS-hez készült Microsoft authentication library (MSAL) megoszthatja az Egyszeri szolgáltató állapotát [az ADAL Objective-C-vel](https://github.com/AzureAD/azure-activedirectory-library-for-objc) az alkalmazások között. Az alkalmazásokat a saját tempójában telepítheti át az MSAL-ba, biztosítva, hogy a felhasználók továbbra is élvezhessék az alkalmazások közötti Egyszeri szolgáltatások előnyeit – még az ADAL és az MSAL-alapú alkalmazások keverékével is.

Ha útmutatást keres az SSO beállításával kapcsolatban az MSAL SDK-t használó alkalmazások között, olvassa el a Csendes sso több alkalmazás között című [témakört.](single-sign-on-macos-ios.md#silent-sso-between-apps) Ez a cikk az ADAL és az MSAL közötti sso-ra összpontosít.

Az SSO implementálásának sajátosságai a használt ADAL-verziótól függenek.

## <a name="adal-27x"></a>ADAL 2.7.x

Ez a szakasz az MSAL és az ADAL 2.7.x közötti SSO-különbségeket ismerteti

### <a name="cache-format"></a>Gyorsítótár formátuma

Az ADAL 2.7.x képes olvasni az MSAL gyorsítótár formátumát. Nem kell semmi különlegeset tennie az Alkalmazásközi Egyszeri tétalkalmazáshoz az ADAL 2.7.x verzióval. Azonban tisztában kell lennie a fiókazonosítók közötti különbségek, hogy a két könyvtár támogatja.

### <a name="account-identifier-differences"></a>Fiókazonosító-különbségek

Az MSAL és az ADAL különböző fiókazonosítókat használ. Az ADAL az UPN-t használja elsődleges fiókazonosítóként. Az MSAL nem megjeleníthető fiókazonosítót használ, amely egy objektumazonosítón és egy AAD-fiókok bérlői azonosítóján, valamint más típusú `sub` fiókokra vonatkozó jogcímen alapul.

Amikor egy `MSALAccount` objektumot kap az MSAL eredményében, `identifier` az egy fiókazonosítót tartalmaz a tulajdonságban. Az alkalmazásnak ezt az azonosítót kell használnia a későbbi csendes kérelmekhez.

A lehetőség `identifier` `MSALAccount` mellett az objektum egy `username`megjeleníthető azonosítót is tartalmaz. Ez az `userId` ADAL-ra van fordítva. `username`nem minősül egyedi azonosítónak, és bármikor módosítható, ezért csak az ADAL-lal való visszamenőleges kompatibilitási forgatókönyvekhez használható. Az MSAL támogatja a `username` `identifier`gyorsítótár-lekérdezéseket `identifier` a vagy a használatával, ahol ajánlott a lekérdezés.

A következő táblázat összefoglalja az ADAL és az MSAL közötti fiókazonosító-különbségeket:

| Fiókazonosító                | MSAL                                                         | ADAL 2.7.x      | Régebbi ADAL (az ADAL 2.7.x előtt) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| megjeleníthető azonosító            | `username`                                                   | `userId`        | `userId`                       |
| egyedi, nem megjeleníthető azonosító | `identifier`                                                 | `homeAccountId` | N/A                            |
| Nincs ismert fiókazonosító               | Az összes `allAccounts:` fiók lekérdezése api-n keresztül`MSALPublicClientApplication` | N/A             | N/A                            |

Ez az `MSALAccount` azonosítókat biztosító felület:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO-tól MSAL a ADAL

Ha rendelkezik Egy MSAL-alkalmazással és egy ADAL-alkalmazással, és a felhasználó először jelentkezik be az `username` MSAL-alapú alkalmazásba, az ADAL alkalmazásban egyszeri bejelentkezést kaphat, ha menti az objektumból, és átadja azt az `MSALAccount` ADAL-alapú alkalmazásnak. `userId` Az ADAL ezután az API-val csendben megtalálhatja a `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` fiókadatokat.

### <a name="sso-from-adal-to-msal"></a>SSO-tól ADAL-tól MSAL-ig

Ha rendelkezik egy MSAL-alkalmazással és egy ADAL-alkalmazással, és a felhasználó először jelentkezik be az ADAL-alapú alkalmazásba, használhatja az ADAL felhasználói azonosítókat az MSAL-ban a fiókhirdetésekhez. Ez az ADAL-ról az MSAL-ra való áttéréskor is érvényes.

#### <a name="adals-homeaccountid"></a>Az ADAL otthoni accountidazonosítója

Az ADAL 2.7.x `homeAccountId` `ADUserInformation` a tulajdonságon keresztül adja vissza az objektumban lévő értéket az eredményben:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`az ADAL-ban egyenértékű `identifier` az MSAL-ban. Ezt az azonosítót mentheti az MSAL-ban `accountForIdentifier:error:` az API-val való fiók-keresmények hez.

#### <a name="adals-userid"></a>ADAL-k`userId`

Ha `homeAccountId` nem érhető el, vagy csak a megjeleníthető azonosítóval `userId` rendelkezik, az ADAL segítségével keresheti meg a fiókot az MSAL-ban.

Az MSAL, először keressen `username` `identifier`meg egy fiókot a vagy . Mindig `identifier` használja a lekérdezés, ha van, `username` és csak tartalékként használja. Ha a fiók megtalálható, használja `acquireTokenSilent` a fiókot a hívásokban.

C célkitűzés:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL által támogatott fiókfizetési API-k:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

Ez a szakasz az MSAL és az ADAL 2.x-2.6.6 sso közötti különbségeket ismerteti.

A régebbi ADAL-verziók nem támogatják natív módon az MSAL gyorsítótár formátumát. Az ADAL-ról az MSAL-ra való zökkenőmentes áttelepítés érdekében azonban az MSAL a felhasználói hitelesítő adatok ismételt kérése nélkül tudja olvasni a régebbi ADAL gyorsítótár-formátumot.

Mivel `homeAccountId` a régebbi ADAL-verziókban nem érhető el, a `username`következő használatával kell fiókokat keresnie:

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Példa:

C célkitűzés:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Másik lehetőségként elolvashatja az összes fiókot, amely az ADAL fiókadatait is olvassa el:

C célkitűzés:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>További lépések

További információ a [hitelesítési folyamatokról és az alkalmazásforgatókönyvekről](authentication-flows-app-scenarios.md)
