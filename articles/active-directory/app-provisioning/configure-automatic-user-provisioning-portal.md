---
title: A vállalati alkalmazások felhasználói kiépítési kezelése az Azure AD-ben
description: Megtudhatja, hogy miként kezelheti a vállalati alkalmazások felhasználói fiókkiépítését az Azure Active Directory használatával
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264127"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Vállalati alkalmazások felhasználói fiókkiépítésének kezelése az Azure Portalon

Ez a cikk az automatikus felhasználói fiók-kiépítés és az azt támogató alkalmazások kiépítésének általános lépéseit ismerteti. *A felhasználói fiókok kiépítése* az alkalmazás helyi felhasználói profiltárolójában a felhasználói fiókbejegyzések létrehozása, frissítése és/vagy letiltása. A legtöbb felhő- és SaaS-alkalmazás a felhasználói szerepkört és engedélyeket a felhasználó saját helyi felhasználói profiltárolójában tárolja, és az ilyen felhasználói bejegyzés jelenléte a felhasználó helyi tárolójában az egyszeri bejelentkezéshez és a munkához való *hozzáféréshez szükséges.* Ha többet szeretne tudni az automatikus felhasználói fiókok kiépítéséről, olvassa el [a Felhasználói kiépítés és a SaaS-alkalmazások létesítésének automatizálása az Azure Active Directoryval.](user-provisioning.md)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) rendelkezik egy katalógus, amely több ezer előre integrált alkalmazások, amelyek engedélyezve vannak az Azure AD automatikus kiépítéséhez. Először keresse meg az alkalmazásra vonatkozó létesítési beállítási oktatóanyagot az [Útmutatók listájában aRról, hogyan integrálható az SaaS-alkalmazások az Azure Active Directoryval.](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) Valószínűleg részletes útmutatást talál az alkalmazás és az Azure AD konfigurálásához a létesítési kapcsolat létrehozásához.

## <a name="finding-your-apps-in-the-portal"></a>Alkalmazások megkeresése a portálon

