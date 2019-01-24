---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező felismerése |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés felismerése és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 49d501a07f2efa6e9c6e20dfe2a026badf13f624
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824150"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező felismerése

Ebben az oktatóanyagban elsajátíthatja, hogyan felismerése integrálása az Azure Active Directory (Azure AD).

Felismerése integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá felismerése Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett felismerése (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása felismerése, a következőkre van szükség:

- Azure AD-előfizetés
- Egy felismerése egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [Próbaverziós ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Felismerése hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-recognize-from-the-gallery"></a>Felismerése hozzáadása a katalógusból
Konfigurálása az Azure AD integrálása a felismerése, hozzá kell felismerése a galériából a felügyelt SaaS-alkalmazások listájára.

**Felismerése hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **felismerése**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/recognize-tutorial/tutorial_recognize_search.png)

1. Az eredmények panelen válassza ki a **felismerése**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a teszt "Britta Simon" nevű felhasználó felismerése.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó felismerése mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó felismerése hivatkozás kapcsolata kell létrehozni.

Felismerése, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az felismerése tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Felismerése tesztfelhasználó létrehozása](#creating-a-recognize-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure AD felhasználói ábrázolása felismerése.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és felismerése alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés felismerése, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **felismerése** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/recognize-tutorial/tutorial_recognize_samlbase.png)

1. Az a **ismeri fel a tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/recognize-tutorial/tutorial_recognize_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://recognizeapp.com/<your-domain>/saml/sso`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [ügyfél ismeri fel a támogatási csapat](mailto:support@recognizeapp.com) beolvasni a bejelentkezési URL-címet, és beszerezheti azonosító értékét az egyszeri bejelentkezési beállításainak szakaszban az oktatóanyag későbbi részében kifejtett nyissa meg a Service Provider metaadatok URL-címe. . 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/recognize-tutorial/tutorial_recognize_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/recognize-tutorial/tutorial_general_400.png)

1. Az a **ismeri fel a konfigurációs** területén kattintson **konfigurálása felismerni** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/recognize-tutorial/tutorial_recognize_configure.png) 

1. Egy másik böngészőablakban, a bejelentkezés a felismerése bérlői rendszergazdaként.

1. Kattintson a jobb felső sarokban **menü**. Lépjen a **vállalati rendszergazdai**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_000.png)

1. A bal oldali navigációs panelén kattintson **beállítások**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_001.png)

1. Hajtsa végre a következő lépéseket **egyszeri bejelentkezési beállításainak** szakaszban.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Mint **SSO engedélyezése**válassza **ON**.

    b. Az a **Identitásszolgáltató Entitásazonosító** szövegmező, illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt.
    
    c. Az a **Sso cél URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
    
    d. A a **Slo-cél URL-cím** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím** Azure Portalról másolt. 
    
    e. Nyissa meg a letöltött **tanúsítvány (Base64)** fájlt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **tanúsítvány** szövegmezőbe.
    
    f. Kattintson a **beállítások mentése** gombra. 

1. Mellett a **egyszeri bejelentkezési beállításainak** területén másolja az URL-CÍMÉT a **Service Provider metaadatok URL-címe**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_003.png)

1. Nyissa meg a **metaadatok URL-Címhivatkozás** egy üres böngészőt, és töltse le a metaadat-dokumentum alapján. Ezután a EntityDescriptor value(entityID) átmásolhatja a fájlt, és illessze be a **azonosító** szövegmezőjébe **ismeri fel a tartomány és URL-címek szakaszt** az Azure Portalon.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/recognize-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/recognize-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/recognize-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/recognize-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-recognize-test-user"></a>Felismerése tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók felismerése szolgáltatásba való bejelentkezéshez, akkor ki kell építeni felismerése be. Felismerése, esetén kiépítése a manuális feladat.

Ez az alkalmazás nem támogatja az SCIM-kiépítés, de egy másik felhasználó szinkronizálási felhasználókat kiépítő rendelkezik. 

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a felismerése vállalati hely rendszergazdaként.

1. Kattintson a jobb felső sarokban **menü**. Lépjen a **vállalati rendszergazdai**.

1. A bal oldali navigációs panelén kattintson **beállítások**.

1. Hajtsa végre a következő lépéseket **felhasználói szinkronizálási** szakaszban.
   
   ![Új felhasználó](./media/recognize-tutorial/tutorial_recognize_005.png "új felhasználó")
   
   a. Mint **-szinkronizálást engedélyező**válassza **ON**.
   
   b. Mint **válasszon szinkronizálási szolgáltató**válassza **Microsoft / Office 365**.
   
   c. Kattintson a **felhasználói Sync futtatásával**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés felismerése Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel felismerése, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **felismerése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/recognize-tutorial/tutorial_recognize_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a felismerése csempére kattint, meg kell lekérése automatikusan bejelentkezett az felismerése alkalmazás. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/recognize-tutorial/tutorial_general_01.png
[2]: ./media/recognize-tutorial/tutorial_general_02.png
[3]: ./media/recognize-tutorial/tutorial_general_03.png
[4]: ./media/recognize-tutorial/tutorial_general_04.png

[100]: ./media/recognize-tutorial/tutorial_general_100.png

[200]: ./media/recognize-tutorial/tutorial_general_200.png
[201]: ./media/recognize-tutorial/tutorial_general_201.png
[202]: ./media/recognize-tutorial/tutorial_general_202.png
[203]: ./media/recognize-tutorial/tutorial_general_203.png

