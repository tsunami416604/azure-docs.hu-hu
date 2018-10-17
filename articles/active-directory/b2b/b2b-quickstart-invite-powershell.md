---
title: Rövid útmutató vendégfelhasználó felvételéhez PowerShell használatával az Azure Active Directory B2B együttműködéshez | Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan küldhet az Azure AD B2B együttműködés külső felhasználó számára meghívót a PowerShell használatával.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.openlocfilehash: f0dc63d84ec7583e721b116b450c890d46524622
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986562"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Rövid útmutató: Vendégfelhasználó hozzáadása PowerShell-lel

Az Azure Active Directory B2B együttműködés sokféle lehetőséget nyújt a külső partnerek alkalmazásokba és szolgáltatásokba történő meghívására. Az előző rövid útmutatóban láthattuk, hogyan lehet vendégfelhasználókat közvetlenül az Azure Active Directory admin portálon felvenni. Felhasználók felvételéhez a PowerShell is használható, akár egyenként, akár kötegben. Ebben a rövid útmutatóban a New-AzureADMSInvitation paranccsal adunk új vendég felhasználót az Azure bérlőhöz.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="install-the-latest-azureadpreview-module"></a>A legújabb AzureADPreview modul telepítése
Győződjön meg róla, hogy az Azure AD PowerShell Graph modulhoz (AzureADPreview) legújabb verzióját telepíti. 

Először ellenőrizze, melyik modulokat telepítette. Nyissa meg a Windows PowerShellt rendszergazdaként (Futtatás rendszergazdaként), majd futtassa a következő parancsot:
 
````powershell  
Get-Module -ListAvailable AzureAD*
````

Ha az AzureADPreview modul anélkül megjelenik anélkül, hogy újabb verzióra figyelmeztető üzenet jelenne meg, készen van. Ellenkező esetben a kimenet alapján, tegye a következők egyikét:

- Ha nem kapott eredményt, a következő parancs futtatásával telepítse az AzureADPreview modult:
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- Ha az eredmények között csak az Azure AD-modul jelenik meg, a következő parancsok futtatásával telepítse az AzureADPreview-modult: 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- Ha az eredmények között csak az AzureADPreview-modul jelenik meg, de kap egy üzenetet, amely jelzi, hogy van újabb verziója, futtassa a következő parancsokat a modul frissítésére: 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ````

Előfordulhat, hogy kap egy kérdést, hogy a modult nem megbízható tárházból telepíti. Ez akkor fordul elő, ha korábban még nem állította be megbízható tárháznak a PSGallery tárházat. Nyomja meg **Y**-t a modul telepítéséhez.

### <a name="get-a-test-email-account"></a>Teszt e-mail-fiók létrehozása

Szüksége lesz egy próba e-mail fiókra, ahova a meghívót küldheti. A fióknak a szervezeten kívülinek kell lennie. Bármilyen típusú fiókot használhat, közösségi fiókot is például gmail.com vagy Outlook.com-os e-mail címet.

## <a name="sign-in-to-your-tenant"></a>Bejelentkezés bérlőhöz

Futtassa a következő parancsot a bérlő tartományhoz való csatlakozáshoz:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Például: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Amikor a rendszer erre kéri, adja meg a hitelesítő adatait.

## <a name="send-an-invitation"></a>Meghívó küldése

1. A teszt e-mail fiókba meghívó küldéséhez, futtassa a következő PowerShell-parancsot (a **„Sanda”** és **sanda@fabrikam.com** helyett írja be a saját e-mail fiókjához tartozó nevet és e-mail címet): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.azure.com -SendInvitationMessage $true
   ```
2. A parancs elküldi a meghívót a megadott e-mail-címre. Ellenőrizze a kimenetet, aminek a következőképpen kell kinéznie:

   ![Felhasználói elfogadásra várakozást mutató PowerShell kimenet](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>A felhasználó létezésének ellenőrzése a címtárban

1. Annak ellenőrzésére, hogy a meghívott felhasználót hozzáadták-e az Azure AD-hoz, futtassa a következő parancsot:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Ellenőrizze, hogy a meghívott felhasználó szerepel-e a listán, egyszerű felhasználónévvel (UPN) a következő formátumban: *e-mail cím*#EXT #@*tartomány*. Például *sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com*, ahol a contoso.onmicrosoft.com a szervezet, ahonnan a meghívót küldte.

   ![A vendégfelhasználó felvételét mutató PowerShell kimenet](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti a tesztfelhasználói fiókot a címtárban. A felhasználói fiók törléséhez a következő parancsot futtassa:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Például:`Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egyetlen vendégfelhasználót hívtunk meg és adtunk hozzá a címtárhoz a PowerShell használatával. Ezután megnézzük, hogyan lehet a PowerShell segítségével vendégfelhasználókat tömegesen meghívni.

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure AD B2B együttműködő felhasználók tömeges meghívása](tutorial-bulk-invite.md)
