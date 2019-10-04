---
title: Az alkalmazásokhoz való hozzáférés kezelése az Azure AD használatával | Microsoft Docs
description: Leírja, hogy Azure Active Directory lehetővé teszi a szervezetek számára, hogy megadják azokat az alkalmazásokat, amelyekhez az egyes felhasználók hozzáférnek.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99c7947b6469f64f2ea05b2290305710db1ee796
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477111"
---
# <a name="managing-access-to-apps"></a>Az alkalmazásokhoz való hozzáférés kezelése
A folyamatos hozzáférés-kezelés, a használat kiértékelése és a jelentéskészítés továbbra is kihívást jelent, miután az alkalmazás integrálva van a szervezet identitási rendszerébe. Sok esetben a rendszergazdáknak vagy az ügyfélszolgálatnak folyamatosan aktív szerepkört kell betartania az alkalmazásaihoz való hozzáférés kezelésében. Időnként a hozzárendelést egy általános vagy egy részlegi informatikai csapat végzi. A hozzárendelési döntés gyakran arra szolgál, hogy az üzleti döntéshozónak legyen delegálva, mielőtt jóváhagyja a hozzárendelést.  Más szervezetek olyan meglévő automatizált identitás-és hozzáférés-kezelési rendszerbe fektetnek be integrációt, mint a szerepköralapú Access Control (RBAC) vagy az attribútum-alapú Access Control (ABAC). Az integráció és a szabályok fejlesztése is általában specializált és költséges. A felügyeleti megközelítés figyelése vagy jelentése a saját külön, költséges és összetett befektetés.

## <a name="how-does-azure-active-directory-help"></a>Hogyan segít a Azure Active Directory?
 Az Azure AD széles körű hozzáférés-vezérlést biztosít a konfigurált alkalmazásokhoz, így lehetővé teszi a szervezetek számára, hogy az automatikus, attribútum-alapú hozzárendeléstől (ABAC vagy RBAC forgatókönyvek) kezdve egyszerűen elérjék a megfelelő hozzáférési házirendeket a delegáláson keresztül, beleértve rendszergazdai felügyelet. Az Azure AD segítségével könnyedén, összetett házirendeket hozhat létre, több felügyeleti modellt is használhat egyetlen alkalmazáshoz, és a felügyeleti szabályokat is újra felhasználhatja az alkalmazások között ugyanazzal a célközönséggel.

* [Új vagy meglévő alkalmazások hozzáadása](add-gallery-app.md)

  Az Azure AD alkalmazás-hozzárendelése két elsődleges hozzárendelési módra összpontosít:

* **Egyéni hozzárendelés** A globális rendszergazdai jogosultságokkal rendelkező informatikai rendszergazdák kijelölhetik az egyes felhasználói fiókokat, és hozzáférést biztosíthatnak az alkalmazáshoz.
* **Csoport alapú hozzárendelés (csak fizetett Azure ad)** A globális rendszergazdai jogosultságokkal rendelkező informatikai rendszergazdák hozzárendelhet egy csoportot az alkalmazáshoz. Az adott felhasználók hozzáférését az határozza meg, hogy a csoport tagjai-e, amikor megpróbálják elérni az alkalmazást. Más szóval a rendszergazda gyakorlatilag létrehozhat egy hozzárendelési szabályt, amely "a hozzárendelt csoport bármely aktuális tagja hozzáfér az alkalmazáshoz". Ha ezt a hozzárendelési lehetőséget választja, a rendszergazdák az Azure AD csoportos felügyeleti lehetőségei közül bármelyiket kihasználhatják, beleértve az [attribútum-alapú dinamikus csoportokat](../fundamentals/active-directory-groups-create-azure-portal.md), a külső rendszercsoportokat (például a helyszíni Active Directory vagy a munkanapokat), vagy Rendszergazda által felügyelt vagy önkiszolgáló által felügyelt csoportok. Egyetlen csoport egyszerűen hozzárendelhető több alkalmazáshoz, így biztosíthatja, hogy a hozzárendelési affinitással rendelkező alkalmazások megosszák a hozzárendelési szabályokat, így csökkentve az általános felügyelet bonyolultságát. Vegye figyelembe, hogy a beágyazott csoporttagságok nem támogatottak a csoport alapú hozzárendelések esetében.

A két hozzárendelési mód használatával a rendszergazdák bármilyen kívánt hozzárendelés-kezelési módszert érhetnek el.

