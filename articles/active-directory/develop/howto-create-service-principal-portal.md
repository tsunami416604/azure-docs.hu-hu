---
title: Identitás létrehozása az Azure-alkalmazáshoz a portálon | Microsoft Docs
description: Ismerteti, hogyan lehet létrehozni egy új Azure Active Directory alkalmazást és egyszerű szolgáltatásnevet, amely a Azure Resource Manager szerepköralapú hozzáférés-vezérlésével használható az erőforrásokhoz való hozzáférés kezeléséhez.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 825966fbb0db537aad8de39e69e17418e6432b44
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324684"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Útmutató: A portál használatával létrehozhat egy Azure AD-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz

Ez a cikk bemutatja, hogyan hozhat létre egy új Azure Active Directory (Azure AD) alkalmazást és egyszerű szolgáltatást, amely a szerepköralapú hozzáférés-vezérléssel használható. Ha olyan kóddal rendelkezik, amelynek szüksége van az erőforrások eléréséhez vagy módosításához, létrehozhat egy identitást az alkalmazáshoz. Ezt az identitást szolgáltatásnévnek nevezzük. Ezután hozzárendelheti az egyszerű szolgáltatáshoz szükséges engedélyeket. Ez a cikk bemutatja, hogyan hozhatja létre az egyszerű szolgáltatásnevet a portál használatával. Egy egybérlős alkalmazásra koncentrál, amelyben az alkalmazás csak egy szervezeten belül fut. Általában egybérlős alkalmazásokat használ a szervezeten belül futó üzletági alkalmazásokhoz.

> [!IMPORTANT]
> Egyszerű szolgáltatásnév létrehozása helyett érdemes felügyelt identitásokat használni az Azure-erőforrásokhoz az alkalmazás identitásához. Ha a kód olyan szolgáltatáson fut, amely támogatja a felügyelt identitásokat, és hozzáfér az Azure AD-hitelesítést támogató erőforrásokhoz, a felügyelt identitások jobb megoldást biztosítanak Önnek. Ha többet szeretne megtudni az Azure-erőforrások felügyelt identitásáról, beleértve a jelenleg támogatott szolgáltatásokat, tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](../managed-identities-azure-resources/overview.md)című témakört.

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory-alkalmazás létrehozása

