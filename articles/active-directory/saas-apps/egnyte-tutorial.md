---
title: 'Oktatóanyag: Azure Active Directory-integráció az Egnyte |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Egnyte között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2018
ms.author: jeedes
ms.openlocfilehash: 33989d04cba06c789089dfc0db62d177d258292a
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976108"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Oktatóanyag: Azure Active Directory-integráció az Egnyte

Ebben az oktatóanyagban elsajátíthatja, hogyan Egnyte integrálása az Azure Active Directory (Azure AD).

Egnyte integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Egnyte Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Egnyte (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Egnyte az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Egnyte egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Egnyte hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-egnyte-from-the-gallery"></a>Egnyte hozzáadása a katalógusból

Az Azure AD integrálása a Egnyte konfigurálásához hozzá kell Egnyte a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Egnyte hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Egnyte**válassza **Egnyte** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Egnyte](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Egnyte a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Egnyte mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Egnyte hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Egnyte tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy Egnyte tesztfelhasználó létrehozása](#creating-an-egnyte-test-user)**  – egy megfelelője a Britta Simon Egnyte, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Egnyte alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Egnyte, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Egnyte** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egnyte tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Egnyte ügyfél-támogatási csapatának](https://www.egnyte.com/corp/contact_egnyte.html) lekérni ezt az értéket. 

5. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

6. Az a **Egnyte beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![Egnyte konfiguráció](common/configuresection.png)

7. Egy másik böngészőablakban jelentkezzen be a Egnyte vállalati hely rendszergazdaként.

8. Kattintson a **beállítások**.
   
    ![Beállítások](./media/egnyte-tutorial/ic787819.png "beállításai")

9. Kattintson a menü **beállítások**.

    ![Beállítások](./media/egnyte-tutorial/ic787820.png "beállításai")

10. Kattintson a **konfigurációs** fülre, majd **biztonsági**.

    ![Biztonsági](./media/egnyte-tutorial/ic787821.png "biztonsági")

11. Az a **egyszeri bejelentkezéses hitelesítéshez** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés hitelesítési](./media/egnyte-tutorial/ic787822.png "egyszeri bejelentkezés – hitelesítés")   
    
    a. Mint **egyszeri bejelentkezéses hitelesítéshez**válassza **SAML 2.0**.
   
    b. Mint **identitásszolgáltató**válassza **AzureAD**.
   
    c. Beillesztés **bejelentkezési URL-cím** az Azure Portalról másolt a **Identity provider bejelentkezési URL-cím** szövegmezőbe.
   
    d. Beillesztés **az Azure AD-azonosító** az Azure Portalról másolt a **Identity provider Entitásazonosító** szövegmezőbe.
      
    e. A base-64 kódolású tanúsítvány megnyitása a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **szolgáltató identitástanúsítványt** szövegmezőbe.
   
    f. Mint **felhasználóleképezést alapértelmezett**válassza **E-mail-cím**.
   
    g. Mint **tartomány-specifikus kibocsátó értékét használja**válassza **le van tiltva**.
   
    h. Kattintson a **Save** (Mentés) gombra.

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

### <a name="creating-an-egnyte-test-user"></a>Egy Egnyte tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Egnyte jelentkezzen be, akkor ki kell építeni Egnyte be. Egnyte, esetén kiépítése a manuális feladat.

**A felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Egnyte** rendszergazdaként a vállalati webhely.

2. Lépjen a **beállítások \> felhasználók és csoportok**.

3. Kattintson a **új felhasználó hozzáadása**, majd válassza ki a hozzáadni kívánt felhasználó típusát.
   
    ![Felhasználók](./media/egnyte-tutorial/ic787824.png "felhasználók")

4. Az a **új kiemelt felhasználó** szakaszban, hajtsa végre az alábbi lépéseket:
    
    ![Új általános jogú felhasználói](./media/egnyte-tutorial/ic787825.png "új általános jogú felhasználó")   

    a. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó **Brittasimon@contoso.com**.

    b. A **felhasználónév** szöveget adja meg például a felhasználó felhasználóneve **Brittasimon**.

    c. Válassza ki **egyszeri bejelentkezési** , **hitelesítési típus**.
   
    d. Kattintson a **Save** (Mentés) gombra.
    
    >[!NOTE]
    >Az Azure Active Directory fióktulajdonos értesítő e-mailt fog kapni.
    >

>[!NOTE]
>Bármely más Egnyte felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Egnyte által biztosított API-k.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Egnyte Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Egnyte**.

    ![Egyszeri bejelentkezés konfigurálása](./media/egnyte-tutorial/tutorial_egnyte_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Egnyte csempére kattint, meg kell lekérése automatikusan bejelentkezett az Egnyte alkalmazáshoz.
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
