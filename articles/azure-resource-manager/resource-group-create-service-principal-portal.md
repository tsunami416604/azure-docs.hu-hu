---
title: Azure-alkalmazáshoz identitás létrehozása a portálon |} A Microsoft Docs
description: Ismerteti, hogyan hozzon létre egy új Azure Active Directory-alkalmazás és egyszerű szolgáltatás, amely a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager-erőforrásokhoz való hozzáférés kezelésére használható.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: 2f053f6dd98b9f4e97d69e51bce933a003633277
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497943"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Az Azure Active Directory-alkalmazás és -erőforrások elérésére képes egyszerű szolgáltatás létrehozása a portál használatával

Ha rendelkezik, amelyet eléréséhez, vagy módosítsa erőforrások, be kell állítania egy Azure Active Directory (AD) alkalmazást. Majd a szükséges engedélyeket rendelhet az AD-alkalmazás. Ez a megközelítés célszerű fut az alkalmazás a saját hitelesítő adatokkal, mert engedélyeket rendelhet a saját engedélyek eltérő alkalmazás identitását. Ezek az engedélyek jellemzően csak azt engedélyezik, amire az alkalmazásnak szüksége van.

Ez a cikk bemutatja, hogyan lehet ezeket a lépéseket a portálon keresztül. Egy egybérlős alkalmazást, ahol az alkalmazás futtatásához csak egy szervezeten belül célja összpontosít. Általában használnak a egybérlős alkalmazások az üzleti alkalmazások futtatására a szervezeten belül.

