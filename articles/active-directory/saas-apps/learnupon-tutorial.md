---
title: 'Oktatóanyag: Azure Active Directory-integráció az LearnUpon |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és LearnUpon között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 27d7949be97dc9f64f3c0855f4f7b936312bf7a8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438667"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Oktatóanyag: Azure Active Directory-integráció az LearnUpon

Ebben az oktatóanyagban elsajátíthatja, hogyan LearnUpon integrálása az Azure Active Directory (Azure AD).

LearnUpon integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá LearnUpon Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett LearnUpon (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

LearnUpon az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy LearnUpon egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. LearnUpon hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-learnupon-from-the-gallery"></a>LearnUpon hozzáadása a katalógusból
Az Azure AD integrálása a LearnUpon konfigurálásához hozzá kell LearnUpon a katalógusból a felügyelt SaaS-alkalmazások listájára.

**LearnUpon hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **LearnUpon**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learnupon-tutorial/tutorial_learnupon_search.png)

1. Az eredmények panelen válassza ki a **LearnUpon**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés LearnUpon a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó LearnUpon mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó LearnUpon hivatkozás kapcsolata kell létrehozni.

LearnUpon, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az LearnUpon tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[LearnUpon tesztfelhasználó létrehozása](#creating-a-learnupon-test-user)**  – egy megfelelője a Britta Simon LearnUpon, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és LearnUpon alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés LearnUpon, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **LearnUpon** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_samlbase.png)

1. Az a **LearnUpon tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_url.png)

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Vegye figyelembe, hogy ez nem a tényleges érték. frissíteni ezt az értéket a tényleges válasz URL-címmel rendelkezik. Ez az érték ügyfél megszerezni [LearnUpon támogatási csapatának](https://www.learnupon.com/features/support/).



1. Az a **SAML-aláíró tanúsítvány** területén keresse meg a **ujjlenyomat** – Ez hozzáadódik a LearnUpon SAML-beállítások.

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_general_400.png)

1. Az a **LearnUpon konfigurációs** területén kattintson **konfigurálása LearnUpon** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_configure.png) 

1. Nyissa meg egy másik böngészőpéldányból és bejelentkezési LearnUpon azokat egy rendszergazdai fiókkal. 

1. Kattintson a **beállítások** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Kattintson a **egyszeri bejelentkezés – SAML**, és kattintson a **általános beállítások** SAML-beállítások konfigurálása.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Az a **általános beállítások** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Válassza ki **engedélyezve**.

    b. Válassza ki **verzió** , **2.0**.

    c. Válassza ki **feltételek kihagyása** , **nem**.

    d. Az a **SAML tokent feladása param name** szövegmezőbe típusát fent jelzett fogyasztói URL SAML-kérelem post paraméter neve, amely tartalmazza a SAML-előfeltétel ellenőrzése és hitelesített – például **SAMLResponse** .

    e. Az a **azonosító formátuma** szövegmezőbe írja be az érték, amely azt jelzi, hogy hol található a SAML helyességi feltétel alkalmazása a felhasználók azonosítója (E-mail-cím) található – például **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: e-mail cím**.
  
    f. Az a **azonosítása szolgáltató helye** szövegmezőbe írja be az értéket, amely azt jelzi, ahol a felhasználóknak legyenek elküldve Ha az Azure portal bejelentkezési képernyőjéről a feltöltött ikonra kattintanak.
  
    g. Az a **kijelentkezési URL-** szövegmezőjébe illessze be a **kijelentkezéses URL-cím** az Azure Portalról másolt.
    
    h. Kattintson a **ujját megrendelése kezelése**, majd töltse fel a a letöltött tanúsítvány-ujjlenyomat.

1. Kattintson a **felhasználói beállítások**, és hajtsa végre az alábbi lépéseket:
   
     ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. Az a **Utónév azonosító formátuma** szövegmezőbe írja be az érték, amely tudatja velünk, ahol a a SAML helyességi feltétel alkalmazása a felhasználók firstname található – például: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. Az a **utolsó azonosító formátumot** szövegmezőbe írja be az érték, amely tudatja velünk, ahol a a SAML helyességi feltétel alkalmazása a felhasználók lastname található – például: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learnupon-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learnupon-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learnupon-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learnupon-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-learnupon-test-user"></a>LearnUpon tesztfelhasználó létrehozása

Ez a szakasz célja LearnUpon Britta Simon nevű felhasználó létrehozásához. LearnUpon támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van.

Nincs meg ebben a szakaszban a művelet elem. Egy új felhasználót hoz létre az LearnUpon elérésére, ha még nem létezik tett kísérlet során. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Hozzon létre egy felhasználót manuálisan kell, ha kapcsolatba kell [LearnUpon támogatási csapatának](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés LearnUpon Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel LearnUpon, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **LearnUpon**.

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LearnUpon csempére kattint, meg kell lekérése automatikusan bejelentkezett az LearnUpon alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learnupon-tutorial/tutorial_general_01.png
[2]: ./media/learnupon-tutorial/tutorial_general_02.png
[3]: ./media/learnupon-tutorial/tutorial_general_03.png
[4]: ./media/learnupon-tutorial/tutorial_general_04.png

[100]: ./media/learnupon-tutorial/tutorial_general_100.png

[200]: ./media/learnupon-tutorial/tutorial_general_200.png
[201]: ./media/learnupon-tutorial/tutorial_general_201.png
[202]: ./media/learnupon-tutorial/tutorial_general_202.png
[203]: ./media/learnupon-tutorial/tutorial_general_203.png

