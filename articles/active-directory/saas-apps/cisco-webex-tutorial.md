---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Cisco Webex |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Cisco Webex között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: e718d37bc1d34bccf1ea8d0ce2607ff83fc355d1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824406"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Oktatóanyag: Cisco Webex az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Cisco Webex integrálása az Azure Active Directory (Azure AD).
Cisco Webex integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Cisco Webex Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve Cisco Webex (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Cisco Webex, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Webex egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Cisco Webex támogatja **SP** által kezdeményezett egyszeri bejelentkezés

* Cisco Webex támogatja **igény szerinti** felhasználók átadása

* Cisco Webex támogatja **automatikus** felhasználók átadása

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex hozzáadása a katalógusból

Az Azure AD integrálása a Cisco Webex konfigurálásához hozzá kell Cisco Webex a galériából a felügyelt SaaS-alkalmazások listájára.

**Cisco Webex hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Cisco Webex**válassza **Cisco Webex** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Cisco Webex a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Cisco Webex nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a Cisco Webex hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés a Cisco Webex tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Cisco Webex egyszeri bejelentkezés konfigurálása](#configure-cisco-webex-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a Cisco Webex tesztfelhasználót](#create-cisco-webex-test-user)**  – egy megfelelője a Britta Simon Cisco Webex, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Cisco Webex, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Cisco Webex** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Egy másik böngészőablakban jelentkezzen be a Cisco Webex vállalati hely rendszergazdaként.

4. Kattintson a **beállítások** bal oldali menü.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. A beállítások lapon görgessen lefelé a a **hitelesítési** területén kattintson **módosítás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Válassza ki **integrálása egy 3. fél identitásszolgáltatót. (Speciális)**  , és folytassa a következő képernyőn.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. Az a **exportálása könyvtár metaadatai** kattintson **metaadatait tartalmazó fájl letöltése** a metaadat-fájl letöltéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. Az Azure Portalon az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

9. Az a **alapszintű SAML-konfigurációja** szakaszt, és töltse fel a letöltött **szolgáltató metaadatok** fájlt, és az alkalmazás konfigurálása a következő lépések végrehajtásával:

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. Szolgáltató metaadatfájl feltöltése sikeres befejezése után a **azonosító** és **válasz URL-cím** értékeket automatikusan az első **alapszintű SAML-konfigurációja** a szakaszban:

    ![Cisco Webex tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<SUBDOMAIN>.webex.com/`

    > [!Note]
    > A bejelentkezési URL-érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Cisco Webex ügyfél-támogatási csapatának](https://www.webex.co.in/support/support-overview.html) lekérni ezt az értéket.

10. Cisco Webex-alkalmazásban a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

11. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Name (Név) | Adatforrás-attribútum|
    | ---------------| --------------- | --------- |
    |   Keresztnév    | user.givenname |
    |   Vezetéknév    | user.surname |
    |   egyedi azonosítója    | user.mail |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

12. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

13. Az a **beállításához a Cisco Webex** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-cisco-webex-single-sign-on"></a>Cisco Webex egyszeri bejelentkezés konfigurálása

1. A Cisco Webex vállalati hely rendszergazda lapon a fájl böngésző lehetőséget használva keresse meg és az Azure AD-metaadatfájl feltöltésére. Ezután válassza ki **szükséges metaadatokat (biztonságosabb) a hitelesítésszolgáltató által aláírt tanúsítvány** , és folytassa a következő képernyőn. 

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

2. Válassza ki **SSO-kapcsolat tesztelése**, és a egy új böngészőlapon nyílik meg, amikor hitelesítéshez az Azure AD-bejelentkezés.

3. Lépjen vissza a **Cisco együttműködési Felhőfelügyelet** böngészőlapon. Ha a teszt sikeres volt, válassza ki a **a teszt sikeres volt. Egyszeri bejelentkezés a beállítás engedélyezése** kattintson **mentése**.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Cisco Webex Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Cisco Webex**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Cisco Webex**.

    ![Az alkalmazások listáját a Cisco Webex hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-cisco-webex-test-user"></a>Cisco Webex tesztfelhasználó létrehozása

Ez a szakasz célja a Cisco Webex Britta Simon nevű felhasználó létrehozásához. Cisco Webex támogatja a just-in-time kiépítését és a felhasználók automatikus átadása, amely alapértelmezetten engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Cisco Webex, egy új létrejön, amikor megpróbálja elérni a Cisco Webex.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Cisco Webex csempére kattint, meg kell lehet automatikusan bejelentkezett a Cisco Webex, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

