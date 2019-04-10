---
title: 'Oktatóanyag: A Tableau Server az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Tableau Server között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 2ae0aefe194ca8bca6ea62420314b4fbdb1e0187
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357920"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Oktatóanyag: A Tableau Server az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Tableau Server az Azure Active Directory (Azure AD).
A Tableau Server integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Tableau Server az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a Tableau Server (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Tableau Server segítségével, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* A tableau Server egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* A tableau Server támogatja a **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-tableau-server-from-the-gallery"></a>A Tableau Server hozzáadása a katalógusból

Az Azure AD integrálása a Tableau Server konfigurálásához hozzá kell Tableau Server a galériából a felügyelt SaaS-alkalmazások listájára.

**A Tableau Server hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Tableau Server**válassza **Tableau Server** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A tableau Server a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó Tableau Server segítségével **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a Tableau Server közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés Tableau Server segítségével tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A Tableau Server egyszeri bejelentkezés konfigurálása](#configure-tableau-server-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[A Tableau Server tesztfelhasználó létrehozása](#create-tableau-server-test-user)**  - a-megfelelője a Britta Simon rendelkezik a Tableau Server, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

A Tableau Server konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Tableau Server** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A tableau Server tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://azure.<domain name>.link`

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím: `https://azure.<domain name>.link`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Az előző értékek nem valódi értékek. Frissítse az értékeket a tényleges URL-cím és a Tableau Server konfigurációs lapon, az oktatóanyag későbbi részében ismertetett azonosítója.

5. A tableau Server alkalmazás vár egy egyéni jogcím **felhasználónév** amely kell definiálni látható. Ez helyett egyedi felhasználói azonosító jogcím felhasználói azonosítóként van használatban. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok & jogcímek** szakasz alkalmazás integráció lapján. Kattintson a **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok & jogcímek** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok & jogcímek** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) | Adatforrás-attribútum | Névtér |
    | ---------------| --------------- | ----------- |
    | felhasználónév | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Adja meg a **Namespace** értéket.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

8. Az a **állítsa be a Tableau Server** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-tableau-server-single-sign-on"></a>A Tableau Server egyszeri bejelentkezés konfigurálása

1. Egyszeri bejelentkezés az alkalmazáshoz konfigurált kapni, jelentkezzen be rendszergazdaként a Tableau Server bérlő kell.

2. Az a **konfigurációs** lapon jelölje be **felhasználói identitás és hozzáférés**, majd válassza ki a **hitelesítési** mód lapon.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Az a **konfigurációs** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. A **hitelesítési módszer**, válassza ki a SAML.

    b. A jelölőnégyzet bejelölésével **SAML-hitelesítés engedélyezése a kiszolgáló**.

    c. A tableau Server visszatérési URL-cím – a Tableau Server felhasználók érik el, mint például az URL-cím <http://tableau_server>. Használatával `http://localhost` nem ajánlott. Egy URL-cím használatával záró perjellel (például `http://tableau_server/`) nem támogatott. Másolás **Tableau Server visszatérési URL-cím** , és illessze be a való **bejelentkezési URL-** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon

    d. SAML-entitás azonosítója – az entitás azonosítója egyedileg azonosítja az identitásszolgáltató a Tableau Server telepítése. Adhatja meg a Tableau Server URL-CÍMÉT újra itt, ha szeretné, de nem rendelkezik a Tableau Server URL-címét. Másolás **SAML Entitásazonosító** , és illessze be a való **azonosító** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon

    e. Kattintson a **XML metaadatait tartalmazó fájl letöltése** és nyissa meg a szöveges szerkesztő alkalmazásban. Keresse meg a helyességi feltétel fogyasztói szolgáltatás URL-CÍMÉT a Http Post és Index 0, és másolja az URL-címet. Illessze be azt a **válasz URL-cím** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon

    f. Keresse meg az összevonási metaadatait tartalmazó fájl, az Azure-portálról letöltött, és ezután töltse fel a a **identitásszolgáltató SAML-metaadatfájl**.

    g. Írja be a nevét, az attribútumokat, amelyek az identitásszolgáltató használja, tartsa lenyomva a felhasználónevek, megjelenítendő nevek és e-mail-címek számára.

    h. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Töltse fel a Tableau Server SAML SSO-konfiguráció bármely olyan tanúsítványt kell ügyfél, és figyelmen beolvasni az egyszeri bejelentkezési folyamatban. Ha szüksége a SAML konfigurálása a Tableau Server segítségével, akkor tekintse meg a cikk [SAML konfigurálása](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon ad hozzáférést, a Tableau Server által használandó Azure egyszeri bejelentkezést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Tableau Server**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Tableau Server**.

    ![A Tableau Server hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-tableau-server-test-user"></a>A Tableau Server tesztfelhasználó létrehozása

Ez a szakasz célja, a Tableau Server Britta Simon nevű felhasználó létrehozásához. A Tableau server az összes felhasználó kiépítése kell.

A felhasználó felhasználóneve meg kell egyeznie az érték, amely az Azure ad-ben az egyéni attribútum be vannak állítva **felhasználónév**. Az integráció megfelelő hozzárendelési működik az Azure AD konfigurálása egyszeri bejelentkezéshez.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha szüksége a Tableau Server rendszergazdától a szervezetben.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Tableau Server csempére kattint, akkor kell automatikusan megtörténik a a Tableau-kiszolgálóra, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

