---
title: OIDC-bejelentkezés hozzáadása egy Node.js webalkalmazáshoz – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan valósítható meg a hitelesítés egy Node.js webalkalmazásban az OpenID Connect használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: 8e42d906cce65cf51d6359343bc4f218b94a05fb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257690"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Rövid útmutató: bejelentkezés hozzáadása az OpenID használatával Node.js webalkalmazáshoz

Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja be az OpenID Connect-hitelesítést egy Node.js és Express használatával létrehozott webalkalmazásban. A minta úgy van kialakítva, hogy bármilyen platformon fusson.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szüksége:

* Node.js telepítése innen: http://nodejs.org/

* [Microsoft-fiók](https://www.outlook.com) vagy [Microsoft 365 fejlesztői program](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Az alkalmazás regisztrálása
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. Ha a fiókja több Azure AD-bérlőn is megtalálható:
    - Válassza ki a profilt a lap jobb felső sarkában található menüből, majd **váltson át a könyvtárra**.
    - Módosítsa a munkamenetet arra az Azure AD-bérlőre, ahol létre szeretné hozni az alkalmazást.

1. Az alkalmazás regisztrálásához navigáljon [Azure Active Directory > Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) .

1. Válassza az **új regisztráció lehetőséget.**

1. Amikor megjelenik az **alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
    - A **név** szakaszban adjon meg egy értelmes nevet, amely megjelenik az alkalmazás felhasználói számára. Például: MyWebApp
    - A **támogatott fióktípus** szakaszban válassza a fiókok lehetőséget a **szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com)**.

    Ha egynél több átirányítási URI van, ezeket az alkalmazás sikeres létrehozása után később hozzá kell adnia a **hitelesítés** lapon.

1. Az alkalmazás létrehozásához válassza a **regisztráció** lehetőséget.

1. Az alkalmazás **Áttekintés** lapján keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később. Erre az értékre szüksége lesz ahhoz, hogy az alkalmazást később konfigurálja a projektben.

1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
    - Az **átirányítási URI** -k szakaszban válassza a **web** elemet a kombinált listában, és adja meg a következő átirányítási URI-t: `http://localhost:3000/auth/openid/return`
    - A **Speciális beállítások** szakaszban állítsa be a **KIJELENTKEZÉSI URL-címet** a következőre: `https://localhost:3000` .
    - A **Speciális beállítások > implicit engedélyezési** szakaszban tekintse meg az **azonosító jogkivonatokat** , mivel ez a minta megköveteli, hogy az [implicit engedélyezési folyamat](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) engedélyezze a bejelentkezést a felhasználó számára.

1. Kattintson a **Mentés** gombra.

1. A **tanúsítványok & titkok** oldal **ügyfél-titkok** szakaszában válassza az **új ügyfél titka**elemet.
    - Adja meg a kulcs leírását (például az alkalmazás titkos kulcsaként).
    - Válassza ki a kulcs időtartamát **1 év vagy 2 év között,** vagy **Soha ne járjon le**.
    - A **Hozzáadás** gombra kattintva megjelenik a kulcs értéke. Másolja a kulcs értékét, és mentse biztonságos helyre.

    Az alkalmazás konfigurálásához később szüksége lesz erre a kulcsra. Ez a kulcs nem jelenik meg újra, és semmilyen más módon nem kérhető le, ezért jegyezze fel, amint a Azure Portal látható.

## <a name="download-the-sample-application-and-modules"></a>A minta alkalmazás és modulok letöltése

Ezután a minta-tárház klónozása és a NPM-modulok telepítése.

A rendszerhéjból vagy parancssorból:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

vagy

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

A projekt gyökérkönyvtárában futtassa a következő parancsot:

`$ npm install`

## <a name="configure-the-application"></a>Az alkalmazás konfigurálása

Adja meg a paramétereket a `exports.creds` config.js utasítás szerint.

* Frissítse a `<tenant_name>` - `exports.identityMetadata` t az Azure ad-bérlő neve \* . onmicrosoft.com formátumban.
* Frissítse `exports.clientID` az alkalmazás-regisztráció során feljegyzett alkalmazás-azonosítóval.
* Frissítse `exports.clientSecret` az alkalmazás-regisztráció során feljegyzett alkalmazási titokkal.
* Frissítse `exports.redirectUrl` az alkalmazás regisztrációja során feljegyzett átirányítási URI-val.

**Az üzemi alkalmazások opcionális konfigurációja:**

* `exports.destroySessionUrl`Ha másikat szeretne használni, config.js frissítsen `post_logout_redirect_uri` .

* `exports.useMongoDBSessionStore`Ha a [mongoDB](https://www.mongodb.com) -t vagy más [kompatibilis munkamenet-tárolókat](https://github.com/expressjs/session#compatible-session-stores)szeretne használni, állítsa be config.js értéke TRUE (igaz) értékre.
A példában szereplő alapértelmezett munkamenet-tároló `express-session` . Az alapértelmezett munkamenet-tároló nem alkalmas az éles környezetben való használatra.

* Frissítsen `exports.databaseUri` , ha a mongoDB munkamenet-áruházat és egy másik adatbázis-URI-t szeretne használni.

* Frissítés `exports.mongoDBSessionMaxAge` . Itt adhatja meg, hogy mennyi ideig szeretné megőrizni a munkamenetet a mongoDB. Az egység másodperc (ek).

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Indítsa el a mongoDB szolgáltatást. Ha ebben az alkalmazásban a mongoDB munkamenet-tárolót használja, először [telepítenie](http://www.mongodb.org/) kell a mongoDB-t, és előbb el kell indítania a szolgáltatást. Ha az alapértelmezett munkamenet-tárolót használja, akkor kihagyhatja ezt a lépést.

Futtassa az alkalmazást a parancssorból a következő parancs használatával.

```
$ node app.js
```

**Nehezen érthető a kiszolgáló kimenete?:** Ennek a `bunyan` mintának a naplózására használjuk. A konzol nem fog sok értelmet adni Önnek, ha nem is telepíti a Bunyan-t, és nem futtatja a fentieket, hanem a Bunyan bináris fájlon keresztül átadja a kiszolgálót:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Ennyi az egész!

A kiszolgáló sikeresen fut `http://localhost:3000` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések
További információ a Microsoft Identity platform által támogatott Web App-forgatókönyvről:
> [!div class="nextstepaction"]
> [Felhasználói forgatókönyvet használó webalkalmazás](scenario-web-app-sign-user-overview.md)
