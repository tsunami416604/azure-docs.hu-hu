---
title: Az Azure AD-vel az alkalmazásokhoz való hozzáférés kezelése |} A Microsoft Docs
description: Ismerteti, hogyan Azure Active Directory lehetővé teszi a szervezetek számára, hogy az alkalmazások, amelyhez hozzáféréssel rendelkezik minden felhasználóhoz adja meg.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: celested
ms.openlocfilehash: 1be695384ff6828561fe8e1e858d052cba302dc0
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982694"
---
# <a name="managing-access-to-apps"></a>Alkalmazásokhoz való hozzáférés kezelése
Folyamatban lévő hozzáférés-kezelés, használati értékelő és jelentéskészítési továbbra is kihívást jelenthet, miután az alkalmazás integrálva van a szervezet identitáskezelő rendszerbe. Sok esetben a rendszergazdák vagy a segélyszolgálat kell elvégeznie az alkalmazásokhoz való hozzáférés kezelése a folyamatban lévő aktív szerepet. Egyes esetekben hozzárendelést egy általános vagy részlegszintű informatikai csapat történik. Gyakran előfordul, a hozzárendelés döntést célja, hogy az üzleti döntéshozó, azok révén az informatikai előtt jóváhagyásra van szükség a delegálható a hozzárendelést.  Integráció a meglévő automatikus identitás- és hozzáférés felügyeleti rendszer, mint a szerepköralapú hozzáférés-vezérlés (RBAC) vagy attribútum-szerepköralapú hozzáférés-vezérlés (ABAC) más szervezetek befektetni. Az integráció és a szabály fejlesztés általában a speciális és költséges lehet. Monitoring, vagy mindkét felügyeleti megközelítés a reporting saját külön, költséges és bonyolult befektetése jelenti.

## <a name="how-does-azure-active-directory-help"></a>Hogyan segít az Azure Active Directory?
 Az Azure AD támogatja a széles körű hozzáférés-kezelés konfigurált alkalmazások lehetővé teszi a cégeknek könnyedén szert a delegálást, és többek között az automatikus, az attribútum-alapú hozzárendelés (ABAC vagy RBAC-forgatókönyv) hibanaplózást megfelelő hozzáférési szabályzatok rendszergazdák kezelése. Az Azure AD-val könnyen érheti el, összetett házirendek kombinálásával egyetlen alkalmazásra vonatkozó több felügyeleti modell, és a portkezelési szabályok is felhasználhatja az azonos célközönséggel rendelkező alkalmazások közötti.

* [Új vagy meglévő alkalmazásait](configure-single-sign-on-portal.md)

 Az Azure AD alkalmazás-hozzárendelés összpontosít két elsődleges hozzárendelési módban:

* **Egyéni hozzárendelés** egy rendszergazda címtár globális rendszergazdai jogosultságokkal rendelkező válassza ki az egyéni felhasználói fiókokhoz és hozzáférést biztosít nekik az alkalmazáshoz.
* **Csoport alapú hozzárendelés (fizetős Azure AD csak)** címtár globális rendszergazdai jogosultságokkal rendelkező egy informatikai rendszergazda is kiadhatjuk egy csoportnak az alkalmazáshoz. Adott felhasználók hozzáférésének csatlakoznak-e a csoport tagjai a időben próbálnak hozzáférni az alkalmazás határozza meg. Más szóval a rendszergazda hatékonyan hozhat létre egy hozzárendelési szabály figyelmezteti a "minden aktuális tagot a hozzárendelt csoport férhet hozzá az alkalmazás". A hozzárendelés lehetőséget használja, a rendszergazdák kihasználhatják a bármelyik Azure AD csoportkezelési beállítás megjeleníthető, beleértve a [Attribútumalapú dinamikus csoportok](../fundamentals/active-directory-groups-create-azure-portal.md), külső rendszer csoportok (például a helyszíni Active Directory vagy Workday), vagy rendszergazdai vagy önkiszolgáló-üzemeltetési felügyelt csoportok. Egyetlen csoport könnyedén rendelhet több alkalmazás gondoskodik róla, hogy, hogy a hozzárendelés affinitással alkalmazásokat megoszthatja a hozzárendelési szabályok, csökkenti a teljes felügyelet összetettségét. Vegye figyelembe, hogy a beágyazott csoporttagság biztonságicsoport-alapú hozzárendelés alkalmazások jelenleg nem támogatottak.

