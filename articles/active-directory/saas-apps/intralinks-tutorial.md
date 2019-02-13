---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Intralinks |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Intralinks között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41aac9c64922a3d551ae685bb5b642bf72dd52b2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166223"
---
# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Intralinks

Ebben az oktatóanyagban elsajátíthatja, hogyan Intralinks integrálása az Azure Active Directory (Azure AD).

Intralinks integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Intralinks Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Intralinks (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Intralinks az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Intralinks egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Intralinks hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-intralinks-from-the-gallery"></a>Intralinks hozzáadása a katalógusból
Az Azure AD integrálása a Intralinks konfigurálásához hozzá kell Intralinks a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Intralinks hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Intralinks**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intralinks-tutorial/tutorial_intralinks_search.png)

1. Az eredmények panelen válassza ki a **Intralinks**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intralinks-tutorial/tutorial_intralinks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Intralinks a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Intralinks mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Intralinks hivatkozás kapcsolata kell létrehozni.

Intralinks, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Intralinks tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy Intralinks tesztfelhasználó létrehozása](#creating-an-intralinks-test-user)**  – egy megfelelője a Britta Simon Intralinks, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Intralinks alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Intralinks, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Intralinks** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/intralinks-tutorial/tutorial_intralinks_samlbase.png)

1. Az a **Intralinks tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/intralinks-tutorial/tutorial_intralinks_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:  `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Intralinks ügyfél-támogatási csapatának](https://www.intralinks.com/contact) lekérni ezt az értéket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/intralinks-tutorial/tutorial_intralinks_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/intralinks-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **Intralinks** oldalon kell küldenie a letöltött **metaadatainak XML** [Intralinks támogatási csoportjának](https://www.intralinks.com/contact). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intralinks-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intralinks-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intralinks-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intralinks-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-intralinks-test-user"></a>Egy Intralinks tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Intralinks hoz létre. Együttműködve [Intralinks támogatási csoportjának](https://www.intralinks.com/contact) a felhasználók hozzáadása az Intralinks platformon.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Intralinks Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Intralinks, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Intralinks**.

    ![Egyszeri bejelentkezés konfigurálása](./media/intralinks-tutorial/tutorial_intralinks_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="add-intralinks-via-or-elite-application"></a>Intralinks keresztül vagy Elite alkalmazás hozzáadása

Intralinks kivéve ügylet Nexus alkalmazás Intralinks alkalmazására azonos SSO identity platformot használ. Így ha tervezi használni minden olyan Intralinks alkalmazást majd először kell egy elsődleges Intralinks alkalmazáshoz a fent ismertetett eljárással egyszeri bejelentkezés konfigurálása.

Ezt követően, kövesse az alábbi eljárást követve egy másik Intralinks alkalmazás hozzáadása a bérlőben, amelyek kihasználhatják az elsődleges alkalmazás egyszeri bejelentkezéshez. 

>[!NOTE]
>Ez a funkció csak az Azure AD Premium SKU-ügyfelek számára érhető el, és nem érhető el az ingyenes vagy alapszintű Termékváltozat ügyfelek.

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]


1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Intralinks**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intralinks-tutorial/tutorial_intralinks_search.png)

1. A **alkalmazás Intralinks hozzáadása** hajtsa végre az alábbi lépéseket:

    ![Intralinks keresztül vagy Elite alkalmazás hozzáadása](./media/intralinks-tutorial/tutorial_intralinks_addapp.png)

    a. A **neve** szövegmezőbe írja be a megfelelő az alkalmazás neve, pl. **Intralinks Elite**.

    b. Kattintson a **Hozzáadás** gombra.

1.  Az Azure Portalon az a **Intralinks** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **csatolt bejelentkezés**.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/intralinks-tutorial/tutorial_intralinks_linkedsignon.png)

1. Az SP által kezdeményezett egyszeri bejelentkezési URL-címet első [Intralinks csapat](https://www.intralinks.com/contact) a másik Intralinks alkalmazás írja be a **bejelentkezési URL-cím konfigurálása** alább látható módon. 
    
     ![Egyszeri bejelentkezés konfigurálása](./media/intralinks-tutorial/tutorial_intralinks_customappurl.png)
    
     A bejelentkezési az URL-címe szövegmezőbe írja be a bejelentkezéshez, a következő minta használatával Intralinks alkalmazását a felhasználók által használt URL-cím:
   
    `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/intralinks-tutorial/tutorial_general_400.png)

1. Az alkalmazás felhasználókhoz vagy csoportokhoz hozzárendelni, ahogyan az a szakasz  **[hozzárendelése az Azure ad-ben tesztfelhasználó](#assigning-the-azure-ad-test-user)**.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Intralinks csempére kattint, meg kell lekérése automatikusan bejelentkezett az Intralinks alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/intralinks-tutorial/tutorial_general_01.png
[2]: ./media/intralinks-tutorial/tutorial_general_02.png
[3]: ./media/intralinks-tutorial/tutorial_general_03.png
[4]: ./media/intralinks-tutorial/tutorial_general_04.png

[100]: ./media/intralinks-tutorial/tutorial_general_100.png

[200]: ./media/intralinks-tutorial/tutorial_general_200.png
[201]: ./media/intralinks-tutorial/tutorial_general_201.png
[202]: ./media/intralinks-tutorial/tutorial_general_202.png
[203]: ./media/intralinks-tutorial/tutorial_general_203.png

