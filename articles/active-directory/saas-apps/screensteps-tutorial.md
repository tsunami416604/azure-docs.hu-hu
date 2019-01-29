---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ScreenSteps |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és ScreenSteps között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 820446ba30e8743bc80b46fbebf60408c21ea43d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164724"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ScreenSteps

Ebben az oktatóanyagban elsajátíthatja, hogyan ScreenSteps integrálása az Azure Active Directory (Azure AD).

ScreenSteps integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá ScreenSteps Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett ScreenSteps (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

ScreenSteps az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy ScreenSteps egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. ScreenSteps hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-screensteps-from-the-gallery"></a>ScreenSteps hozzáadása a katalógusból
Az Azure AD integrálása a ScreenSteps konfigurálásához hozzá kell ScreenSteps a katalógusból a felügyelt SaaS-alkalmazások listájára.

**ScreenSteps hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **ScreenSteps**válassza **ScreenSteps** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában ScreenSteps](./media/screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés ScreenSteps a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó ScreenSteps mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó ScreenSteps hivatkozás kapcsolata kell létrehozni.

ScreenSteps, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az ScreenSteps tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy ScreenSteps tesztfelhasználót](#create-a-screensteps-test-user)**  – egy megfelelője a Britta Simon ScreenSteps, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és ScreenSteps alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ScreenSteps, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **ScreenSteps** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/screensteps-tutorial/tutorial_screensteps_samlbase.png)

1. Az a **ScreenSteps tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![ScreenSteps tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/screensteps-tutorial/tutorial_screensteps_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Az érték nem valódi. Frissítse a tényleges bejelentkezési URL-cím, az oktatóanyag későbbi részében ismertetett ezt az értéket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/screensteps-tutorial/tutorial_screensteps_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/screensteps-tutorial/tutorial_general_400.png)

1. Az a **ScreenSteps konfigurációs** területén kattintson **konfigurálása ScreenSteps** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![ScreenSteps konfiguráció](./media/screensteps-tutorial/tutorial_screensteps_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a ScreenSteps vállalati hely rendszergazdaként.

1. Kattintson a **Fiókbeállítások**.

    ![Fiókkezelés](./media/screensteps-tutorial/ic778523.png "fiókkezelés")

1. Kattintson a **egyszeri bejelentkezési**.

    ![Távoli hitelesítés](./media/screensteps-tutorial/ic778524.png "távoli hitelesítés")

1. Kattintson a **egyszeri bejelentkezési végpont létrehozása**.

    ![Távoli hitelesítés](./media/screensteps-tutorial/ic778525.png "távoli hitelesítés")

1. Az a **létrehozása egyszeri bejelentkezési végpont** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Hozzon létre egy hitelesítési végpont](./media/screensteps-tutorial/ic778526.png "hitelesítési végpont létrehozása")
    
    a. Az a **cím** szövegmezőbe, a címet írja be.
    
    b. Az a **mód** listáról válassza ki **SAML**.
    
    c. Kattintson a **Create** (Létrehozás) gombra.

1. **Szerkesztés** az új végpont.

    ![Végpont szerkesztése](./media/screensteps-tutorial/ic778528.png "végpont szerkesztése")

1. Az a **szerkesztése egyszeri bejelentkezési végpont** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Távoli hitelesítési végpontja](./media/screensteps-tutorial/ic778527.png "távoli hitelesítési végpontja")

    a. Kattintson a **új SAML-tanúsítvány fájlfeltöltés**, majd töltse fel a tanúsítványt, amely már letöltötte az Azure Portalról.
    
    b. Beillesztés **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról történő másolta a **távoli bejelentkezési URL-cím** szövegmezőbe.
    
    c. Beillesztés **kijelentkezéses URL-cím** érték, amely az Azure Portalról történő másolta a **kijelentkezési URL-cím** szövegmezőbe.
    
    d. Válassza ki a **csoport** kiépítésüket amikor a felhasználók hozzárendelése.
    
    e. Kattintson az **Update** (Frissítés) elemre.

    f. Másolás a **SAML ügyfél URL-címe** a vágólapra, és illessze be a a **bejelentkezési URL-** szövegmezőjébe **ScreenSteps tartomány és URL-címek** szakaszban.
    
    g. Lépjen vissza a **egyszeri bejelentkezési végpont szerkesztése**.
    
    h. Kattintson a **alapértelmezett fiók** ezt a végpontot használja az összes felhasználó számára ScreenSteps bejelentkezés gombot. Másik lehetőségként kattinthat a **helynek** gombra kattintva ezt a végpontot használja az adott webhelyekhez **ScreenSteps**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/screensteps-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/screensteps-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/screensteps-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/screensteps-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-screensteps-test-user"></a>ScreenSteps tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű ScreenSteps hoz létre. Együttműködve [ScreenSteps ügyfél-támogatási csapatának](https://www.screensteps.com/contact) a felhasználók hozzáadása az ScreenSteps platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ScreenSteps Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel ScreenSteps, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **ScreenSteps**.

    ![Az alkalmazások listáját a ScreenSteps hivatkozásra](./media/screensteps-tutorial/tutorial_screensteps_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ScreenSteps csempére kattint, meg kell lekérése automatikusan bejelentkezett az ScreenSteps alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/screensteps-tutorial/tutorial_general_01.png
[2]: ./media/screensteps-tutorial/tutorial_general_02.png
[3]: ./media/screensteps-tutorial/tutorial_general_03.png
[4]: ./media/screensteps-tutorial/tutorial_general_04.png

[100]: ./media/screensteps-tutorial/tutorial_general_100.png

[200]: ./media/screensteps-tutorial/tutorial_general_200.png
[201]: ./media/screensteps-tutorial/tutorial_general_201.png
[202]: ./media/screensteps-tutorial/tutorial_general_202.png
[203]: ./media/screensteps-tutorial/tutorial_general_203.png

