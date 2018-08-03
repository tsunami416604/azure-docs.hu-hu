---
title: 'Oktatóanyag: Azure Active Directory-integráció az BC a felhőben |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és BC között a felhőben.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7dc40d2c-6349-40cb-b304-b098bd03a66c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/1/2017
ms.author: jeedes
ms.openlocfilehash: 5d9d2bb0dc44eab0a419efce0c26a8f30135285e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431657"
---
# <a name="tutorial-azure-active-directory-integration-with-bc-in-the-cloud"></a>Oktatóanyag: Azure Active Directory-integráció az BC a felhőben

Ebben az oktatóanyagban elsajátíthatja, hogyan BC integrálása az Azure Active Directoryval (Azure AD) a felhőben.

BC integrálása az Azure ad-vel a felhőben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá BC a felhőben az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett, BC a felhőben az Azure AD-fiókjukat (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

BC az Azure AD-integráció konfigurálása a felhőben, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy BC az a felhő egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. BC hozzáadása a katalógusból a felhőben
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-bc-in-the-cloud-from-the-gallery"></a>BC hozzáadása a katalógusból a felhőben
BC integrációjának konfigurálása az Azure AD-be a felhőben, hozzá kell BC a felhőben a galériából a felügyelt SaaS-alkalmazások listájára.

**A felhőben a katalógusból BC hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **felhőbeli BC**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/bcinthecloud-tutorial/tutorial_bcinthecloud_search.png)

1. Az eredmények panelen válassza ki a **felhőbeli BC**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/bcinthecloud-tutorial/tutorial_bcinthecloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés BC-a felhőben, egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a felhőben lévő BC tartozó felhasználó Mi az a felhasználó Azure AD-ben. Más szóval Azure AD-felhasználót és a felhőben lévő BC kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

BC a felhőben, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

A felhőben az Azure AD egyszeri bejelentkezés az BC tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A felhő tesztfelhasználó létrehozása egy BC](#creating-a-bc-in-the-cloud-test-user)**  – BC-megfelelője a Britta Simon van a felhőben, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a BC a felhőalkalmazásba az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés BC a felhőben, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **BC a felhőben** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/bcinthecloud-tutorial/tutorial_bcinthecloud_samlbase.png)

1. Az a **a felhő tartomány és URL-címek BC** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/bcinthecloud-tutorial/tutorial_bcinthecloud_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://app.bcinthecloud.com/router/loginSaml/<customerid>`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-cím: `https://app.bcinthecloud.com`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [BC, felhőalapú ügyfél-támogatási csoportjának](https://www.bcinthecloud.com/supportcenter/) lekérni ezt az értéket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/bcinthecloud-tutorial/tutorial_bcinthecloud_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/bcinthecloud-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **BC a felhőben** oldalon kell küldenie a letöltött **metaadatainak XML** való [BC felhőbeli támogatási csoportjának](https://www.bcinthecloud.com/supportcenter/).

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/bcinthecloud-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/bcinthecloud-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/bcinthecloud-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/bcinthecloud-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-bc-in-the-cloud-test-user"></a>Egy BC a felhő tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű BC a felhőbeli felhasználó hoz létre. Együttműködve [BC, felhőalapú ügyfél-támogatási csoportjának](https://www.bcinthecloud.com/supportcenter/) felhasználót is hozzáadhat a a BC a felhőalapú alkalmazásokban. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BC a felhőben Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel BC a felhőben, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **felhőbeli BC**.

    ![Egyszeri bejelentkezés konfigurálása](./media/bcinthecloud-tutorial/tutorial_bcinthecloud_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

 Ha rákattint a BC a felhő csempén a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett a BC a felhőalapú alkalmazásokban való. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bcinthecloud-tutorial/tutorial_general_01.png
[2]: ./media/bcinthecloud-tutorial/tutorial_general_02.png
[3]: ./media/bcinthecloud-tutorial/tutorial_general_03.png
[4]: ./media/bcinthecloud-tutorial/tutorial_general_04.png

[100]: ./media/bcinthecloud-tutorial/tutorial_general_100.png

[200]: ./media/bcinthecloud-tutorial/tutorial_general_200.png
[201]: ./media/bcinthecloud-tutorial/tutorial_general_201.png
[202]: ./media/bcinthecloud-tutorial/tutorial_general_202.png
[203]: ./media/bcinthecloud-tutorial/tutorial_general_203.png

