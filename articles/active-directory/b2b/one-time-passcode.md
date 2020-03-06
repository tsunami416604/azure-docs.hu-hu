---
title: Egyszeri jelszavas hitelesítés a VÁLLALATKÖZI vendég felhasználói számára – Azure AD
description: E-mail egyszeri jelszó használata a B2B vendég-felhasználók hitelesítéséhez Microsoft-fiók nélkül.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376471"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-mail egyszeri jelszó-hitelesítés (előzetes verzió)

|     |
| --- |
| Az egyszeri e-mailes PIN-kód a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ez a cikk azt ismerteti, hogyan engedélyezhető a levelezés egyszeri jelszavas hitelesítése a B2B vendég felhasználói számára. Az egyszeri e-mail-jelszó funkció hitelesíti a B2B vendég felhasználóit, ha nem hitelesíthetők más módon, például az Azure AD-vel, a Microsoft-fiók (MSA) vagy a Google Federation szolgáltatással. Egyszeri jelszavas hitelesítés esetén nincs szükség Microsoft-fiók létrehozására. Ha a vendég felhasználó bevált egy meghívót, vagy egy megosztott erőforráshoz fér hozzá, ideiglenes kódot kérhet, amelyet a rendszer elküld az e-mail-címére. Ezután a kód beírásával folytathatja a bejelentkezést.

