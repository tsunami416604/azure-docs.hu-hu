---
title: Bejelentkezés a felhasználókba és a Microsoft Graph API meghívása .NET Desktop-(WPF-) alkalmazásból | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre olyan .NET-es Windows-alkalmazást, amely integrálható az Azure AD-vel a bejelentkezéshez és az Azure AD által védett API-kat hívja a OAuth 2,0 használatával
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 946d376ef8af7d08b7924bd5b126c8b04e555668
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324417"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Gyors útmutató: Bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy .NET Desktop-(WPF-) alkalmazásból

A [Microsoft Identity platform](v2-overview.md) a Azure Active Directory (Azure ad) fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg az olyan Microsoft API-kat, mint a Microsoft Graph, vagy olyan API-kat, amelyeket fejlesztők készítettek.

A [Microsoft Authentication Library (MSAL)](msal-overview.md) lehetővé teszi a fejlesztők számára a tokenek beszerzését a Microsoft Identity platform-végpontból a biztonságos webes API-k eléréséhez. Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik.

Az új asztali alkalmazások esetében javasoljuk, hogy használja a Microsoft Identity platformot (v 2.0) és a MSAL a jogkivonatok beszerzésére és a biztonságos webes API-k elérésére: [Rövid útmutató: Token beszerzése és Microsoft Graph API meghívása egy Windows asztali alkalmazásból](quickstart-v2-windows-desktop.md)
