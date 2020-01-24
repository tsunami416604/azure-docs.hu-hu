---
title: Microsoft-bejelentkezés hozzáadása egy ASP.NET-webalkalmazáshoz | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá Microsoft-bejelentkezést egy hagyományos webböngésző-alapú ASP.NET-megoldáshoz az OpenID Connect standard használatával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: quickstart
ms.workload: identity
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.openlocfilehash: 91c697cd11c0d411b7805e22be4cf70fe2a3e763
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703881"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Rövid útmutató: Microsoft-bejelentkezés hozzáadása egy ASP.NET-webalkalmazáshoz

A [Microsoft Identity platform](v2-overview.md) a Azure Active Directory (Azure ad) fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg az olyan Microsoft API-kat, mint a Microsoft Graph, vagy olyan API-kat, amelyeket fejlesztők készítettek.

A [Microsoft Authentication Library (MSAL)](msal-overview.md) lehetővé teszi a fejlesztők számára a tokenek beszerzését a Microsoft Identity platform-végpontból a biztonságos webes API-k eléréséhez. Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik.

## <a name="next-steps"></a>Következő lépések

Az új webalkalmazások esetében javasoljuk, hogy használja a Microsoft Identity platformot (v 2.0) és a MSAL a jogkivonatok beszerzésére és a biztonságos webes API-k elérésére. A kezdéshez tekintse meg a következőt: gyors útmutató [: bejelentkezés felvétele a Microsofttal egy ASP.net webalkalmazásba](quickstart-v2-aspnet-webapp.md) .
