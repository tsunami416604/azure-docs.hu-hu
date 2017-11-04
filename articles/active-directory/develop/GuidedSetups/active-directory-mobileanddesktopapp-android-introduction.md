---
title: "Az Azure AD v2 Android első lépések – bevezetés |} Microsoft Docs"
description: "Android-alkalmazás hogyan szereznie egy hozzáférési jogkivonatot és hívható meg Microsoft Graph API-val vagy a hozzáférési jogkivonatok az Azure Active Directory v2 végpont igénylő API-k"
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
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>A Microsoft Graph API hívása az Android-alkalmazás

Ez az útmutató ismerteti, hogyan natív Android-alkalmazás szereznie egy hozzáférési jogkivonatot és hívható meg Microsoft Graph API vagy egyéb Azure Active Directory v2 végpont a hozzáférési jogkivonatok igénylő API-k.

Ez az útmutató végén az alkalmazás fogja tudni hívható meg egy védett API használatával személyes fiókok (például outlook.com, live.com és mások) valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely Azure Active Directory.  

### <a name="how-this-sample-works"></a>Ez a minta működése
![Ez a minta működése](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

Ez az útmutató által létrehozott minta egy olyan forgatókönyvet, ahol egy Android-alkalmazás, amely az Azure Active Directory v2 végpont – ebben az esetben a Microsoft Graph API származó jogkivonatokat fogad el egy webes API lekérdezésére szolgál alapul. Ebben az esetben jogkivonat adni a hitelesítési fejlécéhez via HTTP-kérelmekre. Token beszerzése és -megújítás kezelése a Microsoft hitelesítési könyvtár (MSAL).

### <a name="pre-requisites"></a>Előfeltételek
* Ez az interaktív telepítés Android Studio összpontosít, de bármely olyan Android-alkalmazás fejlesztői környezetben is elfogadható. 
* Android SDK 21 vagy újabb rendszer szükséges (SDK 25 ajánlott).
* Google Chrome vagy egy webes böngésző egyéni lapok használatával szükség ebben a kiadásban Microsoft hitelesítési könyvtár (MSAL) Android rendszerhez.

> Megjegyzés: Google Chrome nem szerepel a Visual Studio-emulátor Android rendszerhez. Azt javasoljuk, hogy ez a kód egy emulátorának API 25 vagy lemezkép API 21 vagy újabb Google Chrome telepített tesztelése.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>Egy védett Web API eléréséhez token beszerzési kezelésének módját

Miután a felhasználó hitelesíti magát, a mintaalkalmazást kap egy jogkivonatot, amely segítségével lekérdezési Microsoft Graph API vagy egy Microsoft Azure Active Directory v2 által biztosított webes API.

Például a Microsoft Graph API-k igényelnek olyan hozzáférési jogkivonatot, hogy az adott erőforrások – például elérése egy profil, hozzáférés felhasználói naptár olvasni és e-mailt küldeni. Az alkalmazás egy MSAL API hatókörök megadásával ezek az erőforrások eléréséhez használt jogkivonatot kérhet. Ez a jogkivonat majd hozzáadódik a HTTP Authorization fejlécet minden hívás, szemben a védett erőforrásokhoz. 

MSAL kezeli, és a hozzáférési jogkivonatok frissítése, így nem kell az alkalmazást.

### <a name="libraries"></a>Szalagtárak

Ez az útmutató a következő tárakat használ:

|Részletes ismertetés|Leírás|
|---|---|
|[com.microsoft.Identity.Client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft hitelesítési könyvtár (MSAL)|
