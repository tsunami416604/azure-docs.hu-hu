---
title: A Google identitás-szolgáltatóként való hozzáadása a B2B-Azure Active Directoryhoz | Microsoft Docs
description: A Google összevonása lehetővé teszi a vendég felhasználók számára, hogy saját Gmail-fiókjával jelentkezzenek be az Azure AD-alkalmazásokba
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b26679542753d5fb429c33e4220c23a3937c5cb
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430441"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users-preview"></a>Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói számára (előzetes verzió)

|     |
| --- |
| A Google Federation a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

A Google-nal való összevonás beállításával engedélyezheti a meghívott felhasználóknak, hogy saját Gmail-fiókkal jelentkezzenek be a megosztott alkalmazásaiba és erőforrásaiba anélkül, hogy Microsoft-fiókokat (MSAs) vagy Azure AD-fiókokat kellene létrehozniuk. A Google Federation kifejezetten a Gmail-felhasználók számára készült. A G Suite-tartományokkal való összevonása használja helyette a [közvetlen összevonási funkciót](direct-federation.md) .
> [!NOTE]
> A Google Guest Users szolgáltatásnak be kell jelentkeznie egy olyan hivatkozással, amely tartalmazza a bérlői környezetet (például `https://myapps.microsoft.com/?tenantid=<tenant id>` vagy `https://portal.azure.com/<tenant id>`, vagy egy ellenőrzött tartomány esetén `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Az alkalmazásokra és az erőforrásokra mutató közvetlen hivatkozásokat is használhatja, amennyiben azok tartalmazzák a bérlői környezetet. A vendég felhasználók jelenleg nem tudnak bejelentkezni a bérlői kontextus nélküli végpontok használatával. Ha például a `https://myapps.microsoft.com`, `https://portal.azure.com` vagy a Teams Common végpontot használja, a rendszer hibát eredményez.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Mi a Google-felhasználó tapasztalata?
Amikor meghívót küld egy Google Gmail-felhasználó számára, a vendég felhasználónak el kell érnie a megosztott alkalmazásait vagy erőforrásait egy olyan hivatkozás használatával, amely tartalmazza a bérlői környezetet. A felhasználói élmény attól függően változhat, hogy már be vannak jelentkezve a Google-ba:
  - Ha a vendég felhasználó nincs bejelentkezve a Google-ba, a rendszer felszólítja, hogy jelentkezzen be a Google-ba.
  - Ha a vendég felhasználó már be van jelentkezve a Google-ba, a rendszer kérni fogja a használni kívánt fiók kiválasztását. Ki kell választania azt a fiókot, amellyel meghívja őket.

Ha a vendég felhasználó a "fejléc túl hosszú" hibaüzenetet látja, megpróbálkozhat a cookie-k törlésével, vagy megnyithat egy privát vagy inkognitóban-ablakot, és próbálkozhat újra a bejelentkezéssel.

![A Google bejelentkezési oldalát bemutató képernyőkép](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>1\. lépés: a Google fejlesztői projekt konfigurálása
Először hozzon létre egy új projektet a Google Developers konzolon egy ügyfél-azonosító és egy ügyfél-titok beszerzéséhez, amelyet később hozzáadhat az Azure AD-hez. 
1. Nyissa meg a Google API-kat https://console.developers.google.com címen, és jelentkezzen be Google-Fiókjával. Javasoljuk, hogy a megosztott csapat Google-fiókját használja.
2. Hozzon létre egy új projektet: az irányítópulton válassza a **projekt létrehozása**lehetőséget, majd válassza a **Létrehozás**lehetőséget. Az új projekt lapon adja meg a **projekt nevét**, majd válassza a **Létrehozás**lehetőséget.
   
   ![Képernyőfelvétel: a Google új projekt oldalát jeleníti meg](media/google-federation/google-new-project.png)

3. Győződjön meg arról, hogy az új projekt ki van választva a projekt menüben. Ezután nyissa meg a menüt a bal felső sarokban, és válassza az API-k **& Services** > **hitelesítő adatokat**.

   ![Képernyőfelvétel a Google API hitelesítő adatai lehetőségről](media/google-federation/google-api.png)
 
4. Válassza a **OAuth-beleegyezés képernyő** fület, és adja meg az **alkalmazás nevét**. (Hagyja meg a többi beállítást.)

   ![Képernyőfelvétel a Google OAuth-beleegyezési képernyő beállításáról](media/google-federation/google-oauth-consent-screen.png)

5. Görgessen a **jóváhagyott tartományok** szakaszhoz, és adja meg a microsoftonline.com.

   ![A jogosultsággal rendelkező tartományok szakaszt bemutató képernyőkép](media/google-federation/google-oauth-authorized-domains.png)

6. Kattintson a **Mentés** gombra.

7. Válassza a **hitelesítő adatok** fület. A **hitelesítő adatok létrehozása** menüben válassza a **OAUTH ügyfél-azonosító**elemet.

   ![Képernyőfelvétel a Google API-k hitelesítő adatok létrehozása lehetőségéről](media/google-federation/google-api-credentials.png)

8. Az **alkalmazás típusa**területen válassza a **webalkalmazás**elemet, majd az **engedélyes átirányítási URI**-k területen adja meg a következő URI-ket:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(ahol a `<directory id>` a címtár-azonosító)
   
     > [!NOTE]
     > A könyvtár AZONOSÍTÓjának megkereséséhez nyissa meg a https://portal.azure.com elemet, majd a **Azure Active Directory**területen válassza a **Tulajdonságok** lehetőséget, és másolja a **címtár-azonosítót**.

   ![A jóváhagyott átirányítási URI-k szakaszt ábrázoló képernyőkép](media/google-federation/google-create-oauth-client-id.png)

9. Kattintson a **Létrehozás** gombra. Másolja ki az ügyfél-azonosítót és az ügyfél titkát, amelyet akkor fog használni, amikor hozzáadja az identitás-szolgáltatót az Azure AD-portálon.

   ![A OAuth ügyfél-AZONOSÍTÓját és az ügyfél titkát ábrázoló képernyőkép](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2\. lépés: a Google-összevonás konfigurálása az Azure AD-ben 
Most be kell állítania a Google ügyfél-azonosítót és az ügyfél titkát, vagy megadhatja azt az Azure AD-portálon vagy a PowerShell használatával. Ügyeljen arra, hogy a Google összevonási konfigurációját egy Gmail-címen hívja meg, és próbálja meg beváltani a meghívót Google-Fiókjával. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>A Google Federation konfigurálása az Azure AD-portálon 
1. Nyissa meg az [Azure Portal](https://portal.azure.com). A bal oldali panelen válassza az **Azure Active Directory** gombot. 
2. Válassza a **szervezeti kapcsolatok**lehetőséget.
3. Válassza az **identitás-szolgáltatók**lehetőséget, majd kattintson a **Google** gombra.
4. Adjon meg egy nevet. Ezután adja meg a korábban beszerzett ügyfél-azonosítót és az ügyfél-titkot. Kattintson a **Mentés** gombra. 

   ![Képernyőfelvétel a Google Identity Provider hozzáadása oldalról](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>A Google-összevonás konfigurálása a PowerShell használatával
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa a következő parancsot: `Connect-AzureAD`.
3. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
4. Futtassa az alábbi parancsot: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Használja az ügyfél-azonosítót és az ügyfél titkos kulcsát az "1. lépés: Google fejlesztői projekt konfigurálása" című témakörben létrehozott alkalmazásból. További információt a [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) című cikkben talál. 
 
## <a name="how-do-i-remove-google-federation"></a>Hogyan eltávolítja a Google Federation szolgáltatást?
A Google összevonási telepítőt törölheti. Ha így tesz, a Google vendég felhasználóinak, akik már beváltották a meghívót, nem tudnak majd bejelentkezni, de ismét hozzáférhetnek az erőforrásokhoz úgy, hogy törlik őket a címtárból, és újra meghívja őket. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>A Google Federation törlése az Azure AD-portálon: 
1. Nyissa meg az [Azure Portal](https://portal.azure.com). A bal oldali panelen válassza az **Azure Active Directory** gombot. 
2. Válassza a **szervezeti kapcsolatok**lehetőséget.
3. Válassza az **identitás-szolgáltatók**elemet.
4. A **Google** sorban válassza a helyi menüt ( **...** ), majd válassza a **Törlés**lehetőséget. 
   
   ![A közösségi identitás-szolgáltató törlési beállítását ábrázoló képernyőkép](media/google-federation/google-social-identity-providers.png)

1. A törlés megerősítéséhez válassza az **Igen** lehetőséget. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Google-összevonás törlése a PowerShell használatával: 
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa az `Connect-AzureAD` parancsot.  
4. A bejelentkezési üzenetben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
5. Írja be a következő parancsot:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > További információ: [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
