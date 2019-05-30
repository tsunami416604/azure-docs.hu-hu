---
title: Adjon meg egy HttpClient és a proxy (MSAL.NET) |} Az Azure
description: Ismerje meg a saját HttpClient és a proxy szeretne csatlakozni az Azure AD-bA a Microsoft-hitelesítési tár .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72ab8a85ecc5649352382469e09d7dfd83a5ddfa
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305729"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>A saját HttpClient és a proxy MSAL.NET használatával
Amikor [inicializálása egy nyilvános ügyfélalkalmazás](msal-net-initializing-client-applications.md), használhatja a `.WithHttpClientFactory method` saját HttpClient biztosít.  Biztosítása a saját HttpClient lehetővé teszi a speciális forgatókönyvek ilyen egy HTTP proxy, a felhasználói ügynök fejlécek testreszabása, vagy egy adott HttpClient használata (például az ASP.NET Core web apps és API-k) MSAL kényszerítése.

## <a name="initialize-with-httpclientfactory"></a>A HttpClientFactory inicializálása
Az alábbi példa bemutatja, hozzon létre egy `HttpClientFactory` majd inicializálása egy nyilvános ügyfélalkalmazás vele:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient és a Xamarin IOS-es
Ha Xamarin iOS-es, javasoljuk, hogy hozzon létre egy `HttpClient` , amely explicit módon használja a `NSURLSession`-kezelő iOS 7 és újabb-alapú. MSAL.NET automatikusan létrehoz egy `HttpClient` használó `NSURLSessionHandler` az iOS 7 és újabb. További információkért olvassa el a [Xamarin IOS-es dokumentációját HttpClient](/xamarin/cross-platform/macios/http-stack).