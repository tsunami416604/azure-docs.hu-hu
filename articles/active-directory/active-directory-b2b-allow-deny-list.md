---
title: Engedélyezi vagy letiltja az adott vállalatoknak - Azure Active Directory B2B felhasználók meghívást |} Microsoft Docs
description: Látható, a rendszergazda használatát az Azure portálon vagy a PowerShell hozzáférés és letiltási lista engedélyezi vagy letiltja az egyes tartományok B2B felhasználóit.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/19/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7e89bf47f592e4698a6e50fced78aeab0152ebc6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Engedélyezi vagy letiltja az adott vállalatoknak B2B felhasználók meghívást

Engedélyezési vagy tiltólista meghívókat B2B felhasználók számára az adott szervezetek egy engedélyezési vagy letiltási lista segítségével. Például ha szeretné tiltani a személyes e-mail-cím tartomány, állíthat be a letiltási lista, amely például Gmail.com és Outlook.com-tartományt tartalmaz. Vagy, ha a vállalatnál a számos más vállalatok számára, például a Contoso.com, Fabrikam.com és Litware.com, és csak ezek a szervezetek felhívás korlátozni szeretné, adhat hozzá a Contoso.com, Fabrikam.com és Litware.com számára az engedélyezési lista.
  
## <a name="important-considerations"></a>Fontos tudnivalók találhatók

