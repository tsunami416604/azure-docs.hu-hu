---
title: "Az Azure AD - Azure Logic Apps egyéni összekötők biztonságos |} Microsoft Docs"
description: "Biztonság és hitelesítés hozzáadása az API-hoz és az összekötőhöz az Azure Active Directory (Azure AD) segítségével"
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
ms.openlocfilehash: 5fd59677fc6abf80666caf90aa3da7e553648bca
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Az API és az Azure Active Directoryval (Azure AD-) összekötő biztonsági hozzáadása

Az API és az egyéni összekötő közötti hívások biztonságossá tételéhez adjon Azure AD-hitelesítést a webes API-hoz és az összekötőhöz. Ebben a forgatókönyvben két Azure Active Directory-alkalmazást (Azure AD-alkalmazást) hozunk létre – az egyik Azure AD-alkalmazás biztosítja a webes API-t, míg a másik Azure AD-alkalmazás az összekötő regisztrációját biztosítja, valamint delegált hozzáférést ad hozzá. 

Ez az oktatóanyag az Azure Resource Manager API-t használja példaként. Az Azure Resource Manager segít az Azure-ban létrehozott megoldások (például adatbázisok, virtuális gépek és webalkalmazások) összetevőinek kezelésében. Az egyéni Azure Resource Manager-összekötő hasznos lehet az Azure-erőforrások munkafolyamatból történő kezelése során. További információk: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, kezdhet egy [ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben regisztráljon egy [használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Ebben az oktatóanyagban a [minta OpenAPI-fájl az Azure Resource Managerhez](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > A minta OpenAPI-fájl nem határozza meg az összes Azure Resource Manager-műveletet, és jelenleg csak az [Összes előfizetés listázása](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List) műveletet tartalmazza. Szerkesztheti ezt az OpenAPI-t vagy létrehozhat egy másik OpenAPI-fájlt az [online OpenAPI-szerkesztő](http://editor.swagger.io/) használatával.
  >
  > Ezt az oktatóanyagot alkalmazhatja bármely RESTful API esetében, amelyhez Azure AD-hitelesítés szeretne használni.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Az első Azure AD-alkalmazás létrehozása a webes API biztosításához

Az első Azure AD-alkalmazás akkor végzi el a hitelesítést, amikor az egyéni összekötő meghívja az API-t, amely ebben a példában az Azure Resource Manager API.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha több Azure Active Directory-bérlővel rendelkezik, ellenőrizze, hogy a megfelelő címtárba jelentkezett be. Ehhez tekintse meg a felhasználónevénél megjelenő címtárat. 

   ![Címtár megerősítése](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Címtárak módosításához válassza ki a felhasználónevét, majd válassza a kívánt címtárat.

2. Az Azure főmenüjéből válassza az **Azure Active Directory** lehetőséget, hogy megtekinthesse az aktuális címtárat.

   ![Válassza az „Azure Active Directory” elemet](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Ha nem jeleníti meg az Azure főmenü **Azure Active Directory**, válassza a **minden szolgáltatás**. A **Szűrő** mezőbe írja be szűrőként az „Azure Active Directory” kifejezést, majd válassza az **Azure Active Directory** elemet.

3. A címtár menü **Kezelés** szakaszában válassza az **Alkalmazásregisztrációk** lehetőséget. A regisztrál alkalmazások listájából válassza az **+ Új alkalmazásregisztráció** elemet.

   ![Válassza az „Alkalmazásregisztrációk”, „+ Új alkalmazásregisztráció” lehetőséget](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. A **Létrehozás** szakaszban adja meg az Azure AD alkalmazás részleteit a táblában leírtak szerint, majd válassza a **Létrehozás** elemet. 

   ![Azure AD alkalmazás létrehozása](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Name (Név)** | *webes-api-alkalmazás-neve* | A webes API Azure AD-alkalmazásának neve | 
   | **Alkalmazás típusa** | **Webalkalmazás / Webes API** | Az alkalmazás típusa | 
   | **Bejelentkezési URL** | `https://login.windows.net` | | 
   |||| 

5. Amikor visszatér a címtár **Alkalmazásregisztrációk** listájához, válassza ki az Azure AD-alkalmazást.

   ![Az Azure AD-alkalmazás kiválasztása a listából](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. Amikor megjelenik az alkalmazás részletei lap, győződjön meg arról, hogy **biztonságos helyre másolja és menti az *alkalmazás* Alkalmazás-azonosítóját**. Erre az azonosítóra szüksége lesz a későbbi használat során.

   ![Az „Alkalmazás azonosítójának” mentése későbbi használat céljából](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Most adjon meg egy válasz URL-címet az Azure AD-alkalmazáshoz. Az alkalmazás **Beállítások** menüjében válassza a **Válasz URL-címek** elemet. Írja be ezt az URL-címet, majd válassza a **Mentés** lehetőséget.

   ![Válasz URL-címek](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Válasz URL-címek** | Saját API-hoz írja be a következő URL-t: </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Delegált engedélyek** | {nem szükséges} | | 
   | **Ügyfélkulcs** | {nem szükséges} | | 
   |||| 

   > [!TIP]
   > Ha a **Beállítások** menü előzőleg nem jelent meg, válassza az alábbi **Beállítások** elemet:
   >
   > ![A „Beállítások” elem kiválasztása](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. Második Azure AD-alkalmazás létrehozása az egyéni összekötőhöz

A második Azure AD-alkalmazás biztosítja az egyéni összekötő regisztrációját, valamint delegált hozzáférést ad hozzá az első Azure AD-alkalmazás által védett webes API-hoz. 

> [!IMPORTANT]
> Győződjön meg arról, hogy mind a két Azure AD-alkalmazás ugyanabban a címtárban található.

1. Lépjen vissza az **Alkalmazásregisztrációk** listájához, majd válassza ismét az **+ Új alkalmazásregisztráció** elemet.

   ![Válassza az „Alkalmazásregisztrációk”, „+ Új alkalmazásregisztráció” lehetőséget](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. A **Létrehozás** szakaszban adja meg a második Azure AD alkalmazás részleteit a táblában leírtak szerint, majd válassza a **Létrehozás** elemet. 

   ![Azure AD alkalmazás létrehozása](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Name (Név)** | *saját-összekötő-neve* | Az összekötő Azure AD-alkalmazásának neve | 
   | **Alkalmazás típusa** | **Webalkalmazás / Webes API** | Az alkalmazás típusa | 
   | **Bejelentkezési URL** | `https://login.windows.net` | | 
   |||| 

3. Amikor visszatér a címtár **Alkalmazásregisztrációk** listájához, válassza ki a második Azure AD-alkalmazást.

   ![A második Azure AD-alkalmazás kiválasztása a listából](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. Amikor megjelenik az alkalmazás részletei lap, győződjön meg arról, hogy **biztonságos helyre másolja és menti ennek az *alkalmazásnak* az Alkalmazás-azonosítóját** is. Erre az azonosítóra szüksége lesz a későbbi használat során.

   ![Az „Alkalmazás azonosítójának” mentése későbbi használat céljából](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Most adjon meg egy válasz URL-címet az Azure AD-alkalmazáshoz. Az alkalmazás **Beállítások** menüjében válassza a **Válasz URL-címek** elemet. Írja be ezt az URL-címet, majd válassza a **Mentés** lehetőséget.

   | Beállítás | Ajánlott érték | 
   | ------- | --------------- | 
   | **Válasz URL-címek** | Az Azure Resource Manager egyéni összekötőhöz írja be a következő URL-címet: `https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > Ha a **Beállítások** menü előzőleg nem jelent meg, válassza az alábbi **Beállítások** elemet:
   >
   > ![A „Beállítások” elem kiválasztása](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. A **Beállítások** menüben válassza a **Szükséges engedélyek** > **Hozzáadás** lehetőséget.

   ![Szükséges engedélyek > Hozzáadás](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. Amikor megnyílik az **API-hozzáférés hozzáadása** menü, válassza az **API kiválasztása** > 
**Windows Azure Szolgáltatásfelügyeleti API** > **Kiválasztás** elemet.

   ![API kiválasztása](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. Az **API-hozzáférés hozzáadása** menüben válassza az **Engedélyek kiválasztása** lehetőséget. A **Delegált engedélyek** szakaszban válassza a **Hozzáférés az Azure szolgáltatásfelügyelethez vállalati felhasználóként** > **Kiválasztás** lehetőséget.

   ![A „Delegált engedélyek” > „Hozzáférés az Azure szolgáltatásfelügyelethez vállalati felhasználóként” lehetőség kiválasztása](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   Egyéb esetben a további lehetőségek:

   | Beállítás | Ajánlott érték | Leírás | 
   | ------ | --------------- | ----------- | 
   | **Delegált engedélyek** | | Webes API delegált hozzáférés-engedélyeinek kiválasztása | 
   |||| 

9. Az **API-hozzáférés hozzáadása** menüben válassza a **Kész** lehetőséget.

   ![„API-hozzáférés hozzáadása” menü > „Kész”](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Most hozzon létre egy *ügyfélkulcsot*, vagy „titkos kódot”, az összekötő Azure AD-alkalmazásához. 

    1. A **Beállítások** menüben válassza a **Kulcsok** lehetőséget. 
    Adja meg a kulcs legfeljebb 16 karakter hosszúságú nevét, válassza ki a lejárati időt, majd kattintson a **Mentés** elemre.

       ![Ügyfélkulcs létrehozása](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. Amikor megjelenik a létrehozott kulcs, **győződjön meg arról, hogy biztonságos helyre másolta és mentette a kulcsot**, mielőtt elhagyja a **Kulcsok** lapot.
    
       ![Kulcs manuális másolása és mentése](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. A kulcs mentése utána nyugodtan bezárhatja a **Beállítások** menüt.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Azure AD-hitelesítés hozzáadása a webes API-alkalmazáshoz

Most kapcsolja be a hitelesítést a webes API-hoz az első Azure AD-alkalmazás segítségével. További információt a további [konfigurálása az App Service alkalmazás használhatja az Azure Active Directory bejelentkezési](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. Az a [Azure-portálon](https://portal.azure.com), a webes API-alkalmazást a korábban közzétett található [hozzon létre egyéni összekötők webes API-](../logic-apps/custom-connector-build-web-api-app-tutorial.md).

2. A **Beállítások** területen válassza a **Hitelesítés/Engedélyezés** elemet. 

   1. Az **App Service-hitelesítés** szakaszban válassza a **Be** lehetőséget.
   2. Az **Elvégzendő művelet, ha a kérés nincs hitelesítve** területen válassza a **Bejelentkezés az Azure Active Directoryval** lehetőséget.
   3. A **Hitelesítési szolgáltatók** területen válassza az **Azure Active Directory** lehetőséget. 

   ![A „Hitelesítés/Engedélyezés” lehetőség kiválasztása](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. Az **Azure Active Directory beállítások** lapján:

   1. A **Felügyelet mód** területen válassza az **Expressz** lehetőséget.

   2. Válassza ki azt az Azure AD-alkalmazást, amelyet a webes API-alkalmazás használ a hitelesítéshez. 
   Válassza a **Meglévő AD-alkalmazás kiválasztása** > **Azure AD-alkalmazás** lehetőséget.

   3. Az **Azure AD-alkalmazások** területen válassza ki a webes API-alkalmazáshoz korábban létrehozott Azure AD-alkalmazást. 
   
   4. Kattintson az **OK** gombra, amíg vissza nem jut a **Hitelesítés/Engedélyezés** oldalra.

   Példa:

   ![A webes API-alkalmazást képviselő Azure AD-alkalmazás kiválasztása](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. A **Hitelesítés/Engedélyezés** lapon kattintson a **Mentés** gombra.

   ![Hitelesítési beállítások mentése](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   A webes API-alkalmazás mostantól használhatja az Azure AD-t a hitelesítéshez.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Azure AD-hitelesítés beállítása a webes API OpenAPI-fájljában

Nyissa meg a Webes API-alkalmazás OpenAPI-fájlját, így hozzáadhatja a `securityDefintions` objektumot és az Azure AD-hitelesítést a `host` tulajdonságnál. Az alábbi példa a `host` tulajdonságot és az `securityDefinitions` objektumot mutatja:

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
Most már készen áll az egyéni összekötő létrehozására és regisztrálására.

## <a name="next-steps"></a>További lépések

* [Összekötő regisztrálása](../logic-apps/logic-apps-custom-connector-register.md)
* [Egyéni összekötő – gyakori kérdések](../logic-apps/custom-connector-faq.md)
