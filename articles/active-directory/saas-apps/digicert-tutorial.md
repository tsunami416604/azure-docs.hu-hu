---
title: 'Oktatóanyag: Azure Active Directory-integráció a DigiCert |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a DigiCert között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 646f3129-aa67-4875-9073-1d0b6a3173d9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: 428878d3b1a8a369b58b045544f034eb4235c74c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047868"
---
# <a name="tutorial-azure-active-directory-integration-with-digicert"></a>Oktatóanyag: Azure Active Directory-integráció a DigiCert

Ebben az oktatóanyagban elsajátíthatja a DigiCert integrálása az Azure Active Directory (Azure AD).

A DigiCert integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a DigiCert Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett, a DigiCert (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a DigiCert, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A DigiCert egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A DigiCert hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-digicert-from-the-gallery"></a>A DigiCert hozzáadása a katalógusból
Az Azure AD integrálása a DigiCert konfigurálásához szüksége DigiCert hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**A DigiCert hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **DigiCert**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/digicert-tutorial/tutorial_digicert_search.png)

5. Az eredmények panelen válassza ki a **DigiCert**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/digicert-tutorial/tutorial_digicert_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a DigiCert egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a DigiCert tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a DigiCert hivatkozás kapcsolatát kell létrehozni.

A DigiCert, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** hivatkozás viszony.

Az Azure AD egyszeri bejelentkezés a DigiCert tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[A DigiCert tesztfelhasználó létrehozása](#creating-a-digicert-test-user)**  – egy megfelelője a Britta Simon DigiCert, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a DigiCert alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés, a DigiCert, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **DigiCert** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/digicert-tutorial/tutorial_digicert_samlbase.png)

3. Az a **DigiCert tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/digicert-tutorial/tutorial_digicert_url.png)
    
    Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://www.digicert.com/sso`

4. A DigiCert alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. A következő képernyőképen látható egy példa az ehhez a konfigurációhoz. 

    ![Egyszeri bejelentkezés konfigurálása](./media/digicert-tutorial/tutorial_digicert_attributes.png)
    
5. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | Vállalati | < companycode > |
    | digicertrole | CanAccessCertCentral |

    > [!Note]
    > Az érték **vállalati** attribútum értéke nem valódi. Tényleges vállalat kódja frissítse ezt az értéket. A gépkulcsengedélyek értékének **vállalati** forduljon attribútum [DigiCert támogatási csapatának](mailto:support@digicert.com).

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/digicert-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/digicert-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra. 

6. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/digicert-tutorial/tutorial_digicert_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/digicert-tutorial/tutorial_general_400.png)

8. Az egyszeri bejelentkezés konfigurálása **DigiCert** oldalon kell küldenie a letöltött **metaadatainak XML** való [DigiCert támogatási csapatának](mailto:support@digicert.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/digicert-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/digicert-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/digicert-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/digicert-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-digicert-test-user"></a>A DigiCert tesztfelhasználó létrehozása

Ebben a szakaszban egy a DigiCert Britta Simon nevű felhasználó hoz létre. Együttműködve [DigiCert támogatási csapatának](mailto:support@digicert.com) felhasználót is hozzáadhat a DigiCert.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés DigiCert Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel a DigiCert, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **DigiCert**.

    ![Egyszeri bejelentkezés konfigurálása](./media/digicert-tutorial/tutorial_digicert_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a DigiCert csempére kattint, meg kell lekérése automatikusan bejelentkezett az DeigiCert alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/digicert-tutorial/tutorial_general_01.png
[2]: ./media/digicert-tutorial/tutorial_general_02.png
[3]: ./media/digicert-tutorial/tutorial_general_03.png
[4]: ./media/digicert-tutorial/tutorial_general_04.png

[100]: ./media/digicert-tutorial/tutorial_general_100.png

[200]: ./media/digicert-tutorial/tutorial_general_200.png
[201]: ./media/digicert-tutorial/tutorial_general_201.png
[202]: ./media/digicert-tutorial/tutorial_general_202.png
[203]: ./media/digicert-tutorial/tutorial_general_203.png

