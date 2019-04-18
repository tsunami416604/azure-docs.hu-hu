---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Sprinklr |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Sprinklr között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 1c3b95686b8c91552615a9014102fd6a14f8c385
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59277068"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Sprinklr

Ebben az oktatóanyagban elsajátíthatja, hogyan Sprinklr integrálása az Azure Active Directory (Azure AD).
Sprinklr integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Sprinklr Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Sprinklr (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Sprinklr az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Sprinklr egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Sprinklr **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-sprinklr-from-the-gallery"></a>Sprinklr hozzáadása a katalógusból

Az Azure AD integrálása a Sprinklr konfigurálásához hozzá kell Sprinklr a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Sprinklr hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Sprinklr**válassza **Sprinklr** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Sprinklr](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Sprinklr nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Sprinklr hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Sprinklr tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Sprinklr egyszeri bejelentkezés konfigurálása](#configure-sprinklr-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Sprinklr tesztfelhasználót](#create-sprinklr-test-user)**  – egy megfelelője a Britta Simon Sprinklr, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Sprinklr, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Sprinklr** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Sprinklr tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.sprinklr.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [Sprinklr ügyfél-támogatási csapatának](https://www.sprinklr.com/contact-us/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Sprinklr beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-sprinklr-single-sign-on"></a>Sprinklr egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Sprinklr vállalati hely rendszergazdaként.

1. Lépjen a **felügyeleti \> beállítások**.

    ![Felügyeleti](./media/sprinklr-tutorial/ic782907.png "felügyelete")

1. Lépjen a **kezelése Partner \> egyszeri bejelentkezési** a bal oldali panelén.

    ![Partner kezelése](./media/sprinklr-tutorial/ic782908.png "Partner kezelése")

1. Kattintson a **+ Hozzáadás egyetlen bejelentkezéseknél**.

    ![Egyszeri bejelentkezés – Ons](./media/sprinklr-tutorial/ic782909.png "bejelentkezéseknél – egyszeri")

1. Az a **az egyszeri bejelentkezés** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés – Ons](./media/sprinklr-tutorial/ic782910.png "bejelentkezéseknél – egyszeri")

    a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például: *WAADSSOTest*).

    b. Válassza ki **engedélyezve**.

    c. Válassza ki **új SSO-tanúsítvány használatára**.

    d. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **szolgáltató Identitástanúsítványt** szövegmezőbe.

    e. Illessze be a **az Azure AD-azonosító** érték, amely az Azure Portalról másolta a **entitásazonosító** szövegmezőbe.

    f. Illessze be a **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta a **Identity Provider bejelentkezési URL-cím** szövegmezőbe.

    g. Illessze be a **kijelentkezési URL-címe** érték, amely az Azure Portalról másolta a **Identity Provider kijelentkezési URL-címe** szövegmezőbe.

    h. Mint **SAML felhasználói azonosító típusa**válassza **helyességi feltétel tartalmaz sprinklr.com felhasználónév**.

    i. Mint **SAML felhasználói azonosító hely**, jelölje be **felhasználói Azonosítóját a tulajdonos utasítás Névazonosító elemében van**.

    j. Kattintson a **Save** (Mentés) gombra.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Sprinklr Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Sprinklr**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Sprinklr**.

    ![Az alkalmazások listáját a Sprinklr hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sprinklr-test-user"></a>Sprinklr tesztfelhasználó létrehozása

1. Jelentkezzen be rendszergazdaként a Sprinklr vállalati webhely.

1. Lépjen a **felügyeleti \> beállítások**.

    ![Felügyeleti](./media/sprinklr-tutorial/ic782907.png "felügyelete")

1. Lépjen a **kezelése ügyfél \> felhasználók** a bal oldali ablaktáblán.

    ![Beállítások](./media/sprinklr-tutorial/ic782914.png "beállításai")

1. Kattintson a **felhasználó hozzáadása**.

    ![Beállítások](./media/sprinklr-tutorial/ic782915.png "beállításai")

1. Az a **szerkesztési felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Felhasználó szerkesztése](./media/sprinklr-tutorial/ic782916.png "felhasználó szerkesztése")

    a. Az a **E-mail**, **Utónév** és **Vezetéknév** , beleértve a szövegdobozokat, írja be az adatokat egy Azure AD felhasználói fiók kíván üzembe helyezni.

    b. Válassza ki **jelszó le van tiltva**.

    c. Válassza ki **nyelvi**.

    d. Válassza ki **felhasználótípus**.

    e. Kattintson az **Update** (Frissítés) elemre.

    > [!IMPORTANT]
    > **Jelszó le van tiltva** meg kell adnia egy felhasználó egy identitásszolgáltató használatával bejelentkezni. 

1. Lépjen a **szerepkör**, és hajtsa végre az alábbi lépéseket:

    ![Partner-szerepkörök](./media/sprinklr-tutorial/ic782917.png "Partner-szerepkörök")

    a. Az a **globális** listáról válassza ki **ALL_Permissions**.  

    b. Kattintson az **Update** (Frissítés) elemre.

> [!NOTE]
> Eszközt is használhat bármilyen más Sprinklr felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése Sprinklr által biztosított API-k.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Sprinklr csempére kattint, meg kell lehet automatikusan bejelentkezett a Sprinklr, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