Ez a funkció jelenleg előzetes verzióként érhető el (lásd az alábbi [előnézetet](#opting-in-to-the-preview) ). Az előnézet után a szolgáltatás alapértelmezés szerint be lesz kapcsolva az összes bérlőnél.

> [!NOTE]
> Egyszeri jelszóval rendelkező felhasználóknak be kell jelentkezniük egy olyan hivatkozás használatával, amely tartalmazza a bérlői környezetet (például `https://myapps.microsoft.com/?tenantid=<tenant id>` vagy `https://portal.azure.com/<tenant id>`, vagy egy ellenőrzött tartomány esetén `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Az alkalmazásokra és az erőforrásokra mutató közvetlen hivatkozásokat is használhatja, amennyiben azok tartalmazzák a bérlői környezetet. A vendég felhasználók jelenleg nem tudnak bejelentkezni a bérlői kontextus nélküli végpontok használatával. A `https://myapps.microsoft.com`, `https://portal.azure.com`vagy a csapatok közös végpontjának használata például hibát eredményez. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Felhasználói élmény egyszeri jelszó vendég felhasználói számára
Egyszeri PIN-kód-hitelesítéssel a vendég felhasználó a közvetlen hivatkozásra kattintva vagy a meghívó e-mailben is beválthatja a meghívót. Mindkét esetben egy üzenet jelenik meg a böngészőben, amely azt jelzi, hogy a rendszer elküld egy kódot a vendég felhasználói e-mail-címére. A vendég felhasználó kiválasztja a **küldési kódot**:
 
   ![A kód küldése gombra mutató képernyőkép](media/one-time-passcode/otp-send-code.png)
 
A rendszer elküld egy jelszót a felhasználó e-mail-címére. A felhasználó lekéri a PIN-kódot az e-mailben, és beírja a böngészőablakba:
 
   ![A kód megadása lapot ábrázoló képernyőfelvétel](media/one-time-passcode/otp-enter-code.png)
 
A vendég felhasználó hitelesítése megtörtént, és láthatja a megosztott erőforrást, vagy folytathatja a bejelentkezést. 

> [!NOTE]
> Az egyszeri PIN-kódok 30 percig érvényesek. 30 perc elteltével az adott egyszeri PIN-kód már nem érvényes, és a felhasználónak újat kell igényelnie. A felhasználói munkamenetek 24 óra elteltével lejárnak. Ezt követően a vendég felhasználó új jelszót kap az erőforráshoz való hozzáféréskor. A munkamenet lejárata nagyobb biztonságot nyújt, különösen akkor, ha a vendég felhasználó elhagyja a vállalatot, vagy már nincs hozzáférése.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Mikor kap egyszeri jelszót a vendég felhasználó?

Ha egy vendég felhasználó bevált egy meghívót, vagy egy olyan erőforrásra mutató hivatkozást használ, amelyet megosztottak velük, akkor egyszeri jelszót kapnak, ha:
- Nem rendelkezik Azure AD-fiókkal 
- Nincs Microsoft-fiók 
- A meghívó bérlő nem állította be a Google-összevonást @gmail.com és @googlemail.com felhasználók számára 

A meghívás időpontjában nincs jelezve, hogy a meghívott felhasználó egyszer használatos hitelesítő adatokat fog használni. Ha azonban a vendég felhasználó bejelentkezik, egyszeri jelszó-hitelesítés lesz a tartalék módszer, ha más hitelesítési módszer nem használható. 

Megtekintheti azokat a vendég felhasználókat, akik a Azure Portal egyszer használatos PIN-kóddal hitelesítik magukat, **Azure Active Directory** > **szervezeti kapcsolatokat** > **más szervezetek felhasználóinak**.

![Képernyőfelvétel: egyszeri jelszóval rendelkező felhasználó, amely az OTP forrás értékét tartalmazza](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Amikor egy felhasználó egyszeri jelszót vált ki, és később beolvas egy MSA, egy Azure AD-fiókot vagy más összevont fiókot, a rendszer továbbra is egyszer használatba veszi a hitelesítő kódot. Ha frissíteni szeretné a hitelesítési módszert, törölheti a vendég felhasználói fiókját, és újból meghívhatja őket.

### <a name="example"></a>Példa
Vendég felhasználói alexdoe@gmail.com meghívja a fabrikam-t, amelyhez nincs beállítva a Google Federation. Alex nem rendelkezik Microsoft-fiók. Egy egyszeri jelszót kapnak a hitelesítéshez.

## <a name="opting-in-to-the-preview"></a>Az előnézet megválasztása 
Eltarthat néhány percig, amíg a beavatkozás érvénybe lép. Ezt követően csak az újonnan meghívott felhasználók használhatnak egyszeri jelszavas hitelesítést. Azok a vendég felhasználók, akik korábban beváltottak egy meghívót, továbbra is ugyanazt a hitelesítési módszert használják.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Az Azure AD-portál használatának engedélyezése
1.  Jelentkezzen be az [Azure Portalba](https://portal.azure.com/) Azure ad globális rendszergazdaként.
2.  A navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.
3.  A **kezelés**területen válassza a **szervezeti kapcsolatok**elemet.
4.  Válassza a **Beállítások**lehetőséget.
5.  Az **E-mail egyszeri jelszó engedélyezése a vendégek számára (előzetes verzió)** területen válassza az **Igen**lehetőséget.
 
### <a name="to-opt-in-using-powershell"></a>A PowerShell használatának engedélyezése

Először telepítenie kell az Azure AD PowerShell for Graph modul (AzureADPreview) legújabb verzióját. Ezután meghatározhatja, hogy a B2B-szabályzatok már léteznek-e, és futtatják-e a megfelelő parancsokat.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Előfeltétel: a legújabb AzureADPreview-modul telepítése
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

#### <a name="check-for-existing-policies-and-opt-in"></a>Meglévő szabályzatok keresése és az engedélyezése

Ezután ellenőrizze, hogy van-e B2BManagementPolicy jelenleg a következő futtatásával:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Ha a kimenet hamis, a szabályzat jelenleg nem létezik. Hozzon létre egy új B2BManagementPolicy, és a következő parancs futtatásával lépjen be az előnézetbe:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Ha a kimenet igaz, a B2BManagementPolicy házirend jelenleg létezik. A szabályzat frissítéséhez és az előzetes verzióra való feliratkozáshoz futtassa a következőt:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Az előzetes verzió kiválasztását követően
Eltarthat néhány percig, amíg a letiltási művelet érvénybe lép. Ha kikapcsolja az előzetes verziót, az egyszeri jelszót beváltó vendég-felhasználók nem fognak tudni bejelentkezni. Törölheti a vendég felhasználót, és újból meghívhatja a felhasználót, hogy engedélyezze a bejelentkezést egy másik hitelesítési módszer használatával.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Az előnézet kikapcsolása az Azure AD-portál használatával
1.  Jelentkezzen be az [Azure Portalba](https://portal.azure.com/) Azure ad globális rendszergazdaként.
2.  A navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.
3.  A **kezelés**területen válassza a **szervezeti kapcsolatok**elemet.
4.  Válassza a **Beállítások**lehetőséget.
5.  Az **E-mail egyszeri jelszó engedélyezése a vendégek számára (előzetes verzió)** területen válassza a **nem**lehetőséget.

### <a name="to-turn-off-the-preview-using-powershell"></a>Az előnézet kikapcsolása a PowerShell használatával
Telepítse a legújabb AzureADPreview-modult, ha még nem rendelkezik ezzel (lásd [az előfeltételt: telepítse a fenti legújabb AzureADPreview-modult](#prerequisite-install-the-latest-azureadpreview-module) ). Ezt követően győződjön meg arról, hogy az egyszeri jelszó-előnézeti házirend jelenleg létezik a következő futtatásával:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Ha a kimenet igaz, a következő parancs futtatásával lépjen ki az előnézetből:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

