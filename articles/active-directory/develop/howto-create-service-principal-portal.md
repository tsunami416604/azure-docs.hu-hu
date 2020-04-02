---
title: Azure AD-alkalmazás létrehozása & egyszerű szolgáltatás a portálon
titleSuffix: Microsoft identity platform
description: Hozzon létre egy új Azure Active Directory-alkalmazást, & egyszerű szolgáltatást, amely az erőforrásokhoz való hozzáférést az Azure Resource Manager szerepköralapú hozzáférés-vezérléssel kezeli.
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
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546136"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Útmutató: A portál használatával hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatást, amely képes hozzáférni az erőforrásokhoz

Ez a cikk bemutatja, hogyan hozhat létre egy új Azure Active Directory (Azure AD) alkalmazást és egyszerű szolgáltatás, amely használható a szerepköralapú hozzáférés-vezérlés. Ha olyan kóddal rendelkezik, amelynek erőforrásokat kell elérnie vagy módosítania kell, létrehozhat egy identitást az alkalmazáshoz. Ezt az identitást szolgáltatásnévnek nevezzük. Ezután hozzárendelheti a szükséges engedélyeket a szolgáltatásnévhez. Ez a cikk bemutatja, hogyan használhatja a portált az egyszerű szolgáltatás létrehozásához. Egy egybérlős alkalmazásra összpontosít, ahol az alkalmazás csak egy szervezeten belül fut. Általában egy-bérlős alkalmazásokat használ a szervezeten belül futó üzletági alkalmazásokhoz.

> [!IMPORTANT]
> Egyszerű szolgáltatás létrehozása helyett fontolja meg felügyelt identitások használata az Azure-erőforrások az alkalmazás identitásához. Ha a kód fut egy szolgáltatás, amely támogatja a felügyelt identitások és az Azure AD-hitelesítést támogató erőforrások eléréséhez, felügyelt identitások egy jobb választás az Ön számára. Ha többet szeretne megtudni az Azure-erőforrások felügyelt identitásairól, beleértve azt is, hogy jelenleg mely szolgáltatások támogatják, olvassa el [a Mi az Azure-erőforrások felügyelt identitásai.](../managed-identities-azure-resources/overview.md)

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory-alkalmazás létrehozása

