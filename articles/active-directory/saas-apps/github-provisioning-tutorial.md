---
title: 'Oktatóanyag: Felhasználói kiépítés a GitHubhoz – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok githubra való automatikus kiépítésére és kivonásának kivonására.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82f7252f2d9cdd2c54fae593d8463bfe84bd6ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057651"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Oktatóanyag: A GitHub konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a Lépéseket, amelyeket végre kell hajtania a GitHubés az Azure AD automatikus kiépítése és a felhasználói fiókok kiépítése az Azure AD-ről a GitHub.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* A [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)szolgáltatásban létrehozott GitHub-szervezet, amelyhez a [GitHub Enterprise számlázási csomagszükséges](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* A GitHubon rendszergazdai engedélyekkel rendelkező felhasználói fiók a szervezetszámára
* Annak ellenőrzése, hogy az OAuth-hozzáférés meg lett-e adva a szervezet számára az [itt](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) leírtak szerint

> [!NOTE]
> Az Azure AD-kiépítési integráció a [GitHub SCIM API-ra](https://developer.github.com/v3/scim/)támaszkodik, amely a [GitHub Enterprise Cloud-ügyfelek](https://help.github.com/articles/github-s-products/#github-enterprise) számára érhető el a [GitHub Enterprise számlázási csomagban.](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="assigning-users-to-github"></a>Felhasználók hozzárendelése a GitHubhoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva van. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek szükségük van a GitHub-alkalmazáshoz való hozzáférésre. Miután eldöntötte, hozzárendelheti ezeket a felhasználókat a GitHub-alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Fontos tippek a felhasználók GitHubhoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a GitHub a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a GitHubhoz rendel, ki kell **választania** a Felhasználói szerepkört, vagy egy másik érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkör nem működik a kiépítéshez, és ezek a felhasználók kimaradnak.

## <a name="configuring-user-provisioning-to-github"></a>Felhasználói kiépítés konfigurálása a GitHubra

Ez a szakasz végigvezeti az Azure AD-t a GitHub felhasználói fióklétesítési API-jával való csatlakoztatásával, és a kiépítési szolgáltatás konfigurálásával hozza létre, frissítse és tiltsa le a hozzárendelt felhasználói fiókokat a GitHubon az Azure AD-ben a felhasználói és csoport-hozzárendelés alapján.

> [!TIP]
> Az [Azure Portalon](https://portal.azure.com)megadott utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a GitHubon. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Automatikus felhasználói fiók-kiépítés konfigurálása a GitHubra az Azure AD-ben

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

2. Ha már konfigurálta a GitHubot egyszeri bejelentkezéshez, keresse meg a GitHub példányát a keresési mező használatával. Ellenkező esetben válassza **a Hozzáadás** és a **GitHub** keresése az alkalmazásgyűjteményben. Válassza ki a GitHub ot a keresési eredmények között, és adja hozzá az alkalmazások listájához.

3. Válassza ki a GitHub példányát, majd válassza **a Kiépítés** lapot.

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![GitHub-kiépítés](./media/github-provisioning-tutorial/GitHub1.png)

5. A **Rendszergazdai hitelesítő adatok csoportban** kattintson az **Engedélyezés gombra.** Ez a művelet megnyit egy GitHub-engedélyezési párbeszédpanelt egy új böngészőablakban. Ne feledje, hogy meg kell győződnie arról, hogy engedélyezték a hozzáférés engedélyezését. Kövesse az [itt](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)leírt utasításokat .

6. Az új ablakban jelentkezzen be a GitHubon a rendszergazdai fiókjával. Az eredményül kapott engedélyezési párbeszédpanelen jelölje ki azt a GitHub-csapatot, amelyhez engedélyezni szeretné a kiépítést, majd válassza **az Engedélyezés lehetőséget.** Miután elkészült, térjen vissza az Azure Portalra a létesítési konfiguráció befejezéséhez.

    ![Engedélyezési párbeszédpanel](./media/github-provisioning-tutorial/GitHub2.png)

7. Az Azure Portalon adja meg a **bérlői URL-címet,** és kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD kapcsolódjon a GitHub-alkalmazáshoz. Ha a kapcsolat sikertelen, győződjön meg arról, hogy a GitHub-fiók rendszergazdai engedélyekkel rendelkezik, és a `https://api.github.com/scim/v2/organizations/<Organization_name>` **bérlői url-t** helyesen kell beadni, majd próbálja meg újra az "Engedélyezés" lépést (szabály szerint jelenthet **bérlői URL-címet:** , megtalálhatja a szervezeteket a GitHub-fiók: **Beállítások** > **szervezetek)** alatt.

    ![Engedélyezési párbeszédpanel](./media/github-provisioning-tutorial/GitHub3.png)

8. Írja be annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be a "E-mail értesítés küldése hiba esetén" jelölőnégyzetet.

9. Kattintson a **Mentés** gombra.

10. A Leképezések csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása a GitHubra**lehetőséget.

11. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD és a GitHub között szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kijelölt attribútumok a GitHub felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

12. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a GitHub, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban

13. Kattintson a **Mentés** gombra.

Ez a művelet elindítja a GitHubhoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását a Felhasználók és csoportok szakaszban. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakasz ban figyelheti az előrehaladást, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
