---
title: Egyéni jogcímek kérése (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: További információ az egyéni jogcímek igényléséről.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 4974fe3b387683f662d7a7b4f3ccb4935153f07e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80883096"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Útmutató: egyéni jogcímek kérése az iOS és a macOS rendszerhez készült MSAL használatával

Az OpenID Connect segítségével igény szerint kérheti az egyes jogcímek visszaadását a UserInfo-végpontból és/vagy az azonosító jogkivonatban. A jogcím-kérelmek JSON-objektumként jelennek meg, amely tartalmazza a kért jogcímek listáját. További részletekért lásd: [OpenID Connect Core 1,0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) .

Az iOS és a macOS rendszerhez készült Microsoft Authentication Library (MSAL) lehetővé teszi, hogy konkrét jogcímeket kérjen az interaktív és a csendes jogkivonat-beszerzési forgatókönyvekben. Ezt a `claimsRequest` paraméterrel végezheti el.

Több forgatókönyv is van, ahol ez szükséges. Például:

- Az alkalmazás szabványos készletén kívüli jogcímek igénylése.
- A standard jogcímek adott kombinációinak kérése, amelyek nem adhatók meg hatókörökkel az alkalmazáshoz. Ha például egy hozzáférési jogkivonat a hiányzó jogcímek miatt elutasításra kerül, az alkalmazás a MSAL használatával kérheti le a hiányzó jogcímeket.

> [!NOTE]
> A MSAL megkerüli a hozzáférési jogkivonat gyorsítótárát, ha meg van adva jogcím-kérelem. Fontos, hogy csak akkor adja `claimsRequest` meg a paramétereket, ha további jogcímek szükségesek (az egyes `claimsRequest` MSAL API-hívásokban mindig ugyanazt a paramétert biztosítjuk).

`claimsRequest`a és `MSALInteractiveTokenParameters`a esetén `MSALSilentTokenParameters` adható meg:

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`a JSON-jogcímek kérelmének NSString-reprezentációja alapján hozható létre. 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



További konkrét jogcímek igénylésével is módosítható:

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`Ezután be kell állítani a jogkivonat paramétereit, és meg kell adni az egyik MSAL jogkivonat-beszerzési API-nak:

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>További lépések

További információ a [hitelesítési folyamatokról és az alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md)
