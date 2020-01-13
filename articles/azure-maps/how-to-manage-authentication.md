---
title: Hitelesítés kezelése | Microsoft Azure térképek
description: A Azure Portal a Microsoft Azure Maps-ben történő hitelesítés kezelésére használható.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1a38c970f3c9fa5b90032f5816f8e541b305531c
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911549"
---
# <a name="manage-authentication-in-azure-maps"></a>Hitelesítés kezelése Azure Maps

Azure Maps fiók létrehozása után létrejön egy ügyfél-azonosító és-kulcs, amely támogatja az Azure Active Directory (Azure AD) vagy a megosztott kulcsos hitelesítést.

## <a name="view-authentication-details"></a>Hitelesítés részleteinek megtekintése

A Azure Maps fiók létrehozását követően létrejön az elsődleges és a másodlagos kulcs. A [megosztott kulcsos hitelesítés](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)használata esetén ajánlott az elsődleges kulcs használata előfizetési kulcsként használni Azure maps. A másodlagos kulcs olyan helyzetekben használható, mint például a kulcsok változásai. További információ: [hitelesítés Azure Maps](https://aka.ms/amauth)használatával.

A hitelesítési adatokat a Azure Portalon tekintheti meg. Nyissa meg a fiókját, és válassza a **hitelesítés** lehetőséget a **Beállítások** menüben.

![Hitelesítés részletei](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="set-up-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrációjának beállítása

Azure Maps fiók létrehozása után létre kell hoznia egy hivatkozást az Azure AD-bérlő és a Azure Maps erőforrás között.

1. Nyissa meg az Azure AD panelt, és hozzon létre egy alkalmazást. Adja meg a regisztráció nevét. A **bejelentkezési URL-cím** mezőben adja meg a webalkalmazás/API kezdőlapját (például https:\//localhost/). Ha már rendelkezik regisztrált alkalmazással, folytassa a 2. lépéssel.

    ![Alkalmazásregisztráció](./media/how-to-manage-authentication/app-registration.png)

    ![Alkalmazás regisztrációjának részletei](./media/how-to-manage-authentication/app-create.png)

2. Ha delegált API-engedélyeket szeretne hozzárendelni Azure Mapshoz, nyissa meg az alkalmazást a **Alkalmazásregisztrációk**területen, majd válassza a **Beállítások**lehetőséget.  Válassza a **szükséges engedélyek**lehetőséget, majd válassza a **Hozzáadás**lehetőséget. Keresse meg és válassza ki **Azure Maps** az **API kiválasztása**területen, majd kattintson a **kiválasztás** gombra.

    ![Alkalmazás API-engedélyei](./media/how-to-manage-authentication/app-permissions.png)

3. Az **engedélyek kiválasztása**területen válassza a **hozzáférés Azure Maps**elemet, majd kattintson a **kiválasztás** gombra.

    ![Alkalmazás API-engedélyeinek kiválasztása](./media/how-to-manage-authentication/select-app-permissions.png)

4. A hitelesítési módszertől függően végezze el az a vagy a b lépést.

    1. Ha az alkalmazás felhasználói tokenes hitelesítést használ a Azure Maps web SDK-val, engedélyezze a `oauthEnableImplicitFlow`t az alkalmazás regisztrációjának részletei oldal jegyzékfájl szakaszában az igaz értékre állítva.
    
       ![Alkalmazás jegyzékfájlja](./media/how-to-manage-authentication/app-manifest.png)

    2. Ha az alkalmazás kiszolgálói/alkalmazás-hitelesítést használ, lépjen a **kulcsok** panelre az alkalmazás regisztrálása lapon, vagy hozzon létre egy jelszót, vagy töltsön fel egy nyilvános kulcsú tanúsítványt az alkalmazás regisztrálásához. Ha jelszót hoz létre, a **Mentés**gombra kattintva másolja a jelszót később, és tárolja biztonságosan. Ezt a jelszót fogja használni a jogkivonatok Azure AD-ből való beszerzéséhez.

       ![Alkalmazás kulcsai](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>RBAC megadása Azure Maps

Miután hozzárendelt egy Azure Maps fiókot az Azure AD-bérlőhöz, hozzáférés-vezérlést adhat hozzá egy felhasználóhoz, csoporthoz vagy alkalmazáshoz egy vagy több Azure Maps hozzáférés-vezérlési szerepkörhöz.

1. Lépjen a **hozzáférés-vezérlés (iam)** elemre, válassza a **szerepkör-hozzárendelések**lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása**elemet.

    ![RBAC megadása](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. A **szerepkör-hozzárendelés hozzáadása** ablakban a **szerepkör**területen válassza a **Azure Maps Date Reader (előzetes verzió)** lehetőséget. A **hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**lehetőséget. A **kiválasztás**területen válassza ki a felhasználót vagy az alkalmazást. Kattintson a **Mentés** gombra.

    ![Szerepkör-hozzárendelés hozzáadása](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Elérhető Azure Maps RBAC szerepköreinek megtekintése

Ha Azure Maps számára elérhető szerepköralapú hozzáférés-vezérlési (RBAC) szerepköröket szeretné megtekinteni, lépjen a **hozzáférés-vezérlés (iam)** elemre, válassza a **szerepkörök**lehetőséget, majd keresse meg a **Azure Mapstól**kezdődő szerepköröket. Ezek azok a szerepkörök, amelyekhez hozzáférést biztosíthat.

![Elérhető szerepkörök megtekintése](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure Maps RBAC megtekintése

A RBAC részletes hozzáférés-vezérlést biztosít.

A Azure Maps RBAC kapott felhasználók és alkalmazások megtekintéséhez lépjen a **Access Control (iam)** elemre, válassza a szerepkör- **hozzárendelések**lehetőséget, majd a szűrés a **Azure Maps**alapján.

![A RBAC által biztosított felhasználók és alkalmazások megtekintése](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps igénylési jogkivonatai

Miután regisztrálta az alkalmazást, és hozzárendelte Azure Mapshoz, hozzáférési jogkivonatokat kérhet le.

* Ha az alkalmazás felhasználói tokenes hitelesítést használ a Azure Maps web SDK-val, a HTML-lapot a Azure Maps ügyfél-AZONOSÍTÓval és az Azure AD-alkalmazás azonosítójával kell konfigurálnia.

* Ha az alkalmazás kiszolgálói/alkalmazás-hitelesítést használ, akkor az Azure ad-jogkivonat végpontjának `https://login.microsoftonline.com` kell igényelnie az Azure AD erőforrás-azonosító `https://atlas.microsoft.com/`, az Azure Maps ügyfél-azonosító, az Azure AD-alkalmazás azonosítója és az Azure AD-alkalmazás regisztrációs jelszava vagy tanúsítványa alapján.

| Azure-környezet   | Azure AD-jogkivonat végpontja | Azure-erőforrás azonosítója |
| --------------------|-------------------------|-------------------|
| Azure Public        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

További információ az Azure AD-től a felhasználók és az egyszerű szolgáltatások hozzáférési jogkivonatok igénylésével kapcsolatban: [hitelesítési forgatókönyvek az Azure ad-hez](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Következő lépések

Az Azure AD-hitelesítéssel és a Azure Maps web SDK-val kapcsolatos további tudnivalókért tekintse meg az [Azure ad-t és a Azure Maps web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)-t.

Ismerje meg, hogyan tekintheti meg Azure Maps fiókjának API-használati metrikáit:
> [!div class="nextstepaction"] 
> [Használati metrikák megtekintése](how-to-view-api-usage.md)

A Azure Active Directory (HRE) Azure Maps használatával történő integrálását bemutató minták listáját a következő témakörben tekintheti meg:

> [!div class="nextstepaction"]
> [Azure AD-hitelesítési minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)