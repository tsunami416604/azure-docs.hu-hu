---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a workdayjel képes |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Workday között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 8451fd692409933803f5f8023f1e1161c3a97daf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278530"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Oktatóanyag: A workdayjel képes az Azure Active Directory-integráció

Ebben az oktatóanyagban megismerheti, hogyan integrálható a Workday az Azure Active Directory (Azure AD).
Az Azure AD integrálása a Workday nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Workday az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a Workday (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Workday, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Munkanapi egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a WORKDAY **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-workday-from-the-gallery"></a>Workday hozzáadása a katalógusból

Az Azure AD integrálása a Workday konfigurálásához hozzá kell Workday a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Workdayből a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Workday**válassza **Workday** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában munkanap](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a workdayjel képes nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó WORKDAY hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a workdayjel képes tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Workday egyszeri bejelentkezés konfigurálása](#configure-workday-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Workday tesztfelhasználót](#create-workday-test-user)**  – szeretné, hogy egy Britta Simon megfelelője a WORKDAY, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a workdayjel képes, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Workday** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Munkanapi tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https:\//impl.workday.com/<tenant>/login-saml2.flex`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.workday.com`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést:

    ![Munkanapi tartomány és URL-címek egyszeri bejelentkezési adatait](./media/workday-tutorial/reply.png)

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:  `https:\//impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Ezek az értékek nem a valós. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és a válasz URL-cím. A válasz URL-cím rendelkeznie kell egy altartomány például: www, wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Vagy hasonló `http://www.myworkday.com` működik, de `http://myworkday.com` nem létezik. Kapcsolattartó [Workday ügyfél-támogatási csapatának](https://www.workday.com/en-us/partners-services/services/support.html) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. A Workday-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg, hol **nameidentifier** le van képezve a **user.userprincipalname**. Munkanapi alkalmazás vár **nameidentifier** a leképezendő **user.mail**, **UPN** stb., így a attribútumleképezés szerkesztéséhez kattintson a szükséges **szerkesztése**  ikonra, és módosítsa a attribútumleképezés.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Itt azt leképezett az egyszerű felhasználónév (user.userprincipalname) a Névazonosító alapértelmezettként. Meg kell feleltetni a Névazonosító a tényleges felhasználói Azonosítót a Workday-fiókban (az e-mailben UPN stb.), egyszeri bejelentkezés sikeres működéséhez.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. Az a **állítsa be a Workday** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-workday-single-sign-on"></a>Munkanapi egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Workday vállalati hely rendszergazdaként.

2. Az a **keresőmezőbe** nevű keresési **szerkesztése Bérlőbeállítást – biztonsági** bal oldalt a kezdőlap tetején található.

    ![Bérlői biztonsági szerkesztése](./media/workday-tutorial/IC782925.png "bérlői biztonsági szerkesztése")

3. Az a **átirányítási URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Redirection URLs](./media/workday-tutorial/IC7829581.png "Redirection URLs")

    a. Kattintson a **sort ad hozzá a**.

    b. Az a **bejelentkezés átirányítási URL-cím** szövegmezőbe, és a **Mobile átirányítási URL-cím** szövegmezőbe írja be a **bejelentkezési URL-** megadott a **alapszintű SAML-konfigurációja**  szakaszában az Azure Portalon.

    c. Az Azure Portalon az a **állítsa be a Workday** területén másolja a **kijelentkezési URL-címe**, és illessze be azt a **kijelentkezési átirányítási URL-cím** szövegmezőbe.

    d. A **környezetekben használt** szövegmezőben válassza ki a környezet nevét.  

   > [!NOTE]
   > A környezet attribútum vannak kötve, a bérlői URL-cím értékét:  
   > -Ha a tartomány neve a Workday-bérlői URL-cím kezdődik impl például: *https:\//impl.workday.com/\<bérlői\>/login-saml2.flex*), a **környezet**attribútum megvalósítási értékre kell állítani.  
   > -Ha a tartománynév valami mást kezdődik, lépjen kapcsolatba kell [Workday ügyfél-támogatási csapatának](https://www.workday.com/en-us/partners-services/services/support.html) beolvasni a megfelelő **környezet** érték.

4. Az a **SAML-telepítő** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAML-telepítő](./media/workday-tutorial/IC782926.png "SAML-telepítő")

    a.  Válassza ki **SAML-hitelesítés engedélyezése**.

    b.  Kattintson a **sort ad hozzá a**.