E két hozzárendelés mód használata esetén a rendszergazdák érheti el bármelyik kívánatos hozzárendelés felügyeleti módszerrel.

Az Azure AD-használat és a hozzárendelés jelentéskészítési rendszer teljesen integrált része, ezáltal a rendszergazda egyszerűen hozzárendelés állapota hozzárendelési hibákkal és még akkor is, használati jelentést.

## <a name="complex-application-assignment-with-azure-ad"></a>Az Azure ad-vel bonyolult az alkalmazás-hozzárendelés
Vegyünk egy alkalmazást, mint például a Salesforce. Számos szervezet Salesforce elsősorban a marketing- és értékesítési csapatokat. Gyakran előfordul a marketing csapat tagjai rendelkeznek magas privilegizált hozzáférési jogosultsága a Salesforce-hoz, amíg az értékesítési csoport tagjai csak korlátozott hozzáféréssel rendelkeznek. Sok esetben az információkkal dolgozó szakemberek a széles körű populációs korlátozta a hozzáférést az alkalmazáshoz. Ezek a szabályok alóli kivételek bonyolítja. Ez a legtöbbször a marketing vagy értékesítési vezetői csapatok számára a felhasználói hozzáférésének biztosítása, vagy módosítsa az alábbi általános szabályokkal függetlenül szerepkörökhöz korlátain.

Az Azure AD-alkalmazások, mint például a Salesforce lehet előre konfigurált az egyszeri bejelentkezés (SSO) és automatikus üzembe helyezést. Miután konfigurálta az alkalmazást, a rendszergazda is igénybe vehet a egyszeri művelet létrehozása és hozzárendelése a megfelelő csoportok. Ebben a példában a rendszergazda a következő hozzárendeléseket hajthatnak végre:

* [Dinamikus csoportok](../fundamentals/active-directory-groups-create-azure-portal.md) automatikusan az összes tagjai a marketing és értékesítés csapatokkal, részleg vagy szerepkör-attribútumok jelölésére adható meg:
  
  * Marketing csoportok minden tagját hozzájuk a "marketing" szerepkörhöz, a Salesforce-ban
  * Csoportok hozzájuk a "sales" szerepkör a Salesforce-ban az értékesítési csoport minden tagját. További finomítható több különböző Salesforce szerepkörökhöz hozzárendelt regionális értékesítési csapatot képviselő csoport használatával.
* Ahhoz, hogy a kivételképzési mechanizmus, egy önkiszolgáló csoportkezelési sikerült létrehozni az egyes szerepkörökhöz. Például a "Salesforce marketing kivétel" csoport is létrehozható egy önkiszolgáló csoportként. A csoport a Salesforce marketing szerepkört kell hozzárendelni, és a marketing vezetőségi tulajdonos lehet tenni. A marketing vezetőségi tagja sikerült hozzáadása, vagy távolítsa el a felhasználókat, illesztési szabályzat, vagy még hagyja jóvá vagy csatlakozni az egyes felhasználói kérések elutasítása. Ez a mechanizmus segítségével adatokat feldolgozó megfelelő felületet tulajdonosok és tagok speciális képzési nem igénylő használata támogatott.

Ebben az esetben az összes hozzárendelt felhasználó lenne automatikusan üzembe helyezve a Salesforce-hoz, a szerepkör-hozzárendelés frissülnek a Salesforce-ban különböző csoportokhoz való hozzáadásuk. Felhasználók lenne képes felderíteni és hozzáférni a Salesforce keresztül a Microsoft alkalmazás-hozzáférési panelen, az Office web ügyfelek számára, vagy akár szervezeti Salesforce bejelentkezési oldalra lépjen. A rendszergazdák egyszerűen megtekintheti a használati és a hozzárendelés állapotát az Azure AD-jelentéskészítés lenne.

A rendszergazdák azonban [Azure AD feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) az egyes szerepkörök hozzáférési házirendjeinek beállítása. Ezek a szabályzatok lehetnek hozzáférés a vállalati környezetben, és még a multi-factor Authentication szolgáltatás- vagy eszközkövetelmények különféle esetekben hozzáférés eléréséhez kívül engedélyezett-e.

## <a name="next-steps"></a>További lépések
* [Feltételes hozzáféréssel rendelkező alkalmazások védelme](../active-directory-conditional-access-azure-portal.md)
* [Önkiszolgáló csoport felügyeleti/SSAA](../users-groups-roles/groups-self-service-management.md)
