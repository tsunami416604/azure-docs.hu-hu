---
title: 'Oktatóanyag: Node. js webes API-k biztosítása Azure AD B2C használatával és hozzáférés engedélyezése egyoldalas alkalmazáshoz (SPA)'
titleSuffix: Azure AD B2C
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Active Directory B2Ct a Node. js webes API-k elleni védelemhez, és hogyan hívhat meg egy egyoldalas alkalmazásból.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50524159186987b7a30015c878fa3fac949afc79
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875679"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Oktatóanyag: Node. js webes API-hoz való hozzáférés biztosítása egy egyoldalas alkalmazásból Azure AD B2C

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy Azure Active Directory B2C (Azure AD B2C) által védett Node. js webes API-t egy egyoldalas alkalmazásból.

Ebben az oktatóanyagban a második egy kétrészes sorozatban:

> [!div class="checklist"]
> * Webes API-alkalmazás regisztrációjának létrehozása a Azure AD B2C-bérlőben
> * Hatókörök konfigurálása a webes API-hoz
> * Engedélyek megadása a webes API számára
> * Webes API-kód minta módosítása a Bérlővel való együttműködéshez

A sorozat [első oktatóanyagában](tutorial-single-page-app.md) letöltötte a kód mintát, és módosította a felhasználókat felhasználói folyamattal való bejelentkezésre a Azure ad B2C bérlőben.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre a következő lépéseket és előfeltételeket az [oktatóanyagban: hitelesítés engedélyezése egyoldalas alkalmazásban Azure ad B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) vagy más Kódszerkesztő
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban a webes API olvasási és írási engedélyeit is meghatározza.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Az egyoldalas alkalmazás konfigurálásakor `demo.read` a **hatókör hatókörében** lévő értéket jegyezze fel egy későbbi lépésben való használatra. A hatókör teljes értéke hasonló a `https://contosob2c.onmicrosoft.com/api/demo.read`következőhöz:.

## <a name="grant-permissions"></a>Engedélyek megadása

Ha egy védett webes API-t szeretne meghívni egy másik alkalmazásból, meg kell adnia a webes API-nak az alkalmazáshoz szükséges engedélyeket.

Az előfeltételként szolgáló oktatóanyagban létrehozott egy *webapp1*nevű webalkalmazást. Ebben az oktatóanyagban úgy konfigurálja az alkalmazást, hogy hívja meg az előző szakaszban létrehozott webes API-t ( *webapi1*).

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Az egyoldalas webalkalmazás mostantól engedélyt kapott a védett webes API-ra a megadott hatókörökhöz. A felhasználók a Azure AD B2C használatával hitelesítik egyoldalas alkalmazást. Az egyoldalas alkalmazás az engedélyezési folyamat használatával fér hozzá a védett webes API-hoz a Azure AD B2C által visszaadott hozzáférési jogkivonattal.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy regisztrálta a webes API-t, és definiálta a hatóköröket, konfigurálja a webes API-kódot a Azure AD B2C Bérlővel való együttműködéshez. Ebben az oktatóanyagban egy példa Node. js webes API-t konfigurál, amelyet a GitHubról letöltött.

[Töltse le \*a. zip-archívumot](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) , vagy klónozott webes API-projektet a githubról. Közvetlenül is böngészhet az [Azure-Samples/Active-Directory-B2C-JavaScript-NodeJS-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) projektben a githubon.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. Nyissa meg a *config. js* fájlt a Kódszerkesztőben.
1. Módosítsa a változó értékeit úgy, hogy azok megfeleljenek a korábban létrehozott alkalmazás-regisztrációnak. Frissítse a `policyName` -t az előfeltételek részeként létrehozott felhasználói folyamattal is. Például *B2C_1_signupsignin1*.

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>CORS engedélyezése

