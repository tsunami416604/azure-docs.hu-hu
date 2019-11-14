---
title: Önkiszolgáló regisztráció e-mailben ellenőrzött felhasználók számára – Azure AD | Microsoft Docs
description: Önkiszolgáló regisztráció használata Azure Active Directory (Azure AD) bérlőben
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 953837e22cdd3ba8a54d702eac61461739db82d2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027635"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Mi a Azure Active Directory önkiszolgáló regisztrációja?

Ez a cikk azt ismerteti, hogyan használhatja az önkiszolgáló regisztrációt Azure Active Directory (Azure AD) szervezetének feltöltéséhez. Ha egy nem felügyelt Azure AD-szervezetből szeretne tartománynevet átvenni, tekintse meg a nem [felügyelt címtár átvétele rendszergazdaként](domains-admin-takeover.md)című témakört.

## <a name="why-use-self-service-sign-up"></a>Miért érdemes önkiszolgáló regisztrációt használni?
* Az ügyfeleket gyorsabban szeretnék lekérni
* E-mail-alapú ajánlatok létrehozása szolgáltatáshoz
* Hozzon létre e-mail-alapú regisztrációs folyamatokat, amelyekkel gyorsan engedélyezheti a felhasználók számára, hogy identitásokat hozzanak létre a könnyen megjegyezhető munkahelyi e-mail Aliasok használatával
* Az önkiszolgáló által létrehozott Azure AD-címtár olyan felügyelt címtárba kapcsolható, amely más szolgáltatásokhoz is használható

## <a name="terms-and-definitions"></a>Feltételek és definíciók
* **Önkiszolgáló regisztráció**: ez az a módszer, amellyel a felhasználó feliratkozik egy felhőalapú szolgáltatásra, és az Azure ad-ben automatikusan létrejön egy identitás az e-mail tartományuk alapján.
* Nem **felügyelt Azure ad-címtár**: ez az a könyvtár, amelyben a rendszer létrehozta az identitást. A nem felügyelt könyvtárak olyan könyvtárak, amelyeknek nincs globális rendszergazdája.
* E- **mailben ellenőrzött felhasználó**: ez az Azure ad-beli felhasználói fiók. Az önkiszolgáló ajánlatra való regisztráció után automatikusan létrehozott identitással rendelkező felhasználó e-mailben ellenőrzött felhasználó néven ismert. Az e-mailek által ellenőrzött felhasználók a creationmethod = EmailVerified címkével ellátott címtár szabályos tagja.

## <a name="how-do-i-control-self-service-settings"></a>Hogyan szabályozhatja az önkiszolgáló beállításokat?
A rendszergazdáknak jelenleg két önkiszolgáló vezérlője van. Megadhatják, hogy:

* A felhasználók e-mailben csatlakozhatnak a címtárhoz
* A felhasználók licenccel rendelkezhetnek az alkalmazásokhoz és szolgáltatásokhoz

### <a name="how-can-i-control-these-capabilities"></a>Hogyan szabályozható ezek a képességek?
A rendszergazdák a következő Azure AD-parancsmagok Msolcompanysettings parancsmagjával paramétereinek használatával konfigurálhatják ezeket a képességeket:

* A **AllowEmailVerifiedUsers** szabályozza, hogy egy felhasználó létrehozhat-e vagy csatlakozhat-e egy címtárhoz. Ha úgy állítja be ezt a paramétert, hogy $false, akkor az e-mailek által ellenőrzött felhasználók nem csatlakozhatnak a címtárhoz.
* A **AllowAdHocSubscriptions** lehetővé teszi, hogy a felhasználók önkiszolgáló regisztrációt végezzenek. Ha úgy állítja be ezt a paramétert, hogy $false, egyetlen felhasználó sem végezhet önkiszolgáló regisztrációt.
  
A AllowEmailVerifiedUsers és a AllowAdHocSubscriptions olyan címtár-szintű beállítások, amelyek felügyelt vagy nem felügyelt címtárra alkalmazhatók. Íme egy példa:

* A címtárat ellenőrzött tartománnyal (például contoso.com) felügyelheti
* A VÁLLALATKÖZI együttműködés egy másik címtárból való használata olyan felhasználó meghívásához, amely még nem létezik (userdoesnotexist@contoso.com) a contoso.com kezdőkönyvtárának
* A kezdőkönyvtár a AllowEmailVerifiedUsers be van kapcsolva

Ha az előző feltételek teljesülnek, akkor a rendszer létrehoz egy tagot a kezdőlapon, és létrehoz egy B2B vendég felhasználót a meghívó címtárban.

A flow és a PowerApps próbaverzióját nem a **AllowAdHocSubscriptions** beállítás vezérli. További információkért tekintse át a következő cikkeket:

* [Hogyan akadályozható meg, hogy a meglévő felhasználók megkezdsék a Power BI használatát?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [A flow a munkahelyen Q & A](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hogyan működnek együtt a vezérlőelemek?
Ez a két paraméter együtt használható az önkiszolgáló regisztráció pontosabb szabályozásának meghatározásához. A következő parancs például lehetővé teszi, hogy a felhasználók önkiszolgáló regisztrációt végezzenek, de csak akkor, ha ezek a felhasználók már rendelkeznek fiókkal az Azure AD-ben (vagyis a felhasználóknak, akik e-mailben hitelesített fiókra lenne szükségük, először nem végezhetnek önkiszolgáló regisztrációt):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

A következő folyamatábra ismerteti a paraméterek különböző kombinációit, valamint a címtár és az önkiszolgáló regisztráció eredményeként létrejövő feltételeket.

![önkiszolgáló bejelentkezési vezérlők folyamatábrája](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

További információt és példákat a paraméterek használatáról a [set-msolcompanysettings parancsmagjával](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

* [Egyéni tartománynév hozzáadása az Azure AD-hez](../fundamentals/add-custom-domain.md)
* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Munkahelyi vagy iskolai fiók lezárása nem felügyelt címtárban](users-close-account.md)
