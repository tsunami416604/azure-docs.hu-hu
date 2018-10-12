---
title: Azure-alkalmazáshoz identitás létrehozása a portálon |} A Microsoft Docs
description: Ismerteti, hogyan hozzon létre egy új Azure Active Directory-alkalmazás és egyszerű szolgáltatás, amely a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager-erőforrásokhoz való hozzáférés kezelésére használható.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2018
ms.author: tomfitz
ms.openlocfilehash: 5233cde48d52e34960e87d3362b8cfaf3a0722c0
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113733"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Az Azure Active Directory-alkalmazás és -erőforrások elérésére képes egyszerű szolgáltatás létrehozása a portál használatával

Ha rendelkezik, amelyet eléréséhez, vagy módosítsa erőforrások, az alkalmazáshoz tartozó identitást hozhat létre. Ezt az identitást szolgáltatásnévnek nevezzük. A szolgáltatásnévnek majd hozzárendelheti a szükséges engedélyekkel. Ez a cikk bemutatja, hogyan az egyszerű szolgáltatás létrehozása a portál használatával. Egy egybérlős alkalmazást, ahol az alkalmazás futtatásához csak egy szervezeten belül célja összpontosít. Általában használnak a egybérlős alkalmazások az üzleti alkalmazások futtatására a szervezeten belül.

