---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező N2F - költségjelentés |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és N2F - között költségjelentés.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 2851383ed992b314af541b24e7753b1e6b12b28d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898669"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező N2F - költségjelentés

Ebben az oktatóanyagban megismerheti, hogyan integrálhatja N2F - költségjelentés az Azure Active Directoryval (Azure AD).
Integráló N2F - költségjelentések az Azure ad-ben a következő előnyöket biztosítja:

* Költségjelentés szabályozhatja, hogy ki férhet hozzá N2F – Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve N2F - költségjelentések (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

N2F - költségjelentések, az Azure AD-integráció konfigurálása a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* N2F - költségjelentések egy bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* N2F - költségjelentés támogatja **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Hozzáadás N2F - költségjelentések a katalógusból

N2F – az Azure AD-be a költségjelentések integráció konfigurálásához, hozzá kell adnia N2F - kiadásokról szóló jelentéseket a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a N2F - katalógusból, költségjelentések hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **N2F - költségjelentések**, jelölje be **N2F - költségjelentések** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![N2F - költségjelentések a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés az N2F – alapján egy tesztfelhasználót költségjelentések nevű **Britta Simon**.
Az egyszeri bejelentkezés a munkahelyi Azure AD-felhasználót és a kapcsolódó felhasználó N2F - hivatkozás kapcsolata a költségjelentés kell létrehozni.

Az Azure AD egyszeri bejelentkezés az N2F - költségjelentések, tesztelése és konfigurálása kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[N2F konfigurálása – egyszeri bejelentkezés költségjelentés](#configure-n2f---expense-reports-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre N2F – költségjelentések tesztfelhasználó](#create-n2f---expense-reports-test-user)**  -, N2F rendelkezik egy megfelelője a Britta Simon – költségeket jelent, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés az N2F - költségjelentések, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **N2F - költségjelentések** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, a felhasználó nem rendelkezik minden lépést végrehajtani, mert az alkalmazás már előre integrált, az Azure-ral.

    ![N2F - tartomány és URL-címek költségjelentések egyszeri bejelentkezési adatait](common/preintegrated.png)

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![N2F - tartomány és URL-címek költségjelentések egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe:  `https://www.n2f.com/app/`

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

7. Az a **myPolicies beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>N2F - költségjelentések egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a N2F - költségek jelentések vállalati hely rendszergazdaként.

2. Kattintson a **beállítások** majd **speciális beállítások** a legördülő listából.

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure1.png)

3. Válassza ki **Fiókbeállítások** fülre.

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure2.png)

4. Válassza ki **hitelesítési** majd **+ Hozzáadás hitelesítési módszerként** lapon.

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure3.png)

5. Válassza ki **SAML Microsoft Office 365** hitelesítési módszerként.

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure4.png)

6. Az a **hitelesítési módszer** szakaszban, hajtsa végre az alábbi lépéseket:

    ![N2F - költségjelentések konfiguráció](./media/n2f-expensereports-tutorial/configure5.png)

    a. Az a **Entitásazonosító** szövegmezőjébe illessze be a **az Azure AD-azonosító** érték, amely az Azure Portalról másolta.

    b. Az a **metaadatok URL-címe** szövegmezőjébe illessze be a **alkalmazás összevonási metaadatainak URL-címe** érték, amely az Azure Portalról másolta.

    c. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon által biztosított hozzáférés N2F használandó Azure egyszeri bejelentkezés engedélyezése – költségjelentés.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **N2F - költségjelentések**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **N2F - költségjelentések**.

    ![A N2F - költségjelentés hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-n2f---expense-reports-test-user"></a>Hozzon létre N2F – költségjelentések tesztfelhasználó

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

    a. Az a **E-mail-cím** szövegmezőbe írja be például a felhasználó e-mail-címe **brittasimon\@contoso.com**.

    b. Az a **Utónév** szövegmezőbe írja be például a felhasználó utónevét **Britta**.

    c. Az a **neve** szövegmezőbe írja be például a felhasználó nevét **BrittaSimon**.

    d. Válasszon **szerepkör (N + 1) közvetlen manager**, és **osztás** a szervezeti követelmények alapján.

    e. Kattintson a **ellenőrzése és a küldési meghívó**.

    > [!NOTE]
    > Ha a felhasználó hozzáadása során fennálló problémák, lépjen kapcsolatba [N2F - költségjelentések támogatási csoport](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattint a N2F – költségjelentések csempére a hozzáférési panelen, amikor meg kell lehet automatikusan bejelentkezett N2F - költségjelentések, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

