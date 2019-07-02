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
ms.openlocfilehash: 301abe95b245603e5414eef84ce74cdc8de01d19
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509864"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>A Microsoft Graph API hívása Windows asztali alkalmazások

Ez az útmutató azt ismerteti, hogyan natív Windows asztali .NET (XAML) alkalmazások használja a Microsoft Graph API meghívása a hozzáférési jogkivonatot. Az alkalmazás más API-k, amely a Microsoft identity platform hozzáférési jogkivonatok igényel a v2.0-végpont a fejlesztők is elérhető. Erre a platformra volt nevén Azure AD.

Az útmutató befejezése után, az alkalmazás fogja tudni személyes fiókok (beleértve az Outlook.com-os, live.com, és mások) használó védett API hívása. Az alkalmazás is használja a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directory.  

> [!NOTE]
> Útmutató a Visual Studio 2015 Update 3, a Visual Studio 2017 vagy Visual Studio 2019 igényel. Nem rendelkezik ezen verzióinak? [Töltse le az ingyenes Visual Studio 2019](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által létrehozott mintaalkalmazás működése

![A mintaalkalmazás által ebben az oktatóanyagban létrehozott működését mutatja](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

A mintaalkalmazás által létrehozott, ez az útmutató a lehetővé teszi, hogy a Windows asztali alkalmazás, amely a Microsoft Graph API vagy egy webes API-t, amely a Microsoft identitásplatformjához végpontról jogkivonatokat fogad. Ebben a forgatókönyvben az engedélyezési fejléc via HTTP-kérelmekre vegyen fel egy token. A Microsoft-hitelesítési tár (MSAL) kezeli a token beszerzése és megújítása.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Kezelési eléréséhez token beszerzése által védett webes API-k

A felhasználó hitelesítése után a mintaalkalmazás kap egy tokent a Microsoft Graph API vagy a fejlesztők a Microsoft identity platform által védett webes API lekérdezéséhez használhatja.

Például a Microsoft Graph API-k az adott erőforrásokhoz való hozzáférés engedélyezése egy tokent igényelnek. Például egy jogkivonatot szükség a felhasználói profil olvasása, a felhasználó naptár elérésére vagy e-mailt. Az alkalmazás egy hozzáférési jogkivonatot kérhet erőforrások eléréséhez az API-hatókörök megadásával az MSAL használatával. A hozzáférési jogkivonatot a HTTP-hitelesítési fejléchez, a védett erőforráson végrehajtott minden hívás kerül.

MSAL gyorsítótárazását és hozzáférési tokenek frissítése, kezeli, így nem kell az alkalmazást.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagok használja:

|Erőforrástár|Leírás|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|A Microsoft-hitelesítési tár (MSAL.NET)|
