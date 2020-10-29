---
title: A Google hozzáadása identitás-szolgáltatóként a B2B-Azure AD-ben
description: A Google összevonása lehetővé teszi a vendég felhasználók számára, hogy saját Gmail-fiókjával jelentkezzenek be az Azure AD-alkalmazásokba.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926235"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói számára

A Google-nal való összevonás beállításával engedélyezheti a meghívott felhasználóknak, hogy a saját Gmail-fiókjával jelentkezzenek be a megosztott alkalmazásaiba és erőforrásaiba anélkül, hogy Microsoft-fiókokat kellene létrehozniuk. 

> [!NOTE]
> A Google Federation kifejezetten a Gmail-felhasználók számára készült. A G Suite-tartományokkal való összevonása használja a [közvetlen összevonás](direct-federation.md)lehetőséget.

## <a name="what-is-the-experience-for-the-google-user"></a>Mi a Google-felhasználó tapasztalata?
Ha meghívót küld a Google Gmail felhasználói számára, a vendég felhasználóknak a bérlői kontextust tartalmazó hivatkozás használatával kell hozzáférnie a megosztott alkalmazásokhoz vagy erőforrásokhoz. A felhasználói élmény attól függően változhat, hogy már be vannak jelentkezve a Google-ba:
  - A rendszer a Google-ba nem bejelentkezett vendégeket fogja kérni.
  - A Google-ba már bejelentkezett vendég felhasználók a használni kívánt fiók kiválasztását kérik. Ki kell választania azt a fiókot, amellyel meghívja őket.

A "fejléc túl hosszú" hibát mutató vendég felhasználók törölhetik a cookie-kat, vagy megnyithatnak egy privát vagy inkognitóban-ablakot, és megpróbálnak újra bejelentkezni.

