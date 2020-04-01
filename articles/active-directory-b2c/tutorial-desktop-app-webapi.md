---
title: 'Oktatóanyag: Hozzáférés a Node.js webes API-hoz asztali alkalmazásból'
description: Oktatóanyag arról, hogyan védheti meg a Node.js webes API-k at az Active Directory B2C segítségével, és hogyan hívhatja meg egy .NET asztali alkalmazásból.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 59670cda68f54e4c0b20b361f0688e6766acba61
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183378"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Oktatóanyag: Node.js webes API-khoz való hozzáférés engedélyezése egy asztali alkalmazásból az Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhatja meg az Azure Active Directory B2C (Azure AD B2C) által védett Node.js webes API-t egy Windows Presentation Foundation (WPF) asztali alkalmazásból, amelyet szintén az Azure AD B2C véd.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API-hoz
> * A minta frissítése az alkalmazás használatához

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az oktatóanyag lépéseit és [előfeltételeit: Felhasználók hitelesítése natív asztali ügyfélprogramban](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban meghatározzuk az olvasási és írási engedélyeket a webes API számára.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Rögzítse a **hatókör hatóköre** alatti értéket egy `demo.read` későbbi lépésben az asztali alkalmazás konfigurálásakor. A teljes hatókör értéke `https://contosob2c.onmicrosoft.com/api/demo.read`hasonló a hoz.

## <a name="grant-permissions"></a>Engedélyek megadása

A védett webes API-t egy natív ügyfélalkalmazásból való hívásához meg kell adnia a regisztrált natív ügyfélalkalmazás-engedélyeket az Azure AD B2C-ben regisztrált webes API-hoz.

Az előfeltételként szolgáló oktatóanyagban regisztrált egy natív ügyfélalkalmazást, amelynek neve *natívapp1.* A következő lépések konfigurálják, hogy a natív alkalmazás regisztrációja az API-t elérhetővé tett *WEBAPI1* az előző szakaszban elérhetővé tett API-hatókörökkel. Ez lehetővé teszi, hogy az asztali alkalmazás hozzáférési jogkivonatot szerezzen be az Azure AD B2C-ből, amelyet a webes API az erőforrások ellenőrzéséhez és hatókörrel rendelkező hozzáférés biztosításához használhat. Az oktatóanyag későbbi részében konfigurálhatja és futtathatja az asztali alkalmazás és a webes API-kódmintákat is.

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Válassza **az Alkalmazások**lehetőséget, majd a *natív alkalmazás1*lehetőséget.
1. Válassza az **API-hozzáférés**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. Az **API kiválasztása** legördülő menüben válassza a *webapi1*lehetőséget.
1. A **Scopes kiválasztása** legördülő menüben jelölje ki a korábban definiált hatóköröket. *Például, demo.read* és *demo.write*.
1. Válassza **az OK gombot.**

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza **az Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza ki azt a natív ügyfélalkalmazást, amelynek hozzáféréssel kell rendelkeznie az API-hoz. Például *nativeapp1*.
1. A **Kezelés csoportban**válassza az **API-engedélyek lehetőséget.**
1. A **Konfigurált engedélyek**csoportban válassza **az Engedély hozzáadása**lehetőséget.
1. Válassza a **Saját API-k** lapot.
1. Válassza ki azt az API-t, amelyhez a natív ügyfélalkalmazásnak hozzáférést kell biztosítani. Például *webapi1*.
1. Az **Engedély csoportban**bontsa ki a **bemutatót,** majd jelölje ki a korábban definiált hatóköröket. *Például, demo.read* és *demo.write*.
1. Válassza **az Engedélyek hozzáadása**lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblépne a következő lépésre.
1. Válassza **a Rendszergazdai hozzájárulás megadása (a bérlő neve) lehetőséget.**
1. Válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy fiókkal az Azure AD B2C-bérlőben, amely legalább a *felhőalkalmazás-rendszergazdai* szerepkörhöz van rendelve.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **Frissítés**lehetőséget, majd ellenőrizze, hogy a "Granted for ..." mindkét hatókör **Állapota** csoportban jelenik meg. Az engedélyek propagálása eltarthat néhány percig.

* * *

A WPF asztali alkalmazás használatához a felhasználó az Azure AD B2C-vel hitelesíti magát. Az asztali alkalmazás lekéri az engedélyezést az Azure AD B2C-ből a védett webes API-hoz való hozzáféréshez.

## <a name="configure-the-samples"></a>A minták konfigurálása

Most, hogy a webes API regisztrálva van, és konfigurált hatókörökés engedélyek, konfigurálja az asztali alkalmazás és a webes API-mintákat az Azure AD B2C-bérlő használatára.

### <a name="update-the-desktop-application"></a>Az asztali alkalmazás frissítése

A cikk előfeltétele, hogy módosítottegy [WPF asztali alkalmazás,](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) amely lehetővé teszi a bejelentkezést egy felhasználói folyamat az Azure AD B2C bérlőben. Ebben a szakaszban frissíti ugyanazt az alkalmazást, hogy hivatkozzon a korábban regisztrált webes *API-ra, a webapi1-re.*

1. Nyissa meg az **active-directory-b2c-wpf** megoldást (`active-directory-b2c-wpf.sln`) a Visual Studióban.
1. Az **active-directory-b2c-wpf** projektben nyissa meg a *App.xaml.cs* fájlt, és keresse meg a következő változódefiníciókat.
    1. Cserélje le a `ApiScopes` változó értékét a **demo.read** hatókör definiálásakor korábban rögzített értékre.
    1. Cserélje le a `ApiEndpoint` változó értékét a bérlőben a web API (például *webapi1)* regisztrálásakor korábban rögzített **átirányítási URI-ra.**

    Például:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>A Node.js API-minta beszerezni és frissítése

Ezután a Node.js web API-kód mintát a GitHubról, és konfigurálja, hogy az Azure AD B2C-bérlőben regisztrált webes API-t használja.

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

A mintául szolgáló Node.js webes API a Passport.js kódtárat használja arra, hogy engedélyezze az Azure AD B2C-nek az API felé irányuló hívások védelmét.

1. Nyissa meg az `index.js` fájlt.
1. Frissítse ezeket a változódefiníciókat a következő értékekkel. A `<web-API-application-ID>` korábban regisztrált webes API *(webapi1)* **alkalmazásazonosítójának (ügyfélazonosítójának)** módosítása. Módosítsa `<your-b2c-tenant>` az Azure AD B2C-bérlő nevét.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Mivel helyileg futtatja az API-t, frissítse a GET `/` metódus útvonalának elérési útját `/hello`a bemutató alkalmazás helye helyett:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>A minták futtatása

### <a name="run-the-nodejs-web-api"></a>A Node.js webes API futtatása

1. Indítsa el a Node.js parancssort.
2. Váltson arra a könyvtárra, amelyben a Node.js-minta található. Például: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Futtassa az alábbi parancsot:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Az asztali alkalmazás futtatása

1. Nyissa meg az **active-directory-b2c-wpf** megoldást a Visual Studióban.
2. Az asztali alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Jelentkezzen be a [felhasználók asztali alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](tutorial-desktop-app.md) foglalkozó oktatóanyagban használt e-mail-címmel és jelszóval.
4. Válassza az **API hívása** gombot.

Az asztali alkalmazás kérést küld a helyileg futó webes API-nak, és egy érvényes hozzáférési jogkivonat ellenőrzése után megjeleníti a bejelentkezett felhasználó megjelenítendő nevét.

![A WPF asztali alkalmazás felső ablaktáblájában látható megjelenítendő név](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Az Azure AD B2C által védett asztali alkalmazás a helyileg futó webes API-t hívja meg, amelyet az Azure AD B2C is véd.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API-hoz
> * A minta frissítése az alkalmazás használatához

> [!div class="nextstepaction"]
> [Oktatóanyag: Identitásszolgáltatók hozzáadása az alkalmazásokhoz az Azure Active Directory B2C-ben](tutorial-add-identity-providers.md)
