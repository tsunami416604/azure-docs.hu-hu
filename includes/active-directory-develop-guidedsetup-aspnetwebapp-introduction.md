---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: bc439cbe5e690077213b5d7953a4e74488988c3b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988544"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Jelentkezzen be a Microsoft ASP.NET-webalkalmazás hozzáadása

Ez az útmutató bemutatja, hogyan valósíthatók meg jelentkezzen be a Microsoft az OpenID Connect használatával hagyományos webhely böngészőalapú alkalmazást egy ASP.NET MVC-megoldás használatával.

Ez az útmutató végén az alkalmazás fogja tudni fogadja el a bejelentkezések a személyes fiókok (beleértve az Outlook.com-os, live.com, és mások) valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely integrálva van az Azure Active Directory.

> Ez az útmutató a Visual Studio 2015 Update 3 vagy a Visual Studio 2017 van szükség.  Nincs telepítve?  [Töltse le ingyen a Visual Studio 2017-et](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által létrehozott mintaalkalmazás működése

![Az útmutató működése](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

Ez az útmutató által létrehozott alkalmazás a forgatókönyvet, amelyben egy felhasználó használ kér egy bejelentkezés gombot-on keresztül hitelesítendő felhasználó egy ASP.NET-webhely eléréséhez a böngészőben alapul. A forgatókönyvben a webhely renderelésének nagy része a kiszolgálói oldalon történik.

## <a name="libraries"></a>Kódtárak

Ez az útmutató a következő kódtárakra használja:

|Erőforrástár|Leírás|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Közbenső szoftver, amely lehetővé teszi az alkalmazások számára az OpenIdConnect hitelesítésre való használatát|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Közbenső szoftver, amely lehetővé teszi az alkalmazások számára a felhasználói munkamenet fenntartását cookie-k segítségével|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Lehetővé teszi az OWIN-alapú alkalmazások számára, hogy az IIS-en fussanak az ASP.NET kérési folyamat használatával|
