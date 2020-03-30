---
title: 'Oktatóanyag: Comeet toborzó szoftver konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat a Comeet toborzó szoftverbe.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: f427fb75cfaeda79b037c327992e4ad482a7e689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058333"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Oktatóanyag: A Comeet Toborzó szoftver konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Comeet Recruiting Software és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és dekontáló felhasználókat és/vagy csoportokat a Comeet toborzó szoftverbe.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [A Comeet toborzó szoftver bérlő](https://www.comeet.co/)
* A Comeet Toborzó szoftver rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Add Comeet toborzó szoftver a galériából

Mielőtt konfigurálna comeet toborzó szoftver automatikus felhasználói kiépítés az Azure AD,hozzá kell adnia Comeet toborzó szoftver az Azure AD alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

**Ha comeet toborzó szoftvert szeretne hozzáadni az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Comeet Recruiting Software ( Comeet Recruiting Software**) lehetőséget, válassza a **Comeet Recruiting Software elemet** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Comeet Toborzó Szoftver az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Felhasználók hozzárendelése a Comeet toborzó szoftverhez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Comeet toborzó szoftverhez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Comeet Recruiting Szoftverhez az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Fontos tippek a felhasználók Comeet toborzó szoftverhez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve comeet toborzó szoftver az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Comeet toborzó szoftverhez rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Automatikus felhasználói kiépítés konfigurálása comeet toborzó szoftverhez 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Comeet Recruiting Software-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Azt is választhatja, hogy saml-alapú egyszeri bejelentkezés comeet toborzó szoftver, utasításait követve a [Comeet Recruiting Software egyszeri bejelentkezés eded](comeetrecruitingsoftware-tutorial.md). Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>A Comeet recruiting szoftver automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Comeet Recruiting Software lehetőséget.**

    ![A Comeet toborzó szoftver hivatkozás az alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-t** és a Comeet toborzó szoftver fiókjának **titkos tokent** a 6.

6. A [Comeet Recruiting Software felügyeleti konzolon](https://app.comeet.co/)keresse meg a **Comeet > Beállítások > a Microsoft Azure-> a** **titkos tokent,** és másolja a vállalati érték titkos tokenjét az Azure AD **titkos token** mezőjébe.

    ![Comeet toborzó szoftver kiépítése](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Az 5. **Test Connection** Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Comeet Recruiting Software fiók rendelkezik admin engedéllyel, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-token.png)

8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása comeet lehetőséget.**

    ![Comeet toborzó szoftver felhasználói térképezések](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD-ből az **Attribútum-leképezés** szakaszban az Azure AD-ből a Comeet Recruiting Software szolgáltatásba szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kiválasztott attribútumok a Comeet Recruiting Software felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Comeet Toborzó Szoftver csoport attribútumok](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Comeet toborzó szoftver, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. A **Beállítások** szakasz **Hatókör** területén a kívánt értékek kiválasztásával határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Comeet Recruiting Szoftverbe.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amelyek ismertetik az Azure AD-kiépítési szolgáltatás által a Comeet recruiting szoftveren végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* A Comeet Recruiting Software jelenleg nem támogatja a csoportokat.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