Az Azure Active Directory portálon megtekintheti és kezelheti az összes olyan alkalmazást, amely konfigurálva van a címtárban való egyszeri bejelentkezéshez. A vállalati alkalmazások olyan alkalmazások, amelyek a szervezeten belül vannak telepítve és használatban. A vállalati alkalmazások megtekintéséhez és kezeléséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Active Directory portált.](https://aad.portal.azure.com)
1. Válassza a **vállalati alkalmazások lehetőséget** a bal oldali ablaktáblából. Megjelenik az összes konfigurált alkalmazás listája, beleértve a katalógusból hozzáadott alkalmazásokat is.
1. Jelölje ki bármelyik alkalmazást az erőforrásablak ának betöltéséhez, ahol megtekintheti a jelentéseket és kezelheti az alkalmazásbeállításokat.
1. Válassza **a Kiépítés** lehetőséget a kijelölt alkalmazás felhasználói fiók kiépítési beállításainak kezeléséhez.

   ![A felhasználói fiókok kiépítési beállításainak kezeléséhez szolgáló kiépítési képernyő](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Kiépítési módok

A **kiépítés** ablaktábla egy **mód menüvel** kezdődik, amely egy vállalati alkalmazás számára támogatott létesítési módokat jelenít meg, és lehetővé teszi azok konfigurálását. A rendelkezésre álló lehetőségek a következők:

* **Automatikus** – Ez a beállítás akkor jelenik meg, ha az Azure AD támogatja az automatikus API-alapú kiépítése vagy az alkalmazás felhasználói fiókok kiépítésének kiépítése. Ebben a módban olyan felületet szeretne megjeleníteni, amely segít a rendszergazdáknak:

  * Az Azure AD konfigurálása az alkalmazás felhasználói felügyeleti API-jához való csatlakozáshoz
  * Fiókleképezések és munkafolyamatok létrehozása, amelyek meghatározzák, hogy a felhasználói fiók adatai hogyan folynak az Azure AD és az alkalmazás között
  * Az Azure AD-kiépítési szolgáltatás kezelése

* **Manuális** – Ez a beállítás akkor jelenik meg, ha az Azure AD nem támogatja a felhasználói fiókok automatikus kiépítését az alkalmazásba. Ebben az esetben az alkalmazásban tárolt felhasználói fiókrekordokat egy külső folyamat használatával kell kezelni, az adott alkalmazás által biztosított felhasználói felügyeleti és kiépítési képességek alapján (amely magában foglalhatja az SAML just-in-time kiépítését).

## <a name="configuring-automatic-user-account-provisioning"></a>Automatikus felhasználói fiók-kiépítés konfigurálása

Válassza az **Automatikus** lehetőséget a rendszergazdai hitelesítő adatok, a hozzárendelések, a kezdés és leállítás, valamint a szinkronizálás beállításainak megadásához.

### <a name="admin-credentials"></a>Rendszergazdai hitelesítő adatok

Bontsa ki a **rendszergazdai hitelesítő adatokat** az Azure AD-hez az alkalmazás felhasználói felügyeleti API-jához való csatlakozáshoz szükséges hitelesítő adatok megadásához. A szükséges bemenet az alkalmazástól függően változik. Az adott alkalmazás hitelesítő adatok típusairól és követelményeiről az [adott alkalmazás konfigurációs oktatóanyaga](user-provisioning.md)című témakörben olvashat.

Válassza a **Kapcsolat tesztelése** a hitelesítő adatok teszteléséhez, ha az Azure AD megkísérli az alkalmazás létesítési alkalmazáshoz való csatlakozást a megadott hitelesítő adatok használatával.

### <a name="mappings"></a>Leképezések

A **Leképezések** csomópontot az Azure AD és a célalkalmazás között a felhasználói fiókok kiépítésekor vagy frissítésekor futó felhasználói attribútumok megtekintéséhez és szerkesztéséhez bontsa ki.

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között előre konfigurált leképezések előre konfigurált készlete található. Egyes alkalmazások más típusú objektumokat kezelnek, például csoportokat vagy névjegyeket. Jelöljön ki egy leképezést a táblában a jobb oldali leképezési szerkesztő megnyitásához, ahol megtekintheti és testreszabhatja őket.

![Az Attribútumleképezés képernyő megjelenítése](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

A támogatott testreszabások a következők:

* Adott objektumok, például az Azure AD felhasználói objektum leképezésének engedélyezése és letiltása az SaaS-alkalmazás felhasználói objektumához.
* Az Azure AD felhasználói objektumból az alkalmazás felhasználói objektumába áramló attribútumok szerkesztése. Az attribútumleképezésről az [Attribútumleképezési típusok ismertetése](customize-application-attributes.md#understanding-attribute-mapping-types)című témakörben talál további információt.
* Az Azure AD által a megcélzott alkalmazáson futtatott létesítési műveletek szűrése. Ahelyett, hogy az Azure AD teljesen szinkronizálja az objektumokat, korlátozhatja a műveletek futtatását.

  Például csak **válassza a Frissítés** és az Azure AD csak frissíti a meglévő felhasználói fiókok egy alkalmazásban, de nem hoz létre újakat. Csak válassza **a Create and** Azure csak új felhasználói fiókokat hoz létre, de nem frissíti a meglévőket. Ez a funkció lehetővé teszi a rendszergazdák számára, hogy különböző hozzárendeléseket hozzanak létre a fiók létrehozásához és a munkafolyamatok frissítéséhez.

* Új attribútumleképezés hozzáadása. Az **Attribútumleképezés** ablaktábla alján válassza az **Új leképezés hozzáadása** lehetőséget. Töltse ki az **Attribútum szerkesztése űrlapot,** és válassza az **Ok gombra** lehetőséget, ha hozzá szeretné adni az új hozzárendelést a listához.

### <a name="settings"></a>Beállítások

Elindíthatja és leállíthatja az Azure AD-kiépítési szolgáltatást a kiválasztott alkalmazáshoz a **Kiépítés** képernyő **Beállítások** területén. Azt is választhatja, hogy törölje a létesítési gyorsítótárat, és indítsa újra a szolgáltatást.

Ha egy alkalmazás első alkalommal engedélyezve van, kapcsolja be a szolgáltatást a **Kiépítési állapot** **bekapcsolva**állapotértékre módosításával. Ez a módosítás hatására az Azure AD-kiépítési szolgáltatás egy kezdeti ciklus futtatásához. Beolvassa a felhasználók rendelt a **felhasználók és** csoportok szakaszban, lekérdezi a célalkalmazást, és majd futtatja az Azure **AD-leképezések** szakaszban meghatározott létesítési műveleteket. A folyamat során a létesítési szolgáltatás tárolja a gyorsítótárazott adatokat arról, hogy milyen felhasználói fiókokat kezel, így a nem kezelt fiókok a célalkalmazásokon belül, amelyek soha nem voltak hatókörben hozzárendelésnem érinti a de-kiépítési műveletek. A kezdeti ciklus után a létesítési szolgáltatás automatikusan szinkronizálja a felhasználói és csoport objektumok egy negyven perces időközzel.

Módosítsa a **kiépítési állapot** **ot Kikapcsolva** állapotra a kiépítési szolgáltatás szüneteltetéséhez. Ebben az állapotban az Azure nem hoz létre, nem frissít és nem távolít el felhasználói vagy csoportobjektumokat az alkalmazásban. Módosítsa az állapotot **Be** állapotra, és a szolgáltatás ott folytatja, ahol abbahagyta.

**Az aktuális állapot törlésének és a szinkronizálás újraindításának törlése** kezdeti ciklust indít el. A szolgáltatás ezután újra kiértékeli a forrásrendszer összes felhasználóját, és megállapítja, hogy a kiépítés hatókörében vannak-e. Ez akkor lehet hasznos, ha az alkalmazás jelenleg karanténban van, vagy módosítania kell az attribútumleképezéseket. Vegye figyelembe, hogy a kezdeti ciklus befejezése hosszabb időt vesz igénybe, mint a tipikus növekményes ciklus a kiértékelendő objektumok száma miatt. A kezdeti és a növekményes ciklusok teljesítményéről itt olvashat [bővebben.](application-provisioning-when-will-provisioning-finish-specific-user.md) 
