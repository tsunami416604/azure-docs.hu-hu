---
title: Adja hozzá a Google identitásszolgáltatójaként az Azure Active Directory B2B |} A Microsoft Docs
description: A Google-lel, jelentkezzen be a saját Gmail-fiókba az Azure AD-alkalmazások vendég felhasználók összevonása
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 08/20/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: d36a4071dbbfb52e22a4e0ecc850da68ebeae6e5
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888117"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Adja hozzá a Google B2B vendégfelhasználó Identitásszolgáltatóként

A Google összevonási beállításával engedélyezheti a meghívott felhasználók jelentkezhetnek be a megosztott alkalmazások és erőforrások a saját Google-fiókok esetében a Microsoft Accounts (msa-k) vagy az Azure AD-fiókok létrehozása nélkül.  
> [!NOTE]
> A Google vendég felhasználók be kell jelentkeznie, egy hivatkozással, amely tartalmazza a bérlő összefüggésben, például a `https://myapps.microsoft.com/?tenantid=<tenant id>`. Alkalmazások és erőforrások mutató közvetlen hivatkozásokat is működik, amíg a bérlő környezet tartalmazzák. Vendégfelhasználók nem jelentkezzen be a végpontok, amelyeken nincs bérlői környezet. Például `https://myapps.microsoft.com`, `https://portal.azure.com`, vagy a csapatok közös végponti egy hibát eredményez.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Mi az a Google felhasználói élményének?
Egy Google Gmail felhasználói meghívót küld, ha a vendégfelhasználó hozzá kell férnie a megosztott vagy egy hivatkozással, amely tartalmazza a bérlő helyi erőforrásokhoz. Saját eltérőek lehetnek attól függően, hogy azok már bejelentkezett a Google-fiókba:
  - Ha a Vendég felhasználó nem jelentkezett be a Google-fiókba, jelentkezzen be Google kell adnia.
  - Ha a Vendég felhasználó már bejelentkezett a Google-fiókba, akkor a használni kívánt fiók kiválasztásához kéri. A fiók is, ha címtártagot szeretne meghívni, válasszon ki.

Ha a Vendég felhasználó egy "túl hosszú fejléc" hibát látja, próbálkozhatnak, törölje a cookie-kat, vagy nyisson meg egy privát vagy inkognitó ablakot, és próbálja meg újra bejelentkezni.

