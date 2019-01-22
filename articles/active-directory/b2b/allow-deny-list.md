---
title: Az adott szervezetek – Azure Active Directory B2B-felhasználók meghívások engedélyezése vagy letiltása |} A Microsoft Docs
description: Bemutatja, hogyan rendszergazda használatával az Azure portal vagy a PowerShell-hozzáférés beállítása, vagy engedélyezheti vagy tilthatja le a B2B-felhasználók az egyes tartományok listában elutasítása.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: d0458fa9c40c5a6681a3f691cbb3d6a02f01ce66
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429067"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Az adott szervezetek a B2B-felhasználók meghívások engedélyezése vagy letiltása

Engedélyezési és blokkolási Segítségkérések B2B-felhasználók az adott szervezetek egy engedélyezési vagy letiltási lista segítségével. Például ha letiltja a személyes e-mail-cím tartományokhoz, is beállította a letiltási lista, amely tartalmazza a tartományok, mint a Gmail.com és az Outlook.com-os. Vagy, ha üzleti tevékenységének más vállalatok számára, például a Contoso.com, Fabrikam.com és Litware.com partneri, és csak az adott szervezetek küldött meghívások korlátozni szeretné, adhat hozzá a Contoso.com, Fabrikam.com és Litware.com, az engedélyezési lista.
  
## <a name="important-considerations"></a>Fontos szempontok

