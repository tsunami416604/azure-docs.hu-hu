---
title: Oktatóanyag – a Node. js webes API-khoz való hozzáférés engedélyezése egy asztali alkalmazásból – Azure Active Directory B2C | Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Active Directory B2C-t egy Node.js webes API védelmére és meghívására egy asztali .NET-alkalmazásból.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 35d850dc6439173c83730375c576855f6920b450
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475229"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Oktatóanyag: Node.js webes API-khoz való hozzáférés engedélyezése egy asztali alkalmazásból az Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg Azure Active Directory B2C (Azure AD B2C) által védett Node. js webes API-t egy Windows megjelenítési alaprendszer (WPF) asztali alkalmazásból, amelyet Azure AD B2C is véd.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta frissítése az alkalmazás használatára

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő lépéseket és előfeltételeket az [oktatóanyagban: felhasználók hitelesítése natív asztali ügyfélen](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetővé teszik a védett erőforrásokhoz való hozzáférés szabályozását. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban meghatározzuk az olvasási és írási engedélyeket a webes API számára.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Jegyezze fel az értéket az `demo.read` hatókörhöz tartozó **hatókörök** területen az asztali alkalmazás konfigurálásakor egy későbbi lépésben való használatra. A hatókör teljes értéke hasonló a `https://contosob2c.onmicrosoft.com/api/demo.read`hoz.

## <a name="grant-permissions"></a>Engedélyek megadása

A védett webes API-k natív ügyfélalkalmazások általi meghívásához meg kell adnia a regisztrált natív ügyfélalkalmazás-engedélyeket a Azure AD B2C regisztrált webes API-hoz.

Az előfeltételként szolgáló oktatóanyagban regisztrált egy *nativeapp1*nevű natív ügyfélalkalmazás. A következő lépésekkel konfigurálhatja, hogy a natív alkalmazás regisztrálása az előző szakaszban *webapi1* elérhető API-hatókörökkel történjen. Ez lehetővé teszi, hogy az asztali alkalmazás olyan hozzáférési jogkivonatot szerezzen be Azure AD B2Ctól, amelyet a webes API használhat az erőforrásokhoz való hatókörön belüli hozzáférés ellenőrzéséhez és biztosításához. Az oktatóanyag későbbi részében az asztali alkalmazás és a webes API-kód is konfigurálható és futtatható.

#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. Válassza az **alkalmazások**, majd a *nativeapp1*lehetőséget.
1. Válassza az **API-hozzáférés**lehetőséget, majd kattintson a **Hozzáadás**gombra.
1. Az **API kiválasztása** legördülő menüben válassza a *webapi1*lehetőséget.
1. A **hatókörök kiválasztása** legördülő menüben válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Kattintson az **OK** gombra.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza ki azt a natív ügyfélalkalmazás-alkalmazást, amelynek hozzáféréssel kell rendelkeznie az API-hoz. Például: *nativeapp1*.
1. A **kezelés**területen válassza az **API-engedélyek**lehetőséget.
1. A **konfigurált engedélyek**területen válassza **az engedély hozzáadása**elemet.
1. Válassza a **saját API** -k fület.
1. Válassza ki azt az API-t, amelyhez hozzáférést szeretne adni a natív ügyfélalkalmazás számára. Például: *webapi1*.
1. Az **engedély**területen bontsa ki a **bemutató**elemet, majd válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Válassza az **engedélyek hozzáadása**lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblép a következő lépésre.
1. Válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget.
1. Válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy olyan fiókkal a Azure AD B2C-bérlőben, amely legalább a *Cloud Application Administrator* szerepkörhöz van rendelve.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **frissítés**lehetőséget, majd ellenőrizze, hogy a "engedélyezve..." mindkét hatókör **állapota** alatt jelenik meg. Eltarthat néhány percig, amíg az engedélyek propagálása megtörténik.

* * *

A felhasználó az Azure AD B2C használatával hitelesíti a WPF asztali alkalmazást. Az asztali alkalmazás a védett webes API-hoz való hozzáféréshez Azure AD B2C engedélyezési engedélyt kap.

## <a name="configure-the-samples"></a>A minták konfigurálása

Most, hogy regisztrálta a webes API-t, és hatóköröket és engedélyeket konfigurált, konfigurálja az asztali alkalmazást és a webes API-mintákat a Azure AD B2C bérlő használatára.

### <a name="update-the-desktop-application"></a>Asztali alkalmazás frissítése

A cikk előfeltétele, hogy egy [WPF asztali alkalmazást](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) módosítson, hogy engedélyezze a bejelentkezést egy felhasználói folyamattal a Azure ad B2C-bérlőben. Ebben a szakaszban ugyanazt az alkalmazást frissíti, hogy a korábban regisztrált webes API-ra, a *webapi1*-ra hivatkozzon.

1. Nyissa meg az **Active Directory-B2C-WPF** megoldást (`active-directory-b2c-wpf.sln`) a Visual Studióban.
1. Az **Active Directory-B2C-WPF** projektben nyissa meg a *app.XAML.cs* fájlt, és keresse meg az alábbi változók definícióit.
    1. Cserélje le a `ApiScopes` változó értékét a **demo. Read** hatókör meghatározásakor korábban rögzített értékre.
    1. Cserélje le a `ApiEndpoint` változó értékét a korábban feljegyzett **átirányítási URI-ra** , amikor a bérlőben regisztrálta a webes API-t (például *webapi1*).

    Például:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>A Node. js API-minta beolvasása és frissítése

Ezután szerezze be a Node. js webes API-kód mintát a GitHubról, és konfigurálja úgy, hogy az Azure AD B2C-bérlőben regisztrált webes API-t használja.

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

A mintául szolgáló Node.js webes API a Passport.js kódtárat használja arra, hogy engedélyezze az Azure AD B2C-nek az API felé irányuló hívások védelmét.

1. Nyissa meg az `index.js` fájlt.
1. Frissítse ezeket a változók definícióit a következő értékekkel. Módosítsa `<web-API-application-ID>` a korábban regisztrált webes API **alkalmazás-(ügyfél-) azonosítójára** (*webapi1*). Módosítsa `<your-b2c-tenant>` a Azure AD B2C bérlő nevére.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Mivel helyileg futtatja az API-t, frissítse a GET metódus útvonalát a beolvasási módszer `/` helyett a bemutató alkalmazás helye `/hello`:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>A minták futtatása

### <a name="run-the-nodejs-web-api"></a>A Node. js webes API futtatása

1. Indítsa el a Node.js parancssort.
2. Váltson arra a könyvtárra, amelyben a Node.js-minta található. Például: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Futtassa az alábbi parancsot:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Asztali alkalmazás futtatása

1. Nyissa meg az **Active-Directory-B2C-WPF** megoldást a Visual Studióban.
2. Az asztali alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Jelentkezzen be a [felhasználók asztali alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-desktop-app.md) foglalkozó oktatóanyagban használt e-mail-címmel és jelszóval.
4. Válassza az **API hívása** gombot.

Az asztali alkalmazás egy kérést küld a helyileg futó webes API-nak, és egy érvényes hozzáférési jogkivonat ellenőrzése után megjeleníti a bejelentkezett felhasználó megjelenítendő nevét.

![A WPF asztali alkalmazás felső ablaktábláján megjelenő megjelenítendő név](media/active-directory-b2c-tutorials-desktop-app-webapi/desktop-app-01-post-api-call.png)

A Azure AD B2C által védett asztali alkalmazás a helyileg futó webes API-t hívja meg, amelyet Azure AD B2C is véd.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta frissítése az alkalmazás használatára

> [!div class="nextstepaction"]
> [Oktatóanyag: identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure Active Directory B2C](tutorial-add-identity-providers.md)
