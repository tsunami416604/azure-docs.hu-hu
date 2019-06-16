---
title: Mobilalkalmazás, amely meghívja a webes API-k – áttekintés |} A Microsoft identity platform
description: Ismerje meg a mobilalkalmazások, hogy a hívások webes API-k (áttekintés)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f007ad1d5bf99136328ec5706f7ccbb5f6593c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111231"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Forgatókönyv: A mobilalkalmazás, hogy a hívások webes API-k

Ismerje meg a mobilalkalmazások, amely meghívja a webes API-k ismernie kell az összes.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Az első mobile alkalmazás létrehozása, és próbálja ki a rövid útmutató!

> [!div class="nextstepaction"]
> [Rövid útmutató: A token beszerzéséhez és a Microsoft Graph API hívása Androidos alkalmazásokból](./quickstart-v2-android.md)
>
> [Rövid útmutató: A token beszerzéséhez és a Microsoft Graph API meghívása iOS-alkalmazások](./quickstart-v2-ios.md)
>
> [Rövid útmutató: A token beszerzéséhez és a Microsoft Graph API hívása egy Xamarin iOS és Android-alkalmazás](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Áttekintés

A mobile apps egy személyre szabott, gördülékeny felhasználói élmény elengedhetetlen.  A Microsoft identity platform lehetővé teszi, hogy a élmény iOS és Android-felhasználók a mobilos fejlesztésekhez. Az alkalmazás bejelentkezhet az Azure Active Directory (Azure AD) felhasználók, a személyes Microsoft-fiókok felhasználóinak és az Azure AD B2C-felhasználókat és szerzi be a jogkivonatokat a webes API-hívás a felhasználók nevében. Ezek a folyamatok megvalósításához, a Microsoft hitelesítési tár (MSAL), az iparági szabvány, amely fogjuk használni [OAuth2.0 hitelesítési kódfolyamat](v2-oauth2-auth-code-flow.md).

![Démonalkalmazások](./media/scenarios/mobile-app.svg)

A mobile apps szempontjai:

- **Felhasználói környezet**: Lehetővé teszi a felhasználók számára az alkalmazás értéke bejelentkezés kérése előtt, és csak a szükséges engedélyek kéréséhez.
- **Támogatja az összes felhasználói konfiguráció**: Számos mobil üzleti felhasználók a feltételes hozzáférés és az eszközmegfelelőségi szabályzatok alatt találhatók. Győződjön meg arról, ezek a legfontosabb forgatókönyvek támogatásához.
- **Egyszeri bejelentkezés (SSO) megvalósítása**: MSAL és a Microsoft identity platform révén egyszeri bejelentkezés engedélyezése egyszerűen elvégezhető a böngésző vagy a Microsoft Authenticator (és az Intune vállalati portál Android rendszeren).

## <a name="specifics"></a>Tulajdonságairól

Ezeket a szempontokat tartsa szem előtt a Microsoft identitásplatformja mobilalkalmazások készítése során:

- A platformtól függően néhány felhasználói beavatkozás során szükség lehet az első alkalommal jelentkeznek be. Például az iOS felhasználói interakció megjeleníthető SSO először a Microsoft Authenticator (és az Intune vállalati portál Android rendszeren) használatakor alkalmazások van szükség.
- Az iOS és Android az MSAL előfordulhat, hogy használja a egy külső böngésző (amely felett az alkalmazás jelenhet meg) a felhasználók. Testre szabhatja a konfigurációt, használja helyette az alkalmazáson belüli Webnézeteket.
- Titkos kulcs soha ne használja a mobilalkalmazás. Minden felhasználó számára elérhető lesz.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-mobile-app-registration.md)