![Bejelentkezés Google-fiókkal](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>1. lépés: A Google developer-projekt konfigurálása
Először hozzon létre egy új projektet a Google fejlesztői konzolon a ügyfél azonosítója és a egy ügyfélkulcsot, amely a későbbiekben is hozzáadhat az Azure AD. 
1. Nyissa meg a Google API-k, https://console.developers.google.com, és jelentkezzen be a Google-fiókját. Azt javasoljuk, hogy használja-e egy megosztott csapat Google-fiók.
2. Hozzon létre egy új projektet: az az irányítópulton, válassza ki **projekt létrehozása**, majd válassza ki **létrehozás**. Az új projekt lapon adjon meg egy **projektnév**, majd válassza ki **létrehozása**.
   
   ![Új Google-projekt](media/google-federation/google-new-project.png)

3. Győződjön meg arról, hogy az új projekt ki van jelölve, a projekt menüben. Ezután nyissa meg a menüt a bal felső sarokban, és válassza ki **API-k és szolgáltatások** > **hitelesítő adatok**.

   ![A Google API hitelesítő adatai](media/google-federation/google-api.png)
 
4. Válassza ki a **Oauth-hozzájárulási képernyő** lapra, és adjon meg egy **a felhasználóknak megjelenített Terméknév**. (A többi beállítást hagyja.) Kattintson a **Mentés** gombra.

   ![Google OAuth-hozzájárulási képernyő](media/google-federation/google-oauth-consent-screen.png)

5. Válassza ki a **hitelesítő adatok** fülre. Az a **hitelesítő adatok létrehozása** menüben válassza a **OAuth-Ügyfélazonosító**.

   ![A Google API hitelesítő adatai](media/google-federation/google-api-credentials.png)

6. Alatt **alkalmazástípus**, válassza a **webes alkalmazás**, majd a **jogosult átirányítási URI-k**, adja meg a következő URI-k:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(Ha `<directory id>` a címtár-azonosító)
   
    > [!NOTE]
    > A könyvtár Azonosítójának megkereséséhez lépjen https://portal.azure.com, majd a **Azure Active Directory**, válassza a **tulajdonságok** , és másolja a **címtár-azonosító**.

   ![Hozzon létre az OAuth-Ügyfélazonosító](media/google-federation/google-create-oauth-client-id.png)

7. Kattintson a **Létrehozás** gombra. Az ügyfél-azonosító és titkos ügyfélkódként fogjuk használni, amikor hozzáadja az identitásszolgáltató az Azure AD-portálra másolja.

   ![OAuth-Azonosítót és titkos Ügyfélkód](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2. lépés: A Google-összevonás konfigurálása az Azure ad-ben 
Most, értékre állítjuk a Google-ügyfél-Azonosítót és a titkos kulcsot, az Azure AD portálon megadásával vagy a PowerShell használatával. Győződjön meg arról, Gmail-címet használó és a kísérlet beváltani a meghívót a meghívott Google-fiókkal való meghívása saját maga által a Google-összevonási konfiguráció tesztelése. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>A Google-összevonás konfigurálása az Azure AD portálon 
1. Nyissa meg az [Azure Portal](https://portal.azure.com). A bal oldali panelen válassza ki a **Azure Active Directory**. 
2. Válassza ki **szervezeti kapcsolatok**.
3. Válassza ki **Identitásszolgáltatók**, majd kattintson a **Google** gombra.
4. Adjon meg egy nevet. Majd adja meg az ügyfél-azonosító és titkos Ügyfélkód korábban szerzett be. Kattintson a **Mentés** gombra. 

   ![Google-identitásszolgáltató hozzáadása](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>A Google-összevonás konfigurálása a PowerShell használatával
1. Telepítse a legújabb verziót az Azure AD PowerShell modul a Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Futtassa a következő parancsot: `Connect-AzureAD`.
3. A bejelentkezési parancssorba jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
4. Futtassa az alábbi parancsot: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Használja az ügyfél-azonosítót és az alkalmazásból a létrehozott titkos "1. lépés: a Google developer-projekt konfigurálásához." További információkért lásd: a [New-AzureADMSIdentityProvider](https://docs.microsoft.com/en-us/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) cikk. 
 
## <a name="how-do-i-remove-google-federation"></a>Hogyan távolíthatom el a Google összevonási?
A Google-összevonási telepítés törölheti. Ha így tesz, Google vendég felhasználók, akik már beváltott a meghívót nem lesz lehetősége bejelentkezni, de átadásával hozzáférés ismét az erőforrásokhoz való törlésével őket a könyvtárból, és újra meghívja őket. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Az Azure AD portálon Google összevonási törlése: 
1. Nyissa meg az [Azure Portal](https://portal.azure.com). A bal oldali panelen válassza ki a **Azure Active Directory**. 
2. Válassza ki **szervezeti kapcsolatok**.
3. Válassza ki **Identitásszolgáltatók**, majd kattintson a **Google** gombra.
4. Válassza ki **Google**, majd válassza ki **törlése**. 
   
   ![A közösségi identitásszolgáltató törölve](media/google-federation/google-social-identity-providers.png)

1. Válassza ki **Igen** a törlés megerősítéséhez. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Google összevonási törlése PowerShell használatával: 
1. Telepítse a legújabb verziót az Azure AD PowerShell modul a Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Futtassa az `Connect-AzureAD` parancsot.  
4. A bejelentkezés a parancssorban jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
5. Írja be a következő parancsot:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > További információkért lásd: [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/en-us/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 