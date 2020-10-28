---
title: 'Rövid útmutató: hitelesítés hozzáadása egy Node-webalkalmazáshoz a MSAL Node használatával | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan valósítható meg a hitelesítés egy Node.js webalkalmazással és a Microsoft Authentication Library (MSAL) szolgáltatással a Node.jshoz.
services: active-directory
author: amikuma
manager: saeeda
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: amikuma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: cfe1b66662d73c18934ef9ecc884adee79493eb6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92641000"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Gyors útmutató: bejelentkezés a felhasználókba és hozzáférési jogkivonat beszerzése egy Node-webalkalmazásban az Auth Code flow használatával

Ebben a rövid útmutatóban egy kódrészletet fog futtatni, amely bemutatja, hogyan jelentkezhet be egy Node.js webalkalmazás a személyes fiókok, a munkahelyi fiókok és az iskolai fiókok felhasználói számára az engedélyezési kód folyamatábrájának használatával. A kód minta azt is bemutatja, hogyan szerezhet be egy hozzáférési jogkivonatot a webes API meghívásához, ebben az esetben a Microsoft Graph API-t. Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

Ez a rövid útmutató a Microsoft hitelesítési függvénytárát használja Node.jshoz (MSAL node) az engedélyezési kód folyamatával.

> [!IMPORTANT]
> MSAL csomópont [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [ingyenes Azure-előfizetés létrehozása](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>A gyors üzembe helyezési alkalmazás regisztrálása és letöltése
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure AD-bérlőre.
> 1. Válassza az [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lehetőséget.
> 1. Válassza az **új regisztráció** lehetőséget.
> 1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
> 1. A **támogatott fiókok típusai** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban** .
> 1. Állítsa be az **átirányítási URI** értékét a következőre: `http://localhost:3000/redirect` .
> 1. Válassza a **Regisztráció** lehetőséget. 
> 1. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. A **tanúsítványok & titkok** területen válassza az **új ügyfél titka** lehetőséget.  Hagyja üresen a leírást és az alapértelmezett lejáratot, majd kattintson a **Hozzáadás** gombra.
> 1. Jegyezze fel az **ügyfél titkos kulcsának** **értékét** későbbi használatra.

#### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése

> [!div renderon="docs"]
> Ha Node.js használatával szeretné futtatni a projektet egy webkiszolgálóval, [töltse le az alapprojekt fájljait](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> A projekt futtatása webkiszolgálóval Node.js használatával

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>3. lépés: a Node-alkalmazás konfigurálása
>
> Bontsa ki a projektet, és nyissa meg az *MS-Identity-Node-Main* mappát, majd nyissa meg a *index.js* fájlt.
> Állítsa be az `clientID` **alkalmazást az alkalmazás (ügyfél) azonosítójával** .
> Állítsa be az `clientSecret` értéket az **ügyfél titkának** **értékével** .
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> Módosítsa a szakasz értékeit az `config` itt leírtak szerint:
>
> - `Enter_the_Application_Id_Here` a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** .
> - `Enter_the_Client_Secret_Here`a regisztrált alkalmazáshoz tartozó **ügyfél-titkos kulcs** **értéke** .
>
> Az alapértelmezett `authority` érték a fő (globális) Azure-felhőt jelenti:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosító** értékének megkereséséhez nyissa meg a Azure Portal az alkalmazás regisztrációjának **Áttekintés** lapját. Az új **ügyfél-titkos** kód lekéréséhez vagy létrehozásához lépjen a **tanúsítványok & Secrets** elemre.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>4. lépés: a projekt futtatása

Futtassa a projektet Node.js használatával:

1. A kiszolgáló elindításához futtassa a következő parancsokat a projekt könyvtárából:
    ```console
    npm install
    npm start
    ```
1. Nyissa meg a következő címet: `http://localhost:3000/`.

1. A bejelentkezési folyamat elindításához válassza **a bejelentkezés** lehetőséget.

    Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg az engedélyt, hogy az alkalmazás hozzáférhessen a profiljához, és jelentkezzen be. Miután sikeresen bejelentkezett, megjelenik egy naplófájl a parancssorban.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése

A futtatáskor a rendszer egy webkiszolgálót üzemeltet a localhost-on, a 3000-es portot. Amikor egy webböngésző hozzáfér a webhelyhez, a minta azonnal átirányítja a felhasználót egy Microsoft-hitelesítési lapra. Emiatt a minta nem tartalmaz *HTML* -vagy megjelenített elemeket. A sikeres hitelesítés az "OK" üzenetet jeleníti meg.

### <a name="msal-node"></a>MSAL csomópont

A MSAL-csomópont könyvtára aláírja a felhasználókat, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kéri. A legújabb verziót a Node.js Package Manager (NPM) segítségével töltheti le:

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hitelesítés hozzáadása meglévő webalkalmazáshoz – GitHub-mintakód >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code/readme.md)
