---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f0cc888eaf3724737e9c868c69a641094a19348c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59498387"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>A Microsoft Graph API hívása Windows asztali alkalmazások

Ez az útmutató azt ismerteti, hogyan jogkivonatainak egy natív Windows asztali .NET (XAML) alkalmazás hozzáférési token lekérése, és hívja a Microsoft Graph API vagy más API-k, amelyek hozzáférést igényelnek egy Microsoft identitásplatformja v2.0-végpont (korábbi nevén az Azure AD) fejlesztők számára.

Az útmutató befejezése után, az alkalmazás fogja tudni személyes fiókok (beleértve az Outlook.com-os, live.com, és mások) használó védett API hívása. Az alkalmazás is használja a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directory.  

> [!NOTE]
> Útmutató a Visual Studio 2015 Update 3 vagy a Visual Studio 2017 van szükség. Ezek egyikét nincs? [Töltse le az ingyenes Visual Studio 2017](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által létrehozott mintaalkalmazás működése

![Bemutatja, hogyan jöjjön létre a mintaalkalmazás által ezen oktatóanyagok működik](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

A mintaalkalmazás által létrehozott, ez az útmutató a lehetővé teszi, hogy a Windows asztali alkalmazás, amely a Microsoft Graph API vagy egy webes API-t, amely a Microsoft identity platform végpont származó jogkivonatokat fogad el. Ebben a forgatókönyvben az engedélyezési fejléc via HTTP-kérelmekre vegyen fel egy token. A Microsoft-hitelesítési tár (MSAL) kezeli a token beszerzése és megújítása.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Kezelési eléréséhez token beszerzése által védett webes API-k

A felhasználó hitelesítése után a mintaalkalmazás kap egy jogkivonatot, amely a Microsoft Graph API vagy a fejlesztők a Microsoft identity platform által védett webes API lekérdezéséhez használható.

Például a Microsoft Graph API-k az adott erőforrásokhoz való hozzáférés engedélyezése egy tokent igényelnek. Például egy jogkivonatot szükség a felhasználói profil olvasása, a felhasználó naptár elérésére vagy e-mailt. Az alkalmazás egy hozzáférési jogkivonatot kérhet erőforrások eléréséhez az API-hatókörök megadásával az MSAL használatával. A hozzáférési jogkivonatot a HTTP-hitelesítési fejléchez, a védett erőforráson végrehajtott minden hívás kerül.

MSAL gyorsítótárazását és hozzáférési tokenek frissítése, kezeli, így nem kell az alkalmazást.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagok használja:

|Kódtár|Leírás|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|A Microsoft-hitelesítési tár (MSAL.NET)|
