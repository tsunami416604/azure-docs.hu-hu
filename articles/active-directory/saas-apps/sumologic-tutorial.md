---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SumoLogic |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SumoLogic között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 4edd445fe526b9be5c421b21b25f9b095c734f43
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269894"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SumoLogic

Ebben az oktatóanyagban elsajátíthatja, hogyan SumoLogic integrálása az Azure Active Directory (Azure AD).
SumoLogic integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá SumoLogic Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve SumoLogic (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

SumoLogic az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* SumoLogic egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a SumoLogic **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-sumologic-from-the-gallery"></a>SumoLogic hozzáadása a katalógusból

Az Azure AD integrálása a SumoLogic konfigurálásához hozzá kell SumoLogic a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SumoLogic hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SumoLogic**válassza **SumoLogic** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában SumoLogic](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SumoLogic nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó SumoLogic hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az SumoLogic tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[SumoLogic egyszeri bejelentkezés konfigurálása](#configure-sumologic-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre SumoLogic tesztfelhasználót](#create-sumologic-test-user)**  – egy megfelelője a Britta Simon SumoLogic, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés SumoLogic, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **SumoLogic** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SumoLogic tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<tenantname>.SumoLogic.com`

   b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím:

    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [SumoLogic ügyfél-támogatási csapatának](https://www.sumologic.com/contact-us/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **SumoLogic beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-sumologic-single-sign-on"></a>SumoLogic egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a SumoLogic vállalati hely rendszergazdaként.

1. Lépjen a **kezelése \> biztonsági**.

    ![Kezelése](./media/sumologic-tutorial/ic778556.png "kezelése")

1. Kattintson a **SAML**.

    ![A globális biztonsági beállításokat](./media/sumologic-tutorial/ic778557.png "globális biztonsági beállítások")

1. Az a **válassza ki a konfigurációt, vagy hozzon létre egy új** listájáról válassza ki a **Azure ad-ben**, és kattintson a **konfigurálása**.

    ![Konfigurálja a SAML 2.0-s](./media/sumologic-tutorial/ic778558.png "SAML 2.0 konfigurálása")

1. Az a **konfigurálása a SAML 2.0-s** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Konfigurálja a SAML 2.0-s](./media/sumologic-tutorial/ic778559.png "SAML 2.0 konfigurálása")

    a. Az a **konfiguráció neve** szövegmezőbe írja be **Azure ad-ben**.

    b. Válassza ki **hibakeresési mód**.

    c. Az a **kibocsátó** szövegmező, illessze be az értéket a **az Azure AD-azonosító**, az Azure Portalról másolt.

    d. Az a **Authn kérelem URL-Címének** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    e. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be a teljes tanúsítványt **X.509-tanúsítvány** szövegmezőbe.

    f. Mint **E-mail attribútum**, jelölje be **használható SAML-tulajdonos**.  

    g. Válassza ki **SP által kezdeményezett bejelentkezési konfigurációs**.

    h. Az a **bejelentkezési elérési** szövegmezőbe írja be **Azure** kattintson **mentése**.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SumoLogic Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SumoLogic**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **SumoLogic**.

    ![Az alkalmazások listáját a SumoLogic hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sumologic-test-user"></a>SumoLogic tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SumoLogic bejelentkezni, akkor ki kell építenie SumoLogic. SumoLogic, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **SumoLogic** bérlő.

1. Lépjen a **kezelése \> felhasználók**.

    ![Felhasználók](./media/sumologic-tutorial/ic778561.png "felhasználók")

1. Kattintson a **Hozzáadás** parancsra.

    ![Felhasználók](./media/sumologic-tutorial/ic778562.png "felhasználók")

1. Az a **új felhasználó** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/sumologic-tutorial/ic778563.png "új felhasználó") 

    a. Írja be a kiépítendő be az Azure AD-fiók kapcsolódó információit a **Utónév**, **Vezetéknév**, és **E-mail** szövegmezőből.
  
    b. Válasszon ki egy szerepkört.
  
    c. Mint **állapot**válassza **aktív**.
  
    d. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Bármely más SumoLogic felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését SumoLogic által biztosított API-k.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SumoLogic csempére kattint, meg kell lehet automatikusan bejelentkezett a SumoLogic, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

