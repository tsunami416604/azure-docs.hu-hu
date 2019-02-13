---
title: 'Oktatóanyag: Az mártás Labs - mobil- és webes tesztelése az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés mártás Labs - mobil- és webes tesztelése és Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c4e3d074ad241ec6add6fe43b77413ce283e389
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207007"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Oktatóanyag: Az Azure Active Directory-integráció az mártás Labs - mobil- és webalkalmazás tesztelése

Ebben az oktatóanyagban megismerheti, hogyan integrálható a mártás Labs - mobil- és webes tesztelése az Azure Active Directoryval (Azure AD).

Mártás Labs - mobil- és webes tesztelése az Azure AD integrálása nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá mártás Labs - mobil- és webes tesztelése az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a mártás Tesztkörnyezetekhez - mobil- és webes tesztelésével (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az mártás Labs - mobil- és webes tesztelése, a következőkre van szükség:

- Azure AD-előfizetés
- Mártás Labs - mobil és webes egyszeri bejelentkezés tesztelése előfizetés engedélyezve van.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás mártás Labs - mobil- és webes tesztelése a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Hozzáadás mártás Labs - mobil- és webes tesztelése a katalógusból
Konfigurálja az integrációt, mártás Labs -, mobil- és webes tesztelése az Azure AD-be való hozzá kell mártás Labs - mobil- és webes tesztet a galériából a felügyelt SaaS-alkalmazások listájában.

**Adja hozzá a mártás Labs - mobil- és webes tesztelése a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **mártás Labs - mobil- és webes tesztelés**, jelölje be **mártás Labs - mobil- és webes tesztelése** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Mártás Labs - mobil- és webes tesztelése a találatok listájában](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés az mártás Labs tesztelése és konfigurálása,-mobil- és webes tesztelése egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó mártás Labs - mobil- és webes tesztelése a mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó mártás Labs - mobil- és webes tesztelése a hivatkozás kapcsolata kell létrehozni.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az mártás Labs - mobil- és webes tesztelés, szüksége a következő építőelemeit végrehajtásához:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy mártás Labs – mobil- és webes tesztelés tesztfelhasználó](#create-a-sauce-labs---mobile-and-web-testing-test-user)**  – egy megfelelője a Britta Simon mártás Labs - mobil- és webes tesztelése, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a mártás Labs - mobil és webes alkalmazás tesztelése az egyszeri bejelentkezés konfigurálása.

**Az mártás Labs - mobil- és webes tesztelés, Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **mártás Labs - mobil- és webes tesztelése** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. Az a **mártás Labs - mobil és webes tesztelés tartomány és URL-címek** szakaszban, a felhasználónak nem kell végrehajthatja a lépéseket, ahogy az alkalmazás már előre integrálva van az Azure-ral.

    ![Mártás Labs - mobil és webes tesztelés tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. Egy másik böngészőablakban jelentkezzen be a mártás Labs - mobil- és webalkalmazások rendszergazdaként a vállalati webhely tesztelése.

7. Kattintson a a **felhasználói ikon** válassza **csapat felügyeleti** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. Adja meg a **tartománynév** be a szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. Kattintson a **konfigurálása** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. Az a **konfigurálása egyszeri bejelentkezéshez** szakaszban, a következő lépésekkel.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Kattintson a **Tallózás** és az Azure AD-ből a letöltött metaadatfájl feltöltése.

    b. Válassza ki a **engedélyezték-KIÉPÍTÉS engedélyezése** jelölőnégyzetet.

    c. Clcik **mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Hozzon létre egy mártás Labs - tesztfelhasználó mobil- és webalkalmazás tesztelése

Ez a szakasz célja mártás Labs - mobil- és webes tesztelés Britta Simon nevű felhasználó létrehozásához. Mártás Labs - mobil és webes tesztelése támogatja a just-in-time-kiépítés, engedélyezve alapértelmezés szerint ez. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az mártás Labs - mobil- és webes tesztelése, ha még nem létezik elérésére tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [mártás Labs - mobil- és webes tesztelés támogatási csoportjának](mailto:support@saucelabs.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés mártás Labs - mobil- és webes tesztelés Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése mártás Labs - mobil- és webes tesztelés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **mártás Labs - mobil- és webes tesztelés**.

    ![Az alkalmazások listáját hivatkozásra a mártás Labs - mobil- és webalkalmazás tesztelése](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a mártás Labs – mobil- és webes tesztelés csempére a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett a mártás Labs - mobil és webes alkalmazás tesztelése a.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png
