---
title: Webes API-kat meghívó asztali alkalmazás (webes API-k hívása) – Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre olyan asztali alkalmazást, amely webes API-kat hív meg (webes API-t hív meg)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268279"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Webes API-kat meghívó asztali alkalmazás – webes API meghívása

Most, hogy rendelkezik egy jogkivonattal, meghívhat egy védett webes API-t.

## <a name="calling-a-web-api-from-net"></a>Webes API meghívása a .NET-ről

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Webes API meghívása iOS és macOS rendszerű MSAL

A jogkivonatok beszerzésének módszerei `MSALResult` egy objektumot adnak vissza. `MSALResult`egy olyan `accessToken` tulajdonság közzététele, amely a webes API-k meghívására használható. A hozzáférési jogkivonatot fel kell venni a HTTP-engedélyezési fejlécbe, mielőtt a rendszer meghívja a védett webes API-hoz való hozzáférést.

Objective-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Több API meghívása – növekményes hozzájárulás és feltételes hozzáférés

Ha ugyanahhoz a felhasználóhoz több API-t kell meghívnia, akkor az első API-hoz kapott token után meghívhatja `AcquireTokenSilent`a többi API-tokent, és a legtöbb időt csendesen lekérdezheti.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

A beavatkozást igénylő esetek a következőket jelentik:

- A felhasználó jóváhagyta az első API-t, de most hozzá kell járulnia további hatókörökhöz (növekményes hozzájárulás)
- Az első API-nak nem volt szükség többtényezős hitelesítésre, de a következő lépés.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Áthelyezés éles környezetbe](scenario-desktop-production.md)
