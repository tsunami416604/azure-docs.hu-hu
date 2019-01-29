---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező LockPath Keylight |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és LockPath Keylight között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 14ae26b05bc51a1e6488acf5ed6225697a7bcaed
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176386"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező LockPath Keylight

Ebben az oktatóanyagban elsajátíthatja, hogyan LockPath Keylight integrálható az Azure Active Directory (Azure AD).

LockPath Keylight integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá LockPath Keylight Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a LockPath Keylight (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

LockPath Keylight az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy LockPath Keylight egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. LockPath Keylight hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-lockpath-keylight-from-the-gallery"></a>LockPath Keylight hozzáadása a katalógusból
Az Azure AD integrálása a LockPath Keylight konfigurálásához hozzá kell LockPath Keylight a katalógusból a felügyelt SaaS-alkalmazások listájára.

**LockPath Keylight hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **LockPath Keylight**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keylight-tutorial/tutorial_keylight_search.png)

1. Az eredmények panelen válassza ki a **LockPath Keylight**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a egy "Britta Simon." nevű tesztelési felhasználó alapján LockPath Keylight az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó LockPath Keylight mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó LockPath Keylight hivatkozás kapcsolata kell létrehozni.

LockPath Keylight, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az LockPath Keylight tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[LockPath Keylight tesztfelhasználó létrehozása](#creating-a-lockpath-keylight-test-user)**  – egy megfelelője a Britta Simon LockPath Keylight, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és LockPath Keylight alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés LockPath Keylight, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **LockPath Keylight** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/tutorial_keylight_samlbase.png)

1. Az a **LockPath Keylight tartomány és URL-címek** szakaszban, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/tutorial_keylight_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.keylightgrc.com/`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.keylightgrc.com`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [LockPath Keylight ügyfél-támogatási csapatának](https://www.lockpath.com/contact/) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Raw)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/tutorial_keylight_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/tutorial_general_400.png)
    
1. Az a **LockPath Keylight konfigurációs** területén kattintson **konfigurálása LockPath Keylight** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/tutorial_keylight_configure.png) 

1. Ahhoz, hogy az egyszeri bejelentkezés az LockPath Keylight, hajtsa végre az alábbi lépéseket:
   
    a. Bejelentkezés a LockPath Keylight fiókjához rendszergazdaként.
    
    b. A felső menüben kattintson **személy**, és válassza ki **Keylight telepítő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/401.png) 

    c. Kattintson a bal oldali fanézetben **SAML**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/402.png) 

    d. Az a **SAML-beállítások** párbeszédpanelen kattintson a **szerkesztése**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/404.png) 

1. Az a **SAML-beállítások szerkesztése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/405.png) 
   
    a. Állítsa be **SAML-hitelesítés** való **aktív**.

    b. Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról történő másolta a **Identity Provider bejelentkezési URL-cím** szövegmezőbe.

    c. Illessze be a **egyszeri kijelentkezéses szolgáltatás URL-cím** érték, amely az Azure Portalról történő másolta a **Identity Provider kijelentkezési URL-címe** szövegmezőbe.

    d. Kattintson a **fájl kiválasztása** válassza ki a letöltött LockPath Keylight tanúsítványt, és kattintson a **nyílt** feltölteni a tanúsítványt.

    e. Állítsa be **SAML felhasználóazonosító hely** való **NameIdentifier elem a tulajdonos utasítás**.
    
    f. Adja meg a **Keylight szolgáltató** a következő mintával: **https://&lt;CompanyName&gt;. keylightgrc.com**.
    
    g. Állítsa be **automatikus üzembe felhasználók** való **aktív**.

    h. Állítsa be **automatikus üzembe fióktípus** való **teljes jogú felhasználók**.

    i. Állítsa be **automatikus üzembe biztonsági szerepkör**válassza **az SAML általános jogú felhasználói**.
    
    j. Állítsa be **automatikus üzembe biztonsági konfigurációs**válassza **szabványos felhasználói konfigurációs**.
     
    k. Az a **E-mail attribútum** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. Az a **First name attribútum** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. Az a **utolsó név attribútum** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keylight-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keylight-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keylight-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keylight-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>LockPath Keylight tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű LockPath Keylight hoz létre. LockPath Keylight támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van.

Nincs meg ebben a szakaszban a művelet elem. Egy új felhasználó létrehozása LockPath Keylight elérésekor, ha a felhasználó még nem létezik. 

>[!NOTE]
>Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [LockPath Keylight ügyfél-támogatási csapatának](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés LockPath Keylight Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel LockPath Keylight, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **LockPath Keylight**.

    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/tutorial_keylight_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LockPath Keylight csempére kattint, meg kell lekérése automatikusan bejelentkezett az LockPath Keylight alkalmazáshoz. 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/keylight-tutorial/tutorial_general_01.png
[2]: ./media/keylight-tutorial/tutorial_general_02.png
[3]: ./media/keylight-tutorial/tutorial_general_03.png
[4]: ./media/keylight-tutorial/tutorial_general_04.png

[100]: ./media/keylight-tutorial/tutorial_general_100.png

[200]: ./media/keylight-tutorial/tutorial_general_200.png
[201]: ./media/keylight-tutorial/tutorial_general_201.png
[202]: ./media/keylight-tutorial/tutorial_general_202.png
[203]: ./media/keylight-tutorial/tutorial_general_203.png

