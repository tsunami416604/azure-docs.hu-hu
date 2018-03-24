---
title: Hozzon létre Azure-alkalmazás identitását portálon |} Microsoft Docs
description: Hozzon létre egy új Azure Active Directory-alkalmazás és szolgáltatás egyszerű erőforrásokhoz való hozzáférés kezelésére használható a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: 264befc6c60b87d41658b4da763e477fbb7e3f8c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Hozzon létre egy Azure Active Directory-alkalmazást és egy egyszerű szolgáltatást, amely erőforrások eléréséhez a portál használatával

Ha rendelkezik, amelyet a eléréséhez vagy módosításához erőforrások kód, be kell állítania egy Azure Active Directory (AD) alkalmazást. A szükséges engedélyek hozzárendelése a AD-alkalmazást. Ez a megközelítés célszerű saját credentials az alkalmazást futtató, mert engedélyek hozzárendelése saját engedélyek eltérő alkalmazás identitását. Ezek az engedélyek általában korlátozódik, hogy mit az alkalmazás kell tennie.

Ez a cikk bemutatja, hogyan hajtsa végre ezeket a lépéseket a portálon keresztül. A single-bérlői alkalmazások, ahol az alkalmazás futtatásához csak egy szervezeten belül olyan összpontosít. Általában egy bérlői alkalmazásokat használ futó üzleti alkalmazásokhoz a szervezeten belül.

> [!IMPORTANT]
> Egy egyszerű szolgáltatás létrehozása, helyett érdemes az Azure AD felügyelete a Szolgáltatásidentitást az az alkalmazásidentitás. Az Azure AD MSI az Azure Active Directoryban, amely egyszerűbbé teszi a kód identitás létrehozása a nyilvános előzetes verziójú funkciók. Ha a kódot, amely támogatja az Azure AD MSI és fér hozzá az erőforrásokat, amelyek támogatják az Azure Active Directory hitelesítési szolgáltatás fut, Azure AD MSI beállítás jobban meg. További információt az Azure AD MSI, beleértve a szolgáltatások jelenleg támogatják a forgatókönyvet, tekintse meg a [Szolgáltatásidentitás felügyelt Azure-erőforrások](../active-directory/managed-service-identity/overview.md).

## <a name="required-permissions"></a>Szükséges engedélyek

Ez a cikk befejezéséhez megfelelő engedélyekkel rendelkezik alkalmazás regisztrálása az Azure AD-bérlőn, és az alkalmazást egy szerepkörhöz rendelhető az Azure-előfizetésben. Ellenőrizze, hogy a fenti lépések végrehajtásához a megfelelő engedélyekkel.

### <a name="check-azure-active-directory-permissions"></a>Azure Active Directory-engedélyek ellenőrzése

