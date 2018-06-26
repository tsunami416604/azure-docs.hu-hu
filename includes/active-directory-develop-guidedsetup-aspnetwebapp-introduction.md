---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 10f5eb239fc6320e7597e5f1380f4df8873ab3b6
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943436"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Bejelentkezés a Microsoft ASP.NET webalkalmazás hozzáadása

Ez az útmutató bemutatja, hogyan egy ASP.NET MVC-megoldással egy hagyományos webböngésző-alapú webalkalmazás OpenID Connect használatával a Microsoft bejelentkezési megvalósításához. 

Ez az útmutató végén az alkalmazás fogja tudni fogadja el a bejelentkezést a személyes fiókok (például outlook.com, live.com és mások) valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directoryval integrált. 

> Ez az útmutató a Visual Studio 2015 Update 3 vagy a Visual Studio 2017 van szükség.  Nem rendelkezik?  [A Visual Studio 2017 ingyenesen letölthető](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által generált mintaalkalmazás működése

![Ez az útmutató működése](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

Ez az útmutató által létrehozott mintaalkalmazást a forgatókönyvet, ahol a felhasználó használja a böngészőben egy ASP.NET-webhely kér a felhasználó egy bejelentkezési gomb segítségével alapul. Ebben a forgatókönyvben a lehet megjeleníteni a weblapot a munka nagyobb része a kiszolgáló oldalán következik be.

## <a name="libraries"></a>Szalagtárak

Ez az útmutató a következő tárakat használ:

|Részletes ismertetés|Leírás|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Alkalmazott szoftver, amely lehetővé teszi az alkalmazás OpenIdConnect használja a hitelesítéshez|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Alkalmazott szoftver, amely lehetővé teszi az alkalmazás felhasználói munkamenet cookie-k használata karbantartása|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Lehetővé teszi, hogy a OWIN-alapú alkalmazások futtatásához az IIS használatával az ASP.NET-kérelem folyamata|

