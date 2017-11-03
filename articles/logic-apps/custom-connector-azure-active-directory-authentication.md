---
title: "Az Azure AD - Azure Logic Apps egyéni összekötők biztonságos |} Microsoft Docs"
description: "Az API és az Azure Active Directoryval (Azure AD-) összekötő biztonsági és hitelesítési hozzáadása"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: bcee842cb880002daaa3ae9d9110ee1734941b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Az API és az Azure Active Directoryval (Azure AD-) összekötő biztonsági hozzáadása

Az API és az egyéni összekötő közötti hívások védelme érdekében az Azure AD hitelesítés hozzáadása a webes API- és az összekötő. Ebben a forgatókönyvben két Azure Active Directory (Azure AD)-alkalmazásai létrehozására – egy Azure AD-alkalmazás a webes API biztonságossá tételére, amíg a más Azure AD alkalmazás az összekötő regisztrálásának titkosítja, és hozzáadja a delegált hozzáférést. 

Ez az oktatóanyag az Azure Resource Manager API-t használ példaként. Az Azure Resource Manager kezelheti az összetevők, például adatbázisok, a virtuális gépek és a webalkalmazások létrehozott Azure-megoldások esetén. Az Azure Resource Manager egyéni összekötő hasznos lehet, amikor a munkafolyamatokat a Azure-erőforrások kezeléséhez. További információk: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, Kezdésként használhatja az egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/). Ellenkező esetben regisztráljon egy [használatalapú fizetés előfizetés](https://azure.microsoft.com/pricing/purchase-options/).

* Ebben az oktatóanyagban a [OpenAPI mintafájl Azure Resource Manager](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > A mintafájl OpenAPI összes Azure Resource Manager-művelet nem adja meg, és jelenleg tartalmazza a műveletet csak [listában az összes előfizetés](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List). Szerkessze a OpenAPI, vagy hozzon létre egy másik OpenAPI fájl használatával a [online OpenAPI szerkesztő](http://editor.swagger.io/).
  >
  > Ez az oktatóanyag ahol szeretné használni az Azure AD-alapú hitelesítés RESTful API-k alkalmazhat.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. A webes API biztonságossá tételéhez az első Azure AD-alkalmazás létrehozása

Az első Azure AD-alkalmazás hitelesítést hajt végre, ha az egyéni összekötő hívja az API-t, amely ebben a példában az Azure Resource Manager API.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha több Azure Active Directory-bérlőt, győződjön meg arról, hogy be van jelentkezve a megfelelő könyvtár a könyvtárat a felhasználónév ellenőrzésével. 

   ![Erősítse meg a könyvtár](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Módosítsa a könyvtárat, válassza ki a felhasználó nevét, majd válassza ki a kívánt címtárat.

2. A fő Azure menüben válasszon **Azure Active Directory** szeretné megjeleníteni a jelenlegi címtárban.

   ![Válassza ki a "Az Azure Active Directory"](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Ha nem jeleníti meg az Azure főmenü **Azure Active Directory**, válassza a **további szolgáltatások**. Az a **szűrő** mezőbe, írja be az "Azure Active Directory" szűrőként, majd válassza a **Azure Active Directory**.

3. A könyvtár menü alatt **kezelése**, válassza a **App regisztrációk**. A regisztrált alkalmazások listájában válassza ki a **+ új alkalmazás regisztrációja**.

   ![Válassza az "App regisztrációk", "+ új alkalmazás regisztrációja"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. A **létrehozása**, adatainak megadása az Azure AD-alkalmazáshoz, a táblázatban ismertetett módon, majd válassza a **létrehozása**. 

   ![Az Azure AD-alkalmazás létrehozása](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Name (Név)** | *webalkalmazás-api-alkalmazás-neve* | A Web API az Azure AD-alkalmazás | 
   | **Alkalmazás típusa** | **A webalkalmazás / API** | Az alkalmazás típusa | 
   | **Bejelentkezési URL-cím** | `https://login.windows.net` | | 
   |||| 

5. Amikor visszatér a címtár **App regisztrációk** listára, válassza ki az Azure AD-alkalmazáshoz.

   ![Válassza ki az Azure AD-alkalmazást a listából](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. Amikor megjelenik az alkalmazás részleteit megjelenítő oldalra, győződjön meg arról, hogy Ön **másolja ki és mentse az alkalmazást *Alkalmazásazonosító* biztonságos helyen**. Ezt az Azonosítót kell későbbi használatra.

   !["Alkalmazásazonosító" mentése a későbbi használatra](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Most adja meg egy válasz URL-CÍMEN az Azure AD-alkalmazáshoz. Az alkalmazás **beállítások** menüben válasszon **válasz URL-címek**. Adja meg az URL-cím, majd kattintson a **mentése**.

   ![Válasz URL-címek](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Válasz URL-címek** | A saját API-hoz adja meg az URL-cím: </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Delegált engedélyek** | {nem szükséges} | | 
   | **Ügyfél kulcs** | {nem szükséges} | | 
   |||| 

   > [!TIP]
   > Ha a **beállítások** menü korábban nem szerepelhet, válassza a **beállítások** itt:
   >
   > ![Válassza ki a "Beállítások"](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. A második az Azure AD-alkalmazás az egyéni összekötő létrehozása

A második az Azure AD-alkalmazás biztosítja az egyéni összekötő regisztrációját, és a delegált hozzáférést ad hozzá a Web API-t az első Azure AD-alkalmazás által védett. 

> [!IMPORTANT]
> Ellenőrizze, hogy létezik-e az Azure AD alkalmazásaiban ugyanabban a könyvtárban.

1. Lépjen vissza a **App regisztrációk** listában, és válassza a **+ új alkalmazás regisztrációja** újra.

   ![Válassza az "App regisztrációk", "+ új alkalmazás regisztrációja"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. A **létrehozása**, adatainak megadása a második az Azure AD-alkalmazás, a táblázatban ismertetett módon, majd válassza a **létrehozása**. 

   ![Az Azure AD-alkalmazás létrehozása](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Name (Név)** | *az összekötő neve* | Az összekötő az Azure AD-alkalmazás | 
   | **Alkalmazás típusa** | **A webalkalmazás / API** | Az alkalmazás típusa | 
   | **Bejelentkezési URL-cím** | `https://login.windows.net` | | 
   |||| 

3. Amikor visszatér a címtár **App regisztrációk** listára, válassza ki a második az Azure AD-alkalmazás.

   ![Válassza ki a listából a második az Azure AD-alkalmazás](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. Amikor megjelenik az alkalmazás részleteit megjelenítő oldalra, győződjön meg arról, hogy akkor is **másolja ki és mentse az alkalmazás *Alkalmazásazonosító* biztonságos helyen túl**. Ezt az Azonosítót kell későbbi használatra.

   !["Alkalmazásazonosító" mentése a későbbi használatra](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Most adja meg egy válasz URL-CÍMEN az Azure AD-alkalmazáshoz. Az alkalmazás **beállítások** menüben válasszon **válasz URL-címek**. Adja meg az URL-cím, majd kattintson a **mentése**.

   | Beállítás | Ajánlott érték | 
   | ------- | --------------- | 
   | **Válasz URL-címek** | Az Azure Resource Manager egyéni összekötő adja meg az URL-cím:`https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > Ha a **beállítások** menü korábban nem szerepelhet, válassza a **beállítások** itt:
   >
   > ![Válassza ki a "Beállítások"](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. Vissza a **beállítások** menüben válasszon **szükséges engedélyek** > **Hozzáadás**.

   ![Szükséges engedélyek > Hozzáadás](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. Ha a **hozzáadása API-hozzáférés** menü megnyitása, válassza a **API kiválasztása** > 
**Windows Azure szolgáltatásfelügyeleti API** > **kiválasztása **.

   ![API kiválasztása](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. Az a **hozzáadása API-hozzáférés** menüben válasszon **engedélyként válassza**. A **delegált engedéllyel kell**, válassza a **Access Azure szolgáltatásfelügyelet szervezet felhasználóként** > **kiválasztása**.

   ![Válassza ki a "Delegált engedélyek" > "hozzáférés az Azure szolgáltatások felügyeleti szervezet felhasználóként"](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   Ellenkező esetben az egyéb lehetőségek:

   | Beállítás | Ajánlott érték | Leírás | 
   | ------ | --------------- | ----------- | 
   | **Delegált engedélyek** | | Válassza ki a Web API delegált elérésére vonatkozó engedélyek | 
   |||| 

9. Most már a a **hozzáadása API-hozzáférés** menüben válasszon **végzett**.

   ![A menü "API-hozzáférés hozzáadása" > "Kész"](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Most létre egy *ügyfélkulcsot*, vagy "titkos", az összekötő az Azure AD alkalmazás. 

    1. Vissza a **beállítások** menüben válassza a **kulcsok**. 
    Adjon meg egy nevet a kulcsot a legfeljebb 16 karakter, válassza ki egy olyan lejárati időt, majd válassza **mentése**.

       ![Hozzon létre egy ügyfélkulcsot](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. Amikor megjelenik a létrehozott kulcs, **győződjön meg arról, hogy másolja, és ezt a kulcsot biztonságos helyen mentse** mielőtt kilép a **kulcsok** lap.
    
       ![Manuálisan másolja ki és mentse a kulcsot](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. A kulcs a mentés után nyugodtan bezárhatja a **beállítások** menü.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Az Azure AD-alapú hitelesítés hozzáadása a webes API-alkalmazás

Most a Web API-t az első Azure AD-alkalmazás hitelesítés bekapcsolása. További információt a további [konfigurálása az App Service alkalmazás használhatja az Azure Active Directory bejelentkezési](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. Az a [Azure-portálon](https://portal.azure.com), a webes API-alkalmazást a korábban közzétett található [hozzon létre egyéni összekötők webes API-](../logic-apps/custom-connector-build-web-api-app-tutorial.md).

2. A **beállítások**, válassza a **hitelesítési / engedélyezési**. 

   1. A **App Service hitelesítés**, válassza a **a**.
   2. A **hitelesítetlen kérés esetén elvégzendő művelet**, jelölje be **bejelentkezés Azure Active Directoryval**.
   3. A **hitelesítésszolgáltatókat**, jelölje be **Azure Active Directory**. 

   ![Válassza a "hitelesítési / engedélyezési"](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. Az a **Azure Active Directory beállításai** lap:

   1. A **üzemmód**, válassza a **Express**.

   2. A hitelesítéshez használja a webes API-alkalmazás az Azure AD alkalmazás most kiválasztása. 
   Válasszon **válassza ki a meglévő AD-alkalmazás** > **Azure AD-alkalmazás**.

   3. A **Azure AD-alkalmazások**, jelölje ki az Azure AD-alkalmazást, a webes API-alkalmazást a korábban létrehozott. 
   
   4. Válasszon **OK** , amíg vissza nem a **hitelesítési / engedélyezési** lap.

   Példa:

   ![Válassza ki a Azure AD-alkalmazást, amely jelöli az webes API-alkalmazás](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. Az a **hitelesítési / engedélyezési** lapon, válassza ki **mentése**.

   ![Hitelesítési beállításainak mentése](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   A webes API-alkalmazás most már használhatja az Azure AD hitelesítésre.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. A Web API OpenAPI fájlban az Azure AD-alapú hitelesítés beállítása

Nyissa meg a OpenAPI fájlt a webes API-alkalmazás, így adhat hozzá a `securityDefintions` objektum és az Azure AD-alapú hitelesítés alatt a `host` tulajdonság. Íme egy példa bemutatja a `host` tulajdonság és `securityDefinitions` objektum:

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
Most már készen áll létrehozni és regisztrálni az egyéni összekötő.

## <a name="next-steps"></a>Következő lépések

* [Összekötő regisztrálása](../logic-apps/logic-apps-custom-connector-register.md)
* [Egyéni összekötő – gyakori kérdések](../logic-apps/custom-connector-faq.md)
