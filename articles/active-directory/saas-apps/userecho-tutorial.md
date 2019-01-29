---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező UserEcho |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és UserEcho között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 1aa092391f51d84a5219076ea3c63352718c2f3a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173853"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező UserEcho

Ebben az oktatóanyagban elsajátíthatja, hogyan UserEcho integrálása az Azure Active Directory (Azure AD).

UserEcho integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá UserEcho Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett UserEcho (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

UserEcho az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy UserEcho egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [Próbaverziós ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. UserEcho hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-userecho-from-the-gallery"></a>UserEcho hozzáadása a katalógusból
Az Azure AD integrálása a UserEcho konfigurálásához hozzá kell UserEcho a katalógusból a felügyelt SaaS-alkalmazások listájára.

**UserEcho hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **UserEcho**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/userecho-tutorial/tutorial_userecho_search.png)

1. Az eredmények panelen válassza ki a **UserEcho**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés UserEcho a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó UserEcho mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó UserEcho hivatkozás kapcsolata kell létrehozni.

UserEcho, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az UserEcho tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[UserEcho tesztfelhasználó létrehozása](#creating-a-userecho-test-user)**  – egy megfelelője a Britta Simon UserEcho, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és UserEcho alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés UserEcho, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **UserEcho** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_samlbase.png)

1. Az a **UserEcho tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.userecho.com/`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [UserEcho ügyfél-támogatási csapatának](https://feedback.userecho.com/) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_general_400.png)

1. Az a **UserEcho konfigurációs** területén kattintson **konfigurálása UserEcho** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a UserEcho vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson a felhasználónevére, bontsa ki a menüben, és kattintson **telepítő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_06.png) 

1. Kattintson a **Integrációk**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_07.png) 

1. Kattintson a **webhely**, és kattintson a **egyszeri bejelentkezés (egy SAML2)**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_08.png) 

1. Az a **egyszeri bejelentkezés (SAML)** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Mint **SAML-kompatibilis**válassza **Igen**.
    
    b. Beillesztés **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, azokat az Azure Portalról másolt a **SAML egyszeri bejelentkezési URL-cím** szövegmezőbe.
    
    c. Beillesztés **kijelentkezéses URL-cím**, azokat az Azure Portalról másolt a **távoli logoout URL-cím** szövegmezőbe.
    
    d. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **X.509-tanúsítvány** szövegmezőbe.
    
    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/userecho-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/userecho-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/userecho-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/userecho-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-userecho-test-user"></a>UserEcho tesztfelhasználó létrehozása

Ez a szakasz célja UserEcho Britta Simon nevű felhasználó létrehozásához.

**Britta Simon UserEcho nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a UserEcho vállalati webhelyre rendszergazdaként.

1. A felső eszköztáron kattintson a felhasználónevére, bontsa ki a menüben, és kattintson **telepítő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_06.png)

1. Kattintson a **felhasználók**, bontsa ki a **felhasználók** szakaszban.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_10.png)

1. Kattintson a **felhasználók**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_11.png)

1. Kattintson a **egy új felhasználó meghívása**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_12.png)

1. Az a **egy új felhasználó meghívása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Az a **neve** szövegmezőbe írja be felhasználónevét, például Britta Simon.
    
    b.  Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.
    
    c. Kattintson a **meghívása**.

A rendszer meghívót küld a Britta, amely lehetővé teszi, hogy UserEcho használatának megkezdéséhez. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés UserEcho Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel UserEcho, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **UserEcho**.

    ![Egyszeri bejelentkezés konfigurálása](./media/userecho-tutorial/tutorial_userecho_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.  

Ha a hozzáférési panelen a UserEcho csempére kattint, meg kell lekérése automatikusan bejelentkezett az UserEcho alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/userecho-tutorial/tutorial_general_01.png
[2]: ./media/userecho-tutorial/tutorial_general_02.png
[3]: ./media/userecho-tutorial/tutorial_general_03.png
[4]: ./media/userecho-tutorial/tutorial_general_04.png

[100]: ./media/userecho-tutorial/tutorial_general_100.png

[200]: ./media/userecho-tutorial/tutorial_general_200.png
[201]: ./media/userecho-tutorial/tutorial_general_201.png
[202]: ./media/userecho-tutorial/tutorial_general_202.png
[203]: ./media/userecho-tutorial/tutorial_general_203.png

