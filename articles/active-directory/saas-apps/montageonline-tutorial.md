---
title: 'Oktatóanyag: Azure Active Directory-integráció Montázs Online-nal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Montázs Online között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c5e8c6f-e4fb-43fe-8841-e371f568ebed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: 4ed162adf08b993b5e0b482f941b3199929ad06c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046090"
---
# <a name="tutorial-azure-active-directory-integration-with-montage-online"></a>Oktatóanyag: Azure Active Directory-integráció Montázs Online-nal

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja Montázs Online az Azure Active Directoryval (Azure AD).

Az Azure AD integrálása Montázs Online nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Montázs Online az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Montázs online (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Montázs Online-nal, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Online Montázs egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A katalógusból Montázs Online hozzáadása
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-montage-online-from-the-gallery"></a>A katalógusból Montázs Online hozzáadása
Az Azure AD-be az Online Montázs integráció konfigurálásához, hozzá kell Montázs Online a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Montázs Online a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Montázs Online**válassza **Montázs Online** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Online Montázs](./media/montageonline-tutorial/tutorial_montageonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Montázs Online "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Montázs online mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Montázs online hivatkozás kapcsolata kell létrehozni.

Konfigurálása és az Azure AD egyszeri bejelentkezés Montázs online-nal való teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Montázs Online tesztfelhasználót](#create-a-montage-online-test-user)**  – egy megfelelője a Britta Simon Montázs Online, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Montázs Online alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Montázs Online-nal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Montázs Online** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/montageonline-tutorial/tutorial_montageonline_samlbase.png)

3. Az a **Montázs Online tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Montázs Online tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/montageonline-tutorial/tutorial_montageonline_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:

    Éles környezetben: `https://<subdomain>.montageonline.co.nz/`

    Tesztelési környezetben: `https://build-<subdomain>.montageonline.co.nz/`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címe:

    Éles környezetben: `MOL_Azure`

    Tesztelési környezetben: `MOL_Azure_Build`

    > [!NOTE] 
    > A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Montázs Online ügyfél-támogatási csapatának](https://www.montage.co.nz/contact-us/) a gépkulcsengedélyek értékének. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/montageonline-tutorial/tutorial_montageonline_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/montageonline-tutorial/tutorial_general_400.png)

6. Az a **Montázs Online konfigurációs** területén kattintson **Montázs Online konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Montázs Online konfiguráció](./media/montageonline-tutorial/tutorial_montageonline_configure.png) 

7. Az egyszeri bejelentkezés konfigurálása **Montázs Online** oldalon kell küldenie a letöltött **tanúsítvány (Base64), kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** való [Montázs Online támogatási csapat](https://www.montage.co.nz/contact-us/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/montageonline-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/montageonline-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/montageonline-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/montageonline-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-montage-online-test-user"></a>Online Montázs tesztfelhasználó létrehozása

Ebben a szakaszban egy Montázs online Britta Simon nevű felhasználó hoz létre. Együttműködve [Montázs Online támogatási csoport](https://www.montage.co.nz/contact-us/) a felhasználók hozzáadása az Online Montázs platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Montázs online-hoz való hozzáférés biztosításával az Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon Montázs online-hoz való hozzárendeléséhez végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Montázs Online**.

    ![Az alkalmazások listáját a Montázs Online hivatkozás](./media/montageonline-tutorial/tutorial_montageonline_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Montázs Online csempére kattint, meg kell lekérése automatikusan bejelentkezett az Montázs Online alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/montageonline-tutorial/tutorial_general_01.png
[2]: ./media/montageonline-tutorial/tutorial_general_02.png
[3]: ./media/montageonline-tutorial/tutorial_general_03.png
[4]: ./media/montageonline-tutorial/tutorial_general_04.png

[100]: ./media/montageonline-tutorial/tutorial_general_100.png

[200]: ./media/montageonline-tutorial/tutorial_general_200.png
[201]: ./media/montageonline-tutorial/tutorial_general_201.png
[202]: ./media/montageonline-tutorial/tutorial_general_202.png
[203]: ./media/montageonline-tutorial/tutorial_general_203.png