Ugorjunk bele az identitás megteremtésébe. Ha problémába ütközik, ellenőrizze a [szükséges engedélyeket,](#required-permissions) és győződjön meg arról, hogy a fiók létre tudja hozni az identitást.

1. Jelentkezzen be Azure-fiókjába az [Azure Portalon](https://portal.azure.com)keresztül.
1. Válassza az **Azure Active Directory**lehetőséget.
1. Válassza **az Alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Új regisztráció lehetőséget.**
1. Nevezze el az alkalmazást. Válasszon egy támogatott fióktípust, amely meghatározza, hogy ki használhatja az alkalmazást. Az **URI átirányítása**csoportban válassza a **Web** lehetőséget a létrehozni kívánt alkalmazástípushoz. Adja meg azt az URI-t, ahová a hozzáférési jogkivonatot küldik. Natív [alkalmazáshoz](../manage-apps/application-proxy-configure-native-client-application.md)nem hozhat létre hitelesítő adatokat. Ezt a típust nem használhatja automatikus alkalmazáshoz. Az értékek beállítása után válassza a **Regisztráció**lehetőséget.

   ![Írja be az alkalmazás nevét](./media/howto-create-service-principal-portal/create-app.png)

Az Azure AD-alkalmazást és az egyszerű szolgáltatást hozta létre.

## <a name="assign-a-role-to-the-application"></a>Szerepkör hozzárendelése az alkalmazáshoz

Az előfizetés erőforrásainak eléréséhez hozzá kell rendelnie egy szerepkört az alkalmazáshoz. Döntse el, hogy melyik szerepkör biztosítja a megfelelő engedélyeket az alkalmazáshoz. A rendelkezésre álló szerepkörökről az [RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md)című témakörben olvashat.

A hatókört az előfizetés, az erőforráscsoport vagy az erőforrás szintjén állíthatja be. Az engedélyek alacsonyabb hatókörszintekre öröklődnek. Ha például egy alkalmazást hozzáad egy erőforráscsoport Olvasó szerepköréhez, az azt jelenti, hogy képes olvasni az erőforráscsoportot és a benne lévő erőforrásokat.

1. Az Azure Portalon válassza ki az alkalmazás hozzárendeléséhez kívánt hatókör szintjét. Ha például szerepkört szeretne hozzárendelni az előfizetéshatókörhöz, keresse meg és válassza **az Előfizetések**lehetőséget, vagy válassza az **Előfizetések** lehetőséget a **Kezdőlapon.**

   ![Például rendeljen hozzá egy szerepkört az előfizetéshatókörben](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki azt az előfizetést, amelyhez az alkalmazást hozzá kívánja rendelni.

   ![Előfizetés kiválasztása hozzárendeléshez](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetést, válassza a **globális előfizetések szűrőt.** Győződjön meg arról, hogy a kívánt előfizetés ki van jelölve a portálhoz.

1. Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.**
1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
1. Válassza ki az alkalmazáshoz rendelni kívánt szerepkört. Ha például engedélyezni szeretné, hogy az alkalmazás olyan műveleteket hajtson végre, mint **az újraindítás**, **indítsa el** és állítsa **le** a példányokat, válassza ki a **Közreműködői** szerepkört.  További információ a [rendelkezésre álló szerepkörök](../../role-based-access-control/built-in-roles.md) alapértelmezés szerint az Azure AD-alkalmazások nem jelennek meg a rendelkezésre álló lehetőségeket. Az alkalmazás megkereséséhez keresse meg a nevet, és jelölje ki.

   ![Az alkalmazáshoz rendelő szerepkör kiválasztása](./media/howto-create-service-principal-portal/select-role.png)

1. A szerepkör hozzárendelésének befejezéséhez válassza a **Mentés** lehetőséget. Az alkalmazás az adott hatókörszerepkörrel rendelkező felhasználók listájában jelenik meg.

Az egyszerű szolgáltatás beállítása. Elkezdheti használni a parancsfájlok vagy alkalmazások futtatásához. A következő szakasz bemutatja, hogyan szerezheti be a programozott bejelentkezéshez szükséges értékeket.

## <a name="get-values-for-signing-in"></a>A bejelentkezéshez írt értékek beszerezése

Ha programozott módon bejelentkezik, át kell adnia a bérlői azonosítót a hitelesítési kérelemmel. Az alkalmazás azonosítójára és egy hitelesítési kulcsra is szüksége van. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. Válassza az **Azure Active Directory**lehetőséget.
1. Az **Azure AD-ben az alkalmazásregisztrációk** közül válassza ki az alkalmazást.
1. Másolja a címtár (bérlői) azonosítót, és tárolja azt az alkalmazáskódjában.

    ![A könyvtár másolása (bérlőazonosító) és tárolása az alkalmazáskódban](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában.

   ![Az alkalmazás (ügyfél) azonosítójának másolása](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Tanúsítványok és titkok
A démonalkalmazások két féle hitelesítő adatot használhatnak az Azure AD-vel való hitelesítéshez: tanúsítványokat és alkalmazástitkokat.  Tanúsítvány használatát javasoljuk, de létrehozhat egy új alkalmazástitkos kulcsot is.

### <a name="upload-a-certificate"></a>Tanúsítvány feltöltése

Meglévő tanúsítványt is használhat, ha rendelkezik ilyensel.  Szükség esetén önaláírt tanúsítványt is létrehozhat *tesztelési célokra.* Nyissa meg a PowerShellt, és futtassa az [Új-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) tanúsítványt a következő paraméterekkel, hogy önaláírt tanúsítványt hozzon létre a számítógép felhasználói tanúsítványtárolójában: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportálja ezt a tanúsítványt egy fájlba a Windows Vezérlőpultjáról elérhető [Felhasználói tanúsítvány kezelése](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC beépülő modullal.

1. Válassza a **Start** menü **Futtatás** parancsát, majd írja be a **certmgr.msc parancsot.**

   Megjelenik az aktuális felhasználó Tanúsítványkezelő eszköze.

1. A tanúsítványok megtekintéséhez a bal oldali ablaktábla **Tanúsítványok – Aktuális felhasználó területén** bontsa ki a **Személyes** könyvtárat.
1. Kattintson a jobb gombbal a létrehozott tanúsítványra, és válassza **az Összes feladat >exportálása parancsot.**
1. Kövesse a Tanúsítványexportálás varázslót.  Ne exportálja a személyes kulcsot, és ne exportálja a programba. CER fájlt.

A tanúsítvány feltöltése:

1. Válassza az **Azure Active Directory**lehetőséget.
1. Az **Azure AD-ben az alkalmazásregisztrációk** közül válassza ki az alkalmazást.
1. Válassza **a Tanúsítványok & titkos kulcsok lehetőséget.**
1. Válassza **a Tanúsítvány feltöltése** lehetőséget, és válassza ki a tanúsítványt (meglévő vagy exportált önaláírt tanúsítványt).

    ![Válassza a Tanúsítvány feltöltése lehetőséget, és válassza ki a hozzáadni kívánt tanúsítványt.](./media/howto-create-service-principal-portal/upload-cert.png)

1. Válassza a **Hozzáadás** lehetőséget.

Miután regisztrálta a tanúsítványt az alkalmazásregisztrációs portálon, engedélyeznie kell az ügyfélalkalmazás kódját a tanúsítvány használatához.

### <a name="create-a-new-application-secret"></a>Új alkalmazástitok létrehozása

Ha úgy dönt, hogy nem használ tanúsítványt, létrehozhat egy új alkalmazástitkos kulcsot.

1. Válassza **a Tanúsítványok & titkos kulcsok lehetőséget.**
1. Válassza **ki az ügyféltitkokat -> Új ügyféltitok**.
1. Adja meg a titkos kulcsot és az időtartamot. Ha végzett, válassza **a Hozzáadás**lehetőséget.

   Az ügyféltitkos lista mentése után megjelenik az ügyféltitkos lista értéke. Másolja ezt az értéket, mert később nem tudja beolvasni a kulcsot. A kulcs értéket az alkalmazásazonosítóval együtt adja meg, hogy jelentkezzen be az alkalmazásként. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![A titkos érték másolása, mert később nem lehet beolvasni](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Hozzáférési házirendek konfigurálása erőforrásokon
Ne feledje, hogy előfordulhat, hogy be kell állítania az alkalmazás által elérni kívánt erőforrások összeadási engedélyeit. Például frissítenie kell [a key vault hozzáférési szabályzatait,](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) hogy az alkalmazás hozzáférést biztosítson a kulcsokhoz, titkos kulcsokhoz vagy tanúsítványokhoz.  

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a kulcstartót, és válassza az **Access-szabályzatokat.**  
1. Válassza **a Hozzáférési házirend hozzáadása**lehetőséget, majd jelölje ki az alkalmazásnak megadni kívánt kulcs-, titkos- és tanúsítványengedélyeket.  Válassza ki a korábban létrehozott egyszerű szolgáltatást.
1. Válassza a **Hozzáadás** lehetőséget a hozzáférési házirend hozzáadásához, majd a **Mentés gombra** a módosítások véglegesítéséhez.
    ![Hozzáférési házirend hozzáadása](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Szükséges engedélyek

Megfelelő engedélyekkel kell rendelkeznie ahhoz, hogy regisztrálhasson egy alkalmazást az Azure AD-bérlővel, és az alkalmazáshoz szerepkört kell rendelnie az Azure-előfizetésben.

### <a name="check-azure-ad-permissions"></a>Az Azure AD-engedélyek ellenőrzése

1. Válassza az **Azure Active Directory**lehetőséget.
1. Jegyezze fel a szerepét. Ha **felhasználói** szerepkört rendelkezik, győződjön meg arról, hogy a nem rendszergazdák regisztrálhatnak alkalmazásokat.

   ![Találd meg a szereped. Felhasználó ként győződjön meg arról, hogy a nem rendszergazdák regisztrálhatnak alkalmazásokat](./media/howto-create-service-principal-portal/view-user-info.png)

1. A bal oldali ablaktáblában válassza a **Felhasználói beállítások lehetőséget.**
1. Ellenőrizze az **alkalmazásregisztrációk beállítását.** Ezt az értéket csak rendszergazda állíthatja be. Ha az Azure AD-bérlő bármely felhasználója **igen,** regisztrálhat egy alkalmazást.

Ha az alkalmazásregisztrációk beállítása **Nem,** csak rendszergazdai szerepkörrel rendelkező felhasználók regisztrálhatnak ilyen típusú alkalmazásokat. Tekintse meg [a rendelkezésre álló szerepkörök](../users-groups-roles/directory-assign-admin-roles.md#available-roles) és [szerepkör-engedélyek](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) az elérhető rendszergazdai szerepkörök és az azure AD-ben az egyes szerepköröknek adott engedélyek megismeréséhez. Ha a fiók felhasználói szerepkörrel rendelkezik, de az alkalmazásregisztrációs beállítás rendszergazdai felhasználókra korlátozódik, kérje meg a rendszergazdát, hogy rendeljen hozzá egy olyan rendszergazdai szerepkört, amely az alkalmazásregisztrációk minden aspektusát létrehozhatja és kezelheti, vagy hogy a felhasználók regisztrálhassák az alkalmazásokat.

### <a name="check-azure-subscription-permissions"></a>Az Azure-előfizetési engedélyek ellenőrzése

Az Azure-előfizetésben a `Microsoft.Authorization/*/Write` fióknak hozzáféréssel kell rendelkeznie ahhoz, hogy szerepkört rendeljen egy AD-alkalmazáshoz. Ezt a műveletet a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) szerepkör vagy a [Felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör végezheti el. Ha fiókja **közreműködői** szerepkörhöz van rendelve, akkor nincs megfelelő engedélye. Hibaüzenet et fog kapni, amikor megpróbálja hozzárendelni a szolgáltatásnév szerepkört.

Az előfizetési engedélyek ellenőrzése:

1. Keressen rá az **Előfizetések**elemre, vagy válassza az **Előfizetések** lehetőséget a **Kezdőlapon.**

   ![Keresés](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki azt az előfizetést, amelyben létre szeretné hozni a szolgáltatásegyszerűt.

   ![Előfizetés kiválasztása hozzárendeléshez](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetést, válassza a **globális előfizetések szűrőt.** Győződjön meg arról, hogy a kívánt előfizetés ki van jelölve a portálhoz.

1. Válassza **a Saját engedélyek lehetőséget.** Ezután válassza **a Kattintson ide lehetőséget az előfizetés teljes hozzáférési adatainak megtekintéséhez.**

   ![Válassza ki azt az előfizetést, amelyben létre szeretné hozni a szolgáltatásegyszerűt.](./media/howto-create-service-principal-portal/view-details.png)

1. Válassza a **Nézet** **szerepkör-hozzárendelésekben** lehetőséget a hozzárendelt szerepkörök megtekintéséhez, és annak meghatározásához, hogy rendelkezik-e megfelelő engedélyekkel ahhoz, hogy szerepkört rendeljen egy AD-alkalmazáshoz. Ha nem, kérje meg az előfizetés rendszergazdáját, hogy vegye fel a Felhasználói hozzáférés rendszergazdája szerepkörbe. A következő képen a felhasználó a Tulajdonos szerepkört rendeli hozzá, ami azt jelenti, hogy a felhasználó megfelelő engedélyekkel rendelkezik.

   ![Ez a példa azt mutatja, hogy a felhasználó tulajdonosi szerepkört kapott](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>További lépések

* A biztonsági házirendek megadásáról az [Azure szerepköralapú hozzáférés-vezérlés című témakörben](../../role-based-access-control/role-assignments-portal.md)olvashat.  
* A felhasználók számára megadható vagy megtagadható elérhető műveletek listáját az [Azure Resource Manager erőforrás-szolgáltató műveletei ben kaphatja meg.](../../role-based-access-control/resource-provider-operations.md)