> [!IMPORTANT]
> Egyszerű szolgáltatás létrehozása helyett fontolja meg a felügyelt identitások használatával az Azure-erőforrások esetében az alkalmazás azonosítóját. A kód egy szolgáltatás, amely támogatja a felügyelt identitások és hozzáférések erőforrások, amelyek támogatják az Azure Active Directory-hitelesítés fut, ha felügyelt identitások, jobb megoldás. További információ az Azure-erőforrásokhoz, mely szolgáltatások jelenleg támogatja, beleértve a felügyelt identitásokból [Mi az Azure-erőforrások felügyelt identitások?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Az Azure Active Directory-alkalmazás létrehozása

Nyissa meg közvetlenül az identitás létrehozása. Ha problémát tapasztal, ellenőrizze a [szükséges engedélyek](#required-permissions) , hogy a fiók képes létrehozni az identitást.

1. Jelentkezzen be az Azure-fiók révén a [az Azure portal](https://portal.azure.com).
1. Válassza az **Azure Active Directory** elemet.

   ![az azure active directory kiválasztása](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Válassza az **Alkalmazásregisztrációk** elemet.

   ![alkalmazásregisztrációk kiválasztása](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Válassza ki **+ új alkalmazásregisztráció**.

   ![Alkalmazás felvétele](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Adja meg az alkalmazás nevét és URL-címét. Válassza a **Webalkalmazás/API** lehetőséget a létrehozni kívánt alkalmazás típusaként. Hitelesítő adatok nem hozható létre egy [natív alkalmazás](../active-directory/manage-apps/application-proxy-configure-native-client-application.md). Egy automatikus alkalmazásnak típus nem használható. Miután beállította az értékeket, válassza ki a **létrehozás**.

   ![alkalmazás elnevezése](./media/resource-group-create-service-principal-portal/create-app.png)

Az Azure Active Directory-alkalmazás és egyszerű szolgáltatás létrehozott.

## <a name="assign-application-to-role"></a>Alkalmazás hozzárendelése szerepkörhöz

Az előfizetésben lévő erőforrások eléréséhez, hozzá kell rendelnie az alkalmazás egy szerepkörhöz. Döntse el, milyen szerepkört kínál az alkalmazás a megfelelő engedélyekkel. Az elérhető szerepkörök kapcsolatos további információkért lásd: [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md).

Beállíthatja a hatókör szintjén is az előfizetés, erőforráscsoport vagy erőforrás. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Például egy alkalmazás az Olvasó szerepkörhöz, egy erőforráscsoport hozzáadása azt jelenti, hogy azt az erőforráscsoportot és az összes benne található erőforrást olvashatja.

1. Keresse meg a hatókör az alkalmazást hozzárendelni kívánt szintjét. Válassza ki például az előfizetések szintjén szerepkör hozzárendelése **minden szolgáltatás** és **előfizetések**.

   ![Előfizetés kiválasztása](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Válassza ki az adott előfizetés hozzárendelése az alkalmazást.

   ![Válasszon hozzárendelés előfizetést](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetéshez, válasszon **globális előfizetés-szűrő**. Ellenőrizze, hogy az előfizetés ki van jelölve a portálon. 

1. Válassza ki **hozzáférés-vezérlés (IAM)**.

   ![Jelölje be a hozzáférés](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Válassza a **+ Hozzáadás** lehetőséget.

   ![Válassza a hozzáadása](./media/resource-group-create-service-principal-portal/select-add.png)

1. Válassza ki a az alkalmazáshoz hozzárendelni kívánt szerepkört. Lehetővé teszi az alkalmazás hajtsa végre a műveleteket, például **újraindítás**, **start** és **leállítása** példányok, válassza a **közreműködői** szerepkör. Alapértelmezés szerint az Azure Active Directory-alkalmazások nem megjelenik az elérhető lehetőségek közül. Keresse meg az alkalmazás, keresse meg a nevét, és jelölje ki.

   ![Szerepkör kiválasztása](./media/resource-group-create-service-principal-portal/select-role.png)

1. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. Láthatja, hogy az alkalmazás a felhasználók az adott hatókörnél szerepköre a listában.

Az egyszerű szolgáltatás be van állítva. Akkor megkezdheti a parancsfájlok vagy alkalmazások futtatásához. Ez a szakasz bemutatja, hogyan használatával lekérjük az értékeket, amelyek szükségesek, amikor programozott módon jelentkezik be.

## <a name="get-values-for-signing-in"></a>Bejelentkezés értékek beolvasása

### <a name="get-tenant-id"></a>A bérlőazonosító beszerzése

Ha programozott módon jelentkezik be, kell átadni a bérlő Azonosítóját, a hitelesítési kérelemmel együtt.

1. Válassza az **Azure Active Directory** elemet.

   ![az azure active directory kiválasztása](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Válassza ki **tulajdonságok**.

   ![az Azure AD tulajdonságok kiválasztása](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Másolás a **címtár-azonosító** lekérni a bérlő azonosítója.

   ![Bérlőazonosító](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

### <a name="get-application-id-and-authentication-key"></a>Alkalmazásazonosító és hitelesítési kulcs beszerzése

Az alkalmazás és a hitelesítési kulcs Azonosítóját is szükséges. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. Válassza ki az alkalmazást az Azure Active Directory **Alkalmazásregisztrációk** területén.

   ![Alkalmazás kijelölése](./media/resource-group-create-service-principal-portal/select-app.png)

1. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában.

   ![Ügyfél-azonosító](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Válassza ki **beállítások**.

   ![beállítások kiválasztása](./media/resource-group-create-service-principal-portal/select-settings.png)

1. Válassza a **Kulcsok** elemet.

   ![kulcsok kiválasztása](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Adjon meg egy leírást és egy időtartamot a kulcshoz. Ha elkészült, kattintson a **Mentés** elemre.

   ![kulcs mentése](./media/resource-group-create-service-principal-portal/save-key.png)

   A kulcs mentése után megjelenik a kulcs értéke. Másolja ezt az értéket, mert nem sikerült beolvasni a kulcsot később. A kulcs értékét az alkalmazás azonosítójával jelentkezzen be az alkalmazást, hogy adja meg. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![mentett kulcs](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="required-permissions"></a>Szükséges engedélyek

Rendelkezik megfelelő engedélyekkel alkalmazások regisztrációjához az Azure AD-bérlőhöz, és alkalmazások szerepkörhöz rendeléséhez az Azure-előfizetésében.

### <a name="check-azure-active-directory-permissions"></a>Azure Active Directory-engedélyek ellenőrzése

1. Válassza az **Azure Active Directory** elemet.

   ![Válassza az Azure Active Directory elemet.](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Figyelje meg, hogy a szerepkör. Ha rendelkezik a **felhasználói** szerepkör, győződjön meg arról, hogy a nem rendszergazda felhasználók regisztrálhatnak alkalmazásokat.

   ![felhasználó keresése](./media/resource-group-create-service-principal-portal/view-user-info.png)

1. Válassza ki **felhasználói beállítások**.

   ![Válassza ki a felhasználói beállítások](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Ellenőrizze a **alkalmazásregisztrációk** beállítás. Ez az érték csak egy rendszergazda állítható. Ha beállítása **Igen**, az Azure AD-bérlőben a felhasználói regisztrálhatnak egy alkalmazást.

   ![alkalmazásregisztrációk megtekintése](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

Ha az alkalmazásregisztrációk beállítás értéke **nem**, csak [globális rendszergazdák](../active-directory/users-groups-roles/directory-assign-admin-roles.md) alkalmazások akkor regisztrálhatnak. Ha a fiók hozzá van rendelve a felhasználói szerepkörhöz, de az alkalmazás regisztrációs beállítás korlátozott rendszergazdai jogosultságú felhasználókhoz, kérje meg a rendszergazdát, hogy bármelyik, a globális rendszergazdai szerepkörrel, vagy rendelje hozzá engedélyezése a felhasználók számára alkalmazások regisztrálása.

### <a name="check-azure-subscription-permissions"></a>Azure-előfizetés engedélyek ellenőrzése

Az Azure-előfizetésében, a fióknak rendelkeznie kell `Microsoft.Authorization/*/Write` AD-alkalmazás hozzárendelése szerepkörhöz való hozzáférést. Ezt a műveletet a [Tulajdonos](../role-based-access-control/built-in-roles.md#owner) szerepkör vagy a [Felhasználói hozzáférés rendszergazdája](../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör végezheti el. Ha a fiók hozzá van rendelve a **közreműködői** szerepkör nem rendelkezik megfelelő engedéllyel. Az egyszerű szolgáltatás hozzárendelése szerepkörhöz megkísérlésekor hibaüzenetet kap.

Ellenőrizze előfizetése engedélyei között:

1. Válassza ki a fiók jobb felső sarokban, majd válassza ki **saját engedélyek**.

   ![Válassza ki a felhasználói engedélyek](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. A legördülő listából válassza ki a kívánt előfizetést, az egyszerű szolgáltatás létrehozásához. Ezután válassza ki **teljes hozzáférési megtekintéséhez kattintson ide az előfizetés részletei**.

   ![felhasználó keresése](./media/resource-group-create-service-principal-portal/view-details.png)

1. A hozzárendelt szerepkörök megtekintése, és határozza meg, hogy rendelkezik-e megfelelő engedélyekkel AD-alkalmazás hozzárendelése szerepkörhöz. Ha nem, kérje meg az előfizetés adminisztrátorát, hogy vegye fel Önt a felhasználói hozzáférés rendszergazdájának szerepköre. Az alábbi ábrán a felhasználó a tulajdonos szerepkör, ami azt jelenti, hogy a felhasználó rendelkezik megfelelő engedélyekkel van rendelve.

   ![engedélyek megjelenítése](./media/resource-group-create-service-principal-portal/view-user-role.png)


## <a name="next-steps"></a>További lépések
* Több-bérlős alkalmazás beállításával kapcsolatban lásd: [fejlesztői útmutatója az Azure Resource Manager API-val engedélyezési](resource-manager-api-authentication.md).
* Biztonsági szabályzatok megadásával kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).  
* Rendelkezésre álló műveletek kapnak, vagy nem jogosult felhasználók listáját lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md).
