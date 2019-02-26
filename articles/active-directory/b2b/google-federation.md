---
title: Google hozzáadása a B2B - Azure Active Directory egy identitás-szolgáltatóként |} A Microsoft Docs
description: A Google-lel, jelentkezzen be a saját Gmail-fiókba az Azure AD-alkalmazások vendég felhasználók összevonása
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe259b6a0f45828e100de33f533e370323128eef
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821696"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Adja hozzá a Google B2B vendégfelhasználó Identitásszolgáltatóként

A Google összevonási beállításával engedélyezheti a meghívott felhasználók jelentkezhetnek be a megosztott alkalmazások és erőforrások a saját Google-fiókok esetében a Microsoft Accounts (msa-k) vagy az Azure AD-fiókok létrehozása nélkül.  
> [!NOTE]
> A Google vendégfelhasználók kell jelentkezzen be egy hivatkozást, amely tartalmazza a bérlő környezetben (például `https://myapps.microsoft.com/?tenantid=<tenant id>` vagy `https://portal.azure.com/<tenant id>`, vagy egy ellenőrzött tartomány esetén `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Alkalmazások és erőforrások mutató közvetlen hivatkozásokat is működik, amíg a bérlő környezet tartalmazzák. Vendégfelhasználók nem jelentkezzen be a végpontok, amelyeken nincs bérlői környezet. Például `https://myapps.microsoft.com`, `https://portal.azure.com`, vagy a csapatok közös végponti egy hibát eredményez.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Mi az a Google felhasználói élményének?
Egy Google Gmail felhasználói meghívót küld, ha a vendégfelhasználó hozzá kell férnie a megosztott vagy egy hivatkozással, amely tartalmazza a bérlő helyi erőforrásokhoz. Saját eltérőek lehetnek attól függően, hogy azok már bejelentkezett a Google-fiókba:
  - Ha a Vendég felhasználó nem jelentkezett be a Google-fiókba, jelentkezzen be Google kell adnia.
  - Ha a Vendég felhasználó már bejelentkezett a Google-fiókba, akkor a használni kívánt fiók kiválasztásához kéri. A fiók is, ha címtártagot szeretne meghívni, válasszon ki.

Ha a Vendég felhasználó egy "túl hosszú fejléc" hibát látja, próbálkozhatnak, törölje a cookie-kat, vagy nyisson meg egy privát vagy inkognitó ablakot, és próbálja meg újra bejelentkezni.

![Bejelentkezés Google-fiókkal](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>1. lépés: Egy Google developer-projekt konfigurálása
Először hozzon létre egy új projektet a Google fejlesztői konzolon a ügyfél azonosítója és a egy ügyfélkulcsot, amely a későbbiekben is hozzáadhat az Azure AD. 
1. Nyissa meg a Google API-k, https://console.developers.google.com, és jelentkezzen be a Google-fiókját. Azt javasoljuk, hogy használja-e egy megosztott csapat Google-fiók.
2. Új projekt létrehozása: Az irányítópulton, válassza ki a **projekt létrehozása**, majd válassza ki **létrehozás**. Az új projekt lapon adjon meg egy **projektnév**, majd válassza ki **létrehozása**.
   
   ![Új Google-projekt](media/google-federation/google-new-project.png)

3. Győződjön meg arról, hogy az új projekt ki van jelölve, a projekt menüben. Ezután nyissa meg a menüt a bal felső sarokban, és válassza ki **API-k és szolgáltatások** > **hitelesítő adatok**.

   ![A Google API hitelesítő adatai](media/google-federation/google-api.png)
 
4. Válassza ki a **OAuth-hozzájárulási képernyő** lapra, és adjon meg egy **alkalmazásnév**. (A többi beállítást hagyja.)

   ![Google OAuth-hozzájárulási képernyő](media/google-federation/google-oauth-consent-screen.png)

5. Görgessen a **tartományok jogosult** szakaszt, és adja meg a microsoftonline.com.

   ![Jogosult tartományok szakaszban](media/google-federation/google-oauth-authorized-domains.png)

6. Kattintson a **Mentés** gombra.

7. Válassza ki a **hitelesítő adatok** fülre. Az a **hitelesítő adatok létrehozása** menüben válassza a **OAuth-Ügyfélazonosító**.

   ![A Google API hitelesítő adatai](media/google-federation/google-api-credentials.png)

8. Alatt **alkalmazástípus**, válassza a **webes alkalmazás**, majd a **jogosult átirányítási URI-k**, adja meg a következő URI-k:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(Ha `<directory id>` a címtár-azonosító)
   
    > [!NOTE]
    > A könyvtár Azonosítójának megkereséséhez lépjen https://portal.azure.com, majd a **Azure Active Directory**, válassza a **tulajdonságok** , és másolja a **címtár-azonosító**.

   ![Hozzon létre az OAuth-Ügyfélazonosító](media/google-federation/google-create-oauth-client-id.png)

9. Kattintson a **Létrehozás** gombra. Az ügyfél-azonosító és titkos ügyfélkódként fogjuk használni, amikor hozzáadja az identitásszolgáltató az Azure AD-portálra másolja.

   ![OAuth-Azonosítót és titkos Ügyfélkód](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2. lépés: Google-összevonás konfigurálása az Azure ad-ben 
Most, értékre állítjuk a Google-ügyfél-Azonosítót és a titkos kulcsot, az Azure AD portálon megadásával vagy a PowerShell használatával. Győződjön meg arról, Gmail-címet használó és a kísérlet beváltani a meghívót a meghívott Google-fiókkal való meghívása saját maga által a Google-összevonási konfiguráció tesztelése. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>A Google-összevonás konfigurálása az Azure AD portálon 
1. Nyissa meg az [Azure Portal](https://portal.azure.com). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
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
   > Használja az ügyfél-azonosítót és az alkalmazásból a létrehozott titkos "1. lépés: Egy Google developer-projekt konfigurálásához." További információkért lásd: a [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) cikk. 
 
## <a name="how-do-i-remove-google-federation"></a>Hogyan távolíthatom el a Google összevonási?
A Google-összevonási telepítés törölheti. Ha így tesz, Google vendég felhasználók, akik már beváltott a meghívót nem lesz lehetősége bejelentkezni, de átadásával hozzáférés ismét az erőforrásokhoz való törlésével őket a könyvtárból, és újra meghívja őket. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Az Azure AD portálon Google összevonási törlése: 
1. Nyissa meg az [Azure Portal](https://portal.azure.com). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza ki **szervezeti kapcsolatok**.
3. Válassza ki **Identitásszolgáltatók**.
4. Az a **Google** . sor, válassza ki a helyi menüt (**...** ), majd **törlése**. 
   
   ![A közösségi identitásszolgáltató törölve](media/google-federation/google-social-identity-providers.png)

1. Válassza ki **Igen** a törlés megerősítéséhez. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Google összevonási törlése PowerShell használatával: 
1. Telepítse a legújabb verziót az Azure AD PowerShell modul a Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Futtassa az `Connect-AzureAD` parancsot.  
4. A bejelentkezés a parancssorban jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
5. Írja be a következő parancsot:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > További információkért lásd: [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
