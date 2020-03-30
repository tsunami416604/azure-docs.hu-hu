---
title: 'Oktatóanyag: Az iProva konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az Azure Active Directoryt az iProva felhasználói fiókjainak automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: bb730bad2837616aee0ebfa2da04015542782d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057501"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Oktatóanyag: Az iProva konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az iProva és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és de-de-provision felhasználók és/vagy csoportok iProva.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Egy iProva bérlő](https://www.iProva.com/)
* Az iProva felhasználói fiókja rendszergazdai engedélyekkel.

## <a name="assigning-users-to-iprova"></a>Felhasználók hozzárendelése az iProva-hoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az iProva-hoz. Miután úgy döntött, akkor ezeket a felhasználókat és / vagy csoportokat iProva az alábbi utasításokat itt:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>Fontos tippek a felhasználók iProva-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve az iProva az automatikus felhasználói létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót az iProva-hoz rendel, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-iprova-for-provisioning"></a>Az iProva beállítása a kiépítéshez

1. Jelentkezzen be az [iProva admin konzolra.](https://www.iProva.com/) Nyissa meg **az > alkalmazáskezelés**lapot.

    ![iProva felügyeleti konzol](media/iprova-provisioning-tutorial/admin.png)

2.  Kattintson a **Külső felhasználókezelés gombra.**

    ![iProva hozzáadása SCIM](media/iprova-provisioning-tutorial/external.png)

3. Új szolgáltató hozzáadásához kattintson a **plusz** ikonra. Az új **Szolgáltató hozzáadása** párbeszédpanelen adja meg a **Címet**. **Ip-alapú hozzáférési korlátozást**is hozzáadhat. Kattintson az **OK** gombra.

    ![iProva új](media/iprova-provisioning-tutorial/add.png)

    ![iProva hozzáadásszolgáltató](media/iprova-provisioning-tutorial/addprovider.png)

4.  Kattintson az **Állandó token** gombra. Másolja az **állandó jogkivonatot,** és mentse el, mivel ez lesz az egyetlen alkalom, amikor megtekintheti. Ez az érték az azure portalon az iProva-alkalmazás kiépítés lapján a Titkos jogkivonat mezőben lesz megadva.

    ![iProva token létrehozása](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>Add iProva a galériából

Az iProva konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, hozzá kell adnia az iProva-t az Azure AD-alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

**Az iProva hozzáadása az Azure AD alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **iProva**elemet , válassza az **iProva** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![iProva az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>Automatikus felhasználói kiépítés konfigurálása az iProva rendszerbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le az iProva-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Azt is választhatja, hogy saml-alapú egyszeri bejelentkezés iProva utasításait követve az [iProva Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial). Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Az iProva automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **iProva**lehetőséget.

    ![Az iProva hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://identitymanagement.services.iProva.nl/scim` csoportban adja meg a **bérlői URL-címet.** Adja meg a titkos jogkivonat korábbi, korábbi beolvasása kor beolvasott **állandó** **jogkivonatértékét.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud az iProva-hoz. Ha a kapcsolat nem sikerül, győződjön meg róla, hogy az iProva fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása az iProva szolgáltatásba**lehetőséget.

    ![iProva felhasználói leképezések](media/iprova-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD és az iProva között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok az iProva felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![iProva felhasználói attribútumok](media/iprova-provisioning-tutorial/userattributes.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása az iProva szolgáltatásba**lehetőséget.

    ![iProva csoport leképezései](media/iprova-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD és az iProva között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok az iProva csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![iProva csoport attribútumai](media/iprova-provisioning-tutorial/groupattributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-létesítési szolgáltatás engedélyezéséhez az iProva, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az iProva-ra a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz ban figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által az iProva-n végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

