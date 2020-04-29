---
title: 'Oktatóanyag: a hópehely konfigurálása automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Hópelyhekbe.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063150"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Oktatóanyag: a hópehely konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa a hópehely és Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a hópehely-t.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy hópehely bérlő](https://www.Snowflake.com/pricing/).
* Egy felhasználói fiók a hópehely-ban rendszergazdai engedélyekkel.

## <a name="assigning-users-to-snowflake"></a>Felhasználók kiosztása a hópehely-ban

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára van szükség a hópehely elérésére. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a következő utasításokat követve rendelheti hozzá a hópehely-hoz:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Fontos Tippek a felhasználók a hópehely-hoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen a hópehely-hoz rendelve az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor a felhasználó a hópehely-hoz van rendelve, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-snowflake-for-provisioning"></a>A hópehely beállítása a kiépítés számára

Mielőtt a hópehely-t konfigurálja az Azure AD-vel való automatikus felhasználói kiépítés során, engedélyeznie kell a SCIM-létesítést a hópehely-on.

1. Jelentkezzen be a hópehely felügyeleti konzolra. Adja meg az alább látható lekérdezést a munkalapon, és kattintson a **Futtatás**gombra.

    ![A hópehely felügyeleti konzol](media/Snowflake-provisioning-tutorial/image00.png)

2.  A SCIM hozzáférési token jön létre a hópehely bérlőhöz. A lekéréséhez kattintson az alábbi hivatkozásra.

    ![Hópehely SCIM hozzáadása](media/Snowflake-provisioning-tutorial/image01.png)

3. Másolja a generált jogkivonat értékét, és kattintson a **kész**gombra. Ez az érték a Azure Portalban lévő hópehely-alkalmazás létesítés lapjának **titkos jogkivonat** mezőjében lesz megadva.

    ![Hópehely SCIM hozzáadása](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>A hópehely hozzáadása a katalógusból

Ha a hópehely-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez szeretné konfigurálni, az Azure AD addSnowflake kell a felügyelt SaaS-alkalmazások listájára.

**A hópehely Azure AD-alkalmazás-katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **hópehely**kifejezést, válassza ki a **hópehely** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![A hópehely az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Az automatikus felhasználó-kiépítés beállítása a hópehely-ra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendeléseken alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le a hópehely-ban.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a hópehely esetében, a [hópehely egyszeri bejelentkezés oktatóanyagában](Snowflake-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>A hópehely automatikus felhasználó általi üzembe helyezésének beállítása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **hópehely**elemet.

    ![A hópehely hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja `https://<Snowflake Account URL>/scim/v2` meg a bérlői URL-címet. Példa a bérlő URL-címére:`https://acme.snowflakecomputing.com/scim/v2`

6. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a hópehely-hoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a hópehely-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

8. Kattintson a **Save** (Mentés) gombra.

9. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a hópehely-hoz**lehetőséget.

    ![Hópehely felhasználói leképezések](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Tekintse át az Azure AD-ból a hópehely-ra szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a hópehely felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A hópehely felhasználói attribútumai](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a hópehely-hoz**lehetőséget.

    ![A hópehely csoport leképezései](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Tekintse át az Azure AD-ból a hópehely-ra szinkronizált csoportok attribútumait az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a hópehely-beli csoportoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A hópehely csoport attribútumai](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a hópehely-hez, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

15. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a hópehely-t szeretné kiépíteni, majd a **Beállítások** szakaszban válassza ki a kívánt értékeket a **hatókörben** . Ha ez a lehetőség nem érhető el, konfigurálja a szükséges mezőket a rendszergazdai hitelesítő adatok területen, majd kattintson a **Save (Mentés** ) gombra, és frissítse az oldalt. 

    ![Kiépítési hatókör](common/provisioning-scope.png)

16. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a hópehely-on végrehajtott összes műveletet ismertetik.

    Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md) helyezésével kapcsolatban.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A hópehely által generált SCIM tokenek 6 hónapon belül lejárnak. Vegye figyelembe, hogy ezeket a lejáratuk előtt frissíteni kell, hogy a kiépítési szinkronizálások továbbra is működőképesek legyenek. 

## <a name="additional-resources"></a>További háttéranyagok

* [A vállalati alkalmazások felhasználói fiókok üzembe](../app-provisioning/configure-automatic-user-provisioning-portal.md)helyezésének kezelése.
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések
* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md).
