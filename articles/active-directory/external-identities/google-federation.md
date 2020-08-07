---
title: A Google hozzáadása identitás-szolgáltatóként a B2B-Azure AD-ben
description: A Google összevonása lehetővé teszi a vendég felhasználók számára, hogy saját Gmail-fiókjával jelentkezzenek be az Azure AD-alkalmazásokba
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
ms.openlocfilehash: e4b895054f8fa81526bf72cadd2fea1a3691d758
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909460"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói számára

A Google-nal való összevonás beállításával engedélyezheti a meghívott felhasználóknak, hogy saját Gmail-fiókkal jelentkezzenek be a megosztott alkalmazásaiba és erőforrásaiba anélkül, hogy Microsoft-fiókokat kellene létrehoznia (MSAs). 

> [!NOTE]
> A Google Federation kifejezetten a Gmail-felhasználók számára készült. A G Suite-tartományokkal való összevonása használja a [közvetlen összevonási funkciót](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Mi a Google-felhasználó tapasztalata?
Amikor meghívót küld egy Google Gmail-felhasználó számára, a vendég felhasználónak el kell érnie a megosztott alkalmazásait vagy erőforrásait egy olyan hivatkozás használatával, amely tartalmazza a bérlői környezetet. A felhasználói élmény attól függően változhat, hogy már be vannak jelentkezve a Google-ba:
  - Ha a vendég felhasználó nincs bejelentkezve a Google-ba, a rendszer felszólítja, hogy jelentkezzen be a Google-ba.
  - Ha a vendég felhasználó már be van jelentkezve a Google-ba, a rendszer kérni fogja a használni kívánt fiók kiválasztását. Ki kell választania azt a fiókot, amellyel meghívja őket.

Ha a vendég felhasználó a "fejléc túl hosszú" hibaüzenetet látja, megpróbálkozhat a cookie-k törlésével, vagy megnyithat egy privát vagy inkognitóban-ablakot, és próbálkozhat újra a bejelentkezéssel.

![A Google bejelentkezési oldalát bemutató képernyőkép](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Korlátozások

A csapatok teljes mértékben támogatják a Google vendég felhasználókat minden eszközön. A Google-felhasználók olyan közös végpontból jelentkezhetnek be a csapatba, mint például a `https://teams.microsoft.com` .

Előfordulhat, hogy más alkalmazások általános végpontjai nem támogatják a Google-felhasználókat. A Google Guest Users szolgáltatásnak be kell jelentkeznie egy hivatkozással, amely tartalmazza a bérlő adatait. Az alábbi parancsok példák:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Ha a Google vendég felhasználói olyan hivatkozást próbálnak használni, mint a `https://myapps.microsoft.com` vagy a `https://portal.azure.com` , hibaüzenetet kapnak.

Azt is megteheti, hogy a Google vendég felhasználóinak közvetlen hivatkozása van egy alkalmazásra vagy erőforrásra, feltéve például, hogy ez a hivatkozás tartalmazza a bérlői adatait `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` . 

## <a name="step-1-configure-a-google-developer-project"></a>1. lépés: a Google fejlesztői projekt konfigurálása
Először hozzon létre egy új projektet a Google Developers konzolon egy ügyfél-azonosító és egy ügyfél-titok beszerzéséhez, amelyet később hozzáadhat az Azure AD-hez. 
1. Nyissa meg a Google API-kat a címen https://console.developers.google.com , és jelentkezzen be Google-Fiókjával. Javasoljuk, hogy a megosztott csapat Google-fiókját használja.
2. Hozzon létre egy új projektet: az irányítópulton válassza a **projekt létrehozása**lehetőséget, majd válassza a **Létrehozás**lehetőséget. Az új projekt lapon adja meg a **projekt nevét**, majd válassza a **Létrehozás**lehetőséget.
   
   ![Képernyőfelvétel: a Google új projekt oldalát jeleníti meg](media/google-federation/google-new-project.png)

3. Győződjön meg arról, hogy az új projekt ki van választva a projekt menüben. Ezután az **API-k & szolgáltatások**területen válassza a **OAuth-beleegyezési képernyő**lehetőséget.

4. Válassza a **külső**lehetőséget, majd válassza a **Létrehozás**lehetőséget. 
5. Az **OAuth-beleegyezés képernyőn**adja meg az **alkalmazás nevét**. (Hagyja meg a többi beállítást.)

   ![Képernyőfelvétel a Google OAuth-beleegyezési képernyő beállításáról](media/google-federation/google-oauth-consent-screen.png)

6. Görgessen a **jóváhagyott tartományok** szakaszhoz, és adja meg a microsoftonline.com.

   ![A jogosultsággal rendelkező tartományok szakaszt bemutató képernyőkép](media/google-federation/google-oauth-authorized-domains.png)

7. Kattintson a **Mentés** gombra.

8. Adja meg a **hitelesítő adatokat**. A **hitelesítő adatok létrehozása** menüben válassza a **OAUTH ügyfél-azonosító**elemet.

   ![Képernyőfelvétel a Google API-k hitelesítő adatok létrehozása lehetőségéről](media/google-federation/google-api-credentials.png)

9. Az **alkalmazás típusa**területen válassza a **webalkalmazás**elemet, majd az **engedélyes átirányítási URI**-k területen adja meg a következő URI-ket:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(hol `<directory id>` van a címtár-azonosítója)
   
     > [!NOTE]
     > A címtár-azonosító megkereséséhez nyissa meg a https://portal.azure.com elemet, és a **Azure Active Directory**alatt válassza a **Tulajdonságok** lehetőséget, és másolja a **címtár-azonosítót**.

   ![A jóváhagyott átirányítási URI-k szakaszt ábrázoló képernyőkép](media/google-federation/google-create-oauth-client-id.png)

10. Válassza a **Létrehozás** lehetőséget. Másolja ki az ügyfél-azonosítót és az ügyfél titkát, amelyet akkor fog használni, amikor hozzáadja az identitás-szolgáltatót az Azure AD-portálon.

   ![A OAuth ügyfél-AZONOSÍTÓját és az ügyfél titkát ábrázoló képernyőkép](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2. lépés: a Google-összevonás konfigurálása az Azure AD-ben 
Most be kell állítania a Google ügyfél-azonosítót és az ügyfél titkát, vagy megadhatja azt az Azure AD-portálon vagy a PowerShell használatával. Ügyeljen arra, hogy a Google összevonási konfigurációját egy Gmail-címen hívja meg, és próbálja meg beváltani a meghívót Google-Fiókjával. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>A Google Federation konfigurálása az Azure AD-portálon 
1. Nyissa meg az [Azure Portalt](https://portal.azure.com). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **külső identitások**lehetőséget.
3. Válassza a **minden identitás szolgáltató**lehetőséget, majd kattintson a **Google** gombra.
4. Adjon meg egy nevet. Ezután adja meg a korábban beszerzett ügyfél-azonosítót és az ügyfél-titkot. Kattintson a **Mentés** gombra. 

   ![Képernyőfelvétel a Google Identity Provider hozzáadása oldalról](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>A Google-összevonás konfigurálása a PowerShell használatával
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa a következő parancsot: `Connect-AzureAD` .
3. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
4. Futtassa az alábbi parancsot: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Használja az ügyfél-azonosítót és az ügyfél titkos kulcsát az "1. lépés: Google fejlesztői projekt konfigurálása" című témakörben létrehozott alkalmazásból. További információt a [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) című cikkben talál. 
 
## <a name="how-do-i-remove-google-federation"></a>Hogyan eltávolítja a Google Federation szolgáltatást?
A Google összevonási telepítőt törölheti. Ha így tesz, a Google vendég felhasználóinak, akik már beváltották a meghívót, nem tudnak majd bejelentkezni, de ismét hozzáférhetnek az erőforrásokhoz úgy, hogy törlik őket a címtárból, és újra meghívja őket. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>A Google Federation törlése az Azure AD-portálon: 
1. Nyissa meg az [Azure Portalt](https://portal.azure.com). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **külső identitások**lehetőséget.
3. Válassza ki **az összes identitás szolgáltatót**.
4. A **Google** sorban válassza a helyi menüt (**...**), majd válassza a **Törlés**lehetőséget. 
   
   ![A közösségi identitás-szolgáltató törlési beállítását ábrázoló képernyőkép](media/google-federation/google-social-identity-providers.png)

1. A törlés megerősítéséhez válassza az **Igen** lehetőséget. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Google-összevonás törlése a PowerShell használatával: 
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. A `Connect-AzureAD` parancs futtatása.  
4. A bejelentkezési üzenetben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
5. Írja be a következő parancsot:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > További információ: [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
