---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 2cb4895fc2f884d6da41b55faa91fbcb9e88f52f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978865"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>A Microsoft Graph API meghívása egy JavaScript egyoldalas alkalmazásból (SPA)

Ez az útmutató azt ismerteti, hogyan bejelentkezhet egy JavaScript egyoldalas alkalmazás (SPA) személyes, munkahelyi és iskolai fiókokhoz, hozzáférési jogkivonatot kapjon, és a Microsoft Graph API vagy egyéb megkövetelése a hozzáférési jogkivonatok az Azure Active Directory v2.0-végpont az API-hívás.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által létrehozott mintaalkalmazás működése

![Ez az útmutató által létrehozott mintaalkalmazás működése](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>További információ

Ez az útmutató által létrehozott alkalmazás lehetővé teszi, hogy egy JavaScript SPA lekérdezni a Microsoft Graph API vagy egy webes API-t, amely az Azure Active Directory v2.0-végpont jogkivonatokat fogad. Ebben a forgatókönyvben egy felhasználó bejelentkezése után az, egy hozzáférési jogkivonatot kért és a HTTP-kérelmekre – az engedélyezési fejléc hozzáadása. Token beszerzése és megújítása a Microsoft hitelesítési tár (MSAL) kezeli.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Kódtárak

Ez az útmutató használja a következő könyvtárban:

|Erőforrástár|Leírás|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|A Microsoft Authentication Library for JavaScript-előzetes verzió|

> [!NOTE]
> *msal.js* célok a *Azure Active Directory v2.0-végpont* – lehetővé teszi a személyes, iskolai és a munkahelyi fiókok jelentkezik be, és a jogkivonatok beszerzéséhez. A *Azure Active Directory v2.0-végpont* rendelkezik [bizonyos korlátozások](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Olvassa el az 1.0-s és 2.0-s verziójú végpontok közötti különbségek megértése a [végpont összehasonlító útmutató](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
