---
title: "Az Azure Active Directoryban önkiszolgáló vagy próbaverziós előfizetés |} Microsoft Docs"
description: "Egy Azure Active Directory (Azure AD) bérlői önkiszolgáló regisztrációt használni"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 9f2b541d5028596f9beabc7fd82001b4c9dacad4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Mi az az Azure Active Directory önkiszolgáló regisztráció?
Ez a cikk azt ismerteti, önkiszolgáló regisztrációt, és hogyan támogatják a forgatókönyvet az Azure Active Directory (Azure AD). Ha azt szeretné, hogy átvehesse a tartománynév egy nem felügyelt Azure ad-bérlői című [rendszergazdaként egy nem felügyelt directory átveszi](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Miért érdemes használni önkiszolgáló regisztrációt?
* Az ügyfelek eléréséhez cég gyorsabb szolgáltatások
* E-mail alapú ajánlatok szolgáltatás létrehozása
* E-mail alapú előfizetési flow-gyorsan engedélyezése a felhasználók számára, hogy azok könnyen megjegyezhető munkahelyi e-mail aliasok identitások létrehozása
* Egy önkiszolgáló-üzemeltetési által létrehozott Azure AD-címtár is be kell kapcsolni, egy felügyelt könyvtárba, amely más szolgáltatások is használható

## <a name="terms-and-definitions"></a>Kifejezések és meghatározások
* **Önkiszolgáló regisztrációt**: Ez a felhasználó előfizet egy felhőalapú szolgáltatás és van egy automatikusan létrehozott számukra az Azure AD identity alapuló e-mailek tartományukban mód.
* **Nem felügyelt Azure AD-címtár**: Ez az a könyvtár, ahol létrehozzák identitásukat. Egy nem felügyelt könyvtár értéke nem globális rendszergazda megegyező nevű könyvtárat.
* **E-mailek ellenőrzött felhasználói**: Ez egy felhasználói fiók típusa az Azure AD-ben. A felhasználó identitása önkiszolgáló szolgáltatásokat a regisztráció után automatikusan létrehozza az e-mailek ellenőrzött felhasználói néven ismert. Az e-mailek ellenőrzése felhasználó tagja rendszeres creationmethod címkéjű könyvtár = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Hogyan szabályozza a önkiszolgáló beállítások?
Rendszergazdák két önkiszolgáló vezérlők ma rendelkezik. Is hogy:

* E-mailben directory a felhasználók úgy csatlakozhatnak.
* Felhasználók licencelheti magukat az alkalmazások és szolgáltatások.

### <a name="how-can-i-control-these-capabilities"></a>Hogyan szabályozhatja ezeket a képességeket?
Egy rendszergazda konfigurálhatja ezeket a képességeket a következő Azure AD-parancsmag Set-MsolCompanySettings paraméterekkel:

* **AllowEmailVerifiedUsers** meghatározza, hogy a felhasználó létrehozása, vagy egy nem felügyelt directory join. $False értékre állítja, hogy a paramétert, ha nem ellenőrzött e-mail felhasználók csatlakozhatnak a könyvtár.
* **AllowAdHocSubscriptions** meghatározza, hogy a felhasználók önkiszolgáló regisztrációt végrehajtásához. $False értékre állítja, hogy a paramétert, ha a felhasználók nem végezheti önkiszolgáló regisztrációt. 
  
  > [!NOTE]
  > Folyamat és a PowerApps próba signups nem szabályozzák a **AllowAdHocSubscriptions** beállítást. További információkért tekintse át a következő cikkeket:
  > * [Hogyan akadályozhatom meg, hogy a meglévő felhasználók használata a Power bi-ban?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [A Q & A szervezetében flow](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hogyan vezérlők működnek együtt?
A két paraméter önkiszolgáló regisztrációt szabályozhatják pontosabb meghatározásához használható együtt. Például az alábbi parancs segítségével a felhasználók önkiszolgáló regisztrációt, de csak végrehajtásához, ha azoknak a felhasználóknak már rendelkezik fiókkal az Azure ad-ben (más szóval, felhasználók, akik létrehozni ellenőrzött e-mail fiókkal kell először nem hajtható végre önkiszolgáló regisztrációt):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
Az alábbi folyamatábra bemutatja a különböző kombinációkban e paraméterek és az eredményül kapott a feltételeket a könyvtár és önkiszolgáló regisztrációt.

![][1]

További információt és példákat ezekkel a paraméterekkel, [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>További lépések
* [Egyéni tartománynév hozzáadása az Azure AD](add-custom-domain.md)
* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
