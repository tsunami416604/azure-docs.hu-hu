---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Moxtra |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Moxtra között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f613c7768b92ce97dcc16a757a066dd5796879b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180592"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Moxtra

Ebben az oktatóanyagban elsajátíthatja, hogyan Moxtra integrálása az Azure Active Directory (Azure AD).

Moxtra integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Moxtra Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Moxtra (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Moxtra az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Moxtra egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Moxtra hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-moxtra-from-the-gallery"></a>Moxtra hozzáadása a katalógusból
Az Azure AD integrálása a Moxtra konfigurálásához hozzá kell Moxtra a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Moxtra hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Moxtra**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/moxtra-tutorial/tutorial_moxtra_search.png)

1. Az eredmények panelen válassza ki a **Moxtra**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Moxtra a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Moxtra mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Moxtra hivatkozás kapcsolata kell létrehozni.

Moxtra, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Moxtra tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Moxtra tesztfelhasználó létrehozása](#creating-a-moxtra-test-user)**  – egy megfelelője a Britta Simon Moxtra, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Moxtra alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Moxtra, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Moxtra** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_moxtra_samlbase.png)

1. Az a **Moxtra tartomány és URL-címek** szakaszban, hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_moxtra_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-cím: `https://www.moxtra.com/service/#login`

1. Moxtra alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. A következő képernyőképen látható egy példa az ehhez a konfigurációhoz. 

    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_moxtra_attributes.png)
    
1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | Keresztnév | user.givenname |
    | Vezetéknév | user.surname |
    | idpid    | < SAML entitás azonosítója > 

    > [!Note]
    > Az érték **idpid** attribútum értéke nem valódi. A tényleges értéket kap **rövid összefoglaló** szakaszba **Moxtra konfigurációs**.
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_attribute_04.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_attribute_05.png)

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Kattintson az **OK** gombra.
    
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_moxtra_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_general_400.png)

1. Az a **Moxtra konfigurációs** területén kattintson **konfigurálása Moxtra** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_moxtra_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Moxtra vállalati hely rendszergazdaként.

1. A bal oldali eszköztáron kattintson **felügyeleti konzol > SAML egyszeri bejelentkezés**, és kattintson a **új**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

1. Az a **SAML** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például: *SAML*). 
  
    b. Az a **identitásszolgáltató Entitásazonosító** szövegmező, illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt. 
 
    c. A **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt. 
 
    d. Az a **AuthnContextClassRef** szövegmezőbe írja be **urn: oasis: nevek: tc: SAML:2.0:ac:classes:Password**. 
 
    e. Az a **nameid-formátumához** szövegmezőbe írja be **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: emailAddress**. 
 
    f. Nyissa meg a tanúsítványt, amely már letöltötte az Azure Portalról a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **tanúsítvány** szövegmezőbe.    
 
    g. Az SAML e-mail tartomány szövegmező írja be az SAML e-mail tartományhoz.    
  
    >[!NOTE]
    >A lépéseket a tartomány ellenőrzéséhez kattintson a "**i**" alatt.

    h. Kattintson az **Update** (Frissítés) elemre.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/moxtra-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/moxtra-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/moxtra-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/moxtra-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-moxtra-test-user"></a>Moxtra tesztfelhasználó létrehozása

Ez a szakasz célja Moxtra Britta Simon nevű felhasználó létrehozásához.

**Britta Simon Moxtra nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Moxtra vállalati webhely.

1. A bal oldali eszköztáron kattintson **felügyeleti konzol > felhasználók kezelése**, majd **felhasználó hozzáadása**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
  
    a. Az a **Utónév** szövegmezőbe írja be **Britta**.
  
    b. Az a **Vezetéknév** szövegmezőbe írja be **Simon**.
  
    c. Az a **E-mail** szövegmezőbe írja be a Britta e-mail cím ugyanaz, mint az Azure Portalon.
  
    d. Az a **osztás** szövegmezőbe írja be **fejlesztési**.
  
    e. Az a **részleg** szövegmezőbe írja be **informatikai**.
  
    f. Válassza ki **rendszergazda**.
  
    g. Kattintson a **Hozzáadás** parancsra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Moxtra Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Moxtra, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Moxtra**.

    ![Egyszeri bejelentkezés konfigurálása](./media/moxtra-tutorial/tutorial_moxtra_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Moxtra csempére kattint, meg kell lekérése automatikusan bejelentkezett az Moxtra alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/moxtra-tutorial/tutorial_general_01.png
[2]: ./media/moxtra-tutorial/tutorial_general_02.png
[3]: ./media/moxtra-tutorial/tutorial_general_03.png
[4]: ./media/moxtra-tutorial/tutorial_general_04.png

[100]: ./media/moxtra-tutorial/tutorial_general_100.png

[200]: ./media/moxtra-tutorial/tutorial_general_200.png
[201]: ./media/moxtra-tutorial/tutorial_general_201.png
[202]: ./media/moxtra-tutorial/tutorial_general_202.png
[203]: ./media/moxtra-tutorial/tutorial_general_203.png

