---
title: "Az Azure AD v2 Windows asztali első lépések – bevezetés |} Microsoft Docs"
description: "Hogyan Windows asztali .NET (XAML) alkalmazások Azure Active Directory-v2 végpontja hozzáférési jogkivonatok igénylő API meghívása"
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
ms.openlocfilehash: 4a695c00fce4deb02261ba58ec95469746bb1486
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>A Microsoft Graph API hívása korlátozása a Windows asztali alkalmazások

Ez az útmutató ismerteti, hogyan a natív Windows asztali .NET (XAML) alkalmazás szereznie egy hozzáférési jogkivonatot és hívható meg Microsoft Graph API vagy egyéb Azure Active Directory v2 végpont a hozzáférési jogkivonatok igénylő API-k.

Ez az útmutató végén az alkalmazás fogja tudni hívható meg egy védett API használatával személyes fiókok (például outlook.com, live.com és mások) valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely Azure Active Directory.  

> Ez az útmutató a Visual Studio 2015 Update 3 vagy a Visual Studio 2017 van szükség.  Nem rendelkezik? [A Visual Studio 2017 ingyenesen letölthető](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>Ez az útmutató működése

![Ez az útmutató működése](media/active-directory-mobileanddesktopapp-windowsdesktop-intro/windesktophowitworks.png)

A mintaalkalmazás, ez az útmutató által létrehozott lehetővé teszi, hogy a Windows asztali alkalmazások lekérdezéséhez Microsoft Graph API vagy egy webes API, amely az Azure Active Directory v2 végpont jogkivonatokat fogad el. Ebben az esetben jogkivonat adni a hitelesítési fejlécéhez via HTTP-kérelmekre. Token beszerzése és -megújítás kezelése a Microsoft hitelesítési könyvtár (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Webes API-k eléréséhez token beszerzési kezelése védett.

Miután a felhasználó hitelesíti magát, a mintaalkalmazást kap egy jogkivonatot, amely segítségével lekérdezési Microsoft Graph API vagy egy Microsoft Azure Active Directory v2 által biztosított webes API.

Például a Microsoft Graph API-k igényelnek olyan hozzáférési jogkivonatot, hogy az adott erőforrások – például elérése egy profil, hozzáférés felhasználói naptár olvasni és e-mailt küldeni. Az alkalmazás egy MSAL API hatókörök megadásával ezek az erőforrások eléréséhez használt jogkivonatot kérhet. Ez a jogkivonat majd hozzáadódik a HTTP Authorization fejlécet minden hívás, szemben a védett erőforrásokhoz. 

MSAL kezeli, és a hozzáférési jogkivonatok frissítése, így nem kell az alkalmazást.


### <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagok használja:

|Részletes ismertetés|Leírás|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft hitelesítési könyvtár (MSAL)|

