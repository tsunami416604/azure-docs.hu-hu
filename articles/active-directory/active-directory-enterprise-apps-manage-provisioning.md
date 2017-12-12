---
title: "Vállalati alkalmazások az Azure Active Directory-kezelés kialakítási felhasználói |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a felhasználói fiók kiépítése vállalati alkalmazások az Azure Active Directory használatával"
services: active-directory
documentationcenter: 
author: asmalser
manager: mtillman
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 122dbdb838377a36020f9ec692b38544004e676c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Vállalati alkalmazások az Azure portálon kiépítés felhasználói fiók kezelése
Ez a cikk ismerteti, hogyan használható a [Azure-portálon](https://portal.azure.com) automatikus felhasználói fiók üzembe helyezést és megszüntetést újra a számítógépet, amelyek támogatják ezt, különösen azokat, a "kiemelt" kategóriából a ahozzáadottalkalmazásokkezelése[ Az Azure Active Directory alkalmazáskatalógusában](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Automatikus felhasználói fiók kiépítése és annak működéséről kapcsolatos további információkért lásd: [Felhasználókiépítés és -megszüntetés automatizálása a SaaS-alkalmazásokhoz az Azure Active Directoryval](active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>A portál az alkalmazások keresése
Az egyszeri bejelentkezéshez egy könyvtárban, a használatával directory rendszergazda által beállított összes alkalmazás a [Azure Active Directory alkalmazáskatalógusában](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), tekinthetők meg és kezelhetők a [Azure-portálon](https://portal.azure.com). Az alkalmazások találhatók a **több szolgáltatások** &gt; **vállalati alkalmazások** a portál szakaszban. Vállalati alkalmazások olyan alkalmazások, telepített és a szervezetén belül.

![Vállalati alkalmazások panel][0]

Válassza a **összes alkalmazás** a bal oldali hivatkozást vannak konfigurálva, beleértve az alkalmazások a gyűjteményből hozzáadott összes alkalmazások listáját tartalmazza. Az erőforrás panel az alkalmazáshoz, ahol jelentések megtekinthetők az alkalmazáshoz, és különböző beállítások kezelheti egy alkalmazás kiválasztása tölti be.

Felhasználói fiók kialakítási beállításai kezelhető kiválasztásával **kiépítési** a bal oldalon.

![Alkalmazás erőforráspanelen][1]

## <a name="provisioning-modes"></a>Üzembe helyezési mód
A **kiépítési** panel kezdődik egy **mód** menü, amely bemutatja, milyen üzembe helyezési mód támogatja egy vállalati alkalmazás, és lehetővé teszik. Az elérhető lehetőségek a következők:

* **Automatikus** – Ez a beállítás akkor jelenik meg, ha az Azure AD automatikus API-alapú üzembe helyezés és/vagy az alkalmazás felhasználói fiókokat megszüntetést támogatja. Válassza ezt a módot jeleníti meg, amely végigvezeti a rendszergazdák a az alkalmazás felhasználói felügyeleti API csatlakozni az Azure AD konfigurálása, fiók-hozzárendelések és a munkafolyamatok, amelyek meghatározzák, hogyan felhasználói fiókhoz kell adatfolyam az Azure AD közötti létrehozásának illesztőfelület és a alkalmazást, és kezelése az Azure AD szolgáltatás kiépítését.
* **Manuális** -e beállítás jelenik meg, ha az Azure AD nem támogatja a felhasználói fiókok ezen alkalmazás automatikus kiépítése. Ez a beállítás azt jelenti, hogy felhasználói fiókot rögzíti az alkalmazásban tárolt felügyelni a külső folyamat, a felhasználó felügyeleti és üzembe helyezési képesség maga az alkalmazás (amelyek magukban foglalhatják SAML fordítója kiépítése) alapján.

## <a name="configuring-automatic-user-account-provisioning"></a>Automatikus felhasználói fiók kiépítése konfigurálása
Válassza a **automatikus** beállítás, amely négy részből oszlik képernyő jelenik meg:

### <a name="admin-credentials"></a>Rendszergazdai hitelesítő adatok
Ez azért, ahol a hitelesítő adatokat az alkalmazás felhasználói felügyeleti API van-e megadva csatlakozni az Azure AD szükséges. A szükséges adatokat az alkalmazástól függően változik. A hitelesítőadat-típusok és adott alkalmazásokra vonatkozó követelmények, lásd: a [konfigurációs oktatóanyag az adott alkalmazás](active-directory-saas-app-provisioning.md).

Válassza a **kapcsolat tesztelése** gomb lehetővé teszi a hitelesítő adatok tesztelése azzal, hogy az Azure AD kísérlet az alkalmazáshoz tartozó kiépítés alkalmazásokhoz a megadott hitelesítő adatok használatával.

### <a name="mappings"></a>Leképezések
Ez az adott rendszergazdák megtekintéséhez és szerkesztéséhez, milyen felhasználói attribútumok folyamat az Azure AD között és a célalkalmazás, ha a felhasználói fiókok vannak kiépítésekor vagy frissítésekor.

Nincs olyan előre konfigurált megfeleltetéseket az Azure AD felhasználói és minden SaaS-alkalmazás felhasználói objektumok között. Egyes alkalmazások más típusú objektumok, például a csoportok vagy névjegyek kezelése. Egyikének kiválasztásával a leképezések az a táblázat a jobbra, ahol azok megtekinthetők és testre szabott hozzárendelési szerkesztőt.

![Alkalmazás erőforráspanelen][2]

Támogatott testreszabások a következők:

* Engedélyezése és letiltása adott objektumok, például az SaaS-alkalmazás felhasználói objektum az az Azure AD felhasználói objektum leképezéseit.
* Szerkesztés az alkalmazás felhasználói objektum az az Azure AD-felhasználói objektum áramolnak mely attribútumok. További információ a címtárattribútum-leképezésben: [attribútum hozzárendelési típusokhoz ismertetése](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Szűrje az Azure AD hajt végre a célzott alkalmazás üzembe helyezési műveleteket. Ahelyett, hogy az Azure AD teljesen-objektumokat szinkronizálni, korlátozhatja a végrehajtott műveletekről. Például kiválasztásával csak **frissítés**, az Azure AD csak frissítések meglévő felhasználói fiókok az alkalmazásban, és nem hoz létre újakat. Csak kiválasztásával **létrehozása**, Azure csak új felhasználói fiókokat hoz létre, de nem frissíti a már meglévőket. Ez a funkció lehetővé teszi, hogy a rendszergazdák számára, hogy a fiók létrehozásához különböző hozzárendelések létrehozása és frissítése a munkafolyamatok.

### <a name="settings"></a>Beállítások
Ez a szakasz lehetővé teszi, hogy a rendszergazdák számára, hogy a start és állítsa le az Azure AD a kijelölt alkalmazás, szolgáltatás kiépítését, valamint a nem kötelező a kiépítési gyorsítótárának, és indítsa újra a szolgáltatást.

Ha a kiépítés alatt engedélyezve van az alkalmazás első alkalommal, kapcsolja be a szolgáltatás módosításával a **kiépítési állapot** való **a**. Ennek hatására az Azure AD szolgáltatás kiépítését végrehajtásához egy kezdeti szinkronizálást, ahol a felhasználók a olvassa be a **felhasználók és csoportok** szakaszban azokat a célalkalmazás lekérdezi és hajtja végre az üzembe helyezési általuk meghatározott műveletek az Azure AD-ben **hozzárendelések** szakasz. A folyamat során a létesítési szolgáltatás milyen felhasználói fiókok kezel, a gyorsítótárazott adatokat tárolja, hogy a nem kezelt fiókok belül a célalkalmazások soha nem található a hozzárendelési hatókör megszüntetést műveletek nem érinti. A kezdeti szinkronizálást követően a létesítési szolgáltatás automatikusan szinkronizálja a felhasználói és csoportobjektumok 10 perces időközönként.

Módosítása a **kiépítési állapot** való **ki** egyszerűen felfüggeszti a létesítési szolgáltatás. Ebben az állapotban lévő Azure nem létrehozása, frissítése, vagy távolítsa el a felhasználói és csoportobjektumok az alkalmazásban. Váltás az állapot vissza a azt eredményezi, a szolgáltatás, ahol abbahagyta átvételéhez.

Válassza a **törölje az aktuális állapotát, és indítsa újra a szinkronizálási** jelölőnégyzetet és mentése leállítja a kiépítési szolgáltatást a gyorsítótárazott adatokat az Azure AD milyen fiókokról kezel, újraindítja a szolgáltatásokat, és elvégzi a kezdeti memóriaképek újra a szinkronizálást. Ez a beállítás lehetővé teszi a rendszergazdák a kiépítési folyamat elindításához ismét keresztül.

### <a name="synchronization-details"></a>Szinkronizálás részletei
Ez a szakasz ismerteti továbbá a létesítési szolgáltatás, beleértve az első és utolsó időpontokat, az alkalmazás, és hogy hány felhasználói és csoportobjektumok felügyelt lefutott a létesítési szolgáltatás működését.

Hivatkozásokkal a **tevékenységgel kapcsolatos jelentés kiépítés**, amely biztosítja, hogy a napló összes felhasználók és csoportok létrehozása, frissítése és eltávolított között az Azure AD és a cél alkalmazás, és a **esetleges hibajelentésben való megjelenítéshezkiépítése** biztosító részletes hibaüzenetek a felhasználó- és csoportobjektumok, melyeknél nem sikerült olvasni, létrehozni, frissíteni vagy eltávolítani. 

##<a name="feedback"></a>Visszajelzés

Reméljük, például a Azure AD felhasználói élményt. Ne hamarosan visszajelzését! Visszajelzését és ötleteket javítására szolgáló utáni a **felügyeleti portál** szakasza a [visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Ritkán használt adatok új dolgai minden nap kiépítésével foglalkozó még érdeklődőbbek és felhasználása a shape útmutatást és határozza meg, mi készíteni.


[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
