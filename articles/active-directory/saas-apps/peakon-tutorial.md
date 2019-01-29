---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Peakon |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Peakon között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 637988179228fbf0a6000de74a1185af98277e3c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178953"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Peakon

Ebben az oktatóanyagban elsajátíthatja, hogyan Peakon integrálása az Azure Active Directory (Azure AD).

Peakon integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Peakon Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Peakon (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Peakon az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Peakon egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Peakon hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-peakon-from-the-gallery"></a>Peakon hozzáadása a katalógusból

Az Azure AD integrálása a Peakon konfigurálásához hozzá kell Peakon a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Peakon hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Peakon**válassza **Peakon** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Peakon](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Peakon a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Peakon mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Peakon hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Peakon tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Peakon tesztfelhasználó létrehozása](#creating-a-peakon-test-user)**  – egy megfelelője a Britta Simon Peakon, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Peakon alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Peakon, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Peakon** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **beállítás egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a következő lépésekkel, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Peakon tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://app.peakon.com/saml/<companyid>/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://app.peakon.com/saml/<companyid>/assert`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Peakon tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/peakon-tutorial/tutorial_peakon_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe: `https://app.peakon.com/login`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóval, és a válasz URL-címet, vagyis az oktatóanyag részletesen latert.

6. Az a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Raw)** , és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. Az a **Peakon beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![Peakon konfiguráció](common/configuresection.png)

8. Egy másik böngészőablakban jelentkezzen be Peakon rendszergazdaként.

9. Kattintson a menüsávon az oldal bal oldalán, **konfigurációs**, majd keresse meg a **Integrációk**.

    ![A Config](./media/peakon-tutorial/tutorial_peakon_config.png)

10. A **Integrációk** lapon **egyszeri bejelentkezés**.

    ![Az egyetlen](./media/peakon-tutorial/tutorial_peakon_single.png)

11. A **egyszeri bejelentkezés** területén kattintson a **engedélyezése**.

    ![Az engedélyezése](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. Az a **egyszeri bejelentkezési SAML használatával alkalmazottak** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Az a **Egyszeri bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    b. Az a **Egyszeri kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe**, az Azure Portalról másolt.

    c. Kattintson a **fájl kiválasztása** feltölteni a tanúsítványt, amely már letöltötte az Azure Portalról, a tanúsítvány mezőbe.

    d. Kattintson a **ikon** másolása a **Entitásazonosító** és illessze be a **azonosító** szövegmezőjébe **alapszintű SAML-konfigurációja** szakasz az Azure Portalon.

    e. Kattintson a **ikon** másolása a **válasz URL-cím (ACS)** és illessze be a **válasz URL-cím** szövegmezőjébe **alapszintű SAML-konfigurációja**   a szakasz az Azure Portalon.

    f. Kattintson a **Mentés** gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-a-peakon-test-user"></a>Peakon tesztfelhasználó létrehozása

Peakon bejelentkezni az Azure AD-felhasználók engedélyezésének, akkor ki kell építeni Peakon be.  
Peakon, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Peakon vállalati webhely.

2. Kattintson a menüsávon az oldal bal oldalán, **konfigurációs**, majd keresse meg a **alkalmazottak**.

    ![Az alkalmazott](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. Kattintson a lap felső jobb oldalán, **Hozzáadás alkalmazott**.

      ![A Hozzáadás alkalmazott](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Az a **új alkalmazott** párbeszédpanel lapon, a következő lépésekkel:

     ![Az újonnan felvettek betanítása](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Az a **neve** szövegmezőbe típus első néven **Britta** és Vezetéknév szerint **simon**.

    b. Az a **E-mail** beviteli mező, írja be az e-mail-cím, például **Brittasimon@contoso.com**.

    c. Kattintson a **létrehozás alkalmazott**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Peakon Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Peakon**.

    ![Egyszeri bejelentkezés konfigurálása](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Peakon csempére kattint, meg kell lekérése automatikusan bejelentkezett az Peakon alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
