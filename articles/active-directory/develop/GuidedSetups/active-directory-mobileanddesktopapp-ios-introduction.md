---
title: "Az Azure AD v2 iOS Getting Started – bevezetés |} Microsoft Docs"
description: "Hogyan iOS (Swift) alkalmazások Azure Active Directory-v2 végpontja hozzáférési jogkivonatok igénylő API meghívása"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 5fcd78eaa0c5b09b70aa973466a34556fff56071
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>Az iOS-alkalmazásoknak a Microsoft Graph API hívását

Ez az útmutató ismerteti, hogyan olyan natív iOS-alkalmazás (Swift) szereznie egy hozzáférési jogkivonatot és a Microsoft Graph API vagy egyéb Azure Active Directory v2 végpont a hozzáférési jogkivonatok igénylő API-k hívása.

Ez az útmutató végén az alkalmazás fogja tudni hívható meg egy védett API használatával személyes fiókok (például outlook.com, live.com és mások) valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely Azure Active Directory.

> ### <a name="pre-requisites"></a>Előfeltételek
> - XCode 8.x elengedhetetlen ahhoz, hogy ez az útmutató. Letöltheti az XCode [innen](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode letöltési URL-cím").
> - [Carthage](https://github.com/Carthage/Carthage) a felügyeleti csomag

### <a name="how-this-guide-works"></a>Ez az útmutató működése

![Ez az útmutató működése](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

A mintaalkalmazás, ez az útmutató által létrehozott lehetővé teszi, hogy az iOS-alkalmazás lekérdezése a Microsoft Graph API vagy egy webes API, amely az Azure Active Directory v2 végpont jogkivonatokat fogad el. Ebben az esetben jogkivonat adni a hitelesítési fejlécéhez via HTTP-kérelmekre. Token beszerzése és -megújítás kezelése a Microsoft hitelesítési könyvtár (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Webes API-k eléréséhez token beszerzési kezelése védett.

Miután a felhasználó hitelesíti magát, a mintaalkalmazást kap egy jogkivonatot, amely segítségével a Microsoft Graph API vagy egy Microsoft Azure Active Directory v2 által biztosított webes API.

Például a Microsoft Graph API-k igényelnek olyan hozzáférési jogkivonatot, hogy az adott erőforrások – például elérése egy profil, hozzáférés felhasználói naptár olvasni és e-mailt küldeni. Az alkalmazás olyan hozzáférési jogkivonatot API hatókörök megadásával MSAL használatával kérhet. Ez a jogkivonat majd hozzáadódik a HTTP Authorization fejlécet minden hívás, szemben a védett erőforrásokhoz.

MSAL kezeli, és a hozzáférési jogkivonatok frissítése, így nem kell az alkalmazást.


### <a name="libraries"></a>Szalagtárak

Ez az útmutató használja a következő könyvtárban:

|Részletes ismertetés|Leírás|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|IOS rendszerhez készült Microsoft hitelesítési könyvtár előzetes verzió|

