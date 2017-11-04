---
title: "Az Azure AD v2 ASP.NET webalkalmazás-kiszolgáló első lépések – bevezetés |} Microsoft Docs"
description: "A Microsoft bejelentkezés végrehajtási egy ASP.NET-megoldás a hagyományos böngészőalapú webalkalmazás a szabványos OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 8062923b6270ec6253dc231a3db4333cf4666b42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Bejelentkezés a Microsoft ASP.NET webalkalmazás hozzáadása

Ez az útmutató bemutatja, hogyan egy ASP.NET MVC-megoldással egy hagyományos webböngésző-alapú webalkalmazás OpenID Connect használatával a Microsoft bejelentkezési megvalósításához. 

Ez az útmutató végén az alkalmazás fogja tudni fogadja el a bejelentkezési modulok a személyes fiókok (például outlook.com, live.com és mások), valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directoryval integrált. 

> Ez az útmutató a Visual Studio 2015 Update 3 vagy a Visual Studio 2017 van szükség.  Nem rendelkezik?  [A Visual Studio 2017 ingyenesen letölthető](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Ez az útmutató működése

![Ez az útmutató működése](media/active-directory-serversidewebapp-aspnetwebappowin-intro/aspnetbrowsergeneral.png)

Ez az útmutató alapján a forgatókönyvet, ahol a böngésző hozzáfér-e ASP.NET-webhely, a felhasználó egy bejelentkezési gomb segítségével kér. Ebben a forgatókönyvben a lehet megjeleníteni a weblapot a munka nagyobb része a kiszolgáló oldalán következik be.

## <a name="libraries"></a>Szalagtárak

Ez az útmutató a következő tárakat használ:

|Részletes ismertetés|Leírás|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Alkalmazott szoftver, amely lehetővé teszi az alkalmazás OpenIdConnect használja a hitelesítéshez|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Alkalmazott szoftver, amely lehetővé teszi az alkalmazás felhasználói munkamenet cookie-k használata karbantartása|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Lehetővé teszi, hogy a OWIN-alapú alkalmazások futtatásához az IIS használatával az ASP.NET-kérelem folyamata|

