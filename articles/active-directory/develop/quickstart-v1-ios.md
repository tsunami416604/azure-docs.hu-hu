---
title: Bejelentkezés és a Microsoft Graph meghívása iOS-ről | Azure
description: Ismerje meg, hogyan jelentkeztetheti be a felhasználókat és hívhatja meg a Microsoft Graph API-t saját iOS-alkalmazásából.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: brandwe
ms.openlocfilehash: 0f612a6f59455162d0acdffc8e05201be0a730d9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703779"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app-v10"></a>Gyors útmutató: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása iOS-alkalmazásból (v 1.0)

A [Microsoft Identity platform](v2-overview.md) a Azure Active Directory (Azure ad) fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg az olyan Microsoft API-kat, mint a Microsoft Graph, vagy olyan API-kat, amelyeket fejlesztők készítettek.

A [Microsoft Authentication Library (MSAL)](msal-overview.md) lehetővé teszi a fejlesztők számára a tokenek beszerzését a Microsoft Identity platform-végpontból a biztonságos webes API-k eléréséhez. Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik.

Az új macOS-és iOS-alkalmazások esetében javasoljuk, hogy a Microsoft Identity platformot (v 2.0) és a MSAL használja a jogkivonatok beszerzéséhez és a biztonságos webes API-k eléréséhez: gyors útmutató [: felhasználói bejelentkezés és a Microsoft Graph API meghívása iOS-vagy MacOS-alkalmazásból](quickstart-v2-ios.md).
