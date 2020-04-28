---
title: Azure AD-alkalmazás & egyszerű szolgáltatásnév létrehozása a portálon
titleSuffix: Microsoft identity platform
description: Hozzon létre egy új Azure Active Directory alkalmazás & egyszerű szolgáltatásnév használatával az erőforrásokhoz való hozzáférés kezeléséhez a Azure Resource Manager-ben szerepköralapú hozzáférés-vezérléssel.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: d1ee8e90d1d690315b2727a050e0383d7d28dc03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546136"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Útmutató: a portál használatával létrehozhat egy Azure AD-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz

Ez a cikk bemutatja, hogyan hozhat létre egy új Azure Active Directory (Azure AD) alkalmazást és egyszerű szolgáltatást, amely a szerepköralapú hozzáférés-vezérléssel használható. Ha olyan kóddal rendelkezik, amelynek szüksége van az erőforrások eléréséhez vagy módosításához, létrehozhat egy identitást az alkalmazáshoz. Ezt az identitást szolgáltatásnévnek nevezzük. Ezután hozzárendelheti az egyszerű szolgáltatáshoz szükséges engedélyeket. Ez a cikk bemutatja, hogyan hozhatja létre az egyszerű szolgáltatásnevet a portál használatával. Egy egybérlős alkalmazásra koncentrál, amelyben az alkalmazás csak egy szervezeten belül fut. Általában egybérlős alkalmazásokat használ a szervezeten belül futó üzletági alkalmazásokhoz.

> [!IMPORTANT]
> Egyszerű szolgáltatásnév létrehozása helyett érdemes felügyelt identitásokat használni az Azure-erőforrásokhoz az alkalmazás identitásához. Ha a kód olyan szolgáltatáson fut, amely támogatja a felügyelt identitásokat, és hozzáfér az Azure AD-hitelesítést támogató erőforrásokhoz, a felügyelt identitások jobb megoldást biztosítanak Önnek. Ha többet szeretne megtudni az Azure-erőforrások felügyelt identitásáról, beleértve a jelenleg támogatott szolgáltatásokat, tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](../managed-identities-azure-resources/overview.md)című témakört.

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory-alkalmazás létrehozása

