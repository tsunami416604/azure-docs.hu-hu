---
title: Vállalati alkalmazások az Azure Active Directory felügyeleti felhasználókiépítés |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhasználói fiók kiépítése az Azure Active Directory használatával nagyvállalati alkalmazásokhoz
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 95d2f65e99b37b38f99ec5a750d74828661fe7ee
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44349143"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Felhasználói fiók kiépítése az Azure Portalon a vállalati alkalmazások kezelése
Ez a cikk ismerteti, hogyan használható a [az Azure portal](https://portal.azure.com) automatikus felhasználói fiók kiépítése és megszüntetést, amelyek támogatják ezt, különösen megjelennek a "kiemelt" kategóriát adta meg a hozzáadottalkalmazásokkezeléséhez[ Az Azure Active Directory alkalmazáskatalógusában](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery). Automatikus felhasználói fiók kiépítése, és hogyan működik kapcsolatos további tudnivalókért lásd: [automatizálhatja a Felhasználókiépítés és -megszüntetés SaaS-alkalmazásokhoz az Azure Active Directoryval](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>A portál az alkalmazások keresése
Az egyszeri bejelentkezés egy könyvtárban, a használatával directory rendszergazdája által beállított összes alkalmazás a [Azure Active Directory alkalmazáskatalógusában](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery), tekinthetők meg és kezelhetők a [az Azure portal](https://portal.azure.com). Tekintheti meg az alkalmazások a **minden szolgáltatás** &gt; **vállalati alkalmazások** a portál szakaszát. Vállalati alkalmazások olyan alkalmazások, telepített és a szervezetén belül.

![Vállalati alkalmazások panelen](./media/configure-automatic-user-provisioning-portal/enterprise-apps-pane.png)

Válassza a **minden alkalmazás** a bal oldali hivatkozásra alkalmazásokat, amelyeket konfigurálása megtörtént, beleértve a katalógusból hozzáadott alkalmazások listáját jeleníti meg. Alkalmazás kiválasztása betölti az erőforrás panel az adott alkalmazáshoz, ha jelentéseket tekinthet meg az adott alkalmazáshoz, és számos beállítást is felügyelhetők.

Felhasználói fiók üzembe helyezési beállítások kiválasztásával kezelhetők **kiépítési** a bal oldalon.

![Alkalmazás erőforrás panelen](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Kiépítési mód
A **kiépítési** ablaktábla kezdődik a **mód** menüben, amely bemutatja, milyen üzembe helyezési módok használata támogatott a vállalati alkalmazásokhoz, és lehetővé teszi, hogy kell konfigurálni. Az elérhető lehetőségek a következők:

* **Automatikus** – Ez a beállítás akkor jelenik meg, ha az Azure AD automatikus API-alapú üzembe helyezés és/vagy megszüntetést felhasználói fiókok ezen alkalmazás támogatja. Egy felületet, amely a rendszergazdák a konfigurálása az Azure ad-ben az alkalmazás felhasználókezelési API-hoz való kapcsolódáshoz, leképezéseket és a munkafolyamatok, amelyek meghatározzák a felhasználói fiók adatok közötti áramlásának módját az Azure AD kiválasztása ebben a módban jelenít meg, és a alkalmazás, és az Azure AD létesítési szolgáltatás kezelése.
* **Manuális** -e beállítás jelenik meg, ha az Azure AD nem támogatja az alkalmazás felhasználói fiókok automatikus kiépítésének. Ez a beállítás azt jelenti, hogy az alkalmazásban tárolt felhasználóifiók-rekordok felügyelni egy külső folyamattal, az adott alkalmazás (amelyek magukban foglalhatják SAML engedélyezték kiépítése) által biztosított felhasználói kezelés és üzembe helyezés képességeken alapulnak.

## <a name="configuring-automatic-user-account-provisioning"></a>Automatikus felhasználói fiók üzembe helyezésének konfigurálása
Válassza a **automatikus** lehetőség jelenít meg egy képernyő, amely négy részből áll:

### <a name="admin-credentials"></a>Rendszergazdai hitelesítő adatok
Ebben a szakaszban, ahol a hitelesítő adatokat szeretne csatlakozni a megadott API-t az alkalmazás felhasználói kezelése az Azure AD szükséges. A bemeneti szükséges az alkalmazás függvényében eltérő. A hitelesítő adatok típusát és az adott alkalmazásokra vonatkozó követelmények kapcsolatos további információkért tekintse meg a [konfigurációjára vonatkozó oktatóanyag, hogy az adott alkalmazáshoz](user-provisioning.md).

Válassza a **kapcsolat tesztelése** gomb lehetővé teszi, hogy a hitelesítő adatok tesztelése létesíteni az Azure AD próbál csatlakozni a az alkalmazás a kiépítés alkalmazást a megadott hitelesítő adatok használatával.

### <a name="mappings"></a>Leképezések
Ebben a szakaszban, ahol a rendszergazdák megtekinthetik és szerkeszthetik a felhasználói attribútumok folyamatot az Azure AD között és a cél alkalmazás, amikor a felhasználói fiókok vagy frissítése.

Van egy egy előre konfigurált Azure AD-felhasználói objektumok és az egyes SaaS-alkalmazás felhasználói objektumok közötti leképezéseket. Néhány alkalmazás más típusú objektumok, például a csoportok vagy névjegyek kezelése. Ha rákattint egy leképezések az a táblázat azt mutatja be a hozzárendelési szerkesztőt, a jobb oldalon, ahonnan lehet megtekinteni és testre szabott.

![Alkalmazás erőforrás panelen](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Támogatott testreszabások a következők:

* Engedélyezése és letiltása adott objektumok, például az SaaS-alkalmazás felhasználói objektum az Azure AD felhasználói objektum leképezések.
* Szerkesztés az attribútumokat, amelyek az alkalmazás felhasználói objektumhoz az Azure AD-felhasználói objektum áramlani. Attribútumleképzés további információkért lásd: [attribútum typy mapování ismertetése](customize-application-attributes.md#understanding-attribute-mapping-types).
* Szűrés az Azure AD hajt végre a célzott alkalmazás üzembe helyezési műveleteket. Ahelyett, hogy az Azure AD-objektumok teljes szinkronizálása, korlátozhatja a végrehajtott műveleteket. Például kiválasztásával csak **frissítés**, az Azure AD csak frissítések meglévő felhasználói fiókok egy alkalmazásban, és hozzon létre újakat. Csak kiválasztásával **létrehozás**, az Azure csak új felhasználói fiókokat hoz létre, de nem frissíti a már meglévőket. Ez a funkció lehetővé teszi, hogy a rendszergazdák a fióklétrehozás különböző leképezéseit létrehozni és frissíteni a munkafolyamatokat.

### <a name="settings"></a>Beállítások
Ez a szakasz lehetővé teszi, hogy a rendszergazdák, indítsa el és állítsa le az Azure AD létesítési szolgáltatás a kijelölt alkalmazás, valamint a szükség esetén a kiépítési gyorsítótár törléséhez, és indítsa újra a szolgáltatást.

Ha kiépítés folyamatban engedélyezve van egy alkalmazás először, kapcsolja be a szolgáltatás úgy módosítja a **üzembe helyezési állapotra** való **a**. Ez a módosítás hatására az Azure AD létesítési szolgáltatás végrehajtásához egy kezdeti szinkronizálást, ahol a felhasználók a olvas be a **felhasználók és csoportok** szakaszban azokat a célalkalmazás lekérdezi és az üzembe helyezési műveleteket végez az Azure AD-ben definiált **leképezések** szakaszban. A folyamat során a kiépítési szolgáltatás milyen felhasználói fiókok kezelését, gyorsítótárazott adatokat tárolja, így nem felügyelt fiókokat soha nem található a hozzárendelési hatókör cél alkalmazások belül nem vonatkozik a megszüntetést műveletek. A kezdeti szinkronizálást követően a kiépítési szolgáltatás automatikusan szinkronizálja a felhasználói és csoportobjektumokhoz 10 perces időközönként.

Módosítása a **üzembe helyezési állapotra** való **ki** egyszerűen felfüggeszti a kiépítési szolgáltatáshoz. Ebben az állapotban az Azure nem létrehozása, frissítése vagy távolítsa el a felhasználói és csoportobjektumokhoz az alkalmazásban. Az állapot módosítása vissza a on rendelkezik a szolgáltatás folytattuk a munkát, ahol abbahagyta.

Válassza a **jelenlegi állapot törlése és szinkronizálás újraindítása** jelölőnégyzetet és mentése leállítja a kiépítési szolgáltatás fiókok Azure ad-ben a gyorsítótárazott adatokat kezel, újraindítja a szolgáltatásokat, és elvégzi a kezdeti memóriaképek a szinkronizálás újra. Ez a beállítás lehetővé teszi, hogy a rendszergazdák a kiépítési központi telepítési folyamat elindításához ismét keresztül.

### <a name="synchronization-details"></a>Szinkronizálás részletei
Ebben a szakaszban a művelet a kiépítési szolgáltatást, többek között az első és utolsó időpontokat, az alkalmazást, és hogy hány felhasználói és csoportobjektumokhoz kezelt lefutott a kiépítési szolgáltatás emellett részletesen ismerteti.

Mutató hivatkozások a **kiépítési tevékenységek jelentésének** biztosít naplózása a minden felhasználó és csoport hozta létre, frissített, és az eltávolított között az Azure AD és a cél alkalmazás, és a **hibajelentéskiépítése** , amely biztosítja a részletes hibaüzenetek, felhasználó és csoport objektumok, amelyek nem lehet olvasni, létrehozott, frissítve vagy eltávolítva. 

## <a name="feedback"></a>Visszajelzés

Ne zárja be a visszajelzés hamarosan elérhető! A visszajelzések és javítására szolgáló ötleteket közzététele a **felügyeleti portálon** szakaszában az [Visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  A mérnöki csapathoz nagyon jó cuccok új naponta létrehozásával kapcsolatos Izgatottan várjuk, és kövesse az alakzathoz az útmutatókat, és a meghatározásához, mit hozhat létre a következő.

