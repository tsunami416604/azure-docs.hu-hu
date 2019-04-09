---
title: Vállalati alkalmazások az Azure Active Directory felügyeleti felhasználókiépítés |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhasználói fiók kiépítése az Azure Active Directory használatával nagyvállalati alkalmazásokhoz
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03ca64f3f3d2f034433f2aaa49f6babb7f9e5b4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260289"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Felhasználói fiók kiépítése az Azure Portalon a vállalati alkalmazások kezelése

Ez a cikk ismerteti, hogyan használható a [az Azure portal](https://portal.azure.com) automatikus felhasználói fiók kiépítése, és az alkalmazásokat, amelyek támogatják ezt a megszüntetést kezeléséhez. Automatikus felhasználói fiók kiépítése, és hogyan működik kapcsolatos további tudnivalókért lásd: [automatizálhatja a Felhasználókiépítés és -megszüntetés SaaS-alkalmazásokhoz az Azure Active Directoryval](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>A portál az alkalmazások keresése

Az Azure Active Directory portál segítségével megtekintheti és kezelheti az egyszeri bejelentkezés egy címtárban beállított összes alkalmazás. Vállalati alkalmazások olyan alkalmazások, telepített és a szervezetén belül. Kövesse az alábbi lépéseket, megtekintése és kezelése a vállalati alkalmazások:

1. Nyissa meg a [Azure Active Directory portálon](https://aad.portal.azure.com).

1. Válassza ki **vállalati alkalmazások** a bal oldali ablaktáblán. Az összes konfigurált alkalmazások listája látható, beleértve az alkalmazásokat, amelyek a katalógusból lettek hozzáadva.

1. Válassza ki az erőforrás panel, ahol jelentések megtekintése és kezelése az alkalmazásbeállítások betölteni bármely alkalmazást.

1. Válassza ki **kiépítési** kezelheti a felhasználói fiók kiépítése a kiválasztott alkalmazás beállításait.

   ![Alkalmazás erőforrás panelen](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Kiépítési mód

A **kiépítési** ablaktábla kezdődik a **mód** menü, amely megjeleníti az üzembe helyezési módot támogat egy vállalati alkalmazás, és azok konfigurálását teszi lehetővé. Az elérhető lehetőségek a következők:

* **Automatikus** -e beállítás jelenik meg, ha az Azure AD automatikus API-alapú üzembe helyezés vagy megszüntetést felhasználói fiókok ezen alkalmazás támogatja-e. Válassza ki az ebben a módban jelenítse meg egy felületet, amely segítséget nyújt a rendszergazdáknak:

  * Az alkalmazás felhasználókezelési API-hoz csatlakozni az Azure AD konfigurálása
  * Hozzon létre a fiók-hozzárendelések és a munkafolyamatok, amelyek meghatározzák a felhasználói fiók adatok közötti áramlásának módját az Azure AD és az alkalmazás
  * Az Azure AD létesítési szolgáltatás kezelése

* **Manuális** -e beállítás jelenik meg, ha az Azure AD nem támogatja, ez az alkalmazás felhasználói fiókok automatikus kiépítésének. Ebben az esetben az alkalmazásban tárolt rekordok felhasználói fiókkal kell kezelnie egy külső folyamattal, az adott alkalmazás (amelyek magukban foglalhatják SAML engedélyezték kiépítése) által biztosított felhasználói kezelés és üzembe helyezés képességeken alapulnak.

## <a name="configuring-automatic-user-account-provisioning"></a>Automatikus felhasználói fiók üzembe helyezésének konfigurálása

Válassza ki a **automatikus** rendszergazdai hitelesítő adataival, a leképezések, elindítása és leállítása, és a szinkronizálási beállítások megadására.

### <a name="admin-credentials"></a>Rendszergazdai hitelesítő adatok

Bontsa ki a **rendszergazdai hitelesítő adataival** az Azure ad az alkalmazás felhasználókezelési API-hoz való kapcsolódáshoz szükséges hitelesítő adatok megadását. A bemeneti szükséges az alkalmazás függvényében eltérő. A hitelesítő adatok típusát és az adott alkalmazásokra vonatkozó követelmények kapcsolatos további információkért tekintse meg a [konfigurációjára vonatkozó oktatóanyag, hogy az adott alkalmazáshoz](user-provisioning.md).

Válassza ki **kapcsolat tesztelése** a hitelesítő adatok tesztelése kellene az Azure AD próbál csatlakozni a az alkalmazás-alkalmazást a megadott hitelesítő adatok használatával a kiépítés.

### <a name="mappings"></a>Leképezések

Bontsa ki a **leképezések** megtekintéséhez és szerkesztéséhez a felhasználói attribútumok, amelyek az Azure AD között, és a célalkalmazás, ha a felhasználói fiókok vagy frissítése.

Van egy egy előre konfigurált Azure AD-felhasználói objektumok és az egyes SaaS-alkalmazás felhasználói objektumok közötti leképezéseket. Néhány alkalmazás más típusú objektumok, például a csoportok vagy névjegyek kezelése. Válassza ki a leképezést a táblázatban, nyissa meg a hozzárendelési szerkesztőt, a jobb oldalon, melyen megtekintheti és testre is szabhatja őket.

![Alkalmazás erőforrás panelen](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Támogatott testreszabások a következők:

* Engedélyezése és letiltása adott objektumok, például az SaaS-alkalmazás felhasználói objektum az Azure AD felhasználói objektum leképezések.
* Szerkesztés az attribútumokat, amelyek az alkalmazás felhasználói objektumhoz az Azure AD-felhasználói objektum áramlani. Attribútumleképzés további információkért lásd: [attribútum typy mapování ismertetése](customize-application-attributes.md#understanding-attribute-mapping-types).
* Szűrés a célzott alkalmazás fut az Azure AD létesítési műveleteket. Az Azure AD-objektumok teljes szinkronizálása nem, korlátozhatja a műveletek futtatása. 

  Csak válassza ki például **frissítés** és az Azure ad-ben csak frissítések meglévő felhasználói fiókok egy alkalmazásban, de nem hozzon létre újakat. Csak válassza **létrehozás** és az Azure csak új felhasználói fiókokat hoz létre, de nem frissíti a már meglévőket. Ez a funkció lehetővé teszi, hogy a rendszergazdák a fióklétrehozás különböző leképezéseit létrehozása és frissítése a munkafolyamatokat.

* Egy új attribútum megfeleltetés hozzáadása. Válassza ki **új hozzárendelés hozzáadása** alján a **attribútumleképzés** ablaktáblán. Töltse ki a **attribútum szerkesztése** alkotnak, és válassza ki **Ok** az új megfeleltetés hozzáadása a listához. 

### <a name="settings"></a>Beállítások

Indítsa el, és állítsa le az Azure AD létesítési szolgáltatás a kijelölt alkalmazás a a **beállítások** területén a **kiépítési** képernyő. Azt is beállíthatja a kiépítési gyorsítótár törléséhez, és indítsa újra a szolgáltatást.

Ha kiépítés folyamatban engedélyezve van egy alkalmazás először, kapcsolja be a szolgáltatás úgy módosítja a **üzembe helyezési állapotra** való **a**. Ez a módosítás hatására az Azure AD létesítési szolgáltatás egy kezdeti szinkronizálást futtatni. A hozzárendelt felhasználók olvas be a **felhasználók és csoportok** szakaszban azokat a célalkalmazás lekérdezése, és ezután futtatja az Azure AD-ben definiált üzembe helyezési műveletek **leképezések** szakaszban. A folyamat során a kiépítési szolgáltatás milyen felhasználói fiókok kezelését, gyorsítótárazott adatokat tárolja, így nem felügyelt fiókokat soha nem található a hozzárendelési hatókör cél alkalmazások belül nem vonatkozik a megszüntetést műveletek. A kezdeti szinkronizálást követően a kiépítési szolgáltatás automatikusan szinkronizálja a felhasználói és csoportobjektumokhoz 10 perces időközönként.

Módosítsa a **üzembe helyezési állapotra** való **ki** a kiépítési szolgáltatás. Ebben az állapotban az Azure nem létrehozása, frissítése vagy távolítsa el a felhasználói és csoportobjektumokhoz az alkalmazásban. Módosítsa a biztonsági állapota **a** és a szolgáltatás szerzi be, ahol abbahagyta.

Válassza ki a **jelenlegi állapot törlése és szinkronizálás újraindítása** jelölőnégyzetet, és válassza ki **mentése** való:

* A kiépítési szolgáltatás leállítása
* A gyorsítótárazott adatok milyen Azure AD által kezelt fiókkal kapcsolatos memóriakép
* Indítsa újra a szolgáltatásokat, és futtassa újra a kezdeti szinkronizálás

Ez a beállítás lehetővé teszi, hogy a rendszergazdák az üzembe helyezési üzembe helyezés elindításához ismét keresztül.

### <a name="synchronization-details"></a>Szinkronizálás részletei

Ez a szakasz további információt nyújt a kiépítési szolgáltatást, többek között az első és utolsó időpontokat a kiépítési szolgáltatás lefutott az alkalmazást, és hány felhasználói és csoportobjektumokhoz kezeli a műveletet.

Egy hivatkozást a **kiépítési tevékenységek jelentésének**, amely biztosítja a napló az összes olyan felhasználó és csoportok létrehozott, frissített és eltávolított között az Azure AD és a cél alkalmazás. Hivatkozást is nyújtanak a **kiépítési hibákat tartalmazó jelentést**, amely biztosítja a részletes hibaüzeneteket, a felhasználói és csoportobjektumokhoz, amelyeket nem sikerült olvasni, hozta létre, frissített vagy eltávolítva.
