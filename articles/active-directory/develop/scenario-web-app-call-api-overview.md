---
title: Webes API-kat megnevező webalkalmazás létrehozása – Microsoft identity platform | Azure
description: Megtudhatja, hogy miként hozhat létre webes alkalmazásokat webes API-k hívására (áttekintés)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d121d6c198cb0d92cd098a40096e2f2300f65537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758989"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Eset: Webes API-kat meghívja ó, webalkalmazás

Megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely bejelentkezi a felhasználókat a Microsoft identitásplatformra, majd webes API-kat hív meg a bejelentkezett felhasználó nevében.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Ez a forgatókönyv feltételezi, hogy már átment a következő forgatókönyvön:

> [!div class="nextstepaction"]
> [Felhasználót bejelentkeztető webalkalmazás](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Áttekintés

Hitelesítést ad hozzá a webalkalmazáshoz, hogy a felhasználók bejelentkezhessenek, és a bejelentkezett felhasználó nevében meghívja a webes API-t.

![Webes API-kat hívó webalkalmazás](./media/scenario-webapp/web-app.svg)

A webes API-kat hívó webalkalmazások bizalmas ügyfélalkalmazások.
Ezért regisztrálnak egy titkos (alkalmazásjelszót vagy tanúsítványt) az Azure Active Directory (Azure AD) használatával. Ez a titkos kulcs átadása az Azure AD-hez egy jogkivonat lekéréséhez.

## <a name="specifics"></a>Sajátosságai

> [!NOTE]
> A webalkalmazásba való bejelentkezés a webalkalmazás védelméről szól. Ez a védelem *a köztes szoftvertárak* használatával érhető el, nem a Microsoft authentication library (MSAL) használatával. Az előző forgatókönyv, [a felhasználókban bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md)lefedte a témát.
>
> Ez a forgatókönyv ismerteti, hogyan hívhatja meg a webes API-kat egy webalkalmazásból. Hozzáférési jogkivonatokat kell beszereznie ezekhez a webes API-khoz. A jogkivonatok beszerzéséhez msal-kódtárak at kell használnia ezeket a jogkivonatokat.

A forgatókönyv fejlesztése a következő konkrét feladatokat foglalja magában:

- Az [alkalmazás regisztrációja](scenario-web-app-call-api-app-registration.md)során meg kell adnia egy válasz URI-t, titkos vagy tanúsítványt az Azure AD-vel megosztandó. Ha az alkalmazást több helyre telepíti, ezeket az információkat minden helyhez meg fogja adni.
- Az [alkalmazás konfigurációjának](scenario-web-app-call-api-app-configuration.md) meg kell adnia az Azure AD-vel az alkalmazás regisztrációja során megosztott ügyfélhitelesítő adatokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívjaó webalkalmazás: Alkalmazásregisztráció](scenario-web-app-call-api-app-registration.md)