Annak engedélyezéséhez, hogy egy egyoldalas alkalmazás meghívja a Node. js webes API-t, engedélyeznie kell a [CORS](https://expressjs.com/en/resources/middleware/cors.html) a webes API-ban. Éles alkalmazásban körültekintően kell megjelennie, hogy melyik tartományhoz készül a kérelem, de ebben az oktatóanyagban bármely tartományból engedélyezheti a kérelmeket.

A CORS engedélyezéséhez használja a következő middleware-t. Ebben az oktatóanyagban a Node. js webes API-kód mintájában már hozzá lett adva az *index. js* fájlhoz.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Egyoldalas alkalmazás konfigurálása

A sorozat [előző oktatóanyagának](tutorial-single-page-app.md) egyoldalas alkalmazása (SPA) Azure ad B2C használ a felhasználói regisztrációhoz és bejelentkezéshez, és alapértelmezés szerint meghívja a *fabrikamb2c* demo bérlő által védett Node. js webes API-t.

Ebben a szakaszban az egyoldalas webalkalmazást frissíti, hogy meghívja a Azure AD B2C bérlő által védett Node. js webes API-t (és azt, *amelyet a helyi* gépen futtat).

A SPA beállításainak módosítása:

1. Az előző oktatóanyagban letöltött vagy klónozott [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] projektben nyissa meg a *apiConfig. js* fájlt a *JavaScriptSPA* mappában.
1. Konfigurálja a mintát a bemutató URI azonosítójának használatával *. olvassa el* a korábban létrehozott hatókört, valamint a webes API URL-címét.
    1. A `apiConfig` definícióban cserélje le az `b2cScopes` értéket a bemutató teljes URI-azonosítójához *. olvasási* hatókör (a korábban feljegyzett **hatókör** -érték).
    1. Módosítsa az `webApi` érték tartományát az ÁTirányítási URI-ra, amelyet akkor adott meg, amikor regisztrálta a webes API-alkalmazást egy korábbi lépésben.

    Mivel az API elérhető a `/hello` végponton, hagyja a */Hello* az URI-ban.

    A `apiConfig` definíciónak az alábbi kódrészlethez hasonlóan kell kinéznie, de a B2C-bérlő neve a következő helyen `<your-tenant-name>`található:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>A SPA és a webes API futtatása

Most már készen áll az egyoldalas alkalmazás hatókörön belüli hozzáférésének tesztelésére az API-hoz. Futtassa mind a Node. js webes API-t, mind a minta JavaScript egyoldalas alkalmazást a helyi gépen. Ezután jelentkezzen be az egyoldalas alkalmazásba, és válassza a **hívás API** gombot a védett API-ra irányuló kérelem elindításához.

Bár mindkét alkalmazás helyileg fut, amikor ezt az oktatóanyagot követi, úgy konfigurálta, hogy a biztonságos bejelentkezési és bejelentkezési Azure AD B2C használatára konfigurálja őket, és hozzáférést biztosít a védett webes API-hoz.

### <a name="run-the-nodejs-web-api"></a>A Node. js webes API futtatása

1. Nyisson meg egy konzolablak ablakát, és váltson a Node. js webes API-mintát tartalmazó könyvtárra. Például:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Futtassa az alábbi parancsot:

    ```console
    npm install && npm update
    node index.js
    ```

    A konzol ablaka az alkalmazás által üzemeltetett portszámot jeleníti meg.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Egy egyoldalas alkalmazás futtatása

1. Nyisson meg egy másik konzolablak ablakát, és váltson át a JavaScript SPA-mintát tartalmazó könyvtárba. Például:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. Futtassa az alábbi parancsot:

    ```console
    npm install && npm update
    npm start
    ```

    A konzolablak megjeleníti a portszámot, ahol az alkalmazás üzemeltetve van.

    ```console
    Listening on port 6420...
    ```

1. `http://localhost:6420` Az alkalmazás megtekintéséhez navigáljon a böngészőben.

    ![Egy egyoldalas alkalmazás mintája a böngészőben megjelenítve](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Jelentkezzen be az [előző oktatóanyagban](tutorial-single-page-app.md)használt e-mail-cím és jelszó használatával. A sikeres bejelentkezés után megjelenik az `User 'Your Username' logged-in` üzenet.
1. Válassza az **API hívása** gombot. A fürdő Azure AD B2Ctól kapott engedélyezési engedélyt, majd hozzáfér a védett webes API-hoz a bejelentkezett felhasználó nevének megjelenítéséhez:

    ![Egyoldalas alkalmazás a böngészőben, amely az API által visszaadott Felhasználónév JSON-eredményt jeleníti meg](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Webes API-alkalmazás regisztrációjának létrehozása a Azure AD B2C-bérlőben
> * A webes API konfigurált hatókörei
> * Engedélyek megadása a webes API-nak
> * Webes API-kód mintájának módosítása a Bérlővel való együttműködéshez

Most, hogy már látott egy SPA-kérést egy védett webes API-ból származó erőforrással, mélyebben megértette, hogy ezek az alkalmazások hogyan hatnak egymással és a Azure AD B2C.

> [!div class="nextstepaction"]
> [Active Directory B2C >használható alkalmazások típusai](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
