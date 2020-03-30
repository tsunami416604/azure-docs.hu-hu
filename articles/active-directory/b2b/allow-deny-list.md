---
title: Adott szervezeteknek szóló meghívók engedélyezése vagy letiltása – Azure AD
description: Bemutatja, hogy a rendszergazda hogyan használhatja az Azure Portalon vagy a PowerShellben egy hozzáférési vagy megtagadási lista beállításához, hogy bizonyos tartományokb2B-felhasználók engedélyezése vagy letiltása.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5100c4406cfd4a8395dfa177dc3cd5e911decb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273426"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Meghatározott B2B-felhasználóktól érkező meghívások engedélyezése vagy letiltása

Az engedélyezési lista vagy a megtagadási lista segítségével engedélyezheti vagy letilthatja az adott szervezetek B2B-felhasználóinak szóló meghívókat. Ha például le szeretné tiltani a személyes e-mail címtartományokat, beállíthat egy megtagadási listát, amely olyan tartományokat tartalmaz, mint Gmail.com és Outlook.com. Ha vállalkozása más vállalkozásokkal is partnerségben áll, például Contoso.com, Fabrikam.com és Litware.com, és csak ezekre a szervezetekre szeretné korlátozni a meghívókat, hozzáadhat Contoso.com, Fabrikam.com és Litware.com az engedélyezési listához.
  
## <a name="important-considerations"></a>Fontos szempontok

- Létrehozhat engedélyezési listát vagy megtagadási listát. Nem állítható be mindkét típusú lista. Alapértelmezés szerint bármilyen tartomány nem szerepel az engedélyezési listában, szerepel a megtagadási listán, és fordítva. 
- Szervezetenként csak egy házirendet hozhat létre. Frissítheti a házirendet úgy, hogy több tartományt vegyen fel, vagy törölheti a házirendet egy új fájl létrehozásához. 
- Az engedélyezési listához vagy a megtagadási listához hozzáadható tartományok számát csak a házirend mérete korlátozza. A teljes házirend maximális mérete 25 KB (25 000 karakter), amely tartalmazza az engedélyezési listát vagy a megtagadási listát, valamint az egyéb szolgáltatásokhoz konfigurált paramétereket.
- Ez a lista a OneDrive Vállalati verziótól és a SharePoint Online engedélyezési/tiltólistáitól függetlenül működik. Ha korlátozni szeretné az egyes fájlmegosztásokat a SharePoint Online-ban, be kell állítania egy engedélyezési vagy megtagadási listát a OneDrive Vállalati verzióhoz és a SharePoint Online-hoz. További információt a [Korlátozott tartományok megosztása a SharePoint Online-ban és a OneDrive Vállalati verzióban című témakörben talál.](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9)
- A lista nem vonatkozik azokra a külső felhasználókra, akik már beváltották a meghívót. A lista beállítása után a lista lesz érvényesítve. Ha egy felhasználói meghívó függőben van, és olyan házirendet állít be, amely blokkolja a tartományukat, a felhasználó sikertelenül próbálja beváltani a meghívót.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Az engedélyezési vagy megtagadási listaházirend beállítása a portálon

Alapértelmezés szerint a **Meghívók elküldésének engedélyezése bármely tartomány (legátfogóbb)** beállításban engedélyezve van. Ebben az esetben bármely szervezetBB-felhasználóit meghívhatja.

### <a name="add-a-deny-list"></a>Megtagadási lista hozzáadása

Ez a legjellemzőbb forgatókönyv, ahol a szervezet szinte minden szervezettel szeretne dolgozni, de meg szeretné akadályozni, hogy bizonyos tartományokfelhasználói b2b-felhasználóként meghívást kapjanak.

Megtagadási lista hozzáadása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **Azure Active Directory** > **felhasználóinak** > **beállításait.**
3. A **Külső felhasználók csoportban**válassza **a Külső együttműködési beállítások kezelése**lehetőséget.
4. Az **Együttműködési korlátozások**csoportban jelölje be a **Meghívók megtagadása a megadott tartományokra jelölőnégyzetet.**
5. A **TARGET DOMAINS csoportban**adja meg a blokkolni kívánt tartományok egyikének nevét. Több tartomány esetén adja meg az egyes tartományokat egy új sorban. Példa:

   ![A megtagadási lehetőség megjelenítése hozzáadott tartományokkal](./media/allow-deny-list/DenyListSettings.png)
 
6. Ha elkészült, kattintson a **Mentés gombra.**

Miután beállította a házirendet, ha egy felhasználót megpróbál meghívni egy letiltott tartományból, egy üzenet jelenik meg, amely szerint a felhasználó tartományát jelenleg letiltja a meghívási házirend.
 
