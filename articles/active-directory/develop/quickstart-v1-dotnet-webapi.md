---
title: Azure AD .NET webes API létrehozása a hitelesítési & engedélyezéséhez | Microsoft Docs
description: Hogyan lehet az Azure AD szolgáltatással integrálható, .NET MVC webes API-t létrehozni hitelesítéshez és engedélyezéshez?
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: abc4c05aab6efcfc55d5e4dc812ab41a3e3fc8aa
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963273"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Rövid útmutató: Az Azure AD szolgáltatással integrálható, .NET-alapú webes API létrehozása hitelesítéshez és engedélyezéshez

A [Microsoft Identity platform](v2-overview.md) a Azure Active Directory (Azure ad) fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg az olyan Microsoft API-kat, mint a Microsoft Graph, vagy olyan API-kat, amelyeket fejlesztők készítettek.

A [Microsoft Authentication Library (MSAL)](msal-overview.md) lehetővé teszi a fejlesztők számára a tokenek beszerzését a Microsoft Identity platform-végpontból a biztonságos webes API-k eléréséhez. Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik.

Az új webes API-k esetében javasoljuk, hogy a Microsoft Identity platformot (v 2.0) és a MSAL használja a jogkivonatok beszerzéséhez és a biztonságos webes API-khoz való hozzáféréshez: gyors útmutató [: bejelentkezés felvétele a Microsofttal egy ASP.net-webalkalmazásba](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
