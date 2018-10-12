---
title: 'Oktatóanyag: Azure Active Directory-integráció az Velpic SAML |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Velpic SAML között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 2a8fe814801d00b3f27e52484d742cbe6c7b0770
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114006"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Oktatóanyag: Azure Active Directory-integráció az Velpic SAML

Ebben az oktatóanyagban elsajátíthatja, hogyan Velpic SAML integrálható az Azure Active Directory (Azure AD).

Velpic SAML integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Velpic SAML Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Velpic SAML (egyszeri bejelentkezés)
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Velpic SAML, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Velpic SAML egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Velpic SAML hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-velpic-saml-from-the-gallery"></a>Velpic SAML hozzáadása a katalógusból
Az Azure AD integrálása a Velpic SAML konfigurálása, hozzá kell Velpic SAML a galériából a felügyelt SaaS-alkalmazások listájára.

**Velpic SAML hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Velpic SAML**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/velpicsaml-tutorial/tutorial_velpicsaml_search.png)

1. Az eredmények panelen válassza ki a **Velpic SAML**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és az Azure AD egyszeri bejelentkezés tesztelése az Velpic SAML "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó SAML-Velpic mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Velpic SAML-hivatkozás kapcsolatát kell létrehozni.

A hivatkozás kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** Velpic SAML.

Az Azure AD egyszeri bejelentkezés az Velpic SAML tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Velpic SAML tesztfelhasználó létrehozása](#creating-a-velpic-saml-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Velpic SAML-, amely kapcsolódik az Azure ad-ben ábrázolása számára.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon, és az alkalmazásában Velpic SAML egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az Velpic SAML, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **Velpic SAML** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

1. Adja meg a részleteket a **Velpic SAML-tartomány és URL-címek** szakasz –

    ![Egyszeri bejelentkezés konfigurálása](./media/velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be az értéket, mint: `https://<sub-domain>.velpicsaml.net`

    b. Az a **azonosító** szövegmezőjébe illessze be a **"Egyszeri bejelentkezési URL-címe"** érték `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Vegye figyelembe, hogy a bejelentkezési URL-cím a Velpic SAML-csapat által megadott és azonosító értéket Velpic SAML oldalán az SSO beépülő modul konfigurálásakor lesz elérhető. Az érték másolásához Velpic SAML-kérelem oldaláról, és illessze be ide kell.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/velpicsaml-tutorial/tutorial_general_400.png)

1. A SAML-konfigurációja Velpic területen kattintson a Velpic SAML konfigurálása konfigurálása bejelentkezési ablak megnyitásához. Másolja a gyors útmutató szakaszban az SAML-entitás azonosítója.

1. Egy másik böngészőablakban jelentkezzen be a Velpic SAML vállalati hely rendszergazdaként.

1. Kattintson a **kezelés** lapra, és nyissa meg **integrációs** szakaszban kattintson a kell **beépülő modulok** gombra kattintva hozzon létre új beépülő modult a bejelentkezéshez.

    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_1.png)

1. Kattintson a **"Beépülő modul hozzáadása"** gombra.
    
    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_2.png)

1. Kattintson a **SAML** csempére a beépülő modul hozzáadása oldalon.
    
    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_3.png)

1. Adja meg az új SAML beépülő modul nevét, majd kattintson a **"Hozzáadás"** gombra.

    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_4.png)

1. Adja meg a részleteket a következők szerint:

    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_5.png)

    a. Az a **neve** szövegmezőbe írja be a SAML beépülő modul nevét.

    b. A a **kiállítójának URL-címe** szövegmezőjébe illessze be a **SAML Entitásazonosító** , átmásolja a **bejelentkezés konfigurálása** ablakot, az Azure Portalon.

    c. Az a **szolgáltató metaadatok Config** töltse fel a metaadatainak XML-fájlt, amely az Azure Portalról letöltött.

    d. Azt is beállíthatja az SAML engedélyezése csak az idő-kiépítés engedélyezése a **"Automatikus létrehozása az új felhasználók"** jelölőnégyzetet. Ha egy felhasználó Velpic nem létezik, és ez a jelző nincs engedélyezve, az Azure-ból nem fog tudni bejelentkezni. Ha a jelző engedélyezve van a felhasználó automatikusan kiépítheti Velpic történő bejelentkezés alkalmával. 

    e. Másolás a **egyszeri bejelentkezési URL** szövegből mezőbe, és illessze be az Azure Portalon.
    
    f. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/velpicsaml-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/velpicsaml-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/velpicsaml-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/velpicsaml-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-velpic-saml-test-user"></a>Velpic SAML tesztfelhasználó létrehozása

Ezt a lépést általában nem kötelező, csak az idő felhasználókiépítés az alkalmazás támogatja. Ha nincs engedélyezve a felhasználók automatikus átadása majd a felhasználó manuális létrehozása hajtható végre az alább ismertetett.

Jelentkezzen be rendszergazdaként vállalati Velpic SAML webhelyét, és hajtsa végre a következő lépéseket:
    
1. Kattintson a kezelés lapon, majd nyissa meg a felhasználók szakaszban kattintson az új felhasználók hozzáadása gombra.

    ![Felhasználó hozzáadása](./media/velpicsaml-tutorial/velpic_7.png)

1. Az a **"Az új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel.

    ![Felhasználó](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Az a **Utónév** szövegmezőbe írja be az első Britta Simon nevét.

    b. Az a **Vezetéknév** szövegmezőbe írja be a Britta Simon vezetékneve.

    c. Az a **felhasználónév** szövegmezőbe írja be a felhasználónevet Britta Simon.

    d. Az a **E-mail** szövegmezőbe írja be a Britta Simon fiók e-mail-címét.

    e. Az adatok a REST használata nem kötelező, szükség esetén kitöltheti.
    
    f. Kattintson a **SAVE** (Mentés) gombra.  

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon számára a hozzáférés biztosításával az Velpic SAML Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Velpic SAML, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Velpic SAML**.

    ![Egyszeri bejelentkezés konfigurálása](./media/velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

1. Ha a hozzáférési panelen a Velpic SAML csempére kattint, bejelentkezési oldal Velpic SAML-kérelem szerezheti be. Megtekintheti a **"Bejelentkezés az Azure ad-ben"** gomb a bejelentkezési oldalon.

    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_6.png)

1. Kattintson a **"Bejelentkezés az Azure ad-ben"** gombra kattintva jelentkezzen be az Azure AD-fiókot használó Velpic.


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/velpicsaml-tutorial/tutorial_general_203.png