5. Az a **SAML Identitásszolgáltatók** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAML-Identitásszolgáltatók](./media/workday-tutorial/IC7829271.png "SAML Identitásszolgáltatók")

    a. Az a **identitásszolgáltató neve** szövegmezőbe írja be a szolgáltató nevét (például: *SPInitiatedSSO*).

    b. Az Azure Portalon az a **állítsa be a Workday** területén másolja a **az Azure AD-azonosító** értéket, és illessze be azt a **kibocsátó** szövegmezőbe.

    ![SAML-Identitásszolgáltatók](./media/workday-tutorial/IC7829272.png "SAML Identitásszolgáltatók")

    c. Az Azure Portalon az a **állítsa be a Workday** területén másolja a **kijelentkezési URL-címe** értéket, és illessze be azt a **kijelentkezési válasz URL-címe** szövegmezőbe.

    d. Az Azure Portalon az a **állítsa be a Workday** területén másolja a **bejelentkezési URL-cím** értéket, és illessze be azt a **identitásszolgáltató egyszeri bejelentkezési szolgáltatás URL-címe** szövegmezőbe.

    e. A **környezetekben használt** szövegmezőben válassza ki a környezet nevét.

    f. Kattintson a **szolgáltató nyilvános kulcs Identitástanúsítványt**, és kattintson a **létrehozás**.

    ![Hozzon létre](./media/workday-tutorial/IC782928.png "létrehozása")

    g. Kattintson a **x509 létrehozása nyilvános kulcs**.

    ![Hozzon létre](./media/workday-tutorial/IC782929.png "létrehozása")

6. Az a **nézet x509 nyilvános kulcs** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Nyilvános kulcs nézet x509](./media/workday-tutorial/IC782930.png "nézet x509 nyilvános kulcs")

    a. Az a **neve** szövegmezőbe írja be a tanúsítvány nevét (például: *PPE\_SP*).

    b. Az a **érvényes a** szövegmezőbe írja be az attribútum értékét a tanúsítvány érvényes.

    c.  Az a **érvényes való** szövegmezőbe írja be a tanúsítvány attribútum értéke érvénytelen.

    > [!NOTE]
    > Kérheti az érvényes dátumot és az érvényes, ha duplán kattint a letöltött tanúsítvány az a dátum a.  A dátumok vannak felsorolva a **részletek** fülre.
    >
    >

    d.  Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, és másolja a tartalmát.

    e.  Az a **tanúsítvány** szövegmezőjébe illessze be a vágólap tartalmát.

    f.  Kattintson az **OK** gombra.

7. Hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurációja](./media/workday-tutorial/WorkdaySSOConfiguratio.png "egyszeri bejelentkezés konfigurálása")

    a.  Az a **szolgáltatás Szolgáltatóazonosító** szövegmezőbe írja be **https://www.workday.com**.

    b. Válassza ki **nem Deflate hitelesítési Szolgáltató által kezdeményezett kérelem**.

    c. Mint **hitelesítési kérelmek aláírását módszer**válassza **SHA256**.

    ![Hitelesítési kérelem-aláírás módszer](./media/workday-tutorial/WorkdaySSOConfiguration.png "hitelesítési kérelmek aláírását módja") 

    d. Kattintson az **OK** gombra.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Ellenőrizze, hogy megfelelően állítsa be egyszeri bejelentkezést. Abban az esetben, ha egyszeri bejelentkezést helytelen telepítés engedélyezi, nem lehet kizárva, és adja meg az alkalmazás a hitelesítő adataival. Ebben a helyzetben a Workday biztosít egy biztonsági mentési bejelentkezési URL-cím, a felhasználók is be a normál felhasználónevükkel és jelszavukkal használatával a következő formátumban: [a Workday URL]/login.flex?redirect=n

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

Ebben a szakaszban engedélyezze Britta Simon workdaybe a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Workday**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Workday**.

    ![Az alkalmazások listáját a Workday hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-workday-test-user"></a>Workday tesztfelhasználó létrehozása

Ebben a szakaszban egy WORKDAY Britta Simon nevű felhasználó létrehozásához. Együttműködve [Workday ügyfél-támogatási csapatának](https://www.workday.com/en-us/partners-services/services/support.html) a felhasználók hozzáadása a Workday-platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Workday csempére kattint, meg kell lehet automatikusan bejelentkezett a Workday, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

