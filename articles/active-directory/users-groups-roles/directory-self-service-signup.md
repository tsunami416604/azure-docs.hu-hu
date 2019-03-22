---
title: Az e-mailben ellenőrzött felhasználói fiókok – az Azure Active Directory önkiszolgáló regisztráció |} A Microsoft Docs
description: Használja az Azure Active Directory (Azure AD) bérlő önkiszolgáló regisztráció
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfc31705909710e6a60e0d2fd3963e74e9397ee
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201262"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Mi az Azure Active Directory önkiszolgáló regisztráció?

Ez a cikk bemutatja az önkiszolgáló regisztráció feltölti egy szervezet az Azure Active Directoryban (Azure AD) használatával. Ha azt szeretné, tartománynév átvételével egy nem felügyelt Azure AD a szervezeten belül, lásd: [rendszergazdaként egy nem felügyelt könyvtár átvétele](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Miért érdemes használni az önkiszolgáló regisztráció?
* A szolgáltatások gyorsabban szeretnének hívja meg ügyfeleit
* Hozzon létre egy szolgáltatás e-mail-alapú ajánlatok
* Gyorsan engedélyezése a felhasználók számára, hogy azok könnyen megjegyezhető munkahelyi e-mail alias identitások regisztráció e-mail-alapú folyamatok létrehozásához
* Egy önkiszolgáló-üzemeltetési által létrehozott Azure AD-címtár kapcsolható egy felügyelt könyvtárba, amely más szolgáltatások is használható

## <a name="terms-and-definitions"></a>Kifejezések és meghatározások
* **Önkiszolgáló regisztráció**: Ez az a módszer egy felhasználója regisztrál a felhőszolgáltatások és van egy automatikusan jönnek létre számukra az Azure AD identity alapuló e-mail-címe.
* **Nem felügyelt Azure AD-címtár**: Ez az a könyvtárat, amelyben létrehozza az identitásukat. Egy nem felügyelt címtár egy könyvtárat, amely nem globális rendszergazda rendelkezik.
* **E-mailben ellenőrzött felhasználói**: Ez olyan felhasználói fiók az Azure ad-ben. Egy felhasználó, aki a rendszer automatikusan létrehozza, miután egy önkiszolgáló ajánlatra való regisztráláskor nevezik, egy e-mailben ellenőrzött felhasználói identitást. Az e-mailben ellenőrzött felhasználók tagja rendszeres címkével ellátott creationmethod könyvtár = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Hogyan szabályozhatja a önkiszolgáló beállítások?
Rendszergazdák rendelkeznek két önkiszolgáló vezérlőelem még ma. Szabályozhatja, hogy:

* A felhasználók úgy csatlakozhatnak az e-mailen keresztül könyvtár
* Felhasználók licencelheti magukat az alkalmazások és szolgáltatások

### <a name="how-can-i-control-these-capabilities"></a>Hogyan felügyelhetem ezeket a képességeket?
Egy rendszergazda konfigurálhatja ezeket a képességeket a következő Azure ad-ben Set-MsolCompanySettings parancsmag-paraméterek használatával:

* **AllowEmailVerifiedUsers** azt szabályozza, hogy egy felhasználó létrehozhat vagy csatlakozzon egy könyvtárat. A paraméter értéke $false, ha e-mailben ellenőrzött felhasználó nem csatlakozhat a címtárban.
* **AllowAdHocSubscriptions** meghatározza, hogy a felhasználók önkiszolgáló regisztráció végrehajtásához. A paraméter értéke $false, ha a felhasználó nem hajthat végre önkiszolgáló regisztrációt.
  
AllowEmailVerifiedUsers AllowAdHocSubscriptions jsou alkalmazható egy felügyelt vagy egy nem felügyelt címtár directory kiterjedő beállítások. Íme egy példa ahol:

* Felügyelni, például a contoso.com egy ellenőrzött tartomány könyvtár
* Egy másik címtárból B2B-együttműködés segítségével, amely már nem létezik felhasználó meghívása (userdoesnotexist@contoso.com) constoso.com otthoni könyvtárában
* A kezdőkönyvtár rendelkezik-e kapcsolva a AllowEmailVerifiedUsers

Az előző feltételek teljesülése esetén majd jön létre a tag felhasználó a kezdőkönyvtárban, és a B2B vendégfelhasználó meghívó a címtárban jön létre.

Flow és a PowerApps próbaverziós részleggel nem szabályozza a **AllowAdHocSubscriptions** beállítás. További információkért tekintse át a következő cikkeket:

* [Hogyan védekezhetek a meglévő felhasználók elkezdjék használni a Power bi-ban?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Flow a munkahelyen, a Q & A](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hogyan vezérlők működik együtt?
A két paraméter még pontosabban szabályozhatja az önkiszolgáló regisztráció meghatározásához használható együtt. Például a következő parancs segítségével a felhasználók önkiszolgáló regisztrációt, de csak végrehajtani, ha ezek a felhasználók már rendelkezik fiókkal az Azure ad-ben (más szóval, felhasználók, akik e-mailben ellenőrzött fiók hozható létre kell először nem hajtható végre az önkiszolgáló regisztráció):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

A következő folyamatábra szemlélteti azt ismerteti, az e paraméterek különböző kombinációit és az eredményül kapott feltételeinek a címtárral és az önkiszolgáló regisztrációt.

![önkiszolgáló regisztrációs vezérlők folyamatábrája](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

További információt és példákat ezekkel a paraméterekkel: [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>További lépések

* [Egy egyéni tartománynév hozzáadása az Azure ad-ben](../fundamentals/add-custom-domain.md)
* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
