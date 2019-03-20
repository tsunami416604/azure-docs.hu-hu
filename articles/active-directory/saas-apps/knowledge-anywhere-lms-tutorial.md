---
title: 'Oktatóanyag: Tudásbázis bárhol segítségével az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az ismeretek bárhol LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.openlocfilehash: f39952c74006964155fd23920c85506cac13a878
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892717"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledge-anywhere-lms"></a>Oktatóanyag: Tudásbázis bárhol segítségével az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Tudásbázis bárhol LMS integrálása az Azure Active Directory (Azure AD).
Tudásbázis bárhol LMS integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Tudásbázis bárhol segítségével az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve Tudásbázis bárhol LMS (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Tudásbázis bárhol LMS, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Tudásbázis bárhol LMS egy bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Tudásbázis bárhol LMS **SP és IDP** által kezdeményezett egyszeri bejelentkezés
* Támogatja a Tudásbázis bárhol LMS **igény szerinti** felhasználók átadása

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Tudásbázis bárhol LMS hozzáadása a katalógusból

Az Azure AD integrálása a Tudásbázis bárhol LMS konfigurálásához hozzá kell Tudásbázis bárhol LMS a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Tudásbázis bárhol LMS hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Tudásbázis bárhol LMS**, jelölje be **Tudásbázis bárhol LMS** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Tudásbázis bárhol LMS a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Tudásbázis bárhol segítségével egy teszt nevű felhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó a Tudásbázis bárhol LMS hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Tudásbázis bárhol LMS tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Tudásbázis bárhol LMS egyszeri bejelentkezés konfigurálása](#configure-knowledge-anywhere-lms-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Tudásbázis bárhol LMS tesztfelhasználót](#create-knowledge-anywhere-lms-test-user)**  – egy megfelelője a Britta Simon kell a Tudásbázis bárhol segítségével, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Tudásbázis bárhol LMS, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Tudásbázis bárhol LMS** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Tudásbázis bárhol LMS tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját és a válasz URL-cím, az oktatóanyag későbbi részében ismertetett ezeket az értékeket.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Tudásbázis bárhol LMS tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > A bejelentkezési URL-érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Tudásbázis bárhol LMS ügyfél-támogatási csapatának](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) lekérni ezt az értéket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **állítsa be a Tudásbázis bárhol LMS** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-knowledge-anywhere-lms-single-sign-on"></a>Tudásbázis bárhol konfigurálása LMS egyszeri bejelentkezéshez

1. Egy másik böngésző ablakában nyissa meg a Tudásbázis bárhol LMS felügyeleti portált.

2. Válassza ki a a **hely** fülre.

    ![Tudásbázis bárhol LMS-konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure1.png)

3. Válassza ki a a **SAML-beállítások** fülre.

    ![Tudásbázis bárhol LMS-konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure2.png)

4. Kattintson a **hozzáadhat új**.

    ![Tudásbázis bárhol LMS-konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure3.png)

5. Az a **Add/Update SAML-beállítások** lapon, a következő lépésekkel:

    ![Tudásbázis bárhol LMS-konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Adja meg az Identitásszolgáltató nevét beállításával a szervezet. Az ex:- `Azure`.

    b. Az a **Identitásszolgáltató Entitásazonosító** szövegmezőjébe illessze be **Azure Ad-azonosító** Azure Portalról másolt érték.

    c. Az a **Identitásszolgáltató URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím** Azure Portalról másolt érték.

    d. Nyissa meg a letöltött tanúsítvány-fájlt az Azure Portalról a Jegyzettömbbe, másolja a tartalmat a tanúsítvány, és illessze be azt **tanúsítvány** szövegmezőbe.

    e. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be **kijelentkezési URL-címe** Azure Portalról másolt érték.

    f. Válassza ki **fő hely** lehetőséget a legördülő menüből a **tartomány**.

    g. Másolás a **SP Entitásazonosító** értékét, és illessze be azt **azonosító** szöveget szövegmezőbe a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    h. Másolás a **SP Response(ACS) URL-cím** értékét, és illessze be azt **válasz URL-cím** szöveget szövegmezőbe a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    i. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a Tudásbázis bárhol LMS Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Tudásbázis bárhol LMS**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Tudásbázis bárhol LMS**.

    ![Az alkalmazások listáját a Tudásbázis bárhol LMS hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Tudásbázis bárhol LMS tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó a Tudásbázis bárhol LMS jön létre. Tudásbázis bárhol LMS támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Tudásbázis bárhol segítségével, a hitelesítés után egy új jön létre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Tudásbázis bárhol LMS csempére kattint, meg kell lehet automatikusan bejelentkezett a Tudásbázis bárhol segítségével, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)