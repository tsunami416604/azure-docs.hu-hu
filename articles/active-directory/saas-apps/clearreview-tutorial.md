---
title: 'Oktatóanyag: Egyértelmű tekintse át az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés egyértelmű felülvizsgálati és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: ba4d6af15145bb043629a81e4bf21f53658875a3
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692188"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Oktatóanyag: Egyértelmű tekintse át az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan egyértelmű felülvizsgálati integrálása az Azure Active Directory (Azure AD).
Egyértelmű felülvizsgálati integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá egyértelmű tekintse át az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve (egyszeri bejelentkezés) törölje tekintse át az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Egyértelmű tekintse át az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Egyértelmű tekintse át az egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Törölje a felülvizsgálati támogatja **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-clear-review-from-the-gallery"></a>Egyértelmű felülvizsgálat hozzáadása a katalógusból

Egyértelmű tekintse át az Azure ad-ben integráció konfigurálásához, hozzá kell egyértelmű tekintse át a galériából a felügyelt SaaS-alkalmazások listájára.

**Egyértelmű felülvizsgálat hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **egyértelmű felülvizsgálati**, jelölje be **egyértelmű tekintse át** eredmény panelen kattintson a **hozzáadása** gombra kattintva vegye fel az alkalmazást.

     ![Felülvizsgálat törlése az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés nevű tesztfelhasználó alapuló egyértelmű tekintse át a **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó törlése tekintse át a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés egyértelmű tekintse át a tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Egyértelmű felülvizsgálati egyszeri bejelentkezés konfigurálása](#configure-clear-review-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre egyértelmű felülvizsgálati tesztfelhasználót](#create-clear-review-test-user)**  – egy megfelelője a Britta Simon egyértelmű tekintse át, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Egyértelmű tekintse át az Azure AD egyszeri bejelentkezés konfigurálása, hajtsa végre az alábbi lépéseket:

1. A a [az Azure portal](https://portal.azure.com/), a a **egyértelmű felülvizsgálati** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Világos felülvizsgálati tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<customer name>.clearreview.com/sso/metadata/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<customer name>.clearreview.com/sso/acs/`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Világos felülvizsgálati tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<customer name>.clearreview.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [egyértelmű felülvizsgálati ügyfél-támogatási csapatának](https://clearreview.com/contact/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Egyértelmű felülvizsgálat alkalmazása a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen, a jogcímek szerkesztése használatával **Szerkesztés ikon** , vagy adja hozzá a jogcímek használatával **hozzáadása új jogcímet**SAML-jogkivonat attribútum beállítása, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Name (Név) | Adatforrás-attribútum | 
    | ---------------| --------------- |
    | Azonosító érték neve   | user.mail |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

9. Az a **egyértelmű felülvizsgálati beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-clear-review-single-sign-on"></a>Felülvizsgálat törlése egyszeri bejelentkezés konfigurálása

1. Az egyszeri bejelentkezés konfigurálása **egyértelmű felülvizsgálati** oldalán, nyissa meg a **egyértelmű tekintse át** portál, rendszergazdai hitelesítő adataival.

2. Válassza ki **rendszergazdai** a bal oldali navigációs sávon.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. Válassza ki **módosítása** az oldal alján.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. Hajtsa végre a következő lépéseket **egyszeri bejelentkezési beállításainak** lap

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. Az a **kiállítójának URL-címe** szövegmező, illessze be az értéket a **Azure Ad-azonosító** Azure Portalról másolt.

    b. Az a **SAML-végpont** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.  

    c. A a **SLO-végpont** szövegmezőjébe illessze be az értéket, **kijelentkezési URL-címe** Azure Portalról másolt.  

    d. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben, és illessze be a tartalmat a **X.509-tanúsítvány** szövegmezőbe.   

5. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával egyértelmű tekintse át az Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd válassza **egyértelmű felülvizsgálati**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **egyértelmű felülvizsgálati**.

    ![A tiszta felülvizsgálati hivatkozással alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-clear-review-test-user"></a>Egyértelmű felülvizsgálati tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű egyértelmű tekintse át a felhasználó hoz létre. Együttműködve [egyértelmű támogatási csapatától](https://clearreview.com/contact/) a felhasználók hozzáadása az egyértelmű felülvizsgálati platformon.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a tiszta áttekintése csempe a hozzáférési panelen, meg kell automatikusan megtörténik a, amelynek beállítása egyszeri bejelentkezés az egyértelmű ellenőrzéshez. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

