---
title: "Az Azure AD v2 JS SPA interaktív telepítés – bevezetés |} Microsoft Docs"
description: "Hogyan JavaScript SPA-alkalmazások Azure Active Directory-v2 végpontja hozzáférési jogkivonatok igénylő API meghívása"
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
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: 3d195d0d67f8f82c9450ffd93767917698addee3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>A Microsoft Graph API meghívása a JavaScript egyetlen oldal alkalmazásból (SPA)

Ez az útmutató ismerteti, hogyan bejelentkezhet az egy JavaScript egyetlen oldal alkalmazás (SPA) a személyes, munkahelyi és iskolai fiókok szereznie egy hozzáférési jogkivonatot, és hívja meg a Microsoft Graph API vagy egyéb szükséges hozzáférési jogkivonatok az Azure Active Directory v2 végpont az API-k.

### <a name="how-this-guide-works"></a>Ez az útmutató működése

![Ez az útmutató által generált mintaalkalmazás működése](media/active-directory-singlepageapp-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>További információ

Ez az útmutató által létrehozott mintaalkalmazás lehetővé teszi, hogy a JavaScript SPA lekérdezni a Microsoft Graph API vagy egy webes API, amely az Azure Active Directory v2 végpont jogkivonatokat fogad el. Ebben a forgatókönyvben után a felhasználó bejelentkezik olyan hozzáférési jogkivonatot kérése és hitelesítési fejlécéhez via HTTP-kérelmek hozzáadni. Token beszerzése és -megújítás kezelése a Microsoft hitelesítési könyvtár (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Szalagtárak

Ez az útmutató használja a következő könyvtárban:

|Részletes ismertetés|Leírás|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|A JavaScript Preview Microsoft hitelesítési kódtár|

> [!NOTE]
> *msal.js* célok a *Azure Active Directory v2 végpont* -lehetővé teszi a személyes, iskolai és munkahelyi fiókokat kell jelentkezzen be, és beszerezni a jogkivonatokat. A *Azure Active Directory v2 végpont* rendelkezik [bizonyos korlátozások](..\active-directory-v2-limitations.md). Ha érdekli, csak a munkahelyi és iskolai fiókok, *adal.js* és a *V1 végpont*. Olvassa el a v1 és v2 végpontok közötti különbségek megértése a [v1-v2 összehasonlító](..\active-directory-v2-compare.md).

<!--end-collapse-->
