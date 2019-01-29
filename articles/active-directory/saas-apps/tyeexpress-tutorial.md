---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a T & E Express |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a T & E Express között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.openlocfilehash: 77e9bc8be6b85cdd49a3ca675c360f868f582f6e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155408"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Oktatóanyag: Az Azure Active Directory-integráció a T & E Express

Ebben az oktatóanyagban elsajátíthatja a & E Express integrálása az Azure Active Directory (Azure AD).

T & E Express integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a & E Express Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a T & E Express (egyszeri bejelentkezés) az Azure AD-fiókjukat
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a T & E Express, a következőkre van szükség:

- Azure AD-előfizetés
- A T & E Express egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. T & E Express hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-te-express-from-the-gallery"></a>T & E Express hozzáadása a katalógusból
Az Azure AD-be olyan példá & E Express integráció konfigurálásához, hozzá kell i & E Express a katalógusból a felügyelt SaaS-alkalmazások listájára.

**T & E Express hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **i & E Express**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tyeexpress-tutorial/tutorial_tyeexpress_search.png)

1. Az eredmények panelen válassza ki a **i & E Express**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a T & E Express a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a T & E Express tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a T & E Express hivatkozás kapcsolatát kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a T & E Express.

Az Azure AD egyszeri bejelentkezés a T & E Express tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[T & E Express tesztfelhasználó létrehozása](#creating-a-te-express-test-user)**  - a-megfelelője a T & E Express, amely kapcsolódik az Azure ad-ben ábrázolása őt Britta Simon rendelkezik.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon, és a T & E Express alkalmazást az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása a T & E Express, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **i & E Express** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

1. Az a **& E Express tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az értéket, mint: `https://<domain>.tyeexpress.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Vegye figyelembe, hogy ezek nem állnak a valós értékeket. Az értékeket módosítsa a tényleges azonosítója és a válasz URL-címet kell. Itt javasoljuk, hogy az azonosító egyedi karakterlánc értékét használhatja. Kapcsolattartó [i & E Express támogatási csoportjának](http://www.tyeexpress.com/contacto.aspx) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/tyeexpress-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **T & E expressz** oldalán, a T & E bejelentkezési express anélkül, hogy egyszeri bejelentkezési SAML rendszergazdai hitelesítő adatok használatával.

1. Alatt a **rendszergazdai** fülre, kattintson a **SAML tartomány** a SAML-beállítások lap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/tyeexpress-tutorial/tye-SAML.png)

1. Válassza ki a **Activar(Activate)** parancsát **nem** való **SI(Yes)**. A a **Identity Provider metaadatok** szövegmezőjébe illessze be a metaadatokat, amelyek XML letöltése az Azure Portalról.

    ![Egyszeri bejelentkezés konfigurálása](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Kattintson a **Guardar(Save)** gombra kattintva mentse a beállításokat.  


### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tyeexpress-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tyeexpress-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tyeexpress-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tyeexpress-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-te-express-test-user"></a>T & E Express tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a & E Express, akkor ki kell építeni T & E Express.  
T & E Express, esetén kiépítése a manuális feladat.

**A felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a T & E Express vállalati webhely.

1. Rendszergazdai címke kattintson a felhasználók számára a felhasználók fő lap megnyitásához.

    ![Alkalmazott hozzáadása](./media/tyeexpress-tutorial/tye-adminusers.png)

1. A kezdőlapon kattintson a **+** adhat hozzá a felhasználókat.

    ![Alkalmazott hozzáadása](./media/tyeexpress-tutorial/tye-usershome.png)

1. Írja be a kötelező részleteit kéri a képernyőn kattintson a Mentés gombra kattintva mentse a részleteket.

    ![Alkalmazott hozzáadása](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Alkalmazott hozzáadása](./media/tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon számára hozzáférést biztosít az i & E Express által használandó Azure egyszeri bejelentkezést.

![Felhasználó hozzárendelése][200] 

**T & E Express Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **i & E Express**.

    ![Egyszeri bejelentkezés konfigurálása](./media/tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a T & E Express csempére kattint, akkor kell lekérése automatikusan bejelentkezett a T & E Express alkalmazást.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/tyeexpress-tutorial/tutorial_general_203.png

