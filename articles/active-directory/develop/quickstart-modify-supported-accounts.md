---
title: A Microsoft Identity Platformon regisztrált alkalmazás által támogatott fiókok módosítása | Azure
description: A Microsoft Identity Platformon regisztrált alkalmazás konfigurálásával módosíthatja, hogy kik, illetve mely fiókok érhetik el az alkalmazást.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a2c68d607e7afc2e3eac675511734c8d054c427
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174197"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application-preview"></a>Gyors útmutató: Egy alkalmazás (előzetes verzió) támogatja a fiókok módosítása

Az alkalmazások Microsoft Identity Platformon való regisztrálásakor esetenként az a cél, hogy az alkalmazást csak a szervezethez tartozó felhasználók érhessék el. Előfordulhat az is, hogy az alkalmazást olyan felhasználók számára is szeretné elérhetővé tenni, akik külső szervezetekhez tartoznak, vagy nem feltétlenül tartoznak valamely szervezethez (a személyes fiókok).

Ebből a rövid útmutatóból megtudhatja, hogyan változtassa meg alkalmazása beállításait annak módosításához, hogy kik, illetve mely érhetik el az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

Először is győződjön meg arról, hogy az alábbi előfeltételek teljesülnek:

* Megismerte a támogatott [engedélyekkel és hozzájárulással](v2-permissions-and-consent.md) kapcsolatos tudnivalókat, mivel fontos szem előtt tartania őket olyan alkalmazások készítésekor, amelyeket más felhasználóknak vagy alkalmazásoknak is használniuk kell.
* Rendelkezik olyan bérlővel, amelyhez regisztrált alkalmazások tartoznak.
  * Ha nincsenek regisztrált alkalmazásai, [ismerje meg, hogyan regisztrálhat alkalmazásokat a Microsoft Identity Platformon](quickstart-register-app.md).
* Hagyja jóvá az előzetes verzió használatát az alkalmazások Azure Portalon való regisztrálására. E rövid útmutató lépései az új felhasználói felületen alapulnak, és csak akkor működnek, ha Ön jóváhagyta az előzetes verzió használatát.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Bejelentkezés az Azure Portalra és az alkalmazás kiválasztása

