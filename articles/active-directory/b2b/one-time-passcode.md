---
title: Egyszeri jelkód-hitelesítés B2B vendégfelhasználók számára – Azure AD
description: Az E-mail egyszeri jelkód használata a B2B vendégfelhasználók hitelesítéséhez Microsoft-fiók nélkül.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d897bb983eb06baa4f1573f1f875eea8bb8afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263373"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-mail küldése egyszeri jelkód-hitelesítésről (előzetes verzió)

|     |
| --- |
| Az egyszeri e-mail-kód az Azure Active Directory nyilvános előzetes verziójú szolgáltatása. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ez a cikk bemutatja, hogyan engedélyezheti az e-mail egyszeri jelkód-hitelesítést a B2B vendégfelhasználók számára. Az e-mail egyszeri jelkód funkció hitelesíti a B2B vendégfelhasználókat, ha nem hitelesíthetők más eszközökkel, például az Azure AD-vel, egy Microsoft-fiókkal (MSA) vagy a Google-összevonással. Az egyszeri jelkód-hitelesítéssel nincs szükség Microsoft-fiók létrehozására. Amikor a vendégfelhasználó bevált egy meghívót, vagy hozzáfér egy megosztott erőforráshoz, kérhet egy ideiglenes kódot, amelyet a rendszer az e-mail címére küld. Ezután beírják ezt a kódot a bejelentkezés folytatásához.

