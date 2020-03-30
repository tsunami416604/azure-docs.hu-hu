---
title: Egyéni jogcímek kérése (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: További információ az egyéni jogcímek kérelmezése.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 44158296faaf238fd72f2360149d3d93f68c5ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085606"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Útmutató: Egyéni jogcímek kérése az MSAL használatával iOS és macOS rendszerhez

OpenID Connect lehetővé teszi, hogy opcionálisan kérheti az egyes jogcímek visszaadását a UserInfo végpontés/vagy az id token. A jogcímkérés JSON-objektumként jelenik meg, amely a kért jogcímek listáját tartalmazza. További részletek az [OpenID Connect Core 1.0-ban.](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter)

Az iOS és macOS rendszerhez tartozó Microsoft authentication library (MSAL) lehetővé teszi bizonyos jogcímek igénylését interaktív és csendes jogkivonat-beszerzési forgatókönyvekben. Ezt a `claimsRequest` paraméteren keresztül teszi.

Több forgatókönyv, ahol ez szükséges. Példa:

- Jogcímek kérése az alkalmazáshoz beállított szabványon kívül.
- A szabványos jogcímek adott kombinációinak kérése, amelyek nem adhatók meg az alkalmazás hatóköreinek használatával. Ha például egy hozzáférési jogkivonatot a hiányzó jogcímek miatt utasítanak el, az alkalmazás kérheti a hiányzó jogcímeket az MSAL használatával.

> [!NOTE]
> Az MSAL megkerüli a hozzáférési jogkivonat-gyorsítótárat, ha jogcímkérés van megadva. Fontos, hogy csak `claimsRequest` akkor adjon meg paramétert, ha további jogcímekre van szükség (szemben azzal, hogy mindig ugyanazt `claimsRequest` a paramétert adja meg minden Egyes MSAL API-hívásban).

`claimsRequest`a következő `MSALInteractiveTokenParameters`ben `MSALSilentTokenParameters` adható meg:

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
`MSALClaimsRequest`a JSON-jogcímek kérelem NSString-ábrázolásából építhető fel. 

C célkitűzés:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



További konkrét állítások kérésével is módosítható:

C célkitűzés:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

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



`MSALClaimsRequest`ezután be kell állítani a token paramétereket, és meg kell adni az egyik MSAL token beszerzésAPI-k:

C célkitűzés:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>További lépések

További információ a [hitelesítési folyamatokról és az alkalmazásforgatókönyvekről](authentication-flows-app-scenarios.md)
