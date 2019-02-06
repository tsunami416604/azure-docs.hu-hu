---
title: Oktatóanyag – való hozzáférés engedélyezése Egy Node.js webes API-t egy asztali alkalmazás – Azure Active Directory B2C |} A Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Active Directory B2C-t egy Node.js webes API védelmére és meghívására egy asztali .NET-alkalmazásból.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 90a6a88ff0dc5aab1163e471b24cd1d00e548a1b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755118"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Oktatóanyag: Egy Node.js webes API-t egy asztali alkalmazásból az Azure Active Directory B2C használatával való hozzáférés engedélyezése

Az oktatóanyag azt mutatja be, hogyan hívhat meg egy Azure Active Directory (Azure AD) B2C-vel védett Node.js webes API-erőforrást egy asztali Windows megjelenítési alaprendszeri (WPF-) alkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adjon hozzá egy webes API-alkalmazás
> * A webes API hatóköreinek konfigurálásáról
> * Adja meg az engedélyeket a webes API-hoz
> * A minta az alkalmazás frissítése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a lépéseket és az Előfeltételek [oktatóanyag: Asztali alkalmazás hitelesítés engedélyezése az Azure Active Directory B2C fiókkal rendelkező](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Adjon hozzá egy webes API-alkalmazás

Webes API-erőforrásokhoz regisztrálni kell a bérlőn belüli előtt fogadja el, és védett erőforrás-kérelmek megválaszolásához ügyfélalkalmazások, amelyek egy hozzáférési jogkivonatot. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy nevet az alkalmazásnak. Ha például *webapi1*.
6. A **közé tartozik a webalkalmazás vagy webes API** és **implicit engedélyezési folyamat engedélyezése**válassza **Igen**.
7. A **válasz URL-cím**, adja meg a végpont, ahol az Azure AD B2C-t adja vissza az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a `https://localhost:5000`.
8. A **Alkalmazásazonosító URI-t**, adja meg a webes API-hoz használt azonosító. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson a **Create** (Létrehozás) gombra.
10. A Tulajdonságok lapon jegyezze fel az Alkalmazásazonosítót, amelyek a webalkalmazás konfigurálásakor fogja használni.

## <a name="configure-scopes"></a>Hatókörök konfigurálása

Hatókört biztosít a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban meghatározzuk az olvasási engedélyeket a webes API számára.

1. Válassza ki **alkalmazások**, majd válassza ki *webapi1*.
2. Válassza ki **közzétett hatókörök**.
3. A **hatókör**, adja meg `Hello.Read`, és adja meg a leírást `Read access to hello`.
4. A **hatókör**, adja meg `Hello.Write`, és adja meg a leírást `Write access to hello`.
5. Kattintson a **Save** (Mentés) gombra.

A közzétett hatókörök segítségével ügyfélalkalmazás-engedélyeket biztosíthat a webes API-nak.

## <a name="grant-permissions"></a>Engedélyek megadása

Egy védett webes API hívása egy alkalmazásból, az alkalmazásengedélyeket az API-nak kell. Az előfeltételnek számító oktatóanyagot hozott létre egy webalkalmazást az Azure AD B2C nevű *app1*. Ez az alkalmazás használhatja a webes API meghívásához.

1. Válassza ki **alkalmazások**, majd válassza ki *nativeapp1*.
2. Válassza ki **API-hozzáférés**, majd válassza ki **Hozzáadás**.
3. Az a **API kiválasztása** legördülő menüben válasszon ki *webapi1*.
4. Az a **hatókörök kiválasztása** legördülő menüben válasszon ki a **Hello.Read** és **Hello.Write** , amelyet korábban megadott hatókörök.
5. Kattintson az **OK** gombra.

Egy felhasználó hitelesíti magát, az Azure AD B2C a WPF asztali alkalmazás használatához. Az asztali alkalmazás Azure AD B2C-vel hozzáférni a védett webes API-engedélyek kapja.

## <a name="configure-the-sample"></a>A minta konfigurálásához

Most, hogy a webes API regisztrálva van, és meghatározta a hatóköröket, konfigurálnia a webes API kódját az Azure AD B2C-bérlő használatához. Ebben az oktatóanyagban a githubról letölthető, mintául szolgáló node.js alapú webes alkalmazás konfigurálása. 

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

### <a name="run-the-desktop-application"></a>Az asztali alkalmazás futtatása

1. Nyissa meg a **active-directory-b2c-wpf** megoldást a Visual Studióban.
2. Az asztali alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Jelentkezzen be a [felhasználók asztali alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-desktop-app.md) foglalkozó oktatóanyagban használt e-mail-címmel és jelszóval.
4. Kattintson az **API meghívása** gombra. 

Az asztali alkalmazás kérést küld a webes API-t, és a bejelentkezett felhasználó megjelenített neve válaszként megkapja. Védett asztali alkalmazás hívja a védett webes API az Azure AD B2C-bérlőben.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adjon hozzá egy webes API-alkalmazás
> * A webes API hatóköreinek konfigurálásáról
> * Adja meg az engedélyeket a webes API-hoz
> * A minta az alkalmazás frissítése

> [!div class="nextstepaction"]
> [Oktatóanyag: Identitásszolgáltató hozzáadása az alkalmazásait az Azure Active Directory B2C-vel](tutorial-add-identity-providers.md)
