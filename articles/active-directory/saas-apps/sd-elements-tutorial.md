---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SD-elemek |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az SD-elemek közötti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 93f838ac846415de0f3caf14b36f4bdf36b241ee
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814800"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Oktatóanyag: Az Azure Active Directory integrációja az SD-elemek

Ebben az oktatóanyagban elsajátíthatja, hogyan SD elemek integrálása az Azure Active Directory (Azure AD).

SD elemek integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá az SD-elemek az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SD elemek (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az SD-elemek, a következőkre van szükség:

- Azure AD-előfizetés
- Egy SD-elemek az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SD-elemek hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sd-elements-from-the-gallery"></a>SD-elemek hozzáadása a katalógusból
SD elemek integrálása az Azure AD beállításához szüksége SD-elemek hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**SD-elemek hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **SD elemek**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sd-elements-tutorial/tutorial_sdelements_search.png)

1. Az eredmények panelen válassza ki a **SD elemek**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sd-elements-tutorial/tutorial_sdelements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés SD elemeket a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó, az SD-elemeket mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó, az SD-elemeket hivatkozás kapcsolata kell létrehozni.

SD-elemek rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálása és az Azure AD egyszeri bejelentkezés az SD-elemek teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[SD elemek tesztfelhasználó létrehozása](#creating-a-sd-elements-test-user)**  - a-megfelelője a Britta Simon rendelkezik az SD-elemeket, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az SD-elemek alkalmazás egyszeri bejelentkezés konfigurálása.

**Az SD-elemek konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SD elemek** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sdelements_samlbase.png)

1. Az a **SD elemek tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sdelements_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [SD elemek támogatási csoportjának](mailto:support@sdelements.com) beolvasni ezeket az értékeket.

1. SD elemek alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **"Felhasználói attribútum"** az alkalmazás lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sdelements_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket: 

    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | e-mail |user.mail |
    | Keresztnév |user.givenname |
    | Vezetéknév |user.surname |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_officespace_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_officespace_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Kattintson az **OK** gombra.
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sdelements_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_general_400.png)

1. Az a **SD elemek konfigurációs** területén kattintson **SD-elemek konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sdelements_configure.png)

1. Első egyszeri bejelentkezés engedélyezve van, lépjen kapcsolatba a [SD elemek támogatási csoportjának](mailto:support@sdelements.com) és adja meg a letöltött tanúsítvány-fájlját. 

1. Egy másik böngészőablakban bejelentkezés rendszergazdaként az SD-elemek bérlőhöz.

1. A felső menüben kattintson **rendszer**, majd **egyszeri bejelentkezés**. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sd-elements_09.png) 

1. Az a **egyszeri bejelentkezési beállításainak** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. Mint **egyszeri bejelentkezési típus**válassza **SAML**.
   
    b. Az a **Identity Provider Entitásazonosító** szövegmezőbe, illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt. 
   
    c. Az a **Identity Provider egyszeri bejelentkezési szolgáltatás** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt. 
   
    d. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sd-elements-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sd-elements-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sd-elements-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sd-elements-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sd-elements-test-user"></a>SD elemek tesztfelhasználó létrehozása

Ez a szakasz célja az SD-elemeket Britta Simon nevű felhasználó létrehozásához. SD-elemek esetén SD elemek felhasználók létrehozása a manuális feladat.

**Az SD-elemeket Britta Simon létrehozásához hajtsa végre az alábbi lépéseket:**

1. Egy böngészőablakban bejelentkezést az SD-elemek vállalat webhelye rendszergazdaként.

1. A felső menüben kattintson **felhasználókezelés**, majd **felhasználók**.
   
    ![SD elemek tesztfelhasználó létrehozása](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Kattintson a **új felhasználó hozzáadása**.
   
    ![SD elemek tesztfelhasználó létrehozása](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)
 
1. Az a **új felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![SD elemek tesztfelhasználó létrehozása](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
    a. Az a **E-mail** szövegmezőben adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.
   
    b. Az a **Utónév** szövegmezőbe írja be például a felhasználó utónevét **Britta**.
   
    c. Az a **Vezetéknév** szövegmezőbe írja be például a felhasználó vezetékneve **Simon**.
   
    d. Mint **szerepkör**válassza **felhasználói**. 
   
    e. Kattintson a **felhasználó létrehozása**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SD elemek Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel SD elemeket, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SD elemek**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sdelements_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.
  
Ha a hozzáférési panelen az SD-elemek csempére kattint, meg kell lekérése automatikusan bejelentkezett az SD-elemek alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/sd-elements-tutorial/tutorial_general_04.png

[100]: ./media/sd-elements-tutorial/tutorial_general_100.png

[200]: ./media/sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/sd-elements-tutorial/tutorial_general_201.png
[202]: ./media/sd-elements-tutorial/tutorial_general_202.png
[203]: ./media/sd-elements-tutorial/tutorial_general_203.png