- Létrehozhat egy engedélyezési lista vagy a letiltási listát. Mindkét típusú lista nem tudja beállítani. Alapértelmezés szerint bármely tartományok nincsenek található az engedélyezési listán vannak a letiltási listát, és fordítva. 
- Egy szervezet csak egy házirendet hozhat létre. Frissítheti a házirendet további tartományokat, vagy törölheti a házirendet, hozzon létre egy újat. 
- Ez a lista függetlenül működik a onedrive-ról az üzleti és a SharePoint Online engedélyezése/letiltása listák. Ha a SharePoint Online egyedi fájlmegosztási korlátozni szeretné, állítson be egy engedélyezési vagy tiltólista a OneDrive és SharePoint Online szüksége. További információkért lásd: [korlátozza a SharePoint Online és onedrive vállalati verzió megosztása tartományok](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Ebben a listában a külső felhasználók számára, aki már rendelkezik beváltott a meghívót nem vonatkozik. A lista léptet érvénybe, miután a lista van beállítva. Ha egy felhasználó felkérést függő állapotban van, és, amely blokkolja a tartományi házirend beállítása, a meghívó beváltani a felhasználó kísérlet sikertelen lesz.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Állítsa be az engedélyezési vagy megtagadási lista házirend a portálon

Alapértelmezés szerint a **(a határokat is beleértve) valamelyik tartományához kérésének engedélyezése** beállítás engedélyezve van. Ebben az esetben felajánlhatja a B2B bármely szervezet felhasználóinak.

### <a name="add-a-deny-list"></a>Adja hozzá a letiltási lista

Ez a legjellemzőbb forgatókönyve, ahol a szervezet szinte bármely szervezet dolgozni szeretne, de szeretné megakadályozni a B2B felhasználók, meghívják olyan tartományok felhasználóit.

A letiltási lista hozzáadása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **felhasználók** > **felhasználói beállítások**.
3. A **külső felhasználók**, jelölje be **külső együttműködés beállításainak kezelése**.
4. A **együttműködés korlátozások**, jelölje be **felhívás a megadott tartomány megtagadása**.
5. A **CÉLTARTOMÁNYOK**, adja meg az egyik a blokkolni kívánt tartomány nevét. Több tartomány új sorba írjon be tartományonként. Példa:

   ![Megjeleníti a Megtagadás beállítás hozzáadott tartományokkal](./media/active-directory-b2b-allow-deny-list/DenyListSettings.png)
 
6. Amikor elkészült, kattintson a **mentése**.

Miután a házirendet, ha egy felhasználó egy blokkolt tartományból meghívása próbál, üzenetet kap arról, hogy a tartományi felhasználó jelenleg le van tiltva, a meghívó házirend kapni.
 
### <a name="add-an-allow-list"></a>Adja hozzá az engedélyezési listák

Ez a beállítás egy szigorúbb, ahol olyan tartományok állíthat be az engedélyezési listán, és bármely más szervezetek vagy tartományt, amely nem említett meghívókat korlátozása. 

Ha szeretne egy engedélyezési lista használja, győződjön meg arról, hogy időt teljesen kiértékelheti az üzleti igények is. Ha ez a házirend túl szigorú, a felhasználók választhatják dokumentumok küldjön e-mailek keresztül, vagy találjon más nem informatikai áttérést dolgoznak.


Az engedélyezési listák hozzáadása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **felhasználók** > **felhasználói beállítások**.
3. A **külső felhasználók**, jelölje be **külső együttműködés beállításainak kezelése**.
4. A **együttműködés korlátozások**, jelölje be **engedélyezése csak a megadott tartomány felhívás (szigorúbb)**.
5. A **CÉLTARTOMÁNYOK**, adja meg az egyik a tartományok, amelyek számára engedélyezni kívánja a neve. Több tartomány új sorba írjon be tartományonként. Példa:

   ![A hozzáadott tartományokkal engedélyezése beállítás megjelenítése](./media/active-directory-b2b-allow-deny-list/AllowListSettings.png)
 
6. Amikor elkészült, kattintson a **mentése**.

Miután a házirendet, ha egy felhasználó egy tartományból, amely nem szerepel az engedélyezési listán meghívása próbál, üzenetet kap arról, hogy a tartományi felhasználó jelenleg le van tiltva, a meghívó házirend kapni.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Váltson a visszautasítja a listában, és fordítva engedélyezése 

Ha átvált egy házirendet a másikra, ez elveti a meglévő házirend-konfigurációt. Ügyeljen arra, hogy készítsen biztonsági másolatot a konfiguráció részletes adatait, mielőtt elvégezné a kapcsoló. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Állítsa be az engedélyezési vagy megtagadási lista házirend PowerShell használatával

### <a name="prerequisite"></a>Előfeltétel

Az engedélyezési és letiltási lista PowerShell használatával, telepítenie kell az Active Directory modul Windows Powershellhez készült Azure előzetes verzióját. Pontosabban, telepítse a AzureADPreview 2.0.0.98 verziója vagy újabb.

Ellenőrizheti, hogy a modul verziója (ha az telepítve van):
 
1. Nyissa meg a Windows PowerShell egy emelt szintű felhasználói (Futtatás rendszergazdaként). 
2. Futtassa a vannak-e az Active Directory modul Windows Powershellhez készült Azure számítógépre telepített verzióinak törlésére a következő parancsot:

   ````powershell  
   Get-Module -ListAvailable AzureAD*
   ````

Ha a modul nincs telepítve, vagy nem rendelkezik a szükséges verzió, tegye a következők egyikét:

- Ha nincs találat, a következő paranccsal telepítse a legújabb verziót a AzureADPreview modul:
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- Ha csak a AzureAD modul megjelenik az eredmények között, a AzureADPreview modul telepítése a következő parancsokat: 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- Ha csak a AzureADPreview modul megjelenik az eredmények között, de a verziószáma kisebb, mint 2.0.0.98, a frissítéshez a következő parancsokat: 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ````

- Ha az AzureAD- és a AzureADPreview modulok jelennek meg az eredményeket, de a AzureADPreview modul verziószáma kisebb, mint 2.0.0.98, a következő parancsokat a frissítést: 

   ````powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ````

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Konfigurálja a házirendet a AzureADPolicy-parancsmagok használatával

Hozzon létre egy engedélyezési vagy tiltólista használja a [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) parancsmag. A következő példa bemutatja, hogyan telepíthet a letiltási lista, amely blokkolja a "live.com" tartomány.

````powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

A következő ugyanebben a példában látható, de a házirend-definíció beágyazott.

````powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Állítsa be az engedélyezési vagy megtagadási lista házirend használja a [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) parancsmag. Példa:

````powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
````

A házirend beszerzéséhez használja a [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) parancsmag. Példa:

````powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
````

Távolítsa el a szabályzatot, használja a [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) parancsmag. Példa:

````powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
````

## <a name="next-steps"></a>További lépések

- Azure AD B2B áttekintését lásd: [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
- További információ a feltételes hozzáférés és a B2B együttműködés: [feltételes hozzáférés a B2B együttműködés felhasználók](active-directory-b2b-mfa-instructions.md).



