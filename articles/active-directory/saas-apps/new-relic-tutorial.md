---
title: 'Oktatóanyag: A New relic használatával az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a New relic-bővítménnyel között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 4d50c4366128edb5b4bc76408eb1085817af1ce3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271900"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Oktatóanyag: Az Azure Active Directory-integráció, a New relic használatával

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a New relic-bővítménnyel az Azure Active Directory (Azure AD).
New relic-bővítménnyel integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a New relic-bővítménnyel, Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a New relic-bővítménnyel (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a New relic-bővítménnyel, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Új Relic egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a New relic-bővítménnyel **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-new-relic-from-the-gallery"></a>New relic-bővítménnyel hozzáadása a katalógusból

Konfigurálhatja az Azure AD integrálása a New relic-bővítménnyel, hozzá kell New relic-bővítménnyel a galériából a felügyelt SaaS-alkalmazások listájára.

**New relic-bővítménnyel hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **New relic-bővítménnyel**, jelölje be **New relic-bővítménnyel** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában a New relic-bővítménnyel](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a New relic segítségével egy teszt nevű felhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a New relic-bővítménnyel hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés a New relic segítségével tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Új Relic egyszeri bejelentkezés konfigurálása](#configure-new-relic-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[New relic-bővítménnyel tesztfelhasználó létrehozása](#create-new-relic-test-user)**  – van egy Britta Simon megfelelője a New relic-bővítménnyel, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a New relic-bővítménnyel, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **New relic-bővítménnyel** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Új Relic tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be egy URL-címet a következő mintával: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` – ügyeljen arra, hogy cserélje le a saját New Relic-fiók azonosítóját.

    b. Az a **azonosító (entityid)** szövegmezőbe írja be egy URL-címe: `rpm.newrelic.com`

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **állítsa be a New relic-bővítménnyel** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-new-relic-single-sign-on"></a>Új Relic egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **New relic-bővítménnyel** rendszergazdaként a vállalati webhely.

2. A felső menüben kattintson **fiókbeállításokat**.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797036.png "Fiókbeállítások")

3. Kattintson a **biztonság és hitelesítés** fülre, majd a **egyszeri bejelentkezés** fülre.
   
    ![Egyszeri bejelentkezés](./media/new-relic-tutorial/ic797037.png "egyszeri bejelentkezés")

4. Az SAML párbeszédpanel oldalon hajtsa végre az alábbi lépéseket:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Kattintson a **fájl kiválasztása** a letöltött Azure Active Directory-tanúsítvány feltöltéséhez.

    b. Az a **távoli bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.
   
    c. A a **kijelentkezési URL-cím üzenetsorokra** szövegmezőjébe illessze be az értéket, **kijelentkezési URL-címe**, az Azure Portalról másolt.

    d. Kattintson a **módosítások mentése**.

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

Ebben a szakaszban engedélyezze Britta Simon a New relic-bővítménnyel való hozzáférés biztosítása az Azure egyszeri bejelentkezés használatával.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **New relic-bővítménnyel**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **New relic-bővítménnyel**.

    ![A New relic-bővítménnyel hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-new-relic-test-user"></a>New relic-bővítménnyel tesztfelhasználó létrehozása

Ahhoz, hogy az Azure Active Directory-felhasználók jelentkezzen be a New relic-bővítménnyel, akkor ki kell építeni, a New relic-bővítménnyel. New relic-bővítménnyel, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot a New relic-bővítménnyel, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **New relic-bővítménnyel** rendszergazdaként a vállalati webhely.

2. A felső menüben kattintson **fiókbeállításokat**.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797040.png "Fiókbeállítások")

3. Az a **fiók** a bal oldali ablaktáblán kattintson a **összefoglalás**, és kattintson a **felhasználó hozzáadása**.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797041.png "Fiókbeállítások")

4. Az a **aktív felhasználók** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Aktív felhasználók](./media/new-relic-tutorial/ic797042.png "aktív felhasználók")
   
    a. Az a **E-mail** szövegmezőbe írja be egy érvényes kíván üzembe helyezni Azure Active Directory-felhasználó e-mail-címét.

    b. Mint **szerepkör** kiválasztása **felhasználói**.

    c. Kattintson a **adja hozzá a felhasználót**.

>[!NOTE]
>Bármely más New relic-bővítménnyel felhasználói fiók létrehozása eszközöket használhatja, vagy aad-ben a felhasználói fiókok kiépítését a New Relic által biztosított API-k.
> 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a New relic-bővítménnyel csempére kattint, meg kell lehet automatikusan bejelentkezett a New relic-bővítménnyel, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

