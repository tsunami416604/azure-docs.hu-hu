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
ms.openlocfilehash: dbf08e23b2bc1f657363f69df55763437e6c8a90
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76695049"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Saját HttpClient és proxy biztosítása a MSAL.NET használatával
[Egy nyilvános ügyfélalkalmazás inicializálásakor](msal-net-initializing-client-applications.md)a `.WithHttpClientFactory method` használatával biztosíthatja saját HttpClient.  A saját HttpClient lehetővé teszi a speciális forgatókönyvek, például a HTTP-proxyk részletes felügyeletét, a felhasználói ügynökök fejlécének testreszabását, vagy a MSAL adott HttpClient használatára való kényszerítését (például ASP.NET Core Web Apps/API-k).

## <a name="initialize-with-httpclientfactory"></a>Inicializálás a HttpClientFactory
A következő példa egy nyilvános ügyfélalkalmazás létrehozását `HttpClientFactory` mutatja be, majd inicializálja azt:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient és Xamarin iOS
A Xamarin iOS használata esetén ajánlott létrehozni egy `HttpClient` , az `NSURLSession`iOS 7-es és újabb verzióit explicit módon használó kezelőt. A MSAL.NET automatikusan létrehoz `HttpClient` egy, `NSURLSessionHandler` az iOS 7-es és újabb verzióját használót. További információért olvassa el a [Xamarin iOS-dokumentációját a HttpClient](/xamarin/cross-platform/macios/http-stack).