1. Válassza ki **az Azure Active Directory**.

   ![select azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Válassza ki az Azure Active Directoryban, **felhasználói beállítások**.

   ![Válassza ki a felhasználói beállítások](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Ellenőrizze a **App regisztrációk** beállítást. Ha beállítása **Igen**, nem rendszergazdai felhasználók regisztrálhatják AD alkalmazásaiban. Ez a beállítás azt jelenti, hogy egyetlen felhasználóhoz sem az Azure AD-bérlő regisztrálhatja az alkalmazást. Lépne [Azure ellenőrizze előfizetése engedélyei között](#check-azure-subscription-permissions).

   ![alkalmazás-regisztrációk megtekintése](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Ha az alkalmazás regisztrációk beállítás értéke **nem**, csak a rendszergazda felhasználók regisztrálhatják az alkalmazásokat. Ellenőrizze, hogy a fiók egy rendszergazda az Azure AD-bérlő. Válassza ki **áttekintése** , és tekintse meg a felhasználói adatokat. Ha a fiók hozzá van rendelve a felhasználói szerepkör, de a regisztrációs alkalmazásbeállításhoz (az előző lépésben) korlátozott rendszergazdai jogosultságú felhasználókhoz, kérje meg a rendszergazdát, vagy hogy rendeljen Önhöz egy rendszergazdai szerepkört, vagy lehetővé teszi a felhasználók alkalmazásokat regisztrálni.

   ![felhasználó keresése](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Azure-előfizetés engedélyek ellenőrzése

Az Azure-előfizetéséhez, a fiók rendelkezik `Microsoft.Authorization/*/Write` egy AD-alkalmazás hozzárendelése szerepkörhöz a hozzáférést. Ez a művelet biztosítja a [tulajdonos](../active-directory/role-based-access-built-in-roles.md#owner) szerepkör vagy [felhasználói hozzáférés adminisztrátora](../active-directory/role-based-access-built-in-roles.md#user-access-administrator) szerepkör. Ha a fiók hozzá van rendelve a **közreműködő** szerepkör, Önnek nincs megfelelő engedélye. Arra vonatkozó hibaüzenetet kap, a szolgáltatás egyszerű hozzárendelése egy szerepkörhöz megkísérlése során.

Ellenőrizze előfizetése engedélyei között:

1. Válassza ki a fiókot jobb felső sarokban, és válassza ki **saját engedélyek**.

   ![Válassza ki a felhasználói engedélyek](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. A legördülő listából válassza ki az előfizetést. Válassza ki **itt részletek megtekintéséhez kattintson ide teljes hozzáférést ehhez az előfizetéshez**.

   ![felhasználó keresése](./media/resource-group-create-service-principal-portal/view-details.png)

1. A hozzárendelt szerepkörök megtekintése, és határozza meg, hogy van-e megfelelő engedélyekkel az AD-alkalmazás hozzárendelése szerepkörhöz. Ha nem, kérje meg a előfizetési rendszergazda, akkor a felhasználói hozzáférés adminisztrátora szerepkörbe való felvételre. Az alábbi ábrán a felhasználó rendelve a tulajdonosi szerepkört, ami azt jelenti, hogy a felhasználó a megfelelő engedélyekkel rendelkezik.

   ![engedélyek megjelenítése](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Egy Azure Active Directory-alkalmazás létrehozása

1. Az Azure-fiók használatával jelentkezzen be a [Azure-portálon](https://portal.azure.com).
1. Válassza ki **az Azure Active Directory**.

   ![select azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Válassza ki **App regisztrációk**.

   ![Válassza ki az alkalmazás-regisztráció](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Válassza ki **új alkalmazás regisztrációja**.

   ![alkalmazás hozzáadása](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Adjon meg egy nevet és egy URL-címet az alkalmazáshoz. Válassza ki **Web app / API** a létrehozandó alkalmazás típusától. Nem hozható létre a hitelesítő adatokat egy [natív alkalmazás](../active-directory/active-directory-application-proxy-native-client.md), ezért, hogy a típus nem működik egy automatizált alkalmazás. Miután beállította az értékeket, válassza ki a **létrehozása**.

   ![alkalmazás neve](./media/resource-group-create-service-principal-portal/create-app.png)

Az alkalmazás hozott létre.

## <a name="get-application-id-and-authentication-key"></a>Alkalmazás azonosítója és a hitelesítési kulcs beszerzése

Bejelentkezéskor programozott módon, a azonosító szükséges az alkalmazás és egy hitelesítési kulcs. Ahhoz, hogy ezeket az értékeket, tegye a következőket:

1. A **App regisztrációk** az Azure Active Directoryban, válassza ki az alkalmazást.

   ![alkalmazás kiválasztása](./media/resource-group-create-service-principal-portal/select-app.png)

1. Másolás a **Alkalmazásazonosító** és az alkalmazás kódjában tárolja. Néhány [mintaalkalmazást](#log-in-as-the-application) tekintse meg ezt az értéket az ügyfél-azonosítót.

   ![Ügyfél-azonosító](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. A hitelesítési kulcs létrehozásához válasszon **beállítások**.

   ![Válassza ki a beállítások](./media/resource-group-create-service-principal-portal/select-settings.png)

1. A hitelesítési kulcs létrehozásához válasszon **kulcsok**.

   ![Válassza ki a kulcsok](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Adja meg a kulcsot, és egy időtartamot a kulcs leírását. Ha elkészült, válassza ki a **mentése**.

   ![kulcs mentése](./media/resource-group-create-service-principal-portal/save-key.png)

   A kulcs mentése után a kulcsnak az értéke megjelenik. Másolja ezt az értéket, mert nem sikerült beolvasni a a kulcs később. A kulcs értéke az alkalmazás azonosítójával jelentkezzen be az alkalmazás számára adja meg. Tárolja a kulcs értékét, ahol az alkalmazás kérheti le.

   ![kulcs mentése](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>-Bérlőazonosító beszerzése

Bejelentkezéskor programozott módon, kell átadni a hitelesítési kéréshez a bérlő azonosítója.

1. Válassza ki **az Azure Active Directory**.

   ![select azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Válassza ki ahhoz, hogy a bérlő azonosítója, **tulajdonságok** az Azure AD-bérlő.

   ![Válassza ki az Azure AD-tulajdonságok](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Másolás a **könyvtár-azonosítója**. Ez az érték az a bérlő azonosítója.

   ![Bérlő azonosítója](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Szerepkör alkalmazást

Az előfizetés az erőforrások eléréséhez az alkalmazást egy szerepkörhöz kell rendelni. Döntse el, melyik szerepkört jelöli a megfelelő engedélyekkel az alkalmazáshoz. A rendelkezésre álló szerepkörökkel kapcsolatos további tudnivalókért lásd: [RBAC: beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md).

A hatókör szintjén található az előfizetés, erőforráscsoportból vagy erőforrás állíthatja be. Engedélyek hatókör alacsonyabb szintre származnak. Például egy alkalmazást az olvasó szerepkört erőforráscsoport hozzáadása azt jelenti, hogy ez az erőforráscsoport és a benne található erőforrásokat tudja olvasni.

1. Nyissa meg a szint szeretne hozzárendelni az alkalmazást. Például egy szerepkört az előfizetés hatókörből rendeléséhez jelölje **előfizetések**. Ehelyett kiválaszthatja egy erőforráscsoport vagy az erőforrás.

   ![Válassza ki az előfizetést](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Válassza az adott előfizetés (erőforráscsoport vagy erőforrás) az alkalmazást.

   ![Válassza ki az előfizetést a hozzárendeléshez](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Válassza ki **hozzáférés-vezérlés (IAM)**.

   ![Jelölje be a hozzáférés](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Válassza a **Hozzáadás** lehetőséget.

   ![Válassza ki hozzáadása](./media/resource-group-create-service-principal-portal/select-add.png)

1. Jelölje ki az alkalmazáshoz hozzárendelni kívánt szerepkört. Az alábbi képen látható a **olvasó** szerepkör.

   ![szerepkör kiválasztása](./media/resource-group-create-service-principal-portal/select-role.png)

1. Alapértelmezés szerint Azure Active Directory-alkalmazások nem jelennek meg a rendelkezésre álló beállításokat. Az alkalmazás megkereséséhez nevét, a keresési mezőbe kell megadnia. Válassza ki azt.

   ![alkalmazások keresése](./media/resource-group-create-service-principal-portal/search-app.png)

1. Válassza ki **mentése** a szerepkör hozzárendelése befejezéséhez. Megjelenik a listában, hogy a hatókör adott szerepkörhöz rendelt felhasználók az alkalmazást.

## <a name="next-steps"></a>További lépések
* Egy több-bérlős alkalmazás beállításához tekintse meg a [fejlesztői útmutató az Azure Resource Manager API-val engedélyezési](resource-manager-api-authentication.md).
* Biztonsági házirendek megadásával kapcsolatos további tudnivalókért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md).  
* Elérhető műveleteket, lehet megadott vagy megtagadta a felhasználók listáját lásd: [Azure Resource Manager erőforrás-szolgáltató műveletek](../active-directory/role-based-access-control-resource-provider-operations.md).
