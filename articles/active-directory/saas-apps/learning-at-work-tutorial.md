---
title: 'Oktatóanyag: Azure Active Directory-integráció a munkahelyi tanulással |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálása egyszeri bejelentkezéshez Learning és az Azure Active Directory közötti munkahelyi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d607174-bea1-4f40-8233-54cabe02c66a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: jeedes
ms.openlocfilehash: 35b02adade9f202480e3649fbfcb24d18c765aa1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436039"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-at-work"></a>Oktatóanyag: Azure Active Directory-integráció a munkahelyi tanulással

Ebben az oktatóanyagban elsajátíthatja a munkahelyi Learning integrálása az Azure Active Directory (Azure AD).

A munkahelyi Learning integrálása az Azure AD nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a tanulást a munkahelyi Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett munkahelyi (egyszeri bejelentkezés) Learning az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a munkahelyi tanulással, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A munkahelyi egyszeri bejelentkezéses engedélyezett előfizetés Tanulásért

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Tanulási hozzáadása a munkahelyi a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-learning-at-work-from-the-gallery"></a>Tanulási hozzáadása a munkahelyi a katalógusból
Tanulási integrációjának konfigurálása az Azure AD-be munkahelyi, hozzá kell Learning munkahelyi a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a munkahelyi Learning a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **munkahelyi tanulási**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learning-at-work-tutorial/tutorial_learningatwork_search.png)

1. Az eredmények panelen válassza ki a **munkahelyi tanulási**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learning-at-work-tutorial/tutorial_learningatwork_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a teszt "Britta Simon." nevű felhasználó munkahelyi tanulással

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó munkahelyi Learning mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a munkahelyi Learning hivatkozás kapcsolata kell létrehozni.

A hivatkozás kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** munkahelyi Learning.

Az Azure AD egyszeri bejelentkezés a munkahelyi tanulással tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A Learning létrehozná munkahelyi tesztfelhasználó](#creating-a-learning-at-work-test-user)**  – a felhasználó Azure ad-ben reprezentációja kapcsolódó munkahelyi Learning egy megfelelője a Britta Simon van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a tanulási Work alkalmazást, az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés a munkahelyi tanulással, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **munkahelyi tanulási** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/learning-at-work-tutorial/tutorial_learningatwork_samlbase.png)

1. Az a **tanulás egy munkahelyi tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/learning-at-work-tutorial/tutorial_learningatwork_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.sabacloud.com/Saba/Web/<company code>`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.sabacloud.com/Saba/saml/SSO/alias/<company name>`

    > [!NOTE] 
    > Ezek az értékek nem a valós. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [tanulás egy munkahelyi ügyfél-támogatási csapatának](https://www.learninga-z.com/site/contact/support) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/learning-at-work-tutorial/tutorial_learningatwork_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/learning-at-work-tutorial/tutorial_general_400.png)

1. Az a **tanulás egy munkahelyi konfiguráció** területén kattintson **Learning konfigurálása a munkahelyi** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/learning-at-work-tutorial/tutorial_learningatwork_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **munkahelyi tanulási** oldalon kell küldenie a letöltött **metaadatainak XML**, **SAML Entitásazonosító**, **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, és **kijelentkezéses URL-cím** való [munkahelyi terméktámogatási tanulási](https://www.learninga-z.com/site/contact/support).

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learning-at-work-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learning-at-work-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learning-at-work-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learning-at-work-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-learning-at-work-test-user"></a>A Learning munkahelyi tesztfelhasználó létrehozása

Ebben a szakaszban egy munkahelyi Learning Britta Simon nevű felhasználó hoz létre. Együttműködve [munkahelyi terméktámogatási tanulási](https://www.learninga-z.com/site/contact/support) felhasználót is hozzáadhat a munkahelyi platform Tanulásért.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon ehhez biztosítson hozzáférést a munkahelyi Learning Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel a munkahelyi tanulás, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **munkahelyi tanulási**.

    ![Egyszeri bejelentkezés konfigurálása](./media/learning-at-work-tutorial/tutorial_learningatwork_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha munkahelyi csempe a hozzáférési panelen Tanulásért gombra kattint, kell lekérése automatikusan bejelentkezett, a munkahelyi alkalmazások Tanulásért.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learning-at-work-tutorial/tutorial_general_01.png
[2]: ./media/learning-at-work-tutorial/tutorial_general_02.png
[3]: ./media/learning-at-work-tutorial/tutorial_general_03.png
[4]: ./media/learning-at-work-tutorial/tutorial_general_04.png

[100]: ./media/learning-at-work-tutorial/tutorial_general_100.png

[200]: ./media/learning-at-work-tutorial/tutorial_general_200.png
[201]: ./media/learning-at-work-tutorial/tutorial_general_201.png
[202]: ./media/learning-at-work-tutorial/tutorial_general_202.png
[203]: ./media/learning-at-work-tutorial/tutorial_general_203.png

