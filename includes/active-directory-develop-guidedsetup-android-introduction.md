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
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 33e2ac136ae68ee0c0ce0109a6f6934727d3a6c5
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203701"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>A felhasználók és a Microsoft Graph hívása Androidos alkalmazásokból

Ebben az oktatóanyagban megismerheti, hogyan hozhat létre Android-alkalmazás, és integrálják őket, a Microsoft identity platform lesz. Pontosabban az alkalmazás fogja bejelentkeztetni egy felhasználót, a Microsoft Graph API meghívása a hozzáférési jogkivonatot kapjon és indítson egy alapszintű a Microsoft Graph API.  

Az útmutató befejezése után, az alkalmazás fogad a bejelentkezések a személyes Microsoft-fiókok (beleértve az Outlook.com-os, live.com, és mások), és a munkahelyi vagy iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directory.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által létrehozott mintaalkalmazás működése

![Bemutatja, hogyan jöjjön létre a mintaalkalmazás által ezen oktatóanyagok működik](media/active-directory-develop-guidedsetup-android-intro/android-intro-updated.png)

Ebben a példában az alkalmazás a felhasználók, és a adatok beolvasása a felhasználók nevében.  Ezeket az adatokat fogják elérni egy távoli API-val (ebben az esetben a Microsoft Graph API), amely engedélyt igényel, és Microsoft identitásplatformja is védi.

Pontosabban:

* Az alkalmazás elindít egy weblap, a felhasználó bejelentkezni.
* Az alkalmazás fogja kiállítani egy hozzáférési jogkivonatot a Microsoft Graph API-hoz.
* A hozzáférési jogkivonatot fog szerepelni a HTTP-kérést a webes API-hoz.
* A Microsoft Graph-válasz feldolgozása.

Ebben a példában a Microsoft Authentication library for Android (MSAL) összehangolására és útmutatás nyújtása az Outlookhoz Az MSAL lesz automatikusan újítsa meg a jogkivonatok, egyszeri bejelentkezés az eszközön lévő más alkalmazások között, az segíti a fiók(ok) kezelését, és többségében feltételes hozzáférés kezelésére.

## <a name="prerequisites"></a>Előfeltételek

* Az interaktív telepítés Android Studio 3.0 használ.
* 21-én vagy újabb Android megadása kötelező (25 + ajánlott).
* Google Chrome vagy egy webes böngésző által használt egyéni lapok szükség, az Android az MSAL ezen verziójához.

## <a name="library"></a>Kódtár

Ez az útmutató a következő hitelesítési tárat használja:

|Kódtár|Leírás|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|A Microsoft-hitelesítési tár (MSAL)|
