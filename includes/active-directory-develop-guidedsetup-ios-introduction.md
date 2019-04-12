---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 971ae8cd44f1b345d3a71b8fa4f256c8f25ef961
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502986"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>A Microsoft Graph API meghívása iOS-alkalmazásból

Ez az útmutató ismerteti, hogyan egy natív iOS-alkalmazás (Swift) segítségével meghívhatja a API-k, amelyek a Microsoft identity platform végpontról hozzáférési jogkivonatok igényelnek. Az útmutató bemutatja, hogyan hozzáférési tokenek beszerzése és a Microsoft Graph API és más API-hívásokat használni őket.

Miután végzett az útmutatóban szereplő gyakorlatok, az alkalmazás meghívhat egy védett API-t bármely vállalat vagy szervezet, amely rendelkezik az Azure ad-ben. Az alkalmazás például outlook.com, live.com, és egyéb személyes fiókok, valamint a munkahelyi vagy iskolai fiókok használatával is felvehető a védett API-hívások.

## <a name="prerequisites"></a>Előfeltételek

- XCode-verzió 10.x a jelen útmutatóban létrehozott minta megadása kötelező. Az XCode letöltheti a [iTunes webhely](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode letöltési URL-cím").
- A [Carthage](https://github.com/Carthage/Carthage) Függőségkezelő csomagkezelés szükség.

## <a name="how-this-guide-works"></a>Az útmutató működése

![Bemutatja, hogyan jöjjön létre a mintaalkalmazás által ezen oktatóanyagok működik](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Ebben az útmutatóban a mintaalkalmazás lehetővé teszi az IOS-es alkalmazás lekérdezése a Microsoft Graph API vagy a webes API, a Microsoft identity platform végpontról jogkivonatokat fogad. Ebben a forgatókönyvben egy jogkivonatot a HTTP-kérelmekre szolgáltatással hozzáadott a **engedélyezési** fejléc. Token beszerzése és megújítása a Microsoft hitelesítési tár (MSAL) kezeli.

### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Token beszerzése a védett webes API-khoz való hozzáférés kezelése

Miután a felhasználó hitelesíti magát, a mintaalkalmazás kap jogkivonatot. A jogkivonatot a Microsoft Graph API vagy a webes API-k a Microsoft identity platform végpont által védett lekérdezésére szolgál.

API-k, például a Microsoft Graph, egy hozzáférési jogkivonatot az adott erőforrások hozzáférésének engedélyezéséhez szükséges. Jogkivonatok szükségesek egy felhasználói profil olvasása, a felhasználó naptár elérésére, e-mail küldése és így tovább. Az alkalmazás hozzáférési tokent tud kérni az MSAL használatával, és adja meg az API-hatóköröket. A hozzáférési jogkivonatot a HTTP-re kerül **engedélyezési** fejlécet minden hívás, amely a védett erőforrás ellen.

Az MSAL gyorsítótárazását és hozzáférési jogkivonatok, frissíteni, így nem kell az alkalmazás kezeli.

## <a name="libraries"></a>Kódtárak

Ez az útmutató használja a következő könyvtárban:

|Kódtár|Leírás|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|IOS-hez készült Microsoft hitelesítési tár előzetes verzióját|
