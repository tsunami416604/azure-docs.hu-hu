---
title: 'Oktatóanyag: Azure Active Directory-integráció az Cerner központi |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Cerner központi és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d2bc549d-d286-4679-854e-bb67c62b0475
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: 77cd9f90de1d50e91061a6a7222d01c72aadf3f3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047909"
---
# <a name="tutorial-azure-active-directory-integration-with-cerner-central"></a>Oktatóanyag: Azure Active Directory-integráció az Cerner közép-India

Ebben az oktatóanyagban elsajátíthatja, hogyan Cerner központi integrálása az Azure Active Directory (Azure AD).

Cerner központi integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Cerner közép-India Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Cerner központi (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Cerner központi, a következő elemek van szükség:

- Az Azure AD-előfizetéshez
- Egy jóváhagyott Cerner központi rendszerfiók

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Cerner központi hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-cerner-central-from-the-gallery"></a>Cerner központi hozzáadása a katalógusból
Az Azure AD integrálása a Cerner központi konfigurálásához hozzá kell Cerner központi a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Cerner központi hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gomb fölött a párbeszédpanelen.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Cerner központi**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cernercentral-tutorial/tutorial_cernercentral_search.png)

5. Az eredmények panelen válassza ki a **Cerner központi**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cernercentral-tutorial/tutorial_cernercentral_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a egy "Britta Simon." nevű tesztelési felhasználó alapján Cerner központi az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Cerner közép-India Mi az a felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Cerner közép-India hivatkozás kapcsolatának kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Cerner Centrallal tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Cerner központi tesztfelhasználó létrehozása](#creating-a-cerner-central-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Cerner közép-India, amely a felhasználó Azure ad-ben reprezentációja van csatolva.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Cerner központi alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Cerner központi, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Cerner központi** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/cernercentral-tutorial/tutorial_cernercentral_samlbase.png)

3. Az a **Cerner központi tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/cernercentral-tutorial/tutorial_cernercentral_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az értéket a következő minták segítségével:

    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/metadata` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/metadata` |
    
    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minták használatával URL-címe:
    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/sso` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/sso` |

    > [!NOTE]
    > Ezek az értékek nem a valós. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [Cerner központi támogatási csapatának](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/cernercentral-tutorial/tutorial_metadataurl.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/cernercentral-tutorial/tutorial_general_400.png)

6. Az egyszeri bejelentkezés konfigurálása **Cerner központi** oldalon kell küldenie a **alkalmazás összevonási metaadatainak URL-címe** való [Cerner központi támogatási](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations). Az egyszeri bejelentkezés konfigurálja az alkalmazás oldalán, az integráció végrehajtásához.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cernercentral-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cernercentral-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cernercentral-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cernercentral-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** Britta Simon.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-cerner-central-test-user"></a>Cerner központi tesztfelhasználó létrehozása

**Cerner központi** alkalmazás lehetővé teszi, hogy a hitelesítés bármely összevont identitásszolgáltatótól. Ha egy felhasználó tud bejelentkezni az alkalmazás kezdőlapjára, összevont, és nem szükséges manuális telepítéshez. További részleteket talál [Itt](cernercentral-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Cerner közép-India Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200]

**Britta Simon rendel Cerner központi, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Cerner központi**.

    ![Egyszeri bejelentkezés konfigurálása](./media/cernercentral-tutorial/tutorial_cernercentral_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Cerner központi csempére kattint, meg kell lekérése automatikusan bejelentkezett a Cerner központi alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](cernercentral-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/cernercentral-tutorial/tutorial_general_01.png
[2]: ./media/cernercentral-tutorial/tutorial_general_02.png
[3]: ./media/cernercentral-tutorial/tutorial_general_03.png
[4]: ./media/cernercentral-tutorial/tutorial_general_04.png

[100]: ./media/cernercentral-tutorial/tutorial_general_100.png

[200]: ./media/cernercentral-tutorial/tutorial_general_200.png
[201]: ./media/cernercentral-tutorial/tutorial_general_201.png
[202]: ./media/cernercentral-tutorial/tutorial_general_202.png
[203]: ./media/cernercentral-tutorial/tutorial_general_203.png