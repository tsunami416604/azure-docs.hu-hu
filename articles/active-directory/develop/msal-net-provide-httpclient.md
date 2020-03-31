---
title: HttpClient & proxy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy miként biztosíthatja saját HttpClient-jét és proxyját az Azure AD-hez való csatlakozáshoz a Microsoft Authentication Library for .NET (MSAL.NET) használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: dbf08e23b2bc1f657363f69df55763437e6c8a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695049"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Saját HttpClient és proxy biztosítása MSAL.NET
[Nyilvános ügyfélalkalmazás inicializálásakor](msal-net-initializing-client-applications.md)használhatja a `.WithHttpClientFactory method` saját HttpClient.  Saját HttpClient-kiszolgáló javunkra teszi a http-proxyk részletes vezérlését, a felhasználói ügynök fejléceinek testreszabását vagy az MSAL kényszerítését egy adott HttpClient használatára (például ASP.NET Core webalkalmazások/API-k esetében).

## <a name="initialize-with-httpclientfactory"></a>Inicializálás a HttpClientFactory segítségével
A következő példa egy `HttpClientFactory` nyilvános ügyfélalkalmazás létrehozását, majd inicializálását mutatja be vele:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient és Xamarin iOS
A Xamarin iOS használataesetén ajánlott `HttpClient` olyan kezelőt `NSURLSession`létrehozni, amely kifejezetten az iOS 7 és újabb rendszerhez használja a -based handlert. MSAL.NET automatikusan `HttpClient` létrehoz `NSURLSessionHandler` egy olyan rendszert, amely az iOS 7-et és az újabbakat használja. További információért olvassa el a [Xamarin iOS dokumentációját a HttpClient számára.](/xamarin/cross-platform/macios/http-stack)