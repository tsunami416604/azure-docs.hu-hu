---
title: Microsoft Identity platform – OIDC Node. js-alapú webalkalmazáshoz | Azure
description: Megtudhatja, hogyan valósítható meg a hitelesítés egy Node. js-webalkalmazásban az OpenID Connect használatával.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ac3344fe16d24ae116a5fde289421998f11f18
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73178045"
---
# <a name="quickstart-add-sign-in-using-oidc-to-a-nodejs-web-app"></a>Gyors útmutató: bejelentkezés hozzáadása a OIDC használatával Node. js-alapú webalkalmazáshoz

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja be az OpenID Connect-hitelesítést egy Node. js-vel létrehozott webalkalmazásban az Express használatával. A minta úgy van kialakítva, hogy bármilyen platformon fusson.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szüksége:

* A Node. js telepítése http://nodejs.org/ ról

* Vagy egy [Microsoft-fiók](https://www.outlook.com) vagy [Office 365 fejlesztői program](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Alkalmazás regisztrálása 
1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com/).
1. Ha a fiókja több Azure AD-bérlőn is megtalálható:
    - Válassza ki a profilt a lap jobb felső sarkában található menüből, majd **váltson át a könyvtárra**.
    - Módosítsa a munkamenetet arra az Azure AD-bérlőre, ahol létre szeretné hozni az alkalmazást.

1. Az alkalmazás regisztrálásához navigáljon [Azure Active Directory > Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) .

1. Válassza az **új regisztráció lehetőséget.**

1. Amikor megjelenik az **alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
    - A **név** szakaszban adjon meg egy értelmes nevet, amely megjelenik az alkalmazás felhasználói számára. Például: MyWebApp
    - A **támogatott fióktípus** szakaszban válassza a fiókok lehetőséget a **szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com)** .

    Ha egynél több átirányítási URI van, ezeket az alkalmazás sikeres létrehozása után később hozzá kell adnia a **hitelesítés** lapon.

1. Az alkalmazás létrehozásához válassza a **regisztráció** lehetőséget.

1. Az alkalmazás **Áttekintés** lapján keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később. Erre az értékre szüksége lesz ahhoz, hogy az alkalmazást később konfigurálja a projektben.

1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
    - Az **átirányítási URI** -k szakaszban válassza a **web** elemet a kombinált listában, és adja meg a következő átirányítási URI-t: `http://localhost:3000/auth/openid/return`
    - A **Speciális beállítások** szakaszban állítsa be `http://localhost:3000`a **KIJELENTKEZÉSI URL-címet** .
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

Adja meg a paramétereket `exports.creds` a config. js fájlban, az utasítások szerint.

* Frissítse a `exports.identityMetadata` `<tenant_name>` a \*. onmicrosoft.com formátum Azure AD-bérlői nevével.
* Frissítse `exports.clientID` az alkalmazás-regisztráció során feljegyzett alkalmazás-AZONOSÍTÓval.
* Frissítse `exports.clientSecret`t az alkalmazás regisztrációja alapján feljegyzett alkalmazási titokkal.
* Frissítse `exports.redirectUrl` az alkalmazás regisztrációja által jelzett átirányítási URI azonosítóval.

**Az üzemi alkalmazások opcionális konfigurációja:**

* Frissítse `exports.destroySessionUrl` a config. js fájlban, ha másik `post_logout_redirect_uri`szeretne használni.

* Ha a [mongoDB](https://www.mongodb.com) vagy más [kompatibilis munkamenet-tárolókat](https://github.com/expressjs/session#compatible-session-stores)szeretne használni, állítsa a `exports.useMongoDBSessionStore` a config. js fájlból True értékre.
Ebben a példában az alapértelmezett munkamenet-tároló `express-session`. Az alapértelmezett munkamenet-tároló nem alkalmas az éles környezetben való használatra.

* Frissítse `exports.databaseUri`, ha a mongoDB munkamenet-tárolót és egy másik adatbázis-URI-t kíván használni.

* `exports.mongoDBSessionMaxAge`frissítése. Itt adhatja meg, hogy mennyi ideig szeretné megőrizni a munkamenetet a mongoDB. Az egység másodperc (ek).

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Indítsa el a mongoDB szolgáltatást. Ha ebben az alkalmazásban a mongoDB munkamenet-tárolót használja, először [telepítenie](http://www.mongodb.org/) kell a mongoDB-t, és előbb el kell indítania a szolgáltatást. Ha az alapértelmezett munkamenet-tárolót használja, akkor kihagyhatja ezt a lépést.

Futtassa az alkalmazást a parancssorból a következő parancs használatával.

```
$ node app.js
```

**Nehezen érthető a kiszolgáló kimenete?:** Ennek a mintának a naplózásához `bunyan` használjuk. A konzol nem fog sok értelmet adni Önnek, ha nem is telepíti a Bunyan-t, és nem futtatja a fentieket, hanem a Bunyan bináris fájlon keresztül átadja a kiszolgálót:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Készen van!

A kiszolgáló sikeresen fut `http://localhost:3000`on.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések
További információ a Microsoft Identity platform által támogatott Web App-forgatókönyvről:
> [!div class="nextstepaction"]
> [Felhasználói forgatókönyvet használó webalkalmazás](scenario-web-app-sign-user-overview.md)
