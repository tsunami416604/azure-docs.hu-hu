---
title: 'Oktatóanyag: A kép Relay Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a lemezkép továbbítási között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 0671d2f5ad4be34926c8e3d2b22711c4af5fd435
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812097"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Oktatóanyag: A kép Relay Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan kép Relay integrálása az Azure Active Directory (Azure AD).

Kép Relay integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a lemezkép Relay Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a lemezkép Relay (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a lemezkép Relay, a következőkre van szükség:

- Azure AD-előfizetés
- Egy rendszerkép Relay egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Kép Relay hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-image-relay-from-the-gallery"></a>Kép Relay hozzáadása a katalógusból
Kép Relay integrálása az Azure AD beállítása, hozzá kell kép Relay a galériából a felügyelt SaaS-alkalmazások listájára.

**Kép Relay hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **kép Relay**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/imagerelay-tutorial/tutorial_imagerelay_search.png)

1. Az eredmények panelen válassza ki a **kép Relay**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálni, és a lemezkép Relay a teszt "Britta Simon" nevű felhasználó az Azure AD egyszeri bejelentkezés teszteléséhez.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a lemezkép Relay tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználói lemezkép Relay a hivatkozás kapcsolata kell létrehozni.

Kép Relay, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a lemezkép Relay tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy rendszerkép Relay tesztfelhasználó létrehozása](#creating-an-image-relay-test-user)**  – a felhasználó Azure ad-ben reprezentációja csatolt kép Relay-megfelelője a Britta Simon rendelkezik.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a lemezkép Relay alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a lemezkép Relay, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **kép Relay** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

1. Az a **kép Relay tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.imagerelay.com/`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [kép Relay ügyfél-támogatási csapatának](http://support.imagerelay.com/) beolvasni ezeket az értékeket. 
 


1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_general_400.png)

1. Az a **rendszerkép továbbítóügynök-konfiguráció** területén kattintson **konfigurálása kép Relay** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses szolgáltatás URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a rendszerkép Relay vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson a **felhasználók és engedélyek** számítási feladatot.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Kattintson a **hozzon létre új engedély**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

1. Az a **egyszeri bejelentkezést a beállítások** feladatnál válassza a **csak jelentkezzen be az egyszeri bejelentkezést keresztül lehet ennek a csoportnak a** jelölőnégyzetet, majd kattintson a **mentése**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Lépjen a **Fiókbeállítások**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Nyissa meg a **egyszeri bejelentkezést a beállítások** számítási feladatot.
    
     ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

1. Az a **SAML-beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. A **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    b. A **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **egyszeri kijelentkezéses szolgáltatás URL-cím** Azure Portalról másolt.

    c. Mint **Névazonosító formátuma**válassza **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: emailAddress**.

    d. Mint **beállítások kötelező a szolgáltató (kép továbbítóként) érkező kéréseket**, jelölje be **POST kötés**.

    e. A **x.509 tanúsítvány**, kattintson a **tanúsítvány frissítése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. A Jegyzettömb alkalmazásban nyissa meg a letöltött tanúsítvány, másolja a tartalmat, és illessze be az x.509-tanúsítvány szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. A **engedélyezték Felhasználókiépítés** szakaszban jelölje be a **engedélyezték Felhasználókiépítés engedélyezése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Válassza ki az engedélycsoporthoz tartozik (például **SSO alapszintű**) ez engedélyezett, csak a keresztül egyszeri bejelentkezéshez.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/imagerelay-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/imagerelay-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/imagerelay-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/imagerelay-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-image-relay-test-user"></a>Egy rendszerkép Relay tesztfelhasználó létrehozása

Ez a szakasz célja egy kép Relay Britta Simon nevű felhasználó létrehozásához.

**Kép Relay Britta Simon nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a lemezkép Relay vállalati webhelyre rendszergazdaként.

1. Lépjen a **felhasználók és engedélyek** válassza **SSO-felhasználó létrehozása**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Adja meg a **E-mail**, **Utónév**, **Vezetéknév**, és **vállalati** kiépítése, és válassza ki az engedélycsoporthoz tartozik (a felhasználó egyszeri bejelentkezés alapszintű példa) Ez a csoport, amely keresztül bejelentkezni képes csak egyszeri bejelentkezést.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon hozzáférésének engedélyezésére lemezkép Relay által használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel kép Relay, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **kép Relay**.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.    

Ha a hozzáférési panelen a lemezkép Relay csempére kattint, meg kell beolvasása automatikusan bejelentkezett a lemezkép Relay alkalmazásba.


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/imagerelay-tutorial/tutorial_general_203.png