Ugorjon egyenesen az identitás létrehozásához. Ha probléma lép fel, ellenőrizze a [szükséges engedélyeket](#required-permissions) annak biztosításához, hogy a fiókja létre tudja hozni az identitást.

1. Jelentkezzen be az Azure-fiókjába a [Azure Portalon](https://portal.azure.com)keresztül.
1. Válassza a **Azure Active Directory**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás nevét. Válasszon egy támogatott számlatípust, amely meghatározza, hogy kik használhatják az alkalmazást. Az **átirányítási URI**területen válassza a **web** lehetőséget a létrehozni kívánt alkalmazás típusához. Adja meg azt az URI-t, ahová a hozzáférési tokent elküldi. [Natív alkalmazás](../manage-apps/application-proxy-configure-native-client-application.md)hitelesítő adatai nem hozhatók létre. Az adott típus nem használható automatikus alkalmazáshoz. Az értékek beállítása után válassza a **regisztráció**lehetőséget.

   ![Adja meg az alkalmazás nevét](./media/howto-create-service-principal-portal/create-app.png)

Létrehozott egy Azure AD-alkalmazást és egy egyszerű szolgáltatásnevet.

## <a name="assign-a-role-to-the-application"></a>Szerepkör társítása az alkalmazáshoz

Az előfizetéshez tartozó erőforrások eléréséhez hozzá kell rendelnie egy szerepkört az alkalmazáshoz. Döntse el, melyik szerepkör kínálja a megfelelő engedélyeket az alkalmazáshoz. Az elérhető szerepkörökről a [RBAC: beépített szerepkörök](../../role-based-access-control/built-in-roles.md)című témakörben olvashat bővebben.

Megadhatja a hatókört az előfizetés, az erőforráscsoport vagy az erőforrás szintjén. Az engedélyek a hatókör alacsonyabb szintjein vannak örökölve. Ha például hozzáad egy alkalmazást az erőforráscsoport olvasó szerepköréhez, az azt jelenti, hogy elolvashatja az erőforráscsoportot és a benne található összes erőforrást.

1. A Azure Portal válassza ki azt a hatóköri szintet, amelyhez az alkalmazást hozzá szeretné rendelni. Ha például egy szerepkört szeretne hozzárendelni az előfizetés hatóköréhez, keresse meg és válassza ki az **előfizetések**elemet, vagy válassza az **előfizetések** lehetőséget a **kezdőlapon** .

   ![Például rendeljen hozzá egy szerepkört az előfizetés hatókörében](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki azt az előfizetést, amelyhez hozzá kívánja rendelni az alkalmazást.

   ![Előfizetés kiválasztása hozzárendeléshez](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetést, válassza a **globális előfizetések szűrőt**. Győződjön meg arról, hogy a portálon a kívánt előfizetés van kiválasztva.

1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
1. Válassza ki az alkalmazáshoz hozzárendelni kívánt szerepkört. Ha például engedélyezni szeretné, hogy az alkalmazás olyan műveleteket hajtson végre, mint például az **Újraindítás**, a példányok **elindítása** és **leállítása** , válassza ki a **közreműködő** szerepkört.  További információ az [elérhető szerepkörökről](../../role-based-access-control/built-in-roles.md) alapértelmezés szerint az Azure ad-alkalmazások nem jelennek meg az elérhető lehetőségek között. Az alkalmazás megkereséséhez keresse meg a nevet, és jelölje ki.

   ![Válassza ki az alkalmazáshoz hozzárendelni kívánt szerepkört](./media/howto-create-service-principal-portal/select-role.png)

1. A szerepkör hozzárendelésének befejezéséhez kattintson a **Mentés** gombra. Az alkalmazás az adott hatókörhöz tartozó szerepkörrel rendelkező felhasználók listájában jelenik meg.

Az egyszerű szolgáltatásnév be van állítva. Elkezdheti használni a parancsfájlok vagy alkalmazások futtatását. A következő szakasz bemutatja, hogyan kérheti le a programozott módon történő bejelentkezéshez szükséges értékeket.

## <a name="get-values-for-signing-in"></a>Bejelentkezések értékeinek beolvasása

Ha programozott módon jelentkezik be, át kell adnia a bérlő AZONOSÍTÓját a hitelesítési kérelemmel. Az alkalmazáshoz és a hitelesítési kulcshoz is szüksége lesz az AZONOSÍTÓra. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. Válassza a **Azure Active Directory**lehetőséget.
1. Az Azure AD-ban **Alkalmazásregisztrációk** válassza ki az alkalmazást.
1. Másolja a címtár-(bérlői) azonosítót, és tárolja azt az alkalmazás kódjában.

    ![Másolja a könyvtárat (bérlői azonosítót), és tárolja azt az alkalmazás kódjában.](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában.

   ![Az alkalmazás (ügyfél) AZONOSÍTÓjának másolása](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Tanúsítványok és titkos kódok
A Daemon-alkalmazások kétféle hitelesítő adatot használhatnak az Azure AD-vel való hitelesítéshez: tanúsítványok és alkalmazás-titkos kódok.  Javasoljuk, hogy használjon egy tanúsítványt, de új alkalmazás-titkos kulcsot is létrehozhat.

### <a name="upload-a-certificate"></a>Tanúsítvány feltöltése

Ha van ilyen, használhat meglévő tanúsítványt is.  Lehetőség van arra is, hogy önaláírt tanúsítványt *csak tesztelési célra*hozzon létre. Nyissa meg a PowerShellt, és futtassa a [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsot a következő paraméterekkel egy önaláírt tanúsítvány létrehozásához a számítógép felhasználói tanúsítványtárolójában: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportálja a tanúsítványt egy fájlba a Windows Vezérlőpultján elérhető [felhasználói tanúsítvány kezelése](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC beépülő modul használatával.

1. Válassza a **Start** menü **Futtatás** elemét, majd írja be a **certmgr. msc parancsot**.

   Megjelenik az aktuális felhasználóhoz tartozó tanúsítványkezelő eszköz.

1. A tanúsítványok megtekintéséhez a bal oldali ablaktábla **tanúsítványok-aktuális felhasználó** területén bontsa ki a **személyes** könyvtárat.
1. Kattintson a jobb gombbal a létrehozott tanúsítványra, válassza a **minden feladat – >exportálás**lehetőséget.
1. Kövesse a tanúsítvány exportálása varázslót.  Ne exportálja a titkos kulcsot, és exportálja a-ba. CER-fájl.

A tanúsítvány feltöltése:

1. Válassza a **Azure Active Directory**lehetőséget.
1. Az Azure AD-ban **Alkalmazásregisztrációk** válassza ki az alkalmazást.
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

   Az ügyfél titkos kulcsának mentése után megjelenik az ügyfél titkos kulcsának értéke. Másolja ezt az értéket, mert később nem fogja tudni lekérni a kulcsot. Adja meg a kulcs értékét az alkalmazás-AZONOSÍTÓval, és jelentkezzen be alkalmazásként. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![Másolja a titkos értéket, mert később nem lehet beolvasni](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Hozzáférési szabályzatok konfigurálása az erőforrásokon
Ne feledje, hogy az alkalmazáshoz hozzáférő erőforrásokhoz is konfigurálnia kell a hozzáadási engedélyeket. A [Key Vault hozzáférési házirendjeit is frissítenie](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) kell, hogy az alkalmazás hozzáférjen a kulcsokhoz, titkokhoz vagy tanúsítványokhoz.  

1. A [Azure Portal](https://portal.azure.com)navigáljon a kulcstartóhoz, és válassza a **hozzáférési szabályzatok**lehetőséget.  
1. Válassza a **hozzáférési házirend hozzáadása**lehetőséget, majd válassza ki az alkalmazáshoz használni kívánt kulcs-, titkos és tanúsítvány-engedélyeket.  Válassza ki a korábban létrehozott szolgáltatásnevet.
1. A hozzáférési szabályzat hozzáadásához válassza a **Hozzáadás** lehetőséget, majd a **Mentés** gombra kattintva véglegesítse a módosításokat.
    ![Hozzáférési szabályzat hozzáadása](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Szükséges engedélyek

Megfelelő engedélyekkel kell rendelkeznie az alkalmazások Azure AD-Bérlővel való regisztrálásához, és az alkalmazáshoz az Azure-előfizetéshez tartozó szerepkört kell rendelni.

### <a name="check-azure-ad-permissions"></a>Azure AD-engedélyek keresése

1. Válassza a **Azure Active Directory**lehetőséget.
1. Jegyezze fel a szerepkört. Ha rendelkezik a **felhasználói** szerepkörrel, meg kell győződnie arról, hogy a nem rendszergazdák regisztrálhatják az alkalmazásokat.

   ![Keresse meg a szerepkört. Ha Ön felhasználó, győződjön meg arról, hogy a nem rendszergazdák regisztrálhatnak alkalmazásokat](./media/howto-create-service-principal-portal/view-user-info.png)

1. A bal oldali ablaktáblán válassza a **felhasználói beállítások**lehetőséget.
1. Keresse meg a **Alkalmazásregisztrációk** beállítást. Ezt az értéket csak rendszergazda állíthatja be. Ha az **Igen**értékre van állítva, akkor az Azure ad-bérlő bármelyik felhasználója regisztrálhat egy alkalmazást.

Ha az alkalmazás regisztrációja **nem**értékre van állítva, akkor csak a rendszergazdai szerepkörrel rendelkező felhasználók regisztrálhatják az ilyen típusú alkalmazásokat. A rendelkezésre álló rendszergazdai szerepkörökről és az egyes szerepkörökhöz megadott Azure AD-engedélyekről az [elérhető szerepkörök](../users-groups-roles/directory-assign-admin-roles.md#available-roles) és [szerepkör-engedélyek](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) című szakaszban talál további információt. Ha a fiókja hozzá van rendelve a felhasználói szerepkörhöz, de az alkalmazás regisztrációs beállítása a rendszergazda felhasználókra korlátozódik, kérje meg a rendszergazdát, hogy rendeljen hozzá egy olyan rendszergazdai szerepkört, amely az alkalmazások regisztrálásának minden aspektusát létrehozhatja és kezelheti, illetve lehetővé teheti a felhasználók számára, hogy regisztrálják az alkalmazásokat.

### <a name="check-azure-subscription-permissions"></a>Azure-előfizetési engedélyek keresése

Az Azure-előfizetésében a fióknak `Microsoft.Authorization/*/Write` hozzáféréssel kell rendelkeznie ahhoz, hogy szerepkört rendeljen egy ad-alkalmazáshoz. Ezt a műveletet a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) szerepkör vagy a [Felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör végezheti el. Ha a fiókja hozzá van rendelve a **közreműködő** szerepkörhöz, nincs megfelelő engedélye. Hibaüzenet jelenik meg, amikor az egyszerű szolgáltatás szerepkört rendeli hozzá.

Az előfizetési engedélyek ellenőrzését:

1. Keresse meg és válassza ki az **előfizetéseket**, vagy válassza az **előfizetések** lehetőséget a **kezdőlapon** .

   ![Keresés](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni az egyszerű szolgáltatásnevet.

   ![Előfizetés kiválasztása hozzárendeléshez](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetést, válassza a **globális előfizetések szűrőt**. Győződjön meg arról, hogy a portálon a kívánt előfizetés van kiválasztva.

1. Válassza **a saját engedélyek**lehetőséget. Ezután **kattintson ide az előfizetés teljes hozzáférés részleteinek megtekintéséhez**.

   ![Válassza ki azt az előfizetést, amelyben létre kívánja hozni a szolgáltatásnevet a következőben:](./media/howto-create-service-principal-portal/view-details.png)

1. Válassza ki a **szerepkör-hozzárendelések** **nézet** elemet a hozzárendelt szerepkörök megtekintéséhez, és állapítsa meg, hogy rendelkezik-e megfelelő engedélyekkel ahhoz, hogy szerepkört rendeljen egy ad-alkalmazáshoz. Ha nem, kérje meg az előfizetés rendszergazdáját, hogy vegye fel Önt a felhasználói hozzáférés rendszergazdai szerepkörbe. A következő ábrán a felhasználó hozzárendeli a tulajdonosi szerepkört, ami azt jelenti, hogy a felhasználó rendelkezik a megfelelő engedélyekkel.

   ![Ez a példa azt mutatja be, hogy a felhasználó hozzá van rendelve a tulajdonosi szerepkörhöz](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>További lépések

* A biztonsági szabályzatok megadásával kapcsolatos információkért lásd: [Azure szerepköralapú Access Control](../../role-based-access-control/role-assignments-portal.md).  
* A felhasználók számára megadható vagy megtagadható elérhető műveletek listáját itt tekintheti meg: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../../role-based-access-control/resource-provider-operations.md).
