---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az Aha! | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Aha között!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: d46467947116da6f6e5439a54e0315f216961819
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813219"
---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Oktatóanyag: Az Azure Active Directory-integráció az Aha!

Ebben az oktatóanyagban megismerheti, hogyan integrálható a Aha! az Azure Active Directory (Azure AD).

Integrálás Aha! az Azure ad-vel nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Aha Azure AD-ben!
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Aha! (Egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Aha!, a következőkre van szüksége:

- Azure AD-előfizetés
- An Aha! egyszeri bejelentkezés engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás Aha! a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-aha-from-the-gallery"></a>Hozzáadás Aha! a katalógusból
Aha integrációjának konfigurálása! Azure ad-ben hozzá kell adnia Aha! a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Aha hozzáadása! a katalógusból hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Aha!**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/aha-tutorial/tutorial_aha_search.png)

5. Az eredmények panelen válassza ki a **Aha!**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/aha-tutorial/tutorial_aha_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Aha! egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, milyen a partner felhasználó Aha! a felhasználó az Azure AD-ben. Más szóval egy hivatkozás közötti kapcsolat egy Azure AD-felhasználót és a kapcsolódó felhasználó Aha! meg kell határozni.

A Aha!, az értéket a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** hivatkozás viszony.

A konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az Aha!, a következő építőelemeit végrehajtásához szükséges:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Létrehozás egy Aha! tesztfelhasználó](#creating-an-aha-test-user)**  – egy megfelelője a Britta Simon Aha van! a felhasználó Azure ad-ben ábrázolását, amely van csatolva.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Aha az egyszeri bejelentkezés konfigurálása! az alkalmazás.

**Az Azure AD egyszeri bejelentkezés konfigurálása Aha!, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Aha!** alkalmazás-integráció lapján, kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/aha-tutorial/tutorial_aha_samlbase.png)

3. Az a **Aha! Tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/aha-tutorial/tutorial_aha_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.aha.io/session/new`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.aha.io`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Aha! Ügyfél-támogatási csapatának](https://www.aha.io/company/contact) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/aha-tutorial/tutorial_aha_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/aha-tutorial/tutorial_general_400.png)

6. Egy másik böngészőablakban jelentkezzen be a Aha! vállalati hely rendszergazdaként.

7. A felső menüben kattintson **beállítások**.

    ![Beállítások](./media/aha-tutorial/IC798950.png "beállításai")

8. Kattintson a **fiók**.
   
    ![Profil](./media/aha-tutorial/IC798951.png "profil")

9. Kattintson a **biztonsági és egyszeri bejelentkezés**.
   
    ![Biztonság és egyszeri bejelentkezés](./media/aha-tutorial/IC798952.png "biztonsági és egyszeri bejelentkezés")

10. A **egyszeri bejelentkezés** szakaszban, **identitásszolgáltató**válassza **SAML2.0**.
   
    ![Biztonság és egyszeri bejelentkezés](./media/aha-tutorial/IC798953.png "biztonsági és egyszeri bejelentkezés")

11. Az a **egyszeri bejelentkezés** konfiguráció lapon, a következő lépésekkel:
    
    ![Egyszeri bejelentkezés](./media/aha-tutorial/IC798954.png "egyszeri bejelentkezés")
    
       a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét.

       b. A **konfigurálása a következővel**válassza **metaadatait tartalmazó fájl**.
   
       c. A letöltött metaadatfájl feltöltése, kattintson a **Tallózás**.
   
       d. Kattintson az **Update** (Frissítés) elemre.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/aha-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/aha-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/aha-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/aha-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-aha-test-user"></a>Létrehozás egy Aha! felhasználó tesztelése

Ahhoz, hogy az Azure AD-felhasználók Aha jelentkezzen be!, azok ki kell építeni az Aha!.  

Aha esetén!, kiépítése egy automatizált feladat. Nincs művelet elem az Ön számára.

Felhasználók automatikusan jönnek létre szükség esetén az első egyszeri bejelentkezési kísérlet során.

>[!NOTE]
>Bármely más Aha is használhatja! felhasználói fiók létrehozása eszközök vagy Aha által biztosított API-k! AAD-felhasználói fiókok kiépítéséhez.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Aha Azure egyszeri bejelentkezés használatára!.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Aha!, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Aha!**.

    ![Egyszeri bejelentkezés konfigurálása](./media/aha-tutorial/tutorial_aha_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ha szeretné tesztelni az egyszeri bejelentkezés beállításai, nyissa meg a hozzáférési panelen. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/aha-tutorial/tutorial_general_01.png
[2]: ./media/aha-tutorial/tutorial_general_02.png
[3]: ./media/aha-tutorial/tutorial_general_03.png
[4]: ./media/aha-tutorial/tutorial_general_04.png

[100]: ./media/aha-tutorial/tutorial_general_100.png

[200]: ./media/aha-tutorial/tutorial_general_200.png
[201]: ./media/aha-tutorial/tutorial_general_201.png
[202]: ./media/aha-tutorial/tutorial_general_202.png
[203]: ./media/aha-tutorial/tutorial_general_203.png

