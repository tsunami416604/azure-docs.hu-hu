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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 46247d42837f8ac181d33216d2b93d28e2533c09
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482448"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Jelentkezzen be a Microsoft ASP.NET-webalkalmazás hozzáadása

Ez az útmutató bemutatja, hogyan valósíthatók meg jelentkezzen be a Microsoft az OpenID Connect használatával hagyományos webhely böngészőalapú alkalmazást egy ASP.NET MVC-megoldás használatával.

Ez az útmutató végén az alkalmazás képes fogadni, például outlook.com, live.com, és egyéb személyes fiókok bejelentkezések lesz. Ezek a fiókok is tartalmazzák a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely integrálva van az Azure Active Directoryban.

> Ez az útmutató a Visual Studio 2019 van szükség.  Nincs telepítve?  [Töltse le az ingyenes Visual Studio 2019](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által létrehozott mintaalkalmazás működése

![A mintaalkalmazás által ebben az oktatóanyagban létrehozott működését mutatja](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

A mintaalkalmazás hoz létre a forgatókönyvet, ahol a böngésző elérésére használja egy ASP.NET-webhely, amely kéri a felhasználót, hogy egy bejelentkezés gombot-on keresztül hitelesítendő alapul. A forgatókönyvben a webhely renderelésének nagy része a kiszolgálói oldalon történik.

## <a name="libraries"></a>Kódtárak

Ez az útmutató a következő kódtárakra használja:

|Erőforrástár|Leírás|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Közbenső szoftver, amely lehetővé teszi az alkalmazások számára az OpenIdConnect hitelesítésre való használatát|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Közbenső szoftver, amely lehetővé teszi az alkalmazások számára a felhasználói munkamenet fenntartását cookie-k segítségével|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Lehetővé teszi az OWIN-alapú alkalmazások számára, hogy az IIS-en fussanak az ASP.NET kérési folyamat használatával|
