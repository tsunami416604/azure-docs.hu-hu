---
title: HttpClient & proxy (MSAL.NET) megadása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan biztosíthatja saját HttpClient és proxyját az Azure AD-hez való csatlakozáshoz a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e13c4ed472535d2c02734949bf72a5ed42108e7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477328"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Saját HttpClient és proxy biztosítása a MSAL.NET használatával
[Egy nyilvános ügyfélalkalmazás inicializálásakor](msal-net-initializing-client-applications.md)a használatával `.WithHttpClientFactory method` biztosíthatja saját HttpClient.  A saját HttpClient lehetővé teszi a speciális forgatókönyvek, például a HTTP-proxyk részletes felügyeletét, a felhasználói ügynökök fejlécének testreszabását, vagy a MSAL adott HttpClient használatára való kényszerítését (például ASP.NET Core Web Apps/API-k).

## <a name="initialize-with-httpclientfactory"></a>Inicializálás a HttpClientFactory
A következő példa egy nyilvános ügyfélalkalmazás létrehozását mutatja be, `HttpClientFactory` majd inicializálja azt:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient és Xamarin iOS
A Xamarin iOS használata esetén ajánlott létrehozni egy, `HttpClient` az iOS 7-es és újabb verzióit explicit módon használó `NSURLSession` kezelőt. A MSAL.NET automatikusan létrehoz egy `HttpClient` , az `NSURLSessionHandler` iOS 7-es és újabb verzióját használót. További információért olvassa el a [Xamarin iOS-dokumentációját a HttpClient](/xamarin/cross-platform/macios/http-stack).