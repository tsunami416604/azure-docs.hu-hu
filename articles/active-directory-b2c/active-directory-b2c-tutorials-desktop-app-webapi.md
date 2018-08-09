---
title: Oktatóanyag – Node.js webes API-khoz való hozzáférés engedélyezése egy asztali alkalmazásból az Azure Active Directory B2C használatával | Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Active Directory B2C-t egy Node.js webes API védelmére és meghívására egy asztali .NET-alkalmazásból.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 98c86f5613116dce5423aa9ca6a2ff43e5414592
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594780"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Oktatóanyag: Node.js webes API-khoz való hozzáférés engedélyezése egy asztali alkalmazásból az Azure Active Directory B2C használatával

Az oktatóanyag azt mutatja be, hogyan hívhat meg egy Azure Active Directory (Azure AD) B2C-vel védett Node.js webes API-erőforrást egy asztali Windows megjelenítési alaprendszeri (WPF-) alkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API regisztrálása az Azure AD B2C-bérlőben
> * Webes API hatóköreinek meghatározása és konfigurálása
> * Alkalmazásengedélyek megadása a webes API számára
> * Mintakód frissítése egy webes API Azure AD B2C-vel történő védelméhez

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Végezze el a [felhasználók asztali alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-desktop-app.md) foglalkozó oktatóanyagot.
* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) telepítése **.NET asztali fejlesztési**, valamint **ASP.NET és webfejlesztési** számítási feladatokkal.
* [Node.js](https://nodejs.org/en/download/) telepítése

## <a name="register-web-api"></a>Webes API regisztrálása

A webes API-erőforrásoknak regisztrálva kell lenniük a bérlőben, mielőtt fogadni és válaszolni tudnának az [ügyfélalkalmazások](../active-directory/develop/developer-glossary.md#client-application) által leadott, [védett erőforrásokra vonatkozó kérelmekre](../active-directory/develop/developer-glossary.md#resource-server), amelyekhez egy, az Azure Active Directoryból származó [hozzáférési jogkivonat](../active-directory/develop/developer-glossary.md#access-token) tartozik. A regisztráció meghatározza az [alkalmazás- és szolgáltatásnév-objektumot](../active-directory/develop/developer-glossary.md#application-object) a bérlőben. 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Az Azure Portalon válassza az **Azure AD B2C** lehetőséget a szolgáltatások listájából.

2. A B2C beállításaiban kattintson az **Alkalmazások**, majd a **Hozzáadás** lehetőségre.

    A mintául szolgáló webes API bérlőben történő regisztrálásához használja a következő beállításokat.
    
    ![Új API hozzáadása](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | Mintául szolgáló saját Node.js webes API | Adjon meg egy olyan **nevet**, amely megfelelően körülírja a webes API-t a felhasználók számára. |
    | **Webalkalmazás vagy webes API szerepeltetése** | Igen | Válassza az **Igen** lehetőséget a webes API-k esetén. |
    | **Implicit folyamat engedélyezése** | Igen | Válassza az **Igen** lehetőséget, mivel az API [OpenID Connect bejelentkezést](active-directory-b2c-reference-oidc.md) használ. |
    | **Válasz URL-cím** | `http://localhost:5000` | A válasz URL-címek olyan végpontok, amelyeken keresztül az Azure AD B2C visszaadja az API által kért jogkivonatokat. Ebben az oktatóanyagban a mintául szolgáló webes API helyileg fut (localhost), és az 5000-es porton figyel. |
    | **Alkalmazásazonosító URI** | demoapi | Az URI egyedileg azonosítja az API-t a bérlőben. Ez lehetővé teszi, hogy bérlőnként több API-t is regisztráljon. A [hatókörök](../active-directory/develop/developer-glossary.md#scopes) szabályozzák a hozzáférést a védett API-erőforrásokhoz, és alkalmazásazonosító URI-nként vannak meghatározva. |
    | **Natív ügyfél** | Nem | Mivel ez egy webes API, nem pedig egy natív ügyfél, válassza a Nem lehetőséget. |
    
3. Kattintson a **Létrehozás** gombra az API regisztrálásához.

A regisztrált API-k az Azure AD B2C-bérlő alkalmazásainak listájában jelennek meg. Válassza ki webes API-ját a listáról. Ekkor megjelenik a webes API tulajdonságpanelje.

![Webes API – Tulajdonságok](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Jegyezze fel az **alkalmazás ügyfél-azonosítóját**. Az azonosító egyedi módon azonosítja az API-t, és az oktatóanyag későbbi részében, az alkalmazás konfigurálásakor lesz rá szükség.

A webes API Azure AD B2C-vel végzett regisztrációja egy megbízhatósági kapcsolatot határoz meg. Mivel az API B2C-vel van regisztrálva, megbízhat a más alkalmazásoktól kapott B2C hozzáférési jogkivonatokban.

## <a name="define-and-configure-scopes"></a>Hatókörök meghatározása és konfigurálása

A [hatókörök](../active-directory/develop/developer-glossary.md#scopes) lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban meghatározzuk az olvasási és írási engedélyeket a webes API számára.

### <a name="define-scopes-for-the-web-api"></a>A webes API hatóköreinek meghatározása

A regisztrált API-k az Azure AD B2C-bérlő alkalmazásainak listájában jelennek meg. Válassza ki webes API-ját a listáról. Ekkor megjelenik a webes API tulajdonságpanelje.

Kattintson a **Közzétett hatókörök (előzetes verzió)** gombra.

Az API hatóköreinek konfigurálásához adja meg a következő bejegyzéseket. 

![a webes API-ban meghatározott hatókörök](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Beállítás      | Ajánlott érték  | Leírás                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Hatókör** | demo.read | Olvasási hozzáférés a bemutató API-hoz|

Kattintson a **Save** (Mentés) gombra.

A közzétett hatókörök segítségével ügyfélalkalmazás-engedélyeket biztosíthat a webes API-nak.

### <a name="grant-app-permissions-to-web-api"></a>Alkalmazásengedélyek megadása a webes API-nak

Egy védett webes API alkalmazásból történő hívásához alkalmazásengedélyeket kell biztosítania az API számára. Ebben az oktatóanyagban használja a [felhasználók asztali alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-desktop-app.md) foglalkozó oktatóanyagban létrehozott asztali alkalmazást.

1. Válassza ki az Azure Portalon az **Azure AD B2C** elemet a szolgáltatások listájáról, majd kattintson az **Alkalmazások** lehetőségre a regisztrált alkalmazások listájának megjelenítéséhez.

2. Válassza ki a **Mintául szolgáló saját WPF-alkalmazás** elemet az alkalmazáslistából, és kattintson az **API-hozzáférés (előzetes verzió)**, majd a **Hozzáadás** gombra.

3. Az **API kiválasztása** legördülő menüben válassza ki a regisztrált webes API-t: **Mintául szolgáló saját Node.js webes API**.

4. A **Hatókörök kiválasztása** legördülő menüben válassza ki azt a hatókört, amelyet a webes API regisztrációja során megadott.

    ![alkalmazás hatóköreinek kiválasztása](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Kattintson az **OK** gombra.

A **Mintául szolgáló saját WPF-alkalmazás** regisztrálva van a védett **Mintául szolgáló saját Node.js webes API** meghívásához. A WPF asztali alkalmazás használatához a felhasználó az Azure AD B2C-vel [hitelesíti magát](../active-directory/develop/developer-glossary.md#authentication). Az asztali alkalmazás lekéri az [engedélyezést](../active-directory/develop/developer-glossary.md#authorization-grant) az Azure AD B2C-ből a védett webes API-hoz való hozzáféréshez.

## <a name="update-web-api-code"></a>Webes API kódjának frissítése

Most, hogy regisztrálta a webes API-t és meghatározta a hatóköröket, konfigurálnia kell a webes API kódját az Azure AD B2C-bérlő használatához. Ebben az oktatóanyagban a GitHubról letölthető, mintául szolgáló Node.js-webalkalmazást fog konfigurálni. 

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
A mintául szolgáló Node.js webes API a Passport.js kódtárat használja arra, hogy engedélyezze az Azure AD B2C-nek az API felé irányuló hívások védelmét. 

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. Nyissa meg az `index.html` fájlt a mintául szolgáló Node.js webes API-ban.
2. Konfigurálja a mintát az Azure AD B2C-bérlő regisztrációs információival. Módosítsa a következő kódsorokat:

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>Az asztali alkalmazás konfigurálása

1. Nyissa meg a [felhasználók asztali alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-desktop-app.md) foglalkozó oktatóanyagból származó `active-directory-b2c-wpf` megoldást a Visual Studióban.

## <a name="run-the-sample"></a>Minta futtatása

A Node.js webes API futtatása:

1. Indítsa el a Node.js parancssort.
2. Váltson arra a könyvtárra, amelyben a Node.js-minta található. Például: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Futtassa az alábbi parancsot:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
Asztali alkalmazás futtatása:

1. Az asztali alkalmazás futtatásához nyomja le az **F5** billentyűt.
2. Jelentkezzen be a [felhasználók asztali alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-desktop-app.md) foglalkozó oktatóanyagban használt e-mail-címmel és jelszóval.
3. Kattintson az **API meghívása** gombra. 

Az asztali alkalmazás kérést küld a webes API-nak, és válaszként megkapja a bejelentkezett felhasználó megjelenítendő nevét. A védett asztali alkalmazás hívja a védett webes API-t az Azure AD B2C-bérlőben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C-oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>További lépések

Ez a cikk azon vezette végig, hogyan biztosíthat védelmet egy ASP.NET webes API-nak hatókörök Azure AD B2C-ben való regisztrálásával és meghatározásával. További információkért tekintse át az elérhető Azure AD B2C-kódmintákat.

> [!div class="nextstepaction"]
> [Azure AD B2C-kódminták](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
