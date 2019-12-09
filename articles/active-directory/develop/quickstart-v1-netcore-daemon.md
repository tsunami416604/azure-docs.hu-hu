---
title: Token beszerzése és Microsoft Graph API meghívása a konzol alkalmazás identitásával (v 1.0) | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre olyan .NET Daemon-alkalmazást, amely integrálható az Azure ad szolgáltatással az Azure AD által védett API-kkal a OAuth 2,0 használatával
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 384aa90fd4edb8f52224bdf2885208fde5789398
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920888"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-api-with-console-apps-identity-v10"></a>Gyors útmutató: token beszerzése és Microsoft Graph API meghívása a konzol alkalmazás identitásával (v 1.0) 

A [Microsoft Identity platform](v2-overview.md) a Azure Active Directory (Azure ad) fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg az olyan Microsoft API-kat, mint a Microsoft Graph, vagy olyan API-kat, amelyeket fejlesztők készítettek.

A [Microsoft Authentication Library (MSAL)](msal-overview.md) lehetővé teszi a fejlesztők számára a tokenek beszerzését a Microsoft Identity platform-végpontból a biztonságos webes API-k eléréséhez. Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik.

## <a name="next-steps"></a>Következő lépések

Az új .NET Daemon-alkalmazások esetében javasoljuk, hogy használja a Microsoft Identity platformot (v 2.0) és a MSAL a jogkivonatok beszerzésére és a biztonságos webes API-k elérésére: gyors útmutató [: token beszerzése és Microsoft Graph API meghívása egy alkalmazás identitásával a konzol alkalmazásban](quickstart-v2-netcore-daemon.md).
