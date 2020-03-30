---
title: A Google hozzáadása a B2B identitásszolgáltatójaként – Azure AD
description: A Google-lal együttműködve lehetővé teszi a vendégfelhasználók számára, hogy saját Gmail-fiókjukkal jelentkezzenek be az Azure AD-alkalmazásokba
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c18e48c27942c7bea47931ec79a31af941064e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126656"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>A Google hozzáadása identitásszolgáltatóként a B2B vendégfelhasználók számára

Ha a Google-lal összevonást hoz létre, engedélyezheti a meghívott felhasználóknak, hogy saját Gmail-fiókjukkal jelentkezzenek be a megosztott alkalmazásokba és erőforrásokba anélkül, hogy Microsoft-fiókokat (MSA-kat) kellene létrehozniuk. 

> [!NOTE]
> A Google-szövetség kifejezetten a Gmail-felhasználók számára készült. A G Suite-tartományokkal való összevonáshoz használja a [közvetlen összevonási funkciót.](direct-federation.md)

## <a name="what-is-the-experience-for-the-google-user"></a>Mi a Google-felhasználó tapasztalata?
Amikor meghívót küld egy Google Gmail-felhasználónak, a vendégfelhasználónak a bérlői környezetet tartalmazó hivatkozás használatával kell hozzáférnie a megosztott alkalmazásokhoz vagy erőforrásokhoz. Tapasztalataik attól függően változnak, hogy már be vannak-e jelentkezve a Google-ba:
  - Ha a vendégfelhasználó nincs bejelentkezve a Google-ba, a rendszer kéri, hogy jelentkezzen be a Google-ba.
  - Ha a vendégfelhasználó már be van jelentkezve a Google-ba, a rendszer kéri, hogy válassza ki a használni kívánt fiókot. Ki kell választaniuk a meghívott fiókot.

Ha a vendégfelhasználó "túl hosszú fejléc" hibaüzenetet lát, megpróbálhatja törölni a cookie-kat, vagy megnyithat egy privát vagy inkognitóablakot, és megpróbálhat újra bejelentkezni.

