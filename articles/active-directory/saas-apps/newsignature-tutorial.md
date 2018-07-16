---
title: 'Oktatóanyag: Azure Active Directory-integráció a Microsoft Azure felhőalapú felügyeleti portállal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a felhőalapú felügyeleti portálján a Microsoft Azure között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 8ea156f09f79d2e2718b0aeb1a6a9e870828c32d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051836"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Oktatóanyag: Azure Active Directory-integráció a Microsoft Azure felhőalapú felügyeleti portállal

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a felhőalapú felügyeleti portálján a Microsoft Azure az Azure Active Directory (Azure AD).

A Microsoft Azure felhőalapú felügyeleti portálján integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Cloud Management Portal a Microsoft Azure, Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett felhőalapú felügyeleti portáljára a Microsoft Azure (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Microsoft Azure felhőalapú felügyeleti portállal, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy felhőalapú felügyeleti portálon, a Microsoft Azure egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Microsoft Azure felhőalapú felügyeleti portál hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>A Microsoft Azure felhőalapú felügyeleti portál hozzáadása a katalógusból
Konfigurálása az Azure AD integrálása a Microsoft Azure felhőalapú felügyeleti portálján, hozzá kell a Microsoft Azure felhőalapú felügyeleti portálján a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A Microsoft Azure felhőalapú felügyeleti portál hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **felhőalapú felügyeleti portálján a Microsoft Azure**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/newsignature-tutorial/tutorial_newsignature_search.png)

5. Az eredmények panelen válassza ki a **felhőalapú felügyeleti portálján a Microsoft Azure**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az úgynevezett "Britta Simon" tesztfelhasználó alapján a Microsoft Azure felhőalapú felügyeleti portállal.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó felhőalapú felügyeleti portálján a Microsoft Azure mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Microsoft Azure felhőalapú felügyeleti portálon hivatkozás kapcsolata kell létrehozni.

A Microsoft Azure-felhő a felügyeleti portálon, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Microsoft Azure felhőalapú felügyeleti portállal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy felhőalapú felügyeleti portálon, a Microsoft Azure tesztfelhasználó létrehozása](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)**  – felhőalapú felügyeleti portálján, amely kapcsolódik az Azure AD felhasználói ábrázolása a Microsoft Azure-megfelelője a Britta Simon rendelkeznie.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezése az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Microsoft Azure-alkalmazások a Felhőbeli felügyeleti portálon egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása a Microsoft Azure felhőalapú felügyeleti portállal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **felhőalapú felügyeleti portálján a Microsoft Azure** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/newsignature-tutorial/tutorial_newsignature_samlbase.png)

3. Az a **felhőalapú felügyeleti portálján a Microsoft Azure-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/newsignature-tutorial/tutorial_newsignature_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minták használatával URL-címe: 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. Az a **azonosító** szövegmezőbe írja be a következő minták használatával URL-címe: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minták használatával URL-címe: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [felhőalapú felügyeleti portálján a Microsoft Azure ügyfél-támogatási csapatának](mailto:jczernuszka@newsignature.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/newsignature-tutorial/tutorial_newsignature_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/newsignature-tutorial/tutorial_general_400.png)

6. Az a **felhőalapú felügyeleti portálján a Microsoft Azure-konfiguráció** területén kattintson **felhőalapú felügyeleti portálon konfigurálhatja a Microsoft Azure** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/newsignature-tutorial/tutorial_newsignature_configure.png) 

7. Az egyszeri bejelentkezés konfigurálása **felhőalapú felügyeleti portálján a Microsoft Azure** oldalon kell küldenie a letöltött **tanúsítvány**, **kijelentkezéses URL-cím**,  **SAML egyszeri bejelentkezési szolgáltatás URL-cím** és **SAML Entitásazonosító** való [felhőalapú felügyeleti portálján a Microsoft Azure támogatási csapatának](mailto:jczernuszka@newsignature.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/newsignature-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/newsignature-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/newsignature-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/newsignature-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Egy felhőalapú felügyeleti portálon, a Microsoft Azure tesztfelhasználó létrehozása

Ez a szakasz célja egy nevű Britta Simon felhőalapú felügyeleti portálon, a Microsoft Azure-felhasználó létrehozásához. Együttműködve [felhőalapú felügyeleti portálján a Microsoft Azure támogatási csapatának](mailto:jczernuszka@newsignature.com) a felhasználók hozzáadása a Microsoft Azure-fiókkal a felhőalapú felügyeleti portálon.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon felhőalapú felügyeleti portálján a Microsoft Azure-hoz való hozzáférés biztosítása az Azure egyszeri bejelentkezés használja.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése a Microsoft Azure felhőalapú felügyeleti portálján, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **felhőalapú felügyeleti portálján a Microsoft Azure**.

    ![Egyszeri bejelentkezés konfigurálása](./media/newsignature-tutorial/tutorial_newsignature_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.
Ha rákattint a felhőalapú felügyeleti portálon, a Microsoft Azure csempe a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett a felhőalapú felügyeleti portálra a Microsoft Azure-alkalmazások.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/newsignature-tutorial/tutorial_general_01.png
[2]: ./media/newsignature-tutorial/tutorial_general_02.png
[3]: ./media/newsignature-tutorial/tutorial_general_03.png
[4]: ./media/newsignature-tutorial/tutorial_general_04.png

[100]: ./media/newsignature-tutorial/tutorial_general_100.png

[200]: ./media/newsignature-tutorial/tutorial_general_200.png
[201]: ./media/newsignature-tutorial/tutorial_general_201.png
[202]: ./media/newsignature-tutorial/tutorial_general_202.png
[203]: ./media/newsignature-tutorial/tutorial_general_203.png

