---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező O.C. Péter - AppreciateHub |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálása egyszeri bejelentkezéshez O.C. és az Azure Active Directory között Péter - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 3a9b11b5d5e70e72a90683d4952b7d34f0303960
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163602"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező O.C. Péter - AppreciateHub

Ebben az oktatóanyagban megismerheti, hogyan integrálható a O.C. Péter - AppreciateHub az Azure Active Directory (Azure AD).

O.C. integrálása Péter – az Azure ad-vel AppreciateHub nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá O.C. Azure AD-ben Péter - AppreciateHub
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a O.C. Péter - AppreciateHub (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az O.C. Péter - AppreciateHub, akkor a következők szükségesek:

- Azure AD-előfizetés
- EGY O.C. Péter - AppreciateHub egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. O.C. hozzáadása Péter - AppreciateHub a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>O.C. hozzáadása Péter - AppreciateHub a katalógusból
O.C. integrációjának konfigurálása Péter - AppreciateHub az Azure AD-be, hozzá kell adnia O.C. Péter - AppreciateHub a galériából a felügyelt SaaS-alkalmazások listájára.

**O.C. hozzáadása Péter - AppreciateHub katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **O.C. Péter - AppreciateHub**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

1. Az eredmények panelen válassza ki a **O.C. Péter - AppreciateHub**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az O.C. Péter - AppreciateHub "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, milyen a partner felhasználó O.C. Péter - AppreciateHub, hogy egy felhasználó Azure AD-ben. Más szóval a hivatkozás kapcsolatot Azure AD-felhasználót és a kapcsolódó felhasználó O.C. között Péter - AppreciateHub kell létrehozni.

A O.C. Péter - AppreciateHub, az értéket a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az O.C. tesztelése és konfigurálása Péter - AppreciateHub, át kell adnia a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy O.C. létrehozása Péter - AppreciateHub tesztfelhasználó](#creating-a-oc-tanner---appreciatehub-test-user)**  – egy megfelelője a Britta Simon O.C. van Péter - AppreciateHub, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a O.C. az egyszeri bejelentkezés konfigurálása Péter - AppreciateHub alkalmazás.

**O.C. az Azure AD egyszeri bejelentkezés konfigurálása Péter - AppreciateHub, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **O.C. Péter - AppreciateHub** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

1. Az a **O.C. Péter - AppreciateHub tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges válasz URL-cím. Kapcsolattartó [O.C. Péter - AppreciateHub támogatási csapatának](mailto:sso@octanner.com) lekérni ezt az értéket.

    b. Nyissa meg a metaadat-fájlt a következő hivatkozás használatával: [ https://fed.appreciatehub.com/fed/sp/metadata ](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Keresse meg a **md:AssertionConsumerService** csomópont. 
   
    d. Másolja az értéket, a **hely** attribútum. 
   
    ![Alkalmazásbeállítások konfigurálása][12]
   
    e. Az a **bejelentkezési URL-** szövegmező, az érték az előző lépésben beszerzett múltbeli.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/oc-tanner-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **O.C. Péter - AppreciateHub** oldalon kell küldenie a letöltött **metaadatainak XML** való [O.C. Péter - AppreciateHub támogatási csapatának](mailto:sso@octanner.com).

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/oc-tanner-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/oc-tanner-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/oc-tanner-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/oc-tanner-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Egy O.C. létrehozása Péter - AppreciateHub tesztfelhasználó számára

Ez a szakasz célja az, hogy O.C. Britta Simon nevű felhasználó létrehozása Péter - AppreciateHub.

**Britta Simon O.C. nevű felhasználó létrehozása Péter - AppreciateHub, hajtsa végre az alábbi lépéseket:**

Kérje meg a [O.C. Péter - AppreciateHub támogatási csapatának](mailto:sso@octanner.com) , amely rendelkezik nameID attribútum Britta Simon felhasználóneve megegyezik az Azure AD-felhasználó létrehozásához.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés O.C. Azure egyszeri bejelentkezés használatára Péter - AppreciateHub.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése O.C. Péter - AppreciateHub, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **O.C. Péter - AppreciateHub**.

    ![Egyszeri bejelentkezés konfigurálása](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.  
Amikor rákattint a O.C. Péter - AppreciateHub csempe a hozzáférési panelen azt kell lekérése automatikusan bejelentkezett, a O.C. Péter - AppreciateHub alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/oc-tanner-tutorial/tutorial_general_203.png

