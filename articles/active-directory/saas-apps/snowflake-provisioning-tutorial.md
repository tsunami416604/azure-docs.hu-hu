---
title: 'Oktatóanyag: Hópehely konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiirtsa a felhasználói fiókokat a Hópehely szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063150"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Oktatóanyag: Hópehely konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Snowflake és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és de-kiépítse a felhasználókat és/vagy csoportokat a Hópehelybe.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [A Hópehely bérlő](https://www.Snowflake.com/pricing/).
* A Felhasználói fiók Snowflake rendszergazdai engedélyekkel.

## <a name="assigning-users-to-snowflake"></a>Felhasználók hozzárendelése a Hópehelyhez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Hópehelyhez. Miután úgy döntött, akkor hozzá ezeket a felhasználókat és / vagy csoportokat Snowflake az alábbi utasításokat itt:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Fontos tippek a felhasználók hópehelyhez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve A Hópehely az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót hópehelyhez rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-snowflake-for-provisioning"></a>Hópehely beállítása kiépítéshez

Mielőtt konfigurálná a Hópehely az Azure AD automatikus felhasználói kiépítés, engedélyeznie kell az SCIM kiépítése a Hópehely.

1. Jelentkezzen be a Hópehely Admin konzolra. Írja be az alábbi lekérdezést a kiemelt munkalapon, és kattintson a **Futtatás gombra.**

    ![Hópehely Admin konzol](media/Snowflake-provisioning-tutorial/image00.png)

2.  Egy SCIM-hozzáférési jogkivonat jön létre a hópehely-bérlő számára. A visszakereséshez kattintson az alábbi linkre.

    ![Hópehely Add SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Másolja a programot a létrehozott tokenértékre, és kattintson **a Kész gombra.** Ezt az értéket a **Titkos jogkivonat** mezőben adja meg a Hópihe-alkalmazás kiépítés lapján az Azure Portalon.

    ![Hópehely Add SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Hópehely hozzáadása a galériából

Konfigurálása Hópehely automatikus felhasználói kiépítés az Azure AD-vel, hozzá kell adnia Hópehely az Azure AD alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

**Ha hópelyhet szeretne hozzáadni az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Hópihe**, válassza **a Hópehely** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Hópehely az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Automatikus felhasználói kiépítés konfigurálása hópehelybe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le hópehelyben az Azure AD-ben lévő felhasználói és/vagy csoport-hozzárendelések alapján.

> [!TIP]
> Azt is választhatja, hogy saml-alapú egyszeri bejelentkezés a Snowflake , utasításait követve a [Snowflake Single sign-on tutorial](Snowflake-tutorial.md). Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>A Hópehely automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Hópehely**lehetőséget.

    ![A Hópehely link az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A Rendszergazdai hitelesítő adatok `https://<Snowflake Account URL>/scim/v2` szakaszban adja meg a bérlői URL-címet. Példa a bérlői URL-címre:`https://acme.snowflakecomputing.com/scim/v2`

6. Adja meg a **titkos jogkivonat**korábbi, beolvasott **SCIM hitelesítési token** értékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Hópehelyhez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Hópehely-fiók rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

7. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

8. Kattintson a **Mentés** gombra.

9. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása hópehelyhez**lehetőséget.

    ![Hópehely felhasználói térképezések](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Tekintse át az Azure AD-ről hópehelyre szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Hópehely felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Hópehely felhasználói attribútumok](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. A **Leképezések** csoportban válassza **az Azure Active Directory-csoportok szinkronizálása hópehelyhez**lehetőséget.

    ![Hópehely csoport térképezések](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Tekintse át az Azure AD-ről hópehelyre szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Hópehely csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Hópehely csoport attribútumok](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

14. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Hópehely, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

15. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Hópihe-be, ha kiválasztja a kívánt értékeket a **Beállítások** szakasz **Hatókör** területén. Ha ez a beállítás nem érhető el, konfigurálja a szükséges mezőket a Rendszergazdai hitelesítő adatok csoportban, kattintson a **Mentés gombra,** és frissítse a lapot. 

    ![Kiépítési hatókör](common/provisioning-scope.png)

16. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a hópehelyen végrehajtott összes műveletet.

    Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésének jelentéskészítése című témakörben](../app-provisioning/check-status-user-account-provisioning.md) olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* Hópehely generált SCIM tokenek lejár 6 hónap. Ne feledje, hogy ezeket frissíteni kell, mielőtt lejár, hogy a kiépítési szinkronizálások továbbra is működik. 

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókkiépítési kezelésének kezelése.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések
* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések becsatornázásáról.](../app-provisioning/check-status-user-account-provisioning.md)
