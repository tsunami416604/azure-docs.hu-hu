---
title: Hitelesítés kezelése | Microsoft Azure térképek
description: A Azure Portal a Microsoft Azure Maps-ben történő hitelesítés kezelésére használható.
author: walsehgal
ms.author: v-musehg
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1f7f128898089292a8ccd92686af5d68fe328f3c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766092"
---
# <a name="manage-authentication-in-azure-maps"></a>Hitelesítés kezelése Azure Maps

Azure Maps fiók létrehozása után létrejön egy ügyfél-azonosító és egy kulcs, amely támogatja a Azure Active Directory (Azure AD) és a megosztott kulcsos hitelesítést.

## <a name="view-authentication-details"></a>Hitelesítés részleteinek megtekintése

A Azure Maps fiók létrehozását követően létrejön az elsődleges és a másodlagos kulcs. Az elsődleges kulcsot használja előfizetési kulcsként, néha ezek a nevek is felhasználhatók. A másodlagos kulcsot olyan forgatókönyvekben lehet használni, mint például a kulcsok változásai. Mindkét esetben szükség van egy kulcsra a Azure Maps meghívásához. Ezt a folyamatot [megosztott kulcsos hitelesítésnek](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)nevezzük. A megosztott kulccsal és az Azure AD-hitelesítéssel kapcsolatos további tudnivalókért tekintse meg a [Azure Maps hitelesítéssel](https://aka.ms/amauth) foglalkozó témakört.

A hitelesítési adatokat a Azure Portalon tekintheti meg. Nyissa meg a fiókját, és válassza a **hitelesítés** lehetőséget a **Beállítások** menüben.

![Hitelesítés részletei](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="set-up-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrációjának beállítása

Azure Maps fiók létrehozása után létre kell hoznia egy hivatkozást az Azure AD-bérlő és a Azure Maps erőforrás között.

1. A portál menüjében válassza a **Azure Active Directory** lehetőséget. Adja meg a regisztráció nevét. Kattintson **Alkalmazásregisztrációk** majd az **új regisztráció**elemre. Az **átirányítási URI** mezőben adja meg a webalkalmazás kezdőlapját. Például: https://localhost/. Ha már rendelkezik regisztrált alkalmazással, folytassa a 2. lépéssel.

    ![Alkalmazásregisztráció](./media/how-to-manage-authentication/app-registration.png)

    ![Alkalmazás regisztrációjának részletei](./media/how-to-manage-authentication/app-create.png)

2. Ha delegált API-engedélyeket szeretne hozzárendelni Azure Mapshoz, nyissa meg az alkalmazást a **Alkalmazásregisztrációk**területen, majd válassza az **API-engedélyek**lehetőséget. Válassza az **engedély hozzáadása**lehetőséget. Keresse meg és válassza ki **Azure Maps** az **API kiválasztása**területen.

    ![Alkalmazás API-engedélyei](./media/how-to-manage-authentication/app-permissions.png)

3. Az **engedélyek kiválasztása**területen jelölje be a **felhasználó megszemélyesítése**jelölőnégyzetet, majd kattintson a lap alján található **kiválasztás** gombra.

    ![Alkalmazás API-engedélyeinek kiválasztása](./media/how-to-manage-authentication/select-app-permissions.png)

4. A hitelesítési módszertől függően végezze el az a vagy a b lépést.

    1. Ha az alkalmazás felhasználói tokenes hitelesítést használ a Azure Maps web SDK-val, engedélyezze a `oauth2AllowImplicitFlow`t az alkalmazás regisztrációjának jegyzékfájl szakaszában az igaz értékre állítva.
    
       ![Alkalmazás jegyzékfájlja](./media/how-to-manage-authentication/app-manifest.png)

    2. Ha az alkalmazás kiszolgálói/alkalmazás-hitelesítést használ, nyissa meg a **tanúsítványok & Secrets** panelt az alkalmazás regisztrációjában, és hozzon létre egy jelszót, vagy töltsön fel egy nyilvános kulcsú tanúsítványt az alkalmazás regisztrálásához. Ha jelszót hoz létre, azt biztonságosan tárolja a későbbi használat érdekében. Ezt a jelszót fogja használni a jogkivonatok Azure AD-ből való beszerzéséhez.

       ![Alkalmazás kulcsai](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése Azure Maps

Miután hozzárendelt egy Azure Maps fiókot az Azure AD-bérlőhöz, hozzáférés-vezérlést adhat meg. Hozzáférés-vezérlést biztosít egy felhasználó, csoport vagy alkalmazás egy vagy több Azure Maps hozzáférés-vezérlési szerepkörhöz való hozzárendelésével.

1. Nyissa meg **Azure Maps-fiókját**. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **szerepkör-hozzárendelés**elemet.

    ![RBAC megadása](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. A **szerepkör-hozzárendelés** ablak **szerepkör**területén válassza a **Azure Maps Date Reader (előzetes verzió)** lehetőséget. **A hozzáférés társítása lehetőségnél** válassza ki az **Azure ad-felhasználó, csoport vagy szolgáltatás elvét**. Válassza ki a felhasználót vagy alkalmazást. Kattintson a **Mentés** gombra.

    ![Szerepkör-hozzárendelés hozzáadása](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Elérhető Azure Maps RBAC szerepköreinek megtekintése

Ha Azure Maps számára elérhető szerepköralapú hozzáférés-vezérlési (RBAC) szerepköröket szeretné megtekinteni, lépjen a **hozzáférés-vezérlés (iam)** elemre, válassza a **szerepkörök**lehetőséget, majd keresse meg a **Azure Mapstól**kezdődő szerepköröket. Ezek a szerepkörök azok a szerepkörök, amelyekhez hozzáférést biztosíthat.

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
| Nyilvános Azure        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Az Azure AD-ból származó hozzáférési jogkivonatok igénylésével kapcsolatos további információkért tekintse meg a felhasználók és az egyszerű szolgáltatások című témakört az [Azure ad hitelesítési forgatókönyvei](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)című részben.


## <a name="next-steps"></a>Következő lépések

Az Azure AD-hitelesítéssel és a Azure Maps web SDK-val kapcsolatos további tudnivalókért tekintse meg az [Azure ad-t és a Azure Maps web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)-t.

Ismerje meg, hogyan tekintheti meg Azure Maps fiókjának API-használati metrikáit:
> [!div class="nextstepaction"] 
> [Használati metrikák megtekintése](how-to-view-api-usage.md)

A Azure Active Directory (HRE) Azure Maps használatával történő integrálását bemutató minták listáját a következő témakörben tekintheti meg:

> [!div class="nextstepaction"]
> [Azure AD-hitelesítési minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
