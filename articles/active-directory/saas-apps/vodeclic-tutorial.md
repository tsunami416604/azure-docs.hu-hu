---
title: 'Oktatóanyag: Azure Active Directory-integráció az Vodeclic |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Vodeclic között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: fb985b389139bfd8d54e6c54d101bbfa8a68a6d4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444623"
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Oktatóanyag: Azure Active Directory-integráció az Vodeclic

Ebben az oktatóanyagban elsajátíthatja, hogyan Vodeclic integrálása az Azure Active Directory (Azure AD).

Vodeclic integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Vodeclic Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezve Vodeclic (egyszeri bejelentkezés, vagy az SSO) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Vodeclic az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Előfizetés Vodeclic egyszeri bejelentkezés engedélyezve

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure AD-próba környezetet [egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Vodeclic hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-vodeclic-from-the-gallery"></a>Vodeclic hozzáadása a katalógusból
Az Azure AD integrálása a Vodeclic konfigurálásához hozzá kell Vodeclic a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A katalógusból Vodeclic hozzáadásához tegye a következőket:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Lépjen a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Vodeclic**. Válassza ki **Vodeclic** az eredmények panelen, és válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Vodeclic](./media/vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Vodeclic az Azure AD egyszeri bejelentkezés tesztelése

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, akik a Vodeclic megfelelőjére felhasználót, hogy egy felhasználó Azure AD-ben. Más szóval kell Vodeclic az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolatot létesít.

Vodeclic, adjon az érték **felhasználónév** azonos értéket **felhasználónév** az Azure ad-ben. Most hozott létre a kapcsolatot a két olyan felhasználó között.

Az Azure AD egyszeri bejelentkezés az Vodeclic tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. [Hozzon létre egy Vodeclic tesztfelhasználót](#create-a-vodeclic-test-user) egy megfelelője a Britta Simon Vodeclic, amely a felhasználó Azure ad-ben reprezentációja van csatolva van.
1. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Vodeclic alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Vodeclic, tegye a következőket:**

1. Az Azure Portalon az a **Vodeclic** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanel **Single-Sign-on mód**, jelölje be **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

1. Ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, a **Vodeclic tartomány és URL-címek** területén az alábbi lépéseket:

    ![Vodeclic tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. Az a **azonosító** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. Az a **válasz URL-cím** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

1. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett módot, jelölje be a **speciális URL-beállítások megjelenítése** jelölőnégyzetet, majd hajtsa végre az alábbi lépést:

    ![Vodeclic tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/vodeclic-tutorial/tutorial_vodeclic_url1.png)

    Az a **bejelentkezési URL-** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Ezek az értékek nem valódi. Az értékeket módosítsa a tényleges azonosítóval, válasz URL-cím és bejelentkezés URL-címe. Forduljon a [Vodeclic ügyfél-támogatási csapatának](mailto:hotline@vodeclic.com) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **metaadatainak XML**. Mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

1. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/vodeclic-tutorial/tutorial_general_400.png)
    
1. Az egyszeri bejelentkezés konfigurálásához a **Vodeclic** oldalán, a letöltött küldése **metaadatainak XML** , a [Vodeclic támogatási csapatával](mailto:hotline@vodeclic.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com) közben állítja be az alkalmazás. Ez az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez dokumentáció a **konfigurációs** alul található szakaszában. További tudnivalók a beágyazott dokumentáció funkció [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/vodeclic-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**. Válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/vodeclic-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/vodeclic-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/vodeclic-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-vodeclic-test-user"></a>Vodeclic tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Vodeclic hoz létre. Együttműködik a [Vodeclic támogatási csapatának](mailto:hotline@vodeclic.com) a felhasználók hozzáadása az Vodeclic platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

> [!NOTE]
> Alkalmazás követelményeinek megfelelően szüksége lehet beolvasni a gép szerepel az engedélyezési listán. Az, hogy megtörténjen, meg kell osztania a nyilvános IP-címet a [Vodeclic támogatási csapatának](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Vodeclic Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Vodeclic, tegye a következőket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és keresse meg a könyvtár nézetben. Folytassa **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Vodeclic**.

    ![Az alkalmazások listáját a Vodeclic hivatkozásra](./media/vodeclic-tutorial/tutorial_vodeclic_app.png)  

1. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

1. Az a **felhasználók és csoportok** párbeszédpanelen válassza ki a **kiválasztása** gombra.

1. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

A Vodeclic csempe kiválasztásakor a hozzáférési panelen, első automatikusan bejelentkezett Vodeclic alkalmazását.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/vodeclic-tutorial/tutorial_general_203.png

