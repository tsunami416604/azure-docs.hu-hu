---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8c2dc41fde9387da291b6e4a6c8a6220ae62b514
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59503111"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>A felhasználók és a Microsoft Graph API meghívása egy JavaScript egyoldalas alkalmazásból (SPA)

Ez az útmutató bemutatja, hogyan a panelt a JavaScript egyoldalas alkalmazás (SPA) bejelentkezhet a személyes, munkahelyi és iskolai fiókokhoz, hozzáférési jogkivonatot kapjon, és hívja a Microsoft Graph API vagy más API-k a Microsoft identity platform végpontról hozzáférési jogkivonatok igénylő.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által létrehozott mintaalkalmazás működése

![Bemutatja, hogyan jöjjön létre a mintaalkalmazás által ezen oktatóanyagok működik](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>További információ

Ez az útmutató által létrehozott alkalmazás lehetővé teszi, hogy egy JavaScript SPA lekérdezni a Microsoft Graph API vagy egy webes API-t, amely a Microsoft identity platform végpontról jogkivonatokat fogad. Ebben a forgatókönyvben egy felhasználó bejelentkezése után az, egy hozzáférési jogkivonatot kért és a HTTP-kérelmekre – az engedélyezési fejléc hozzáadása. Token beszerzése és megújítása a Microsoft hitelesítési tár (MSAL) kezeli.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Kódtárak

Ez az útmutató használja a következő könyvtárban:

|Kódtár|Leírás|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|A Microsoft Authentication Library for JavaScript-előzetes verzió|

> [!NOTE]
> *msal.js* célok a *a Microsoft identity platform endpoint* – lehetővé teszi a személyes, iskolai és a munkahelyi fiókok jelentkezik be, és a jogkivonatok beszerzéséhez. A *a Microsoft identity platform endpoint* rendelkezik [bizonyos korlátozások](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Olvassa el az 1.0-s és 2.0-s verziójú végpontok közötti különbségek megértése a [végpont összehasonlító útmutató](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