- Egy engedélyezési vagy letiltási lista vagy hozhat létre. Mindkét típusú lista nem tudja beállítani. Alapértelmezés szerint bármilyen tartományai nem található az engedélyezési listán vannak a letiltási listát, és ez fordítva is igaz. 
- Szervezetenként csak egy házirendet hozhat létre. Frissítheti a házirendet, további kiszolgálókat tartalmazza, vagy törölheti a házirendet, hozzon létre egy újat. 
- Ez a lista függetlenül működik a onedrive-ról üzleti és a SharePoint Online engedélyezésére és letiltására listák készítéséhez. A SharePoint online-ban egyedi fájlmegosztási korlátozni szeretné, ha szeretne beállítani egy engedélyezési vagy letiltási lista a onedrive és SharePoint online-hoz. További információkért lásd: [megosztását a SharePoint Online és OneDrive vállalati verzióba tartományok korlátozott](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Ez a lista nem vonatkozik a külső felhasználók számára, akik már beváltott a meghívót. Miután beállította a listában, a lista fogja érvényesíteni. Ha egy felhasználó meghívó függőben van, és beállíthat egy szabályzatot, amely letiltja a tartományhoz, beváltani a meghívót a felhasználónak kísérlete sikertelen lesz.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Állítsa be az engedélyezési vagy megtagadási lista házirend a portálon

Alapértelmezés szerint a **meghívás (legmegengedőbb) tartományba küldhető** beállítás engedélyezve van. Ebben az esetben meghívhatja a B2B-felhasználók minden olyan szervezet.

### <a name="add-a-deny-list"></a>Adja hozzá a letiltási lista

Ez a legjellemzőbb forgatókönyve, ahol a szervezet szeretne dolgozni a szinte minden szervezetnek, de szeretné megakadályozni a felhasználók a megadott tartományokból hívható meg a B2B-felhasználók.

A letiltási lista hozzáadása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **felhasználók** > **felhasználói beállítások**.
3. A **külső felhasználók**válassza **külső együttműködési beállítások kezelése**.
4. A **együttműködési korlátozások**válassza **a megadott tartományba küldött meghívások visszautasítása**.
5. A **CÉLTARTOMÁNYOK**, adja meg az egyik a blokkolni kívánt tartomány nevét. Több tartomány adja meg egy új sor minden egyes tartományhoz. Példa:

   ![Megjeleníti a Megtagadás lehetőség hozzáadva tartományokkal](./media/allow-deny-list/DenyListSettings.png)
 
6. Ha elkészült, kattintson a **mentése**.

Miután beállította a házirendet, ha megpróbálja meghívni a felhasználó tartománya blokkolva, üzenetet kap arról, hogy a felhasználó tartománya jelenleg le van tiltva a meghívási szabályzatban kap.
 
### <a name="add-an-allow-list"></a>Engedélyezési lista hozzáadása

Ez a szigorúbb konfigurációs, ahol állítsa be az adott tartomány szerepel az engedélyezési listán, és bármely más szervezetek vagy tartományok, amelyek nem említett meghívók korlátozása. 

Ha szeretné használni az engedélyezési listán, győződjön meg arról, hogy teljes mértékben a az üzleti igények kiértékelése időt, a rendszer. Ha ez a házirend túlságosan korlátozó, a felhasználók választhatják dokumentumok küldése e-mailben vagy egyéb nem informatikai engedélyezett módjait együttműködés keresése.


Engedélyezési lista hozzáadása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **felhasználók** > **felhasználói beállítások**.
3. A **külső felhasználók**válassza **külső együttműködési beállítások kezelése**.
4. A **együttműködési korlátozások**válassza **engedélyezése (legszigorúbb) csak a megadott tartományba küldött meghívások**.
5. A **CÉLTARTOMÁNYOK**, adja meg a nevét, amely számára engedélyezni kívánja a tartomány egyik. Több tartomány adja meg egy új sor minden egyes tartományhoz. Példa:

   ![Megjeleníti a hozzáadott tartományokkal engedélyezése beállítás](./media/allow-deny-list/AllowListSettings.png)
 
6. Ha elkészült, kattintson a **mentése**.

Miután beállította a házirendet, ha megpróbálja meghívni a felhasználót a tartományból, amely nem szerepel az engedélyezési listán, üzenetet kap arról, hogy a felhasználó tartománya jelenleg le van tiltva a meghívási szabályzatban kap.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Váltson az lehetővé teszi, hogy megtagadja a listában, és fordítva 

Ha úgy vált egyik házirendből, hogy a másik ez elveti a meglévő házirend-konfigurációt. Ellenőrizze, hogy készítsen biztonsági másolatot a konfiguráció részleteit, a kapcsoló végrehajtása előtt. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Állítsa be az engedélyezési vagy megtagadási lista házirend PowerShell-lel

### <a name="prerequisite"></a>Előfeltétel

Állítsa be az engedélyezési vagy letiltási lista PowerShell-lel, telepítenie kell az Azure Active Directory modul a Windows PowerShell előzetes verzióját. Pontosabban, telepítse az AzureADPreview Modulverzió 2.0.0.98 vagy újabb.

Ellenőrizheti, hogy a modul verzióját (ha telepítve van):
 
1. Nyisson meg egy emelt szintű felhasználói (Futtatás rendszergazdaként) Windows PowerShell. 
2. Tekintse meg az Azure Active Directory modul a Windows PowerShell a számítógépre telepített bármely verziója van, ha az alábbi parancsot futtassa:

   ````powershell  
   Get-Module -ListAvailable AzureAD*
   ````

Ha a modul nincs telepítve, vagy nem rendelkezik a szükséges verziót, tegye a következők egyikét:

- Ha nem jár eredménnyel, futtassa a következő parancsot a AzureADPreview modulban legújabb verziójának telepítéséhez:
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- Ha csak az Azure ad-modul telepítéséhez az AzureADPreview modulban a következő parancsokat az eredmények látható: 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- Ha csak az eredmények megjelennek az AzureADPreview modulban, de a verziószáma kisebb, mint 2.0.0.98, frissíteni, a következő parancsokat: 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ````

- Ha az eredmények jelennek meg az Azure ad és a AzureADPreview modulban, de az AzureADPreview modulban verziószáma kisebb, mint 2.0.0.98, futtassa a frissítéshez a következő parancsokat: 

   ````powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ````

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>A AzureADPolicy parancsmagokat használja a házirend konfigurálása

Hozzon létre egy engedélyezési vagy letiltási lista használja a [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) parancsmagot. Az alábbi példa bemutatja, hogyan állítsa be a letiltási lista, amely blokkolja a "live.com" tartományt.

````powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

A következő ugyanebben a példában látható, de a szabályzat-definíció beágyazott.

````powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Állítsa be az engedélyezési vagy megtagadási lista házirendet használja a [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) parancsmagot. Példa:

````powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
````

A szabályzat beszerzéséhez használja a [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) parancsmagot. Példa:

````powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
````

A szabályzat eltávolításához használja a [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) parancsmagot. Példa:

````powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
````

## <a name="next-steps"></a>További lépések

- Az Azure AD B2B áttekintését lásd: [Mi az Azure AD B2B együttműködés?](what-is-b2b.md)
- További információ a feltételes hozzáférés és a B2B-együttműködés: [feltételes hozzáférés B2B-együttműködés felhasználók](conditional-access.md).