![Képernyőkép, amely a Google bejelentkezési oldalát jeleníti meg.](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Korlátozások

A csapatok teljes mértékben támogatják a Google vendég felhasználókat minden eszközön. A Google-felhasználók olyan közös végpontból jelentkezhetnek be a csapatba, mint például a `https://teams.microsoft.com` .

Előfordulhat, hogy más alkalmazások általános végpontjai nem támogatják a Google-felhasználókat. A Google Guest Users szolgáltatásnak be kell jelentkeznie egy olyan hivatkozással, amely tartalmazza a bérlő adatait. A következő példák:
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Ha a Google vendég felhasználói egy vagy hasonló hivatkozást próbálnak használni `https://myapps.microsoft.com` `https://portal.azure.com` , hibaüzenetet kapnak.

Azt is megteheti, hogy a Google vendég felhasználóinak közvetlen hivatkozása van egy alkalmazásra vagy erőforrásra, feltéve, hogy a hivatkozás tartalmazza a bérlői adatait. Például: `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>1. lépés: a Google fejlesztői projekt konfigurálása
Először hozzon létre egy új projektet a Google fejlesztői konzolon egy ügyfél-azonosító és egy ügyfél-titok beszerzéséhez, amelyet később hozzáadhat a Azure Active Directory (Azure AD) szolgáltatáshoz. 
1. Nyissa meg a Google API-kat a címen https://console.developers.google.com , és jelentkezzen be Google-Fiókjával. Javasoljuk, hogy a megosztott csapat Google-fiókját használja.
2. Ha a rendszer kéri, fogadja el a szolgáltatási feltételeket.
3. Hozzon létre egy új projektet: az irányítópulton válassza a **projekt létrehozása** elemet, adja meg a projekt nevét (például **Azure ad B2B** ), majd válassza a **Létrehozás** lehetőséget: 
   
   ![Képernyőkép, amely egy új projekt oldalt jelenít meg.](media/google-federation/google-new-project.png)

4. Az API-k **& szolgáltatások** lapon válassza a **nézet** lehetőséget az új projekt alatt.

5. Válassza az **Ugrás az API-khoz – áttekintés** az API-k kártyán. Válassza a **OAuth-beleegyezési képernyő** lehetőséget.

6. Válassza a **külső** lehetőséget, majd válassza a **Létrehozás** lehetőséget. 

7. Az **OAuth-beleegyezés képernyőn** adja meg az **alkalmazás nevét** :

   ![Képernyőkép, amely a Google OAuth-beleegyezési képernyőjét jeleníti meg.](media/google-federation/google-oauth-consent-screen.png)

8. Görgessen a **jóváhagyott tartományok** szakaszhoz, és adja meg a **microsoftonline.com** :

   ![A jogosultsággal rendelkező tartományok szakaszt bemutató képernyőkép.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Válassza a **Mentés** lehetőséget.

10. Válassza a **Hitelesítő adatok** lehetőséget. A **hitelesítő adatok létrehozása** menüben válassza a **OAUTH ügyfél-azonosító** :

    ![A Google API-k hitelesítő adatok létrehozása menüt bemutató képernyőkép.](media/google-federation/google-api-credentials.png)

11. Az **alkalmazás típusa** területen válassza a **webalkalmazás** lehetőséget. Adjon megfelelő nevet az alkalmazásnak, például az **Azure ad B2B** -nek. A **hitelesítő átirányítási URI** -k területen adja meg a következő URI-ket:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(hol `<tenant ID>` van a bérlő azonosítója)
   
    > [!NOTE]
    > A bérlő AZONOSÍTÓjának megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com). A **Azure Active Directory** alatt válassza a **Tulajdonságok** lehetőséget, és másolja a **bérlői azonosítót** .

    ![Az átirányítási URI-k engedélyezését bemutató képernyőkép.](media/google-federation/google-create-oauth-client-id.png)

12. Válassza a **Létrehozás** lehetőséget. Másolja az ügyfél-azonosítót és az ügyfél titkos kulcsát. Ezeket akkor fogja használni, amikor hozzáadja az identitás-szolgáltatót a Azure Portal.

    ![Képernyőkép, amely a OAuth ügyfél-azonosítót és az ügyfél titkos kulcsát mutatja.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2. lépés: a Google-összevonás konfigurálása az Azure AD-ben 
Ekkor megadhatja a Google ügyfél-azonosítót és az ügyfél titkos kulcsát. Ezt a Azure Portal vagy a PowerShell használatával teheti meg. Győződjön meg arról, hogy saját maga hívja meg a Google összevonási konfigurációját. Használjon Gmail-címeket, és próbálja meg beváltani a meghívót a meghívott Google-fiókkal. 

**A Google Federation konfigurálása a Azure Portalban** 
1. Nyissa meg az [Azure Portalt](https://portal.azure.com). A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget. 
2. Válassza a **külső identitások** lehetőséget.
3. Válassza a **minden identitás szolgáltató** lehetőséget, majd kattintson a **Google** gombra.
4. Adja meg a korábban beszerzett ügyfél-azonosítót és az ügyfél-titkot. Válassza a **Mentés** lehetőséget: 

   ![A Google Identity Provider hozzáadása oldalt megjelenítő képernyőkép.](media/google-federation/google-identity-provider.png)

**A Google-összevonás konfigurálása a PowerShell használatával**
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa a következő parancsot: `Connect-AzureAD`
3. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
4. Futtassa az alábbi parancsot: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Használja az ügyfél-azonosítót és az ügyfél titkos kulcsát az "1. lépés: Google fejlesztői projekt konfigurálása" című témakörben létrehozott alkalmazásból. További információ: [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Hogyan eltávolítja a Google Federation szolgáltatást?
A Google összevonási telepítőt törölheti. Ha így tesz, a Google vendég felhasználóinak, akik már beváltották a meghívót, nem tudnak majd bejelentkezni. A címtárból való törléssel és azok újbóli meghívásával azonban ismét hozzáférhet az erőforrásokhoz. 
 
**A Google-összevonás törlése az Azure AD-portálon**
1. Nyissa meg az [Azure Portalt](https://portal.azure.com). A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget. 
2. Válassza a **külső identitások** lehetőséget.
3. Válassza ki **az összes identitás szolgáltatót** .
4. A **Google** sorban válassza a három pontot ( **...** ), majd kattintson a **Törlés** gombra. 
   
   ![A közösségi személyazonosság-szolgáltató törlés gombját megjelenítő képernyőkép.](media/google-federation/google-social-identity-providers.png)

1. A törlés megerősítéséhez válassza az **Igen** lehetőséget. 

**A Google-összevonás törlése a PowerShell használatával** 
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa a `Connect-AzureAD` parancsot.  
4. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
5. Írja be a következő parancsot:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > További információ: [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
