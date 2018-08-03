---
title: 'Oktatóanyag: Azure Active Directory-integráció az Thoughtworks Mingle |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Thoughtworks Mingle között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: a685b5702aa9f74f3e0abf2a06774a30ac0d996f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436990"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Oktatóanyag: Azure Active Directory-integráció az Thoughtworks Mingle

Ebben az oktatóanyagban elsajátíthatja, hogyan Thoughtworks Mingle integrálása az Azure Active Directoryval (Azure AD).

Az Azure AD integrálása a Thoughtworks Mingle nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Thoughtworks Mingle Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Thoughtworks Mingle (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Thoughtworks Mingle konfigurálni az Azure AD-integráció, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Thoughtworks Mingle egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Thoughtworks Mingle hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Thoughtworks Mingle hozzáadása a katalógusból
Az Azure AD integrálása a Thoughtworks Mingle konfigurálásához hozzá kell Thoughtworks Mingle a galériából a felügyelt SaaS-alkalmazások listájára.

**Thoughtworks Mingle hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Thoughtworks Mingle**válassza **Thoughtworks Mingle** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Mingle Thoughtworks](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Thoughtworks Mingle a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Thoughtworks Mingle mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Thoughtworks Mingle hivatkozás kapcsolata kell létrehozni.

Thoughtworks Mingle, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Thoughtworks Mingle tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Thoughtworks Mingle tesztfelhasználót](#create-a-thoughtworks-mingle-test-user)**  – egy megfelelője a Britta Simon Thoughtworks Mingle, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Thoughtworks Mingle alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Thoughtworks Mingle, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Thoughtworks Mingle** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

1. Az a **Thoughtworks Mingle tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Thoughtworks Mingle tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Thoughtworks Mingle ügyfél-támogatási csapatának](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) a gépkulcsengedélyek értékének. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/thoughtworks-mingle-tutorial/tutorial_general_400.png)

1. Jelentkezzen be a **Thoughtworks Mingle** rendszergazdaként a vállalati webhely.

1. Kattintson a **rendszergazdai** lapra, és kattintson a **egyszeri bejelentkezési konfiguráció**.
   
    ![Rendszergazda lap](./media/thoughtworks-mingle-tutorial/ic785157.png "egyszeri bejelentkezés konfigurálása")

1. Az a **egyszeri bejelentkezési konfiguráció** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés Config](./media/thoughtworks-mingle-tutorial/ic785158.png "egyszeri bejelentkezés konfigurálása")
    
    a. A metaadatfájl feltöltése, kattintson a **fájl kiválasztása**. 

    b. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/thoughtworks-mingle-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/thoughtworks-mingle-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/thoughtworks-mingle-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel](./media/thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Thoughtworks Mingle tesztfelhasználó létrehozása

Az Azure AD-felhasználók számára jelentkezhetnek be akkor ki kell építeni az Azure Active Directory-felhasználó nevük alapján Thoughtworks Mingle alkalmazáshoz. Esetén Thoughtworks Mingle kiépítés a manuális feladat.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a vállalati Thoughtworks Mingle hely.

1. Kattintson a **profil**.
   
    ![Az első projektet](./media/thoughtworks-mingle-tutorial/ic785160.png "első projektjét")

1. Kattintson a **rendszergazdai** fülre, majd **felhasználók**.
   
    ![Felhasználók](./media/thoughtworks-mingle-tutorial/ic785161.png "felhasználók")

1. Kattintson a **új felhasználó**.
   
    ![Új felhasználó](./media/thoughtworks-mingle-tutorial/ic785162.png "új felhasználó")

1. Az a **új felhasználó** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Új felhasználó párbeszédpanel](./media/thoughtworks-mingle-tutorial/ic785163.png "új felhasználó")  
 
    a. Írja be a **bejelentkezési név**, **megjelenítendő név**, **válasszon jelszó**, **jelszó megerősítése** egy érvényes Azure AD-fiókot kíván üzembe helyezni azokat a kapcsolódó szöveges mezőkben. 

    b. Mint **felhasználótípus**válassza **teljes jogú felhasználók**.

    c. Kattintson a **hozzon létre ehhez a profilhoz**.

>[!NOTE]
>Bármely más Thoughtworks Mingle felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Thoughtworks Mingle által biztosított API-k.
> 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Thoughtworks Mingle Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Thoughtworks Mingle, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Thoughtworks Mingle**.

    ![Az alkalmazások listáját a Thoughtworks Mingle hivatkozás](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Thoughtworks Mingle csempére kattint, meg kell lekérése automatikusan bejelentkezett az Thoughtworks Mingle alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/thoughtworks-mingle-tutorial/tutorial_general_203.png