Ez a funkció jelenleg előzetes verzióban érhető el (lásd: Az alábbi [előzetes verzióra való feliratkozás).](#opting-in-to-the-preview) Az előzetes verzió után ez a funkció alapértelmezés szerint be lesz kapcsolva az összes bérlő esetében.

> [!NOTE]
> Az egyszeri jelkódú felhasználóknak a bérlői környezetet tartalmazó `https://myapps.microsoft.com/?tenantid=<tenant id>` hivatkozással kell bejelentkezniük (például vagy `https://portal.azure.com/<tenant id>`, vagy ellenőrzött tartomány `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`esetén). Az alkalmazásokhoz és erőforrásokhoz mutató közvetlen hivatkozások is működnek, amennyiben tartalmazzák a bérlői környezetet. A vendégfelhasználók jelenleg nem tudnak bejelentkezni olyan végpontok használatával, amelyek nem rendelkeznek bérlői környezetben. Ha például `https://myapps.microsoft.com` `https://portal.azure.com`a , vagy a Teams közös végpontot használja, az hibát eredményez. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Felhasználói élmény egyszeri jelkódú vendégfelhasználók számára
Az egyszeri jelkód-hitelesítéssel a vendégfelhasználó egy közvetlen hivatkozásra kattintva vagy a meghívó e-mail használatával válthatja be a meghívót. A böngészőben megjelenő üzenet mindkét esetben azt jelzi, hogy a rendszer kódot küld a vendégfelhasználó e-mail címére. A vendégfelhasználó a **Kód küldése**lehetőséget választja:
 
   ![A Kód küldése gombbal látható képernyőkép](media/one-time-passcode/otp-send-code.png)
 
A felhasználó e-mail címére a rendszer jelkódot küld. A felhasználó lekéri a jelszót az e-mailből, és beírja a böngészőablakba:
 
   ![Az Enter kódlapját ábrázoló képernyőkép](media/one-time-passcode/otp-enter-code.png)
 
A vendégfelhasználó most már hiteles, és láthatja a megosztott erőforrást, vagy folytathatja a bejelentkezést. 

> [!NOTE]
> Az egyszeri kódok 30 percig érvényesek. 30 perc elteltével az adott egyszeri jelkód már nem érvényes, és a felhasználónak újat kell kérnie. A felhasználói munkamenetek 24 óra elteltével lejárnak. Ezt követően a vendégfelhasználó kap egy új jelkódot, amikor hozzáférnek az erőforráshoz. A munkamenet lejárata nagyobb biztonságot nyújt, különösen akkor, ha egy vendégfelhasználó elhagyja a vállalatot, vagy már nincs szüksége hozzáférésre.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Mikor kap egy vendégfelhasználó egyszeri jelkódot?

Amikor egy vendégfelhasználó bevált egy meghívót, vagy egy vele megosztott erőforrásra mutató hivatkozást használ, egyszeri jelszót kap, ha:
- Nem rendelkeznek Azure AD-fiókkal 
- Nem rendelkeznek Microsoft-fiókkal 
- A meghívó bérlő nem állította @gmail.com be @googlemail.com a Google-összevonást a felhasználók és a felhasználók számára 

A meghívás időpontjában nincs arra utaló jel, hogy a meghívott felhasználó egyszeri jelkód-hitelesítést fog használni. De amikor a vendég felhasználó bejelentkezik, egyszeri jelkód-hitelesítés lesz a tartalék módszer, ha más hitelesítési módszer nem használható. 

Megtekintheti a vendég felhasználók, akik hitelesítik az egyszeri jelkódok az Azure Portalon az **Azure Active Directory** > **szervezeti kapcsolatok** > **felhasználók más szervezetekből.**

![Képernyőkép egy egyszeri jelkódhasználóról, aki nakja piszkotp forrásértékkel van elévülve](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Amikor egy felhasználó bevált egy egyszeri jelkódot, és később beszerez egy MSA-, Azure AD-fiókot vagy más összevont fiókot, továbbra is egy egyszeri jelkód használatával hitelesítve lesz. Ha frissíteni szeretné a hitelesítési módszert, törölheti a vendégfelhasználói fiókjukat, és újra meghívhatja őket.

### <a name="example"></a>Példa
A alexdoe@gmail.com vendégfelhasználó meghívást kap a Fabrikam-ba, amely nem rendelkezik google-összevonási beállítással. Alex nem rendelkezik Microsoft-fiókkal. Egyszeri jelszót kapnak a hitelesítéshez.

## <a name="opting-in-to-the-preview"></a>Feliratkozás az előnézetre 
Eltarthat néhány percig, amíg az opt-in művelet érvénybe lép. Ezt követően csak a fenti feltételeknek megfelelő újonnan meghívott felhasználók fogják használni az egyszeri jelkód-hitelesítést. Azok a vendégfelhasználók, akik korábban beváltottak egy meghívót, továbbra is ugyanazt a hitelesítési módszert fogják használni.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Az Azure AD-portál használatának letiltása
1.  Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) azure AD globális rendszergazdaként.
2.  A navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
3.  A **Kezelés csoportban**válassza a **Szervezeti kapcsolatok lehetőséget.**
4.  Válassza a **Beállítások lehetőséget.**
5.  Az **Egyszeri e-mail címkód engedélyezése a vendégeknek (előzetes verzió)** csoportban válassza az **Igen**lehetőséget.
 
### <a name="to-opt-in-using-powershell"></a>Engedélyezés a PowerShell használatával

Először telepítenie kell az Azure AD PowerShell graph modul (AzureADPreview) legújabb verzióját. Ezután meghatározza, hogy a B2B-házirendek már léteznek-e, és futtathatja a megfelelő parancsokat.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Előfeltétel: Telepítse a legújabb AzureADPreview modult
Először ellenőrizze, melyik modulokat telepítette. Nyissa meg a Windows PowerShellt rendszergazdaként (Futtatás rendszergazdaként), majd futtassa a következő parancsot:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Ha az AzureADPreview modul anélkül megjelenik anélkül, hogy újabb verzióra figyelmeztető üzenet jelenne meg, készen van. Ellenkező esetben a kimenet alapján, tegye a következők egyikét:

- Ha nem kapott eredményt, a következő parancs futtatásával telepítse az AzureADPreview modult:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Ha az eredmények között csak az Azure AD-modul jelenik meg, a következő parancsok futtatásával telepítse az AzureADPreview-modult: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Ha az eredmények között csak az AzureADPreview-modul jelenik meg, de kap egy üzenetet, amely jelzi, hogy van újabb verziója, futtassa a következő parancsokat a modul frissítésére: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Előfordulhat, hogy kap egy kérdést, hogy a modult nem megbízható tárházból telepíti. Ez akkor fordul elő, ha korábban még nem állította be megbízható tárháznak a PSGallery tárházat. Nyomja meg **Y**-t a modul telepítéséhez.

#### <a name="check-for-existing-policies-and-opt-in"></a>Meglévő házirendek ellenőrzése és engedélyezés

Ezután ellenőrizze, hogy létezik-e jelenleg B2BManagementPolicy a következő futtatásával:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Ha a kimenet hamis, a házirend jelenleg nem létezik. Hozzon létre egy új B2BManagementPolicy és opt-in az előnézet futtatásával a következő:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Ha a kimenet igaz, a B2BManagementPolicy házirend jelenleg létezik. A házirend frissítéséhez és az előzetes verzióhoz való bejelentkezéshez futtassa a következőket:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Az előzetes verzió letiltása a feliratkozás után
Az opt-out művelet érvénybe léptetése eltarthat néhány percig. Ha kikapcsolja az előnézetet, az egyszeri jelkódot beváltott vendégfelhasználók nem tudnak bejelentkezni. Törölheti a vendégfelhasználót, és újra meghívhatja a felhasználót, hogy egy másik hitelesítési módszerrel újra bejelentkezhessenek.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Az előzetes verzió kikapcsolása az Azure AD portálhasználatával
1.  Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) azure AD globális rendszergazdaként.
2.  A navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
3.  A **Kezelés csoportban**válassza a **Szervezeti kapcsolatok lehetőséget.**
4.  Válassza a **Beállítások lehetőséget.**
5.  Az **Egyszeri e-mail-kód engedélyezése a vendégeknek (előzetes verzió)** csoportban válassza a **Nem**lehetőséget.

### <a name="to-turn-off-the-preview-using-powershell"></a>Az előnézet kikapcsolása a PowerShell használatával
Telepítse a legújabb AzureADPreview modult, ha még nem rendelkezik vele [(lásd: Előfeltétel: Telepítse a fenti legújabb AzureADPreview modult).](#prerequisite-install-the-latest-azureadpreview-module) Ezután ellenőrizze, hogy az egyszeri jelkód-előzetes verzióra vonatkozó szabályzat jelenleg létezik-e a következők futtatásával:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Ha a kimenet igaz, a következő futtatásával válassza le az előnézetet:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

