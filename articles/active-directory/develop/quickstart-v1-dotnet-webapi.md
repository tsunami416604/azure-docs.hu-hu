---
title: .NET webes API létrehozása az Azure AD-vel a hitelesítéshez és az engedélyezéshez | Microsoft Docs
description: Hogyan lehet az Azure AD szolgáltatással integrálható, .NET MVC webes API-t létrehozni hitelesítéshez és engedélyezéshez?
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7891ea6aa2dbe10456c701b5e0216117f6ed27b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380716"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Gyors útmutató: Az Azure AD-vel integrált .NET-alapú webes API-k létrehozása hitelesítéshez és engedélyezéshez

A [Microsoft Identity platform](v2-overview.md) a Azure Active Directory (Azure ad) fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg az olyan Microsoft API-kat, mint a Microsoft Graph, vagy olyan API-kat, amelyeket fejlesztők készítettek.

A [Microsoft Authentication Library (MSAL)](msal-overview.md) lehetővé teszi a fejlesztők számára a tokenek beszerzését a Microsoft Identity platform-végpontból a biztonságos webes API-k eléréséhez. Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik.

Az új webes API-k esetében javasoljuk, hogy a Microsoft Identity platformot (v 2.0) és a MSAL használja a jogkivonatok beszerzéséhez és a biztonságos webes API-khoz való hozzáféréshez: [Rövid útmutató: Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
