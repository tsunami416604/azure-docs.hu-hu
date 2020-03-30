---
title: Hitelesítés kezelése | Microsoft Azure Maps
description: Az Azure Portal használatával kezelheti a Microsoft Azure Maps hitelesítését.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335528"
---
# <a name="manage-authentication-in-azure-maps"></a>Hitelesítés kezelése az Azure Mapsben

Miután létrehozott egy Azure Maps-fiókot, egy ügyfélazonosító és kulcsok jönnek létre az Azure Active Directory (Azure AD) hitelesítésének és a megosztott kulcshitelesítés támogatásához.

## <a name="view-authentication-details"></a>Hitelesítési adatok megtekintése

Miután létrehozott egy Azure Maps-fiókot, az elsődleges és másodlagos kulcsok jönnek létre. Azt javasoljuk, hogy az elsődleges kulcsot használja előfizetési kulcsként, amikor [megosztott kulcsú hitelesítést használ az Azure Maps hívásához.](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication) Másodlagos kulcsot használhat olyan esetekben, mint például a kulcsváltás. További információ: [Authentication in Azure Maps](https://aka.ms/amauth).

A hitelesítési adatokat az Azure Portalon tekintheti meg. Ott a Fiókban a **Beállítások** menüben válassza a **Hitelesítés parancsot.**

![Hitelesítésrészletei](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Azure AD-alkalmazás regisztrálása és konfigurálása

1. Az Azure Portalon az Azure-szolgáltatások listájában válassza az **Azure Active Directory** > **alkalmazás regisztrációja** > **Új regisztráció.**  

    ![Alkalmazásregisztráció](./media/how-to-manage-authentication/app-registration.png)

1. Ha már regisztrálta az alkalmazást, folytassa a következő lépéssel. Ha még nem regisztrálta az alkalmazást, írja be a **nevet**, válasszon **egy támogatási fióktípust,** majd válassza a **Regisztráció**lehetőséget.  

    ![Alkalmazásregisztrációs adatok](./media/how-to-manage-authentication/app-create.png)

1. Delegált API-engedélyek hozzárendelése az Azure Maps, nyissa meg az alkalmazást. Ezután **az Alkalmazásregisztrációk**csoportban válassza az **API-engedélyek** > **Engedély hozzáadása**lehetőséget. A **szervezetáltal használt API-k**csoportban keresse meg és válassza az **Azure Maps**lehetőséget.

    ![Alkalmazás API-engedélyek hozzáadása](./media/how-to-manage-authentication/app-permissions.png)

1. Jelölje be az **Access Azure Maps**melletti jelölőnégyzetet, majd az **Engedélyek hozzáadása**lehetőséget.

    ![Alkalmazás API-engedélyei kiválasztása](./media/how-to-manage-authentication/select-app-permissions.png)

1. A hitelesítési módszertől függően hajtsa végre az alábbi lépések egyikét. 

    * Ha az alkalmazás felhasználói jogkivonat-hitelesítést használ az Azure `oauth2AllowImplicitFlow`Maps Web SDK-val, engedélyezze a . Az engedélyezéshez az alkalmazásregisztráció **jegyzékfájljában** igaz értékre van állítva. `oauth2AllowImplicitFlow` 
    
       ![Alkalmazásjegyzék](./media/how-to-manage-authentication/app-manifest.png)

    * Ha az alkalmazás kiszolgáló- vagy alkalmazáshitelesítést használ, akkor az alkalmazás regisztrációs lapján nyissa meg a **Tanúsítványok & titkos kulcsok című**lapot. Ezután töltsön fel egy nyilvános kulcsos tanúsítványt, vagy hozzon létre egy jelszót az **Új ügyféltitok**lehetőséget választva. 
    
       ![Ügyféltitok létrehozása](./media/how-to-manage-authentication/app-keys.png)

        Ha létrehoz egy jelszót, majd miután a **Hozzáadás**lehetőséget, másolja a jelszót, és tárolja biztonságosan. Ezt a jelszót fogja használni az Azure AD-ből származó jogkivonatok lekéréséhez.

       ![Ügyféltitok hozzáadása](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Szerepköralapú hozzáférés-vezérlés megadása az Azure Maps számára

Miután társítegy Azure Maps-fiókot az Azure AD-bérlővel, hozzáférést biztosíthat. Szerepköralapú *hozzáférés-vezérlést* (RBAC) ad meg egy felhasználó, csoport vagy alkalmazás hozzárendelésével egy vagy több Azure Maps hozzáférés-vezérlési szerepkörhöz. 

1. Nyissa meg **Azure Maps-fiókját.** Válassza a **Hozzáférés-vezérlési (IAM)** > **szerepkör-hozzárendelés lehetőséget.**

    ![Grant RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. A **Szerepkör-hozzárendelések** lap **Szerepkör csoportjában**válassza az **Azure Maps dátumolvasóját (előzetes verzió).** A **Hozzáférés hozzárendelése a**csoportban válassza az Azure **AD-felhasználó, -csoport vagy egyszerű szolgáltatás lehetőséget.** Jelölje ki a felhasználót vagy alkalmazást. Ezután válassza a **Save** (Mentés) lehetőséget.

    ![Szerepkör-hozzárendelés hozzáadása](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Az elérhető Azure Maps RBAC-szerepkörök megtekintése

Az Azure Maps számára elérhető RBAC-szerepkörök megtekintéséhez nyissa meg a **Hozzáférés-vezérlés (IAM)** című területet. Válassza **a Szerepkörök**lehetőséget, majd keresse meg az *Azure Maps*szolgáltatással kezdődő szerepköröket. Ezek az Azure Maps-szerepkörök azok a szerepkörök, amelyekhez hozzáférést biztosíthat.

![Elérhető szerepkörök megtekintése](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Az Azure Maps RBAC megtekintése

Az RBAC részletes hozzáférés-vezérlést biztosít.

Az Azure Maps rBAC-licencet kapott felhasználók és alkalmazások megtekintéséhez nyissa meg **a Hozzáférés-vezérlés (IAM)** lapot. Itt válassza **a Szerepkör-hozzárendelések**lehetőséget, majd szűrje az **Azure Maps**szerint.

![Az RBAC-ra kapott felhasználók és alkalmazások megtekintése](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Tokenek kérése az Azure Maps számára

Miután regisztrálta az alkalmazást, és társítja az Azure Maps,hozzáférési jogkivonatokat kérhet.

Ha az alkalmazás felhasználói jogkivonat-hitelesítést használ az Azure Maps Web SDK-val, majd konfigurálja a HTML-lapot az Azure Maps ügyfélazonosítóval és az Azure AD alkalmazásazonosítóval.

Ha az alkalmazás kiszolgáló- vagy alkalmazáshitelesítést használ, majd kérjen `https://login.microsoftonline.com`egy jogkivonatot az Azure AD token végpontjától. A kérelemben használja a következő adatokat: 

* Azure AD-erőforrásazonosító`https://atlas.microsoft.com/`
* Azure Maps ügyfélazonosító
* Az Azure AD alkalmazás azonosítója
* Az Azure AD alkalmazás regisztrációs jelszava vagy tanúsítványa

| Azure-környezet   | Azure AD token végpont | Azure-erőforrás azonosítója |
| --------------------|-------------------------|-------------------|
| Azure nyilvános felhő        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Az Azure Government felhője   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

A hozzáférési jogkivonatok Azure AD-től felhasználók és egyszerű szolgáltatásért történő kéréséről az [Azure AD hitelesítési forgatókönyvei](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)című témakörben talál további információt.


## <a name="next-steps"></a>További lépések

További információ: [Azure AD és Azure Maps Web SDK.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)

Az Azure Maps-fiók API-használati mutatóinak megkeresése:
> [!div class="nextstepaction"] 
> [Használati metrikák megtekintése](how-to-view-api-usage.md)

Ismerje meg az Azure AD azure Maps beépülő szolgáltatását bemutató mintákat:

> [!div class="nextstepaction"]
> [Az Azure AD hitelesítési minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
