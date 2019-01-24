---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, Marketo-|} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Marketo között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: ac6d7c23c6bb107ce6920cce600a57143dafc62d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816432"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Oktatóanyag: Az Azure Active Directory-integráció, Marketo-

Ebben az oktatóanyagban elsajátíthatja, hogyan Marketo integrálása az Azure Active Directory (Azure AD).

A Marketo integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Marketo Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a marketo eszközhöz (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Marketo, a következőkre van szükség:

- Azure AD-előfizetés
- A Marketo egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Marketo hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-marketo-from-the-gallery"></a>A Marketo hozzáadása a katalógusból
Az Azure AD-be a Marketo-integráció konfigurálásához, hozzá kell Marketo a galériából a felügyelt SaaS-alkalmazások listájára.

**A Marketo hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Marketo**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/marketo-tutorial/tutorial_marketo_search.png)

1. Az eredmények panelen válassza ki a **Marketo**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálni, és a Marketo a teszt "Britta Simon." nevű felhasználó az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a Marketo tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a marketo rendszerében hivatkozás kapcsolata kell létrehozni.

Marketo, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Marketo tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A Marketo tesztfelhasználó létrehozása](#creating-a-marketo-test-user)**  –, amely kapcsolódik az Azure AD felhasználói ábrázolása a Marketo-megfelelője a Britta Simon van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Marketo-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a Marketo, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Marketo** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_samlbase.png)

1. Az a **Marketo-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://saml.marketo.com/sp`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [Marketo támogatási csapatának](http://investors.marketo.com/contactus.cfm) beolvasni ezeket az értékeket.
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_general_400.png)

1. Az a **Marketo konfigurációs** területén kattintson **konfigurálása Marketo** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_configure.png) 

1. Az alkalmazás Munchkin azonosítójának lekéréséhez, jelentkezzen be a Marketo-rendszergazda hitelesítő adataival, és hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be rendszergazdai hitelesítő adatokkal a Marketo-alkalmazást.
   
    b. Kattintson a **rendszergazdai** gombot a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Az integrációs menüben keresse meg és kattintson a **Munchkin hivatkozás**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Másolja a képernyőn megjelenő Munchkin azonosítója, és a válasz URL-cím az Azure AD konfigurációs varázsló befejezéséhez.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_12.png) 

1. Az alkalmazás az egyszeri bejelentkezés konfigurálásához kövesse az alábbi lépéseket:
   
    a. Jelentkezzen be rendszergazdai hitelesítő adatokkal a Marketo-alkalmazást.
   
    b. Kattintson a **rendszergazdai** gombot a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Az integrációs menüben keresse meg és kattintson **az egyszeri bejelentkezést**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Az SAML-beállítások engedélyezéséhez kattintson **szerkesztése** gombra.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Engedélyezett** egyszeri bejelentkezés beállításai.
   
    f. Illessze be a **SAML Entitásazonosító**, a a **kibocsátó azonosító** szövegmezőbe.
   
    g. Az a **Entitásazonosító** szövegmezőbe írja be az URL-cím `http://saml.marketo.com/sp`.
   
    h. Válassza ki a felhasználói azonosító helye legyen **Névazonosító elem**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Ha a felhasználói azonosító nem egyszerű felhasználónevet, majd módosítsa az értéket az attribútumnak lapon.
   
    i. Töltse fel a tanúsítványt, amely már letöltötte az Azure AD-konfigurációs varázsló. **Mentés** a beállításokat.
   
    j. Az átirányítási hibalapok beállításainak szerkesztése.
   
    k. Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** a a **bejelentkezési URL-cím** szövegmezőbe.
   
    l. Illessze be a **kijelentkezéses URL-cím** a a **kijelentkezési URL-címe** szövegmezőbe.
   
    m. Az a **hiba URL-cím**, másolása a **Marketo-példány URL-címe** kattintson **mentése** gombra kattintva mentse a beállításokat.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_10.png)

1. A felhasználók számára az egyszeri bejelentkezés engedélyezéséhez hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be rendszergazdai hitelesítő adatokkal a Marketo-alkalmazást.
   
    b. Kattintson a **rendszergazdai** gombot a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Keresse meg a **biztonsági** menüben, majd kattintson **bejelentkezés beállításai**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Ellenőrizze a **megkövetelése SSO** lehetőséget és **mentése** a beállításokat.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/marketo-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/marketo-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/marketo-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/marketo-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-marketo-test-user"></a>A Marketo tesztfelhasználó létrehozása

Ebben a szakaszban egy a marketo rendszerében Britta Simon nevű felhasználó hoz létre. kövesse az alábbi lépéseket egy felhasználó létrehozásához a Marketo-platformon.

1. Jelentkezzen be rendszergazdai hitelesítő adatokkal a Marketo-alkalmazást.

1. Kattintson a **rendszergazdai** gombot a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 

1. Keresse meg a **biztonsági** menüben, majd kattintson **felhasználók és szerepkörök**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_19.png)  

1. Kattintson a **új felhasználó meghívása** hivatkozásra a felhasználók lapon
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_15.png) 

1. Az új felhasználó meghívása varázslóban adja meg a következő információkat
   
    a. Adja meg a felhasználó **E-mail** címét a szövegmezőben
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Adja meg a **Utónév** be a szövegmezőbe
   
    c. Adja meg a **Vezetéknév** be a szövegmezőbe
   
    d. Kattintson a **Tovább** gombra

1. Az a **engedélyek** lapon jelölje be a **userRoles** kattintson **tovább**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_17.png)
1. Kattintson a **küldése** gombra kattintva a felhasználó meghívó küldése
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_18.png)

1. Felhasználó az e-mailben értesítést kap, és kattintson a hivatkozásra, és módosítsa a jelszót a fiók aktiválásához. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a marketo eszközhöz Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel a Marketo, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Marketo**.

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Marketo csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Marketo-alkalmazásba.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/marketo-tutorial/tutorial_general_01.png
[2]: ./media/marketo-tutorial/tutorial_general_02.png
[3]: ./media/marketo-tutorial/tutorial_general_03.png
[4]: ./media/marketo-tutorial/tutorial_general_04.png

[100]: ./media/marketo-tutorial/tutorial_general_100.png

[200]: ./media/marketo-tutorial/tutorial_general_200.png
[201]: ./media/marketo-tutorial/tutorial_general_201.png
[202]: ./media/marketo-tutorial/tutorial_general_202.png
[203]: ./media/marketo-tutorial/tutorial_general_203.png

