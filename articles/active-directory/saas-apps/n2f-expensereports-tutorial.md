---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező N2F - költségjelentés |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és N2F - között költségjelentés.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3166a4e19bc137d57b97dbb516e7037228e1c020
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188817"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező N2F - költségjelentés

Ebben az oktatóanyagban megismerheti, hogyan integrálhatja N2F - költségjelentés az Azure Active Directoryval (Azure AD).

Integráló N2F - költségjelentések az Azure ad-ben a következő előnyöket biztosítja:

- Költségjelentés szabályozhatja, hogy ki férhet hozzá N2F – Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett N2F - költségjelentések (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

N2F - költségjelentések, az Azure AD-integráció konfigurálása a következőkre van szükség:

- Azure AD-előfizetés
- Egy N2F - költségjelentések egy bejelentkezési engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás N2F - költségjelentések a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Hozzáadás N2F - költségjelentések a katalógusból

N2F – az Azure AD-be a költségjelentések integráció konfigurálásához, hozzá kell adnia N2F - kiadásokról szóló jelentéseket a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a N2F - katalógusból, költségjelentések hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **N2F - költségjelentések**, jelölje be **N2F - költségjelentések** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![N2F - költségjelentések a találatok listájában](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés N2F – alapján egy tesztfelhasználót költségjelentések az úgynevezett "Britta Simon".

Egyszeri bejelentkezés használatát Azure ad-ben tudnia kell, milyen a partner felhasználó N2F – a beküldéstől, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó N2F - hivatkozás kapcsolata költségjelentés kell létrehozni.

Az Azure AD egyszeri bejelentkezés az N2F - költségjelentések, tesztelése és konfigurálása kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **Hozzon létre egy N2F – költségjelentések tesztfelhasználó** -, N2F rendelkezik egy megfelelője a Britta Simon – költségeket jelent, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a N2F az egyszeri bejelentkezés konfigurálása,-költségjelentés alkalmazás.

**Konfigurálja az Azure AD egyszeri bejelentkezés az N2F - költségjelentések, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **N2F - költségjelentések** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. Az a **N2F - költségjelentés tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, a felhasználó nem rendelkezik, végrehajthatja a lépéseket, mivel az alkalmazás már az előre integrált Azure-t.

    ![N2F - tartomány és URL-címek költségjelentések egyszeri bejelentkezési adatait](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![N2F - tartomány és URL-címek költségjelentések egyszeri bejelentkezési adatait](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://www.n2f.com/app/`

5. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozás](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. Az a **N2F - költségjelentés konfigurációs** területén kattintson **konfigurálása N2F - költségjelentések** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** származó a **gyors útmutató szakaszban.**

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. Egy másik böngészőablakban jelentkezzen be a N2F - költségek jelentések vállalati hely rendszergazdaként.

9. Kattintson a **beállítások** majd **speciális beállítások** a legördülő listából.

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure1.png)

10. Válassza ki **Fiókbeállítások** fülre.

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure2.png)

11. Válassza ki **hitelesítési** majd **+ Hozzáadás hitelesítési módszerként** lapon.

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure3.png)

12. Válassza ki **SAML Microsoft Office 365** hitelesítési módszerként.

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure4.png)

13. Az a **hitelesítési módszer** szakaszban, hajtsa végre az alábbi lépéseket:

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure5.png)

    a. Az a **Entitásazonosító** szövegmezőjébe illessze be a **SAML Entitásazonosító** érték, amely az Azure Portalról másolta.

    b. Az a **metaadatok URL-címe** szövegmezőjébe illessze be a **alkalmazás összevonási metaadatainak URL-címe** érték, amely az Azure Portalról másolta.

    c. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-n2f---expense-reports-test-user"></a>Hozzon létre egy N2F – költségjelentések tesztfelhasználó

Ahhoz, hogy az Azure AD-felhasználók N2F - költségjelentések, jelentkezzen be, ki kell építeni N2F - bA költségjelentés. N2F - költségjelentések, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a N2F - költségek jelentések vállalati hely rendszergazdaként.

2. Kattintson a **beállítások** majd **speciális beállítások** a legördülő listából.

   ![N2F - felhasználó költségelszámolás hozzáadása](./media/n2f-expensereports-tutorial/configure1.png)

3. Válassza ki **felhasználók** lap bal oldali navigációs panelen.

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/user1.png)

4. Válassza ki **+ új felhasználó** fülre.

   ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/user2.png)

5. Az a **felhasználói** szakaszban, hajtsa végre az alábbi lépéseket:

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/user3.png)

    a. Az a **E-mail-cím** szövegmezőbe írja be például a felhasználó e-mail-címe **brittasimon@contoso.com**.

    b. Az a **Utónév** szövegmezőbe írja be például a felhasználó utónevét **Britta**.

    c. Az a **neve** szövegmezőbe írja be például a felhasználó nevét **BrittaSimon**.

    d. Válasszon **szerepkör (N + 1) közvetlen manager**, és **osztás** a szervezeti követelmények alapján.

    e. Kattintson a **ellenőrzése és a küldési meghívó**.

    > [!NOTE]
    > Ha a felhasználó hozzáadása során fennálló problémák, lépjen kapcsolatba [N2F - költségjelentések támogatási csoport](mailto:support@n2f.com)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon által biztosított hozzáférés N2F használandó Azure egyszeri bejelentkezés engedélyezése – költségjelentés.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése N2F - költségjelentések, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **N2F - költségjelentések**.

    ![A N2F - költségjelentés hivatkozásra az alkalmazások listáját](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A N2F kattintva – költségjelentések csempére a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett, a N2F - költségjelentés alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

