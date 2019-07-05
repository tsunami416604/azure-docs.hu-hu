---
title: Azure-alkalmazáshoz identitás létrehozása a portálon |} A Microsoft Docs
description: Ismerteti, hogyan hozzon létre egy új Azure Active Directory-alkalmazás és egyszerű szolgáltatás, amely a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager-erőforrásokhoz való hozzáférés kezelésére használható.
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
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd1534b3f966051104a3f3ee389fb047ab258fc
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482812"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Útmutató: Az Azure AD-alkalmazás és -erőforrások elérésére képes egyszerű szolgáltatás létrehozása a portál használatával

Ez a cikk bemutatja, hogyan hozhat létre egy új Azure Active Directory (Azure AD-) alkalmazás és a szerepköralapú hozzáférés-vezérléssel használható egyszerű szolgáltatást. Ha rendelkezik, amelyet eléréséhez, vagy módosítsa erőforrások, az alkalmazáshoz tartozó identitást hozhat létre. Ezt az identitást szolgáltatásnévnek nevezzük. A szolgáltatásnévnek majd hozzárendelheti a szükséges engedélyekkel. Ez a cikk bemutatja, hogyan az egyszerű szolgáltatás létrehozása a portál használatával. Egy egybérlős alkalmazást, ahol az alkalmazás futtatásához csak egy szervezeten belül célja összpontosít. Általában használnak a egybérlős alkalmazások az üzleti alkalmazások futtatására a szervezeten belül.