Az Azure AD-vel a használati és a hozzárendelési jelentéskészítés teljes mértékben integrálva van, így a rendszergazdák egyszerűen jelenthetik be a hozzárendelési állapotot, a hozzárendelési hibákat és a használatot is.

## <a name="complex-application-assignment-with-azure-ad"></a>Összetett alkalmazás-hozzárendelés az Azure AD-vel
Vegyünk egy olyan alkalmazást, mint például a Salesforce. Számos szervezetben a Salesforce elsődlegesen a marketing-és értékesítési csapatok használják. Gyakran a marketing csapat tagjai magas jogosultsági szintű hozzáféréssel rendelkeznek a Salesforce, míg az értékesítési csapat tagjai korlátozott hozzáféréssel rendelkeznek. Sok esetben az információkkal dolgozó szakemberek széles köre korlátozott hozzáféréssel rendelkezik az alkalmazáshoz. Az ezekre a szabályokra vonatkozó kivételek bonyolítják az ügyet. Gyakran a marketing-vagy értékesítési vezető csapatok előjoga, hogy felhasználói hozzáférést biztosítson, vagy a szerepköröket az általános szabályoktól függetlenül módosítsa.

Az Azure AD-ben az olyan alkalmazások, mint a Salesforce, előre konfigurálhatók az egyszeri bejelentkezés (SSO) és az automatikus kiépítés számára. Az alkalmazás konfigurálása után a rendszergazda a megfelelő csoportok létrehozásához és hozzárendeléséhez igénybe vehet egy egyszeri műveletet. Ebben a példában a rendszergazda a következő hozzárendeléseket hajthatja végre:

* A [dinamikus csoportok](../fundamentals/active-directory-groups-create-azure-portal.md) úgy határozhatók meg, hogy automatikusan képviseljék a marketing-és értékesítési csapatok összes tagját olyan attribútumok használatával, mint például az osztály vagy a szerepkör:
  
  * A marketing-csoportok összes tagja hozzá lesz rendelve a "marketing" szerepkörhöz a Salesforce-ben
  * Az értékesítési csoportok összes tagja hozzá lesz rendelve az "értékesítés" szerepkörhöz a Salesforce-ben. A további finomítások több olyan csoportot is használhatnak, amelyek a különböző Salesforce-szerepkörökhöz rendelt regionális értékesítési csapatokat képviselik.
* A kivételi mechanizmus engedélyezéséhez létrehozható egy önkiszolgáló csoport az egyes szerepkörökhöz. Például a "Salesforce marketing Exception" csoport önkiszolgáló csoportként hozható létre. A csoport hozzárendelhető a Salesforce marketing szerepkörhöz, és a marketing Leadership csapata tulajdonosként is elvégezhető. A marketing Leadership csapat tagjai hozzáadhatnak vagy eltávolíthatnak felhasználókat, beállíthat egy csatlakozási szabályzatot, vagy akár jóváhagyhatja vagy megtagadhatja az egyes felhasználói kérések összekapcsolását. Ezt a mechanizmust olyan információkkal dolgozó szakemberek támogatják, akik nem igényelnek speciális képzést a tulajdonosok vagy a tagok számára.

Ebben az esetben az összes hozzárendelt felhasználó automatikusan a Salesforce lesz kiépítve, mivel azokat a különböző csoportokhoz adja hozzá a szerepkör-hozzárendelésük a Salesforce-ben való frissítésekor. A felhasználók a Microsoft alkalmazás-hozzáférési paneljén, az Office-webügyfeleken, vagy akár a szervezeti Salesforce bejelentkezési oldalán is megkereshetik és érhetik el a Salesforce. A rendszergazdák könnyedén megtekinthetik a használati és hozzárendelési állapotot az Azure AD jelentéskészítési funkciójával.

A rendszergazdák az [Azure ad feltételes hozzáférést](../active-directory-conditional-access-azure-portal.md) alkalmazhatják a hozzáférési szabályzatok meghatározott szerepkörökhöz való beállításához. Ezek a szabályzatok magukban foglalhatják, hogy a hozzáférés engedélyezett-e a vállalati környezeten kívül, és még a többtényezős hitelesítés vagy az eszközök követelményei a különböző esetekben való hozzáférés eléréséhez.

## <a name="next-steps"></a>További lépések
* [Alkalmazások védelme feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md)
* [Önkiszolgáló csoport kezelése/SSAA](../users-groups-roles/groups-self-service-management.md)
