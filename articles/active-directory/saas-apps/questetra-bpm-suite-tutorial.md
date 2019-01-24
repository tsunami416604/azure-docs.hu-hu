---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Questetra BPM Suite-tal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Questetra BPM Suite és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: cc2d88bfc6b8ce57cebc2e35e3a9f2e3b826e9cd
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816772"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Oktatóanyag: Az Azure Active Directory-integráció Questetra BPM Suite-tal

Ebben az oktatóanyagban elsajátíthatja, hogyan Questetra BPM Suite integrálható az Azure Active Directory (Azure AD).

Questetra BPM Suite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Questetra BPM Suite Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Questetra BPM Suite (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Questetra BPM Suite, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Questetra BPM Suite egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Questetra BPM Suite hozzáadása a katalógusból
1. Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Questetra BPM Suite hozzáadása a katalógusból
Az Azure AD-be Questetra BPM Suite integrációjának konfigurálása, hozzá kell Questetra BPM Suite a galériából a felügyelt SaaS-alkalmazások listájára.

**Questetra BPM Suite hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Questetra BPM Suite**válassza **Questetra BPM Suite** eredmény panelen, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Katalógus hozzáadása](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Questetra BPM Suite egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Questetra BPM Suite mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Questetra BPM Suite hivatkozás kapcsolata kell létrehozni.

Questetra BPM Suite, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Questetra BPM Suite tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Questetra BPM Suite tesztfelhasználót](#create-a-questetra-bpm-suite-test-user)**  – egy megfelelője a Britta Simon Questetra BPM csomag, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Questetra BPM csomag alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Questetra BPM Suite-tal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Questetra BPM Suite** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![SAML-alapú bejelentkezés](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

1. Az a **Questetra BPM Suite tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A szakasz Questetra BPM Suite tartomány és URL-címek](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Beszerezheti ezeket az értéteket **SP információk** szakaszában a **Questetra BPM Suite** vállalati webhelyre, ahol később az oktatóanyag, vagy forduljon a [Questetra BPM Suite ügyfelek támogatása csapat](https://www.questetra.com/contact/). 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base-64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![SAML-aláíró tanúsítvány szakaszban](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Mentés gomb](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

1. Az a **Questetra BPM konfigurálásának** területén kattintson **Questetra BPM Suite konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Questetra BPM Suite konfigurációs szakasz](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

1. Egy másik böngészőablakban, jelentkezzen be a **Questetra BPM Suite** rendszergazdaként a vállalati webhely.

1. A felső menüben kattintson **rendszerbeállítások**. 
   
    ![Az Azure AD egyszeri bejelentkezés][10]

1. Megnyitásához a **SingleSignOnSAML** kattintson **egyszeri bejelentkezés (SAML)**. 
   
    ![Az Azure AD egyszeri bejelentkezés][11]

1. Az a **Questetra BPM Suite** vállalati hely, az a **SP információkat** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Másolás a **ACS URL**, és illessze be azt a **bejelentkezési URL-** szövegmezőjébe a **Questetra BPM Suite tartomány és URL-címek** szakaszban az Azure Portalról.
    
    b. Másolás a **Entitásazonosító**, és illessze be azt a **azonosító** szövegmezőjébe a **Questetra BPM Suite tartomány és URL-címek** szakaszban az Azure Portalról.

1. Az a **Questetra BPM Suite** vállalati hely, hajtsa végre az alábbi lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása][15]
   
    a. Válassza ki **egyszeri bejelentkezés engedélyezése**.
   
    b. A **Entitásazonosító** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító** Azure Portalról másolt.
    
    c. A **bejelentkezési oldal URL** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
    
    d. A **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt.
    
    e. Az a **nameid-formátumához** szövegmezőbe írja be `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Nyissa meg a **Base-64** kódolt tanúsítvány a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **érvényesítési tanúsítvány** szövegmezőbe. 

    g. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Questetra BPM Suite tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon Questetra BPM Suite nevű felhasználó létrehozásához.

**Britta Simon Questetra BPM Suite nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Questetra BPM Suite vállalati webhely.
1. Lépjen a **Rendszerbeállítások > Felhasználólista > Új felhasználó**. 
1. Új felhasználó párbeszédpanelen hajtsa végre az alábbi lépéseket: 
   
    ![Tesztfelhasználó létrehozása][300] 
   
    a. Az a **neve** szövegmezőbe írja be **neve** felhasználó **britta.simon@contoso.com**.
   
    b. Az a **E-mail** szövegmezőbe írja be **e-mail** felhasználó **britta.simon@contoso.com**
   
    c. Az a **jelszó** szövegmezőben adjon meg egy **jelszó** felhasználó.
    
    d. Kattintson a **új felhasználó hozzáadása**.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Questetra BPM Suite Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Questetra BPM Suite, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Questetra BPM Suite**.

    ![Questetra BPM Suite alkalmazások listájában](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Questetra BPM Suite csempére kattint, meg kell lekérése automatikusan bejelentkezett az Questetra BPM Suite alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

