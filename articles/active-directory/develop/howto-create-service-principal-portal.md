---
title: Azure-alkalmazáshoz identitás létrehozása a portálon |} A Microsoft Docs
description: Ismerteti, hogyan hozzon létre egy új Azure Active Directory-alkalmazás és egyszerű szolgáltatás, amely a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager-erőforrásokhoz való hozzáférés kezelésére használható.
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: tomfitz
ms.openlocfilehash: 708ec047a1c9883e34f2e255cd7a0f1237ded2bf
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096874"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Útmutató: Az Azure AD-alkalmazás és -erőforrások elérésére képes egyszerű szolgáltatás létrehozása a portál használatával

Ha rendelkezik, amelyet eléréséhez, vagy módosítsa erőforrások, az alkalmazáshoz tartozó identitást hozhat létre. Ezt az identitást szolgáltatásnévnek nevezzük. A szolgáltatásnévnek majd hozzárendelheti a szükséges engedélyekkel. Ez a cikk bemutatja, hogyan az egyszerű szolgáltatás létrehozása a portál használatával. Egy egybérlős alkalmazást, ahol az alkalmazás futtatásához csak egy szervezeten belül célja összpontosít. Általában használnak a egybérlős alkalmazások az üzleti alkalmazások futtatására a szervezeten belül.