### <a name="add-an-allow-list"></a>Engedélyezési lista hozzáadása

Ez egy szigorúbb konfiguráció, ahol az engedélyezési listában meghatározott tartományokat állíthat be, és a meghívókat bármely más, nem említett szervezetre vagy tartományra korlátozhatja. 

Ha engedélyezési listát szeretne használni, győződjön meg arról, hogy időt szán arra, hogy teljes mértékben kiértékelje üzleti igényeit. Ha ezt a házirendet túlságosan korlátozóvá teszi, a felhasználók dönthetnek úgy, hogy e-mailben küldenek dokumentumokat, vagy más, nem informatikai szempontból engedélyezett együttműködési módokat keresnek.


Engedélyezési lista hozzáadása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **Azure Active Directory** > **felhasználóinak** > **beállításait.**
3. A **Külső felhasználók csoportban**válassza **a Külső együttműködési beállítások kezelése**lehetőséget.
4. Az **Együttműködési korlátozások csoportban**jelölje be a Csak a megadott tartományokra szóló **meghívók engedélyezése jelölőnégyzetet (a legszigorúbb).**
5. A **TARGET DOMAINS területen**adja meg az engedélyezni kívánt tartományok egyikének nevét. Több tartomány esetén adja meg az egyes tartományokat egy új sorban. Példa:

   ![Az engedélyezési beállítás megjelenítése hozzáadott tartományokkal](./media/allow-deny-list/AllowListSettings.png)
 
6. Ha elkészült, kattintson a **Mentés gombra.**

Miután beállította a házirendet, ha olyan tartományból próbál meg meghívni egy felhasználót, amely nem szerepel az engedélyezési listán, egy üzenet jelenik meg arról, hogy a felhasználó tartományát jelenleg letiltja a meghívási házirend.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Váltás az engedélyezési listáról és fordítva 

Ha egyik házirendről a másikra vált, ez elveti a meglévő házirend-konfigurációt. A váltás végrehajtása előtt készítsen biztonsági másolatot a konfiguráció részleteiről. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Az engedélyezési vagy megtagadási listaházirend beállítása a PowerShell használatával

### <a name="prerequisite"></a>Előfeltétel

> [!Note]
> Az AzureADPreview modul nem teljesen támogatott modul, mivel előzetes verzióban érhető el. 

Az engedélyezési vagy megtagadási lista beállítása a PowerShell használatával, telepítenie kell az Azure Active Directory modul windows PowerShell előzetes verzióját. Pontosabban telepítse az AzureADPreview modul 2.0.0.98-as vagy újabb verzióját.

A modul verziójának ellenőrzése (és annak ellenőrzése, hogy telepítve van-e):
 
1. Nyissa meg a Windows PowerShellt emelt szintű felhasználóként (Futtatás rendszergazdaként). 
2. Futtassa a következő parancsot, és nézze meg, hogy telepítve van-e az Azure Active Directory modul a Windows PowerShellhez a számítógépen:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Ha a modul nincs telepítve, vagy nincs kötelező verziója, tegye az alábbiak egyikét:

- Ha nem ad vissza eredményt, futtassa a következő parancsot az AzureADPreview modul legújabb verziójának telepítéséhez:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Ha csak az AzureAD-modul jelenik meg az eredményekközött, futtassa a következő parancsokat az AzureADPreview modul telepítéséhez: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Ha csak az AzureADPreview modul jelenik meg az eredményekközött, de a verzió kisebb, mint 2.0.0.98, futtassa a következő parancsokat a frissítéséhez: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Ha mind az AzureAD, mind az AzureADPreview-modulok megjelennek az eredményekközött, de az AzureADPreview modul verziója kisebb, mint 2.0.0.98, futtassa a következő parancsokat a frissítéshez: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Az AzureADPolicy parancsmagokkal konfigurálhatja a házirendet

Engedélyezési vagy megtagadási lista létrehozásához használja a [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) parancsmag. A következő példa bemutatja, hogyan állíthat be olyan megtagadási listát, amely blokkolja a "live.com" tartományt.

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

A következő mutatja ugyanazt a példát, de a házirend-definíció inline.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Az engedélyezési vagy megtagadási listaházirend beállításához használja a [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) parancsmag. Példa:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

A szabályzat lekért, használja a [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) parancsmag. Példa:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

A szabályzat eltávolításához használja az [Eltávolítás-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) parancsmag. Példa:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>További lépések

- Az Azure AD B2B áttekintését a [Mi az Azure AD B2B együttműködés?](what-is-b2b.md)
- A feltételes hozzáférésről és a B2B együttműködésről a [Feltételes hozzáférés a B2B együttműködési felhasználók számára című](conditional-access.md)témakörben talál.



