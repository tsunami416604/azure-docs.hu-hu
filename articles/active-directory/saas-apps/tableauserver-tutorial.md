---
title: 'Oktatóanyag: Azure Active Directory-integráció Tableau Server segítségével |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Tableau Server között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 4c88d6a8b22e4c5c19112c30e15bc960bc8ed1b5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045223"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Oktatóanyag: Azure Active Directory-integráció Tableau Server segítségével

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Tableau Server az Azure Active Directory (Azure AD).

A Tableau Server integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Tableau Server az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Tableau Server (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Tableau Server segítségével, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Tableau Server egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Tableau Server hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-tableau-server-from-the-gallery"></a>A Tableau Server hozzáadása a katalógusból
Az Azure AD integrálása a Tableau Server konfigurálásához hozzá kell Tableau Server a galériából a felügyelt SaaS-alkalmazások listájára.

**A Tableau Server hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Tableau Server**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauserver-tutorial/tutorial_tableauserver_search.png)

5. Az eredmények panelen válassza ki a **Tableau Server**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a teszt "Britta Simon." nevű felhasználó Tableau Server segítségével

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a Tableau Server a partner felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Tableau Server hivatkozás kapcsolata kell létrehozni.

A Tableau Server, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Tableau Server segítségével tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[A Tableau Server tesztfelhasználó létrehozása](#creating-a-tableau-server-test-user)**  - a-megfelelője a Britta Simon rendelkezik a Tableau Server, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Tableau Server alkalmazás egyszeri bejelentkezés konfigurálása.

**A Tableau Server konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Tableau Server** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. Az a **Tableau Server tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://azure.<domain name>.link`
    
    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://azure.<domain name>.link`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > Az előző értékek nem valódi értékek. Az értékeket később, a tényleges URL-cím és a Tableau Server konfigurációs lapon azonosító frissítése. 

4. A tableau Server alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **"Felhasználói attribútumok"** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa látható a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/3.png)
    
5. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | felhasználónév | *user.mailnickname* |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_officespace_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson a **Ok**


6. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Egyszeri bejelentkezés az alkalmazáshoz konfigurált lekéréséhez kell bejelentkezést a Tableau Server bérlői rendszergazdaként az.
   
   a. A Tableau Server a konfigurációban, kattintson a **SAML** fülre.
  
    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. A jelölőnégyzet bejelölésével **használható SAML egyszeri bejelentkezésre**.
   
   c. A tableau Server visszatérési URL-cím – a Tableau Server felhasználók érik el, mint például az URL-cím http://tableau_server. Használatával http://localhost nem ajánlott. Egy URL-cím használatával záró perjellel (például http://tableau_server/) nem támogatott. Másolás **Tableau Server visszatérési URL-cím** , és illessze be az Azure AD **bejelentkezési URL-** szövegmezőjébe **Tableau Server tartomány és URL-címek** szakaszban.
   
   d. SAML-entitás azonosítója – az entitás azonosítója egyedileg azonosítja az identitásszolgáltató a Tableau Server telepítése. Adhatja meg a Tableau Server URL-CÍMÉT újra itt, ha szeretné, de nem rendelkezik a Tableau Server URL-címét. Másolás **SAML Entitásazonosító** , és illessze be az Azure AD **azonosító** szövegmezőjébe **Tableau Server tartomány és URL-címek** szakaszban.
     
   e. Kattintson a **metaadatait tartalmazó fájl exportálása** és nyissa meg a szöveges szerkesztő alkalmazásban. Keresse meg a helyességi feltétel fogyasztói szolgáltatás URL-CÍMÉT a Http Post és Index 0, és másolja az URL-címet. Most illessze be az Azure AD **válasz URL-cím** szövegmezőjébe **Tableau Server tartomány és URL-címek** szakaszban.
   
   f. Keresse meg az összevonási metaadatait tartalmazó fájl, az Azure-portálról letöltött, és ezután töltse fel a a **identitásszolgáltató SAML-metaadatfájl**.
   
   g. Kattintson a **OK** gomb a Tableau Server konfigurációs lapja.
   
    >[!NOTE] 
    >Töltse fel a Tableau Server SAML SSO-konfiguráció bármely olyan tanúsítványt kell ügyfél, és figyelmen beolvasni az egyszeri bejelentkezési folyamatban.
    >Ha szüksége a SAML konfigurálása a Tableau Server segítségével, akkor tekintse meg a cikk [SAML konfigurálása](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauserver-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauserver-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauserver-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauserver-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-tableau-server-test-user"></a>A Tableau Server tesztfelhasználó létrehozása

Ez a szakasz célja, a Tableau Server Britta Simon nevű felhasználó létrehozásához. A Tableau server az összes felhasználó kiépítése kell. 

A felhasználó felhasználóneve meg kell egyeznie az érték, amely az Azure ad-ben az egyéni attribútum be vannak állítva **felhasználónév**. Megfelelő hozzárendelési működnie kell az integrációs [konfigurálása az Azure AD egyszeri bejelentkezés](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Hozzon létre egy felhasználót manuálisan kell, ha szüksége a Tableau Server rendszergazdától a szervezetben.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon ad hozzáférést, a Tableau Server által használandó Azure egyszeri bejelentkezést.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel a Tableau Server, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Tableau Server**.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Tableau Server csempére kattint, meg kell lekérése automatikusan bejelentkezett a Tableau Server alkalmazást.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/tableauserver-tutorial/tutorial_general_203.png

