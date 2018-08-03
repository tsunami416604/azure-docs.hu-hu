---
title: 'Oktatóanyag: Azure Active Directory-integráció az PlanMyLeave |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és PlanMyLeave között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: jeedes
ms.openlocfilehash: 2f5dde2d99844ecc71d72207296cf8c7dac29e46
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424340"
---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Oktatóanyag: Azure Active Directory-integráció az PlanMyLeave

Ebben az oktatóanyagban elsajátíthatja, hogyan PlanMyLeave integrálása az Azure Active Directory (Azure AD).

PlanMyLeave integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá PlanMyLeave Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett PlanMyLeave (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

PlanMyLeave az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy PlanMyLeave egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. PlanMyLeave hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-planmyleave-from-the-gallery"></a>PlanMyLeave hozzáadása a katalógusból
Az Azure AD integrálása a PlanMyLeave konfigurálásához hozzá kell PlanMyLeave a katalógusból a felügyelt SaaS-alkalmazások listájára.

**PlanMyLeave hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **PlanMyLeave**válassza **PlanMyLeave** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában PlanMyLeave](./media/planmyleave-tutorial/tutorial_planmyleave_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés PlanMyLeave a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó PlanMyLeave mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó PlanMyLeave hivatkozás kapcsolata kell létrehozni.

PlanMyLeave, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az PlanMyLeave tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy PlanMyLeave tesztfelhasználót](#create-a-planmyleave-test-user)**  – egy megfelelője a Britta Simon PlanMyLeave, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és PlanMyLeave alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés PlanMyLeave, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **PlanMyLeave** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/planmyleave-tutorial/tutorial_planmyleave_samlbase.png)

1. Az a **PlanMyLeave tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![PlanMyLeave tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/planmyleave-tutorial/tutorial_planmyleave_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company-name>.planmyleave.com/Login.aspx`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company-name>.planmyleave.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [PlanMyLeave ügyfél-támogatási csapatának](mailto:support@planmyleave.com) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/planmyleave-tutorial/tutorial_planmyleave_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/planmyleave-tutorial/tutorial_general_400.png)

1. Az a **PlanMyLeave konfigurációs** területén kattintson **konfigurálása PlanMyLeave** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![PlanMyLeave konfiguráció](./media/planmyleave-tutorial/tutorial_planmyleave_configure.png) 
1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a PlanMyLeave bérlő.

1. Lépjen a **rendszerbeállítás**. Ezután a a **biztonságkezelés** szakaszban kattintson **vállalati SAML-beállítások** .

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/planmyleave-tutorial/tutorial_planmyleave_002.png) 

1. Az a **SAML-beállítások** területén kattintson a szerkesztőben ikonra.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/planmyleave-tutorial/tutorial_planmyleave_003.png)

1. Az a **SAML beállítások** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  Az a **bejelentkezési URL-cím** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    b.  Nyissa meg a letöltött metaadatok másolási **X509Certificate** értékét, és illessze be azt a **tanúsítvány** szövegmezőbe.

    c. Állítsa be "**beállítás engedélyezve van**"záró"**Igen**".

    d. Kattintson a **Save** (Mentés) gombra. 

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/planmyleave-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/planmyleave-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/planmyleave-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/planmyleave-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-planmyleave-test-user"></a>PlanMyLeave tesztfelhasználó létrehozása

Ez a szakasz célja PlanMyLeave Britta Simon nevű felhasználó létrehozásához. PlanMyLeave támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van.

Nincs meg ebben a szakaszban a művelet elem. Egy új felhasználót hoz létre az PlanMyLeave elérésére, ha még nem létezik tett kísérlet során.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolatba kell [PlanMyLeave támogatási csapatának](mailto:support@planmyleave.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés PlanMyLeave Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel PlanMyLeave, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **PlanMyLeave**.

    ![Az alkalmazások listáját a PlanMyLeave hivatkozásra](./media/planmyleave-tutorial/tutorial_planmyleave_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a PlanMyLeave csempére kattint, meg kell lekérése automatikusan bejelentkezett az PlanMyLeave alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/planmyleave-tutorial/tutorial_general_203.png