![Képernyőkép a Google bejelentkezési oldalának](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Korlátozások

A Teams teljes mértékben támogatja a Google vendégfelhasználóit minden eszközön. A Google-felhasználók egy közös végpontról `https://teams.microsoft.com`jelentkezhetnek be a Teamsbe, például a .

Előfordulhat, hogy más alkalmazások közös végpontjai nem támogatják a Google-felhasználókat. A Google vendégfelhasználóinak a bérlői adatokat tartalmazó hivatkozáson keresztül kell bejelentkezniük. Az alábbi parancsok példák:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Ha a Google vendégfelhasználói megpróbálnak `https://myapps.microsoft.com` `https://portal.azure.com`használni egy olyan hivatkozást, mint vagy a, hibaüzenetet kapnak.

A Google vendégfelhasználóinak közvetlen hivatkozást is adhat egy alkalmazáshoz vagy erőforráshoz, `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`feltéve, hogy ez a hivatkozás tartalmazza a bérlői adatokat, például . 

## <a name="step-1-configure-a-google-developer-project"></a>1. lépés: Google fejlesztői projekt konfigurálása
Először hozzon létre egy új projektet a Google Developers Console-ban egy ügyfélazonosító és egy ügyféltitok beszerzéséhez, amelyet később hozzáadhat az Azure AD-hez. 
1. Nyissa meg a Google https://console.developers.google.comAPI-kat a számon, és jelentkezzen be Google-fiókjával. Azt javasoljuk, hogy megosztott csapatGoogle-fiókot használjon.
2. Új projekt létrehozása: Az irányítópulton válassza a **Projekt létrehozása**lehetőséget, majd a **Létrehozás**lehetőséget. Az Új projekt lapon adja meg a **Projekt nevét,** majd válassza a **Létrehozás gombot.**
   
   ![Képernyőkép a Google új projektoldalának](media/google-federation/google-new-project.png)

3. Győződjön meg arról, hogy az új projekt ki van jelölve a projekt menüben. Ezután **az API-k & Szolgáltatások csoportban**válassza az **OAuth consent screen (OAuth consent screen ) (OAuth consent screen ) (OAuth consent screen**) (Api-k & Szolgáltatások

4. Válassza **a Külső**lehetőséget, majd a **Létrehozás lehetőséget.** 
5. Az **OAuth hozzájárulási képernyőn**adja meg **az Alkalmazás nevét.** (Hagyja meg a többi beállítást.)

   ![Képernyőkép a Google OAuth hozzájárulási képernyőjéről](media/google-federation/google-oauth-consent-screen.png)

6. Görgessen az **Engedélyezett tartományok** szakaszhoz, és írja be microsoftonline.com.

   ![Az Engedélyezett tartományok szakaszról készült képernyőkép](media/google-federation/google-oauth-authorized-domains.png)

7. Kattintson a **Mentés** gombra.

8. Válassza **a Hitelesítő adatok lehetőséget.** A **Hitelesítő adatok létrehozása** menüben válassza az **OAuth ügyfélazonosítót.**

   ![Képernyőkép a Google API-k hitelesítő adatok létrehozásá beállítását ábrázoló képernyőkép](media/google-federation/google-api-credentials.png)

9. Az **Alkalmazástípusa csoportban**válassza a **Webalkalmazás**lehetőséget, majd az **Engedélyezett átirányítási URI-k**csoportban adja meg a következő URI-kat:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(hol `<directory id>` van a könyvtárazonosítója)
   
     > [!NOTE]
     > A címtárazonosító megkereséséhez https://portal.azure.comnyissa meg a t, majd az **Azure Active Directory**csoportban válassza a **Tulajdonságok** lehetőséget, és másolja a **címtárazonosítót.**

   ![Képernyőkép az Engedélyezett átirányítási URI-k szakaszról](media/google-federation/google-create-oauth-client-id.png)

10. Kattintson a **Létrehozás** gombra. Másolja az ügyfél-azonosítót és az ügyféltitkos kulcsot, amelyet akkor fog használni, amikor hozzáadja az identitásszolgáltatót az Azure AD portálon.

   ![Az OAuth ügyfélazonosítót és az ügyféltitkoskulcsot ábrázoló képernyőkép](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2. lépés: A Google-összevonás konfigurálása az Azure AD-ben 
Most beállíthatja a Google ügyfél-azonosító és az ügyfél titkos, akár az Azure AD portálon, vagy a PowerShell használatával. Mindenképpen tesztelje a Google összevonási konfigurációját úgy, hogy gmailes címet használ, és megpróbálja beváltani a meghívót a meghívott Google-fiókkal. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>A Google-összevonás konfigurálása az Azure AD portálon 
1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **Szervezeti kapcsolatok lehetőséget.**
3. Válassza **az Identitásszolgáltatók**lehetőséget, majd kattintson a **Google** gombra.
4. Írjon be egy nevet. Ezután adja meg a korábban megszerzett ügyfélazonosítót és ügyféltitkot. Kattintson a **Mentés** gombra. 

   ![A Google-identitásszolgáltató hozzáadása lapjáról képernyőkép](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>A Google-összevonás konfigurálása a PowerShell használatával
1. Telepítse az Azure AD PowerShell graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa a `Connect-AzureAD`következő parancsot: .
3. A bejelentkezési kérdésnél jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
4. Futtassa az alábbi parancsot: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Használja az ügyfélazonosítót és az ügyféltitkos kulcsot az "1. További információ: [A New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) cikkben. 
 
## <a name="how-do-i-remove-google-federation"></a>Hogyan távolíthatom el a Google-összevonást?
Törölheti a Google összevonási beállításokat. Ha így tesz, a meghívót már beváltott Google-vendégfelhasználók nem tudnak bejelentkezni, de ismét hozzáférést adhat nekik az erőforrásaihoz, ha kiveszi őket a címtárból, és újra meghívja őket. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>A Google-összevonás törlése az Azure AD portálon: 
1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **Szervezeti kapcsolatok lehetőséget.**
3. Válassza **az Identitásszolgáltatók lehetőséget.**
4. A **Google-sorban** válassza a helyi menüt (**...**), majd a **Törlés parancsot.** 
   
   ![Képernyőkép a közösségi identitásszolgáltató Törlés beállításával](media/google-federation/google-social-identity-providers.png)

1. A törlés megerősítéséhez válassza az **Igen** lehetőséget. 

### <a name="to-delete-google-federation-by-using-powershell"></a>A Google-összevonás törlése a PowerShell használatával: 
1. Telepítse az Azure AD PowerShell graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa az `Connect-AzureAD` parancsot.  
4. A bejelentkezési kérdésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
5. Írja be a következő parancsot:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > További információ: [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
