---
title: 'Oktatóanyag: A Node.js webes API védelme az Azure AD B2C használatával, és hozzáférés megadása egy egyoldalas alkalmazáshoz (SPA)'
titleSuffix: Azure AD B2C
description: Ebben az oktatóanyagban megtudhatja, hogyan védheti meg a Node.js webes API-t az Active Directory B2C használatával, és hogyan hívhatja meg egyegyoldalas alkalmazásból.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875679"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Oktatóanyag: A Node.js webes API védelme és hozzáférés megadása egy egyoldalas alkalmazásból az Azure AD B2C-vel

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy Azure Active Directory B2C (Azure AD B2C) védett Node.js webes API-t egy egyoldalas alkalmazásból.

Ebben az oktatóanyagban a második egy kétrészes sorozatban:

> [!div class="checklist"]
> * Webes API-alkalmazásregisztráció létrehozása az Azure AD B2C-bérlőben
> * A webes API hatókörének konfigurálása
> * Engedélyek megadása a webes API-hoz
> * Webes API-kódminta módosítása a bérlővel való együttműködésre

A sorozat [első oktatóanyagában](tutorial-single-page-app.md) letöltötte a kódmintát, és módosította, hogy az Azure AD B2C-bérlőben felhasználói folyamattal rendelkező felhasználók jelentkezzen be.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag lépéseinek és előfeltételeinek [végrehajtásához: Hitelesítés engedélyezése egyoldalas alkalmazásban az Azure AD B2C-vel](tutorial-single-page-app.md)
* [Visual Studio-kód](https://code.visualstudio.com/) vagy más kódszerkesztő
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban határozza meg a webes API olvasási és írási engedélyeit is.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Rögzítse az értéket a `demo.read` **Hatókör öklében** a hatókör höz, amelyet egy későbbi lépésben kell használni az egyoldalas alkalmazás konfigurálásakor. A teljes hatókör értéke `https://contosob2c.onmicrosoft.com/api/demo.read`hasonló a hoz.

## <a name="grant-permissions"></a>Engedélyek megadása

Egy védett webes API-t egy másik alkalmazásból való hívásához meg kell adnia az alkalmazásnak a webes API-nak.

Az előfeltételként szolgáló oktatóanyagban létrehozott egy *webapp1*nevű webalkalmazást. Ebben az oktatóanyagban úgy konfigurálja az alkalmazást, hogy meghívja az előző szakaszban létrehozott *web API-t, a webapi1-et.*

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Az egyoldalas webalkalmazás most már engedélyt kapott a védett webes API-hoz a megadott hatókörökhöz. A felhasználó hitelesíti magát az Azure AD B2C az egyoldalas alkalmazás használatához. Az egyoldalas alkalmazás az engedélyezési engedélyezési folyamat használatával éri el a védett webes API-t az Azure AD B2C által visszaadott hozzáférési jogkivonattal.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy a webes API regisztrálva van, és már definiálta a hatóköröket, konfigurálja a webes API-kódot az Azure AD B2C-bérlővel való együttműködésre. Ebben az oktatóanyagban konfigurálja a GitHubról letöltött Node.js webes API-t.

[Töltsön \*le egy .zip archívumot,](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) vagy klónozza a mintawebes API-projektet a GitHubról. Közvetlenül is böngészhet az [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) projektben a GitHubon.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. Nyissa meg a *config.js* fájlt a kódszerkesztőben.
1. Módosítsa a változóértékeket úgy, hogy azok tükrözzék a korábban létrehozott alkalmazásregisztráció értékeit. Frissítse az `policyName` előfeltételek részeként létrehozott felhasználói folyamattal is. Például *B2C_1_signupsignin1*.

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>CORS engedélyezése

Ahhoz, hogy az egyoldalas alkalmazás hívja meg a Node.js webes API-t, engedélyeznie kell a [CORS-t](https://expressjs.com/en/resources/middleware/cors.html) a webes API-ban. Egy éles alkalmazás ban óvatosnak kell lennie azzal kapcsolatban, hogy melyik tartomány teszi a kérelmet, de ebben az oktatóanyagban bármely tartományból érkező kérelmek engedélyezése.

A CORS engedélyezéséhez használja a következő köztes szoftvert. A Node.js webes API-kód minta ebben az oktatóanyagban, már hozzá lett adva az *index.js* fájlhoz.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Az egyoldalas alkalmazás konfigurálása

Az egyoldalas alkalmazás (SPA) az [előző oktatóanyag](tutorial-single-page-app.md) a sorozatban az Azure AD B2C a felhasználói regisztráció és a bejelentkezés, és alapértelmezés szerint meghívja a Node.js webes API a *fabrikamb2c* demo bérlő által védett.

Ebben a szakaszban frissíti az egyoldalas webalkalmazást, hogy meghívja a Node.js web API-t *az* Azure AD B2C bérlő (és amely a helyi gépen fut).

A spa beállításainak módosítása:

1. Az előző oktatóanyagban letöltött vagy klónozott [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] projektben nyissa meg az *apiConfig.js* fájlt a *JavaScriptSPA* mappában.
1. Konfigurálja a mintát a korábban létrehozott *demo.read* hatókör URI-jával és a webes API URL-címével.
    1. A `apiConfig` definícióban cserélje `b2cScopes` le az értéket a *demo.read* hatókör (a korábban rögzített **Hatókör** érték) teljes URI-jára.
    1. Módosítsa a tartományt az `webApi` értékben a web API-alkalmazás egy korábbi lépésben történő regisztrálásakor hozzáadott átirányítási URI-ra.

    Mivel az API elérhető `/hello` a végponton, hagyja *a /hello* az URI-ban.

    A `apiConfig` definíciónak a következő kódblokkhoz hasonlóan kell kinéznie, de a `<your-tenant-name>`B2C bérlő neve a következő helyen:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Az SPA és a webes API futtatása

Most már készen áll az egyoldalas alkalmazás hatókörrel rendelkező API-hozzáférésének tesztelésére. Futtassa a Node.js webes API-t és a minta JavaScript egyoldalas alkalmazást a helyi gépen. Ezután jelentkezzen be az egyoldalas alkalmazásba, és válassza az **API hívása** gombot a védett API-ra vonatkozó kérelem kezdeményezéséhez.

Bár mindkét alkalmazás helyileg fut, ha követi ezt az oktatóanyagot, konfigurálta őket, hogy az Azure AD B2C biztonságos regisztráció/bejelentkezés, és hozzáférést biztosít a védett webes API-t.

### <a name="run-the-nodejs-web-api"></a>A Node.js webes API futtatása

1. Nyisson meg egy konzolablakot, és váltson a Node.js webes API-mintát tartalmazó könyvtárra. Például:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Futtassa az alábbi parancsot:

    ```console
    npm install && npm update
    node index.js
    ```

    A konzolablak ban megjelenik az alkalmazás azon portszáma, ahol az alkalmazás található.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Az egyoldalas alkalmazás futtatása

1. Nyisson meg egy másik konzolablakot, és váltson a JavaScript SPA mintát tartalmazó könyvtárra. Például:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. Futtassa az alábbi parancsot:

    ```console
    npm install && npm update
    npm start
    ```

    A konzolablak az alkalmazás azon portszámát jeleníti meg.

    ```console
    Listening on port 6420...
    ```

1. Keresse `http://localhost:6420` meg a böngészőben az alkalmazás megtekintéséhez.

    ![Egyoldalas alkalmazásminta alkalmazás a böngészőben](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Jelentkezzen be az [előző oktatóanyagban](tutorial-single-page-app.md)használt e-mail címmel és jelszóval. Sikeres bejelentkezéskor látnia kell `User 'Your Username' logged-in` az üzenetet.
1. Válassza az **API hívása** gombot. Az SPA engedélyezési engedélyt kap az Azure AD B2C-től, majd hozzáfér a védett webes API-hoz a bejelentkezett felhasználó nevének megjelenítéséhez:

    ![Egyoldalas alkalmazás a böngészőben, amely az API által visszaadott JSON-eredményt jeleníti meg](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Létrehozott egy webes API-alkalmazás regisztrációt az Azure AD B2C-bérlőben
> * A webes API konfigurált hatókörei
> * A webes API-hoz megadott engedélyek
> * Webes API-kódminta módosítása a bérlővel való együttműködésre

Most, hogy látta, hogy egy SPA egy erőforrást kér egy védett webes API-ból, mélyebben megismerheti, hogy ezek az alkalmazástípusok hogyan kommunikálnak egymással és az Azure AD B2C-vel.

> [!div class="nextstepaction"]
> [Az Active Directory B2C >használható alkalmazástípusok](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
