---
title: 'Oktatóanyag: Azure Active Directory-integráció az antik további |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés és antik további Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0b8ca505-61ea-487c-9a3e-fa50c936df0c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: jeedes
ms.openlocfilehash: 9b7e7a84059f8393e8f900733602e32ca3ad833b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423657"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn"></a>Oktatóanyag: Azure Active Directory-integráció Antik ismerje meg az

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Antik ismerje meg az Azure Active Directory (Azure AD).

Antik további integrálása az Azure ad-vel nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Antik további Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Antik további (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálni az Azure AD-integrációs Antik ismerje meg, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Antik ismerje meg az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Ismerje meg, antik hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-blackboard-learn-from-the-gallery"></a>Ismerje meg, antik hozzáadása a katalógusból
Konfigurálása az Azure AD integrálása a Antik ismerje meg, hozzá kell Antik ismerje meg a galériából a felügyelt SaaS-alkalmazások listájára.

**Ismerje meg, antik hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Antik további**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_search.png)

1. Az eredmények panelen válassza ki a **Antik további**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az antik ismerje meg, hogy a teszt "Britta Simon." nevű felhasználó

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Antik megtudhatja, mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Antik ismerje meg a hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** Antik ismerje meg a.

Az Azure AD egyszeri bejelentkezés az antik ismerje meg, tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Ismerje meg, antik tesztfelhasználó létrehozása](#creating-a-blackboard-learn-test-user)**  – egy megfelelője a Britta Simon Antik megtudhatja, hogy a felhasználó Azure ad-ben reprezentációja van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, majd Antik ismerje meg, alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Antik ismerje meg, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon a a **Antik további** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_samlbase.png)

1. Az a **Antik ismerje meg, tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.blackboard.com/`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.blackboard.com/auth-saml/saml/SSO/entity-id/SAML_AD`
    
    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Antik további ügyfél-támogatási csapatának](https://www.blackboard.com/support/index.aspx) beolvasni ezeket az értékeket. 

1. Antik további alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján.
 A következő képernyőképen látható példát talál azt.

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-tutorial/tutorial_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútumai a képen látható módon, és hajtsa végre az alábbi lépéseket. Itt egyedi felhasználói attribútumként azt van leképezve a Userprincipalname, de a megfelelő értéket, amely egyértelműen azonosítja a felhasználó a szervezetben, és a felhasználónév mezője Antik ismerje meg, amely leképezi a térkép.
           
    | Attribútum neve | Attribútum értéke |   
    | ---------------| ----------------|
    | urn:oid:1.3.6.1.4.1.5923.1.1.1.6 |User.userPrincipalName |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-tutorial/tutorial_general_400.png)

1. Az a **Antik további konfigurációs** területén kattintson **Antik további konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Antik ismerje meg,** oldalon kell küldenie a letöltött **metaadatainak XML** és **SAML Entitásazonosító** való [Antik ismerje meg, támogatja a](https://www.blackboard.com/support/index.aspx).

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** Britta Simon.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-blackboard-learn-test-user"></a>Ismerje meg, antik tesztfelhasználó létrehozása
Ebben a szakaszban egy Britta Simon nevű Antik ismerje meg a felhasználó hoz létre. 

Antik ismerje meg az alkalmazás támogatja a csak az idő felhasználókiépítés. Győződjön meg arról, hogy konfigurálta a jogcímeket a szakaszban leírt módon  **[konfigurálása az Azure AD egyszeri bejelentkezés](#configuring-azure-ad-single-sign-on)**
### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Antik további Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Antik ismerje meg, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Antik további**.

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Antik további csempére kattint, akkor kell lekérése automatikusan bejelentkezett Antik ismerje meg, alkalmazását az. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/blackboard-learn-tutorial/tutorial_general_04.png

[100]: ./media/blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/blackboard-learn-tutorial/tutorial_general_201.png
[202]: ./media/blackboard-learn-tutorial/tutorial_general_202.png
[203]: ./media/blackboard-learn-tutorial/tutorial_general_203.png

