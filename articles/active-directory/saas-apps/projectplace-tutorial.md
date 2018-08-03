---
title: 'Oktatóanyag: Azure Active Directory-integráció az Projectplace |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Projectplace között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 4bbe1fc855dfaf637f5893fff795f47b50bd80c6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430882"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Oktatóanyag: Projectplace-Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Projectplace integrálása az Azure Active Directory (Azure AD).

Projectplace integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Projectplace Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Projectplace (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Projectplace, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Projectplace egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Projectplace hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-projectplace-from-the-gallery"></a>Projectplace hozzáadása a katalógusból
Az Azure AD integrálása a Projectplace konfigurálásához hozzá kell Projectplace a galériából a felügyelt SaaS-alkalmazások listájára.

**Projectplace hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Projectplace**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/projectplace-tutorial/tutorial_projectplace_search.png)

1. Az eredmények panelen válassza ki a **Projectplace**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/projectplace-tutorial/tutorial_projectplace_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Projectplace a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó Projectplace mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Projectplace hivatkozás kapcsolatát kell létrehozni.

Projectplace, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Projectplace tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Projectplace tesztfelhasználó létrehozása](#creating-a-projectplace-test-user)**  –, amely kapcsolódik az Azure AD felhasználói ábrázolása Projectplace-megfelelője a Britta Simon van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Projectplace-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Projectplace, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Projectplace** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/projectplace-tutorial/tutorial_projectplace_samlbase.png)

1. Az a **Projectplace-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/projectplace-tutorial/tutorial_projectplace_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company>.projectplace.com`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Projectplace ügyfél-támogatási csapatának](https://success.planview.com/Projectplace/Support) lekérni ezt az értéket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/projectplace-tutorial/tutorial_projectplace_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/projectplace-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **Projectplace** oldalon kell küldenie a letöltött **metaadatainak XML** való [Projectplace-ügyfélszolgálathoz](https://success.planview.com/Projectplace/Support). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

>[!NOTE]
>Az egyszeri bejelentkezés beállításainak által végrehajtandó rendelkezik a [Projectplace-ügyfélszolgálathoz](https://success.planview.com/Projectplace/Support). Értesítést fog kapni, amint a konfiguráció befejeződött.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/projectplace-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/projectplace-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/projectplace-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/projectplace-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-projectplace-test-user"></a>Projectplace tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Projectplace-ba való bejelentkezéshez, akkor ki kell építeni Projectplace-be. Projectplace, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Projectplace** rendszergazdaként a vállalati webhely.

1. Lépjen a **személyek**, és kattintson a **tagok**.
   
    ![Személyek](./media/projectplace-tutorial/ic790228.png "személyek")

1. Kattintson a **tag hozzáadása**.
   
    ![Tagok hozzáadása](./media/projectplace-tutorial/ic790232.png "tagok hozzáadása")

1. Az a **tag hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Új tagok](./media/projectplace-tutorial/ic790233.png "új tagok")
   
    a. Az a **új tagok** szövegmezőbe írja be a kapcsolódó szövegmezőkben kiépítendő érvényes AAD-fiók e-mail-címét.
   
    b. Kattintson a **küldése**.

   Az Azure Active Directory fióktulajdonos és a telepítőre mutató mielőtt aktívvá válik, győződjön meg arról, hogy a fiók e-mail érkezik.

>[!NOTE]
>Bármely más Projectplace felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Projectplace által biztosított API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon projectplace tartalomcsomagjához a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendeléséhez a Projectplace, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Projectplace**.

    ![Egyszeri bejelentkezés konfigurálása](./media/projectplace-tutorial/tutorial_projectplace_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Projectplace csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Projectplace-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/projectplace-tutorial/tutorial_general_01.png
[2]: ./media/projectplace-tutorial/tutorial_general_02.png
[3]: ./media/projectplace-tutorial/tutorial_general_03.png
[4]: ./media/projectplace-tutorial/tutorial_general_04.png

[100]: ./media/projectplace-tutorial/tutorial_general_100.png

[200]: ./media/projectplace-tutorial/tutorial_general_200.png
[201]: ./media/projectplace-tutorial/tutorial_general_201.png
[202]: ./media/projectplace-tutorial/tutorial_general_202.png
[203]: ./media/projectplace-tutorial/tutorial_general_203.png