> [!IMPORTANT]
> Egyszerű szolgáltatás létrehozása helyett fontolja meg a felügyelt identitások használatával az Azure-erőforrások esetében az alkalmazás azonosítóját. A kódot, amely támogatja a felügyelt identitások és hozzáférések erőforrások, amelyek támogatják az Azure Active Directory (Azure AD) hitelesítési szolgáltatás fut, ha a felügyelt identitásokból, jobb megoldás. További információ az Azure-erőforrásokhoz, mely szolgáltatások jelenleg támogatja, beleértve a felügyelt identitásokból [Mi az Azure-erőforrások felügyelt identitások?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Az Azure Active Directory-alkalmazás létrehozása

Nyissa meg közvetlenül az identitás létrehozása. Ha problémát tapasztal, ellenőrizze a [szükséges engedélyek](#required-permissions) , hogy a fiók képes létrehozni az identitást.

1. Jelentkezzen be az Azure-fiók révén a [az Azure portal](https://portal.azure.com).
1. Válassza az **Azure Active Directory** elemet.
1. Válassza az **Alkalmazásregisztrációk** elemet.

   ![alkalmazásregisztrációk kiválasztása](./media/howto-create-service-principal-portal/select-app-registrations.png)

1. Válassza az **Új alkalmazás regisztrálása** elemet.

   ![Alkalmazás felvétele](./media/howto-create-service-principal-portal/select-add-app.png)

1. Adja meg az alkalmazás nevét és URL-címét. Válassza a **Webalkalmazás/API** lehetőséget a létrehozni kívánt alkalmazás típusaként. Hitelesítő adatok nem hozható létre egy [natív alkalmazás](../manage-apps/application-proxy-configure-native-client-application.md). Egy automatikus alkalmazásnak típus nem használható. Miután beállította az értékeket, válassza ki a **létrehozás**.

   ![alkalmazás elnevezése](./media/howto-create-service-principal-portal/create-app.png)

Az Azure AD-alkalmazás és egyszerű szolgáltatás létrehozott.

## <a name="assign-the-application-to-a-role"></a>Alkalmazások szerepkörhöz rendeléséhez

Az előfizetésben lévő erőforrások eléréséhez, hozzá kell rendelnie az alkalmazás egy szerepkörhöz. Döntse el, milyen szerepkört kínál az alkalmazás a megfelelő engedélyekkel. Az elérhető szerepkörök kapcsolatos további információkért lásd: [RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md).

Beállíthatja a hatókör szintjén is az előfizetés, erőforráscsoport vagy erőforrás. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Például egy alkalmazás az Olvasó szerepkörhöz, egy erőforráscsoport hozzáadása azt jelenti, hogy azt az erőforráscsoportot és az összes benne található erőforrást olvashatja.

1. Keresse meg a hatókör az alkalmazást hozzárendelni kívánt szintjét. Válassza ki például az előfizetések szintjén szerepkör hozzárendelése **minden szolgáltatás** és **előfizetések**.

   ![Előfizetés kiválasztása](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki az adott előfizetés hozzárendelése az alkalmazást.

   ![Válasszon hozzárendelés előfizetést](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetéshez, válasszon **globális előfizetés-szűrő**. Ellenőrizze, hogy az előfizetés ki van jelölve a portálon. 

1. Válassza ki **hozzáférés-vezérlés (IAM)**.
1. Válassza ki **szerepkör-hozzárendelés hozzáadása**.

   ![Válassza ki a szerepkör-hozzárendelés hozzáadása](./media/howto-create-service-principal-portal/select-add.png)

1. Válassza ki a az alkalmazáshoz hozzárendelni kívánt szerepkört. Hajtsa végre a műveleteket, például az alkalmazás számára **újraindítás**, **start** és **leállítása** példányok, válassza ki a **közreműködői** szerepkör. Alapértelmezés szerint az Azure AD-alkalmazások nem megjelenik az elérhető lehetőségek közül. Keresse meg az alkalmazás, keresse meg a nevét, és jelölje ki.

   ![Szerepkör kiválasztása](./media/howto-create-service-principal-portal/select-role.png)

1. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. Láthatja, hogy az alkalmazás a felhasználók az adott hatókörnél szerepköre a listában.

Az egyszerű szolgáltatás be van állítva. Akkor megkezdheti a parancsfájlok vagy alkalmazások futtatásához. Ez a szakasz bemutatja, hogyan használatával lekérjük az értékeket, amelyek szükségesek, amikor programozott módon jelentkezik be.

## <a name="get-values-for-signing-in"></a>Bejelentkezés értékek beolvasása

### <a name="get-tenant-id"></a>A bérlőazonosító beszerzése

Ha programozott módon jelentkezik be, kell átadni a bérlő Azonosítóját, a hitelesítési kérelemmel együtt.

1. Válassza az **Azure Active Directory** elemet.
1. Válassza ki **tulajdonságok**.

   ![az Azure AD tulajdonságok kiválasztása](./media/howto-create-service-principal-portal/select-ad-properties.png)

1. Másolás a **címtár-azonosító** lekérni a bérlő azonosítója.

   ![Bérlőazonosító](./media/howto-create-service-principal-portal/copy-directory-id.png)

### <a name="get-application-id-and-authentication-key"></a>Alkalmazásazonosító és hitelesítési kulcs beszerzése

Az alkalmazás és a hitelesítési kulcs Azonosítóját is szükséges. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. A **alkalmazásregisztrációk** az Azure AD-ben válassza ki az alkalmazását.

   ![Alkalmazás kijelölése](./media/howto-create-service-principal-portal/select-app.png)

1. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában.

   ![Ügyfél-azonosító](./media/howto-create-service-principal-portal/copy-app-id.png)

1. Válassza ki **beállítások**.

   ![beállítások kiválasztása](./media/howto-create-service-principal-portal/select-settings.png)

1. Válassza a **Kulcsok** elemet.
1. Adjon meg egy leírást és egy időtartamot a kulcshoz. Ha elkészült, kattintson a **Mentés** elemre.

   ![kulcs mentése](./media/howto-create-service-principal-portal/save-key.png)

   A kulcs mentése után megjelenik a kulcs értéke. Másolja ezt az értéket, mert nem sikerült beolvasni a kulcsot később. A kulcs értékét az alkalmazás azonosítójával jelentkezzen be az alkalmazást, hogy adja meg. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![mentett kulcs](./media/howto-create-service-principal-portal/copy-key.png)

## <a name="required-permissions"></a>Szükséges engedélyek

Rendelkezik megfelelő engedélyekkel alkalmazások regisztrációjához az Azure AD-bérlőhöz, és alkalmazások szerepkörhöz rendeléséhez az Azure-előfizetésében.

### <a name="check-azure-ad-permissions"></a>Ellenőrizze az Azure AD-engedélyekről

1. Válassza az **Azure Active Directory** elemet.
1. Megjegyzés: a szerepkör. Ha rendelkezik a **felhasználói** szerepkör, győződjön meg arról, hogy a nem rendszergazda felhasználók regisztrálhatnak alkalmazásokat.

   ![felhasználó keresése](./media/howto-create-service-principal-portal/view-user-info.png)

1. Válassza ki **felhasználói beállítások**.

   ![Válassza ki a felhasználói beállítások](./media/howto-create-service-principal-portal/select-user-settings.png)

1. Ellenőrizze a **alkalmazásregisztrációk** beállítás. Ez az érték csak egy rendszergazda állítható. Ha beállítása **Igen**, az Azure AD-bérlőben a felhasználói regisztrálhatnak egy alkalmazást.

   ![alkalmazásregisztrációk megtekintése](./media/howto-create-service-principal-portal/view-app-registrations.png)

Ha az alkalmazásregisztrációk beállítás értéke **nem**, csak [globális rendszergazdák](../users-groups-roles/directory-assign-admin-roles.md) alkalmazások akkor regisztrálhatnak. Ha a fiók hozzá van rendelve a felhasználói szerepkörhöz, de az alkalmazás regisztrációs beállítás korlátozott rendszergazdai jogosultságú felhasználókhoz, kérje meg a rendszergazdát, hogy bármelyik, a globális rendszergazdai szerepkörrel, vagy rendelje hozzá engedélyezése a felhasználók számára alkalmazások regisztrálása.

### <a name="check-azure-subscription-permissions"></a>Azure-előfizetés engedélyek ellenőrzése

Az Azure-előfizetésében, a fióknak rendelkeznie kell `Microsoft.Authorization/*/Write` AD-alkalmazás hozzárendelése szerepkörhöz való hozzáférést. Ezt a műveletet a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) szerepkör vagy a [Felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör végezheti el. Ha a fiók hozzá van rendelve a **közreműködői** szerepkör nem rendelkezik megfelelő engedéllyel. Az egyszerű szolgáltatás hozzárendelése szerepkörhöz megkísérlésekor hibaüzenetet kap.

Ellenőrizze előfizetése engedélyei között:

1. Válassza ki a fiók jobb felső sarokban, majd válassza ki **saját engedélyek**.

   ![Válassza ki a felhasználói engedélyek](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. A legördülő listából válassza ki a kívánt előfizetést, az egyszerű szolgáltatás létrehozásához. Ezután válassza ki **teljes hozzáférési megtekintéséhez kattintson ide az előfizetés részletei**.

   ![felhasználó keresése](./media/howto-create-service-principal-portal/view-details.png)

1. A hozzárendelt szerepkörök megtekintése, és határozza meg, hogy rendelkezik-e megfelelő engedélyekkel AD-alkalmazás hozzárendelése szerepkörhöz. Ha nem, kérje meg az előfizetés adminisztrátorát, hogy vegye fel Önt a felhasználói hozzáférés rendszergazdájának szerepköre. Az alábbi ábrán a felhasználó a tulajdonos szerepkör, ami azt jelenti, hogy a felhasználó rendelkezik megfelelő engedélyekkel van rendelve.

   ![engedélyek megjelenítése](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>További lépések

* Több-bérlős alkalmazás beállításával kapcsolatban lásd: [fejlesztői útmutatója az Azure Resource Manager API-val engedélyezési](../../azure-resource-manager/resource-manager-api-authentication.md).
* Biztonsági szabályzatok megadásával kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md).  
* Rendelkezésre álló műveletek kapnak, vagy nem jogosult felhasználók listáját lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../../role-based-access-control/resource-provider-operations.md).
