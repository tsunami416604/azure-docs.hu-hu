---
title: Hitelesítés kezelése | Microsoft Azure térképek
description: A Azure Portal használatával kezelheti a hitelesítést Microsoft Azure Maps-ben.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335528"
---
# <a name="manage-authentication-in-azure-maps"></a>Hitelesítés kezelése Azure Maps

Azure Maps fiók létrehozása után létrejön egy ügyfél-azonosító és egy kulcs, amely támogatja a Azure Active Directory (Azure AD) hitelesítést és a megosztott kulcsos hitelesítést.

## <a name="view-authentication-details"></a>Hitelesítés részleteinek megtekintése

Azure Maps fiók létrehozása után létrejön az elsődleges és a másodlagos kulcs. Javasoljuk, hogy egy elsődleges kulcsot használjon előfizetési kulcsként, ha [megosztott kulcsos hitelesítést használ a Azure Maps meghívásához](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Másodlagos kulcsot használhat olyan forgatókönyvekben, mint például a kulcsok változásai. További információ: [hitelesítés Azure Mapsban](https://aka.ms/amauth).

A hitelesítési adatokat a Azure Portalban tekintheti meg. A fiókjában a **Beállítások** menüben válassza a **hitelesítés**lehetőséget.

![Hitelesítés részletei](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Azure AD-alkalmazás regisztrálása és konfigurálása

1. Az Azure Portal az Azure-szolgáltatások listájában válassza a **Azure Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.  

    ![Alkalmazásregisztráció](./media/how-to-manage-authentication/app-registration.png)

1. Ha már regisztrálta az alkalmazást, folytassa a következő lépéssel. Ha még nem regisztrálta az alkalmazást, adjon meg egy **nevet**, válassza ki a **támogatási fiók típusát**, majd válassza a **regisztráció**lehetőséget.  

    ![Alkalmazás regisztrációjának részletei](./media/how-to-manage-authentication/app-create.png)

1. Ha delegált API-engedélyeket szeretne hozzárendelni Azure Mapshoz, nyissa meg az alkalmazást. Ezután a **Alkalmazásregisztrációk**alatt válassza az **API-engedélyek** > **Hozzáadás engedélyt**. A **szervezetem által használt API**-k alatt keresse meg és válassza a **Azure Maps**lehetőséget.

    ![Alkalmazás API-engedélyeinek hozzáadása](./media/how-to-manage-authentication/app-permissions.png)

1. Jelölje be a **hozzáférési Azure Maps**melletti jelölőnégyzetet, majd válassza az **engedélyek hozzáadása**lehetőséget.

    ![Alkalmazás API-engedélyeinek kiválasztása](./media/how-to-manage-authentication/select-app-permissions.png)

1. A hitelesítési módszertől függően végezze el az alábbi lépések egyikét. 

    * Ha az alkalmazás felhasználói tokenes hitelesítést használ a Azure Maps web SDK-val, akkor `oauth2AllowImplicitFlow`engedélyezze a következőt:. Ennek engedélyezéséhez az alkalmazás-regisztráció **jegyzékfájl** szakaszában állítsa a True (igaz `oauth2AllowImplicitFlow` ) értéket. 
    
       ![Alkalmazás jegyzékfájlja](./media/how-to-manage-authentication/app-manifest.png)

    * Ha az alkalmazása kiszolgálói vagy alkalmazás-hitelesítést használ, akkor az alkalmazás regisztrációs oldalán lépjen a **tanúsítványok & Secrets**elemre. Ezután töltsön fel egy nyilvános kulcsú tanúsítványt, vagy hozzon létre egy jelszót az **új ügyfél-titok**lehetőség kiválasztásával. 
    
       ![Ügyfél titkos kulcsának létrehozása](./media/how-to-manage-authentication/app-keys.png)

        Ha jelszót hoz létre, akkor a **Hozzáadás**gombra kattintva másolja a jelszót, és tárolja biztonságos módon. Ezt a jelszót fogja használni a jogkivonatok Azure AD-ből való lekéréséhez.

       ![Ügyfél titkos kulcsának hozzáadása](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Szerepköralapú hozzáférés-vezérlés biztosítása Azure Maps

Miután hozzárendelt egy Azure Maps fiókot az Azure AD-bérlőhöz, hozzáférés-vezérlést adhat meg. *Szerepköralapú hozzáférés-vezérlést* (RBAC) biztosít egy felhasználó, csoport vagy alkalmazás egy vagy több Azure Maps hozzáférés-vezérlési szerepkörhöz való hozzárendelésével. 

1. Nyissa meg **Azure Maps-fiókját**. Válassza a **hozzáférés-vezérlés (iam)** > **szerepkör-hozzárendelés**elemet.

    ![RBAC megadása](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. A **szerepkör-hozzárendelések** lap **szerepkör**területén válassza a **Azure Maps Date Reader (előzetes verzió)** lehetőséget. A **hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**lehetőséget. Válassza ki a felhasználót vagy alkalmazást. Ezután válassza a **Save** (Mentés) lehetőséget.

    ![Szerepkör-hozzárendelés hozzáadása](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Elérhető Azure Maps RBAC szerepköreinek megtekintése

A Azure Maps számára elérhető RBAC-szerepkörök megtekintéséhez lépjen a hozzáférés- **vezérlés (iam)** elemre. Válassza ki a **szerepkörök**elemet, majd keressen rá a *Azure Maps*kezdetű szerepkörökre. Ezek a Azure Maps szerepkörök azok a szerepkörök, amelyekhez hozzáférést biztosíthat.

![Elérhető szerepkörök megtekintése](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure Maps RBAC megtekintése

A RBAC részletes hozzáférés-vezérlést biztosít.

A Azure Maps RBAC megadott felhasználók és alkalmazások megtekintéséhez lépjen a **Access Control (iam)** elemre. Itt válassza ki a **szerepkör-hozzárendelések**lehetőséget, majd a szűrést **Azure Maps**alapján.

![A RBAC megadott felhasználók és alkalmazások megtekintése](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps igénylési jogkivonatai

Az alkalmazás regisztrálása és Azure Mapshoz való hozzárendelése után hozzáférési jogkivonatokat kérhet le.

Ha az alkalmazás felhasználói tokenes hitelesítést használ a Azure Maps web SDK-val, akkor konfigurálja a HTML-lapot a Azure Maps ügyfél-AZONOSÍTÓval és az Azure AD-alkalmazás azonosítójával.

Ha az alkalmazás kiszolgálói vagy alkalmazás-hitelesítést használ, akkor igényeljen egy jogkivonatot az Azure `https://login.microsoftonline.com`ad jogkivonat-végpontból. A kérelemben használja a következő adatokat: 

* Azure AD-erőforrás azonosítója`https://atlas.microsoft.com/`
* Azure Maps ügyfél-azonosító
* Azure AD-alkalmazás azonosítója
* Azure AD-alkalmazás regisztrációs jelszava vagy tanúsítványa

| Azure-környezet   | Azure AD-jogkivonat végpontja | Azure-erőforrás azonosítója |
| --------------------|-------------------------|-------------------|
| Azure nyilvános felhő        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government felhő   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

További információ az Azure AD-től a felhasználók és az egyszerű szolgáltatások hozzáférési jogkivonatok igénylésével kapcsolatban: [hitelesítési forgatókönyvek az Azure ad-hez](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>További lépések

További információ: [Azure ad és Azure Maps web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

A Azure Maps-fiók API-használati metrikáinak megkeresése:
> [!div class="nextstepaction"] 
> [Használati metrikák megtekintése](how-to-view-api-usage.md)

Ismerkedjen meg az Azure AD és a Azure Maps integrációját bemutató példákkal:

> [!div class="nextstepaction"]
> [Azure AD-hitelesítési minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
