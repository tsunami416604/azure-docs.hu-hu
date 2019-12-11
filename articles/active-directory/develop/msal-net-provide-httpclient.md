---
title: HttpClient & proxy (MSAL.NET) megadása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan biztosíthatja saját HttpClient és proxyját az Azure AD-hez való csatlakozáshoz a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80822c2a745d7c9c5b3f56b7921fcc83c5c807d1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963233"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Saját HttpClient és proxy biztosítása a MSAL.NET használatával
[Egy nyilvános ügyfélalkalmazás inicializálásakor](msal-net-initializing-client-applications.md)használhatja a `.WithHttpClientFactory method` a saját HttpClient megadásához.  A saját HttpClient lehetővé teszi a speciális forgatókönyvek, például a HTTP-proxyk részletes felügyeletét, a felhasználói ügynökök fejlécének testreszabását, vagy a MSAL adott HttpClient használatára való kényszerítését (például ASP.NET Core Web Apps/API-k).

## <a name="initialize-with-httpclientfactory"></a>Inicializálás a HttpClientFactory
Az alábbi példa egy `HttpClientFactory` létrehozására, majd egy nyilvános ügyfélalkalmazás inicializálására mutat:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient és Xamarin iOS
A Xamarin iOS használata esetén ajánlott olyan `HttpClient` létrehozni, amely explicit módon a `NSURLSession`-alapú kezelőt használja az iOS 7 és újabb rendszerekhez. A MSAL.NET automatikusan létrehoz egy `HttpClient`, amely az iOS 7-es vagy újabb verzióját használja `NSURLSessionHandler`. További információért olvassa el a [Xamarin iOS-dokumentációját a HttpClient](/xamarin/cross-platform/macios/http-stack).