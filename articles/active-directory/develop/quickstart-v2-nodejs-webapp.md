---
title: OIDC-bejelentkezés hozzáadása node.js webalkalmazáshoz – Microsoft identity platform | Azure
description: Megtudhatja, hogy miként valósíthat meg hitelesítést egy Node.js webalkalmazásban az OpenID Connect használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 4aa0cce83f9adc8c648656899ec6dc12d498e26b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77160448"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Rövid útmutató: Bejelentkezés hozzáadása az OpenID Connect használatával egy Node.js webalkalmazáshoz

Ebben a rövid útmutatóban megtudhatja, hogyan állíthatja be az OpenID Connect hitelesítést a Node.js with Express használatával létrehozott webalkalmazásban. A minta úgy van kialakítva, hogy bármilyen platformon fusson.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szükség:

* Node.js telepítése innen:http://nodejs.org/

* [Microsoft-fiók](https://www.outlook.com) vagy [Office 365 fejlesztői program](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Alkalmazás regisztrálása 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. Ha a fiók több Azure AD-bérlőben is jelen van:
    - Válassza ki a profilját a lap jobb felső sarkában lévő menüből, majd **a Könyvtár váltása**lehetőséget.
    - Módosítsa a munkamenetet az Azure AD-bérlőre, ahol létre szeretné hozni az alkalmazást.

1. Az alkalmazás regisztrálásához keresse meg az [Azure Active Directory > alkalmazásregisztrációkat.](https://go.microsoft.com/fwlink/?linkid=2083908)

1. Válassza az **Új regisztráció lehetőséget.**

1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
    - A **Név szakaszban** adjon meg egy értelmes nevet, amely megjelenik az alkalmazás felhasználói számára. Például: MyWebApp
    - A **Támogatott fióktípusok** csoportban válassza a **Fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban (pl. Skype, Xbox, Outlook.com).**

    Ha egynél több átirányítási URI-k vannak, ezeket később hozzá kell **adnia** a Hitelesítés lapról, miután az alkalmazás sikeresen létrejött.

1. Az alkalmazás létrehozásához válassza a **Regisztráció** lehetőséget.

1. Az alkalmazás **áttekintése** lapon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és rögzítse későbbre. Erre az értékre szüksége lesz az alkalmazás konfigurálásához a projekt későbbi részében.

1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
    - Az **Irányított URI-k átirányítása** csoportban válassza a **Web** elemet a kombinált lista területén, és írja be a következő átirányítási URI-t:`http://localhost:3000/auth/openid/return`
    - A **Speciális beállítások** csoportban állítsa `http://localhost:3000`a **Kijelentkezés URL-címét** a-ra.
    - A **Speciális beállítások > Implicit támogatás** szakaszban ellenőrizze az azonosító **jogkivonatokat,** mivel ez a minta megköveteli, hogy az [Implicit támogatási folyamat](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) engedélyezve legyen a felhasználó bejelentkezéséhez.

1. Kattintson a **Mentés** gombra.

1. A **Tanúsítványok & titkos kulcsok** lap **Ügyféltitkos kulcsok** szakaszában válassza az Új **ügyféltitok**lehetőséget.
    - Adjon meg egy kulcs leírást (például alkalmazástitkos kulcsot).
    - Válasszon egy kulcsfontosságú időtartamot: 1 **év, 2 év** vagy **Soha nem jár le.**
    - Amikor a **Hozzáadás** gombra kattint, a kulcs értéke megjelenik. Másolja a kulcs értékét, és mentse biztonságos helyre.

    Az alkalmazás konfigurálásához később szüksége lesz erre a kulcsra. Ez a kulcsérték nem jelenik meg újra, és nem visszakereshető más módon, így rögzítse, amint látható az Azure Portalon.

## <a name="download-the-sample-application-and-modules"></a>A mintaalkalmazás és a modulok letöltése

Ezután klónozza a minta-tárhét, és telepítse az NPM modulokat.

A rendszerhéjból vagy a parancssorból:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

vagy

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

A projekt gyökérkönyvtárából futtassa a következő parancsot:

`$ npm install`  

## <a name="configure-the-application"></a>Az alkalmazás konfigurálása

Adja meg a `exports.creds` paramétereket a config.js fájlban az utasításoknak megfelelően.

* Frissítse `<tenant_name>` `exports.identityMetadata` ni az Azure AD-bérlő nevét \*.onmicrosoft.com.
* Frissítés `exports.clientID` az alkalmazásregisztrációból származó alkalmazásazonosítóval.
* Frissítse `exports.clientSecret` az alkalmazás regisztrációja során észlelt alkalmazástitkost.
* Frissítés `exports.redirectUrl` az alkalmazásregisztrációból észlelt átirányítási URI-val.

**Választható konfiguráció éles alkalmazásokhoz:**

* Frissítse `exports.destroySessionUrl` nitoncikó.js, ha `post_logout_redirect_uri`azt szeretnénk, hogy egy másik .

* Állítsa `exports.useMongoDBSessionStore` a config.js fájlban igaz értékre, ha [a mongoDB-t](https://www.mongodb.com) vagy más [kompatibilis munkamenet-áruházakat](https://github.com/expressjs/session#compatible-session-stores)szeretné használni.
A minta alapértelmezett munkamenet-tárolója a `express-session`. Az alapértelmezett munkamenet-tároló nem alkalmas éles környezetben.

* Frissítse `exports.databaseUri`a mongoDB munkamenet-tárolót és egy másik adatbázis URI-t.

* Frissítés `exports.mongoDBSessionMaxAge`. Itt adhatja meg, hogy mennyi ideig szeretné megtartani a munkamenetet a mongoDB-ban. Az egység második(a).

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Indítsa el a mongoDB szolgáltatást. Ha mongoDB munkamenet-áruházat használ ebben az alkalmazásban, telepítenie kell a [mongoDB-t,](http://www.mongodb.org/) és először el kell indítania a szolgáltatást. Ha az alapértelmezett munkamenet-tárolót használja, kihagyhatja ezt a lépést.

Futtassa az alkalmazást a következő paranccsal a parancssorból.

```
$ node app.js
```

**A kiszolgáló kimenete nehezen érthető?:** Ezt `bunyan` a mintát használjuk a bejelentkezéshez. A konzol nem lesz sok értelme, hogy ha nem is telepíteni bunyan és fuss a szerver, mint a fenti, de cső keresztül bunyan bináris:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Ennyi az egész!

Sikeresen fut a kiszolgáló. `http://localhost:3000`

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések
További információ a Microsoft identitáskezelési platform által támogatott webalkalmazás-forgatókönyvről:
> [!div class="nextstepaction"]
> [A felhasználók forgatókönyvében bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md)
