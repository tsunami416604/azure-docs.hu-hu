---
title: 'Oktatóanyag: A Tableau Server az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Tableau Server között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.openlocfilehash: a68ffb867a738e11c9895416bbdf2009b268c0c6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155811"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Oktatóanyag: A Tableau Server az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Tableau Server az Azure Active Directory (Azure AD).

A Tableau Server integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Tableau Server az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Tableau Server (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Tableau Server segítségével, a következőkre van szükség:

- Azure AD-előfizetés
- A Tableau Server egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Tableau Server hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-tableau-server-from-the-gallery"></a>A Tableau Server hozzáadása a katalógusból

Az Azure AD integrálása a Tableau Server konfigurálásához hozzá kell Tableau Server a galériából a felügyelt SaaS-alkalmazások listájára.

**A Tableau Server hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Tableau Server**válassza **Tableau Server** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A tableau Server a találatok listájában](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Tableau Server a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a Tableau Server a partner felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Tableau Server hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Tableau Server segítségével tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A Tableau Server egyszeri bejelentkezés konfigurálása](#configure-tableau-server-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[A Tableau Server tesztfelhasználó létrehozása](#create-tableau-server-test-user)**  – egy megfelelője a Britta Simon Cisco rendszerére, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
5. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Tableau Server alkalmazás egyszeri bejelentkezés konfigurálása.

**A Tableau Server konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Tableau Server** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial-general-301.png)

3. A tableau Server alkalmazás vár egy egyéni jogcím **felhasználónév** amely kell definiálni látható. Ez helyett egyedi felhasználói azonosító jogcím felhasználói azonosítóként van használatban. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok & jogcímek** szakasz alkalmazás integráció lapján. Kattintson a **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok & jogcímek** párbeszédpanel.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok & jogcímek** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke | Névtér |
    | ---------------| --------------- | ----------- |   
    | felhasználónév | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Adja meg a **Namespace** értéket.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Save** (Mentés) gombra.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

6. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://azure.<domain name>.link`
    
    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://azure.<domain name>.link`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > Az előző értékek nem valódi értékek. Frissítse az értékeket a tényleges URL-cím és a Tableau Server konfigurációs lapon, az oktatóanyag későbbi részében ismertetett azonosítója.

7. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **összevonási metaadatainak XML** , és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png)

### <a name="configure-tableau-server-single-sign-on"></a>A Tableau Server egyszeri bejelentkezés konfigurálása 

1. Egyszeri bejelentkezés az alkalmazáshoz konfigurált lekéréséhez kell bejelentkezést a Tableau Server bérlői rendszergazdaként az.

2. Az a **konfigurációs** lapon jelölje be **felhasználói identitás és hozzáférés**, majd válassza ki a **hitelesítési** mód lapon.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Az a **konfigurációs** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. A **hitelesítési módszer**, válassza ki a SAML.
    
    b. A jelölőnégyzet bejelölésével **SAML-hitelesítés engedélyezése a kiszolgáló**.

    c. A tableau Server visszatérési URL-cím – a Tableau Server felhasználók érik el, mint például az URL-cím http://tableau_server. Használatával http://localhost nem ajánlott. Egy URL-cím használatával záró perjellel (például http://tableau_server/) nem támogatott. Másolás **Tableau Server visszatérési URL-cím** , és illessze be az Azure AD **bejelentkezési URL-** szövegmezőjébe **Tableau Server tartomány és URL-címek** szakaszban.

    d. SAML-entitás azonosítója – az entitás azonosítója egyedileg azonosítja az identitásszolgáltató a Tableau Server telepítése. Adhatja meg a Tableau Server URL-CÍMÉT újra itt, ha szeretné, de nem rendelkezik a Tableau Server URL-címét. Másolás **SAML Entitásazonosító** , és illessze be az Azure AD **azonosító** szövegmezőjébe **Tableau Server tartomány és URL-címek** szakaszban.

    e. Kattintson a **XML metaadatait tartalmazó fájl letöltése** és nyissa meg a szöveges szerkesztő alkalmazásban. Keresse meg a helyességi feltétel fogyasztói szolgáltatás URL-CÍMÉT a Http Post és Index 0, és másolja az URL-címet. Most illessze be az Azure AD **válasz URL-cím** szövegmezőjébe **Tableau Server tartomány és URL-címek** szakaszban.

    f. Keresse meg az összevonási metaadatait tartalmazó fájl, az Azure-portálról letöltött, és ezután töltse fel a a **identitásszolgáltató SAML-metaadatfájl**.

    g. Írja be a nevét, az attribútumokat, amelyek az identitásszolgáltató használja, tartsa lenyomva a felhasználónevek, megjelenítendő nevek és e-mail-címek számára.

    h. Kattintson a **Mentés** gombra.

    >[!NOTE] 
    >Töltse fel a Tableau Server SAML SSO-konfiguráció bármely olyan tanúsítványt kell ügyfél, és figyelmen beolvasni az egyszeri bejelentkezési folyamatban.
    >Ha szüksége a SAML konfigurálása a Tableau Server segítségével, akkor tekintse meg a cikk [SAML konfigurálása](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create-aaduser-01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create-aaduser-02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
  
### <a name="create-tableau-server-test-user"></a>A Tableau Server tesztfelhasználó létrehozása

Ez a szakasz célja, a Tableau Server Britta Simon nevű felhasználó létrehozásához. A Tableau server az összes felhasználó kiépítése kell. 

A felhasználó felhasználóneve meg kell egyeznie az érték, amely az Azure ad-ben az egyéni attribútum be vannak állítva **felhasználónév**. Megfelelő hozzárendelési működnie kell az integrációs [konfigurálása az Azure AD egyszeri bejelentkezés](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Hozzon létre egy felhasználót manuálisan kell, ha szüksége a Tableau Server rendszergazdától a szervezetben.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon ad hozzáférést, a Tableau Server által használandó Azure egyszeri bejelentkezést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Tableau Server**.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Tableau Server csempére kattint, meg kell lekérése automatikusan bejelentkezett a Tableau Server alkalmazást.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