> [!IMPORTANT]
> Egyszerű szolgáltatás létrehozása helyett fontolja meg a felügyelt identitások használatával az Azure-erőforrások esetében az alkalmazás azonosítóját. A kód egy szolgáltatás, amely támogatja a felügyelt identitások és hozzáférések erőforrások, amelyek támogatják az Azure Active Directory-hitelesítés fut, ha felügyelt identitások, jobb megoldás. További információ az Azure-erőforrásokhoz, mely szolgáltatások jelenleg támogatja, beleértve a felügyelt identitásokból [Mi az Azure-erőforrások felügyelt identitások?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="required-permissions"></a>Szükséges engedélyek

Ez a cikk végrehajtásához szükséges engedélyekkel alkalmazások regisztrációjához az Azure AD-bérlő, és alkalmazások szerepkörhöz rendeléséhez az Azure-előfizetésében. Ellenőrizze, hogy ezek a lépések végrehajtásához a megfelelő engedélyekkel rendelkezik.

### <a name="check-azure-active-directory-permissions"></a>Azure Active Directory-engedélyek ellenőrzése

1. Válassza az **Azure Active Directory** elemet.

   ![Azure Active Directory kiválasztása](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Az Azure Active Directory területen válassza a **Felhasználói beállítások** lehetőséget.

   ![Válassza ki a felhasználói beállítások](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Ellenőrizze a **alkalmazásregisztrációk** beállítás. Ha beállítása **Igen**, nem rendszergazda felhasználók regisztrálhatnak az AD-alkalmazások. Ez a beállítás azt jelenti, hogy az Azure AD-bérlő bármely felhasználója regisztrálhat alkalmazásokat. Folytassa [Azure ellenőrizze előfizetése engedélyei](#check-azure-subscription-permissions).

   ![alkalmazásregisztrációk megtekintése](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Ha az alkalmazásregisztrációk beállítás értéke **nem**, csak [globális rendszergazdák](../active-directory/users-groups-roles/directory-assign-admin-roles.md) alkalmazások akkor regisztrálhatnak. Ellenőrizze, hogy a fiók az Azure AD-bérlő rendszergazdája. Válassza ki **áttekintése** , és tekintse meg a felhasználó adatait. Ha a fiók hozzá van rendelve a felhasználói szerepkörhöz, de korlátozott rendszergazdai jogosultságú felhasználókhoz az Alkalmazásbeállítás regisztráció (az előző lépésben), kérje meg a rendszergazdát, hogy bármelyik, a globális rendszergazdai szerepkörrel, vagy rendelje hozzá engedélyezése a felhasználók számára alkalmazások regisztrálása.

   ![felhasználó keresése](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Azure-előfizetés engedélyek ellenőrzése

Az Azure-előfizetésében, a fióknak rendelkeznie kell `Microsoft.Authorization/*/Write` AD-alkalmazás hozzárendelése szerepkörhöz való hozzáférést. Ezt a műveletet a [Tulajdonos](../role-based-access-control/built-in-roles.md#owner) szerepkör vagy a [Felhasználói hozzáférés rendszergazdája](../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör végezheti el. Ha a fiók hozzá van rendelve a **közreműködői** szerepkör nem rendelkezik megfelelő engedéllyel. Az egyszerű szolgáltatás hozzárendelése szerepkörhöz megkísérlésekor hibaüzenetet kap.

Ellenőrizze előfizetése engedélyei között:

1. Válassza ki a fiók jobb felső sarokban, majd válassza ki **saját engedélyek**.

   ![Válassza ki a felhasználói engedélyek](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. A legördülő listából válassza ki az előfizetést. Válassza ki **teljes hozzáférési megtekintéséhez kattintson ide az előfizetés részletei**.

   ![felhasználó keresése](./media/resource-group-create-service-principal-portal/view-details.png)

1. A hozzárendelt szerepkörök megtekintése, és határozza meg, hogy rendelkezik-e megfelelő engedélyekkel AD-alkalmazás hozzárendelése szerepkörhöz. Ha nem, kérje meg az előfizetés adminisztrátorát, hogy vegye fel Önt a felhasználói hozzáférés rendszergazdájának szerepköre. Az alábbi ábrán a felhasználó a tulajdonos szerepkör, ami azt jelenti, hogy a felhasználó rendelkezik megfelelő engedélyekkel van rendelve.

   ![engedélyek megjelenítése](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Az Azure Active Directory-alkalmazás létrehozása

1. Jelentkezzen be az Azure-fiók révén a [az Azure portal](https://portal.azure.com).
1. Válassza az **Azure Active Directory** elemet.

   ![Azure Active Directory kiválasztása](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Válassza az **Alkalmazásregisztrációk** elemet.

   ![alkalmazásregisztrációk kiválasztása](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Válassza az **Új alkalmazás regisztrálása** elemet.

   ![alkalmazás hozzáadása](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Adja meg az alkalmazás nevét és URL-címét. Válassza a **Webalkalmazás/API** lehetőséget a létrehozni kívánt alkalmazás típusaként. Hitelesítő adatok nem hozható létre egy [natív alkalmazás](../active-directory/manage-apps/application-proxy-configure-native-client-application.md); így, hogy egy automatikus alkalmazásnak típusa nem megfelelő. Miután beállította az értékeket, válassza ki a **létrehozás**.

   ![alkalmazás elnevezése](./media/resource-group-create-service-principal-portal/create-app.png)

Létrehozta az alkalmazást.

## <a name="get-application-id-and-authentication-key"></a>Alkalmazásazonosító és hitelesítési kulcs beszerzése

Ha programozott módon jelentkezik be, szüksége lesz az alkalmazásazonosítóra és egy hitelesítési kulcsra. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. Válassza ki az alkalmazást az Azure Active Directory **Alkalmazásregisztrációk** területén.

   ![alkalmazás kiválasztása](./media/resource-group-create-service-principal-portal/select-app.png)

1. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában. Egyes [mintaalkalmazások](#log-in-as-the-application) ügyfél-azonosítóként hivatkoznak erre az értékre.

   ![ügyfél-azonosító](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. A hitelesítési kulcs létrehozásához válassza a **Beállítások** elemet.

   ![beállítások kiválasztása](./media/resource-group-create-service-principal-portal/select-settings.png)

1. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.

   ![kulcsok kiválasztása](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Adjon meg egy leírást és egy időtartamot a kulcshoz. Ha elkészült, kattintson a **Mentés** elemre.

   ![kulcs mentése](./media/resource-group-create-service-principal-portal/save-key.png)

   A kulcs mentése után megjelenik a kulcs értéke. Másolja ezt az értéket, mert nem sikerült beolvasni a kulcsot később. A kulcs értékét az alkalmazás azonosítójával jelentkezzen be az alkalmazást, hogy adja meg. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![mentett kulcs](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>A bérlőazonosító beszerzése

Ha programozott módon jelentkezik be, át kell adnia a bérlőazonosítót a hitelesítési kérelemmel együtt.

1. Válassza az **Azure Active Directory** elemet.

   ![Azure Active Directory kiválasztása](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. A bérlőazonosító lekéréséhez válassza ki az Azure AD-bérlőjéhez tartozó **Tulajdonságok** elemet.

   ![Azure AD tulajdonságok kiválasztása](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Másolja ki a **Címtár-azonosítót**. Ez az érték a bérlőazonosítója.

   ![bérlőazonosító](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Alkalmazás hozzárendelése szerepkörhöz

Az előfizetésben lévő erőforrások eléréséhez, hozzá kell rendelnie az alkalmazás egy szerepkörhöz. Döntse el, melyik szerepkör jelöli az alkalmazást a megfelelő engedélyekkel. Az elérhető szerepkörök kapcsolatos további információkért lásd: [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md).

Beállíthatja a hatókör szintjén is az előfizetés, erőforráscsoport vagy erőforrás. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Például egy alkalmazás az Olvasó szerepkörhöz, egy erőforráscsoport hozzáadása azt jelenti, hogy azt az erőforráscsoportot és az összes benne található erőforrást olvashatja.

1. Keresse meg a hatókör az alkalmazást hozzárendelni kívánt szintjét. Válassza ki például az előfizetések szintjén szerepkör hozzárendelése **előfizetések**. Ehelyett kiválaszthatja egy erőforráscsoportra vagy erőforrásra.

   ![Válasszon előfizetést](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Válassza ki az adott előfizetés (erőforráscsoportra vagy erőforrásra) az alkalmazás hozzárendelése a.

   ![Válasszon hozzárendelés előfizetést](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Válassza ki **hozzáférés-vezérlés (IAM)**.

   ![Jelölje be a hozzáférés](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Válassza a **Hozzáadás** lehetőséget.

   ![Válassza a hozzáadása](./media/resource-group-create-service-principal-portal/select-add.png)

1. Válassza ki a az alkalmazáshoz hozzárendelni kívánt szerepkört. Annak érdekében, hogy az alkalmazás hajtható végre műveleteket, például **újraindítás**, **start** és **leállítása** példányok, kell rendelkeznie a jelölje ki a szerepkört **közreműködő**. Az alábbi képen látható a **olvasó** szerepkör.

   ![szerepkör kiválasztása](./media/resource-group-create-service-principal-portal/select-role.png)

1. Alapértelmezés szerint az Azure Active Directory-alkalmazások nem megjelenik az elérhető lehetőségek közül. Az alkalmazás megkereséséhez a nevét, a keresési mezőben kell adnia. Válassza ki azt.

   ![alkalmazás keresése](./media/resource-group-create-service-principal-portal/search-app.png)

1. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. Láthatja, hogy az alkalmazás a felhasználók az adott hatókörnél szerepköre a listában.

## <a name="next-steps"></a>További lépések
* Több-bérlős alkalmazás beállításával kapcsolatban lásd: [fejlesztői útmutatója az Azure Resource Manager API-val engedélyezési](resource-manager-api-authentication.md).
* Biztonsági szabályzatok megadásával kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).  
* Rendelkezésre álló műveletek kapnak, vagy nem jogosult felhasználók listáját lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md).