Ugorjon egyenesen az identitás létrehozásához. Ha probléma lép fel, ellenőrizze a [szükséges engedélyeket](#required-permissions) annak biztosításához, hogy a fiókja létre tudja hozni az identitást.

1. Jelentkezzen be az Azure-fiókjába [](https://portal.azure.com)a Azure Portalon keresztül.
1. Válassza az **Azure Active Directory** elemet.
1. Válassza az **Alkalmazásregisztrációk** elemet.
1. Válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás nevét. Válasszon egy támogatott számlatípust, amely meghatározza, hogy kik használhatják az alkalmazást. Az **átirányítási URI**területen válassza a **web** lehetőséget a létrehozni kívánt alkalmazás típusához. Adja meg azt az URI-t, ahová a hozzáférési tokent elküldi. [Natív alkalmazás](../manage-apps/application-proxy-configure-native-client-application.md)hitelesítő adatai nem hozhatók létre. Az adott típus nem használható automatikus alkalmazáshoz. Az értékek beállítása után válassza a **regisztráció**lehetőséget.

   ![Adja meg az alkalmazás nevét](./media/howto-create-service-principal-portal/create-app.png)

Létrehozott egy Azure AD-alkalmazást és egy egyszerű szolgáltatásnevet.

## <a name="assign-the-application-to-a-role"></a>Az alkalmazás társítása szerepkörhöz

Az előfizetés erőforrásainak eléréséhez hozzá kell rendelnie az alkalmazást egy szerepkörhöz. Döntse el, melyik szerepkör kínálja a megfelelő engedélyeket az alkalmazáshoz. Az elérhető szerepkörökről további információt a következő [témakörben talál: RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md).

Megadhatja a hatókört az előfizetés, az erőforráscsoport vagy az erőforrás szintjén. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Ha például hozzáad egy alkalmazást az erőforráscsoport olvasó szerepköréhez, az azt jelenti, hogy elolvashatja az erőforráscsoportot és a benne található összes erőforrást.

1. Navigáljon ahhoz a hatókörhöz, amelyhez hozzá szeretné rendelni az alkalmazást. Ha például egy szerepkört szeretne hozzárendelni az előfizetés hatókörében, válassza a **minden szolgáltatás** és **előfizetés**lehetőséget.

   ![Például rendeljen hozzá egy szerepkört az előfizetés hatókörében](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki azt az előfizetést, amelyhez hozzá kívánja rendelni az alkalmazást.

   ![Előfizetés kiválasztása hozzárendeléshez](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetést, válassza a **globális előfizetések szűrőt**. Győződjön meg arról, hogy a portálon a kívánt előfizetés van kiválasztva.

1. Válassza ki **hozzáférés-vezérlés (IAM)** .
1. Válassza ki **szerepkör-hozzárendelés hozzáadása**.
1. Válassza ki az alkalmazáshoz hozzárendelni kívánt szerepkört. Ha engedélyezni szeretné, hogy az alkalmazás olyan műveleteket hajtson végre, mint például az **Újraindítás**, a példányok **elindítása** és **leállítása** , válassza ki a **közreműködő** szerepkört. Alapértelmezés szerint az Azure AD-alkalmazások nem jelennek meg az elérhető beállítások között. Az alkalmazás megkereséséhez keresse meg a nevet, és jelölje ki.

   ![Válassza ki az alkalmazáshoz hozzárendelni kívánt szerepkört](./media/howto-create-service-principal-portal/select-role.png)

1. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. Az alkalmazás az adott hatókörhöz tartozó szerepkörhöz rendelt felhasználók listájában jelenik meg.

Az egyszerű szolgáltatásnév be van állítva. Elkezdheti használni a parancsfájlok vagy alkalmazások futtatását. A következő szakasz bemutatja, hogyan kérheti le a programozott módon történő bejelentkezéshez szükséges értékeket.

## <a name="get-values-for-signing-in"></a>Bejelentkezések értékeinek beolvasása

Ha programozott módon jelentkezik be, át kell adnia a bérlő AZONOSÍTÓját a hitelesítési kérelemmel. Az alkalmazáshoz és a hitelesítési kulcshoz is szüksége lesz az AZONOSÍTÓra. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. Válassza az **Azure Active Directory** elemet.
1. Az Azure AD-ban **Alkalmazásregisztrációk** válassza ki az alkalmazást.
1. Másolja a címtár-(bérlői) azonosítót, és tárolja azt az alkalmazás kódjában.

    ![Másolja a könyvtárat (bérlői azonosítót), és tárolja azt az alkalmazás kódjában.](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában.

   ![Az alkalmazás (ügyfél) AZONOSÍTÓjának másolása](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Tanúsítványok és titkos kódok
A Daemon-alkalmazások kétféle hitelesítő adatot használhatnak az Azure AD-vel való hitelesítéshez: tanúsítványok és alkalmazás-titkos kódok.  Javasoljuk, hogy használjon egy tanúsítványt, de új alkalmazás-titkos kulcsot is létrehozhat.

### <a name="upload-a-certificate"></a>Tanúsítvány feltöltése

Ha van ilyen, használhat meglévő tanúsítványt is.  Létrehozhat egy önaláírt tanúsítványt is tesztelési célokra. Nyissa meg a PowerShellt, és futtassa a [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsot a következő paraméterekkel egy önaláírt tanúsítvány létrehozásához a számítógép felhasználói `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`tanúsítványtárolójában:.  Exportálja a tanúsítványt a Windows Vezérlőpultján elérhető [felhasználói tanúsítvány kezelése](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC beépülő modul használatával.

A tanúsítvány feltöltése:

1. Válassza ki a **tanúsítványok & Secrets**elemet.
1. Válassza a **tanúsítvány feltöltése** lehetőséget, és válassza ki a tanúsítványt (egy meglévő tanúsítványt vagy az exportált önaláírt tanúsítványt).

    ![Válassza a tanúsítvány feltöltése lehetőséget, és válassza ki a hozzáadni kívánt elemet](./media/howto-create-service-principal-portal/upload-cert.png)

1. Válassza a **Hozzáadás** lehetőséget.

Miután regisztrálta a tanúsítványt az alkalmazással az alkalmazás regisztrációs portálján, engedélyeznie kell az ügyfélalkalmazás kódját a tanúsítvány használatához.

### <a name="create-a-new-application-secret"></a>Új alkalmazás-titok létrehozása

Ha úgy dönt, hogy nem használ tanúsítványt, létrehozhat egy új alkalmazás-titkot.

1. Válassza ki a **tanúsítványok & Secrets**elemet.
1. Válassza ki az **ügyfél titkai-> új ügyfél titkát**.
1. Adja meg a titok leírását és időtartamát. Ha elkészült, válassza a **Hozzáadás**lehetőséget.

   Az ügyfél titkos kulcsának mentése után megjelenik az ügyfél titkos kulcsának értéke. Másolja ezt az értéket, mert később nem tudja lekérni a kulcsot. Adja meg a kulcs értékét az alkalmazás-AZONOSÍTÓval, és jelentkezzen be alkalmazásként. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![Másolja a titkos értéket, mert később nem lehet beolvasni](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Szükséges engedélyek

Az alkalmazás Azure AD-Bérlővel való regisztrálásához és az alkalmazás az Azure-előfizetésben lévő szerepkörhöz való hozzárendeléséhez megfelelő engedélyekkel kell rendelkeznie.

### <a name="check-azure-ad-permissions"></a>Azure AD-engedélyek keresése

1. Válassza az **Azure Active Directory** elemet.
1. Jegyezze fel a szerepkört. Ha rendelkezik a **felhasználói** szerepkörrel, meg kell győződnie arról, hogy a nem rendszergazdák regisztrálhatják az alkalmazásokat.

   ![Keresse meg a szerepkört. Ha Ön felhasználó, győződjön meg arról, hogy a nem rendszergazdák regisztrálhatnak alkalmazásokat](./media/howto-create-service-principal-portal/view-user-info.png)

1. Válassza a **felhasználói beállítások**lehetőséget.
1. Keresse meg a **Alkalmazásregisztrációk** beállítást. Ezt az értéket csak rendszergazda állíthatja be. Ha az **Igen**értékre van állítva, akkor az Azure ad-bérlő bármelyik felhasználója regisztrálhat egy alkalmazást.

Ha az alkalmazás regisztrációja **nem**értékre van állítva, akkor csak a rendszergazdai szerepkörrel rendelkező felhasználók regisztrálhatják az ilyen típusú alkalmazásokat. A rendelkezésre álló rendszergazdai szerepkörökről és az egyes szerepkörökhöz megadott Azure AD-engedélyekről az [elérhető szerepkörök](../users-groups-roles/directory-assign-admin-roles.md#available-roles) és [szerepkör-engedélyek](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) című szakaszban talál további információt. Ha a fiókja hozzá van rendelve a felhasználói szerepkörhöz, de az alkalmazás regisztrációs beállítása a rendszergazda felhasználókra korlátozódik, kérje meg a rendszergazdát, hogy rendeljen hozzá egy olyan rendszergazdai szerepkört, amely az alkalmazások regisztrálásának minden aspektusát létrehozhatja és kezelheti, vagy engedélyezheti a felhasználók számára, hogy alkalmazások regisztrálása.

### <a name="check-azure-subscription-permissions"></a>Azure-előfizetési engedélyek keresése

Az Azure-előfizetésében a fióknak `Microsoft.Authorization/*/Write` hozzáféréssel kell rendelkeznie az ad-alkalmazás szerepkörhöz való hozzárendeléséhez. Ezt a műveletet a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) szerepkör vagy a [Felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör végezheti el. Ha a fiókja hozzá van rendelve a **közreműködő** szerepkörhöz, Önnek nincs megfelelő engedélye. Hibaüzenet jelenik meg, amikor megkísérli hozzárendelni a szolgáltatásnevet egy szerepkörhöz.

Az előfizetési engedélyek ellenőrzését:

1. Válassza ki a fiókját a jobb felső sarokban, és válassza a **... – > saját engedélyek**lehetőséget.

   ![Válassza ki a fiókját és a felhasználói engedélyeit](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. A legördülő listában válassza ki azt az előfizetést, amelyben létre szeretné hozni az egyszerű szolgáltatást. Ezután **kattintson ide az előfizetés teljes hozzáférés részleteinek megtekintéséhez**.

   ![Válassza ki azt az előfizetést, amelyben létre kívánja hozni a szolgáltatásnevet a következőben:](./media/howto-create-service-principal-portal/view-details.png)

1. Válassza ki a **szerepkör** -hozzárendeléseket a hozzárendelt szerepkörök megtekintéséhez, és állapítsa meg, hogy rendelkezik-e megfelelő engedélyekkel egy ad-alkalmazás szerepkörhöz való hozzárendeléséhez. Ha nem, kérje meg az előfizetés rendszergazdáját, hogy vegye fel Önt a felhasználói hozzáférés rendszergazdai szerepkörbe. A következő képen a felhasználó a tulajdonos szerepkörhöz lesz rendelve, ami azt jelenti, hogy a felhasználó rendelkezik a megfelelő engedélyekkel.

   ![Ez a példa azt mutatja be, hogy a felhasználó hozzá van rendelve a tulajdonosi szerepkörhöz](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>További lépések

* Több-bérlős alkalmazás beállításához tekintse [meg a fejlesztői útmutató a Azure Resource Manager API-val](../../azure-resource-manager/resource-manager-api-authentication.md)való engedélyezéséhez című témakört.
* A biztonsági szabályzatok megadásával kapcsolatos információkért lásd: [Azure szerepköralapú Access Control](../../role-based-access-control/role-assignments-portal.md).  
* A felhasználók számára megadható vagy megtagadható elérhető műveletek listáját itt tekintheti meg: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../../role-based-access-control/resource-provider-operations.md).
