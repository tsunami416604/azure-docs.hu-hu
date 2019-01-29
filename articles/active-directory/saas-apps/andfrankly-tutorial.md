---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az & frankly |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory közötti és & frankly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1d702060-1b89-4e9d-9f01-ede4f1171c73
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 8b95459e00296950924c7837890e2313bbcb183d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173105"
---
# <a name="tutorial-azure-active-directory-integration-with-frankly"></a>Oktatóanyag: Az Azure Active Directory-integráció az & frankly

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a & frankly az Azure Active Directoryval (Azure AD).

Integrálás & frankly az Azure ad-vel biztosít a következő előnyökkel jár:

- Szabályozhatja, aki hozzáféréssel rendelkezik a & frankly Azure AD-ben
- Engedélyezheti a felhasználók automatikusan el bejelentkezett, a & frankly (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció & frankly, akkor kell a következő elemek:

- Azure AD-előfizetés
- A & frankly engedélyezett előfizetés egyszeri bejelentkezés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás & frankly a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-frankly-from-the-gallery"></a>Hozzáadás & frankly a katalógusból
Konfigurálja az integrációt a & frankly az Azure AD-be, akkor hozzá kell adnia & frankly a felügyelt SaaS-alkalmazások listájára a katalógusból.

**Adjon hozzá & frankly katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **& frankly**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/andfrankly-tutorial/tutorial_andfrankly_search.png)

5. Az eredmények panelen válassza ki a **& frankly**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/andfrankly-tutorial/tutorial_andfrankly_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés az & frankly egy "Britta Simon." nevű tesztelési felhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD milyen megfelelőjére lévő felhasználó tudnia kell, & frankly, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó hivatkozás kapcsolata a & frankly kell hozható létre.

A & frankly, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az & frankly, meg kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Létrehozása egy & frankly tesztfelhasználó](#creating-a-frankly-test-user)**  szeretné, hogy egy megfelelője a Britta Simon – a felhasználó Azure ad-ben ábrázolása a & frankly legyen társított.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés az Azure Portalon engedélyezése és konfigurálása egyszeri bejelentkezéshez az a & frankly alkalmazáshoz.

**Konfigurálhatja az Azure AD egyszeri bejelentkezés az & frankly, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **& frankly** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/andfrankly-tutorial/tutorial_andfrankly_samlbase.png)

3. Az a **& frankly tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/andfrankly-tutorial/tutorial_andfrankly_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/metadata.php/<tenant id>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/saml2-acs.php/<tenant id>`

4. Ellenőrizze **speciális URL-beállítások megjelenítése**. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/andfrankly-tutorial/tutorial_andfrankly_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://andfrankly.com/saml/okta/?saml_sso=<tenant id>`
    > [!NOTE] 
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóval, bejelentkezést és a válasz URL-cím. Kapcsolattartó [andfrankly támogatási csapatának](mailto:help@andfrankly.com) beolvasni ezeket az értékeket.

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/andfrankly-tutorial/tutorial_andfrankly_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/andfrankly-tutorial/tutorial_general_400.png)

7. Az egyszeri bejelentkezés konfigurálása **& frankly** oldalon kell küldenie a letöltött **metaadatainak XML** való [andfrankly támogatási csapatának](mailto:help@andfrankly.com). 

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/andfrankly-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/andfrankly-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/andfrankly-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/andfrankly-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-frankly-test-user"></a>Létrehozás egy & frankly tesztfelhasználó számára

Ebben a szakaszban egy Britta Simon nevű frankly és a felhasználó hoz létre. Együttműködve [andfrankly támogatási csapatának](mailto:help@andfrankly.com) tartozó felhasználók hozzáadása a & frankly platform.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a & frankly Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**A & frankly Britta Simon hozzárendeléséhez végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **& frankly**.

    ![Egyszeri bejelentkezés konfigurálása](./media/andfrankly-tutorial/tutorial_andfrankly_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Kattintás után a & frankly csempére a hozzáférési panelen, kapja meg automatikusan bejelentkezett, a & frankly alkalmazáshoz

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andfrankly-tutorial/tutorial_general_01.png
[2]: ./media/andfrankly-tutorial/tutorial_general_02.png
[3]: ./media/andfrankly-tutorial/tutorial_general_03.png
[4]: ./media/andfrankly-tutorial/tutorial_general_04.png

[100]: ./media/andfrankly-tutorial/tutorial_general_100.png

[200]: ./media/andfrankly-tutorial/tutorial_general_200.png
[201]: ./media/andfrankly-tutorial/tutorial_general_201.png
[202]: ./media/andfrankly-tutorial/tutorial_general_202.png
[203]: ./media/andfrankly-tutorial/tutorial_general_203.png

