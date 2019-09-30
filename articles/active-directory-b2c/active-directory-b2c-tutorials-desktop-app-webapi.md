---
title: Oktatóanyag – a Node. js webes API-khoz való hozzáférés engedélyezése egy asztali alkalmazásból – Azure Active Directory B2C | Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Active Directory B2C-t egy Node.js webes API védelmére és meghívására egy asztali .NET-alkalmazásból.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679325"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Oktatóanyag: Hozzáférés biztosítása egy Node. js webes API-hoz egy asztali alkalmazásból Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy Azure Active Directory B2C (Azure AD B2C) védett Node. js webes API-erőforrást egy Windows megjelenítési alaprendszer (WPF) asztali alkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta frissítése az alkalmazás használatára

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a lépéseket és az [előfeltételeket az oktatóanyagban: Az asztali alkalmazások hitelesítésének engedélyezése a fiókokkal Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md)használatával.

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetővé teszik a védett erőforrásokhoz való hozzáférés szabályozását. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban meghatározzuk az olvasási engedélyeket a webes API számára.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Engedélyek megadása

Ha egy védett webes API-t szeretne meghívni egy alkalmazásból, meg kell adnia az alkalmazás engedélyeit az API-nak. Az előfeltételként szolgáló oktatóanyagban létrehozott egy webalkalmazást a *App1*nevű Azure ad B2Cban. Ezt az alkalmazást használhatja a webes API meghívásához.

1. Válassza az **alkalmazások**, majd a *nativeapp1*lehetőséget.
1. Válassza az **API-hozzáférés**lehetőséget, majd kattintson a **Hozzáadás**gombra.
1. Az **API kiválasztása** legördülő menüben válassza a *webapi1*lehetőséget.
1. A **hatókörök kiválasztása** legördülő menüben válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Kattintson az **OK** gombra.

A felhasználó az Azure AD B2C használatával hitelesíti a WPF asztali alkalmazást. Az asztali alkalmazás a védett webes API-hoz való hozzáféréshez Azure AD B2C engedélyezési engedélyt kap.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy regisztrálta a webes API-t, és hatókörök vannak definiálva, a webes API-kódot a Azure AD B2C bérlő használatára konfigurálja. Ebben az oktatóanyagban egy példa Node. js-webalkalmazást fog konfigurálni, amelyet a GitHubról tölthet le.

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

A mintául szolgáló Node.js webes API a Passport.js kódtárat használja arra, hogy engedélyezze az Azure AD B2C-nek az API felé irányuló hívások védelmét.

1. Nyissa meg az `index.js` fájlt.
2. Konfigurálja a mintát az Azure AD B2C-bérlő regisztrációs információival. Módosítsa a következő kódsorokat:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Minta futtatása

1. Indítsa el a Node.js parancssort.
2. Váltson arra a könyvtárra, amelyben a Node.js-minta található. Például: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Futtassa az alábbi parancsot:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Asztali alkalmazás futtatása

1. Nyissa meg az **Active-Directory-B2C-WPF** megoldást a Visual Studióban.
2. Az asztali alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Jelentkezzen be a [felhasználók asztali alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-desktop-app.md) foglalkozó oktatóanyagban használt e-mail-címmel és jelszóval.
4. Kattintson az **API meghívása** gombra.

Az asztali alkalmazás egy kérést küld a webes API-nak, és választ kap a bejelentkezett felhasználó megjelenítendő nevével. A védett asztali alkalmazás meghívja a védett webes API-t a Azure AD B2C-bérlőben.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta frissítése az alkalmazás használatára

> [!div class="nextstepaction"]
> [Oktatóanyag: Identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure Active Directory B2C](tutorial-add-identity-providers.md)
