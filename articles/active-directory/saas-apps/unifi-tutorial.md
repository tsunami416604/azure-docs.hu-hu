---
title: 'Oktatóanyag: Azure Active Directory-integráció az UNIFI |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és UNIFI között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e1f49ee4-d2d4-4a82-9baf-0587ca1f20f6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: a93e4863a8466ad6599b11e6fe6e53d8d4d971a4
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049915"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Oktatóanyag: Azure Active Directory-integráció az UNIFI

Ebben az oktatóanyagban elsajátíthatja, hogyan UNIFI integrálása az Azure Active Directory (Azure AD).

UNIFI integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá UNIFI Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett UNIFI (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

UNIFI az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy UNIFI egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. UNIFI hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-unifi-from-the-gallery"></a>UNIFI hozzáadása a katalógusból
Az Azure AD integrálása a UNIFI konfigurálásához hozzá kell UNIFI a katalógusból a felügyelt SaaS-alkalmazások listájára.

**UNIFI hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **UNIFI**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/unifi-tutorial/tutorial_unifi_search.png)

5. Az eredmények panelen válassza ki a **UNIFI**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/unifi-tutorial/tutorial_unifi_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés UNIFI a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó UNIFI mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó UNIFI hivatkozás kapcsolata kell létrehozni.

UNIFI, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az UNIFI tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Tesztfelhasználó létrehozása egy UNIFI](#creating-a-unifi-test-user)**  – egy megfelelője a Britta Simon UNIFI, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és UNIFI alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés UNIFI, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **UNIFI** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/tutorial_unifi_samlbase.png)

3. Az a **UNIFI tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/tutorial_unifi_url1.png)

    Az a **azonosító** szövegmezőbe írja be az értéket: `INVIEWlabs` 

4. Ellenőrizze **speciális URL-beállítások megjelenítése**, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/tutorial_unifi_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://app.discoverunifi.com/login`

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/tutorial_unifi_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/tutorial_general_400.png)
    
7. Az a **UNIFI konfigurációs** területén kattintson **konfigurálása UNIFI** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/tutorial_unifi_configure.png)

8. Egy másik böngészőablakban, jelentkezzen be a **UNIFI** rendszergazdaként a vállalati webhely.

9. Kattintson a **felhasználók**.

    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/app1.png) 

10. Kattintson a **adja hozzá az új identitásszolgáltató**.

    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/app2.png)

11. Az a **identitásszolgáltató hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:   

    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/app3.png) 

    a. Az a **szolgáltatónevet** szövegmezőbe írja be az identitásszolgáltató nevét...

    b. Az a **adresa URL Poskytovatele** szövegmező illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról másolt.

    c. Nyissa meg a Jegyzettömbben, az Azure Portalról letöltött tanúsítvány eltávolítása a **---BEGIN CERTIFICATE---** és **---END CERTIFICATE---** címkét, és illessze be a maradék tartalmat a **Tanúsítvány** szövegmezőbe.

    d. Válassza ki a **alapértelmezett szolgáltató** jelölőnégyzetet.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/unifi-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/unifi-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/unifi-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/unifi-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-unifi-test-user"></a>UNIFI tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó létrehozásához. **UNIFI** támogatja a felhasználók automatikus átadása, így nem manuális lépések szükségesek. Felhasználók automatikusan jönnek létre az Azure AD-ből a sikeres hitelesítés után.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés UNIFI Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel UNIFI, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **UNIFI**.

    ![Egyszeri bejelentkezés konfigurálása](./media/unifi-tutorial/tutorial_unifi_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a UNIFI csempére kattint, meg kell lekérése automatikusan bejelentkezett az UNIFI alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/unifi-tutorial/tutorial_general_01.png
[2]: ./media/unifi-tutorial/tutorial_general_02.png
[3]: ./media/unifi-tutorial/tutorial_general_03.png
[4]: ./media/unifi-tutorial/tutorial_general_04.png

[100]: ./media/unifi-tutorial/tutorial_general_100.png

[200]: ./media/unifi-tutorial/tutorial_general_200.png
[201]: ./media/unifi-tutorial/tutorial_general_201.png
[202]: ./media/unifi-tutorial/tutorial_general_202.png
[203]: ./media/unifi-tutorial/tutorial_general_203.png

