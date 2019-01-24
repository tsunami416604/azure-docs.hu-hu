---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Mixpanel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Mixpanel között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 8475cccdac5c864171ac0bad0ad16ed6d6849ecc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823164"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Oktatóanyag: Mixpanel az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Mixpanel integrálása az Azure Active Directory (Azure AD).

Mixpanel integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Mixpanel Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Mixpanel (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Mixpanel, a következőkre van szükség:

- Azure AD-előfizetés
- A Mixpanel egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Mixpanel hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-mixpanel-from-the-gallery"></a>Mixpanel hozzáadása a katalógusból
Az Azure AD integrálása a Mixpanel konfigurálásához hozzá kell Mixpanel a galériából a felügyelt SaaS-alkalmazások listájára.

**Mixpanel hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Mixpanel**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mixpanel-tutorial/tutorial_mixpanel_search.png)

1. Az eredmények panelen válassza ki a **Mixpanel**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mixpanel-tutorial/tutorial_mixpanel_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Mixpanel a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben kell tudja, hogy mi a tartozó felhasználó, a mixpanelben már létrehozott egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a mixpanelben már létrehozott hivatkozás kapcsolatának kell létrehozni.

Mixpanel, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Mixpanel tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Mixpanel tesztfelhasználó létrehozása](#creating-a-mixpanel-test-user)**  – a felhasználó Azure ad-ben reprezentációja kapcsolódó mixpanelben már létrehozott egy megfelelője a Britta Simon rendelkeznie.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Mixpanel alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Mixpanel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Mixpanel** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_samlbase.png)

1. Az a **Mixpanel tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_url.png)

     Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-cím: `https://mixpanel.com/login/`

    > [!NOTE] 
    > Regisztrálja a [ https://mixpanel.com/register/ ](https://mixpanel.com/register/) állíthatja be a bejelentkezési hitelesítő adatait, és forduljon az [Mixpanel támogatási csapatának](mailto:support@mixpanel.com) a bérlőhöz tartozó egyszeri bejelentkezési beállításainak engedélyezése. Is megtekintheti a bejelentkezési URL-érték szükség esetén a Mixpanel támogatási csapatának. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_general_400.png)

1. Az a **Mixpanel konfigurációs** területén kattintson **konfigurálása Mixpanel** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_configure.png) 

1. Egy másik böngészőablakban bejelentkezést a Mixpanel alkalmazást rendszergazdaként.

1. A lap aljára, kattintson a kis **fogaskerék** ikonra a bal oldali sarokban. 
   
    ![Mixpanel Single Sign-On](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

1. Kattintson a **hozzáférését** fülre, majd **beállításainak módosítása**.
   
    ![Mixpanel Settings](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

1. A a **az tanúsítványának módosítása** párbeszédpanel lap, kattintson a **fájl kiválasztása** a letöltött tanúsítvány feltöltéséhez, majd **tovább**.
   
    ![Mixpanel Settings](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

1.  A hitelesítési URL-címe szövegmezőbe a a **módosítsa a hitelesítési URL-címet** párbeszédpanel lapon, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt majd **Tovább**.
   
   ![Mixpanel Settings](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

1. Kattintson a **Done** (Kész) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mixpanel-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mixpanel-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mixpanel-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mixpanel-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-mixpanel-test-user"></a>Mixpanel tesztfelhasználó létrehozása

Ez a szakasz célja a mixpanelben már létrehozott Britta Simon nevű felhasználó létrehozásához. 

1. Jelentkezzen be rendszergazdaként a Mixpanel vállalati webhely.

1. A lap alján kattintson a kis fogaskerék gombra megnyitásához a bal oldali sarokban a **beállítások** ablak.

1. Kattintson a **csapat** fülre.

1. Az a **csapattag** szövegmezőbe írja be a Britta e-mail címet az Azure-ban.
   
    ![Mixpanel Settings](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

1. Kattintson a **meghívása**. 

> [!Note]
> A felhasználó kap egy e-mailt a profil beállításához.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Mixpanel Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Mixpanel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Mixpanel**.

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Mixpanel csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Mixpanel alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/mixpanel-tutorial/tutorial_general_04.png

[100]: ./media/mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/mixpanel-tutorial/tutorial_general_201.png
[202]: ./media/mixpanel-tutorial/tutorial_general_202.png
[203]: ./media/mixpanel-tutorial/tutorial_general_203.png