> [!IMPORTANT]
> Egyszerű szolgáltatás létrehozása helyett fontolja meg a felügyelt identitások használatával az Azure-erőforrások esetében az alkalmazás azonosítóját. A kód egy szolgáltatás, amely támogatja a felügyelt identitások és hozzáférések erőforrások, amelyek támogatják az Azure AD-hitelesítés fut, felügyelt identitások-e az Ön számára jobb megoldás. További információ az Azure-erőforrásokhoz, mely szolgáltatások jelenleg támogatja, beleértve a felügyelt identitásokból [Mi az Azure-erőforrások felügyelt identitások?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Az Azure Active Directory-alkalmazás létrehozása

Nyissa meg közvetlenül az identitás létrehozása. Ha problémát tapasztal, ellenőrizze a [szükséges engedélyek](#required-permissions) , hogy a fiók képes létrehozni az identitást.

1. Jelentkezzen be az Azure-fiók révén a [az Azure portal](https://portal.azure.com).
1. Válassza az **Azure Active Directory** elemet.
1. Válassza az **Alkalmazásregisztrációk** elemet.
1. Válassza ki **új regisztrációs**.
1. Nevezze el az alkalmazást. Válasszon ki egy támogatott fiókot írja be, amely meghatározza, hogy ki használhatja az alkalmazást. Alatt **átirányítási URI-t**válassza **webes** szeretne létrehozni az alkalmazás számára. Adja meg az URI-t, ha a hozzáférési jogkivonatot a érkezik. Hitelesítő adatok nem hozható létre egy [natív alkalmazás](../manage-apps/application-proxy-configure-native-client-application.md). Egy automatikus alkalmazásnak típus nem használható. Miután beállította az értékeket, válassza ki a **regisztrálása**.

   ![Adjon meg egy nevet az alkalmazáshoz](./media/howto-create-service-principal-portal/create-app.png)

Az Azure AD-alkalmazás és egyszerű szolgáltatás létrehozott.

## <a name="assign-the-application-to-a-role"></a>Alkalmazások szerepkörhöz rendeléséhez

Az előfizetésben lévő erőforrások eléréséhez, hozzá kell rendelnie az alkalmazás egy szerepkörhöz. Döntse el, milyen szerepkört kínál az alkalmazás a megfelelő engedélyekkel. Az elérhető szerepkörök kapcsolatos további információkért lásd: [RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md).

Beállíthatja a hatókör szintjén is az előfizetés, erőforráscsoport vagy erőforrás. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Például egy alkalmazás az Olvasó szerepkörhöz, egy erőforráscsoport hozzáadása azt jelenti, hogy azt az erőforráscsoportot és az összes benne található erőforrást olvashatja.

1. Keresse meg a hatókör az alkalmazást hozzárendelni kívánt szintjét. Válassza ki például az előfizetések szintjén szerepkör hozzárendelése **minden szolgáltatás** és **előfizetések**.

   ![Ha például a az előfizetések szintjén szerepkör hozzárendelése](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki az adott előfizetés hozzárendelése az alkalmazást.

   ![Válasszon hozzárendelés előfizetést](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetéshez, válasszon **globális előfizetés-szűrő**. Ellenőrizze, hogy az előfizetés ki van jelölve a portálon.

1. Válassza ki **hozzáférés-vezérlés (IAM)** .
1. Válassza ki **szerepkör-hozzárendelés hozzáadása**.
1. Válassza ki a az alkalmazáshoz hozzárendelni kívánt szerepkört. Hajtsa végre a műveleteket, például az alkalmazás számára **újraindítás**, **start** és **leállítása** példányok, válassza ki a **közreműködői** szerepkör. Alapértelmezés szerint az Azure AD-alkalmazások nem megjelenik az elérhető lehetőségek közül. Keresse meg az alkalmazás, keresse meg a nevét, és jelölje ki.

   ![Válassza ki a szerepkört az alkalmazás hozzárendelése](./media/howto-create-service-principal-portal/select-role.png)

1. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. Láthatja, hogy az alkalmazás a felhasználók az adott hatókörnél szerepköre a listában.

Az egyszerű szolgáltatás be van állítva. Akkor megkezdheti a parancsfájlok vagy alkalmazások futtatásához. Ez a szakasz bemutatja, hogyan használatával lekérjük az értékeket, amelyek szükségesek, amikor programozott módon jelentkezik be.

## <a name="get-values-for-signing-in"></a>Bejelentkezés értékek beolvasása

Ha programozott módon jelentkezik be, kell átadni a bérlő Azonosítóját, a hitelesítési kérelemmel együtt. Az alkalmazás és a hitelesítési kulcs Azonosítóját is szükséges. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. Válassza az **Azure Active Directory** elemet.
1. A **alkalmazásregisztrációk** az Azure AD-ben válassza ki az alkalmazását.
1. A könyvtár (bérlő) azonosítóját, és tárolja az alkalmazás kódjában.

    ![Másolja a könyvtárat (bérlő azonosító), és tárolja a kód](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában.

   ![Másolja az Alkalmazásazonosítót (ügyfél)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Tanúsítványok és titkos kulcsok
Démon alkalmazások használhatják a kétféle hitelesítő adatot az Azure AD-hitelesítést: tanúsítványok és titkos alkalmazáskulcsok.  Javasoljuk, hogy olyan tanúsítványt használ, de is létrehozhat egy új Alkalmazáskulcs.

### <a name="upload-a-certificate"></a>Tanúsítvány feltöltése

Egy létező tanúsítványt is használhatja, ha rendelkezik ilyennel.  Igény szerint hozhat létre egy önaláírt tanúsítványt tesztelési célokra. Nyissa meg a Powershellt, és futtassa [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) önaláírt tanúsítvány létrehozása a felhasználó tanúsítványtárolójában a számítógépen a következő paraméterekkel: `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`.  A tanúsítvány használatával történő exportálás a [felhasználói tanúsítvány kezelése](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC beépülő modul a Windows Vezérlőpult érhető el.

A tanúsítvány feltöltéséhez:

1. Válassza ki **tanúsítványok és titkos kulcsok**.
1. Válassza ki **tanúsítvány feltöltése** , és válassza ki a tanúsítványt (meglévő tanúsítványt vagy önaláírt tanúsítványt, az exportált).

    ![Válassza ki a tanúsítvány feltöltése, és válassza ki a hozzáadni kívánt](./media/howto-create-service-principal-portal/upload-cert.png)

1. Válassza a **Hozzáadás** lehetőséget.

Miután felvette a tanúsítványt az alkalmazásregisztrációs portálon az alkalmazását, a tanúsítvány használatára az ügyfél alkalmazáskód engedélyeznie kell.

### <a name="create-a-new-application-secret"></a>Hozzon létre egy új Alkalmazáskulcs

Ha nem kíván használni egy tanúsítványt, létrehozhat egy új Alkalmazáskulcs.

1. Válassza ki **tanúsítványok és titkos kulcsok**.
1. Válassza ki **ügyfél titkos kódok -> Új titkos ügyfélkulcsot**.
1. Adjon meg egy leírást a titkos kulcsot és egy időtartamot. Ha elkészült, válassza **Hozzáadás**.

   Az ügyfél titkos kulcsát a mentés után jelenik meg a titkos ügyfélkulcsot értékét. Másolja ezt az értéket, mert nem sikerült beolvasni a kulcsot később. A kulcs értékét az alkalmazás azonosítójával jelentkezzen be az alkalmazást, hogy adja meg. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![Másolja a titkos értéket, mert nem lehet lekérdezni a ez később](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Szükséges engedélyek

Rendelkezik megfelelő engedélyekkel alkalmazások regisztrációjához az Azure AD-bérlőhöz, és alkalmazások szerepkörhöz rendeléséhez az Azure-előfizetésében.

### <a name="check-azure-ad-permissions"></a>Ellenőrizze az Azure AD-engedélyekről

1. Válassza az **Azure Active Directory** elemet.
1. Megjegyzés: a szerepkör. Ha rendelkezik a **felhasználói** szerepkör, győződjön meg arról, hogy a nem rendszergazda felhasználók regisztrálhatnak alkalmazásokat.

   ![Keresse meg a szerepkört. Ha egy felhasználó, győződjön meg arról, nem rendszergazda jogosultságú alkalmazások akkor regisztrálhatnak.](./media/howto-create-service-principal-portal/view-user-info.png)

1. Válassza ki **felhasználói beállítások**.
1. Ellenőrizze a **alkalmazásregisztrációk** beállítás. Ez az érték csak egy rendszergazda állítható. Ha beállítása **Igen**, az Azure AD-bérlőben a felhasználói regisztrálhatnak egy alkalmazást.

Ha az alkalmazásregisztrációk beállítás értéke **nem**, csak a rendszergazdai szerepkörrel rendelkező felhasználók regisztrálhatnak ilyen típusú alkalmazásokat. Lásd: [elérhető szerepkörök](../users-groups-roles/directory-assign-admin-roles.md#available-roles) és [szerepköri jogosultságok](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) elérhető rendszergazdai szerepköröket és engedélyeket az Azure ad-ben minden egyes szerepkörhöz megadott ismerteti. Ha a fiók hozzá van rendelve a felhasználói szerepkörhöz, de az alkalmazás regisztrációs beállítás korlátozott rendszergazdai jogosultságú felhasználókhoz, kérje meg a rendszergazdát, hogy akár hozzárendelése egy hozhat létre és kezelhet minden aspektusára alkalmazásregisztrációk vagy engedélyezése a felhasználók számára a rendszergazdai szerepköre alkalmazások regisztrálása.

### <a name="check-azure-subscription-permissions"></a>Azure-előfizetés engedélyek ellenőrzése

Az Azure-előfizetésében, a fióknak rendelkeznie kell `Microsoft.Authorization/*/Write` AD-alkalmazás hozzárendelése szerepkörhöz való hozzáférést. Ezt a műveletet a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) szerepkör vagy a [Felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör végezheti el. Ha a fiók hozzá van rendelve a **közreműködői** szerepkör nem rendelkezik megfelelő engedéllyel. Az egyszerű szolgáltatás hozzárendelése szerepkörhöz megkísérlésekor hibaüzenetet kap.

Ellenőrizze előfizetése engedélyei között:

1. Válassza ki a fiók jobb felső sarokban, majd válassza ki **… -> saját engedélyek**.

   ![Válassza ki a fiókot és a felhasználói engedélyek](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. A legördülő listából válassza ki a kívánt előfizetést, az egyszerű szolgáltatás létrehozásához. Ezután válassza ki **teljes hozzáférési megtekintéséhez kattintson ide az előfizetés részletei**.

   ![Az egyszerű szolgáltatásnév létrehozása a kívánt előfizetés kiválasztásához](./media/howto-create-service-principal-portal/view-details.png)

1. Válassza ki **szerepkör-hozzárendelések** a hozzárendelt szerepkörök megtekintése, és határozza meg, hogy rendelkezik-e megfelelő engedélyekkel AD-alkalmazás hozzárendelése szerepkörhöz. Ha nem, kérje meg az előfizetés adminisztrátorát, hogy vegye fel Önt a felhasználói hozzáférés rendszergazdájának szerepköre. Az alábbi ábrán a felhasználó a tulajdonos szerepkör, ami azt jelenti, hogy a felhasználó rendelkezik megfelelő engedélyekkel van rendelve.

   ![Ez a példa bemutatja, hogy a felhasználó a tulajdonosi szerepkör van rendelve.](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>További lépések

* Több-bérlős alkalmazás beállításával kapcsolatban lásd: [fejlesztői útmutatója az Azure Resource Manager API-val engedélyezési](../../azure-resource-manager/resource-manager-api-authentication.md).
* Biztonsági szabályzatok megadásával kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md).  
* Rendelkezésre álló műveletek kapnak, vagy nem jogosult felhasználók listáját lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../../role-based-access-control/resource-provider-operations.md).
