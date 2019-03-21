---
title: Egyszeri jelszó hitelesítési Vendég B2B - felhasználók Azure Active Directory |} A Microsoft Docs
description: Hogyan használható az e-mailben kapott egyszeri jelszót Microsoft-fiók nélkül B2B vendég felhasználók hitelesítésére.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 1/25/2019
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5234674bc41e1f5bfa260dfc32042a3345d9f7
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294825"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-mailben kapott egyszeri jelszót hitelesítés (előzetes verzió)

|     |
| --- |
| E-mailben kapott egyszeri jelszót az Azure Active Directory nyilvános előzetes verziójú funkció. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Az e-mailben kapott egyszeri jelszót funkció nem hitelesíthetők, más módon, például az Azure ad-ben, egy Microsoft-fiókkal (MSA) vagy a Google összevonási B2B vendégfelhasználók hitelesíti. Egyszeri jelszó hitelesítéssel hiba esetén nem kell egy Microsoft-fiók létrehozásához. Ha a vendégfelhasználó meghívó visszaváltja, vagy egy megosztott erőforráshoz fér hozzá, igényelhetik egy ideiglenes kódra, amely az e-mail címre. Ezután ezek meg ezt a kódot, a folytatáshoz jelentkezzen be.

Ez a funkció jelenleg elérhető előzetes verzióban (lásd: [az előzetes verzióra való engedélyezés](#opting-in-to-the-preview) alább). A minta után ez a funkció, lesz kapcsolva alapértelmezés szerint az összes bérlőre vonatkozóan.

> [!NOTE]
> Egyszer használatos jelszót kell bejelentkeznek egy hivatkozással, amely tartalmazza a bérlő környezetben (például `https://myapps.microsoft.com/?tenantid=<tenant id>` vagy `https://portal.azure.com/<tenant id>`, vagy egy ellenőrzött tartomány esetén `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Alkalmazások és erőforrások mutató közvetlen hivatkozásokat is működik, amíg a bérlő környezet tartalmazzák. Vendégfelhasználók nem jelentkezzen be a végpontok, amelyeken nincs bérlői környezet. Például `https://myapps.microsoft.com`, `https://portal.azure.com`, vagy a csapatok közös végponti egy hibát eredményez. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Felhasználói élmény a kapott egyszer használatos jelszót vendégfelhasználók számára
Egyszeri jelszó hitelesítéssel a vendégfelhasználó beválthatja a meghívást közvetlen hivatkozására kattintva, vagy a meghívó e-mail használatával. Mindkét esetben a böngészőben egy üzenet azt jelzi, hogy egy kódot küldeni a Vendég felhasználó e-mail címét. A Vendég felhasználó **kód küldése**:
 
   ![Képernyőfelvétel a kód küldése gombra:](media/one-time-passcode/otp-send-code.png)
 
PIN-kód kerül a felhasználó e-mail címét. A felhasználó a PIN-kód beolvassa az e-mailt, és megadja azt a böngészőablakban:
 
   ![Képernyőfelvétel: a Enter znaková stránka](media/one-time-passcode/otp-enter-code.png)
 
A Vendég felhasználói most már hitelesítve van, és tekintse meg a megosztott erőforrás vagy továbbra is jelentkezik be. 

> [!NOTE]
> Egyszeri PIN-kódok 30 percig érvényesek. Várjon néhány percig a konkrét egyszeri jelszó már nem érvényes, és a felhasználó egy új kell igényelnie. A felhasználói munkamenetek 24 óra után lejárnak. Ezt követően a vendégfelhasználó új PIN-kódot kap, amikor az erőforrás elérésekor. Munkamenet biztosítja a fokozott biztonság érdekében, különösen akkor, ha a Vendég felhasználó elhagyja a vállalatot, vagy már nincs szüksége a hozzáférés.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Ha a vendégfelhasználó egyszer használatos jelszót be?

Ha a vendégfelhasználó meghívó visszaváltja, vagy a velük megosztott erőforráshoz való kapcsolat használja, kap egy egyszeri jelszót, ha:
- Nem rendelkeznek Azure AD-fiók 
- Nem rendelkeznek a Microsoft-fiókkal 
- A meghívó bérlőben nem állította be a Google-összevonási @gmail.com és @googlemail.com felhasználók 

A meghívó időpontjában a rendszer nem jelzi, hogy az Ön figyelmét felhasználói hitelesítés egyszeri jelszót használja. Azonban ha a Vendég felhasználó jelentkezik be, egyszer használatos jelszót hitelesítéssel kell a tartalék módszer, ha más hitelesítési módszerek használhatók. 

Megtekintheti a vendégfelhasználók számára az Azure Portalon egyszeri PIN-kódok a hitelesítéshez **Azure Active Directory** > **szervezeti kapcsolatok**  >   **Más szervezetek**.

![Képernyőfelvétel: az adatforrás-értéke az egyszeri Jelszavas egyszeri jelszó felhasználó](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Ha egy felhasználó visszaváltja egy egyszeri jelszót, és később beszerzi az MSA, az Azure AD-fiókot vagy más összevont fiók, azok továbbra is hitelesíthető egy egyszeri jelszót. Ha meg szeretné frissíteni a hitelesítési módszert, a Vendég felhasználói fiókhoz törölheti, és újra meg kell hívnia, azokat.

### <a name="example"></a>Példa
A vendégfelhasználók alexdoe@gmail.com felkérik, hogy a Fabrikam, amely nem rendelkezik a Google összevonási beállítása. Alex nem rendelkezik Microsoft-fiókkal. Akkor jelenik meg egy egyszeri jelszót a hitelesítéshez.

## <a name="opting-in-to-the-preview"></a>Az előzetes verzióra való engedélyezés 
A választható művelet érvénybe léptetéséhez néhány percbe is telhet. Ezt követően csak újonnan meghívott felhasználók, akik megfelelnek a fenti feltételek hitelesítés egyszeri jelszót fogja használni. Vendég felhasználók, akik korábban már beváltott egy meghívást arra, hogy ugyanazt a hitelesítési módszert használja továbbra is.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Jelentkezzen be az Azure AD-portál használatával
1.  Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD globális rendszergazdaként.
2.  A navigációs panelen válassza ki **Azure Active Directory**.
3.  A **kezelés**válassza **szervezeti kapcsolatok**.
4.  Válassza ki **beállítások**.
5.  A **E-mail egyszeri jelszót vendégek (előzetes verzió) engedélyezése**válassza **Igen**.
 
### <a name="to-opt-in-using-powershell"></a>Jelentkezzen be a PowerShell használatával

Először is szüksége az Azure AD PowerShell (AzureADPreview) Graph-modul legújabb verziójának telepítéséhez. Ezután fogja meghatározni e B2B házirendek már léteznek, és futtassa a megfelelő parancsokat.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Előfeltétel: A legújabb AzureADPreview modul telepítése
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

Előfordulhat, hogy kap egy kérdést, hogy a modult nem megbízható tárházból telepíti. Ez akkor fordul elő, ha korábban még nem állította be megbízható tárháznak a PSGallery tárházat. Nyomja meg az **Y-t** a modul telepítéséhez.

#### <a name="check-for-existing-policies-and-opt-in"></a>Keresse meg a meglévő szabályzatokat, és engedélyezve van a hibajelentések

Ezután ellenőrizze, hogy egy B2BManagementPolicy jelenleg létezik-e a következő futtatásával:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- A kimenet értéke HAMIS, ha a szabályzat jelenleg nem létezik. Hozzon létre egy új B2BManagementPolicy, és engedélyezve van a hibajelentések az előzetes verzió a következő futtatásával:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- A kimenet értéke igaz, ha a B2BManagementPolicy házirend jelenleg létezik. A házirend módosítása és a Preview-ra, futtassa a következő részt:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Megtagadja az engedélyezés után az előzetes verzió
Eltarthat néhány percig, a letiltási művelet érvénybe léptetéséhez. Ha kikapcsolja az előzetes verzióra, egyszer használatos jelszót beváltott bármely vendég felhasználók nem jelentkezhetnek be. A vendégfelhasználó törölheti és újra meg kell hívnia a felhasználó számára, jelentkezzen be újra egy másik hitelesítési módszert.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Az előzetes verzióra az Azure AD portálon kikapcsolása
1.  Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD globális rendszergazdaként.
2.  A navigációs panelen válassza ki **Azure Active Directory**.
3.  A **kezelés**válassza **szervezeti kapcsolatok**.
4.  Válassza ki **beállítások**.
5.  A **E-mail egyszeri jelszót vendégek (előzetes verzió) engedélyezése**válassza **nem**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Tiltsa le a minta PowerShell-lel
Telepítse a legújabb AzureADPreview modulban, ha még nem rendelkezik (lásd: [Előfeltételek: Telepítse a legújabb AzureADPreview modulban](#prerequisite-install-the-latest-azureadpreview-module) fent). Ezután gondoskodjon arról, hogy a kapott egyszer használatos jelszót előzetes házirend jelenleg a következő futtatásával:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

A kimenet értéke igaz, ha kikapcsolja az előzetes verzió a következő futtatásával:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