Mielőtt konfigurálhatná az alkalmazást, végre kell hajtania az alábbi lépéseket:

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, illetve személyes Microsoft-fiókjával.
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást, majd az **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget.
1. Keresse meg és jelölje ki a konfigurálni kívánt alkalmazást. Miután kijelölte az alkalmazást, az alkalmazás **Áttekintés** lapja vagy regisztrációs főoldala jelenik meg.
1. A lépéseket követve [módosítsa az alkalmazásregisztrációt különböző fiókok támogatásának engedélyezéséhez](#change-the-application-registration-to-support-different-accounts).
1. Ha egyoldalas alkalmazása van, [engedélyezze az OAuth 2.0 implicit engedély használatát](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Alkalmazásregisztráció módosítása különböző fiókok támogatásának engedélyezéséhez

Ha olyan alkalmazást ír, amelyet a szervezeten kívüli ügyfelei és partnerei számára is elérhetővé szeretne tenni, frissítenie kell az alkalmazás definícióját az Azure Portalon.

> [!IMPORTANT]
> Az Azure AD megköveteli, hogy a több-bérlős alkalmazások alkalmazásazonosító URI-ja (App ID URI) globálisan egyedi legyen. Az alkalmazásazonosító URI egy módszer, amellyel az alkalmazás a protokollüzenetekben azonosítható. Egybérlős alkalmazás esetében az alkalmazásazonosító URI-nak csak a bérlőn belül kell egyedinek lennie. Több-bérlős alkalmazás esetében azonban globálisan egyedinek kell lennie, hogy az Azure AD megtalálja az alkalmazást a különböző bérlők közt. A globális egyediség azzal kényszeríthető ki, hogy a rendszer megköveteli, hogy az alkalmazásazonosító URI egy olyan egy gazdanévvel rendelkezzen, amely egyezik az Azure AD-bérlő egy ellenőrzött tartományával. Ha például a bérlő neve contoso.onmicrosoft.com, érvényes alkalmazásazonosító URI lehet a https://contoso.onmicrosoft.com/myapp. Ha a bérlő a contoso.com ellenőrzött tartománnyal rendelkezik, az érvényes alkalmazásazonosító URI ugyanígy a https://contoso.com/myapp lehet. Ha az alkalmazásazonosító URI nem ezt a mintát követi, az alkalmazások több-bérlősként való konfigurálása meghiúsul.

### <a name="to-change-who-can-access-your-application"></a>Az alkalmazást elérők körének módosítása

1. Az alkalmazás **Áttekintés** lapján válassza a **Hitelesítés** szakaszt, és módosítsa a **Támogatott fióktípusok** alatt megadott értéket.
    * Válassza a **Csak az ebben a címtárban található fiókok** lehetőséget, ha üzletági alkalmazást készít. Ez a lehetőség nem érhető el, ha nem regisztrálta az alkalmazást valamely címtárban.
    * Válassza a **Tetszőleges szervezeti címtárban található fiókok** lehetőséget, ha szeretne minden üzleti és az oktatási ügyfelet megcélozni.
    * Válassza a **Tetszőleges szervezeti címtárban található fiókok és személyes Microsoft-fiókok** lehetőséget, ha a lehető legszélesebb ügyfélkört szeretné megcélozni.
1. Kattintson a **Mentés** gombra.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>OAuth 2.0 implicit engedély használatának engedélyezése egyoldalas alkalmazásoknál

Az egyoldalas alkalmazások (SPA-k) általában egy böngészőben futó JavaScript-intenzív előtérrel rendelkeznek, amely az alkalmazás webes API-s hátterét hívja meg az üzleti logika végrehajtatására. Az Azure AD-ben üzemeltetett egyoldalas alkalmazások esetében az OAuth 2.0 implicit engedélyezés használatával hitelesítheti a felhasználókat az Azure AD-ben, és szerezheti be a jogkivonatot, amelynek használatával biztosíthatja a hívásokat az alkalmazás JavaScript-ügyfeléről a háttér webes API-jára.

Miután a felhasználó megadta a hozzájárulást, ugyanez a hitelesítési protokoll használható a jogkivonatok beszerzéséhez az ügyfél és az alkalmazáshoz konfigurált egyéb webes API erőforrások közötti hívások biztosításához. Ha többet szeretne megtudni az implicit engedélyezésről és annak alkalmazhatóságáról az egyes alkalmazás-forgatókönyvekben, ismerkedjen meg az Azure AD [1.0](v1-oauth2-implicit-grant-flow.md)-s és [v2.0](v2-oauth2-implicit-grant-flow.md)-s verziójára vonatkozó OAuth 2.0 implicit engedélyezési folyamattal.

Az OAuth 2.0 implicit engedély alapértelmezés szerint le van tiltva az alkalmazásokon. Ha engedélyezni szeretné az OAuth 2.0 implicit engedély használatát az alkalmazásánál, kövesse az alábbi lépéseket.

### <a name="to-enable-oauth-20-implicit-grant"></a>Az OAuth 2.0 implicit engedélyezési folyamat engedélyezése

1. Az alkalmazás **Áttekintés** lapján válassza a **Hitelesítés** szakaszt.
1. A **Speciális beállítások** alatt keresse meg au **Implicit engedély** szakaszt.
1. Jelölje ki az **Azonosító-jogkivonatok** vagy a **Hozzáférési jogkivonatok** lehetőséget, vagy mindkettőt.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

Tekintse meg az alkalmazásokra vonatkozó alábbi rövid alkalmazásfelügyeleti útmutatókat is:

* [Alkalmazás regisztrálása a Microsoft Identity Platformon](quickstart-register-app.md)
* [Ügyfélalkalmazás konfigurálása a webes API-k elérésére](quickstart-configure-app-access-web-apis.md)
* [Alkalmazás konfigurálása a webes API-k közzétételére](quickstart-configure-app-expose-web-apis.md)
* [Microsoft Identity Platformon regisztrált alkalmazás eltávolítása](quickstart-remove-app.md)

Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.

Ha szeretne többet megtudni az Azure Active Directoryt használó alkalmazások fejlesztése során alkalmazandó védjegyzési irányelvekről, tekintse meg az [alkalmazások védjegyzési irányelveit](howto-add-branding-in-azure-ad-apps.md) ismertető szakaszt.
