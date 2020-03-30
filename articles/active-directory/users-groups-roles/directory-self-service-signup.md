---
title: Önkiszolgáló regisztráció az e-mailben ellenőrzött felhasználók számára - Azure AD | Microsoft dokumentumok
description: Önkiszolgáló regisztráció használata az Azure Active Directory (Azure AD) bérlői környezetében
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027635"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Mi az Önkiszolgáló regisztráció az Azure Active Directoryra?

Ez a cikk bemutatja, hogyan használhatja az önkiszolgáló regisztrációt egy szervezet feltöltéséhez az Azure Active Directoryban (Azure AD). Ha egy nem felügyelt Azure AD-szervezet tartománynevét szeretné átvenni, olvassa el [a Nem felügyelt címtár rendszergazdaként című témakört.](domains-admin-takeover.md)

## <a name="why-use-self-service-sign-up"></a>Miért érdemes önkiszolgáló regisztrációt használni?
* Az ügyfelek gyorsabban eljuthatnak a kívánt szolgáltatásokhoz
* E-mail alapú ajánlatok létrehozása egy szolgáltatáshoz
* E-mail alapú regisztrációs folyamatok létrehozása, amelyek gyorsan lehetővé teszik a felhasználók számára, hogy könnyen megjegyezhető munkahelyi e-mail aliasaikkal identitásokat hozzanak létre
* Az önkiszolgáló azure AD-könyvtár olyan felügyelt könyvtárrá alakítható, amely más szolgáltatásokhoz is használható

## <a name="terms-and-definitions"></a>Feltételek és definíciók
* **Önkiszolgáló regisztráció:** Ez az a módszer, amellyel a felhasználó regisztrál egy felhőalapú szolgáltatásra, és az Azure AD-ben automatikusan létrehozott identitást hoz létre az e-mail tartományuk alapján.
* **Nem felügyelt Azure AD-könyvtár:** Ez az a könyvtár, ahol az identitás létrejön. A nem felügyelt könyvtár olyan könyvtár, amelynek nincs globális rendszergazdája.
* **E-mail-ellenőrzött felhasználó:** Ez egy olyan típusú felhasználói fiók az Azure AD-ben. Az önkiszolgáló ajánlatra való feliratkozás után automatikusan létrehozott identitással rendelkező felhasználót e-mailben ellenőrzött felhasználónak nevezzük. Az e-mailben ellenőrzött felhasználó a creationmethod=EmailVerified címkével ellátott könyvtár rendszeres tagja.

## <a name="how-do-i-control-self-service-settings"></a>Hogyan szabályozhatom az önkiszolgáló beállításokat?
A rendszergazdák nak ma két önkiszolgáló vezérlője van. Szabályozhatják, hogy:

* A felhasználók e-mailben csatlakozhatnak a könyvtárhoz
* A felhasználók licencelhetik magukat az alkalmazásokhoz és szolgáltatásokhoz

### <a name="how-can-i-control-these-capabilities"></a>Hogyan szabályozhatom ezeket a képességeket?
A rendszergazdák ezeket a képességeket a set-MsolCompanySettings következő Azure-parancsmag paramétereivel konfigurálhatja:

* **Az AllowEmailVerifiedUsers azt szabályozza,** hogy a felhasználó létrehozhat-e könyvtárat, illetve csatlakozhat-e hozzájuk. Ha ezt a paramétert $false, egyetlen e-mail-ellenőrzésű felhasználó sem csatlakozhat a címtárhoz.
* **Az AllowAdHocSubscriptions szabályozza** a felhasználók önkiszolgáló regisztrációjának elvégzését. Ha ezt a paramétert $false, egyetlen felhasználó sem végezhet önkiszolgáló regisztrációt.
  
Az AllowEmailVerifiedUsers és az AllowAdHocSubscriptions olyan címtárszintű beállítások, amelyek felügyelt vagy nem felügyelt könyvtárra alkalmazhatók. Íme egy példa, ahol:

* Ellenőrzött tartománnyal rendelkező könyvtárat, például contoso.com
* Egy másik könyvtárból származó B2B együttműködést használ, hauserdoesnotexist@contoso.comolyan felhasználót hív meg, aki még nem létezik ( ) a contoso.com
* A kezdőkönyvtárban be van kapcsolva az AllowEmailVerifiedUsers

Ha az előző feltételek teljesülnek, akkor egy tagfelhasználó jön létre a kezdőkönyvtárban, és egy B2B vendégfelhasználó jön létre a meghívó könyvtárban.

A Flow és a PowerApps próbaverziós regisztrációit nem szabályozza az **AllowAdHocSubscriptions** beállítás. További információkért tekintse át a következő cikkeket:

* [Hogyan gátolható meg, hogy a meglévő felhasználók elkezdjék használni a Power BI-t?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [A Flow a munkahelyen – kérdések és válaszok](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hogyan működnek együtt a vezérlők?
Ez a két paraméter együttesen használható az önkiszolgáló regisztráció pontosabb szabályozásának meghatározásához. A következő parancs például lehetővé teszi a felhasználók számára az önkiszolgáló regisztrációt, de csak akkor, ha ezek a felhasználók már rendelkeznek fiókkal az Azure AD-ben (más szóval azok a felhasználók, akiknek először e-mailben ellenőrzött fiókra van szükségük, nem tudnak önkiszolgáló regisztrációt végrehajtani):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

A következő folyamatábra ismerteti a paraméterek különböző kombinációit, valamint a címtár és az önkiszolgáló regisztráció eredő feltételeit.

![önkiszolgáló regisztrációs vezérlők folyamatábrája](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

A paraméterek használatára vonatkozó további információkért és példákért a [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)című témakörben olvashat bővebben.

## <a name="next-steps"></a>További lépések

* [Egyéni tartománynév hozzáadása az Azure AD-hez](../fundamentals/add-custom-domain.md)
* [How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [A munkahelyi vagy iskolai fiók bezárása nem felügyelt könyvtárban](users-close-account.md)
