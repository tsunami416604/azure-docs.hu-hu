---
title: 'Oktatóanyag: Azure Active Directory-integráció az DocuSign |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a DocuSign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 5d8aef1edf4d7a02686db48d3e788e4f9493c398
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448485"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Oktatóanyag: DocuSign-Azure Active Directory-integráció

Ebben az oktatóanyagban megismerheti, hogyan integrálható a DocuSign az Azure Active Directory (Azure AD).

Az Azure AD integrálása a DocuSign nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a DocuSign az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett, a DocuSign (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a DocuSign, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A DocuSign az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás a katalógusból, DocuSign
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-docusign-from-the-gallery"></a>Hozzáadás a katalógusból, DocuSign
Konfigurálhatja az Azure AD integrálása a DocuSign, hozzá kell DocuSign a galériából a felügyelt SaaS-alkalmazások listájára.

**DocuSign hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **DocuSign**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/docusign-tutorial/tutorial_docusign_search.png)

1. Az eredmények panelen válassza ki a **DocuSign**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a DocuSign, a teszt "Britta Simon." nevű felhasználó

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a DocuSign tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Docusignban hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a Docusignban.

Az Azure AD egyszeri bejelentkezés a DocuSign tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[DocuSign tesztfelhasználó létrehozása](#creating-a-docusign-test-user)**  – van egy Britta Simon megfelelője a DocuSign, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és DocuSign-alkalmazását az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés, a DocuSign, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **DocuSign** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_samlbase.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base-64)** , és mentse a tanúsítványfájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

1. A a **DocuSign konfigurációs** az Azure Portalon, kattintson a szakasz **konfigurálása DocuSign** konfigurálása bejelentkezési ablak megnyitásához. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**
    
    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_configure.png)

1. Egy másik böngészőablakban, jelentkezzen be a **DocuSign felügyeleti portál** rendszergazdaként.

1. A bal oldali navigációs menüben kattintson **tartományok**.
   
    ![Egyszeri bejelentkezés konfigurálása][51]

1. Kattintson a jobb oldali **jogcím tartomány**.
   
    ![Egyszeri bejelentkezés konfigurálása][52]

1. Az a **jogcím egy tartomány** párbeszédpanelen, a a **tartománynév** szövegmezőbe írja be a vállalati tartományhoz, és kattintson **jogcím**. Győződjön meg arról, hogy a tartomány ellenőrzéséhez és az állapot aktív.
   
    ![Egyszeri bejelentkezés konfigurálása][53]

1. A bal oldali menüben kattintson **Identitásszolgáltatók**  
   
    ![Egyszeri bejelentkezés konfigurálása][54]
1. Kattintson a jobb oldali ablaktáblában **identitásszolgáltató hozzáadása**. 
   
    ![Egyszeri bejelentkezés konfigurálása][55]

1. Az a **identitás Szolgáltatóbeállítások** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása][56]

    a. Az a **neve** szövegmezőbe írja be egy egyedi nevet a konfigurációnak. Ne használjon szóközöket.

    b. Beillesztés **SAML Entitásazonosító** be a **identitásszolgáltató kibocsátója** szövegmezőbe.

    c. Beillesztés **SAML egyszeri bejelentkezési szolgáltatás URL-cím** be a **Identity Provider bejelentkezési URL-cím** szövegmezőbe.

    d. Beillesztés **kijelentkezéses URL-cím** be a **Identity Provider kijelentkezési URL-címe** szövegmezőbe.

    e. Válassza ki **AuthN kérés aláírásához**.

    f. Mint **kérelmének küldése AuthN**válassza **POST**.

    g. Mint **küldési kijelentkezési kérés által**válassza **első**.

1. Az a **egyéni attribútumleképzés** területen válassza ki a mezőt hozzá kívánja rendelni az Azure AD-jogcímet. Ebben a példában a **emailaddress** jogcím van leképezve a következő értékkel: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Az alapértelmezett jogcímszabály neve e-mail követelés az Azure AD-ből. 
   
    > [!NOTE]
    > Használja a megfelelő **felhasználóazonosító** hozzárendelni a felhasználó Azure AD-ből, a DocuSign felhasználó-hozzárendelés. Válassza ki a megfelelő mezőt, és adja meg a megfelelő értéket, a szervezet beállítások alapján.
          
    ![Egyszeri bejelentkezés konfigurálása][57]

1. Az a **szolgáltató Identitástanúsítványt** területén kattintson **tanúsítvány hozzáadása**, majd töltse fel a tanúsítványt, hogy letöltötte az Azure AD portálon.   
   
    ![Egyszeri bejelentkezés konfigurálása][58]

1. Kattintson a **Save** (Mentés) gombra.

1. Az a **Identitásszolgáltatók** területén kattintson **műveletek**, és kattintson a **végpontok**.   
   
    ![Egyszeri bejelentkezés konfigurálása][59]
 
1. Az a **SAML 2.0-s végpontok megtekintése** szakaszában **DocuSign felügyeleti portálon**, hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása][60]
   
    a. Másolási a **szolgáltató kibocsátó URL-címe**, majd illessze be a **azonosító** szövegmezőben, hogy a **DocuSign-tartomány és URL-címek** az Azure Portal, a minta a következő szakaszban: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Másolás a **szolgáltató bejelentkezési URL-címe**, majd illessze be a **bejelentkezési URL-** szövegmezőben, hogy a **DocuSign-tartomány és URL-címek** az Azure Portal, a minta a következő szakaszban: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/` .

    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Kattintson a **bezárása**
    
1. Az Azure Portalon kattintson **mentése**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/docusign-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/docusign-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/docusign-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/docusign-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-docusign-test-user"></a>DocuSign tesztfelhasználó létrehozása

Alkalmazás által támogatott **csak az idő felhasználókiépítés** , miután a felhasználók hitelesítésére automatikusan létrehozza az alkalmazásban.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon számára a hozzáférés biztosításával a DocuSign Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel a DocuSign, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **DocuSign**.

    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a DocuSign csempére kattint, meg kell lekérése automatikusan bejelentkezett a DocuSign-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/docusign-tutorial/tutorial_general_01.png
[2]: ./media/docusign-tutorial/tutorial_general_02.png
[3]: ./media/docusign-tutorial/tutorial_general_03.png
[4]: ./media/docusign-tutorial/tutorial_general_04.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/docusign-tutorial/tutorial_general_100.png

[200]: ./media/docusign-tutorial/tutorial_general_200.png
[201]: ./media/docusign-tutorial/tutorial_general_201.png
[202]: ./media/docusign-tutorial/tutorial_general_202.png
[203]: ./media/docusign-tutorial/tutorial_general_203.png

