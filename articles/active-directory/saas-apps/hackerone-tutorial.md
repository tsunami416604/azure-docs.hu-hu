---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Hackerone |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Hackerone között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 613d222b04d5e79b863ca9ce70db287e5e937efe
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177852"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező HackerOne

Ebben az oktatóanyagban elsajátíthatja, hogyan HackerOne integrálása az Azure Active Directory (Azure AD).

HackerOne integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá HackerOne Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett HackerOne (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

HackerOne az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy HackerOne egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. HackerOne hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-hackerone-from-the-gallery"></a>HackerOne hozzáadása a katalógusból
Az Azure AD integrálása a HackerOne konfigurálásához hozzá kell HackerOne a katalógusból a felügyelt SaaS-alkalmazások listájára.

**HackerOne hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **HackerOne**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hackerone-tutorial/tutorial_hackerone_search.png)

1. Az eredmények panelen válassza ki a **HackerOne**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hackerone-tutorial/tutorial_hackerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó HackerOne az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó HackerOne mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó HackerOne hivatkozás kapcsolata kell létrehozni.

HackerOne, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az HackerOne tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[HackerOne tesztfelhasználó létrehozása](#creating-a-hackerone-test-user)**  – egy megfelelője a Britta Simon HackerOne, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és HackerOne alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés HackerOne, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **HackerOne** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_hackerone_samlbase.png)

1. Az a **HackerOne egyszeri bejelentkezési URL-cím és azonosító** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_hackerone_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://hackerone.com/<company name>/authentication`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-cím:  `https://hackerone.com/users/saml/metadata`
    
    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [HackerOne támogatási csapatának](mailto:support@hackerone.com) lekérni ezt az értéket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_hackerone_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_general_400.png)

1. Az a **HackerOne konfigurációs** területén kattintson **konfigurálása HackerOne** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_hackerone_configure.png) 

1. Bejelentkezés rendszergazdaként az HackerOne bérlőhöz.

1. A felső menüben kattintson a "**beállítások**."
   
    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_hackerone_001.png) 

1. Navigáljon a "**hitelesítési**"és kattintson a"**SAML-beállítások hozzáadása**."
   
    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_hackerone_003.png) 

1. Az a **SAML-beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_hackerone_004.png) 

    a. Az a **levelezőtartomány** szövegmezőbe írja be a regisztrált tartomány.

    b. A  **egyszeri bejelentkezési URL-Címként** szövegmezőből, illessze be az értékét **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    c. Nyissa meg a **tanúsítványfájl** a Jegyzettömbben az Azure-portálról letöltött, másolja a tartalmát a vágólapra, és illessze be azt a **X509 tanúsítvány**  szövegmezőbe.
    
    d. Kattintson a **Save** (Mentés) gombra.

1. A hitelesítési beállítások párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_hackerone_005.png) 

    a. Kattintson a **teszt futtatása**.

    b. Ha értékét a **állapot** egyenlő mező **utolsó teszt állapota: létrehozott**, forduljon a [HackerOne támogatási csapatának](mailto:support@hackerone.com) egy át a konfigurációt, kérjen.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hackerone-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hackerone-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hackerone-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hackerone-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-hackerone-test-user"></a>HackerOne tesztfelhasználó létrehozása

Ezután hozzon létre egy HackerOne a Britta Simon nevű felhasználó. HackerOne támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van.

Nincs meg ebben a szakaszban a művelet elem. Amikor HackerOne fér hozzá, ha még nem létezik egy új felhasználót jön létre.

>[!NOTE]
>Hozzon létre egy felhasználót manuálisan kell, ha szüksége fel a kapcsolatot a Certify csapatával. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés HackerOne Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel HackerOne, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **HackerOne**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hackerone-tutorial/tutorial_hackerone_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Végül az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelnie.  

Ha a hozzáférési panelen a HackerOne csempére kattint, meg kell lekérése automatikusan bejelentkezett az HackerOne alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hackerone-tutorial/tutorial_general_01.png
[2]: ./media/hackerone-tutorial/tutorial_general_02.png
[3]: ./media/hackerone-tutorial/tutorial_general_03.png
[4]: ./media/hackerone-tutorial/tutorial_general_04.png

[100]: ./media/hackerone-tutorial/tutorial_general_100.png

[200]: ./media/hackerone-tutorial/tutorial_general_200.png
[201]: ./media/hackerone-tutorial/tutorial_general_201.png
[202]: ./media/hackerone-tutorial/tutorial_general_202.png
[203]: ./media/hackerone-tutorial/tutorial_general_203.png

