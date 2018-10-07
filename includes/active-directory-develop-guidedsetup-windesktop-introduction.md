---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 9f25734259ce34caa0f0d7e844fc985f953b2795
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843117"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>A Microsoft Graph API hívása Windows asztali alkalmazások

Ez az útmutató bemutatja, hogyan natív Windows asztali .NET (XAML) alkalmazások hozzáférési jogkivonatot kapjon és a Microsoft Graph API vagy más hozzáférési jogkivonatok egy Azure Active Directory v2 végpontot a szükséges API-hívás.

Az útmutató befejezése után, az alkalmazás fogja tudni személyes fiókok (beleértve az Outlook.com-os, live.com, és mások) használó védett API hívása. Az alkalmazás is használja a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directory.  

> [!NOTE] 
> Útmutató a Visual Studio 2015 Update 3 vagy a Visual Studio 2017 van szükség.  Ezek egyikét nincs? [Töltse le az ingyenes Visual Studio 2017](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által létrehozott mintaalkalmazás működése

![Az útmutató működése](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

A mintaalkalmazás által létrehozott, ez az útmutató a lehetővé teszi, hogy a Windows asztali alkalmazás, amely a Microsoft Graph API vagy egy webes API-t, amely egy Azure Active Directory v2 végpontot származó jogkivonatokat fogad el. Ebben a forgatókönyvben az engedélyezési fejléc via HTTP-kérelmekre vegyen fel egy token. A Microsoft-hitelesítési tár (MSAL) kezeli a token beszerzése és megújítása.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Kezelési eléréséhez token beszerzése által védett webes API-k

A felhasználó hitelesítése után a mintaalkalmazás kap egy jogkivonatot, amely a Microsoft Graph API vagy egy Azure Active Directory v2 által védett webes API lekérdezéséhez használható.

Például a Microsoft Graph API-k az adott erőforrásokhoz való hozzáférés engedélyezése egy tokent igényelnek. Például egy jogkivonatot szükség a felhasználói profil olvasása, a felhasználó naptár elérésére vagy e-mailt. Az alkalmazás egy hozzáférési jogkivonatot kérhet erőforrások eléréséhez az API-hatókörök megadásával az MSAL használatával. A hozzáférési jogkivonatot a HTTP-hitelesítési fejléchez, a védett erőforráson végrehajtott minden hívás kerül. 

MSAL gyorsítótárazását és hozzáférési tokenek frissítése, kezeli, így nem kell az alkalmazást.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagok használja:

|Erőforrástár|Leírás|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|A Microsoft-hitelesítési tár (MSAL)|

