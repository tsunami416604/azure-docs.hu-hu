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
ms.openlocfilehash: cfbf4dc08ccea95f99216f6cc82c1ee56d65768a
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943351"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>A Microsoft Graph API meghívása a JavaScript egyetlen oldal alkalmazásból (SPA)

Ez az útmutató ismerteti, hogyan bejelentkezhet az egy JavaScript egyetlen oldal alkalmazás (SPA) a személyes, munkahelyi és iskolai fiókok szereznie egy hozzáférési jogkivonatot, és hívja meg a Microsoft Graph API vagy egyéb szükséges hozzáférési jogkivonatok az Azure Active Directory v2 végpont az API-k.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által generált mintaalkalmazás működése

![Ez az útmutató által generált mintaalkalmazás működése](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>További információ

Ez az útmutató által létrehozott mintaalkalmazás lehetővé teszi, hogy a JavaScript SPA lekérdezni a Microsoft Graph API vagy egy webes API, amely az Azure Active Directory v2 végpont jogkivonatokat fogad el. Ebben a forgatókönyvben után a felhasználó bejelentkezik, olyan hozzáférési jogkivonatot kért és megadható a hitelesítési fejlécéhez via HTTP-kérelmekre. Token beszerzése és -megújítás kezelése a Microsoft hitelesítési könyvtár (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Szalagtárak

Ez az útmutató használja a következő könyvtárban:

|Részletes ismertetés|Leírás|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|A JavaScript Preview Microsoft hitelesítési kódtár|

> [!NOTE]
> *msal.js* célok a *Azure Active Directory v2 végpont* -lehetővé teszi a személyes, iskolai és munkahelyi fiókokat kell jelentkezzen be, és beszerezni a jogkivonatokat. A *Azure Active Directory v2 végpont* rendelkezik [bizonyos korlátozások](..\articles\active-directory\develop\active-directory-v2-limitations.md). Ha érdekli, csak a munkahelyi és iskolai fiókok, *adal.js* és a *V1 végpont*. Olvassa el a v1 és v2 végpontok közötti különbségek megértése a [v1-v2 összehasonlító](..\articles\active-directory\develop\active-directory-v2-compare.md).

<!--end-collapse-->
